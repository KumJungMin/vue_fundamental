# Vue.js

## 2) 디렉티브

- v- 접두사가 있는 `특수 속성`을 말한다.
- 속성의 값은 JavaScript 표현식이 된다? ( v-for는 예외)
- 디렉티브는 표현식의 `값이 변경`될 때 `DOM에 적용`한다.

```html
<p v-if="seen">you can see it</p>
<!-- v-if디렉티브는 seen에 따라 <p>를 제거 혹은 삽입함-->
```

### (1) 전달인자

: 디렉티브는 `콜론(:)`을 사용해 전달인자를 사용할 수 있다. 

 : `v-bind 디렉티브`는 반응적으로 HTML 속성을 갱신하는데 사용된다.

: 아래 코드에는 `href="url"`을 `v-bind 디렉티브`에게 알려준다.

```html
<a v-bind:href="url"></a>
<!-- href는 전달인자-->
```

: 또 다른 예로, `v-on 디렉티브`를 사용해 `DOM이벤트` 수신이 가능핟.

```html
<a v-on:click="doSomething"></a>
<!--전달인자는 이벤트를 받는 이름-->
```

### (2) 동적 전달인자, 대괄호

: 2.6버전부터는 JS표현식을 `대괄호([])`로 묶어 `디렉티브 인자`로 사용한다.

```html
<!-- eventName = focus이면 아래 두 코드는 같음-->
<a v-on:[eventName]="doSomething"> ... </a>
<a v-on:click="doSomething"></a>
```

- 동적 전달인자의 제약

: `null`은 명시적으로 바인딩을 제거할 때 사용된다. 

: 그 이외에는 string이 아닌 값은 경고를 출력한다.

: 동적 전달인자의 형식에는 문자상의 제약이 있다.

→ 스페이스와 따옴표는 HTML의 속성명으로서 적합치 않음

```html
<!-- 컴파일러 경고를 불러옵니다 -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

### (3) 수식어, 특수접미사

: 수식어는 점으로 표시되는 특수 접미사이다.

: 디렉티브를 특별한 방법으로 바인딩 해야할 때 사용한다.

: 예를 들어, `.prevent`수식어는 `v-on디렉티브`에게 이벤트에서 `event.preventDefault()`를 호출하도록 알려준다.

```html
<form v-on:submit.prevent="onSubmit"></form>
```

### (4)  약어

: 가장 자주 사용되는 두개의 디렉티브인 `v-bind`와 `v-on`에 대해 특별한 약어를 제공한다.

- v-bind를 생략한 약어

```html
<!--일반-->
<a v-bind:href="url"></a>

<!--약어-->
<a :href="url"></a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a :[key]="url"> ... </a>
```

- v-on 대신 @를 이용한 약어

```html
<!--일반-->
<a v-on:click="doSomething"></a>

<!--약어-->
<a @click="doSomething"></a>

