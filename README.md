# [라포랩스] 프론트엔드 엔지니어 인턴 과제 전형 - 윤강

### 요구사항 분석

1. "게임 시작" 버튼을 클릭해 게임을 시작하면 "꾹 눌러주세요~" 버튼이 나타난다.
2. "꾹 눌러주세요~" 버튼을 누르면 사과가 낙하하기 시작한다.
3. 사과의 바로 위에 도착선으로부터 남은 거리(m)를 보여준다.
4. 사과 낙하 도중에 손을 떼면 게임은 종료된다.
5. 게임이 종료되었을 때 도착선으로부터 사과의 거리가 400m 이내라면 성공한 것이고, 400m 이상이거나 도착선을 넘은 경우 실패한다.
6. 게임 성공 여부에 따라 0.5초 후 결과를 모달로 보여주고, 모달은 "재시도" 버튼을 제공한다.

<br /><br />

### 시연 영상

<table align="center">
  <tr>
    <th>성공</th>
    <th>실패</th>
  </tr>
  <tr>
    <td>
      <img src="https://github.com/user-attachments/assets/20e586e5-1812-476d-8ab8-b78154ebd6d9" alt="성공" />
    </td>
    <td>
      <img src="https://github.com/user-attachments/assets/8cc9972a-d11f-4222-9a3a-02fa7773fc60" alt="성공" />
    </td>
  </tr>
</table>

[영상 크기 제한으로인해 속도를 높여 녹화되었습니다.]

<br /><br />

### 폴더 구조

```
📦 src
┣ 📂 assets
┣ 📂 components
┃ ┣  📂 common
┃ ┣  ...
┃ ┗  📂 game
┃    ┣  📜 index.ts
┃    ┗  📜 GameBoard.tsx
┣ 📂 constants
┃ ┣  📜 game.ts
┃ ┗  📜 metric.ts
┣ 📂 hooks
┣ 📂 pages
┣ 📂 styles
┗ 📂 types
```
* 프로젝트 요구사항에 따르면 게임 도메인 하나만 존재하기 때문에 feature 단위로 나누지 않았습니다.
* 다만, 어느 정도의 확장성을 고려하여 많은 파일이 속한 폴더는 `사용 목적`을 기준으로 폴더를 한번 더 나누었습니다.
* `/src/components/game` 같이 한번 더 폴더로 나눠진 경우 `index.ts` 파일을 두어 컴포넌트 import 시 경로의 길이를 짧게 만들어 주었습니다.

<br /><br />

### 고민한 내용

1. 기술 선정

* `패키지 매니저 - NPM`

  패키지 매니저로 npm, yarn, pnpm을 고려할 수 있었습니다. yarn과 pnpm은 npm에서 발생할 수 있는 의존성 문제를 해결하였고, 캐싱의 적극적인 사용으로 npm 보다 빠른 속도를 보여줍니다.

  하지만 짧은 기간이 주어졌으므로, 기능 구현과 성능에 집중하기 위해 보다 안전하고 호환성이 좋은 npm을 선정하였습니다.

* `스타일링 - Tailwind CSS`

  Next.js는 정적 렌더링 기반 최적화의 장점을 가지는 프레임워크이기 때문에 컴파일 시점에 정적으로 스타일을 분석하는 Tailwind와 조합이 좋고, Tailwind 사용에 익숙하기 때문에 빠른 스타일링에 유리하다고 판단하여 선정하였습니다.

<br />

2. 사과의 시작 위치
   
사용자의 입장에서 게임을 시작하면 화면 중앙에 사과가 오는 것이 자연스럽다고 판단했습니다. 따라서 어떤 기기를 사용하더라도 초기에 화면의 중앙에 사과가 위치할 수 있도록 구현했습니다.

사과는 낙하 애니메이션을 가지기 때문에 `position`을 `fixed`가 아닌 `absolute`로 지정하였고, 뷰포트의 높이를 알 수 있는 `useInnerSize`를 사용해 사과의 초기 높이를 지정해 주었습니다.

<br />

3. 사과의 낙하 애니메이션 구현
   
해당 기능을 구현하며 `타이머 함수`와 `requestAnimationFrame`을 고려했습니다.
   타이머 함수를 사용하는 경우 구현은 가능하지만, 프레임에 맞춰 실행되지 않기 때문에 만약 프레임이 생성되는 시점과 타이머 함수가 실행되는 시점이 겹쳐버리면 해당 프레임이 깎여버리는 문제가 발생할 수 있고, 이것은 비교적 부자연스러운 UI를 제공할 수 있습니다.
   
하지만 `requestAnimationFrame`은 프레임 단위로 콜백 함수를 실행하기 때문에 사용자의 환경에 상관없이 최적화된 애니메이션을 제공할 수 있습니다.

