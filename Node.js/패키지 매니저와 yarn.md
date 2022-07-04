# 패키지 매니저와 yarn

패키지 매니저와 의존성 등 다양한 단어의 의미를 확실하게 잡고, yarn의 특징을 알아보자.

## 패키지 매니저

### 패키지

패키지란, 라이브러리와 비슷한 개념으로 코드의 배포를 위해 사용되는 코드의 묶음이라고 보면 된다.

- 라이브러리: 코드 작성을 위해 필요한 코드 모음

- 패키지: 코드의 배포를 위해 사용되는 코드 모음

일반적으로 패키지는 다음을 포함한다.

1. 라이브러리

2. 실행 파일

3. 컴파일한 binary

4. 환경 설정 정보(configuration)

5. 의존성 정보(dependency)

### 의존성

의존성이란 어떤 패키지를 동작하기 위해 필수적으로 필요한 다른 패키지를 의미한다.

수많은 의존성이 존재하기 때문에 의존성은 수동으로 관리하기 불가능하다.

따라서 각각의 패키지가 자신의 의존성 정보를 갖게 하여 패키지 매니저가 자동으로 설치하도록 한다.

### 패키지 관리와 패키지 매니저

패키지를 설치, 업데이트, 수정, 삭제하는 작업을 패키지 관리라고 하는데,

이 작업을 자동화하고, 안전하게 처리하기 위해 사용되는 도구가 패키지 매니저다.

패키지 매니저는 다음과 같은 내용들을 수행한다.

1. 의존성 과리

2. 패키지 보안 관리(신뢰, 손상 관련 보장)

3. 기능에 따른 분류

4. 압축 해제

5. 소프트웨어 레포지토리\*로부터 패키지 찾기, 다운로드, 설치, 업데이트

- 소프트웨어 레포지토리(Software Repository): 패키지를 저장하고 관리하는 저장소

  - 성능 문제와 위기 상황 대처를 위해 여러 개로 분리되어 있고 각각의 저장소가 동일한 기능을 수행한다.

  - 소프트웨어 레포지토리에 대한 위치 정보를 관리하는 환경 설정 파일이 존재하므로, 그 경로를 참고해서 접근하는 방식을 사용한다.

  - Node.js는 npm과 yarn이라는 패키지 매니저와 소프트웨어 레포지토리를 가지고 있다.

## yarn과 npm의 비교

yarn에 대해 알아보기 전에 간단하게 이전에 배우고 사용했었던 npm에 대해 다시 간략하게 정리해보자.

npm은 자바스크립트 라이브러리를 설치하거나 관리할 수 있는 도구로, node.js를 설치할 때 자동으로 설치된다.

필수 단계를 순차적으로 수행하는 경향이 있기 때문에 다음 패키지로 넘어가기 전에 각 패키지를 완전히 설치해야 한다.

`node_modules` 폴더에 라이브러리를 내려 받아 저장하고, `package.json`이라는 파일에 설치된 라이브러리의 정보를 기록하여 저장한다.

의존 관계를 가지는 다른 패키지들이 즉시 포함되도록 하여 편리하게 사용할 수 있지만, 보안에 있어 취약점들을 불러올 수 있다.

npm의 부족한 부분들을 개선하기 위해 facebook에서 개발되었으며, 간단하게 npm과 비교해 설치 절자가 바뀌었다고 생각하면 쉽다.

npm과도 호환이 되며 속도나 안정성 측면에서 npm보다 향상되어, 가벼운 느낌을 준다.

### 속도

다운로드 받은 패키지는 캐시에 저장되어 중복되는 패키지는 캐시에 저장된 것을 재사용한다.

여러 패키지를 순차적으로 설치하는 npm과 다르게 yarn은 패키지들을 동시에 가져와 설치하도록 최적화 되어있다.

<del>따라서 패키지의 크기가 크면 클수록 yarn이 속도 면에서 우세하다.</del>

npm 5.0이후 버전은 속도가 많이 향상되어 yarn과 큰 차이는 없다고 한다.

### 안정성

또한 `yarn.lock` 또는 `package.json` 파일에 있는 파일만을 설치하기 때문에 <del>보안이 더 뛰어나다고 한다.</del>

이런 yarn의 설치 방식은 모든 디바이스에 같은 패키지들을 설치하는 것을 보장하기 때문에,

패키지의 버전 차이로 인한 오류를 방지할 수 있다.

최근에는 npm 역시 보안 부분에서 업데이트를 여러 번 거듭하여 보안성이 향상되었다고는 한다.

### Syntax

사용하는 CLI Syntax에 약간의 차이가 있다.

<table>
  <thead>
    <tr>
      <td>npm</td>
      <td>yarn</td>
      <td>설명</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>node.js 내에 포함</td>
      <td>npm install -g yarn</td>
      <td>yarn 패키지 전역 설치</td>
    </tr>
    <tr>
      <td>npm init</td>
      <td>yarn init</td>
      <td>package.json 생성</td>
    </tr>
    <tr>
      <td>npm install (npm i)</td>
      <td>yarn install (yarn i)</td>
      <td>package.json 파일 및 종속된 모든 모듈 설치</td>
    </tr>
    <tr>
      <td>npm install <strong>package_name@_VER_</strong></td>
      <td>yarn add <strong>package_name@_VER_</strong></td>
      <td>패키지의 특정 버전 설치</td>
    </tr>
    <tr>
      <td>npm install -g <strong>package_name</strong></td>
      <td>yarn add global <strong>package_name</strong></td>
      <td>전역 설치<br />로컬의 다른 프로젝트도 해당 패키지 사용 가능</td>
    </tr>
    <tr>
      <td>npm insatll <strong>package_name</strong></td>
      <td>yarn add <strong>package_name</strong></td>
      <td>특정 저장소의 패키지 설치</td>
    </tr>
    <tr>
      <td>npm uninstall <strong>package_name</strong></td>
      <td>yarn remove <strong>package_name</strong></td>
      <td>패키지 삭제</td>
    </tr>
    <tr>
      <td>npm update <strong>package_name</strong></td>
      <td>yarn upgrade <strong>package_name</strong></td>
      <td>패키지 업데이트</td>
    </tr>
    <tr>
      <td>npm dedupe</td>
      <td>yarn dedupe</td>
      <td>중복 설치된 패키지 정리</td>
    </tr>
  </tbody>
</table>

### npm과 yarn의 중복 사용

`package-lock.json`과 `yarn.lock`과 같은 의존성 관련 파일이 2개 생긴다.

이는 개발자에게 혼란을 야기할 수 있으며, 패키지 관리의 책임이 두 곳으로 분산되기도 한다.

따라서 npm과 yarn 둘 다 사용하는 것은 좋지 않고, 어떤 패키지를 쓸 것인지 정하고 하나의 패키지만 사용해야 한다.