<!-- shorthand with dynamic argument (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

---

# 2. computed와 watch

## 1) computed?

- 템플릿 내에서 표현식을 쓰는 경우, 간단한 연산인 경우만 쓰는 게 좋다.

```html
<!-- bad case -->
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

### (1) 예제

: computed 속성인 `reverseMessage`를 선언하였다. 

: 해당 함수는 `vm.reverseMessage`속성에 대한 `getter 함수`로 사용된다.

- html

```html
<div id="example">
	<p>메시지 : "{{message}}"</p>
	<p>역순메시지 : "{{reverseMessage}}"</p>
</div>
```

- js

```jsx
var vm = new Vue({
	el : '#example',      //element name
	data : {
		message : 'hello'   //variable
	},
	computed : {          //calculated variable
		reverseMessage : function(){ //`this`는 vm 인스턴스
			return this.message.split('').reverse().join()
		}
	}
})
```

- result

```
원본 메시지 : "hello"
역순메시지 : "olleh"
```

### (2) computed속성의 캐싱 vs 메소드

: 표현식에서 computed 메소드를 호출해도 같은 결과를 얻을 수 있다.

```html
<p>역순메시지 : "{{ reverseMessage() }}"</p>
```

- computed 속성의 캐싱 접근방법은...

**computed 속성은 종속 대상을 따라 저장(캐싱)된다!** 

: 해당 속성이 종속된 대상이 변경될 때만 함수를 실행한다. ( message가 변경되지 않는 한 계산을 다시 하지 않음)

```jsx
computed: {
  now: function () {
    return Date.now()
  }
}
```

:  `Date.now()`처럼 아무 곳에도 의존하지 않는 computed 속성의 경우 절대로 업데이트되지 않는다.

- 메소드 접근방법은...

메소드로 호출시 렌더링할 때마다 **항상** 함수를 실행한다!

***캐싱이 왜 필요할까요?*** 

```
계산에 시간이 많이 걸리는 computed 속성인 **A**를 가지고 있다고 해봅시다. 
이 속성을 계산하려면 거대한 배열을 반복해서 다루고 많은 계산을 해야 합니다. 

"그런데 **A** 에 의존하는 다른 computed 속성값도 있을 수 있습니다." 

캐싱을 하지 않으면 **A** 의 getter 함수를 꼭 필요한 것보다 더 많이 실행하게 됩니다! 
캐싱을 원하지 않는 경우 메소드를 사용하십시오.
```

### (3) computed 속성 vs watch 속성

***watch속성 사용하지 마세요!***

- vue 인스턴스의 데이터 변경을 관찰하고 이에 반응하는 보다 일반적인 watch 속성을 제공한다.
- 다른 데이터 기반으로 변경할 필요가 있는 데이터가 있는 경우, watch 속성를 사용하기도 한다.
- 하지만 명령적인 watch 콜백보다 computed 속성을 사용하는 것이 더 좋다.

### (4) computed 속성의 setter 함수

- 필요한 경우 setter 함수를 만들어 쓸 수 있다.
- `vm.fullName = 'John Doe'`를 실행하면 설정자가 호출되고 `vm.firstName`과 `vm.lastNam`e이 그에 따라 업데이트 된다.

```jsx
//...
computed : {
	fullName : {
		//getter
		get : function() {
			return thus.fistName+ '' + this.lastName
		},
		//setter
		set : function(newValue){
			var names = newValue.split(' ')
			this.firstName = names[0]
			this.lastName = names[names.length-1]
		}
	}
}
```

## 2) watch 속성

- 대부분 computed속성이 적합하지만, watch속성을 사용해야할 경우가 있다.

    : 사용자가 만든 감시자가 필요한 경우

    : 데이터 변경에 대한 응답으로 비동기식 수행을 하는 경우

    : 데이터 변경에 대한 응답이 시간이 많이 소요되는 조작인 경우

- 동기 비동기?

    **동기방식**은 설계가 매우 간단하고 직관적이지만 결과가 주어질 때까지 아무것도 못하고 대기해야 하는 단점이 있고,

    **비동기방식**은 동기보다 복잡하지만 결과가 주어지는데 시간이 걸리더라도 그 시간 동안 다른 작업을 할 수 있으므로 자원을 효율적으로 사용할 수 있는 장점이 있습니다.

- html

```html
<div id="watch-example">
  <p>
    yes/no 질문을 물어보세요:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>

<!-- 이미 Ajax 라이브러리의 풍부한 생태계와 범용 유틸리티 메소드 컬렉션이 있기 때문에, -->
<!-- Vue 코어는 다시 만들지 않아 작게 유지됩니다. -->
<!-- 이것은 이미 익숙한 것을 선택할 수 있는 자유를 줍니다. -->
<script src="https://unpkg.com/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://unpkg.com/lodash@4.13.1/lodash.min.js"></script>
```

- js

: watch 옵션을 사용하여 비동기 연산 (API 엑세스)를 수행한다.

: 우리가 그 연산을 수행빈도 제한과 최종 응답을 얻을 때까지 중간 상태를 설정할 수 있다.

```jsx
var watchExample = new Vue({
	el : '#watch-example',     //element
	data : {
		question : '',
		answer : '질문을 하기전에 대답할 수 없어요'
	},
	watch : {
		//질문 변경시 -> 이 기능을 실행
		question : function(newQuestion){
			this.answer = '입력을 기다리는 중'   
			this.debouncedGetAnswer()       //시간이 많이 소요되는 작업
		}
	},
	created : function(){
		this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
	},
	methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = '질문에는 일반적으로 물음표가 포함 됩니다. ;-)'
        return
      }
      this.answer = '생각중...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = '에러! API 요청에 오류가 있습니다. ' + error
        })
    }
  }
})
```

---

# 3. 클래스와 스타일 바인딩

- 데이터 바인딩은 엘리먼트의 클래스 목록과 인라인 스타일을 조작하기 위해 일반적으로 사용된다.
- 이 두 속성은 v-bind를 사용하여 처리할 수 있다.

## 1) html 클래스 바인딩하기

### (1) 객체 구문

: 클래스를 동적으로 적용하기 위해 `v-bind:class`를 사용한다.

: `isActive`가 true, false인가에 따라 → `active`를 클래스로 적용할지 말지를 결정한다.

```html
<div v-bind:class="{active: isActive}"></div>
```

- 여러 클래스 바인딩하기(1)

: 여러 클래스를 동적으로 적용할 수 있다.

```html
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

