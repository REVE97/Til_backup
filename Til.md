>이 게시글에서는 본격적인 개발 TIL 보다는 개발 도중 궁금했던 부분이나 도메인 지식, CS 지식에 관한 정보를 편하게 기록하는 게시글로 사용하고자 한다

# 📖 FE

## 📙 CS

### 프론트엔드에서 Axios Method 호출 시 payload, response 확인 
>개발 과정에서 프론트엔드에서 백엔드의 API 서버와 `Axios` + `REST API`를 사용하여 데이터를 통신할 때 데이터 통신이 제대로 처리되고 있는지 `console.log` 를 통해 로그로 확인이 어려울 때는 웹페이지의 개발자 도구에서 `NETWORK` 탭을 이용해서 확인가능

### CORS 관련
>프론트엔드와 백엔드 API 서버와 **Axios Method 를 이용해서 서로 통신**할 때 **서로 포트 번호가 다르기 때문에 (Origin)** 프론트엔드에서는 Proxy를 통해 CORS 허용을 해주어야 한다.

- **CORS(Cross Origin Resource Sharing)** : 서버가 Cross Origin을 허용하는 정책 (**브라우저 보안 정책**)
- Same Origin Policy(SOP) : 같은 Origin에서만 리소스 접근을 허용하는 브라우저 보안 정책
- **Cross Origin** : 서로 다른 Origin 을 가진 리소스끼리 통신하는 경우 <-> **Same Origin** : 서로 같은 Origin 을 가진 리소스끼리 통신하는 경우
- Origin 구성요소 : **Protocol**(http,https), **Host**(localhost,domain), **Port**(4545,9100)
- Simple Request: Preflight 없이 바로 요청 / **Preflight Request(OPTIONS)** : OPTIONS 요청으로 먼저 서버 허용 여부 확인
- "GET" 은 대부분 Simple Request 방식 / "POST", "DELETE", "PUT", "PATCH" 는 Preflight Request 방식
- "POST" 메서드 호출 시 `Context-Type`이 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 가 아니라 `application/json` 이면 Preflight Request 
- **브라우저가 Cross Origin 을 막는 이유** : 사용자가 은행 사이트에 로그인하고 이용하다가 다른 사이트로 이동할때 **사용자의 로그인 정보를 쿠키에 담아 이동**하게 되면 만약 이동한 사이트가 보안에 취약할 시 **사용자의 정보가 탈취될 위험**이 있음.

<GET 처리 예시>

```
axios.get("http://localhost:9100/api/test")

GET /api/test
Origin: http://localhost:4545

Access-Control-Allow-Origin // 서버에 이 응답만 있으면 CORS 통과
```

<DELETE 처리 예시>

```
axios.delete("http://localhost:9100/api/test")

OPTIONS /api/test
Origin: http://localhost:4545
Access-Control-Request-Method: DELETE

Access-Control-Allow-Origin
Access-Control-Allow-Methods
Access-Control-Allow-Headers // 서버에 이 응답이 있어야 통과

403
405
CORS error // 서버가 허용하지 않을 시 오류 발생
```
---

### 브라우저 작동 원리

- 사용자가 브라우저 주소창에 `URL`을 입력 + 브라우저가 캐시를 먼저 확인
	- protocol, host 등을 확인 + CSS, JS, 이미지 파일을 저장해둔게 있는지, DNS 캐싱 정보 확인
- DNS(Domain Name System) 조회
	- 컴퓨터는 실제로 IP 주소를 사용해 통신하기 때문에 변환 필요
- 서버와 통신 연결 시작 (TCP 기반)
	- 브라우저(연결 가능한지 확인) - 서버(가능하다면 확인) - 브라우저(연결 시작) = **3-way handshake**
	- HTTPS 프로토콜이라면 TLS도 확인(인증서 확인, 암호화 방식 확인)
