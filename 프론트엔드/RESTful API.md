# RESTful API

라이징 캠프에서 처음으로 백엔드 파트 개발자 분이랑 협업을 하면서 api 연동을 본격적으로 해봤다.

수업에서 REST에 대해 살짝 공부를 해봤지만, 추상적인 느낌만 알고 얼렁뚱땅 api 연동에만 신경쓰다보니

REST가 정확히 뭔지, RESTful하다는 게 어떤 건지, 누군가가 물어보면 대답하지 못할 것 같아서 정리해본다.

<br />

<hr />

### REST(REpresentational State Transfer)

REST는 **자원을 이름(자원의 표현)으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것**을 의미한다고 한다.

- 자원: 해당 소프트웨어가 관리하는 모든 것을 뜻하는 말로 데이터베이스 안에 들어가 있는 데이터 하나하나 등이라고 생각하면 쉽다.

- 상태의 전달: 데이터가 요청되는 시점에서 요청받은 리소스의 상태(정보)를 전달하는 것, 보통 JSON이나 XML 형태를 이용해서 자원의 상태를 전달한다.

REST의 구체적인 개념을 정의하자면, HTTP URL을 통해 자원을 명시하고, HTTP Method(POST, GET, DELETE, PUT)을 통해 해당 자원에 대한 CRUD(CREATE, READ, UPDATE, DELETE) 오퍼레이션을 적용하는 것이라고 할 수 있다.

<br />

<hr />

### REST의 구성 요소

    REST는 "자원을 이름(자원의 표현)으로 구분하여 해당 자원의 상태(정보)를 주고 받는 모든 것"을 의미한다고 한다.

여기서 REST의 중요한 세 가지 구성 요소가 모두 나온다.

1. 자원(Resource): URI\*

   - 모든 자원에는 고유한 ID가 존재하고, 이 자원은 Server에 존재한다.

   - 자원을 구별하는 ID는 HTTP URL로 구분하게 된다. (ex. /members/123)

   - Client는 URI를 이용하여 자원을 지정하고, 해당 자원에 대한 조작을 Server에 요청한다.

2. 행위(Verb): HTTP Method

   - Client는 HTTP Method(POST, GET, DELETE, PUT)를 이용해 지정한 자원에 대한 조작을 요청한다.

3. 표현(Representation of Resource)

   - Client가 Server에 자원에 대한 조작을 요청하면 Server는 이에 대한 적절한 응답(Representation)을 보낸다.

<br />

#### \* URI: 인터넷에 있는 정보의 자원을 표현하는 방법으로 아래와 같은 주소 형식이다.

```
http://restapi.uri.com/member/
```

<br />

<hr />

### REST의 특징

REST에는 다음과 같이 크게 6가지의 특징이 있다.

1. Uniform (유니폼 인터페이스)

   - Uniform Interface는 URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일을 말한다.

2. Stateless (무상태성)

   - REST는 무상태성을 갖고 있어서 작업을 위한 상태정보를 따로 저장하고 관리하지 않는다. 세션 정보나 쿠키 정보를 별도로 저장하고 관리하지 않기 때문에 API 서버는 들어오는 요청만을 단순하게 처리하면 된다. 때문에 서비스의 자유도는 높아지고 서버에서 불필요한 정보를 관리하지 않아 구현이 단순해진다.

3. Cacheable (캐시 가능)

   - REST는 HTTP라는 기존의 웹 표준을 그대로 사용하기 때문에 웹에서 사용하는 기존의 인프라를 그대로 활용 가능하다. 따라서 HTTP가 가진 캐싱 기능이 적용 가능하다. HTTP 프로토콜 표준에서 사용하는 Last-Modified 태그나 E-Tag를 이요하면 캐싱 구현이 가능하다.

4. Self-descriptiveness (자체 표현 구조)

   - REST는 REST API 메시지만 보고도 이를 쉽게 이해할 수 있는 자체 표현 구조로 되어 있다.

5. Client-Server 구조

   - REST 서버는 API 제공, 클라이언트는 사용자 인증이나 context(세션, 로그인 정보) 등을 직접 관리하는 구조로 각각의 역할이 확실히 구분되기 때문에 클라이언트와 서버에서 개발해야 할 내용이 명확해지고 서로간 의존성이 줄어든다.

6. 계층형 구조

   - REST 서버는 다중 계층을 구성될 수 있으며 보안, 로드 밸런싱, 암호화 계층을 추가해 구조 상에 유연성을 둘 수 있고 Proxy, Gateway 같은 네트워크 기반의 중간 매체를 사용할 수 있게 한다.

<br />

<hr />

### REST API 디자인 가이드

