# OSS Week 12 — React Hooks Playground (Student Starter)
이 프로젝트는 react훅인 useState및 useRef를 활용하여 start, stop reset기능을 포함한 타이머를 다루는 프로젝트이다. 

## 실행 방법
Node.js 20.19+ 또는 22.12+ 권장. 프로젝트 폴더에서 실행:

```bash
npm install
npm run dev
```

브라우저에 터미널의 Local URL을 열고 LAB 탭을 이동하세요. 제출 전 `npm run build`도 실행하세요.

## 수행 방법
1. `src/components/` 아래의 LAB 1~4 파일에 있는 `TODO`를 직접 완성합니다.
2. `src/App.jsx`, `src/App.css`는 공통 화면이므로 가급적 수정하지 않습니다.
3. 각 LAB의 동작 화면과 콘솔 결과를 기록합니다.
4. 한 LAB에서 다른 LAB으로 탭을 전환하면 기존 컴포넌트가 언마운트됩니다. Cleanup 결과를 관찰합니다.

# Source Code Exploration: 주요 파일 구조, useEffect 및 useRef 분석, 주요 코드 2개 설명
1. 주요 파일 구조
- Lab1_EffectTimer.jsx는 useEffect()에 dependency array에 빈 배열을 줘서 첫 마운트 시에만 실행되게 하였고, setCount()를 통해 계속해서 숫자를 증가시킨다.
- Lab2_TimerControl.jsx는 dependency array에 running이라는 state를 줘서 start stop reset버튼을 누르면 각 상태가 변화하여 useState를 통해 타이머를 중단 시키거나 중단시키고 0으로 만드는 구성을 한 파일이다.
- Lab3는 useRef를 통해서 <input>에 useRef로 구성된 변수를 넣어줌으로써 input을 참조할 수 있게된다. 따라서 useRef 로 지정한 객체에 .current.focus()를 줌으로 특정 버튼을 누르면 input을 focus하는 효과를 낸다.
- Lab4는 start() reset() stop()함수에 setInterval clearInterval를 직접 호출 하므로싸 타이머를 관리한다. setInterval의 반환아이디를 useRef로 지정한 변수에 저장해둔다. 그리고 각 함수에 setRunning()를 호출해서 true false를 조정하므로써 타이머의 시작과 정지를 다룬다. 
- useEffect()는 컴포넌트의 렌더링과 독립적으로 작업을 수행하고, dependency array를 통해 재실행 여부를 결정함. 또한 그 배열의 state가 변경될 때마다 rerendering을 해준다. 그러나 useRef()는 값이 변경되어도 rerendering은 진행되지 않고 값만 저장됨. 타이머에서는 ID저장 용도로 사용된다. 

- 주요 코드 2개
1. useEffect(() => {
  if (running === false) return;

  const id = setInterval(() => {
    setCount((count) => count + 1);
  }, 1000);

  return () => clearInterval(id);
}, [running]);

const reset = () => {
  setRunning(false);
  setCount(0);
};
이 코드는 running의 상태를 dependancy array에 두어서 true 인지 false인지에 따라 타이머의 정지 시작여부를 결정한다. reset()버튼을 누르게 되면, setRunning(false)로 설정해서 useEffect가 재 실행되었을때 return 하여 타이머를 증지함. 
2. const inputRef=useRef(null);

  const focusInput = () => {
    // TODO 2: ref.current를 이용해 input에 focus() 호출
    inputRef.current.focus();
  };

  const clearInput = () => {
    // TODO 3: input의 값을 지우고 다시 Focus
    inputRef.current.value='';
    inputRef.current.focus();
  };
  이 코드는 useRef를 통해서 inputRef 객체를 만들고, input태그에 inputRef를 ref값으로 넣어서, inputRef.current로 input태그를 참조할 수 있게 하였다. 그리하여서, clear버튼을 누르게 되면 inputRef.current.value값에 '' 를 넣어서 값을 비운다. 또한 focus()를 호출함으로써 input태그로 커서를 focus한다.
## Lab Comparison: STEP 3의 비교 분석 질문 4개에 대한 답변
1. useEffect의 Dependency Array가 필요한 이유는?
- 그 이유는 useState가 Dependency Array가 없으면 렌더링이 진행된후 다시 useState를 계속해서 실행하게 되어 무한 렌더링이 일어나게 된다. 그러나 dependency array에 state를 두고 그 state가 변경되면 useState가 실행되게 해서, 마운트했을때 한번만 실행되게 하거나 상태가 변경되었을 때만 실행되게하여 무한 루프가 일어나지 않게 한다. 
2. Cleanup을 사용하지 않으면 어떤 문제가 발생할 수 있는가?
- 사용하지 않으면, useState가 종료되지 않아, 메모리 누수가 발생할 수 있고, 작업이 계속 중첩되어서 비정상적인 처리가 일어날 수 있다. 
3. useState와 useRef의 가장 중요한 차이는?
- useState는 state가 변경되면 바로 렌더링이 진행되는 반면, useRef는 렌더링이 바로 진행되지 않고, 변경된 값만 저장된다. 
4. LAB 2와 LAB 4의 Timer 관리 방식은 어떻게 다른가?
- LAB2는 running의 state값을 true나 false로 조절해주면서 상태가 바뀌면 useEffect()가 실행되어서 count값을 지속적으로 증가시키고 계속 렌더링이 되는 방식이지만, LAB4의 방식은 start()와 stop()함수에서 직접 setIntervel 과 clearInterval를 실행시키면서 timerRef.current에 타이머 아이디만 저장 시키고, 각자 함수로 타이머의 시작과 끝을 관리한다. 그리고 LAB2에서는 useEffect가 직접 상태변화를 감지하여 setInterval clearInterval을 모두 수행했지만 4에서는 clearInterval만 수행함. 
# Customizing: 수정 내용 및 파일명
App.jsx에서 프로젝트 제목 수정
App.css에서 버튼 radius값 조절, 버튼색, count 글자 색 수정.

# Deployment: 실제 접근 가능한 Vercel URL
https://assign12-c01-22300619-pfv7.vercel.app/
# Weekly Review:
- 새롭게 배운 내용 3가지
1. useEffect는 dependency array에 있는 state값의 변화를 감지하여 실행함.
2. return()=>{}과 같은 cleanup함수는 이전의 이벤트를 저장해뒀다가 useEffect가 다시 실행되면 우선적으로 값을 반환함. 또한 컴포넌트가 사라져도 수행해서 이전의 이벤트를 정리해주는 역할을한다.
3. useRef()는 타이머에서 id를 저장하는 용도로 사용되고, state가 변화되어도 rerendering을 하지않고 단지 값만 저장된다. 
- 어려웠던 점과 해결 방법
cleanup함수를 구현할때 ()=> 와 같이 arrow함수를 사용하지 않아서 숫자가 자꾸 바뀌지 않았던 이슈가 있었으나 강의자료를 다시 꼼꼼히 분석한 결과, 그 원인을 찾아내어 수정함.
- AI 활용 내용 및 느낀 점
UseRef와 useEffect의 차이를 잘 구분하지 못하겠어서 AI를 통해 각각의 정의를 다시 분석하고, 두 hook의 용도를 코드 예시를 통해 비교하여서 차이를 명확히 이해하게 되었다.