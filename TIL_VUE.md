## 📗 VUE

### Optional Chaining
>Optional Chaining (`?.`) 은 왼쪽 값이 `null` or `undefined` 일때 더 이상 하위 속성에 접근하지 않고 `undefined` 로 멈추는 **읽기(조회) 연산자**

- API 응답이 서버로부터 오지 않았거나 (`data === null`), 중간 데이터 객체가 비어있을 때 **런타임 에러가 발생하는 것을 방지**한다. 
ex) `const name = allData.value?.user?.name` 
- `TypeError: Cannot read properties of undefined` 같은 오류 로그가 발생할 때 사용
- Optional Chaining 을 사용해서 런타임 에러를 방지하거나 가독성을 위해서 `null or undefined` 가 발생하면 안되는 **데이터 객체의 초기값을 미리 설정**

---

### Navigator.clipboard
>브라우저 객체인 `navigator` 에 포함된 비동기 클립보드 API

- 주로 Vue 라이브러리에서 사용자가 UI 아이콘을 통해 **간단히 텍스트를 복사하는 기능을 구현하기 위해 사용**
- HTTPS 환경에서만 동작, 사용자 이벤트를 통해 호출 가능, Promise 기반(async/await 필요)

<예시 코드>
```
<button class="icon copy" @click="copyText(allData.renewal.currRiskLvl)"></button>

// 해당 텍스트 클립보드 복사 기능
const copyText = async (value) => {
  try {
    await navigator.clipboard.writeText(value);
    alert('복사 되었습니다.');
  } catch (error) {
    console.error(error);
  }
}
```

---

### Object method
>Object(객체)를 **순회 가능한 배열 형태로 바꿔주는** 메서드

- **객체 데이터를 배열로 처리**함으로써 `map/filter/reduce/every/some` 같은 **함수형 배열 메서드 처리가 가능**

- 자바스크립트에서 Object(객체)란, 키(Key) -> 값(value)을 한 묶음으로 저장하는 자료구조

```
const form = reactive({
  title: "제목",
  date: "2026-03-01",
  content: "오늘은 광복절"
});

console.log(form.title)
console.log(form["content"])
```

#### Object.keys()
>객체 데이터의 키(key) 만 배열로 뽑아서 일괄 검사할 때 사용

```
const form = { title: "A", date: "2026-03-02", content: "" };

Object.keys(form);
// ["title", "date", "content"]
```

#### Object.values()
>객체 데이터의 값(values) 만 **배열로** 뽑아서 일괄 검사할 때 사용, 
실무에서는 프론트엔드에서 API 서버로 **데이터를 보낼려고 할 때 유효성 검사**를 하여 실패 시 상태창을 호출하거나 데이터 전송 기능 버튼을 비활성화를 하고자 할 때 사용

```
const form = { title: "A", date: "2026-03-02", content: "" };

Object.values(form);
// ["A", "2026-03-02", ""]
```

```
const isValid = computed(() => {
  return Object.values(form).every(v => v !== "" && v !== null && v !== undefined);
});
```

#### Object.entries()
>객체 데이터의 키(key) 와 값(values) 를 같이 **배열로** 뽑아서 활용할 때 사용

```
const user = { name: "Kim", age: 25 };

Object.entries(user);
// [["name","Kim"], ["age",25]]
```

```
<template>
  <ul>
    <li v-for="[key, value] in Object.entries(user)" :key="key">
      {{ key }}: {{ value }}
    </li>
  </ul>
</template>
```

---

### router-link, a 태그, router.push 차이점
>Vue 프로젝트에서 화면 이동(라우팅)을 할 때 화면 이동이라는 목적은 같지만 동작 방식은 완전히 다르다

- `<router-link>` : "Vue Router" 가 관리하는 **내부 페이지 이동용** (새로고침 없이 이동)
- `<a>` : 브라우저 기본 링크로 **외부 링크 이동용**
- `router.push()` : 함수 코드 내에서 처리 완료 후 이동용(로그인 후 이동, 저장 후 이동 등)

