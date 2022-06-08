# CORS(Cross-Origin Resourse Sharing)

앱을 개발하거나 프로젝트를 진행하면서 프론트엔드 개발자에게 API 연동 중 한 번쯤은 만나게 되는 에러가 바로 CORS 에러다.

<img src="https://blog.container-solutions.com/hs-fs/hubfs/redscreenshot.png?width=2760&name=redscreenshot.png" alt="cors error"/>

개인 프로젝트 진행할 때 카카오맵 api를 연동하면서 한 번 발생한 적이 있었고, 그건 내가 사이트 도메인을 제대로 적어주지 않아서 발생한 문제였다.

백엔드 개발자랑 처음으로 협업을 진행하면서도 이 오류를 접했는데, 이 때에도 하루종일 붙잡고 있었던 기억이 난다.

물론 이 문제도 백엔드 개발자에게 요청해서 금방 해결될 수 있는 문제였긴 했다.

그만큼 프론트엔드 개발자에게 CORS 에러는 한 번씩은 만날 법한 오류이다.

<hr />

### CORS

- 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 앱이 다른 출처의 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제이다.

- 웹 앱은 리소스가 자신의 출처(프로토콜, 포트)와 다를 때 `교차 출처 HTTP 요청(Cross-Origin HTTP 요청)`을 실행한다.

  - https://a.com의 프론트엔드 JS 코드가 XMLHttpRequest를 이용해서 https://b.com/data.json을 요청하는 경우

  - 클라이언트 포트가 3000번이고 서버의 포트가 8000번인데, 클라이언트에서 서버로 리소스를 요청하는 경우

<img src="https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/cors_principle.png" alt="cross-origin-resourse-sharing" style="background-color: white;"/>

- 위 경우, 브라우저는 보안 상의 이유로 스크립트에서 시작한 Cross-Origin HTTP 요청을 제한하게 된다.

  - 이는 `Same-Origin-Policy(동일 근원 정책)`이다.

  - 요청을 보내려면 요청을 보내고자 하는 대상과 프로토콜과 포트가 모두 같아야 함을 의미한다.

  - 즉, API를 사용하는 웹 앱은 자신의 출처와 동일한 리소스만 불러올 수 있으며, 다른 출처로의 리소스를 불러오기 위해서는 그 출처에서 올바른 CORS 헤더를 포함한 응답을 반환해야 한다.

#### 요약하자면

- CORS는 서로 다른 출처(Origin) 간에 리소스를 공유할 수 있게 해주는 것으로,

- CORS 요청이 가능하려면 서버에서 특정 헤더인 `Access-Control-Allow-Origin`과 함께 응답해야 한다.

<hr />

### 해결 방법: 프록시 서버 이용하기

클라이언트에서 외부 서버로 바로 요청하는 것이 아닌 **프록시 서버를 이용하여 우회**하는 방법이다.

- 프록시 서버: 브라우저와 서버 간의 통신을 도와주는 중계 서버로, 클라이언트가 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해 준다.

구글링을 하다보면 가장 많이 사용되는 프록시 서버가 아래 서버이다.

```
https://cors-anywhere.herokuapp.com
```

이 서버를 사용하면 중간에 요청을 가로채서 HTTP 응답 헤더에 `Access-Control-Allow-Origin: *`을 설정해서 응답해준다.

```
axios({
  method: "GET",
  url: `https://cors-anywhere.herokuapp.com/https://api.abc.com/data`,
  headers: {
    "X-ACCESS-TOKEN": token,
  },
})
```

이렇게 요청해야 하는 URL(`https://api.abc.com/data`) 앞에 프록시 서버의 URL을 붙여서 요청하면, 클라이언트에서 서버로 리소스를 요청할 때 발생하는 CORS 에러를 간단하게 해결할 수 있다.

<hr />

### 기타 해결방법

사실 이미 존재하는 프록시 서버를 사용하는 것 말고도 다른 방법이 있다.

하지만 직접 프록시 서버를 구축하는 방법이라 아직 관련 지식이 없는 내게는 어려운 내용이라는 판다이 들어다음에 공부하기로 한다.

추후에 공부할 수 있도록 아래에 URL을 남겨두겠다.

[CORS 에러 해결 방법](https://xiubindev.tistory.com/115)

[관련 추가 내용](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
