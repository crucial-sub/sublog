# 리액트 성능 최적화(sCU, PureComponent, React.memo)

리액트에서 컴포넌트는 아래 4가지 경우에 `리렌더링`하게 된다.

1. `props`가 바뀔 때
2. `state`가 바뀔 때
3. `부모 컴포넌트`가 리렌더링될 때
4. `this.forceUpdate`로 강제로 렌더링을 트리거할 때

리렌더링이 발생하면 리액트는 새로 반환된 엘리먼트를 이전에 렌더링된 엘리먼트와 비교해서 실제 DOM 업데이트가 필요한지 여부를 결정한다.

리액트가 가상 DOM을 통해 변경된 DOM 노드만 효율적으로 업데이트 한다 하더라도 리렌더링에는 다소의 시간이 소요된다.

대부분의 경우에는 문제가 되지 않지만 속도 저하가 눈에 띌 경우 `불필요한 리렌더링`을 막아주어 성능을 향상 시킬 수 있는 방법이 존재한다.

## 1. shouldComponentUpdate(sCU)

![lifecycle](https://user-images.githubusercontent.com/87363422/174096814-8971229e-e228-483f-9e91-f2a03b834a47.png)

`shouldComponentUpdate`는 클래스형 컴포넌트의 라이프사이클에서 컴포넌트의 리렌더링 여부를 결정해주는 메서드이다.

위 라이프사이클 그림에서 볼 수 있듯이 props나 state가 바뀌어 render 메서드가 호출되기 직전에 sCU 메서드가 호출되며,
sCU가 true를 리턴할 경우 render()가 정상적으로 호출되고 false를 리턴할 경우 render()가 호출되지 않는다.

sCU는 기본적으로는 true를 리턴한다.

```jsx
shouldComponentUpdate(nextProps, nextState) {
    if(nextProps.id !== this.props.id) return true; // id가 변했으면 리렌더링
    if(nextState.text !== this.state.text) return true; // text가 변해도 리렌더링

    return false;
}
```

위의 코드처럼 아무 props나 state가 변했다고 무조건 리렌더링 시켜주는 것이 아니라 id라는 props 혹은 text라는 state가 변했을 때만 리렌더링 하도록 할 수 있다.

## 2. PureComponent

리액트에서 클래스형 컴포넌트에는 크게 `React.Component`와 `React.PureComponent` 두 가지가 존재한다.

둘의 차이점은 sCU의 재정의 여부이다.

PureComponent는 기본적으로 shouldComponentUpdate를 통해 props와 state에 대해 얕은 비교(shallow compare)를 수행하여 값이 다를 경우에만 리렌더링하도록 정의 되어 있는 Component이다.

즉, 불필요한 리렌더링을 막아야 할 때 직접 shouldComponentUpdate를 정의해줄 필요가 있는 것이 아니라면 pureComponent를 사용해 손쉽게 성능 최적화가 가능해진다.

PureComponent를 사용할 때 주의할 점은 얕은 비교를 수행한다는 점이다.

얕은 비교는 참조값을 비교한다는 점!

state나 props가 참조타입이거나 복잡한 데이터 구조를 가지고 있을 경우 제대로 된 비교를 수행하지 못할 수 도 있다.
예를 들어 부모 컴포넌트에서 항상 화살표 함수로 선언하고 이를 props로 전달해 주는 경우 같은 내용이더라도 참조 값이 매번 바뀌어 의도와 다르게 자식 pureComponent에서는 props가 바뀌었다고 인식을 하여 리렌더링을 막지 못한다.(화살표 함수가 아닌 클래스 메서드는 공유 메서드로 상속받은 컴포넌트에서 참조 가능)

또한 부모 컴포넌트에서 속성을 직접 변경한 객체를 props로 전달할 경우, 객체의 참조 값은 그대로이기 때문에 자식 PureComponent는 리렌더링 되지 않는다.

```jsx
class ListOfWords extends React.PureComponent {
  render() {
    return <div>{this.props.words.join(',')}</div>;
  }
}

class WordAdder extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      words: ['marklar']
    };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    const words = this.state.words;
		// state를 직접 변경
    words.push('marklar');
    this.setState({words: words});
  }));
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick} />
        <ListOfWords words={this.state.words} />
      </div>
    );
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<WordAdder />);
```

부모 컴포넌트 WordAdder에서 버튼을 클릭할 경우 words 배열에 단어를 추가하고 이를 자식 컴포넌트 ListOfWords에 props로 전달하여 합쳐서 렌더링하는 코드이다.

이 때 handleClick()에서 words 배열을 직접 변경하여 props로 전달하므로

배열의 실제 단어가 변경되었다 하더라도 this.props.words의 이전 값과 새로운 값을 얕은 비교만 하는 ListOfWords 입장에서는 props가 변경되었다고 느끼지 못하여 리렌더링이 일어나지 않는다.

이 문제를 피하는 가장 간단한 방법은 props와 state로 사용중인 값의 직접적인 변경을 피하는 것이다.

```jsx
class ListOfWords extends React.PureComponent {
  render() {
    return <div>{this.props.words.join(",")}</div>;
  }
}

class WordAdder extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      words: ["marklar"],
    };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    const words = this.state.words;
    // words.push('marklar');
    // this.setState({words: words});
    this.setState((state) => ({
      words: [...state.words, "marklar"],
    }));
  }

  render() {
    return (
      <div>
        <button onClick={this.handleClick} />
        <ListOfWords words={this.state.words} />
      </div>
    );
  }
}

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<WordAdder />);
```

## 3. React.memo

리액트 함수형 컴포넌트에서는 라이프사이클 메서드를 사용할 수 없기에 React.memo라는 고차 컴포넌트(HOC)를 사용하여 리렌더링 최적화를 구현할 수 있다.

```jsx
const MyComponent = React.memo(function MyComponent(props) {
  /* props를 사용하여 렌더링 */
});
```

함수 기반 컴포넌트에 React.memo를 감싸는 방식으로 React.memo를 사용할 수 있다.

React.memo를 통해 생성된 컴포넌트는 전달된 props 값의 변화에 따라 렌더링 여부를 결정한다.

부모 컴포넌트로부터 props를 전달받아 현재 갖고 있는 props와 얕은 수준의 비교를 수행하며, 수행 결과 다른 값이라면 재 렌더링 하고, 그렇지 않다면 재 렌더링 하지 않는다.

React.memo는 PureComponent와 달리 state 변화에는 영향받지 않는다. state 변화는 useState 와 같은 훅을 이용하여 관리한다.

```jsx
function MyComponent(props) {
  /* props를 사용하여 렌더링 */
}
function areEqual(prevProps, nextProps) {
  return prevProps.id === nextProps.id;
}
export default React.memo(MyComponent, areEqual);
```

sCU를 구현해야하는 경우는 위와 같이 React.memo의 두 번째 인자로 렌더링 여부를 결정하는 함수를 전달하면 된다.

위 경우 areEqual에서 id라는 props에 변화가 있는지 확인하고, 변화가 있다면 areEqual은 false를 리턴하고 컴포넌트는 리렌더링 된다.

이때 sCU와 반대로 areEqual 함수는 props의 변화가 없으면 true를 반환한다는 점에 주의하자.