- 브라우저가 HTTP 요청을 보냄
```
GET /products/1 HTTP/1.1
Host: www.example.com
User-Agent: Chrome ...
Accept: text/html
Cookie: ...
```
- 서버가 요청을 처리
- 서버가 HTTP 응답을 보냄(HTML 문서 전송)
```
HTTP/1.1 200 OK
Content-Type: text/html
Cache-Control: max-age=3600
Set-Cookie: ...

```
- HTTP 응답을 통해 HTML 파일을 파싱(DOM 생성) + CSS 파일 파싱(CSSOM 생성) + JS 실행(**필요시 Virtual DOM 변경(diff 알고리즘 실행)** -> 여기서 `Vue/React` 동작)
	- **JS 실행할 때 API 호출, 기존 텍스트 변경, 버튼 이벤트 등록 기능 추가**
- DOM + CSSOM 으로 Render Tree 생성
- Layout(or Reflow) : 위치/크기 계산
- Paint 
- Composite(애니메이션 요소, 고정 헤더,비디오나 캔버스)
- Reflow 
- RePaint

+ **Vue/React = DOM을 조작하는 JavaScript 로직**
+ **Vue/React 에서 랜더링 된다 = DOM을 업데이트한다**

---

### attribute vs property
>`attribute` : HTML 태그에 적혀 있는 **초기값** /<br /> `property` : 현재 자바스크립트 객체 **현재 상태값, 브라우저가 HTML 파일을 파싱해서 만든 DOM 객체의 속성**

<예시 코드>
```
<input type="text" value="hello">
```

```
const input = document.getElementById("myInput");

console.log(input.getAttribute("value")); // "hello"
console.log(input.value); // "hello"
```

- `value="hello"` 는 attribute
- `input.value` 는 property

+ attribute 는 처음 HTML 파일에 적힌 값 만을 가지고 있기 때문에 계속 사용자가 바꾸게 되는 입력값들은 거의 항상 property 로 통해 사용
+ **Vue 프레임워크에서 `v-model` 바인딩을 사용할 때 `property` 를 사용하고 있음**

---

### MCP
>MCP(Model Context Protocol) : AI 모델(LLM)이 외부 도구/데이터와 표준 방식으로 연결되도록 하는 인터페이스 규격

- ex) 데이터베이스 조회, 파일 읽기, API 호출, 코드 실행, 외부 서비스 연동

```
[ 사용자 ]
     ↓
[ AI 모델 (LLM) ]
     ↓
[ MCP ]
     ↓
[ 외부 도구 / API / DB / 파일 시스템 ]
```

---

### Core Web Vitals
>프론트엔드 코드·리소스를 최적화하여 사용자경험(UX)를 향상시키기 위한 지표

- **주요 성능 지표** : `LCP`, `INP`, `CLS`

#### LCP(Largest Contentful Paint)
>사용자가 페이지에 들어왔을 때 화면에 보이는 가장 큰 콘텐츠가 랜더링되기까지 걸린 시간

- **LCP 수치 기준** : 좋음 · 2.5초 이하 / 개선 필요 · 2.5 ~ 4초 / 나쁨 · 4초 초과
- **주요 최적화 방법** : 이미지일 경우 이미지 최적화 / 무거운 라이브러리 lazy import / 초기 랜더링 필요 없는 기능 지연 로딩 / 불필요한 computed·watch 삭제

#### INP(Interaction to Next Paint)
>사용자가 클릭, 탭, 키보드 입력을 했을 때 페이지가 얼마나 빠르게 반응하는 시간

- **주요 문제** : 버튼·체크박스 등 클릭 시 실행 시간이 느림 / 탭 전환이 느림 / 필터링 반응 느림

#### CLS(Cumulative Layout Shift)
>페이지가 로딩되면서 화면 요소의 위치가 부자연스럽게 움직이는 정도를 측정

- **주요 문제** : 페이지에서 광고 등 컨텐츠가 늦게 랜더링되면서 버튼 등의 위치가 갑자기 바뀜 /
이미지가 늦게 랜더링되면서 텍스트 등의 요소의 위치가 갑자기 변경

#### 확인 방법

