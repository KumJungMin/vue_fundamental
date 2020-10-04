# Untitled

# 1. 로그인, 로그아웃

## 1) 로그인, 로그아웃 로직 preview

![Untitled%200e2dcafd8b264d9699ebc74e3cd5db57/1.jpeg](Untitled%200e2dcafd8b264d9699ebc74e3cd5db57/1.jpeg)

## 2) 코드

### (1) App.vue [root component]

```html
<template>
  <div id="app">
    <Navbar />                         <!--router-link 링크 컴포넌트-->
    <router-view class="container" />  <!--라우터에 따라 보여줄 컴포넌트 장소-->
  </div>
</template>
```

```jsx
import Navbar from './components/Navbar.vue'   

export default {  
  name: 'app',   //사용할 컴포넌트 등록
  components: {
    Navbar
  }
}
```

### (2) NavBar.js [link component]

- [1] : `isAuthenicated`가 true이면 로그아웃을 보여준다.

```html
<template>
  <nav class="header">
    <div class="header-logo">
      <router-link to="/">Trelno</router-link>
    </div>
    <div class="header-auth">
      <a v-if="isAuthenicated" href="" @click.prevent="logout">Logout</a> <!--[1]-->
      <router-link v-else to="/login">Login</router-link>
    </div>
  </nav>
</template>
```

```jsx
import {mapState} from 'vuex'  

export default {
  computed: {
    ...mapState({                                 //state
      navbarColor: 'navbarColor',
      bodyColor: 'bodyColor',
    }),
    isAuthenicated() {
      return this.$store.getters.isAuthenticated  //getter
    },
  },
  watch: {    //if bodyColor changed -> Call updateTheme 
    bodyColor: 'updateTheme'
  },
  mounted() {
    this.updateTheme()
  },
  methods: {
    updateTheme() {
      this.$el.style.backgroundColor = this.navbarColor  //Use html element 
      const body = document.querySelector('body')
      if (!body) return 
      body.style.backgroundColor = this.bodyColor
    },
    logout() {
			//부모컴포넌트가 action, mutation등록시 -> 자식도사용가능
      this.$store.commit('LOGOUT')  
      this.$router.push('/login')
    }
  }
}
```

### (3) Login.vue

- `:disabled="invalidForm"`를 사용해 invalidForm 값에 따라 html 속성 변경이 가능하다.

```html
<template>
  <div class="login">
    <h2>Log in to Trello</h2>
    <form @submit.prevent="onSubmit">
      <div>
        <label for="email">Email</label>
        <input class="form-control" type="text" name="email" v-model="email" autofocus/>
      </div>
      <div>
        <label for="password">Passwrod</label>
        <input class="form-control" type="password" v-model="password"/>
      </div>
      <button  class="btn" :class="{'btn-success': !invalidForm}" type="submit" :disabled="invalidForm">Log In</button>
    </form>
    <p class="error" v-if="error">{{error}}</p>
  </div>
</template>
```

```jsx
import {mapMutations} from 'vuex'

export default {
  data() {
    return {
      email: '',
      password: '',
      returnPath: '',
      error: ''
    }
  },
  computed: {
    invalidForm() {  //email, pw칸이 채워지지 않으면 button을 클릭하지 못하게 disable함
      return !this.email || !this.password
    }
  },
  created() {  //component생성시 실행되는 부분
    this.returnPath = this.$route.query.returnPath || '/'  //rpath가져옴
    this.SET_THEME()
  },
  methods: {
    ...mapMutations([
      'SET_THEME'
    ]),
    onSubmit() {
      const {email, password} = this
      this.$store.dispatch('LOGIN', {email, password}) //등록없이 dispatch로 action 사용가능
        .then(() => {
          this.$router.push(this.returnPath)            //리다이렉션
        })
        .catch(err => {
          this.error = err.response.data.error
        })
    }
  }
}
```

### (4) store/action.js

```jsx
import { auth, board, list, card } from '../api'

const actions = {
  LOGIN({ commit }, { email, password }) {
    return auth.login(email, password)
      .then(({ accessToken }) => commit('LOGIN', { accessToken }))
  }
}
```

### (5) api/index.js

```jsx
import axios from 'axios'
import router from '../router'

const domain = 'http://localhost:3000'
const Unauthorized = 401
const onUnauthorized = () => {
  router.push(`/login?returnPath=${encodeURIComponent(location.pathname)}`)
}

const request = {  //권한별 접근가능
  get(path) {
    return axios.get(`${domain + path}`)
      .catch(({response}) => {
        const {status} = response
        if (status === Unauthorized) return onUnauthorized()
        throw Error(response)
      })
  },
  post(path, data) {
    return axios.post(`${domain + path}`, data)
  },
  delete(path) {
    return axios.delete(`${domain + path}`)
  },
  put(path, data) {
    return axios.put(`${domain + path}`, data)
  }
}

export const setAuthInHeader = token => {  //토큰해더 설정
  axios.defaults.headers.common['Authorization'] = token ? `Bearer ${token}` : null;
}

export const auth = {   //로그인 처리
  login(email, password) {
    return request.post('/login', {email, password})
      .then(({data}) => data)
  }
}
```

### (6) store/mutations.js

```jsx
import { setAuthInHeader } from '../api'

const mutations = {
  LOGIN (state, {accessToken}) {
    if (!accessToken) return
    state.accessToken = accessToken
    localStorage.accessToken = accessToken
    setAuthInHeader(accessToken)   //토큰설정
  },
  LOGOUT (state) {
    state.accessToken = null 
    delete localStorage.accessToken
    setAuthInHeader(null)         //토큰을 null로(logout)
  }
}

export default mutations
```

---