#### router-link
- 페이지 새로고침 없음
- `to` 에 path/params/query 를 포함 가능 **(동적 라우팅에 사용)**
- 페이지의 상단 메뉴, **상세 조회 페이지**에 사용

#### a 태그
- 주로 외부 사이트로 이동할 때 사용
- 페이지가 새로고침이 되기 때문에 내부 페이지 이동으로 사용하면 SPA의 장점이 저하될 수 있음

#### router.push()
- 컴포넌트내 함수 로직에서 데이터 전송 성공, 로그인 성공 등 이벤트가 발생 후 페이지를 이동할 때 사용
- 페이지 새로고침 없음
- router.replace 로 대체 가능 : push 히스토리에 쌓임(뒤로가기 가능), replace 히스토리를 덮어씀(뒤로가기 불가능)

---

### computed vs watch
>Vue3 에서 `computed` 와 `watch` 는 **반응형 데이터를 감시하는 점**은 비슷하지만 목적은 다르다

- `computed` : **데이터 상태값을 다시 계산해서 만들어 사용**하는 용도 (파생값)
- `watch` : **데이터 상태값이 변화하였을 때 어떠한 동작을 실행**하는 용도 (작용)

#### computed
- **콜백 함수를 사용**하고 **return 값이 존재**해야 함
- **캐싱됨** : **의존하는 값이 바뀌지 않으면 재계산하지 않는다**

- ex) 검색 필터링

```
import { ref, computed } from "vue";

const keyword = ref("");
const items = ref(["apple", "banana", "orange"]);

const filteredItems = computed(() => {
  const k = keyword.value.trim().toLowerCase();
  if (!k) return items.value;
  return items.value.filter(v => v.toLowerCase().includes(k));
});
```

#### watch
- 데이터 상태의 대한 결과 값이 아니라 동작을 결과로 처리

- ex) keyword 가 바뀔때 서버 검색 API 호출

```
import { ref, watch } from "vue";

const keyword = ref("");

watch(keyword, async (newVal, oldVal) => {
  if (!newVal.trim()) return;
  // 예: 서버 검색 호출
  // await fetchSearch(newVal);
  console.log("검색어 변경:", oldVal, "->", newVal);
});
```

---

### Proxy
>Proxy 는 "객체를 감시하고 가로채는 대리인" 으로 해석됨

>직관적으로 Vue3 라이브러리에서는 **데이터가 바뀌면 화면이 자동으로 바뀌는 것을 가능하게 해주는 기술**(반응성 시스템)으로 이해하면 됨

- ref, reactive, computed, watch 모두 proxy 시스템의 일환으로 사용
- 구조 분해 할당 시에는 반응성이 깨진다 
ex) `const { count } = state;` -> 값만 복사됨 (`toRefs` 사용시 반응성 유지가능)

```
import { reactive } from "vue";

const state = reactive({
  count: 0
});
```
에서 `state` 데이터 객체는 내부적으로 `new Proxy(원본객체, handler)` 으로 작동

- ex) `state.count++; `

1. Proxy 가 `set` 을 감지
2. Vue 가 count의 데이터 변화 인지
3. count를 사용하고 있는 컴포넌트 리랜더링

---

### Axios Response
>Vue3 라이브러리 환경에서 Axios + REST API 사용 시 
`const res = await axios.get,post('url')` 코드에서 res 의 응답 객체 정리

기본 구조
```
{
  data: {},        // 서버가 보낸 실제 데이터
  status: 200,     // HTTP 상태 코드
  statusText: 'OK',
  headers: {},
  config: {},
  request: {}
}

```

- **data : 서버에서 실제로 보내준 데이터**
```
data : {
  "resultCode": 0,
  "message": "success",
  "data": {
    "id": 1
  }
}
```