: 만약 데이터는 아래와 같은 형태가 된다면,

```jsx
data: {
  isActive: true,
  hasError: false
}
```

: div는 아래와 같이 렌더링이 된다.

: isActive 또는 hasError 가 변경되면 클래스 목록도 그에 따라 업데이트된다.

```html
<div class="static active"></div>
```

- 여러 클래스 바인딩하기(2)

: div에는 하나의 클래스를 바인딩하고

```html
<div v-bind:class="classObject"></div>
```

: js상에서 다른 클래스의 true, false를 결정해도 된다.

```jsx
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

- 여러 클래스 바인딩하기(3)

: div에는 하나의 클래스를 바인딩하고

```html
<div v-bind:class="classObject"></div>
```

: computed를 사용해 error가 없고 isActive가 true인 경우에 클래스 바인딩을 결정할 수 있다.

: 이 방법이 가장 일반적인 패턴이다.

```jsx
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### (2) 배열구문을 사용한 바인딩

: 배열을 `v-bind:class` 에 전달하여 클래스 목록을 지정할 수 있다.

: 먼저 두 개의 변수를 배열형태로 바인딩한다.

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

: js상에서는 변수들의 값은 클래스로 지정한다.

```jsx
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

: 결과적으로 아래와 같이 렌더링된다.

```html
<div class="active text-danger"></div>
```

: 또한, 삼항 연산자를 이용해, 클래스를 조건부 적용할 수 있다.

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

: 이것은 항상 `errorClass`를 적용하고 `isActive`가 `true`일 때만 `activeClass`를 적용한다.

: 그러나 여러 조건부 클래스가 있는 경우 장황해질 수 있다. 

: 그래서 배열 구문 내에서 객체 구문을 사용할 수 있다.

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### (3) 컴포넌트와 바인딩

- 예제1

: 사용자 정의 컴포넌트를 선언한다.

```jsx
Vue.component('my-component', {
	template : '<p class="foo bar">Hi</p>'
})
```

: 추가로 사용한 클래스를 추가한다.

```jsx
<my-component class="baz boo"></my-component>
```

: html이 아래와 같이 랜더링이 된다.

```html
<p class="foo bar baz boo">Hi</p>
```

- 예제2

: 사용자 정의 클래스에 클래스 바인딩한다.

```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

: isActive가 true인 경우 아래와 같이 랜더링된다.

```html
<p class="foo bar active">Hi</p>
```

## 2) 인라인 스타일 바인딩

### (1) 객체구문

: `v-bind:style`은 css처럼 보이지만, 사실 js객체이다.

: 속성 이름에 cameCase와 따옴표를 사용한다.

- 방법1

```html
<div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}"></div>
```

```jsx
//js

data: {
  activeColor: 'red',
  fontSize: 30
}
```

- 방법2

```html
<div v-bind:style="styleObject"></div>
```

```jsx
//js
data : {
	styleObject : {
		color : 'red',
		fontSize : '13px'
	}
}
```

### (2) 배열구문

: 아래와 같이 배열을 이용해 스타일 객체를 사용할 수 있다.

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### (3) 자동접두사

: `v-bind:style`에 브라우저 벤더 접두어(`webkit-`)가 필요한 경우 Vue에서 자동으로 해당 접두어를 감지해 스타일에 적용한다.

### (4) 다중 값 제공, 2.3버전이후

: 2.3버전부터는 스타일 속성에 접두사가 있는 여러 값을 배열로 전달할 수 있다.

: 브라우저가 지원하는 배열의 마지막 값만 렌더링한다. 

: 아래코드의 경우 flexbox의 접두어가 붙지않은 버전을 지원하는 브라우저에 대해 `display : flex`를 렌더링한다.

```html
<div v-bind:syle="{display: ['-webkit-box', '-ms-flexbox', 'flex']}">
```

---

# 4. 조건부 렌더링

## 1) v-if

- `v-if`는 조건에 따라 블록을 랜더링한다.
- 블록은 디렉티브의 표현식이 true일 때만 랜더링한다.
- `v-else` 사용도 가능하다.

```html
<h1 v-if="awesome">Vue is smart</h1>
<h1 v-else>on nooooo</h1>
```

