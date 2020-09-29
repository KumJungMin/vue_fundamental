# 1. Vue에 대해 간략히 알아보자
*혹시, 리액트에 대해 알고 계신가요? 그렇다면 vue를 배울 때 더 쉬울 거에요!*

*vue는 js와 react에 대한 지식이 기반이 된다면 중간 정도의 난이도일겁니다.*

## 1) 데이터와 DOM연결하는 법
### (1) html
- 사용자는 div에 id값을 부여한다.
- 가지고 오고 싶은 데이터는 {{variable}} 형식으로 가져온다.
```html
    <div id="app">
        {{ message }}
    </div>
```
<br/>

### (2) js
- [1-1] : 먼저 new Vue를 사용해 선언한다.
- [1-2] : 통신하고자 하는 html태그의 id 혹은 class값을 넣는다.
- [1-3] : 여기서 선언한 변수는, html에서 {{variable}}형식으로 불러온다.
```js
var app = new Vue({  //1-1
    el : '#app',     //1-2
    data : {         //1-3 
        message : 'hello vue!'  //1-4 
    }
}) 
```
- 이제 데이터와 DOM이 연결되었으며 모든 것이 반응형이 되었다. 
- 브라우저의 JavaScript 콘솔을 열고 app.message를 다른 값으로 설정해보자! 
```js
app.message = 'this data changed'
```
- 위 예제가 업데이트 변경된 값에 따라 업데이트되는 것을 볼 수 있습니다.


<br/>

## 2) v-bind는 attr와 유사하다!

- v-bind는 엘리먼트의 상태값을 바꿔줄때 사용한다.
- v-bind 속성은 디렉티브 이라고 한다. 
- 디렉티브는 Vue에서 제공하는 특수 속성으로, 렌더링된 DOM에 반응형 동작을 한다. 

### (1) html
- span에 v-bind를 사용하여, 해당 엘리먼트의 title상태값을 동적 변경한다.
```html
<div id = "app-2">
  <span v-bind:title ="message">
    내 위에 잠시 마우스를 올리면 동적으로 바인딩된 title을 볼 수 있다.
  </span>
</div>
```

<br/>

### (2) js
- [2-1] : 해당 코드 입력시 HTML(이 경우에 title 속성)이 업데이트되었음을 확인할 수 있다.
```js
var app2 = new Vue({  //선언
    el : '#app-2',    //엘리먼트 등록
    data : {          //엘리먼트에서 다룰 데이터 선언
        message : '이 페이지는' + new Date() + '에 로드되었습니다.'
    }
})
app2.message = 'changed message';  //2-1

```

<br/>

## 3) 조건문을 이용해 엘리먼트 표시제어하기

### (1) html
- v-if를 사용해, seen값이 true, false에 따라 해당 엘리먼트의 표시제어한다.
```html
<div id="app-3">
  <p v-if="seen">이제 나를 볼 수 있어요!</p>
</div>
```

<br/>

### (2) js
- [3-1] 해당 코드 입력시 엘리먼트가 사라지는 걸 볼 수 있다.
- DOM의 구조에도 데이터를 바인딩 할 수 있음을 보여준다. 

```js
var app3 = new Vue({    //등록
    el : '#app-3',      //엘리먼트등록
    data : {            //엘리먼트 데이터 초기화
        seen : true
    }
});
app3.seen = false;   //3-1 : variable.data

```

<br/>

## 4) 반복문을 이용해 엘리먼트 표시하기

### (1) html
- v-for를 사용해, 해당 엘리먼트의 데이터들을 반복할 수 있다.
```html
<div id="app-4">
    <ol> <!-- Vue-for문 -->
        <li v-for="todo in todos">
            {{todo.text}}
        </li>
    </ol>
</div>
```

<br/>

### (2) js
- v-for 디렉티브는 배열의 데이터를 바인딩하여 Todo 목록을 표시하는데 사용할 수 있다.

