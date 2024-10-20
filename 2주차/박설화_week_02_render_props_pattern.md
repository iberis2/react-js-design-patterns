# [Render Props 패턴](https://velog.io/@iberis/Render-Props-패턴-1me39t71)

**목차**
- [Render Props 패턴을 사용하지 않는다면](#Render-Props-패턴을-사용하지-않는다면)
- [RenderProps 패턴으로 바꾸기](#RenderProps-패턴으로-바꾸기)
- [자식 컴포넌트를 함수로 받아보자](#자식-컴포넌트를-함수로-받아보자)
- [최종본](#최종본)
- [장단점](#장단점)
-----
<br />

>  Render prop은 컴포넌트의 prop으로 전달되는 함수이며, 이 함수는 JSX 엘리먼트를 반환한다.\
컴포넌트 자체는 아무것도 렌더링하지 않지만, render prop 함수를 호출하여 그 결과를 렌더링한다.\
render prop 함수를 호출할 때 인자를 전달할 수 있다.


Render Props 패턴을 사용할 수 있는 예시로, 리스트를 렌더링하는 컴포넌트를 작성해 보자.

## Render Props 패턴을 사용하지 않는다면


1. `item`에 사용될 데이터를 부모 컴포넌트로 끌어올려 `list`와 `item`으로 전달하거나:

```tsx
export default function App() {
  const dataList = ["Dog 1", "Dog 2", "Dog 3"]; // fetch data list
  
  return (
    <List>
      {dataList.map((data) => (
        <Item key={data} data={data} />
      ))}
    </List>
  );
}
```

2. list 컴포넌트 내부에서 item 컴포넌트를 작성할 수 있다.

```tsx
export default function List() { 
  const dataList = ["Dog 1", "Dog 2", "Dog 3"]; // fetch data list
  
  return (
    <div>
      {dataList.map((data) => (
        <Item key={data} data={data} />
      ))}
    </div>
  );
}
```

## RenderProps 패턴으로 바꾸기

Render Props 패턴을 사용하면, 자식 컴포넌트를 함수로 전달받아 동적으로 렌더링할 수 있다.

```tsx
interface DogListProps {
  render: (data: string) => React.ReactElement;
}

// Render Props 패턴으로 만든 List
function DogList({ render }: DogListProps) {
  const dogs = ["Dog 1", "Dog 2", "Dog 3"]; // fetch data list
  
  return <div>{dogs.map(render)}</div>;
}

// 사용할 때
export default function App() {
  return <DogList render={(dog) => <DogItem name={dog} />} />;
}
```

위 예시에서 DogList는 render prop을 호출하여 DogItem을 렌더링한다.

## 자식 컴포넌트를 함수로 받아보자

자식 컴포넌트를 함수로 받아 JSX에서 사용하는 방법도 Render Props 패턴의 일종이다. children prop으로 함수를 전달할 수 있으며, 이는 render prop과 같은 방식으로 동작한다.

```tsx
interface DogListProps {
  children: (data: string) => React.ReactElement;
}


// Render Props 패턴으로 만든 List
function DogList({ children }: DogListProps) {
  const dogs = ["Dog 1", "Dog 2", "Dog 3"]; // fetch data list
  
  return <div>{dogs.map(children)}</div>;
}

// 사용할 때
export default function App() {
  return <DogList>{(dog) => <DogItem name={dog} />}</DogList>;
}
```

이 방식은 render prop을 명시적으로 전달하는 대신, children prop을 활용하여 보다 자연스러운 방식으로 렌더링할 수 있게 해준다.

## 최종본

[code sand box](https://codesandbox.io/p/sandbox/render-props-patterns-c4f9dt?from-embed=)


## 장단점

### 장점
1. **간단한 데이터 공유**: Render Props 패턴을 사용하면 여러 컴포넌트 간에 데이터를 쉽게 공유할 수 있다.
2. **재사용성**: children prop을 사용하여 컴포넌트를 쉽게 재사용할 수 있다.
3. **명확한 데이터 흐름**: 함수의 인자로 명시적으로 prop이 전달되므로, 데이터가 어디에서 오는지 명확히 파악할 수 있다.
4. **로직과 UI 분리**: Render Props를 통해 로직과 UI를 분리할 수 있다. 상태를 가진 컴포넌트는 render prop을 받고, 상태가 없는 컴포넌트를 렌더링할 수 있다.
5. **유연한 컴포넌트 합성**: Render Props를 통해 부모 컴포넌트는 자식 컴포넌트의 렌더링 방식에 구애받지 않고 다양한 방식으로 자식을 렌더링할 수 있다. 이로 인해 컴포넌트 합성이 더 유연해진다.

### 단점
1. **Prop Drilling**: Render Props 패턴을 많이 사용하면 깊은 컴포넌트 구조에서 prop을 여러 단계로 전달하는 Prop Drilling이 발생할 수 있다.
	- 예시: ParentComponent -> ChildComponent -> GrandchildComponent 순으로 render prop을 전달할 경우, 코드의 깊이가 깊어져 관리가 어려워진다.
	- 대안: React Context API를 사용하여 전역 상태를 관리하거나, 상태 관리 라이브러리(예: Redux)를 사용해 prop 전달을 줄이는 방법을 고려할 수 있다.
2. **복잡성 증가**: 중첩된 Render Props 사용은 컴포넌트 트리를 복잡하게 만들어 가독성을 떨어뜨릴 수 있다.
	- 대안: Hooks나 Higher-Order Components (HOCs) 같은 다른 패턴을 사용해 코드 중첩을 줄이고 구조를 간소화할 수 있다.
3. **성능 문제**: render 함수를 매번 새로 정의하는 경우 불필요한 리렌더링이 발생할 수 있다. 부모 컴포넌트가 렌더링될 때마다 새로운 함수가 생성되므로, React는 이를 새로운 함수로 인식해 추가적인 렌더링을 유발할 수 있다.
    - 대안: 함수는 컴포넌트 외부에 정의하거나, React.memo나 useCallback을 사용해 불필요한 렌더링을 방지할 수 있다.
	- 예시 
   ```tsx
    import { useCallback } from 'react';
	
    // ① useCallback 을 사용하는 경우
    export default function Parent() {
      const renderChild = useCallback((data) => <div>{data}</div>, []);

      return <Child render={renderChild} />;
    }
    
    // ② React.memo 를 사용하는 경우
    const Child = React.memo(({ render }) => <div> {render('Some data')} </div>);
   ```

4. **디버깅과 테스트의 어려움**: Render Props로 인해 로직이 함수 내부에 묻히면 디버깅과 테스트가 어려워질 수 있다. 특히 익명 함수로 정의된 render 함수는 디버깅 시 함수 호출 스택을 추적하기 어렵게 만든다.
	- 대안: 복잡한 로직은 Render Props 내부에서 처리하지 않고, 외부에서 분리된 함수로 정의하여 디버깅 및 테스트를 쉽게 할 수 있도록 해야한다.

----
참고 : https://patterns-dev-kr.github.io/design-patterns/render-props-pattern/