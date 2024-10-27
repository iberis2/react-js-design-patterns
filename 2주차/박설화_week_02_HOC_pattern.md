# [HOC 패턴](https://velog.io/@iberis/HOC-패턴-Funnel-유효성-검사하기)

> 고차 컴포넌트란 다른 컴포넌트를 받는 컴포넌트를 뜻한다. 
HOC는 인자로 넘긴 컴포넌트에게 추가되길 원하는 로직을 가지고 있다. 
HOC는 로직이 적용된 엘리먼트를 반환하게 된다.

compound 패턴으로 만든 Funnel 에 HOC 패턴을 이용해 유효성 검사 로직 추가하기

compound 패턴으로 만들어진 Funnel  은 다음과 같다.
`<Funnel>` 에 현재 step을 전달해서 `<Funnel.Step>` 의 name과 현재 step 이 일치하면 children 을 보여준다.

```tsx
import { createContext, useContext } from "react";

const FunnelContext = createContext<{ step?: string }>({});

interface FunnelProps {
  children: React.ReactNode;
  step: string;
}

const Funnel = ({ children, step }: FunnelProps) => {
  return (
    <FunnelContext.Provider value={{ step }}>{children}</FunnelContext.Provider>
  );
};

interface StepProps {
  children: React.ReactNode;
  name: string;
}

const Step = ({ children, name }: StepProps) => {
  const context = useContext(FunnelContext);
  if (context?.step === name) {
    return <>{children}</>;
  }
  return null;
};

export default Object.assign(Funnel, { Step });
```
```tsx
// App.tsx
  // ...생략
  <Funnel step={step}>
    <Funnel.Step name={steps[0]}>
      <Page title={steps[0]} onNext={onNextStep} />
    </Funnel.Step>
  </Funnel>
```

각 Page 에서는 input 을 입력받는데, 이전 페이지에서 input 을 입력하지 않고 넘어가면, \
다음 페이지가 보여지지 않고 `이전 페이지에서 입력된 내용이 없습니다` 라는 알림과 함께 이전 페이지로 되돌아가도록 하는 기능을 HOC 패턴으로 추가해보자.

> HOC는 컴포넌트를 인자로 받아 컴포넌트를 반환해야 한다

## 1. 만들어야 하는 로직

HOC 패턴으로 만들어야 하는 함수는 다음과 같다.
```tsx
// determine: 유효성(=이전 페이지에서 입력값을 받았는지) 검사하는 함수 
// PassedComponent : 검사를 통과하면 보여줄 페이지 컴포넌트

function requireFunnelData (determine, PassedComponent){
	return () => {
       const result = determine(/* 유효성 검사 */);
      
       if(result === true){
       	  return <>{PassedComponent()}</>
       }
      
      return null;
    }
}
```
첫 번째 인자로 컴포넌트를 보여줄 지 검사하는 함수 `determine`을 받아서 그 결과가 ok 이면 두 번째 인자로 받은 컴포넌트 `PassedComponent` 를 보여준다.

## 2. HOC 만들기

```tsx
export function requireFunnelData<Props, Cause, FunnelAtom>(
  determine: (
    state?: FunnelAtom
  ) => { ok: false; cause: Cause } | { ok: true; cause: undefined },
  PassedComponent: (props: Props, funnelAtom: FunnelAtom) => React.ReactNode
) {
  return function RequireFunnelData({
    onNeedMore,
    ...rest
  }: Props & { onNeedMore?: (cause?: Cause) => void }) {
    const data = useAtomValue(funnelAtom);
    const result = determine(data as FunnelAtom);

    useEffect(() => {
      if (!result.ok) {
        onNeedMore?.(result.cause);
      }
    }, [onNeedMore, result.ok, result.cause, data]);

    if (result.ok) {
      return <>{PassedComponent(rest as Props, data as FunnelAtom)}</>;
    }

    return null;
  };
}
```