- **status : HTTP 상태 코드**
1. **200번대 코드**
- `200` : `request` 요청 성공
- `201` : `post request` 요청 성공
- `204` : 요청 데이터를 반환 했지만 데이터가 없음
2. **400번대 코드** (클라이언트 오류)
- `400` : 요청은 보냈지만 요청 **형식 오류나 필수값이 누락**
ex) 필수 파라미터 값 누락, 데이터 형식 오류, 요청 구조가 다름
- `401` : **인증이 필요**함 (로그인 필요)
ex) 헤더에 로그인 토큰 필요
- `403` : 인증은 되었지만 **접근 권한이 없음**
ex) 관리자 여부 확인
- `404` : **요청한 대상이 존재하지 않는다** (Not Found)
ex) 요청 URL 오타, 잘못된 파라미터값 입력(존재하지 않는 아이디), 백엔드 API서버에 해당 메서드가 없음,
**프론트엔드 Proxy 처리 오류(CORS)**
3. 500 번대 : 서버 오류

- try/catch 문으로 axios를 호출할때 **catch 는 HTTP 응답 오류 시에만 호출됨**
1. 네트워크 오류, 400번대 오류, 500번대 오류, CORS 오류 -> catch문 호출
2. API 서버 오류, resultCode === -1 -> catch문 호출 x

---

### DOM(Document Object Model)
>브라우저가 `HTML` 문서를 객체 형태로 표현한 구조 = **HTML 형식을 JavaScript 으로 조작 가능한 객체 구조**로 변환

<예시 코드>
```
<body>
  <div id="app">
    <h1>Hello</h1>
    <button>Click</button>
  </div>
</body>
```
```
<DOM Tree>
Document
 └─ body
     └─ div#app
         ├─ h1
         └─ button
```

```
Vue state 변경
      ↓
Virtual DOM 변경
      ↓
Diff 알고리즘
      ↓
Real DOM 업데이트
```

---

### Hook
>Vue 에서 **특정 시점에 실행되는 함수**

- ex) `setup()` , `OnMounted`, `OnUnMounted`

---

### Vue 생명주기
>어떤 시점에 어떤 코드를 실행해야 하는지 결정하는데 사용

```
컴포넌트 생성
→ 화면에 붙기 전
→ 화면에 붙음
→ 데이터 변경으로 다시 그림
→ 화면에서 제거됨
```

```
생성 → setup

붙기 전 → onBeforeMount

붙은 후 → onMounted

수정 전 → onBeforeUpdate

수정 후 → onUpdated

제거 전 → onBeforeUnmount

제거 후 → onUnmounted
```

---

### 동적 라우팅 url / name + params 방식 / name + query 방식 / beforeEnter
>Vue3 프로젝트 SPA 환경에서 `<router-link>` , `router.push()` 를 `url` 방식을 통해 동적 라우팅 하거나 `name + params` , `name + query` 방식을 통해 동적 라우팅 할 수 있다.

#### index.js

```
{
  path: "/vulnerInfo/detail/:id",
  name: "vulner_detail",
  component: loadComponent("vsoc/vulnerability/detail/VulnerabilityDetail"),
  props: true,
 },
```

#### url 방식
```
<router-link :to="`/vulnerInfo/detail/${item.id}`">
    {{ item.id }}
</router-link>
```

- 개인 프로젝트에서 많이 사용되는 동적 라우팅 방식
- 직접 `url` 을 입력하여 페이지를 이동할 경우 `props` 전달 오류가 발생할 경우가 있음
- 새로고침을 했을 경우에도 문제가 발생할 수 있음

#### name + params 방식

- 한 페이지에서 **특정 값의 데이터를 출력**할때 사용

```
<router-link :to="{ name: 'vulner_detail' , params : { id: item.id } }">
    {{ item.id }}
</router-link>
```

```
// 함수 방식
const goList = () => {
  router.push({
    name: "source_detail",
    params: { sourceId: route.params.sourceId },
  })
}

// 값 받는 방법
import { useRoute } from 'vue-router';

const route = useRoute();

console.log(route.params.id); // 3

```

