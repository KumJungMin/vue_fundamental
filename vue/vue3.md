# 3. vue 필수요소(2)

## 1) 템플릿 문법
- 내부적으로 Vue는 템플릿을 가상 DOM 렌더링 함수로 컴파일한다. 
- 반응형 시스템과 결합된 Vue는 앱 상태가 변경 될 때 최소한으로 DOM을 조작한다.
- 가상 DOM 개념에 익숙하고 JavaScript의 기본 기능을 선호하는 경우 템플릿 대신 <a href="https://kr.vuejs.org/v2/guide/render-function.html">렌더링 함수를 직접 작성</a>할 수 있다.

### (1) 보간법

- 문자열 : 이중 중괄호를 기본형태 한다.
```html
<span>메시지: {{ msg }}</span>
```

<br/>

- v-once : 데이터 변경 시 업데이트 되지않는 일회성 보간을 만들 수 있다.(단, 같은 노드의 바인딩에도 영향을 미침)
```html
<span v-once>다시는 변경하지 않습니다: {{ msg }}</span>
```

<br/>

- v-html : 실제 HTML을 출력하려면 v-html 디렉티브를 사용해야 한다.(지양할 것!)
```html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
<!--span의 내용은 rawHtml로 대체됨. 
이 때 데이터 바인딩은 무시됨.-->
```

<br/>

- v-bind : <a href="https://kr.vuejs.org/v2/api/#v-bind">bind</a>는 html속성에 사용되며, attr과 유사하다.(속성변경)

```html
<!-- disabled = null, undefined, false이므로 abled임-->
<button v-bind:disabled="isButtonDisabled">Button</button>
```

<br/>

- js 표현식 사용: vue.js는 데이터 바인딩에서 js 표현식이 가능하다.
```html
{{number + 1}}
{{ok ? 'yes' : 'no'}}
<div v-bind:id="'list-' + id"></div>

<!--각 바인딩에 하나의 단일 표현식 만 포함될 수 있으므로 아래처럼 작성하면 안됩니다  -->
<!-- 아래는 구문입니다, 표현식이 아닙니다. -->
{{ var a = 1 }}

<!-- 조건문은 작동하지 않습니다. 삼항 연산자를 사용해야 합니다. -->
{{ if (ok) { return message } }}
```

<br/>
<br/>

## 2) 디렉티브 

- 디렉티브는 v- 접두사가 있는 특수 속성이다. 
- `디렉티브 속성값 == JavaScript 표현식`이다. 
- 디렉티브의 역할은 표현식의 값이 변경될 때 DOM에 적용한다. 

### (1) v-if
- seen이 true, false에 따라 `<p>`를 제거 혹은 삽입한다.

```html
<p v-if="seen">이제 나를 볼 수 있어요</p>
```

<br/>

### (2) 전달인자
- 일부 디렉티브는 콜론을 이용해 “전달인자”를 사용한다.
```html    
<!--  
v-bind 디렉티브는 반응적으로 HTML 속성을 갱신하는데 사용
:href는 전달인자
a 엘리먼트의 href속성을 표현식url에 바인딩
-->
<a v-bind:href="url">url갱신</a>
```

```html
<!-- 
DOM 이벤트를 수신하는 v-on 디렉티브
전달인자는 이벤트를 받을 이름 
-->
    <a v-on:click="doSomething"> ... </a>
```
