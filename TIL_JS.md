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


### localStorage
>**브라우저에 데이터를 저장**하는 저장소

- 새로고침해도 유지된다.
- 브라우저를 종료하고 다시 실행해도 유지된다.
- **문자열만 저장 가능** -> `JSON.stringify()` 메서드를 통해서 문자열로 변경 후 사용
- **같은 도메인에서만 접근 가능**
- 브라우저 탭을 닫으면 사라지게 하려면 `sessionStorage` 사용 (method 는 동일)

#### localStorage.setItem()
>`localStorage` 에 값을 저장할 때 사용

```
localStorage.setItem('키', '값')
```

#### localStorage.getItem()
>`localStorage` 에서 값을 꺼낼 때 사용

```
const userId = localStorage.getItem('userId')

console.log(userId)
```

- 해당 `key` 가 없으면 `null` 이 출력

#### localStorage.removeItem()
>`localStorage` 에서 값을 삭제할 때 사용

```
localStorage.removeItem('userId')
```

- 전체 `localStorage` 를 삭제할 때에는 `localStorage.clear()` 사용

---

### JSON method

#### JSON.stringify()
>객체나 배열을 문자열로 바꾸는 함수

```
const user = {
  userId: 'test',
  nickname: '밴드유저',
  isLoggedIn: true,
}

const userString = JSON.stringify(user)

localStorage.setItem('bandiaryLoginUser', userString)

{"userId":"test","nickname":"밴드유저","isLoggedIn":true}
```

#### JSON.parse()
>JSON 문자열을 다시 객체로 바꾸는 함수

```
const savedUser = localStorage.getItem('bandiaryLoginUser')

'{"userId":"test","nickname":"밴드유저","isLoggedIn":true}'

const parsedUser = JSON.parse(savedUser)

console.log(parsedUser.userId)
console.log(parsedUser.nickname)
```

---

### navigator
>**브라우저 환경과 사용자 디바이스 정보를 제공**하는 전역 객체

#### 정의
>`window.navigator`

#### 주요 인터페이스
- **`navigator.mediaDevices`** : 미디어 장치 접근

```
navigator.mediaDevices.getUserMedia()
navigator.mediaDevices.getDisplayMedia()
navigator.mediaDevices.enumerateDevices()
```
- `navigator.permissions` : 권한 상태 조회

```
const status = await navigator.permissions.query({ name: "camera" });
console.log(status.state); // granted / denied / prompt
```
- `navigator.userAgent` : 브라우저 정보

- `navigator.onLine` : 네트워크 상태 확인

```
if (!navigator.onLine) {
  alert("인터넷 연결이 끊어졌습니다.");
}
```
- `navigator.geolocation` : 위치 정보

```
navigator.geolocation.getCurrentPosition(pos => {
  console.log(pos.coords.latitude, pos.coords.longitude);
});

```

---


### <span style="color: red">`navigator.mediaDevices`</span>
>**미디어 장치를 접근** 할때 사용하는 인터페이스

- 브라우저가 **사용자의 미디어 장치를 관리/제어**할 수 있도록 제공
- HTTPS 환경에서만 동작
- 주로 화면 공유, **실시간 미디어 스트림(WebRTC)** 생성에 사용

#### mediaDevices 주요 메서드
- **`getUserMedia()`** : **카메라 / 마이크 스트림**을 요청 -> 성공 시 **`MediaStream`** 반환

```
navigator.mediaDevices.getUserMedia(constraints)

const stream = await navigator.mediaDevices.getUserMedia({
  video: true,
  audio: true,
});

videoElement.srcObject = stream;

stream.getTracks();       // 모든 트랙
stream.getVideoTracks();  // 비디오만
stream.getAudioTracks();  // 오디오만

stream.getTracks().length; // 보통 2 (audio + video)

stream.getTracks().forEach(track => track.stop()); // MediaStream에 포함된 모든 트랙을 종료
```

- **`getDisplayMedia()`** : **화면 공유**를 요청 (브라우저 탭 / 전체 화면 / 특정 창)

```
const screenStream = await navigator.mediaDevices.getDisplayMedia({
  video: true,
  audio: true
});
```

### srcObject
>**`<video> , <audio>`** 같은 미디어 태그에 **`MediaStream`** 객체를 직접 연결하기 위한 속성

```
<video id="video" autoplay></video>

video.srcObject = stream;
```

### nextTick()
>상태(state) 가 변경되어 **DOM 업데이트가 예약된 뒤**, **실제 DOM 반영이 끝난 다음 시점에 코드를 실행**하기 위한 함수

>Vue3 프로젝트에서는 기본적으로 성능 최적화를 위해 상태 변경 즉시 DOM을 업데이트 하지 않고 비동기적으로 한 번에 처리 (batch)를 하기 때문에 사용

#### 사용 방법
>**`import { nextTick } from "vue";`**

- Promise 방식

```
await nextTick();
// 여기서부터 DOM 업데이트 완료
```
- CallBack 방식

```
nextTick(() => {
  // DOM 접근 가능
});
```

- 미디어 사용 예시 코드

```
import { ref, nextTick } from "vue";

const inputRef = ref(null);

async function open() {
  isOpen.value = true;
  await nextTick();
  inputRef.value.focus();
}

// 미디어 사용 예시
videoRef.value.srcObject = stream;

await nextTick();
videoRef.value.play();
```

---