#### name + query 방식
- `/list?page=2&sort=desc` : 옵션 처럼 url 뒤에 붙는 방식
- 따로 `index.js` 의 `path` 에 따로 작성하지 않는다
- 주로 한 페이지에서 **페이지네이션, 검색 필터링, 정렬**할때 사용

```
// index.js
{
  path: '/list',
  name: 'listPage',
  component: ListPage
}

// 이동 방법
router.push({
  name: 'listPage',
  query: { page: 3, sort: 'asc' }
});

// 값 받는 방법
const route = useRoute();

console.log(route.query.page); // 3
console.log(route.query.sort); // asc

```

#### beforeEnter
- 라우터 가드라고 부른다
- `index.js` 에서 해당 페이지에 **라우팅하기 전에 조건을 검사**하는 속성
- `to` : 이동하려는 페이지 / `from` : 현재 페이지
- `true` · `undefined(return;)` : 라우팅 허용 / false : 라우팅 취소 / `return { name: ~ }` : 해당 페이지 경로로 리다이렉트  

<예시 코드>
```
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/admin',
      component: AdminPage,
      beforeEnter: (to, from) => {
        if (isAdmin) return true;
        return { name: 'login' };
      }
    }
  ]
});
```

- 실무에서는 배포를 염두한 상용 서비스 프로젝트가 많기 때문에 미리 라우팅에 대한 설정을 하고 
`name + params` , `name + query` 을 통한 동적 라우팅 방식을 채택함 + 두 방식 동시에 채택도 가능

```
router.push({
  name: 'userDetail',
  params: { id: 10 },
  query: { tab: 'profile' }
});

// url
/user/10?tab=profile
```
---

### Children router
>**메인페이지 경로에 하위 경로들을 지정**하기 위해서 **`children`** 속성을 이용해서 컴포넌트들을 라우터 단위로 가져오기 위해서 사용

- 부모가 되는 메인 컴포넌트들은 어플리케이션 실행 시 바로 로딩되게 하고 자식이 되는 하위 컴포넌트들은 **Lazy Loading** 되는 방식을 채택해서 **페이지 로딩 속도를 최적화** 시키고 **import 중복 선언을 줄일 수 있는** 효과가 있다.

#### Children router 사용법
>Vue3 프로젝트의 index.js 파일에서 해당 라우트에 **`children`** 속성을 추가해서 사용

- <span style="color: red">**주의사항**</span> : children 속성에서 path 값을 입력할 때 앞에 `/`(루트) 를 입력하게 되면 **루트 라우트**가 되기 때문에 **`/` 를 생략**해야한다. 
ex) path:"/children" -> /main/children (x) /children

- 대규모 프로젝트에서는 **`import.meta.glob`** 를 활용하여 함수를 이용하여 라우터 경로를 설정한다.

- **`import.meta.glob`** : 특정 경로의 파일들을 동적 import(Lazy Loading) or 즉시 import(Eager Loading) 할 수 있게 만드는 Vite API

- **`component: () => import("path")`** = Lazy Loding 
**`component: Component`** = Eager Loading

**index.js**
```
import { createRouter, createWebHistory } from "vue-router";

const modules = import.meta.glob("@/components/**/*.vue");

const loadComponent = (path) => modules[`/src/components/${path}.vue`];

const routes = [
  {
    path: "/",
    name: "root",
    redirect: "/login",
  },
  {
    path: "/login",
    name: "login",
    component: () => import("@/views/LoginView.vue"),
  },
  {
    path: "/main",
    name: "main",
    component: () => import("@/views/MainView.vue"),
    children: [
      {
        path: "before_eolupdate",
        name: "before_eolupdate",
        component: loadComponent("ota/BeforeEolUpdate"),
      },
      {
        path: "after_eolupdate",
        name: "after_eolupdate",
        component: loadComponent("ota/AfterEolUpdate"),
      },
  	],
  },
],
```
#### 추가
#### 즉시 로딩 기법 (Eager Loading) 사용
```
import UserList from "@/views/admin/UserList.vue";

{
  path: "users",
  component: UserList
}
```
이와 같이 컴포넌트를 import 해서 사용하게 되면 **어플리케이션 실행 즉시 하위 페이지들도 전부 로딩**되게 되어
**번들 크기가 증가되고 페이지 로딩 속도가 저하**되는 현상이 발생될 수 있다.

