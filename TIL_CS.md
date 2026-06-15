## 📙 CS

### 프론트엔드에서 Axios Method 호출 시 payload, response 확인 
>개발 과정에서 프론트엔드에서 백엔드의 API 서버와 `Axios` + `REST API`를 사용하여 데이터를 통신할 때 데이터 통신이 제대로 처리되고 있는지 `console.log` 를 통해 로그로 확인이 어려울 때는 웹페이지의 개발자 도구에서 `NETWORK` 탭을 이용해서 확인가능

---

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

### Webpack + Babel vs Vite

- Webpack + Babel 빌드 방식은 개발 서버를 시작할 때 **프로젝트 파일들을 전부 먼저 분석**하고 번들링
- Vite 빌드 방식은 개발 서버를 시작할 때 **브라우저가 필요한 파일만 요청**해서 변환해서 번들링
- Vite 장점 : 개발 서버 시작 속도가 빠름, HMR이 빠름, 설정이 단순해진다

#### Webpack
>여러 파일을 브라우저가 이해할 수 있는 형태로 묶어주는 번들러

- 크게 `Entry` / `Output` / `Loader` / `Plugin` 로 구성됨
- `Loader` : `.vue`, `.css`, `.png`, `.svg` 와 같은 파일을 JavaScript로 변환
ex) vue-loader, css-loader, babel-loader
- `Plugin` : 번들 최적화나 asset 관리 같은 더 넓은 작업에 사용

webpack.config.js

```
module.exports = {
  entry: './src/main.js',
  output: {
    filename: 'bundle.js',
    path: __dirname + '/dist'
  }
}
```

#### Babel
>최신 JavaScript 문법을 오래된 브라우저에서도 동작할 수 있는 JavaScript 로 변환해주는 컴파일러


#### Vite
>최신 프론트엔드 개발에 필요한 빠르고 간단한 빌드 도구

- Native ES Modules 기반으로 빠르게 실행
- Rollup 기반으로 최적화된 번들 생성

---

### Volta
>`Node.js`, `npm`, `yarn` 같은 JavaScript 실행 도구의 버전을 관리해주는 도구

- 예를 들어 해당 프로젝트에서만 Node 버전을 16으로 테스트 해야할 때 사용

#### 사용법

1. Volta 설치 : `curl https://get.volta.sh | bash` (mac 기준) -> `volta -v` 로 확인

2. 해당 프로젝트 루트 경로에서 `volta pin node@16.20.2` -> `package.json` 에 추가됨

```
{
  "volta": {
    "node": "16.20.2"
  }
}
```

3. PATH 설정

```
export VOLTA_HOME="$HOME/.volta"
export PATH="$VOLTA_HOME/bin:$PATH"
```

- `which node` 명령어를 입력했을 때
`/opt/homebrew/bin/node` (반영 x) -> Homebrew Node가 먼저 잡힌 상태
`/Users/사용자명/.volta/bin/node` (반영 o)


4. 설치 후 확인

`node -v`

5. 기존 package 제거 후 다시 설치

```
rm -rf node_modules package-lock.json
npm install
npm run dev
npm run build
```

---

### WebSocket
>서버와 브라우저가 연결을 계속 유지하면서 **실시간으로 데이터를 주고받는 통신 방식**

- 일반 HTTP 방식으로 채팅을 구현할 때는 일정 시간마다 데이터를 요청받아야 함 = `Polling` 방식
```
setInterval(() => {
  fetch("/api/messages")
}, 3000)
```

- 주로 **채팅, 알림, 실시간 차트, 실시간 위치, 게임서비스** 등에 사용
- 브라우저 새로고침 시 연결이 끊기고 다시 연결해야함
- HTTPS 환경에서는 `wss://` 를 사용해야함

<예시 코드>
```
const socket = new WebSocket("ws://localhost:8080/ws")
```

```
const socket = new WebSocket("ws://localhost:8080/ws")

socket.onopen = () => {
  console.log("WebSocket 연결 성공")
}

socket.onmessage = (event) => {
  console.log("서버에서 받은 메시지:", event.data)
}

socket.onerror = (error) => {
  console.error("WebSocket 에러:", error)
}

socket.onclose = () => {
  console.log("WebSocket 연결 종료")
}
```

#### React 에서 구현 예시

- 주로 `useEffect` Hook 을 이용하여 구현

```
import { useEffect, useState } from "react"

function ChatPage() {
  const [messages, setMessages] = useState([])
  const [input, setInput] = useState("")
  const [socket, setSocket] = useState(null)

  useEffect(() => {
    const ws = new WebSocket("ws://localhost:8080/ws")

    ws.onopen = () => {
      console.log("WebSocket 연결 성공")
    }

    ws.onmessage = (event) => {
      const data = JSON.parse(event.data)

      setMessages((prev) => [...prev, data])
    }

    ws.onerror = (error) => {
      console.error("WebSocket 에러:", error)
    }

    ws.onclose = () => {
      console.log("WebSocket 연결 종료")
    }

    setSocket(ws)

    return () => {
      ws.close()
    }
  }, [])

  const sendMessage = () => {
    if (!socket || socket.readyState !== WebSocket.OPEN) {
      alert("서버와 연결되어 있지 않습니다.")
      return
    }

    socket.send(JSON.stringify({
      type: "CHAT_MESSAGE",
      message: input
    }))

    setInput("")
  }

  return (
    <div>
      <h2>채팅</h2>

      <ul>
        {messages.map((message, index) => (
          <li key={index}>{message.message}</li>
        ))}
      </ul>

      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="메시지를 입력하세요"
      />

      <button onClick={sendMessage}>전송</button>
    </div>
  )
}

export default ChatPage
```

#### readyState
>현재 WebSocket 을 **사용할 수 있는 지에 대한 상태 확인** 메서드

```
if (socket.readyState === WebSocket.OPEN) {
  socket.send(JSON.stringify(data))
}
```

| 값   | 상수                     | 의미    |
| --- | ---------------------- | ----- |
| `0` | `WebSocket.CONNECTING` | 연결 중  |
| `1` | `WebSocket.OPEN`       | 연결 완료 |
| `2` | `WebSocket.CLOSING`    | 닫는 중  |
| `3` | `WebSocket.CLOSED`     | 닫힘    |



#### WebSocket vs Socket.IO

- 백엔드 서버가 순수 WebSocket , Socket.IO 인지에 따라 프론트엔드도 동일하게 사용해야함

| 구분      | WebSocket       | Socket.IO          |
| ------- | --------------- | ------------------ |
| 정체      | 브라우저 표준 API     | WebSocket 기반 라이브러리 |
| 사용 방식   | 직접 연결 관리 필요     | 연결 관리 기능 제공        |
| 자동 재연결  | 직접 구현           | 기본 제공              |
| 이벤트 이름  | 직접 type 설계      | `emit`, `on` 사용    |
| 백엔드 호환성 | WebSocket 서버 필요 | Socket.IO 서버 필요    |



---