1. `determine` 함수는  `{ ok: boolean; cause: 이유 타입 }` 을 반환한다. 
    - cause 는 Cause 제네릭 타입으로 사용하는 곳에서 타입을 지정하도록 했다.\
      (이번 구현에서는 string 으로 받아서 alert 로 보여줄 예정)
  

2. 결과가 `ok: true` 이면 `PassedComponent` 를 보여준다.
    - `PassedComponent` 에는 두 번째 인자로 **Funnel Data** (각 페이지의 input 에서 저장한 Funnel Atom Data) 를 넘겨준다.


3. 결과가 `ok: false` 이면 컴포넌트를 보여주지 않는데,
```tsx
   useEffect(() => {
      if (!result.ok) {
        onNeedMore?.(result.cause);
      }
    }, [onNeedMore, result.ok, result.cause, data]);
```
만약 props 로 `onNeedMore` 함수를 받았다면 `onNeedMore()` 을 실행시켜준다.
- `onNeedMore` 함수는 determine 을 통과하지 못했을 때 실행할 로직을 props 로 받은 함수이다. 
 ```tsx
  <SecondPage onNeedMore={(cause: string) =>{alert(cause)}} />
 ```
위와 같이 `result.ok === false` 일 때 **alert** 로 그 이유를 보여주는 함수를 `onNeedMore` props 로 정의할 수 있다.

## 3. HOC 적용
두 번째, 세 번째 페이지는 각각 이전 페이지에서 입력받은 값이 없으면 페이지를 보여주지 않아야 한다. 
`requireFunnelData()` 의 첫 번째 인자로 funnelData 에 이전 페이지 입력 값이 있는지 유효성 검사하는 함수를, 두 번째 인자로 보여줄 컴포넌트를 전달한다.

```tsx
/*
  requireFunnelData(determine: 유효성 검사 함수, PassedComponent: 보여줄 컴포넌트)
*/	

const SecondPage = requireFunnelData(
  // 1️⃣ determine : 유효성 검사 함수
  (funnelData) => {
     if (funnelData?.["First Page"]) { return { ok: true }; } 
     return { ok: false, cause: "첫 번째 페이지에 입력된 내용이 없습니다." };
  }, 
  
  // 2️⃣ PassedComponent : 보여줄 컴포넌트
  (props, funnelData) => {
    // funnelData 를 사용할 로직이 있으면 사용
   return <보여줄 컴포넌트 {...props} />
  }
)
```

```tsx
interface Props {
  title: stepType;
  onNext?: () => void;
  onPrev?: () => void;
}

const SecondPage = requireFunnelData<
  Props,
  string,
  { [key in stepType]: string }
>(
  (funnelData) => {
    if (funnelData?.["First Page"]) {
      return { ok: true };
    }
    return {
      ok: false,
      cause: "첫 번째 페이지에 입력된 내용이 없습니다.",
    };
  },
  (props, funnelData) => {
    return (
      <>
        <ShowFunnelData funnelData={funnelData} />
        <Page {...props} />
      </>
    );
  }
);

export default SecondPage;
```

`<ThirdPage />` 도 `<SecondPage>` 와 동일한 로직으로 만들고 `<Funnel.Step>` 의 **Children** 으로 전달해주면 된다.

```tsx
<Funnel>
  /* ...생략 */
  <Funnel.Step name={steps[1]}>
    <SecondPage
       title={steps[1]}
       onNext={onNextStep}
       onPrev={onPrevStep}
       onNeedMore={(cause) => { // 이전 페이지에서 입력 값을 받지 않았으면 
         alert(cause); // alert 으로 이유를 보여주고
         onPrevStep(); // 이전 페이지로 돌아가도록
       }}
    />
 </Funnel.Step>
  /* ...생략 */
</Funnel>
```

-----

# [🔗 최종 결과 codesandbox ](https://codesandbox.io/p/sandbox/hoc-component-pattern-qjvz8g)


참고 :
https://patterns-dev-kr.github.io/design-patterns/hoc-pattern/\
Toss Frontend Accelerator 1기 