- Chrome DevTools : Chrome 개발자 도구 - Performance 탭
- Lighthouse : Chrome 개발자 도구 - Lighthouse 탭
- PageSpeed Insights : 배포된 사이트에서 사용

#### Microsoft Clarity
>웹사이트나 앱에서 사용자가 실제로 어떻게 활동하는지 확인할 수 있는 도구

- 사용자 행동 세션 녹화, 사용자 이용 히트맵 등을 확인 가능


---

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

## 📗 React

### 데이터 선언

- Vue : `ref`, `reactive` / React : `useState`
- React 에서는 데이터 값을 직접 변경하지 않고 `setter` 함수를 이용

```
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  const [user, setUser] = useState({
    name: "홍길동",
    age: 20,
  });
  
  setCount(count + 1);

  return (
    <div>
      <p>{count}</p>
      <p>{user.name}</p>
    </div>
  );
}

export default App;

```

### 이벤트 함수 선언

- Vue : `@click`, `@change` / React : `onClick`, `onChange`, `onSubmit`

```
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  const increase = () => {
    setCount(count + 1);
  };

  return <button onClick={increase}>증가</button>;
}

export default Counter;
```


### 양방향 바인딩

- Vue : `<input v-model="title" />`
- React : 
```
<input
  value={title}
  onChange={(e) => setTitle(e.target.value)}
/>
```

### computed vs useMemo

- useMemo 함수 내 배열에 선언된 데이터 값이 바뀔때 다시 계산

Vue

```
<script setup>
import { ref, computed } from "vue";

const price = ref(10000);
const count = ref(2);

const totalPrice = computed(() => {
  return price.value * count.value;
});
</script>
```

React

```
import { useMemo, useState } from "react";

function PriceExample() {
  const [price, setPrice] = useState(10000);
  const [count, setCount] = useState(2);

  const totalPrice = useMemo(() => {
    return price * count;
  }, [price, count]);

  return <p>총 가격: {totalPrice}</p>;
}

export default PriceExample;
```

### watch vs useEffect

Vue

```
<script setup>
import { ref, watch } from "vue";

const keyword = ref("");

watch(keyword, (newValue) => {
  console.log("검색어 변경:", newValue);
});
</script>
```

React

```
import { useEffect, useState } from "react";

function SearchExample() {
  const [keyword, setKeyword] = useState("");

  useEffect(() => {
    console.log("검색어 변경:", keyword);
  }, [keyword]);

  return (
    <input
      value={keyword}
      onChange={(e) => setKeyword(e.target.value)}
    />
  );
}

export default SearchExample;
```


### onMounted vs useEffect

- useEffect 두 번째 인자로 빈 배열을 넣으면 컴포넌트가 처음 랜더링된 뒤 한번만 실행

```
useEffect(() => {
  // 처음 한 번만 실행
}, []);
```

### API 호출

```
import { useEffect, useState } from "react";
import axios from "axios";

function PostList() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(false);

  const fetchPosts = async () => {
    setLoading(true);

    try {
      const res = await axios.get("/api/posts");
      setPosts(res.data);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchPosts();
  }, []);

  return (
    <div>
      {loading && <p>로딩 중...</p>}

      {posts.map((post) => (
        <div key={post.id}>
          <h3>{post.title}</h3>
          <p>{post.content}</p>
        </div>
      ))}
    </div>
  );
}

export default PostList;
```

### 리스트 랜더링

Vue

```
<template>
  <div v-for="item in list" :key="item.id">
    {{ item.name }}
  </div>
</template>
```

React

```
{list.map((item) => (
  <div key={item.id}>
    {item.name}
  </div>
))}
```

### 조건부 랜더링

Vue : `v-if`, `v-else`, `v-show` / React : `삼항 연산자`, `&&`

```
{isLogin ? (
  <p>로그인 상태입니다.</p>
) : (
  <p>로그인해주세요.</p>
)}
```

---


## 📘 JavaScript

### <span style="color: red;">Array Method</span>

