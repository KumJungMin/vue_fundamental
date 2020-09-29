# 2. Vue 필수요소(1)
## 1) Vue인스턴스 만들기
### (1) new Vue
- 모든 Vue 앱은 Vue함수로 새 Vue 인스턴스를 만드는 것부터 시작한다.
- Vue 인스턴스를 참조하기 위해 종종 변수 vm(ViewModel의 약자)을 사용한다.
- Vue 앱은 new Vue를 통해 만들어진 루트 Vue 인스턴스로 구성되며 중첩과 재사용이 가능하다.
```js
//js

var vm = new Vue({
    //options
})
```
<br/>

### (2) Vue의 옵션
- 더 많은 옵션들을 보고 싶다면 <a href="https://kr.vuejs.org/v2/api/#Options-Data ">클릭하기</a>

<br/>

## 2) 데이터와 메소드
### (1) Vue인스턴스 속성과 원본 데이터
- Vue 인스턴스 생성시, data 객체에 있는 모든 속성이 반응형 시스템에 추가된다. 
- 각 속성값이 변경될 때 뷰가 “반응”하여 새로운 값과 일치하도록 업데이트된다.

```js
var data = {a : 1}      //데이터 객체
var vm2 = new Vue({     //Vue 생서
    data : data         //Vue 인스턴스에 데이터 객체 추가
})
```
<br/>

- 인스턴스에 있는 속성은 원본 데이터에 있는 값을 반환한다.
```js
vum2.a === data.a  => true
```
- Vue 인스턴스에 있는 속성값을 변경하면 원본 데이터에도 영향을 준다.
- 원본 데이터를 변경하면 인스턴스 속성값에도 영향을 준다.
```js
vm.a = 2
data.a  //=>2

data.a = 3
vm.a //=>3
```
<br/>

- 데이터가 변경되면 화면은 다시 렌더링된다. 
- data에 있는 속성들은 인스턴스가 생성될 때 존재한 것들만 반응형이다. 
- 만약, 아래 코드를 사용해 데이터에 새 속성을 추가한다면..?
```js
vm.b = 'hi!'
```
- b를 추가하려고 해도, 인스턴스 생성시 존재하지 않았기에 추가되지 않는다.
- 나중에 속성이 추가될 거 같다면, 아래와 같이 초기값을 지정하면 된다.
```js
data: {
    newTodoText: '',
     visitCount: 0,
     hideCompletedTodos: false,
     todos: [],
     error: null
   }
```

<br/>

### (2) freeze(), 반응성 막기!

```html
<!--html-->

<div id="app2">
    <p>{{ foo }}</p>
    <!-- obj.foo는 더이상 변하지 않습니다! -->
    <button v-on:click="foo = 'baz'">Change it</button>
</div>
```
- freeze를 쓰면, 기존 속성이 변경되는 것을 막을 수 있다.
```js
//js

var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app2',
  data: obj
})

```

<br/>

### (3) $를 통해, js 기능 사용하기
- js의 eventListnener을 사용할 수 있다.
- 대신, 다른 사용자 정의 속성과 구분하기 위해 $ 접두어를 붙인다.

```js
var data = { a: 1 }     //data 선언
var vm = new Vue({      //vue 생성
  el: '#example',       //example엘리먼트 등록
  data: data
})

vm.$data === data       // => true
vm.$el === document.getElementById('example') // => true

// $watch 는 Vue 인스턴스 메소드
// `vm.a`가 변경되면 호출
vm.$watch('a', function (newVal, oldVal) {
})

```

<br/>

## 3) 인스턴스 라이프사이클 훅
- 사용자 정의 로직을 실행할 수있는 라이프사이클 훅 도 호출된다. 
<img width="70%" src="https://kr.vuejs.org/images/lifecycle.png"/>

- 아래 예제는 created 훅은 인스턴스가 생성된 후에 호출된다.

```js
new Vue({
    data: {
      a: 1
    },
    created: function () {
      // `this` 는 vm 인스턴스를 가리킵니다.
      console.log('a is: ' + this.a)
    }
  })
  // => "a is: 1"
```
























