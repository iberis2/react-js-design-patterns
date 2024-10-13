# [Container/Presentational 패턴](https://velog.io/@iberis/ContainerPresentational-패턴)
> 리액트에서  Container/Presentational Pattern 을 사용하면 비즈니스 로직에서 뷰를 분리해낼 수 있다.

## 구현 로직
요구사항 : 6개의 강아지 사진을 fetch 해서 화면에 보여주는 페이지 만들기
1. 어떤 데이터가 보여질 지에 대해 **데이터를 다루는 컴포넌트** `Container Components` 를 만든다.
 - 요구사항의 강아지 사진들을 다운로드(fetch) 하는 컴포넌트

2. 데이터가 어떻게 사용자에게 보여질 지에 대해서만 다루는 컴포넌트  ` Presentational Components` 를 만든다.
  - 강아지 사진의 목록을 렌더링하는 컴포넌트

3. `Container Components` 에서 `Presentational Components` 로 데이터를 넘겨준다.
  - props 로 넘겨줄 수도 있고, 여러 Presentational 로 중첩되어 있는 경우 Context Api 등으로 넘겨줄 수도 있다.


## 완성본
https://codesandbox.io/p/sandbox/jy5wfh

## Hooks Patterns 로 리팩토링
대개 Container/Presentational 패턴은 React Hooks로 대체 가능하다. React 에 Hooks가 추가되면서 Container 컴포넌트 없이도 stateless 컴포넌트를 쉽게 만들 수 있게 되었다.

DogImagesContainer 컴포넌트를 사용하는 대신 Presentational 컴포넌트인 DogImages 에서 useDogImages 훅을 직접 호출해 사용하면 된다.

https://codesandbox.io/p/sandbox/jy5wfh

## 결론
처음 이 패턴을 소개한 [Dan Abramov는 2019년 기준으로 이 패턴을 사용하지 말라고 언급하고 있다.](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

> 2019년 업데이트 : 이 아티클을 예전에 썼었고 이제는 내 관점이 달라졌다. 특히 나는 더 이상 이런 방식으로 component를 나누는 것을 추천하지 않는다.
만약 자연스럽게 이 패턴의 필요성을 찾는다면 이 패턴은 유용할 것이다. 하지만 어떤 필요성 없이, 독단적으로 이 패턴이 강제되고 있음을 여러번 보았다. 내가 이 패턴을 유용하게 보았던 주된 이유는 이 패턴이 다른 관점의 컴포넌트로부터 복잡하고 stateful한 로직을 분리하게 해주었기 때문이다. 임의의 분리없이 Hook은 똑같은 일을 할 수 있다. 이 글은 역사적인 이유로 보길 바라며 심각하게 받아들이지 마라.

Container/Presentational 패턴을 사용하게 되면 container 로 인한 depth 가 한 층 더 생기는 단점이 있기에, 대부분은 hooks 으로 충분히 대체 가능한 패턴이다.

나는 
- 굳이 별도의 module 로 분리하고 싶지 않고, 하나의 파일 안에서 로직과 뷰를 모두 보고 싶을 때
- 각 컴포넌트마다 hooks 를 만드는 것이 오버 엔지니어링이라고 생각되는 경우,

하나의 모듈 에서 1 뎁스 정도의 props 전달은 괜찮다고 생각하여 container presentational 패턴으로 종종 사용했었다.

```tsx
import React from "react";

interface Props {
  dogs: string[];
  putDogImage: () => void;
  deleteDogImage: (id: string) => void;
}

function DogImages({ dogs, putDogImage, deleteDogImage }: Props) {
  return (
    <div>
    <button onClick={putDogImage}>추가</button>
    <button onClick={deleteDogImage}>삭제</button>
      {dogs.map((dog, i) => (
        <img src={dog} key={i} alt="Dog" />
      ))}
    </div>
  );
}


export default function DogImagesContainer() {
  const [dogs, setDogs] = useState<string[]>([]);

  const putDogImage = () => {
    // putDogs 로직
  };

  const deleteDogImage = (id: string) => {
    // deleteDogs 로직
  };

  useEffect(() => {
    const getDogs = async () => {
      const response = await fetch(
        "https://dog.ceo/api/breed/labrador/images/random/6"
      );
      const { message } = await response.json();
      setDogs(message);
    };
    getDogs();
  }, []);

  return <DogImages dogs={dogs} putDogImage={putDogImage} deleteDogImage={deleteDogImage}/>;
}

```

과거의 패턴에서 얻을 수 있는 충분한 인사이트가 있고, 무조건 특정 패턴을 사용 해야할/안 할 이유는 없다고 생각한다.
따라서 본인이 생각하기에 충분히 납득할 근거가 있다면 (ex- 나는 크게 복잡하지 않은 경우 하나의 모듈 안에서 뷰/로직을 모두 볼 수 있는 코드가 가독성이 좋다고 생각해서) 상황에 맞게 사용하면 되지 않을까? 라고 생각한다.

항상 정답은 없으니 패턴을 맹목적으로 따르는 것을 경계하고 좀 더 나은 방법이 있다면 변형시켜 사용하는 자세를 가지도록 하자.



-----
참고 : 
- https://patterns-dev-kr.github.io/design-patterns/container-presentational-pattern/
- https://tecoble.techcourse.co.kr/post/2021-04-26-presentational-and-container/