### (1) <template>와 v-if

: `v-if`는 하나의 엘리먼트에 추가해야 한다.

: 하지만, 하나 이상의 엘리먼트를 포함하려면 어떻게 해야할까?

→ 보이지 않는 래퍼 엘리먼트(`<template>`)를 사용해 `v-if`사용이 가능하다.

```html
<template v-if="ok">
	<h1>title</h1>
	<p>paragraph 1</p>
</template>
```

### (2) v-else

: `v-else` 엘리먼트는 `v-if` 또는 `v-else-if` 바로 뒤에 있어야 한다.

```html
<div v-if="Math.random() > 0.5">
  이제 나를 볼 수 있어요
</div>
<div v-else>
  이제는 안보입니다
</div>
```

### (3) v-else-if

: 2.1버전이후 부터, `else-if`블록 사용이 가능해졌다! (여러 조건에 따른 필터링 가능)

: `v-else-if`는 `v-if`, `v-else-if` 엘리먼트 바로 뒤에 와야 한다.

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

### (4) key를 이용한 재사용가능한 엘리먼트 제어

: Vue는 가능한 한 효율적으로 엘리먼트를 렌더링하려고 시도한다.

: 이말은 종종 처음부터 랜더링하지 않고 재사용한다는 말이다!

- 예제1

: 아래코드의 경우 `엘리먼트 재사용성` 때문에, 사용자가 이미 입력한 내용은 지워지지 않는다. 

: 두 템플릿 모두 같은 요소를 사용하므로 `<input>`은 대체되지 않고 단지 `placeholder`만 변경된다.ㅜ

```html
<!--bad-->
<template v-if="loginType === 'username'">
	<label>userName</label>
	<input placeholder="유저 이름을 입력하시오">
</template>

<template v-else>
	<label>eMail</label>
	<input placeholder="이메일 주소를 입력하시오">
</template>
```

- 예제2

: 해결책으로, `key`를 추가하여 `"두 엘리먼트는 완전히 별개니까 재사용하지마!"`라고 알려준다.

: <`label>` 엘리먼트는 key 속성이 없기 때문에 여전히 효율적으로 재사용된다.

```html
<template v-if="loginType === 'username'">
  <label>사용자 이름</label>
  <input placeholder="사용자 이름을 입력하세요" key="username-input">
</template>
<template v-else>
  <label>이메일</label>
  <input placeholder="이메일 주소를 입력하세요" key="email-input">
</template>
```

## 2) v-show

- 엘리먼트를 조건부로 표시하는 또다른 옵션이다.
- `v-if`와 사용법이 거의 동일하다.
- 하지만 `v-show:false`여도 항상 랜더링되고 DOM에 남아있다!
- 이는 곧, `v-show`가 엘리먼트에 `display` 속성을 적용한다는 말이다.

```html
<h1 v-show="ok">안녕하세요!</h1>
```

---

# 4. 리스트 렌더링

## 1) v-for

- `v-for`은 배열을 기반으로 리스트를 렌더링한다.
- `item in items` 형태의 문법을 사용한다.

### (1) 기본 사용법

: `item in items`를 이용해 데이터리스트를 탐색한다.

: 이때, `item of items`도 가능하다.

```html
<!--html-->
<ul id="example-1">
	<li v-for="item in itmes">
		{{item.message}}
	</li>
</ul>
```

```jsx
//js
var example = new Vue({
	el : '#example-1'     //element
	data : {[
		{message : 'Foo'},
		{message : 'Bar'}
		]
	}
})
```

```
결과값
* Foo
* Bar
```

: `v-for`는 현재 항목의 인덱스를 두 번째 전달인자로 제공한다.

```html
<!--html-->
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```jsx
//js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

```
결과값
* Parent-0-Foo
* Parent-1-Bar
```

## 2) v-for과 객체

- `v-for`을 사용해 객체를 반복할 수 있다.

```html
<!--html-->
<ul id="v-for-object" class="demo">
	<li v-for="value in object">
		{{ value }}
	</li>
</ul>
```

```jsx
//js
new Vue({
	el : '#v-for-object',
	data : {
		object : {
			title : 'how to do',
			anthor : 'j',
			publishedAt : '2020-10'
		}
	}
})
```

```
결과값
* how to do
* j
* 2020-10
```

- key값을 두번째 인자로 전달할 수 있다.

```html
<!--html-->
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```

```
결과값
title : how to do
author : j
publishedAt : 2020-10
```

- 인덱스를 세 번째 인자로 제공할 수 있다.

