# JSON 줄바꿈

[MPList](https://github.com/soonzero/mplist) 개인 프로젝트에서 lyrics.ovh의 가사 조회 api를 연동하던 중 가사에서 줄바꿈이 전혀 되지 않는 경우가 발생했다.

<img src="https://user-images.githubusercontent.com/95613159/174630100-d01242af-9d6a-4e8f-913f-a301a1f4261f.png" alt="lyrics-1" />

그래서 json 데이터를 확인해보니 다음과 같이 줄바꿈 이스케이프 코드인 `\n`이 있는 걸 확인할 수 있었다.

<img src="https://user-images.githubusercontent.com/95613159/174630387-0d1bb6e9-7a7d-43b9-bec2-f52efc66f90b.png" alt="lyrics-2" />

<hr />

## 해결 방법

css 스타일링 시 `white-space` 코드를 입력하면 해결할 수 있다.

```
white-space: pre-wrap;

// or

white-space: pre-line;
```

- 공통점: 공백을 코드에 표시해주며, 코드에 줄바꿈이 없어도 자동 줄바꿈이 된다.

- `pre-wrap`: 공백을 코드에 있는 그대로 표시한다.

- `pre-line`: 공백을 여러 개 넣어도 1개만 표시한다.

### Tailwind

MPList의 퍼블리싱은 Tailwind를 사용하고 있기 때문에, Tailwind에서 `white-space` 프로퍼티는 다음과 같이 설정할 수 있다.

<img src="https://user-images.githubusercontent.com/95613159/174631427-d9c4bdaa-3f1d-48ff-9229-6db55131acf9.png" alt="white-space-tailwind"/>

<br />

MPList 프로젝트에서는 다음과 같이 추가해줬다.

<br />

<img src="https://user-images.githubusercontent.com/95613159/174631697-cc063b84-6859-4d0f-8d43-c48acc26be37.png" alt="white-space-mplist-1"/>

<br />

이렇게 깔끔하게 줄바꿈 문제가 해결된 것을 볼 수 있다.

<img src="https://user-images.githubusercontent.com/95613159/174631907-05aa346c-bd85-4330-8a84-383973b59bc7.png" alt="white-space-mplist-2" />
