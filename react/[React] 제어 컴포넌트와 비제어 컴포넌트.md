# 제어 컴포넌트와 비제어 컴포넌트

## HTML 폼 엘리먼트

HTML 폼 엘리먼트들(`<input>`, `<textarea>`, `<select>` 등)은 다른 엘리먼트들과 달리 폼 엘리먼트 자체가 내부 상태(`state`)를 가지며, 일반적으로 사용자의 입력을 기반으로 자신의 state를 관리하고 업데이트한다.

이러한 특성에 의해 리액트에서 폼 엘리먼트를 다루는 방법에는 크게 두 가지가 있다.

**제어 컴포넌트**는 React에서 폼에 발생하는 사용자 입력값을 제어하는 것이고,

**비제어 컴포넌트**는 폼 엘리먼트가 가지는 상태를 그대로 사용하는 것이다.

## 제어 컴포넌트

제어 컴포넌트에서는 폼 엘리먼트를 리액트가 제어하는 방식으로 동작한다.

아래와 같이 `<input>` 태그의 `value` 값을 `useState`로 관리하는 것이 제어 컴포넌트의 대표적인 예시이다.

```jsx
import { useState } from "react";

function App() {
  const [inputValue, setInputValue] = useState("기본 입력값");
  const [List, setList] = useState([]);
  const handleChange = (e) => {
    setInputValue(e.target.value);
    console.log(e.target.value);
  };
  const handleSubmit = (e) => {
    e.preventDefault();
    const newOne = inputValue;
    setList((prev) => [...prev, newOne]);
  };

  return (
    <div className="App">
      <form onSubmit={handleSubmit}>
        <input value={inputValue} onChange={handleChange}></input>
        <input type="submit"></input>
      </form>
      <div>제어 컴포넌트 input 값: {inputValue}</div>
      <br />
      <div>
        {List.map((a, i) => (
          <div key={i}>{a}</div>
        ))}
      </div>
    </div>
  );
}

export default App;
```

1. 사용자가 `input` 엘리먼트에 값을 입력
2. input 엘리먼트에 새로운 값이 입력될 때마다 `onChange`가 발생하여 `setInputValue`에 의해 `inputValue` state 업데이트
3. input 엘리먼트의 `value`에 업데이트된 state 할당

![Jun-29-2022_18-28-29](https://user-images.githubusercontent.com/87363422/182178313-d1d7a5ff-8f19-4dad-9d6a-4ef41ed1bb7f.gif)

이때 특징은 사용자가 입력한 값과 저장되는 값이 실시간으로 동기화된다는 점

## 비제어 컴포넌트

비제어 컴포넌트에서는 useState 등 컴포넌트 단위의 상태(state)를 활용하지 않고 태그의 참조(ref)를 활용해 input 엘리먼트의 상태에 접근한다. 이 때 컴포넌트의 상태가 변해야 재렌더링을 수행하는 리액트는 이를 인식하지 못해 데이터와 UI의 동기가 이루어지지 않게 된다.

```jsx
import { useRef, useState } from "react";

function App() {
  const inputRef = useRef();
  const [List, setList] = useState([]);
  const handleSubmit = (e) => {
    e.preventDefault();
    const newOne = inputRef.current.value;
    setList((prev) => [...prev, newOne]);
    inputRef.current.value = "";
  };

  return (
    <div className="App">
      <form onSubmit={handleSubmit}>
        <input type="text" ref={inputRef} defaultValue="기본 입력값"></input>
        <input type="submit"></input>
      </form>
      <div>비제어 컴포넌트 input 값:{inputRef.current?.value}</div>
      <br />
      <div>
        {List.map((a, i) => (
          <div key={i}>{a}</div>
        ))}
      </div>
    </div>
  );
}

export default App;
```

![Jun-29-2022_18-30-15](https://user-images.githubusercontent.com/87363422/182178328-2938f412-6a4c-4c29-898f-ee20a9febedd.gif)

## 차이점

제어 컴포넌트와 비제어 컴포넌트의 가장 큰 차이점은 동기화이다.

제어 컴포넌트는 사용자가 입력할 때마다 state가 동기화된다. 따라서 input의 유효성 검사를 할 때 제어 컴포넌트가 용이하다. 항상 최신 값을 유지하는 특성에 따라 현재 input의 value가 유효한지 안 한 지에 대한 검사를 실시간으로 할 수 있다.

그러다 보니 사용자 입력 액션이 있을 때마다 리렌더링이 발생한다. 그리고 불필요한 단어를 입력했을 때도 값이 입력되고 state에 반영되는데, 이는 만약 실시간으로 api 요청을 하는 상황이었다면 자원의 낭비로 이어질 수 있다.

반면, 비제어 컴포넌트에서는 버튼을 누르는 최종적인 상황에서만 input의 value에 접근하므로 리렌더링이 계속해서 발생하진 않는다. 하지만 그만큼 비제어 컴포넌트는 사용자 입력의 가장 최신 값 에 접근하기 힘들다는 특성이 있다.
