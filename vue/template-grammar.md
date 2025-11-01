# Vue 앱 생성

- main.ts에서 `createApp` 함수는 새로운 앱 인스턴스를 생성한다. (최상위 컴포넌트)
- 앱 인스턴스는 .mount()가 호출될 때까지 아무것도 렌더링하지 않는다.

## 템플릿 문법

- 텍스트 보간법: `{{ msg }}` 컴포넌트 인스턴스의 `msg` 속성의 값으로 대체된다.
- HTML 출력: 데이터를 HTML으로 출력하려면 `<span v-html="msg"></span>`

### 속성 바인딩

- `<div v-bind:id="dynamicId"></div>` 이중 중괄호는 HTML 속성 내에서 사용할 수 없으므로 `v-bind` 디렉티브를 사용한다.
  - 단축 문법: `<div :id="dynamicId"></div>`
- `<button :disabled="isButtonDisabled">버튼</button>`은 isButtonDisabled에 truthy 값이 있는 경우, 속성이 표시된다. 아니라면 속성을 생략한다.
- `<div v-bind="objectOfAttrs"></div>"` 여러 속성을 나타내는 js 객체 바인딩

## JS 표현식 사용

vue는 데이터 바인딩 내에서 js 단일 표현식을 사용할 수 있다.

1. `{{ number + 1 }}`
2. `{{ ok ? '예' : '아니오' }}`
3. `{{ msg.split('').reverse().join('') }}`
4. ``<div :id="`list-${id}`"></div>``

컴포넌트에서 노출하는 메서드를 호출할 수 있다.

```
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```

## 디렉티브

- `v-` 접두사가 있는 특수한 속성을 디렉티브라고 한다. (`v-html`, `v-bind`)
- 디렉티브 속성 값은 단일 js 표현식이어야 한다.

1. `<p v-if="seen">I can see</p>`는 `seen`의 boolean 값을 기반으로 동작한다.
2. 일부 디렉티브는 `:`으로 표시되는 인자를 사용한다.
   - `<a v-bind:href="url"></a>` v-bind는 href에 url 값을 바인딩한다.
   - `<a v-on:click="doSomething"></a>` DOM 이벤트를 수신하여 처리한다.
3. 디렉티브의 인자를 대괄호로 감싸서 js 표현식으로 사용할 수 있다. (동적으로 바인딩)
   - `<a v-bind:[attributeName]="url"></a>`
   - `<a v-on:[eventName]="doSomething"></a>`
4. `<form @submit.prevent="onSubmit">...</form>` 수식어 `.`은 특별한 방식으로 바인딩된다. 해당 예제는 `event.preventDefault()`을 호출하여 새로고침을 막는다.

## ETC.

- @recommended로 추천 플러그인을 다운받을 수 있다.