#### map()
>원본 배열의 **각 요소를 하나씩 바꿔서 새로운 배열을 만드는** 메서드

- `map` 메서드를 호출한 후에도 원본 배열은 변하지 않는다.

<기본 형태>
```
array.map((item, index) => {
  return 바꿀값;
});

const numbers = [1, 2, 3];

const result = numbers.map((item) => {
  return item * 2;
});

console.log(result); // [2, 4, 6]
```

<객체 배열에서 원하는 속성만 출력>
```
const users = [
  { id: 1, name: "김철수" },
  { id: 2, name: "이영희" },
  { id: 3, name: "박민수" }
];

const names = users.map((item) => item.name);

console.log(names); // ["김철수", "이영희", "박민수"]
```
<데이터 가공>
```
const users = [
  { id: 1, name: "김철수", age: 20 },
  { id: 2, name: "이영희", age: 22 }
];

const result = users.map((item) => {
  return {
    userId: item.id,
    userName: item.name
  };
});

console.log(result);

[
  { userId: 1, userName: "김철수" },
  { userId: 2, userName: "이영희" }
]
```

<실무 예시 코드>
- 서버에서 받은 각 `item` 에 **기존 속성은 그대로 둔 상태**로 **속성 추가하여 새로운 배열**로 생성 

```
const list = [
  { id: 1, name: "A" },
  { id: 2, name: "B" }
];

const newList = list.map((item) => ({
  ...item,
  checked: false
}));


[
  { id: 1, name: "A", checked: false },
  { id: 2, name: "B", checked: false }
]
```

<실무 예시 코드 2>
- `axios.post` 를 통해 서버에 보낼 `payload` 만들기

```
const users = [
  { id: "a@test.com", name: "홍길동", role: 2 },
  { id: "b@test.com", name: "김철수", role: 4 }
];

const payload = {
  roleList: users.map((item) => ({
    id: item.id,
    name: item.name,
    vsocRole: item.role
  }))
};

console.log(payload);
```

#### every()
>배열의 모든 요소가 조건을 만족하면 `true` , 하나라도 만족하지 않으면 **즉시** `false` 반환

```
const arr = [2, 4, 6];
arr.every(n => n % 2 === 0); // true (전부 짝수)

const arr2 = [2, 3, 6];
arr2.every(n => n % 2 === 0); // false (3에서 바로 탈락)
```

- 주로 프론트엔드에서 입력값에 대한 유효성 검사를 할때 사용
- 사용 시 주의할 점 : 빈 배열은 항상 true

#### some()
>배열의 모든 요소 중 하나라도 만족하면 'true'

```
arr.some(v => v === "ERROR");
```

#### find()
>배열 중 조건을 만족하는 첫 요소 1개를 반환

```
const firstInvalid = arr.find(v => v === "");
```

#### filter()
>배열 중 조건을 만족하는 것만 추출해서 반환

```
const invalids = arr.filter(v => v == null || v === "");
```

#### splice()
>배열의 특정 위치에서 요소를 **삭제하거나 추가하거나 교체**하는 메서드

- 메서드 사용시 **원본 배열을 직접 바꾼다**

<기본 문법 사용>
```
array.splice(시작인덱스, 삭제할개수, 추가할값1, 추가할값2, ...)
```

<삭제 사용>
```
const arr = [10, 20, 30, 40];
arr.splice(1, 2);

console.log(arr); // [10, 40]
```

<추가 사용>
```
const arr = [10, 30, 40];
arr.splice(1, 0, 20);

console.log(arr); // [10, 20, 30, 40]
```

<교체 사용>
```
const arr = [10, 20, 30];
arr.splice(1, 1, 999);

console.log(arr); // [10, 999, 30]
```

#### reduce()
>배열의 요소들을 **하나씩 순회하면서 하나의 결과값 으로 누적**하는 메서드

<기본 문법 사용>
```
array.reduce((acc, cur) => {
  return 누적결과;
}, 초기값);
```