- REST API 중심 규칙

  - URI는 정보의 자원을 표현해야 한다. (리소스 이름은 동사보다는 명사를 사용한다.)

        GET /members/delete/1

    - 위와 같은 방식은 REST를 제대로 적용하지 않은 URI이다. URI는 자원을 표현하는 데에 중점을 두어야 한다.

  - 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE)로 표현한다.

        DELETE /members/delete/1

    - 위와 같은 방식으로 수정할 수 있겠다.

  - 회원 정보를 가져올 때에는 GET, 회원 추가 등의 조작을 표현하고자 할 때에는 POST 메소드를 사용해서 표현할 수 있다.

        회원정보를 가져오는 URI
        GET /members/1

        회원을 추가할 때
        POST /members/2

  - 다음 네 개의 Method를 가지고 CRUD를 할 수 있다.

    <img src="https://user-images.githubusercontent.com/95613159/172634061-b6c7863b-30d3-42f4-a527-43c7be05af87.png" alt="CRUD" />

    <br />

      <table>
      <thead>
         <tr>
            <td>HTTP 메소드</td>
            <td>CRUD</td>
            <td>여러 번 작업을 수행해도 결과가 동일한가?</td>
         </tr>
      </thead>
      <tbody>
         <tr>
            <td>POST</td>
            <td>CREATE</td>
            <td style="text-align: center;">X</td>
         </tr>
         <tr>
            <td>GET</td>
            <td>READ</td>
            <td style="text-align: center;">O</td>
         </tr>
         <tr>
            <td>PUT/PATCH</td>
            <td>UPDATE</td>
            <td style="text-align: center;">O</td>
         </tr>
         <tr>
            <td>DELETE</td>
            <td>DELETE</td>
            <td style="text-align: center;">O</td>
         </tr>
      </tbody>
      </table>

      <br />

- URI는 자원을 표현하는 데에 집중하고, 행위에 대한 정의는 HTTP Method를 통해 하는 것이 RESTful API를 설계하는 중심 규칙이다.

<br />

- URI 설계 시 주의해야 할 점

  - 슬래시 구분자(`/`)는 계층 관계를 나타내는 데에 사용한다.

        https://example.com/houses/apartments
        https://example.com/animals/mammals/dolphins

  - URI 마지막 문자로 슬래시 구분자(`/`)를 포함하지 않는다.

        https://example.com/houses/apartments/ (x)
        https://example.com/houses/apartments  (o)

    - REST API는 분명한 URI를 만들어 통신을 해야 하기 때문에 혼동을 주지 않도록 URI 경로의 마지막에는 슬래시를 사용하지 않는다.

  - 하이픈(`-`)은 URI의 가독성을 높이는 데에 사용한다.

    - URI를 쉽게 읽고 해석하기 위해 불가피하게 긴 URI 경로를 사용하게 된다면 하이픈을 사용해 가독성을 높일 수 있다.

  - 언더바(`_`)는 URI에 사용하지 않는다.

    - 글꼴에 따라 다르지만, 밑줄은 보기 어렵거나 밑줄 때문에 문자가 가려지기도 한다. 이런 문제를 피하기 위해 밑줄 대신 하이픈(`-`)을 사용하는 것이 가독성 측면에서 좋다.

  - URI 경로에는 소문자가 적합하다.

    - 대소문자에 따라 다른 리소스로 인식하기 때문에 URI 경로에 대문자 사용은 가급적 지양해야 한다.

  - 파일의 확장자명은 URI에 포함하지 않는다.

        https://example.com/members/123/photo.jpg (x)

        GET /members/123/photo HTTP/1.1 Host: example.com Accept: image.png

    - 대신 accept header를 사용할 수 있다.

- 리소스 간의 관계를 표현하는 방법

  - REST 리소스 간에는 연관 관계가 있을 수 있고, 이런 경우 다음과 같은 표현 방법을 사용한다.

        /리소스명/리소스ID/관계가 있는 다른 리소스명

        GET /users/{userId}/devices (소유 관계 표현 시)
        GET /users/{userId}/likes/devices (관계명이 애매하거나 구체적 표현이 필요할 때)

- 자원을 표현하는 컬렉션과 도큐먼트

  - 컬렉션과 도큐먼트의 개념에 대해 알면 URI 설계가 한층 더 쉬워진다. 도큐먼트는 단순히 문서, 한 객체라고 이해하면 된다. 컬렉션 문서들, 객체들의 집합이라고 생각하면 된다. 컬렉션과 도큐먼트는 모두 리소스로 표현할 수 있으며 URI에 표현된다.

        https://example.com/sports/soccer/players/13

    - `sports`, `players` 컬렉션과 `soccer`, `13`을 의미하는 도큐먼트로 URI를 이루고 있다. 여기서 **컬렉션을 복수로 사용**하고 있다는 점이 중요하다. 더 직관적이고 이해하기 쉬운 URI를 설계하고 싶다면 컬렉션과 도큐먼트 사용 시 단/복수를 지켜주면 된다.

<br />

<hr />

### HTTP Status Code(응답 상태 코드)

- 잘 설계된 REST API는 URI뿐만 아니라, 그 리소스에 대한 응답을 적절하게 내어주는 것까지 포함되어야 한다.

- 응답 상태 코드만으로도 성공 및 실패 여부 등의 정보를 전달할 수 있기 때문에, 응답의 상태코드 값을 명확하게 돌려주는 것이 굉장히 중요하다.

#### 100번대: 정보 확인