```html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

- Vue에서 개별 DOM 노드들을 추적하고 기존 엘리먼트를 재사용, 재정렬하기 위해서 v-for의 각 항목들에 고유한 key 속성을 제공해야 한다.
- key에 대한 이상적인 값은 각 항목을 식별할 수 있는 고유한 ID입니다.

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- content -->
</div>
```

## 3) 배열 변경 감지

### (1) 변이 메소드

: 배열값에 변동이 생기면 뷰갱신을 호출한다.

: 변이 메소드는 호출된 원본 배열을 변형한다.

```
- 메소드 종류
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
```

```jsx
example1.items.push({ message: 'Baz' })
```

### (2) 배열 대체

: 원본 배열을 변형하지 않고 항상 새 배열을 반환한다.

```
- 메소드 종류
filter()
concat()
slice()
```

```jsx
//형이 없는 방법으로 이전 배열을 새 배열로 바꿀 수 있음
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

### (3) 주의사항!

: vue는 아래와 같은 경우 변경사항을 감지할 수 없습니다!

→ [1번] 인덱스로 배열항목을 직접 설정할 경우 

→ [2번] 배열 길이를 수정하는 경우

```jsx
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x'   // reactive하지 않음
vm.items.length = 2 // reactive하지 않음
```

- [1번] 극복하는 법

: `splice`를 사용해 해당 인덱스의 값을 새로운 값으로 변경한다.

```jsx
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

- [2번]  극복하는 법

: `splice`를 사용해 원하는 길이만큼만 살려둔다!

```jsx
vm.items.splice(newLength)
```

## 4) 객체 변경 감지에 관한 주의사항

- Vue는 속성 추가 및 삭제를 감지하지 못한다.
- Vue는 이미 만들어진 인스턴스에 루트레벨의 속성 추가를 허용치 않는다.
- 대신 `Vue.set(값을 넣을 객체명, 속성명, 값)` 메소드를 사용하여, 중첩된 객체에 반응형 속성을 추가한다.

```jsx
var vm = new Vue({
	data : {
		userProfile : {
			name : 'Anika'
		}
	}
})
```

```jsx
Vue.set(vm.userProfile, 'age', 27)
//vm.userProfile객체에 age:27속성을 추가한다.
```

- `Object.assign()`이나 `_.extend()`를 사용해 기존의 객체에 새 속성을 할당할 수 있다.

```jsx
//방법1
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})

//방법2
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## 5) 필터링/정렬된 결과 표시하기

- 원본 데이터를 실제로 변경하지않고 배열의 필터링 된 버전이나 정렬된 버전을 표시하고 싶다면?

→ 필터링된 배열이나 정렬된 배열을 반환하는 계산된 속성을 만들자!

- 방법1, 오직 특정 배열만을 사용해 필터링하는 경우

```html
<!--html-->
<li v-for="n in evenNumbers">{{n}}</li>
```

```jsx
data : {
	numbers : [1,2,3,4,5]
},
computed: {
	evenNumbers : function(){
		return this.numbers.filter(function(num){
			return number%2 === 0
		})
	}
}
```

- 방법2, 여러 배열을 입력받아 필터링하는 경우

```html
<!--html-->
<li v-for="n in even(numbers)">{{ n }}</li>
```

```jsx
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {  //even이라는 메소드 생성
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## 6) Range v-for

- v-for은 숫자를 사용할 수 있다.
- 이 방법을 사용해 템플릿을 여러번 반복할 수 있다.

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

```
- 결과
1 2 3 4 5 6 7 8 9 10
```

## 7) v-for 템플릿

- `v-if`와 마찬가지로, `<template>`태그를 사용해 여러 엘리먼트 블록을 렌더링 할 수 있다.

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## 8) v-for와 v-if, 반복이 우선!

- `v-for`가 `v-if`보다 높은 우선순위를 갖는다.
- 즉, `v-if`는 루프가 반복될 때마다 실행된다.
- 이는 일부 항목만 렌더링 할 때 유용하다.

```html
<!--완료되지 않은 할일만 렌더링합-->
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

- 실행을 조건부로 하는게 우선이라면, `래퍼 엘리먼트(또는 <template>)`을 사용한다.

```html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

## 9) v-for과 컴포넌트

- `v-for`를 `사용자 정의 컴포넌트`에 직접 사용할 수 있다.

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

- 2.2.0 이상에서 `v-for`는 `key`가 필수이다.
- 반복할 데이터를 컴포넌트로 전달하기 위해 `props`도 사용한다.

```html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

---