따라서 `requestAnimationFrame`을 사용하여 프레임 단위로 사과의 위치를 조절하여 사과의 낙하 애니메이션을 구현하였습니다.

<br />

4. "꾹 눌러주세요" 클릭 시 스크롤이 잠시 올라갔다가 내려가는 문제 해결

이미지처럼 "꾹 눌러주세요" 버튼 클릭 시 스크롤이 내려가고, 이후에 정상적으로 사과가 낙하하는 문제를 발견했습니다.
   
<table align="center">
  <tr>
    <th>문제 상황</th>
  </tr>
  <tr>
    <td>
      <img width="200" src="https://github.com/user-attachments/assets/7d282a4c-b8fe-4e71-8503-335a2b69b77a" alt="스크롤이 잠시 올라갔다가 내려가는 문제" />
    </td>
  </tr>
</table>

우선 버튼 클릭 시 발생하는 문제이기 때문에 이벤트 핸들러부터 문제를 탐색했습니다. 하지만 이벤트 핸들러에는 아래와 같이 스크롤을 직접적으로 변경시키는 코드는 없었습니다.
  
```typescript
  const handleDropStart = () => {
    if (gameState !== "WAITING" || isPressingRef.current) return;

    isPressingRef.current = true;
    gameActions.setPlaying();
    previousTimeStamp.current = null;
    rafIdRef.current = window.requestAnimationFrame(step);

    window.addEventListener("pointerup", handleDropStop);
    window.addEventListener("pointercancel", handleDropStop);
    window.addEventListener("keyup", handleKeyUp);
  };
```

따라서 `step` 콜백 함수에서 문제를 탐색했습니다.

```typescript
const step = (timestamp: number) => {
  ...
  let nextDrop = 0;
  setDropDistance((prev) => {
    nextDrop = Math.min(
      prev + elapsed * GAME_SETTING.DROP_SPEED,
      GAME_SETTING.MAX_DROP_DISTANCE
    );

    const appleHeight = initialHeight + nextDrop;
    syncScrollToApple(appleHeight);

    return nextDrop;
  });
	...
};
```

`step` 함수에서 `dropDistance`는 사과의 낙하 높이를 관리하는 `state`인데, `setDropDistance` setter 내부에서 부수효과(스크롤)를 실행하고 있었습니다. 따라서 리액트의 순수성을 보장하지 못해 위와 같은 문제가 발생한 것이라 판단했습니다.

결론적으로 스크롤시키는 함수 `syncScrollToApple`의 실행을 해당 콜백에서 분리하고, `useLayoutEffect`를 사용해 사과의 `dropDistance`가 변경될 때마다 스크롤이 진행되게 수정하여 문제를 해결했습니다.

<br /><br />

### lighthouse 성능 개선

1. 이미지 최적화

<table align="center">
  <tr>
    <th>개선 전</th>
    <th>개선 후</th>
  </tr>
  <tr>
    <td>
      <img src="https://github.com/user-attachments/assets/52e38d88-49b4-49f8-9fb4-dcd17f5f87a3" alt="개선 전" />
    </td>
    <td>
      <img src="https://github.com/user-attachments/assets/095bce2e-df80-4af3-bbc6-3b79063853b2" alt="개선 후" />
    </td>
  </tr>
</table>

lighthouse 성능 측정 결과 LCP 성능에 개선이 필요했습니다. 원인 분석 결과 배경 이미지에서 많은 부하가 있었고, 이미지로 모두 `<img>` 태그를 사용하고 있었습니다. 따라서 기본적으로 최적화를 제공하는 Next.js의 `<Image>` 컴포넌트로 전환하였습니다.

추가적으로 서비스 접근 즉시 보이는 배경과 사과 이미지는 `priority` 속성을 추가해 preload 해주었습니다.

그 결과 LCP 성능을 `1.6s` -> `0.4s`로 최적화할 수 있었습니다.

<br />

2. 접근성 및 SEO 최적화

메타데이터 설정 및 favicon 설정을 통해 접근성을 향상시켰습니다.

또한, "꾹 눌러주세요~" 버튼에 `ARIA`를 추가하여 게임에 대한 설명을 작성해 스크린리더 사용자의 접근성을 향상시켰습니다.

<br /><br />

### 리팩토링

1. 애니메이션 `top` -> `transform` 변경

기존에는 사과의 낙하 애니메이션을 `top`을 조절하여 구현했습니다. 하지만 이렇게 수많은 변화가 일어나는 애니메이션에서 `top`을 조절하면 리플로우가 많이 발생하여 성능에 문제가 될 수 있었습니다.

따라서 동일한 결과를 보여주지만 composite 단계부터 실행되는 `transform`을 사용해서 애니메이션을 구현하는 방식으로 변경했습니다.