\-

#### 200번대: 통신 성공

<table>
  <thead>
    <tr>
      <th style="text-align: center;">상태코드</th>
      <th style="text-align: center;">이름</th>
      <th style="text-align: center;">의미</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: center;">200</td>
      <td style="text-align: center;">OK</td>
      <td style="text-align: center;">요청 성공(GET)</td>
    </tr>
    <tr>
      <td style="text-align: center;">201</td>
      <td style="text-align: center;">Create</td>
      <td style="text-align: center;">생성 성공(POST)</td>
    </tr>
    <tr>
      <td style="text-align: center;">202</td>
      <td style="text-align: center;">Accepted</td>
      <td style="text-align: center;">요청 접수 O, 리소스 처리X</td>
    </tr>
    <tr>
      <td style="text-align: center;">204</td>
      <td style="text-align: center;">No Contents</td>
      <td style="text-align: center;">요청 성공 O, 내용 없음</td>
    </tr>
  </tbody>
</table>

#### 300번대: 리다이렉트

<table>
  <thead>
    <tr>
      <th style="text-align: center;">상태코드</th>
      <th style="text-align: center;">이름</th>
      <th style="text-align: center;">의미</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: center;">300</td>
      <td style="text-align: center;">Multiple Choice</td>
      <td style="text-align: center;">요청 URI에 여러 리소스가 존재</td>
    </tr>
    <tr>
      <td style="text-align: center;">301</td>
      <td style="text-align: center;">Move Permanently</td>
      <td style="text-align: center;">요청 URI가 새 위치로 옮겨감</td>
    </tr>
    <tr>
      <td style="text-align: center;">304</td>
      <td style="text-align: center;">Not Modified</td>
      <td style="text-align: center;">요청 URI의 내용이 변경 X</td>
    </tr>
  </tbody>
</table>

#### 400번대: 클라이언트에서의 오류

<table>
  <thead>
    <tr>
      <th style="text-align: center;">상태코드</th>
      <th style="text-align: center;">이름</th>
      <th style="text-align: center;">의미</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: center;">400</td>
      <td style="text-align: center;">Bad Request</td>
      <td style="text-align: center;">API에서 정의되지 않은 요청 들어옴</td>
    </tr>
    <tr>
      <td style="text-align: center;">401</td>
      <td style="text-align: center;">Unauthorized</td>
      <td style="text-align: center;">인증 오류</td>
    </tr>
    <tr>
      <td style="text-align: center;">403</td>
      <td style="text-align: center;">Forbidden</td>
      <td style="text-align: center;">권한 밖의 접근 시도</td>
    </tr>
    <tr>
      <td style="text-align: center;">404</td>
      <td style="text-align: center;">Not Found</td>
      <td style="text-align: center;">요청 URI에 대한 리소스 존재 X</td>
    </tr>
    <tr>
      <td style="text-align: center;">405</td>
      <td style="text-align: center;">Method Not Allowed</td>
      <td style="text-align: center;">API에서 정의되지 않은 메소드 호출</td>
    </tr>
    <tr>
      <td style="text-align: center;">406</td>
      <td style="text-align: center;">Not Acceptable</td>
      <td style="text-align: center;">처리 불가</td>
    </tr>
    <tr>
      <td style="text-align: center;">408</td>
      <td style="text-align: center;">Request Timeout</td>
      <td style="text-align: center;">요청 대기 시간 초과</td>
    </tr>
    <tr>
      <td style="text-align: center;">409</td>
      <td style="text-align: center;">Conflict</td>
      <td style="text-align: center;">모순</td>
    </tr>
    <tr>
      <td style="text-align: center;">429</td>
      <td style="text-align: center;">Too Many Request</td>
      <td style="text-align: center;">요청 횟수 상한 초과</td>
    </tr>
  </tbody>
</table>

#### 500번대: 서버에서의 오류

<table>
  <thead>
    <tr>
      <th style="text-align: center;">상태코드</th>
      <th style="text-align: center;">이름</th>
      <th style="text-align: center;">의미</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align: center;">500</td>
      <td style="text-align: center;">Internal Server Error</td>
      <td style="text-align: center;">서버 내부 오류</td>
    </tr>
    <tr>
      <td style="text-align: center;">502</td>
      <td style="text-align: center;">Bad Gateway</td>
      <td style="text-align: center;">게이트웨이 오류</td>
    </tr>
    <tr>
      <td style="text-align: center;">503</td>
      <td style="text-align: center;">Service Unavailable</td>
      <td style="text-align: center;">서비스 이용 불가</td>
    </tr>
    <tr>
      <td style="text-align: center;">504</td>
      <td style="text-align: center;">Gateway Timeout</td>
      <td style="text-align: center;">게이트웨이 시간 초과</td>
    </tr>
  </tbody>
</table>

<hr />

**REST API는 정해진 명확한 표준이 없기 때문에 '무엇이 옳고 그른지'가 아닌**

**개발하는 서비스의 특징과 개발 집단의 환경과 성향 등이 충분히 고려되어 설계해야 한다.**