<합계 구하기>
```
const numbers = [1, 2, 3, 4];

const sum = numbers.reduce((acc, cur) => {
  return acc + cur;
}, 0);

console.log(sum); // 10
```

<최대값 구하기>
```
const numbers = [3, 7, 2, 9, 5];

const max = numbers.reduce((acc, cur) => {
  return acc > cur ? acc : cur;
}, numbers[0]);

console.log(max); // 9
```
<객체 배열의 총합 구하기>
```
const products = [
  { name: "A", price: 1000 },
  { name: "B", price: 2000 },
  { name: "C", price: 3000 }
];

const totalPrice = products.reduce((acc, cur) => {
  return acc + cur.price;
}, 0);

console.log(totalPrice); // 6000
```

<배열을 객체로 바꾸기>
```
const users = [
  { id: 1, name: "Kim" },
  { id: 2, name: "Lee" },
  { id: 3, name: "Park" }
];

const userMap = users.reduce((acc, cur) => {
  acc[cur.id] = cur.name;
  return acc;
}, {});

console.log(userMap);
// { 1: "Kim", 2: "Lee", 3: "Park" }
```

<실무 코드>
```
// 분석후 공격가능성/합계 데이터 상태값
const postSum = computed(() => {
  return [
    Number(postData.value.data.postAfElapsedTime),
    Number(postData.value.data.postAfTechnicalExpertise),
    Number(postData.value.data.postAfKnowledgeOfItem),
    Number(postData.value.data.postAfWindowOfOpportunity),
    Number(postData.value.data.postAfEquipment),
  ].reduce(( sum, value ) => sum + value, 0);
});

```

#### foreach()
>배열을 **하나씩 순회하면서 작업 수행**

```
const arr = [1, 2, 3];

arr.forEach(item => {
  console.log(item);
});
```

#### push(), pop(), shift()

- `push` : 배열 맨 뒤에 요소를 추가
- `pop` : 배열 맨 뒤에 요소를 하나 제거
- `shift` : 배열 첫 번째 요소를 하나 제거


---

### 스프레드 문법(...변수명)
>`...변수명` 은 스프레드 문법(spread syntax)로 **묶여 있는 값을 펼쳐서 꺼내는 문법**이다.

- 배열을 복사/합칠때 사용, 객체에 복사/속성을 추가 할때 사용, 함수 인자로 펼칠때 주로 사용

<배열을 합칠때>
- `concat()` 메서드 처럼 사용

```
const arr1 = [1, 2];
const arr2 = [3, 4];

const result = [...arr1, ...arr2];
console.log(result); // [1, 2, 3, 4]
```

<객체에 속성 추가할때>
```
const user = {
  name: "홍길동",
  age: 20
};

const newUser = {
  ...user,
  checked: false
};

console.log(newUser);
// { name: "홍길동", age: 20, checked: false }
```

<객체 값 덮어쓰기>
- 뒤에 쓴 값이 우선됨

```
const user = {
  name: "홍길동",
  age: 20
};

const newUser = {
  ...user,
  age: 30
};

console.log(newUser);
// { name: "홍길동", age: 30 }
```

<함수 인자에서 사용>

```
const nums = [3, 7, 2];
const max = Math.max(...nums);

console.log(max); // 7
```

<실무 예시 코드>
- `pinia` 의 `app.js` 파일에서 store 상태 갱신 할때 사용

```
setLoadingInfo(info) {
  this.loadingInfo = { ...info };
}
```

- 업데이트 페이지에서 기존 데이터 가져올때 사용
```
// Source 수정에 필요한 Source 상세조회 API 호출
const getfetch_source_detail = async() => {
  try {
    const sourceId = route.params.sourceId;
    const res = await axios.get(`/api/vsoc/ext-vulns/source/${sourceId}`);
    getData.value = res.data.data.extVulnsSource;

    postData.value = { id: Number(route.params.sourceId), ...getData.value }

  } catch (error) {
    console.error(error);
    alert("데이터 불러오기 실패");
  }
}
```

---