#### path 설정
- `*` (현재 폴더) / `**` (모든 하위 폴더 포함)
- `@/` (해당 프로젝트의 src 폴더 별칭) / `./` (현재 폴더) / `../` (상위 폴더)

---

### Component
> `Component` : 화면을 역할 단위로 화면 + 동작 + 상태를 **재사용 가능한 UI 랜더링 단위**
ex ) Vue3 component : `template` + `script setup` + `style scoped` 
=> 하나의 `.vue` 파일

- 코드를 재사용 가능하게 한다. ( `button`, `input`, `modal`, `card`, `table` )
- 하나의 프로젝트를 기능별 분리하여 유지보수에 용이해진다.
- 기능별 개발로 협업에 유리해진다.
- **`script setup` 에 해당하는 기능을 분리하는 것은 
component(x) / 
로직 모듈 = composable(o) - 상태+함수 로직 ,<br /> utility(o) - 단순한 함수 로직,<br /> api module(o) - API 서버 통신 분리**
- `import ComponentName/ModuleName from '../..'` : 
JavaScript ES Module 문법으로 컴포넌트/모듈을 import 하는 것

<composable 적용 예시 코드>

```
<script setup>
import { useIncidentForm } from "@/composables/useIncidentForm";

const {
  postData,
  isValid,
  addAction,
  removeAction,
  submitForm
} = useIncidentForm();
</script>
```

---

### 이벤트 버블링
>하위 요소에서 발생한 이벤트가 **부모 요소로 순차적으로 전파** 되는 현상

#### 이벤트 수식어
> Vue3 프로젝트에서는 **이벤트 수식어**(Event Modifiers)를 제공해서 버블링을 제어한다

#### `.stop`
> **이벤트 버블링 중단** : 부모 이벤트가 실행이 안된다

- 내부적으로 **`event.stopPropagation()`** 을 호출

```
<template>
  <div @click="onParentClick">
    <button @click.stop="onChildClick">버튼</button>
  </div>
</template>

<script setup>
const onParentClick = () => {
  console.log('부모 클릭');
};

const onChildClick = () => {
  console.log('자식 클릭');
};
</script>
```
**실행 결과**
`자식 클릭`

#### `.prevent`
>특정 요소 / 이벤트에 대한 기본 동작을 차단

- 예 : **`<a>`**, **`<form>`**, 체크박스, 드래그/드롭, 우클릭 메뉴 등 

- 내부적으로 **`event.preventDefault()`** 을 호출


```
<a href="/profile" @click.prevent="goProfile">
  프로필
</a>

```

**실행 결과**

- **기본 동작(페이지 이동) 발생 X**
- **goProfile() 만 실행됨**
- 부모요소에 이벤트가 있으면 실행될 수 있음(**버블링 발생 가능**)
-> **`@click.prevent.stop`** 과 같은 형태로 버블링을 막을 수 있음


### 이벤트 캡처링

>이벤트 캡처링은 **이벤트 버블링과 반대 방향**으로 부모 요소가 **하위 요소의 이벤트를 가로채서 처리**하고 하위 요소로 내려간다

- 주로 **Modal 바깥 요소 클릭 시 닫기 기능**, 카드 클릭, 드롭다운/팝오버 바깥 요소 클릭 감지 기능에 사용

#### 이벤트 수식어

>이벤트 버블링과 동일하게 **이벤트 수식어**(Event Modifiers)를 제공해서 캡처링을 제어한다

#### `.capture`

>하위 요소보다 부모 요소의 이벤트부터 처리

```
<div @click.capture="onOuterCapture">
  <button @click="onInnerClick">클릭</button>
</div>
```

**실행 결과**
**1. onOuterCapture 실행**
**2. onInnerClick 실행**

---

