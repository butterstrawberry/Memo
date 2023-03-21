# 리액트를 다루는 기술

## 목차

- [리액트 시작](#리액트-시작)
- [JSX](#jsx)
- [컴포넌트](#컴포넌트)
- [이벤트 핸들링](#이벤트-핸들링)
- [ref](#ref)
- [컴포넌트 반복](#컴포넌트-반복)
- [컴포넌트의 라이프사이클 메서드](#컴포넌트의-라이프사이클-메서드)
- [Hooks](#hooks)
- [컴포넌트 스타일링](#컴포넌트-스타일링)
- [컴포넌트 성능 최적화](#컴포넌트-성능-최적화)
- [리액트 라우터로 SPA 개발](#리액트-라우터로-spa-개발)
- [Context API](#context-api)
- [리덕스](#리덕스)
- [리액트에 리덕스 적용](#리액트에-리덕스-적용)
- [리덕스 비동기 작업 관리](#리덕스-비동기-작업-관리)

## 리액트 시작

리액트는 자바스크립트 라이브러리로 사용자 인터페이스를 만드는 데 사용하고 오직 V(View)만 신경 쓴다.  
리액트 프로젝트에서 특정 부분이 어떻게 생길지 정하는 선언체를 컴포넌트(component)라고 한다.  
리액트가 언제나 제공할 수 있는 것은 바로 업데이트 처리 간결성이다.

### 초기 렌더링

사용자 화면에 뷰를 보여 주는 것을 렌더링이라고 한다.  
어떤 UI 관련 프레임워크, 라이브러리를 사용하든지 간에 맨 처음 어떻게 보일지를 정하는 초기 렌더링이 필요하다.  
리액트에서는 이를 다루는 render 함수가 있다. 이 함수는 컴포넌트가 어떻게 생겼는지 정의하는 역할을 한다. 뷰가 어떻게 생겼고 어떻게 작동하는지에 대한 정보를 지닌 객체를 반환한다.  
최상위 컴포넌트의 렌더링 작업이 끝나면 지니고 있는 정보들을 사용하여 HTML 마크업을 만들고, 이를 우리가 정하는 실제 페이지의 DOM 요소 안에 주입한다.

### 조화 과정

리액트에서 뷰를 업데이트할 때는 "업데이트 과정을 거친다"라고 하기보다는 "조화 과정(reconciliation)을 거친다"라고 하는 것이 더 정확한 표현이다. 컴포넌트에서 데이터에 변화가 있을 때 우리가 보기에는 변화에 따라 뷰가 변형되는 것처럼 보이지만, 사실은 새로운 요소로 갈아 끼우기 때문이다. 이 작업 또한 render 함수가 맡아서 한다.  
컴포넌트는 데이터를 업데이트했을 때 단순히 업데이트한 값을 수정하는 것이 아니라, 새로운 데이터를 가지고 render 함수를 또 다시 호출한다.  
render 함수가 반환하는 결과를 곧바로 DOM에 반영하지 않고, 이전에 render 함수가 만들었던 컴포넌트 정보와 현재 render 함수가 만든 컴포넌트 정보를 비교한다.  
자바스크립트를 사용하여 두 가지 뷰를 최소한의 연산으로 비교한 후, 둘의 차이를 알아내 최소한의 연산으로 DOM 트리를 업데이트한다.

### DOM은 과연 느릴까?

DOM 자체를 읽고 쓸 때의 성능은 자바스크립트 객체를 처리할 때의 성능과 비교하여 다르지 않다.  
단, 웹 브라우저 단에서 DOM에 변화가 일어나면 웹 브라우저가 CSS를 다시 연산하고, 레이아웃을 구성하고, 페이지를 리페인트하면서 시간이 낭비된다.  
DOM을 조작할 때마다 브라우저 엔진이 웹 페이지를 새로 그리기 때문에 업데이트가 너무 잦으면 성능이 저하될 수 있다.

### Virtual DOM

Virtual DOM을 사용하면 실제 DOM에 접근하여 조작하는 대신, 이를 추상화한 자바스크립트 객체를 구성하여 사용한다.  
리액트에서 데이터가 변하여 웹 브라우저에 실제 DOM을 업데이트할 때는 세 가지 절차를 밟는다.

1. 데이터를 업데이트하면 전체 UI를 Virtual DOM에 리렌더링한다.
2. 이전 Virtual DOM에 있던 내용과 현재 내용을 비교한다.
3. 바뀐 부분만 실제 DOM에 적용한다.

## JSX

JSX는 자바스크립트 확장 문법이며 XML과 매우 비슷하게 생겼다. 이런 형식으로 작성한 코드는 브라우저에서 실행되기 전에 코드가 번들링되는 과정에서 바벨을 사용하여 일반 자바스크립트 형태의 코드로 변환된다.  
JSX는 편리한 문법이지만, 올바르게 사용하려면 몇 가지 규칙을 준수해야 한다.

- 컴포넌트에 여러 요소가 있다면 반드시 부모 요소 하나로 감싸야 한다.
- 자바스크립트 표현식을 작성하려면 JSX 내부에서 코드를 { }로 감싸면 된다.
- JSX 내부의 자바스크립트 표현식에서 if 문을 사용할 수 없어서 JSX 밖에서 if 문을 사용하여 사전에 값을 설정하거나, { } 안에 조건부 연산자를 사용하면 된다.
- && 연산자를 사용해서 조건부 렌더링을 할 수 있다. 단, falsy한 값인 0은 예외적으로 화면에 나타난다.
- 리액트 컴포넌트에서는 함수에서 undefined만 반환하여 렌더링하는 상황을 만들면 안된다. 반면 JSX 내부에서 undefined를 렌더링하는 것은 괜찮다.
- DOM 요소에 스타일을 적용할 때는 객체 형태로 넣어 주어야 하고 카멜 표기법으로 작성해야 한다.
- JSX에서는 class가 아닌 className으로 설정해 주어야 한다.
- JSX에서는 태그를 닫지 않으면 오류가 발생한다.
- JSX 내부에서 주석을 작성할 때는 {/\* ⋯ \*/}와 같은 형식으로 작성한다.

## 컴포넌트

리액트를 사용하여 애플리케이션의 인터페이스를 설계할 때 사용자가 볼 수 있는 요소는 여러 가지 컴포넌트로 구성되어 있다.  
컴포넌트의 기능은 단순한 템플릿 이상이다. 데이터가 주어졌을 때 이에 맞춰 UI를 만들어 주는 것은 물론이고, 라이프사이클 API를 이용하여 컴포넌트가 화면에서 나타날 때, 사라질 때, 변화가 일어날 때 주어진 작업들을 처리할 수 있으며, 임의 메서드를 만들어 특별한 기능을 붙여 줄 수 있다.

### 클래스형 컴포넌트

클래스형 컴포넌트는 state 기능 및 라이프사이클 기능을 사용할 수 있고 임의 메서드를 정의할 수 있다.  
클래스형 컴포넌트에서는 반드시 render 함수가 있어야 하고, 그 안에서 보여 주어야 할 JSX를 반환해야 한다.

```javascript
import { Component } from "react";

class App extends Component {
  render() {
    const name = "react";
    return <div className="react">{name}</div>;
  }
}
```

### 모듈 내보내기<sup>export</sup>

```javascript
export default MyComponent;
```

### 모듈 불러오기<sup>import</sup>

```javascript
import MyComponent from "./MyComponent";
```

### props

props는 properties를 줄인 표현으로 컴포넌트 속성을 설정할 때 사용하는 요소이다.  
props 값은 해당 컴포넌트를 불러와 사용하는 부모 컴포넌트에서 설정할 수 있다.  
props 값은 컴포넌트 함수의 파라미터로 받아 와서 사용할 수 있다.  
컴포넌트 자신은 해당 props를 읽기 전용으로만 사용할 수 있다.

```javascript
const MyComponent = (props) => {
  return <div>{props.name}</div>;
};
```

defaultProps를 설정하면 props 값을 따로 지정하지 않았을 때 보여 줄 기본값을 설정할 수 있다.  
children은 컴포넌트 태그 사이의 내용을 보여 주는 props이다.  
비구조화 할당 문법을 통해 props를 사용하면 작업을 더 편하게 할 수 있다.  
propTypes를 사용하면 컴포넌트의 필수 props를 지정(isRequired)하거나 props의 타입을 지정할 수 있다.

```javascript
import PropTypes from "prop-types";

// destructuring assignment
const MyComponent = ({ name, number, children }) => {
  return (
    <div>{name} {number} {children}</div>
  );
};

MyComponent.defaultProps = {
  name: 'default name'
};

MyComponent.propTypes = {
  name: PropTypes.string
  number: PropTypes.number.isRequired
};
```

```javascript
const App = () => {
  return (
    <MyComponent name="React" number={1}>
      react
    </MyComponent>
  );
};
```

클래스형 컴포넌트에서 props를 사용할 때는 render 함수에서 this.props를 조회하면 된다.

```javascript
class MyComponent extends Component {
  render() {
    const { name, number, children } = this.props;
    return (
      <div>
        {name} {number} {children}
      </div>
    );
  }
}
```

### state

리액트에서 state는 컴포넌트 내부에서 바뀔 수 있는 값을 의미한다.  
두 가지 종류의 state가 있는데 하나는 클래스형 컴포넌트가 지니고 있는 state이고, 다른 하나는 함수 컴포넌트에서 useState라는 함수를 통해 사용하는 state이다.

#### 클래스형 컴포넌트의 state

클래스형 컴포넌트에 state를 설정할 때는 constructor 메서드를 작성하여 설정한다.  
클래스형 컴포넌트에서 constructor를 작성할 때는 반드시 super(props)를 호출해 주어야 한다.  
컴포넌트의 state는 객체 형식이어야 한다.  
render 함수에서 현재 state를 조회할 때는 this.state를 조회하면 된다.  
this.setState를 사용하여 state에 새로운 값을 넣을 수 있다.  
state 객체 안에는 여러 값이 있을 수 있다.

```javascript
(...)
constructor(props) {
  super(props);
  this.state = {
    number: 0,
    fixedNumber: 0
  };
}

render() {
  const { number, fixedNumber } = this.state;
  return (
    <div>
      <h1>{number}</h1>
      <h2>{fixedNumber}</h2>
      <button
        onClick={() => {
          this.setState({ number: number + 1 });
        }}
      >
        +1
      </button>
    </div>
  );
}
(...)
```

constructor 메서드를 선언하지 않고도 state 초깃값을 설정할 수 있다.

```javascript
(...)
state = {
  number: 0,
  fixedNumber: 0
};

render() {
  const { number, fixedNumber } = this.state;
  return (...);
}
(...)
```

this.setState를 사용하여 state 값을 업데이트할 때는 상태가 비동기적으로 업데이트된다. 그래서 this.setState를 두 번 호출해도 state 값이 바로 바뀌지 않는다.  
이에 대한 해결책은 this.setState를 사용할 때 객체 대신에 함수를 인자로 넣어 주는 것이다.  
함수의 파라미터에 prevState는 기존 상태이고, props는 현재 지니고 있는 props를 가리킨다. 만약 업데이트하는 과정에서 props가 필요하지 않다면 생략해도 된다.

```javascript
(...)
<button
  onClick={() => {
    this.setState((prevState, props) => {
      return {
        number: prevState.number + 1
      }
    });
    this.setState(prevState => ({
      number: prevState.number + 1
    }));
  }}
>
  +1
</button>
(...)
```

setState를 사용하여 값을 업데이트하고 난 다음에 특정 작업을 하고 싶을 때는 setState의 두 번째 파라미터로 콜백 함수를 등록하여 작업을 처리할 수 있다.

```javascript
(...)
<button
  onClick={() => {
    this.setState(
      {
        number: number + 1
      },
      () => {
        console.log('call setState');
        console.log(this.state);
      }
    );
  }}
>
  +1
</button>
(...)
```

#### 함수 컴포넌트에서 useState 사용하기

useState 함수의 인자에는 상태의 초깃값을 넣어 준다. 반드시 초깃값은 객체가 아니어도 상관없다.  
함수를 호출하면 배열이 반환되는데 배열의 첫 번째 원소는 현재 상태이고, 두 번째 원소는 상태를 바꾸어 주는 함수다. 배열 비구조화 할당을 통해 이름을 자유롭게 정해 줄 수 있다.  
useState는 한 컴포넌트에서 여러 번 사용해도 상관없다.

```javascript
import { useState } from "react";

const Say = () => {
  const [message, setMessage] = useState("");
  const onClickEnter = () => setMessage("Hi!");
  const onClickLeave = () => setMessage("Bye!");

  const [color, setColor] = useState("black");

  return (
    <div>
      <button onClick={onClickEnter}>enter</button>
      <button onClick={onClickLeave}>leave</button>
      <h1 style={{ color }}>{message}</h1>
      <button style={{ color: "red" }} onClick={() => setColor("red")}>
        red
      </button>
      <button style={{ color: "green" }} onClick={() => setColor("green")}>
        green
      </button>
      <button style={{ color: "blue" }} onClick={() => setColor("blue")}>
        blue
      </button>
    </div>
  );
};

export default Say;
```

## 이벤트 핸들링

사용자가 웹 브라우저에서 DOM 요소들과 상호 작용하는 것을 이벤트라고 한다.  
이벤트를 사용할 때 주의 사항이 있다.

- 이벤트 이름은 카멜 표기법으로 작성한다.
- 이벤트에 실행할 자바스크립트 코드를 전달하는 것이 아니라, 함수 형태의 값을 전달한다.
- DOM 요소에만 이벤트를 설정할 수 있다.

### onChange 이벤트 핸들링하기

콘솔에 기록되는 event 객체는 SyntheticEvent로 웹 브라우저의 네이티브 이벤트를 감싸는 객체이다.  
SyntheticEvent는 네이티브 이벤트와 달리 이벤트가 끝나고 나면 이벤트가 초기화되므로 정보를 참조할 수 없다.  
비동기적으로 이벤트 객체를 참조할 일이 있다면 event.persist() 함수를 호출해 주어야 한다.

```javascript
(...)
<input
  type="text"
  name="message"
  placeholder="default"
  onChange={
    (event) => {
      console.log(event);
    }
  }
/>
(...)
```

state에 input 값을 담고 버튼을 누를 때 message 값을 공백으로 설정하는 코드는 아래와 같다.  
state 초깃값을 설정하고, 이벤트 핸들링 함수 내부에서 this.setState 메서드를 호출하여 state를 업데이트한다.  
그 다음에는 input의 value 값을 state에 있는 값으로 설정한다.  
input 요소 코드 아래쪽에 button을 하나 만들고, 클릭 이벤트가 발생하면 현재 message 값을 메시지 박스로 뛰운 후 값을 공백으로 설정한다.

```javascript
(...)
state = {
  message: ''
};

render() {
  return (
    <div>
      <input
        (...)
        value={this.state.message}
        onChange={
          (event) => {
            this.setState({
              message: event.target.value
            });
          }
        }
      />
      <button onClick={
        () => {
          alert(this.state.message);
          this.setState({
            message: ''
          });
        }
      }>check</button>
    </div>
  );
}
(...)
```

### 임의 메서드 만들기

지금까지 이벤트를 처리할 때 랜더링을 하는 동시에 함수를 만들어서 전달해 주었다. 이 방법 대신 함수를 미리 준비하여 전달하는 방법도 있다. 성능상으로는 차이가 거의 없지만, 가독성은 훨씬 높다.

```javascript
(...)
constructor(props) {
  super(props);
  this.handleChange = this.handleChange.bind(this);
  this.handleClick = this.handleClick.bind(this);
}

handleChange(event) {
  this.setState({
    message: event.target.value
  });
}

handleClick() {
  alert(this.state.message);
  this.setState({
    message: ''
  });
}
(...)
```

함수가 호출될 때 this는 호출부에 따라 결정되므로, 클래스의 임의 메서드가 특정 HTML 요소의 이벤트로 등록되는 과정에서 메서드와 this의 관계가 끊어져 버린다. 이 때문에 임의 메서드가 이벤트로 등록되어도 this를 컴포넌트 자신으로 제대로 가리키기 위해서는 메서드를 this와 바인딩하는 작업이 필요하다.

### Property Initializer Syntax를 사용한 메서드 작성

메서드 바인딩은 생성자 메서드에서 하는 것이 정석이다. 하지만 이 작업을 불편하다고 느낄 수도 있다. 그래서 더 간단하게 하는 방법으로 바벨의 transform-class-properties 문법을 사용하여 화살표 함수 형태로 메서드를 정의하는 것이다.

```javascript
(...)
handleChange = (event) => {
  this.setState({
    message: e.target.value
  });
}

handleClick = () => {
  alert(this.state.message);
  this.setState({
    message: ''
  });
}
(...)
```

### input 여러 개 다루기

input이 여러 개일 때는 event 객체를 활용하는 것이다. event.target.name 값을 사용하여 state를 설정하면 쉽게 해결할 수 있다.  
객체 안에서 key를 \[ \]로 감싸면 그 안에 넣은 레퍼런스가 가리키는 실제 값이 key 값으로 사용된다.

```javascript
(...)
state = {
  username: '',
  message: ''
};

handleChange = (event) => {
  this.setState({
    [event.target.name]: event.target.value
  });
}

handleClick = () => {
  alert(this.state.username + ": " + this.state.message);
  this.setState({
    username: '',
    message: ''
  });
}

(...)
      <input
        type="text"
        name="username"
        placeholder="username"
        value={this.state.username}
        onChange={this.handleChange}
      />
      <input
        (...)
        name="message"
        value={this.state.message}
        onChange={this.handleChange}
      />
(...)
```

### onKeyPress 이벤트 핸들링

아래의 코드는 키를 눌렀을 때 발생하는 KeyPress 이벤트를 처리하는 방법이다.

```javascript
(...)
handleKeyPress = (event) => {
  if (event.key === "Enter") {
    this.handleClick();
  }
}
(...)
      <input
        type="text"
        type="message"
        (...)
        onKeyPress={this.handleKeyPress}
      />
(...)
```

### 함수 컴포넌트로 구현해 보기

```javascript
(...)
const [username, setUsername] = useState('');
const [message, setMessage] = useState('');
const onChangeUsername = event => setUsername(event.target.value);
const onChangeMessage = event => setMessage(event.target.value);
const onClick = () => {
  alert(username + ': ' + message);
  setUsername('');
  setMessage('');
};
const onKeyPress = event => {
  if (event.key === 'Enter') {
    onClick();
  }
};
(...)
```

아래의 코드는 useState를 통해 사용하는 상태에 문자열이 아닌 객체를 넣은 코드이다.

```javascript
(...)
const [form, setForm] = useState({
  username: '',
  message: ''
});
const { username, message } = form;
const onChange = event => {
  const nextForm = {
    ...form,
    [event.target.name]: event.target.value
  };
  setForm(nextForm);
};
(...)
```

## ref

ref는 HTML에서 id를 사용하여 DOM에 이름을 다는 것처럼 리액트 프로젝트 내부에서 DOM에 이름을 다는 방법이다.  
ref는 DOM을 꼭 직접적으로 건드려야 할 때 사용해야 한다. 아래는 state만으로 해결할 수 없어서 ref를 사용해야 하는 상황이다.

- 특정 input에 포커스 주기
- 스크롤 박스 조작하기
- Canvas 요소에 그림 그리기 등

### 콜백 함수를 통한 ref 설정

ref를 만드는 가장 기본적인 방법은 콜백 함수를 사용하는 것이다. ref를 달고자 하는 요소에 ref라는 콜백 함수를 props로 전달해 주면 된다. 이 콜백 함수는 ref 값을 파라미터로 전달받는다. 그리고 함수 내부에서 파라미터로 받은 ref를 컴포넌트의 멤버 변수로 설정해 준다.

```javascript
<input
  ref={(ref) => {
    this.input = ref;
  }}
/>
```

이렇게 하면 앞으로 this.input은 input 요소의 DOM을 가리킨다.  
ref의 이름은 원하는 것으로 자유롭게 지정할 수 있다. DOM 타입과 관계없이 this.jeong = ref처럼 마음대로 지정하면 된다.

### createRef를 통한 ref 설정

ref를 만드는 또 다른 방법은 리액트에 내장되어 있는 createRef라는 함수를 사용하는 것이다.  
createRef를 사용하여 ref를 만들려면 우선 컴포넌트 내부에서 멤버 변수로 React.createRef()를 담아 주어야 한다. 그리고 해당 멤버 변수를 ref를 달고자 하는 요소에 ref props로 넣어 주면 ref 설정이 완료된다.

```javascript
(...)
input = React.createRef();

handleFocus = () => {
  this.input.current.focus();
}

render() {
  return (
    <div>
      <input ref={this.input} />
    </div>
  );
}
(...)
```

설정한 뒤 나중에 ref를 설정해 준 DOM에 접근하려면 this.input.current를 조회하면 된다. 콜백 함수를 사용할 때와 다른 점은 이렇게 뒷부분에 .current를 넣어 주어야 한다는 것이다.

### 컴포넌트에 ref 달기

리액트에서는 컴포넌트에도 ref를 달 수 있다. 이 방법은 주로 컴포넌트 내부에 있는 DOM을 컴포넌트 외부에서 사용할 때 쓴다.

```javascript
<MyComponent
  ref={(ref) => {
    this.myComponent = ref;
  }}
/>
```

이렇게 하면 MyComponent 내부의 메서드 및 멤버 변수에도 접근할 수 있다. 즉, 내부의 ref에도 접근할 수 있다.

## 컴포넌트 반복

아래와 같이 문자열로 구성된 배열을 선언하고 그 배열 값을 사용하여 \<li>⋯\</li> JSX 코드로 된 배열을 새로 생성한 후 nameList라는 변수에 담고 렌더링을 하면 "key" prop이 없다는 경고 메시지가 표시된다.

```javascript
(...)
const names = ['snowman', 'ice', 'snow', 'wind'];
const nameList = names.map(name => <li>{name}</li>);
return <ul>{nameList}</ul>;
(...)
```

### key

리액트에서는 key는 컴포넌트 배열을 랜더링했을 때 어떤 원소에 변동이 있었는지 알아내려고 사용한다. key가 없을 때는 Virtual DOM을 비교하는 과정에서 리스트를 순차적으로 비교하면서 변화를 감지한다. 하지만 key가 있다면 이 값을 사용하여 어떤 변화가 일어났는지 더욱 빠르게 알아낼 수 있다.  
key 값을 설정할 때는 map 함수의 인자로 전달되는 함수 내부에서 컴포넌트 props를 설정하듯이 설정하면 된다. key 값은 언제나 유일해야 한다.

```javascript
(...)
const nameList = names.map((name, index) => <li key={index}>{name}</li>;
(...)
```

고유한 값이 없을 때만 index 값을 key로 사용해야 한다. index를 key로 사용하면 배열이 변경될 때 효율적으로 리렌더링하지 못한다.

### 데이터 추가/제거 기능 구현하기

래액트에서 상태를 업데이트할 때는 기존 상태를 그대로 두면서 새로운 값을 상태로 설정해야 한다. 이를 불변성 유지라고 한다. 불변성 유지를 해 주어야 나중에 리액트 컴포넌트의 성능을 최적화할 수 있다.

```javascript
(...)
const [names, setNames] = useState([
  { id: 1, text: "snowman" },
  { id: 2, text: "ice" },
  { id: 3, text: "snow" },
  { id: 4, text: "wind" },
]);
const [inputText, setInputText] = useState('');
const [nextId, setNextId] = useState(5);

const onChange = event => setInputText(event.target.value);
const onClick = () => {
  const nextNames = names.concat({
    id: nextId,
    text: inputText
  });
  setNextId(nextId + 1);
  setNames(nextNames);
  setInputText('');
};
const onRemove = id => {
  const nextNames = names.filter(name => name.id !== id);
  setNames(nextNames);
};
const namesList = names.map(name => (
  <li key={name.id} onDoubleClick={() => onRemove(name.id)}>
    {name.text}
  </li>
));

return (
  <>
    <input value={inputText} onChange={onChange} />
    <button onClick={onClick}>추가</button>
    <ul>{namesList}</ul>
  </>
);
(...)
```

## 컴포넌트의 라이프사이클 메서드

모든 리액트 컴포넌트에는 라이프사이클이 존재한다. 컴포넌트의 수명은 페이지에 렌더링되기 전인 준비 과정에서 시작하여 페이지에서 사라질 때 끝난다.  
라이프사이클 메서드는 클래스형 컴포넌트에서만 사용할 수 있다.  
라이프사이클 메서드의 종류는 총 아홉 가지이다. Will 접두사가 붙은 메서드는 어떤 작업을 작동하기 전에 실행되는 메서드이고, Did 접두사가 붙은 메서드는 어떤 작업을 작동한 후에 실행되는 메서드이다. 이 메서드들은 컴포넌트 클래스에서 덮어 써 선언함으로써 사용할 수 있다.  
라이프사이클은 총 세 가지, 즉 마운트, 업데이트, 언마운트 카테고리로 나뉜다.

### 마운트

DOM이 생성되고 웹 브라우저상에 나타나는 것을 마운트<sup>mount</sup>라고 한다. 이때 호출하는 메서드는 다음과 같다.

- constructor: 컴포넌트를 새로 만들 때마다 호출되는 클래스 생성자 메서드이다.
- getDerivedStateFromProps: props에 있는 값을 state에 넣을 때 사용하는 메서드이다.
- render: UI를 렌더링하는 메서드이다.
- componentDidMount: 컴포넌트가 웹 브라우저상에 나타난 후 호출하는 메서드이다.

### 업데이트

컴포넌트는 다음과 같은 총 네 가지 경우에 업데이트한다.

1. props가 바뀔 때
2. state가 바뀔 때
3. 부모 컴포넌트가 리렌더링될 때
4. this.forceUpdate로 강제로 렌더링을 트리거할 때

이렇게 컴포넌트를 업데이트할 때는 다음 메서드를 호출한다.

- getDerivedStateFromProps: 이 메서드는 마운트 과정에서도 호출되며, 업데이트가 시작하기 전에도 호출된다. props의 변화에 따라 state 값에도 변화를 주고 싶을 때 사용한다.
- shouldComponentUpdate: 컴포넌트가 리렌더링을 해야 할지 말아야 할지를 결정하는 메서드이다. true를 반환하면 다음 라이프사이클 메서드를 계속 실행하고, false를 반환하면 작업을 중지한다.
- render: 컴포넌트를 리렌더링한다.
- getSnapshotBeforeUpdate: 컴포넌트 변화를 DOM에 반영하기 바로 직전에 호출하는 메서드이다.
- componentDidUpdate: 컴포넌트의 업데이트 작업이 끝난 후 호출하는 메서드이다.

### 언마운트

컴포넌트를 DOM에서 제거하는 것을 언마운트<sup>unmount</sup>라고 한다.

- componentWillUnmount: 컴포넌트가 웹 브라우저상에서 사라지기 전에 호출하는 메서드이다.

### 라이프사이클 메서드 살펴보기

#### render() 함수

이 메서드는 컴포넌트 모양새를 정의한다. 라이프사이클 메서드 중 유일한 필수 메서드이다.  
이 메서드 안에서 this.props와 this.state에 접근할 수 있으며, 리액트 요소를 반환한다.  
이 메서드 안에서는 이벤트 설정이 아닌 곳에서 setState를 사용하면 안되며, 브라우저의 DOM에 접근해서도 안된다.  
DOM 정보를 가져오거나 state에 변화를 줄 때는 componentDidMount에서 처리해야 한다.

```
render() { ... }
```

#### constructor 메서드

이 메서드는 컴포넌트의 생성자 메서드로 컴포넌트를 만들 때 처음으로 실행되고 초기 state를 정할 수 있다.

```
constructor(props) { ... }
```

#### getDerivedStateFromProps 메서드

props로 받아 온 값을 state에 동기화시키는 용도로 사용하며, 컴포넌트가 마운트될 때와 업데이트될 때 호출된다.

```
static getDerivedStateFromProps(nextProps, prevState) { ... }
```

#### componentDidMount 메서드

이 메서드는 컴포넌트를 만들고, 첫 렌더링을 다 마친 후 실행한다.  
이 안에서 라이브러리의 함수를 호출하거나 이벤트 등록, 비동기 작업을 처리하면 된다.

```
componentDidMount() { ... }
```

#### shouldComponentUpdate 메서드

이 메서드는 props 또는 state를 변경했을 때, 리렌더링을 시작할지 여부를 지정하는 메서드이다.  
이 메서드는 반드시 true 값 또는 false 값을 반환해야 한다. 기본적으로 언제나 true 값을 반환하지만 false 값을 반환하면 업데이트 과정은 여기서 중지된다.  
이 메서드 안에서 현재 props와 state는 this.props와 this.state로 접근하고, 새로 설정될 props 또는 state는 nextProps와 nextState로 접근할 수 있다.

```
shouldComponentUpdate(nextProps, nextState) { ... }
```

#### getSnapshotBeforeUpdate 메서드

이 메서드는 render에서 만들어진 결과물이 브라우저에 실제로 반영되기 직전에 호출된다.  
이 메서드에서 반환하는 값은 componentDidUpdate에서 세 번째 파라미터인 snapshot 값으로 전달받을 수 있고 주로 업데이트하기 직전의 값을 참고할 일이 있을 때 활용된다.

```
getSnapshotBeforeUpdate(prevProps, prevState) { ... }
```

#### componentDidUpdate 메서드

이 메서드는 리렌더링을 완료한 후 실행한다.  
업데이트가 끝난 직후이므로, DOM 관련 처리를 해도 무방하다.  
여기서는 prevProps 또는 prevState를 사용하여 컴포넌트가 이전에 가졌던 데이터에 접근할 수 있다.  
getSnapshotBeforeUpdate에서 반환한 값이 있다면 여기서 snapshot 값을 전달받을 수 있다.

```
componentDidUpdate(prevProps, prevState, snapshot) { ... }
```

#### componentWillUnmount 메서드

이 메서드는 컴포넌트를 DOM에서 제거할 때 실행한다.  
componentDidMount에서 등록한 이벤트, 타이머, 직접 생성한 DOM이 있다면 여기서 제거 작업을 해야 한다.

```
componentWillUnmount() { ... }
```

#### componentDidCatch 메서드

이 메서드는 컴포넌트 렌더링 도중에 에러가 발생했을 때 애플리케이션이 먹통이 되지 않고 오류 UI를 보여 줄 수 있게 해 준다.  
error는 파라미터에 어떤 에러가 발생했는지 알려 주며, info 파라미터는 어디에 있는 코드에서 요류가 발생했는지에 대한 정보를 준다.  
이 메서드를 사용할 때는 컴포넌트 자신에게 발생하는 에러를 잡아낼 수 없고 자신의 this.props.children으로 전달되는 컴포넌트에서 발생하는 에러만 잡아낼 수 있다.

```
componentDidCatch(error, info) { ... }
```

## Hooks

Hooks는 함수 컴포넌트에서 다양한 작업을 할 수 있게 해 줍니다.

### useState

useState는 가장 기본적인 Hook이며, 함수 컴포넌트에서 가변적인 상태를 지닐 수 있게 해줍니다.

```javascript
const [value, setValue] = useState(0);
```

useSate 함수의 파라미터에는 상태의 기본값을 넣어 줍니다. 이 함수가 호출되면 배열을 반환하고 그 배열의 첫 번째 원소는 상태 값, 두 번째 원소는 상태를 설정하는 함수입니다. 이 함수에 파라미터를 넣어서 호출하면 전달받은 파라미터로 값이 바뀌고 컴포넌트가 정상적으로 리렌더링됩니다.

### useEffect

useEffect는 리액트 컴포넌트가 **렌더링될 때마다 특정 작업을 수행**하도록 설정할 수 있는 Hook입니다. 클래스형 컴포넌트의 componentDidMount와 componentDidUpdate를 합친 형태로 보아도 무방합니다.

```javascript
useEffect(() => {
  console.log("rendering complited");
});
```

<br>

**마운트될 때만 실행하고 싶을 때**  
useEffect에서 설정한 함수를 컴포넌트가 화면에 맨 처음 렌더링될 때만 실행하고, 업데이트될 때는 실행하지 않으려면 함수의 두 번째 파라미터로 비어 있은 배열을 넣어 주면 됩니다.

```javascript
useEffect(() => {
  console.log("run only when mounted");
}, []);
```

<br>

**특정 값이 업데이트될 때만 실행하고 싶을 때**  
useEffect를 사용할 때, 특정 값이 변경될 때만 호출하고 싶을 경우에는 useEffect의 두 번째 파라미터로 전달되는 배열 안에 검사하고 싶은 값을 넣어 주면 됩니다. 배열 안에는 useState를 통해 관리하고 있는 상태를 넣어 주어도 되고, props로 전달받은 값을 넣어 주어도 됩니다.

```javascript
useEffect(() => {
  console.log(name);
}, [name]);
```

**뒷정리하기**  
컴포넌트가 언마운트되기 전이나 업데이트되기 직전에 어떠한 작업을 수행하고 싶다면 useEffect에서 뒷정리<sup>cleanup</sup> 함수를 반환해 주어야 합니다.

```javascript
useEffect(() => {
  console.log("effect");
  return () => {
    console.log("cleanup");
  };
});
```

오직 언마운트될 때만 뒷정리 함수를 호출하고 싶다면 useEffect 함수의 두 번째 파라미터에 비어 있는 배열을 넣으면 됩니다.

### useReducer

useReducer는 useState보다 더 다양한 컴포넌트 상황에 따라 **다양한 상태를 다른 값으로 업데이트**해 주고 싶을 때 사용하는 Hook입니다.  
리듀서는 현재 상태, 그리고 업데이트를 위해 필요한 정보를 담은 액션 값을 전달받아 새로운 상태를 반환하는 함수입니다. 리듀서 함수에서 새로운 상태를 만들 때는 반드시 불변성을 지켜 주어야 합니다.

```javascript
function reducer(state, action) {
  return { ... };
}

// 액션 값
{
  type: 'INCREMENT'
}
```

useReducer에서 사용하는 액션 객체는 반드시 type을 지니고 있을 필요가 없습니다. 심지어 객체가 아니라 문자열이나 숫자여도 상관없습니다.

```javascript
import React, { useReducer } from "react";

function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { value: state.value + 1 };
    case "DECREMENT":
      return { value: state.value - 1 };
    default:
      return state;
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { value: 0 });

  return (
    <div>
      {state.value}
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+1</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-1</button>
    </div>
  );
};

export default Counter;
```

useReducer의 첫 번째 파라미터에는 리듀서 함수를 넣고, 두 번째 파라미터에는 해당 리듀서의 기본값을 넣어 줍니다. 이 Hook을 사용하면 현재 가리키고 있는 상태인 state 값과 액션을 발생시키는 함수인 dispatch 함수를 받아 옵니다. dispatch(action)과 같은 형태로, 함수 안에 파라미터로 액션 값을 넣어 주면 리듀서 함수가 호출되는 구조입니다.

### useMemo

useMemo를 사용하면 함수 컴포넌트 내부에서 발생하는 **연산을 최적화**할 수 있습니다. 렌더링하는 과정에서 특정 값이 바뀌었을 때만 연산을 실행하고, 원하는 값이 바뀌지 않았다면 이전에 연산했던 결과를 다시 사용하는 방식입니다.

```javascript
(...)
const getAverage = numbers => {
  if (number.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b, 0);
  return sum / numbers.length;
};
(...)
const avg = useMemo(() => getAverage(list), [list]);
(...)
```

### useCallback

useCallback은 useMemo와 상당히 비슷한 함수입니다. 이 Hook을 사용하면 만들어 놨던 함수를 재사용하여 **렌더링 성능을 최적화**할 수 있습니다.  
useCallback의 첫 번째 파라미터에는 생성하고 싶은 함수를 넣고, 두 번째 파라미터에는 배열을 넣으면 됩니다. 이 배열에는 어떤 값이 바뀌었을 때 함수를 새로 생성해야 하는지 명시해야 합니다. 함수 내부에서 상태 값에 의존해야 할 때는 그 값을 반드시 두 번째 파라미터 안에 포함시켜 주어야 합니다.

```javascript
(...)
const onChange = useCallback(e => {
  setNumber(e.target.value);
}, []);

const onInsert = useCallback(() => {
  const nextList = list.concat(parseInt(number));
  setList(nextList);
  setNumber('');
}, [number, list]);
(...)
```

### useRef

useRef Hook은 함수 컴포넌트에서 ref를 쉽게 사용할 수 있도록 해 줍니다.  
useRef를 사용하여 ref를 설정하면 useRef를 통해 만든 객체 안의 current 값이 실제 엘리먼트를 가리킵니다.

```javascript
(...)
const inputEl = useRef(null);
(...)
const onInsert =
(...)
  inputEl.current.focus();
(...)
  <input value={number} onChange={onChange} ref={inputEl} />
(...)
```

추가로 컴포넌트 로컬 변수를 사용해야 할 때도 useRef를 활용할 수 있습니다. 여기서 로컬 변수란 렌더링과 상관없이 바뀔 수 있는 값을 의미합니다.  
ref 안의 값이 바뀌어도 컴포넌트가 렌더링되지 않는다는 점에는 주의해야 합니다. **렌더링과 관련되지 않은 값을 관리할 때만 사용**해야 합니다.

```javascript
(...)
const id = useRef(1);
const setId = n => {
  id.current = n;
};
const printId = () => {
  console.log(id.current);
};
(...)
```

### 커스텀 Hooks 만들기

여러 컴포넌트에서 비슷한 기능을 공유할 경우, 이를 자신만의 Hook으로 작성하여 로직을 재사용할 수 있습니다.

```javascript
import { useReducer } from "react";

function reducer(state, action) {
  return {
    ...state,
    [action.name]: action.value,
  };
}

export default function useInputs(initialFrom) {
  const [state, dispatch] = useReducer(reducer, initialForm);
  const onChange = (e) => {
    dispatch(e.target);
  };
  return [state, onChange];
}
```

## 컴포넌트 스타일링

리액트에서 컴포넌트를 스타일링할 때는 다양한 방식을 사용할 수 있습니다. 여러 방식 중에서 딱히 정해진 방식이란 없습니다.

### 일반 CSS

컴포넌트를 스타일링하는 가장 기본적인 방식입니다.  
CSS를 작성할 때 가장 중요한 점은 CSS 클래스를 중복되지 않게 만드는 것입니다. CSS 클래스가 중복되는 것을 방지하는 여러 가지 방식이 있는데, 그중 하나는 이름을 지을 때 특별한 규칙(예. 이름-클래스)을 사용하여 짓는 것이고, 또 다른 하나는 CSS Selector(예. .App .logo)를 활용하는 것입니다.

### Sass

Sass<sup>Syntactically Awesome Style Sheets</sup>는 CSS 전처리기로 복잡한 작업을 쉽게 할 수 있도록 해 주고, 스타일 코드의 재활용성을 높여 줄 뿐만 아니라 코드의 가독성을 높여서 유지 보수를 더욱 쉽게 해 줍니다.  
Sass에서는 두 가지 확장자 .scss와 .sass를 지원합니다. 주요 차이점을 보면, .sass 확장자는 중괄호와 세미콜론을 사용하지 않습니다. 반면 .scss 확장자는 기존 CSS를 작성하는 방식과 비교해서 문법이 크게 다르지 않습니다.  
여러 파일에서 사용될 수 있는 Sass 변수 및 믹스인은 다른 파일로 따로 분리하여 작성한 뒤 필요한 곳에서 쉽게 불러와 사용할 수 있습니다.

### CSS Module

CSS Module은 CSS를 불러와서 사용할 때 클래스 이름을 고유한 값, 즉 \[파일 이름]\_\[클래스 이름]\_\[해시값] 형태로 자동으로 만들어서 컴포넌트 스타일 클래스 이름이 중첩되는 현상을 방지해 주는 기술입니다.  
CSS Module을 사용하면 클래스 이름을 지을 때 그 고유성에 대해 고민하지 않아도 됩니다. 흔히 사용하는 단어로 이름을 짓는다고 해도 전혀 문제가 되지 않습니다. 해당 클래스는 우리가 방금 만든 스타일을 직접 불러온 컴포넌트 내부에서만 작동하기 때문입니다.  
특정 클래스가 웹 페이지에서 전역적으로 사용되는 경우라면 :global을 앞에 입력하여 글로벌 CSS임을 명시해 줄 수 있습니다.

**classnames**  
classnames는 CSS 클래스를 조건부로 설정할 때 매우 유용한 라이브러리입니다. 또한, CSS Module을 사용할 때 이 라이브러리를 사용하면 여러 클래스를 적용할 때 매우 편리합니다.

### styled-components

컴포넌트 스타일링의 또 다른 패러다임은 자바스크립트 파일 안에 스타일을 선언하는 방식입니다. 이 방식을 'CSS-in-JS'라고 부릅니다. CSS-in-JS 라이브러리 중에서 개발자들이 가장 선호하는 라이브러리는 styled-components입니다.  
styled-components를 사용하면 자바스크립트 파일 하나에 스타일까지 작성할 수 있기 때문에 .css 또는 .scss 확장자를 가진 스타일 파일을 따로 만들지 않아도 된다는 큰 이점이 있습니다.

**Tagged 템플릿 리터럴**  
Tagged 템플릿 리터럴을 사용하면 템플릿 사이사이에 들어가는 자바스크립트 객체나 함수의 원본 값을 그대로 추출할 수 있습니다. styled-components는 이러한 속성을 사용하여 styled-components로 만든 컴포넌트의 props를 스타일 쪽에서 쉽게 조회할 수 있도록 해줍니다.

**스타일링된 엘리먼트 만들기**  
styled-components를 사용하여 스타일링된 엘리먼트를 만들 때는 컴포넌트 파일의 상단에서 styled를 불러오고, styled.태그명을 사용하여 구현합니다. 사용해야 할 태그명이 유동적이거나 특정 컴포넌트 자체에 스타일링해 주고 싶다면 styled('태그명')이나 styled(컴포넌트)를 사용하여 구현합니다.

**스타일에서 props 조회하기**  
styled-components를 사용하면 스타일 쪽에서 컴포넌트에게 전달된 props 값을 참조할 수 있습니다.

```javascript
(...)
const Box = styled.div`
  background: ${props => props.color || 'blue'};
(...)
<Box color="black">(...)</Box>
(...)
```

**props에 따른 조건부 스타일링**  
styled-components에서는 조건부 스타일링을 간단하게 props로 처리할 수 있습니다.

```javascript
import styled, { css } from 'styled-components';
(...)
${props =>
  props.inverted &&
  css`
    (...)
  `};
(...)
<Button inverted={true}>(...)</Button>
(...)
```

**반응형 디자인**  
브라우저의 크기에 따른 다른 스타일을 적용하기 위해서는 일반 CSS를 사용할 때와 똑같이 media 쿼리를 사용하면 됩니다.

## 컴포넌트 성능 최적화

### 느려지는 원인 분석

- 자신이 전달받은 props가 변경될 때
- 자신의 state가 바뀔 때
- 부모 컴포넌트가 리렌더링될 때
- forceUpdate 함수가 실행될 때

### React.memo를 사용하여 컴포넌트 성능 최적화

React.memo는 컴포넌트의 props가 바뀌지 않았다면, 리렌더링하지 않도록 설정하여 함수 컴포넌트의 리렌더링 성능을 최적화해 줄 수 있습니다.

```javascript
(...)
export default React.memo((...));
```

### 함수가 바뀌지 않게 하기

함수가 계속 만들어지는 상황을 방지하는 방법은 두 가지입니다. 첫 번째 방법은 useState의 함수 업데이트 기능을 사용하는 것이고, 두 번째 방법은 useReducer를 사용하는 것입니다.

### 불변성의 중요성

리액트 컴포넌트에서 상태를 업데이트할 때 불변성을 지키는 것은 매우 중요합니다.  
기존 데이터를 수정할 때 직접 수정하지 않고, 새로운 배열을 만든 다음에 새로운 객체를 만들어서 필요한 부분을 교체해 주는 방식으로 구현합니다.  
불변성이 지켜지지 않으면 객체 내부의 값이 새로워져도 바뀐 것을 감지하지 못합니다.

### react-virtualized를 사용한 렌더링 최적화

react-virtualized를 사용하면 리스트 컴포넌트에서 스크롤되기 전에 보이지 않는 컴포넌트는 렌더링하지 않고 크기만 차지하게끔 할 수 있습니다. 그리고 만약 스크롤되면 해당 스크롤 위치에서 보여 주어야 할 컴포넌트를 자연스럽게 렌더링시킵니다.

### immer를 사용한 불변성 유지

immer를 사용하면 불변성을 유지하는 작업을 매우 간단하게 처리할 수 있습니다.

```javascript
import produce from "immer";
const nextState = produce(originalState, (draft) => {
  draft.somewhere.deep.inside = 5;
});
```

produce라는 함수는 두 가지 파라미터를 받습니다. 첫 번째 파라미터는 수정하고 싶은 상태이고, 두 번째 파라미터는 상태를 어떻게 업데이트할지 정의하는 함수입니다. 두 번째 파라미터로 전달되는 함수 내부에서 원하는 값을 변경하면, produce 함수가 불변성 유지를 대신해 주면서 새로운 상태를 생성해 줍니다.  
immer를 사용하여 컴포넌트 상태를 작성할 때는 객체 안에 있는 값을 직접 수정하거나, 배열에 직접적인 변화를 일으키는 push, splice 등의 함수를 사용해도 무방합니다.

## 리액트 라우터로 SPA 개발

### SPA

SPA는 Single Page Application의 약어입니다. 말 그대로 한 개의 페이지로 이루어진 애플리케이션이라는 의미입니다. SPA는 서버에서 사용자에게 제공하는 페이지는 한 종류이지만, 해당 페이지에서 로딩된 자바스크립트와 현재 사용자 브라우저의 주소 상태에 따라 다양한 화면을 보여 줄 수 있습니다.  
다른 주소에 다른 화면을 보여 주는 것을 라우팅이라고 합니다. 리액트 라이브러리 자체에 이 기능이 내장되어 있지는 않습니다. 그 대신 브라우저의 API를 직접 사용하여 이를 관리하거나, 라이브러리를 사용하여 이 작업을 더욱 쉽게 구현할 수 있습니다. 리액트 라우팅 라이브러리는 react-router, reach-router, Next.js 등 여러 가지가 있습니다.  
SPA의 단점은 앱의 규모가 커지면 자바스크립트 파일이 너무 커진다는 것입니다. 페이지 로딩 시 사용자가 실제로 방문하지 않을 수도 있는 페이지의 스크립트도 불러오기 때문입니다. 하지만 코드 스플리팅을 사용하면 라우트별로 파일들을 나누어서 트래픽과 로딩 속도를 개선할 수 있습니다.

### 리액트 라우터 사용법

프로젝트에 리액트 라우터를 적용할 때는 src/index.js 파일에서 react-router-dom에 내장되어 있는 BrowserRouter라는 컴포넌트를 사용하여 감싸면 됩니다. 이 컴포넌트는 웹 애플리케이션에 HTML5의 History API를 사용하여 페이지를 새로고침하지 않고도 주소를 변경하고, 현재 주소에 관련된 정보를 props로 쉽게 조회하거나 사용할 수 있도록 해 줍니다.

**Route 컴포넌트**  
Route라는 컴포넌트를 사용하여 사용자의 현재 경로에 따라 다른 컴포넌트를 보여 줄 수 있습니다. Route 컴포넌트를 사용하면 어떤 규칙을 가진 경로에 어떤 컴포넌트를 보여 줄지 정의할 수 있습니다.  
Route 컴포넌트에는 component 대신 render라는 props를 넣어 줄 수 있습니다. 컴포넌트 자체를 전달하는 것이 아니라, 보여 주고 싶은 JSX를 넣어 줄 수 있습니다. 따로 컴포넌트를 만들기 애매한 상황에 사용해도 되고, 컴포넌트에 props를 별도로 넣어 주고 싶을 때도 사용할 수 있습니다.

```javascript
(...)
import { Route } from 'react-router-dom';
import Home from './Home';

const App = () => {
  return (
    <div>
      <Route path="/" component={Home} exact={true} />
    </div>
  );
};
(...)
```

**Link 컴포넌트**  
Link 컴포넌트는 클릭하면 다른 주소로 이동시켜 주는 컴포넌트입니다. Link 컴포넌트를 사용하여 페이지를 전환하면, 페이지를 새로 불러오지 않고 애플리케이션은 그대로 유지한 상태에서 HTML5 History API를 사용하여 페이지의 주소만 변경해 줍니다. Link 컴포넌트 자체는 a 태그로 이루어져 있지만, 페이지 전환을 방지하는 기능이 내장되어 있습니다.

```javascript
(...)
import { Route, Link } from 'react-router-dom';
(...)
    <Link to="/">Home</Link>
(...)
```

**URL 파라미터**  
파라미터는 특정 아이디 혹은 이름을 사용하여 조회할 때 사용합니다.  
URL 파라미터를 사용할 때는 라우트로 사용되는 컴포넌트에서 받아 오는 match라는 객체 안의 params 값을 참조합니다. match 객체 안에는 현재 컴포넌트가 어떤 경로 규칙에 의해 보이는지에 대한 정보가 들어 있습니다.

**URL 쿼리**  
쿼리는 어떤 키워드를 검색하거나 페이지에 필요한 옵션을 전달할 때 사용합니다.  
쿼리는 location 객체에 들어 있는 search 값에서 조회할 수 있습니다. location 객체는 라우터로 사용된 컴포넌트에게 props로 전달되며, 웹 애플리케이션의 현재 주소에 대한 정보를 지니고 있습니다.  
URL 쿼리를 읽을 때는 location 객체가 지닌 값 중에서 search 값을 확인해야 합니다. 이 값은 문자열 형태로 되어 있습니다. search 값에서 특정 값을 읽어 오기 위해서는 이 문자열을 객체 형태로 변환해 주어야 합니다.  
쿼리를 사용할 때는 쿼리 문자열을 객체로 파싱하는 과정에서 결과 값은 언제나 문자열이라는 점에 주의해야 합니다.

**history**  
history 객체는 라우트로 사용된 컴포넌트에 match, location과 함께 전달되는 props 중 하나로, 이 객체를 통해 컴포넌트 내에 구현하는 메서드에서 라우터 API를 호출할 수 있습니다. 예를 들어 특정 버튼을 눌렀을 때 뒤로 가거나, 로그인 후 화면을 전환하거나, 다른 페이지로 이탈하는 것을 방지해야 할 때 history를 활용합니다.

**withRouter**  
withRouter 함수는 HoC<sup>Higher-order Component</sup>입니다. 라우트로 사용된 컴포넌트가 아니어도 match, location, history 객체를 접근할 수 있게 해 줍니다. withRouter를 사용할 때는 컴포넌트를 내보내 줄 때 함수로 감싸 줍니다. withRouter를 사용하면 현재 자신을 보여 주고 있는 라우트 컴포넌트를 기준으로 match가 전달됩니다.

**Switch**  
Switch 컴포넌트는 여러 Route를 감싸서 그중 일치하는 단 하나의 라우트만을 렌더링시켜 줍니다. Switch를 사용하면 모든 규칙과 일치하지 않을 때 보여 줄 Not Found 페이지도 구현할 수 있습니다.

**NavLink**  
NavLink는 Link와 비슷합니다. 현재 경로와 Link에서 사용하는 경로가 일치하는 경우 특정 스타일 혹은 CSS 클래스를 적용할 수 있는 컴포넌트입니다. NavLink에서 링크가 활성화되었을 때의 스타일을 적용할 때는 activeStyle 값을, CSS 클래스를 적용할 때는 activeClassName 값을 props로 넣어 주면 됩니다.

## Context API

Context API는 리액트 프로젝트에서 전역적으로 사용할 데이터가 있을 때 유용한 기능입니다. 이를테면 사용자 로그인 정보, 애플리케이션 환경 설정, 테마 등 여러 종류가 있습니다.

### 새 Context 만들기

새 Context를 만들 때는 createContext 함수를 사용합니다. 파라미터에는 해당 Context의 기본 상태를 지정합니다.

```javascript
import { createContext } from "react";

const ColorContext = createContext({ color: "black" });

export default ColorContext;
```

### Consumer

Consumer라는 컴포넌트를 사용하여 값을 조회할 수 있습니다.

```javascript
(...)
import ColorContext from '../contexts/color';

const ColorBox = () => {
  return (
    <ColorContext.Consumer>
      {value => (
        <div
          style={{
            width: '64px',
            height: '64px',
            background: value.color
          }}
        />
      )}
    </ColorContext.Consumer>
  );
};
(...)
```

Consumer 사이에 중괄호를 열어서 그 안에 함수를 넣어 주었습니다. 이러한 패턴을 Function as a child, 혹은 Render Props라고 합니다. 컴포넌트의 children이 있어야 할 자리에 일반 JSX 혹은 문자열이 아닌 함수를 전달합니다.

### Provider

Provider를 사용하면 Context의 value를 변경할 수 있습니다. 기존에 createContext 함수를 사용할 때는 파라미터로 Context의 기본값을 넣어 주는데 이 기본값은 Provider를 사용하지 않았을 때만 사용됩니다. 만약 Provider는 사용했는데 value를 명시하지 않았다면, 이 기본값을 사용하지 않기 때문에 오류가 발생합니다.

```javascript
(...)
    <ColorContext.Provider value={{ color: 'red' }}>
      <div>
        <ColorBox />
      </div>
    </ColorContext.Provider>
(...)
```

### 동적 Context

Context의 value에는 무조건 상태 값만 있어야 하는 것은 아닙니다. 함수도 전달해 줄 수도 있습니다.

```javascript
(...)
const ColorContext = createContext({
  state: { color: 'black', subcolor: 'red' },
  actions: {
    setColor: () => {},
    setSubcolor: () => {}
  }
});

const ColorProvider = ({ children }) => {
  const [color, setColor] = useState('black');
  const [subcolor, setSubcolor] = useState('red');

  const value = {
    state: { color, subcolor },
    actions: { setColor, setSubcolor }
  };

  return (
    <ColorContext.Provider value={value}>{children}</ColorContext.Provider>
  );
};

const { Consumer: ColorConsumer } = ColorContext;

export { ColorProvider, ColorConsumer };
(...)
```

Context에서 값을 동적으로 사용할 때 반드시 묶어줄 필요는 없지만, 이렇게 state와 actions 객체를 따로따로 분리해 주면 나중에 다른 컴포넌트에서 Context의 값을 사용할 때 편합니다.

### useContext

리액트에 내장되어 있는 Hooks 중에서 useContext라는 Hook을 사용하면, 함수 컴포넌트에서 Context를 아주 편하게 사용할 수 있습니다. useContext는 children에 함수를 전달하는 Render Props 패턴을 사용하지 않고 Context 값을 조회할 수 있습니다.

```javascript
(...)
const { state } = useContext(ColorContext);
return (
  <>
    <div
      style={{
        width: '64px',
        height: '64px',
        background: state.color
      }}
    />
(...)
```

## 리덕스

리덕스는 가장 많이 사용하는 리액트 상태 관리 라이브러리입니다. 리덕스를 사용하면 컴포넌트의 상태 업데이트 관련 로직을 다른 파일로 분리시켜서 더욱 효율적으로 관리할 수 있습니다. 또한, 컴포넌트끼리 똑같은 상태를 공유해야 할 때도 여러 컴포넌트를 거치지 않고 손쉽게 상태 값을 전달하거나 업데이트할 수 있습니다.

**액션**  
상태에 어떠한 변화가 필요하면 액션<sup>action</sup>이란 것이 발생합니다. 이는 하나의 객체로 표현됩니다.  
액션 객체는 type 필드를 반드시 가지고 있어야 합니다. 이 값을 액션의 이름이라고 생각하면 됩니다. 그리고 그 외의 값들은 나중에 상태 업데이트를 할 때 참고해야 할 값이며, 작성자 마음대로 넣을 수 있습니다.

```javascript
{
  type: 'ADD_TODO',
  data: {
    id: 1,
    text: 'todo'
  }
}
```

**액션 생성 함수**  
액션 생성 함수<sup>action creator</sup>는 액션 객체를 만들어 주는 함수입니다. 어떤 변화를 일으켜야 할 때마다 액션 객체를 만들어야 하는데 매번 액션 객체를 직접 작성하기 번거로울 수 있고, 만드는 과정에서 실수로 정보를 놓칠 수도 있습니다. 이러한 일을 방지하기 위해 이를 함수로 만들어서 관리합니다.

```javascript
const changeInput = (text) => ({
  type: "CHANGE_INPUT",
  text,
});
```

**리듀서**  
리듀서<sup>reducer</sup>는 변화를 일으키는 함수입니다. 액션을 만들어서 발생시키면 리듀서가 현재 상태와 전달받은 액션 객체를 파라미터로 받아 옵니다. 그리고 두 값을 참고하여 새로운 상태를 만들어서 반환해 줍니다.

```javascript
const initialState = {
  counter: 1,
};
function reducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return {
        counter: state.counter + 1,
      };
    default:
      return state;
  }
}
```

**스토어**  
프로젝트에 리덕스를 적용하기 위해 스토어<sup>store</sup>를 만듭니다. 한 개의 프로젝트는 단 하나의 스토어만 가질 수 있습니다. 스토어 안에는 현재 애플리케이션 상태와 리듀서가 들어가 있으며, 그 외에도 몇 가지 중요한 내장 함수를 지닙니다.

**디스패치**  
디스패치<sup>dispatch</sup>는 스토어의 내장 함수 중 하나입니다. 디스패치는 '액션을 발생시키는 것'이라고 이해하면 됩니다. 이 함수는 dispatch(action)과 같은 형태로 액션 객체를 파라미터로 넣어서 호출합니다. 이 함수가 호출되면 스토어는 리듀서 함수를 실행시켜서 새로운 상태를 만들어 줍니다.

**구독**  
구독<sup>subscribe</sup>도 스토어의 내장 함수 중 하나입니다. subscribe 함수 안에 리스너 함수를 파라미터로 넣어서 호출해 주면, 이 리스너 함수가 액션이 디스패치되어 상태가 업데이트될 때마다 호출됩니다.

```javascript
const listener = () => {
  console.log("listener");
};
const unsubscribe = store.subscribe(listener);
```

### 리덕스의 세 가지 규칙

**단일 스토어**  
하나의 애플리케이션 안에는 하나의 스토어가 들어 있습니다. 사실 여러 개의 스토어를 사용하는 것이 완전히 불가능하지는 않습니다. 특정 업데이트가 너무 빈번하게 일어나거나 애플리케이션의 특정 부분을 완전히 분리시킬 때 여러 개의 스토어를 만들 수 있지만, 상태 관리가 복잡해질 수 있으므로 권장하지 않습니다.

**읽기 전용 상태**  
리덕스 상태는 읽기 전용입니다. 상태를 업데이트할 때 기존의 객체는 건드리지 않고 새로운 객체를 생성해 주어야 합니다.  
리덕스에서 불변성을 유지해야 하는 이유는 내부적으로 데이터가 변경되는 것을 감지하기 위해 얕은 비교 검사를 하기 때문입니다.

**리듀서는 순수한 함수**  
변화를 일으키는 리듀서 함수는 순수한 함수여야 합니다. 순수한 함수는 다음 조건을 만족합니다.

- 리듀서 함수는 이전 상태와 액션 객체를 파라미터로 받습니다.
- 파라미터 외의 값에는 의존하면 안 됩니다.
- 이전 상태는 절대로 건드리지 않고, 변화를 준 새로운 상태 객체를 만들어서 반환합니다.
- 똑같은 파라미터로 호출된 리듀서 함수는 언제나 똑같은 결과 값을 반환해야 합니다.

## 리액트에 리덕스 적용

### 컨테이너 컴포넌트

리덕스 스토어와 연동된 컴포넌트를 컨테이너 컴포넌트라고 부릅니다. 컴포넌트를 리덕스와 연동하려면 react-redux에서 제공하는 connect 함수를 사용해야 합니다.

```javascript
connect(mapStateToProps, mapDispatchToProps)(targetComponent);
```

mapStateToProps는 리덕스 스토어 안의 상태를 컴포넌트의 props로 넘겨주기 위해 설정하는 함수이고, mapDispatchToProps는 액션 생성 함수를 컴포넌트의 props로 넘겨주기 위해 사용하는 함수입니다.

```javascript
const mapStateToProps = state => ({
  number: state.counter.number
});

const mapDispatchToProps = dispatch => ({
  increase: () => {...},
  decrease: () => {...},
});
```

mapStateToProps와 mapDispatchProps에서 반환하는 객체 내부의 값들은 컴포넌트의 props로 전달됩니다. mapStateToProps는 state를 파라미터로 받아 오며, 이 값은 현재 스토어가 지니고 있는 상태를 가리킵니다. mapDispatchToProps의 경우 store의 내장 함수 dispatch를 파라미터로 받아 옵니다.

### useSelector Hook

useSelector Hook을 사용하면 connect 함수를 사용하지 않고도 리덕스의 상태를 조회할 수 있습니다.

```javascript
const result = useSelector(mapStateToProps);
```

### useDispatch Hook

이 Hook은 컴포넌트 내부에서 스토어의 내장 함수 dispatch를 사용할 수 있게 해 줍니다. 컨테이너 컴포넌트에서 액션을 디스패치해야 한다면 이 Hook을 사용하면 됩니다.

```javascript
const dispatch = useDispatch();
dispatch({ type: "SAMPLE_ACTION" });
```

## 리덕스 비동기 작업 관리

### redux-saga

**delay(number)**: 딜레이를 줍니다.  
**put(action())**: 특정 액션을 디스패치합니다.  
**takeEvery(actionType, saga)**: 들어오는 모든 액션에 대해 특정 작업을 처리해 줍니다.  
**takeLatest(actionType, saga)**: 가장 마지막으로 실행된 작업만 수행합니다.  
**call(promise, argument)**: Promise를 반환하는 함수를 호출하고 기다립니다. 첫번째 파라미터는 함수, 나머지는 해당 함수에 넣을 인수입니다.