```js
var app4 = new Vue({    //선언
    el : '#app-4',      //엘리먼트등록
    data : {      
        todos : [       //배열데이터초기화
            {text : 'js'},
            {text : 'vue'},
            {text : 'awesome'}
        ]
    }
})
```

<br/>

## 5) 사용자 입력 핸들링

### (1) html
- [5-1] : `id=app-5`인 태그에는 message라는 데이터가 있다.
- [5-2] : `on:클릭이벤트시=특정이벤트호출`을 사용해 이벤트 핸들링을 한다.
```html
<div id="app-5">
    <p> {{message}} </p>     <!--5-1-->
    <button v-on:click="reverseMessage">메시지 뒤집기</button> <!--5-2-->
</div>
```

<br/>

### (2) js
- 사용자가 앱과 상호 작용할 수 있게 하기 위해 v-on:event 디렉티브를 사용한다.
- Vue 인스턴스에서 메소드를 호출하는 이벤트 리스너를 추가 할 수 있다.
- 이 방법은 직접적으로 DOM을 건드리지 않고 앱의 상태만을 업데이트할 수 있다. 

```js
var app5 = new Vue({
    el: '#app-5',
    data: {
      message: '안녕하세요! Vue.js!'
    },
    methods: {      //이벤트함수 선언
      reverseMessage: function () {
        this.message = this.message.split('').reverse().join('')
      }
    }
  })
```

<br/>

## 6) 양방향 바인딩, v-model 
- v-model은 폼입력과 앱 상태를 양방향으로 바인딩한다.
- 이 말은 곧, 둘 중 하나를 수정하면 수정 사항이 둘 다 반영된다는 말이다.
- 사용자가 입력칸에 데이터를 넣으면, 데이터를 보여주는 엘리먼트의 값도 바로 변경된다.

### (1) html
- [6-1] : `v-model=양방향_반응할_데이터변수`를 사용해, 수정사항을 폼과 앱상태에 다 반영할 수 있다.
```html
<div id="app-6">
    <p>{{message}}</p>
    <input v-model="message">   <!--[6-1]-->
</div>

```

<br/>

### (2) js
```js
var app6 = new Vue({
    el : '#app-6',  //엘리먼트 등록
    data : {        //엘리먼트 데이터
        message : 'hello vue~'
    }
})
```

<br/>

## 7) 컴포넌트 시스템
- 컴포넌트 시스템은 Vue의 또 다른 중요한 개념이다. 
- 작고 독립적이며 재사용할 수 있는 컴포넌트로 구성된 대규모 애플리케이션을 구축할 수 있게 해준다. 
- 모든 유형의 애플리케이션 인터페이스를 컴포넌트 트리로 추상화할 수 있다.

### (1) html
- todo-item 컴포넌트의 인스턴스 만들어보자!
- [7-3] : 이제 각 todo-item 에 todo 객체를 제공한다.
- [7-4] : `app-7`엘리먼트의 데이터 수 만큼 반복한다.
- [7-5] : `todo-item`은 `app-7`상위엘리먼트에서 온 prop값을 이용한다.
```html
<div id="app-7">
    <ol>
        <todo-item                          <!--7-3-->
            v-for= "item in groceryList"    <!--7-4-->
            v-bind:todo = "item"            <!--7-5-->
            v-bind:key = "item.id"></todo-item>  <!--키값은 필수!-->
    </ol>
</div>
```

<br/>

### (2) js
- [7-1] : todo-item 이름을 가진 컴포넌트를 정의한다.
- [7-2] : props는 부모 영역의 데이터를 자식 컴포넌트에 전달할 때 쓴다.

```js
Vue.component('todo-item',{             //[7-1]
    props : ['todo'],                   //[7-2]
    template : '<li> {{ todo.text}} </li>'

})
var app7 = new Vue({      //엘리먼트 등록
    el : '#app-7',
    data : {
        groceryList : [   //엘리먼트 데이터
            {id : 0, text : 'vegetables'},
            {id : 1, text : 'cheese'},
            {id : 2, text : 'haman'}
        ]
    }
})



```
















