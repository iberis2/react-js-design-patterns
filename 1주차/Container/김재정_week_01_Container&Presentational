## 1.  깔끔한 코드의 중요성

React에서 관심사의 분리(SoC) 를 강제하는 방법은 Container/Presentational Pattern을 이용하는 방법이 있다. 이 를 통해 비즈니스 로직에서 뷰를 분리해낼 수 있다. 

근데, 왜 그래야 할까?
프론트엔드 개발이 복잡해짐에 따라 깔끔한 코드를 유지하는것은 개발 생산성과 유지 보수성에 큰 영향을 미치게 된다. 특히나 FE의 경우 UI개발에 있어 수 많은 요소들이 얽혀있기 마련이다. (결국 코드가 복잡해지기 쉬움)

이를 해결하기 위한 핵심 원칙중 하나가 SOC 이다. 관심사 분리는 서로 다른 책임을 가진 코드들이 혼합되지 않도록 구분함으로써, 코드의 가독성과 유지보수성을 높이는 중요한 원칙이 된다.

만약 한 코드에 다 때려박는 코드를 작성한다면?

- **가독성 저하**: 비즈니스 로직과 UI 관련 코드가 섞이면 코드가 복잡해지고, 무엇이 어떤 역할을 하는지 명확하게 파악하기 어려워짐
- **유지보수성 악화**: 여러 기능들이 한 컴포넌트에 섞여 있으면, 특정 기능을 수정하거나 추가할 때 다른 부분에 의도치 않은 영향을 줄 수 있어 유지보수가 어려워짐
- **재사용성 감소**: 로직과 UI 코드가 분리되지 않으면 동일한 UI 요소를 다른 곳에서 재사용하기 어렵 모든 요소가 결합된 컴포넌트는 다른 상황에 맞게 쉽게 조정할 수 없기 때문

이러한 문제를 해결하기 위해 UI렌더링과 데이터 로직을 독립적으로 관리할 수 있고 더 작은 단위에 컴포넌트로 나눠 관리하여 유연하고 유지보수하기 쉬운 구조를 만들 수 있다 해당 사항은 비단 Container/Presentational Pattern에 국한되는 사항은 아님

## 2. Container와 Presentational 컴포넌트의 구체적 예시

Container/Presentational 패턴은 React와 같은 컴포넌트 기반 프레임워크에서 가장 기본적인 패턴이라고 할 수 있다.

단순히 컴포넌트의 역할을 명확히 나누어 **Container 컴포넌트**는 데이터 처리와 상태 관리를 담당하고, **Presentational 컴포넌트**는 UI를 렌더링하는 데 집중하게 한다. 

```jsx
//Presentational 컴포넌트

const UserList = ({ users }) => {
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};

export default UserList;
```

- Presentational 컴포넌트는 **오직 UI만을 렌더링**하는 역할을 함, 데이터가 어떻게 관리되는지에 대한 정보는 전혀 알지 못하며, 주로 props를 통해 전달받은 데이터를 화면에 표시하는 데 집중
- 나는 실무할때, 굉장히 많이 쓰이는 패턴임 해당과 같이 오직 UI를 렌더링 하는 역할의 component를 만들어 다른 상황에서도 쉽게 재사용 가능하게 만듦 (alert modal, 게시판 or 댓글 list …) 물론 영특한 Hooks이 있어 Presentational에서도 상태관리로직을 포함시키긴 한다.

```jsx
// Container 컴포넌트 (데이터 및 상태 관리)
import React, { useEffect, useState } from 'react';
import UserList from './UserList';

const UserListContainer = () => {
  const [users, setUsers] = useState([]);

  // 데이터 fetching 로직
  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await fetch('https://어쩌고 저쩌고');
        const data = await response.json();
        setUsers(data);
      } catch (error) {
        console.error("Failed to fetch users", error);
      }
    };
    fetchUsers();
  }, []);

  return <UserList users={users} />;
};

export default UserListContainer;
```

Container 컴포넌트는 데이터 로직과 상태 관리를 담당하며, 필요한 데이터를 가져와서 Presentational 컴포넌트에 전달해 주는 역할을 함.

API 호출, Redux 상태 관리, 또는 컴포넌트 자체 상태를 처리하는 로직이 이곳에 포함될 수 있음

- 상태 관리와 같은 복잡한 로직은 Container 컴포넌트에서 관리되므로, Presentational 컴포넌트는 단순화됩니다.

<aside>
🤔

나는 재사용이 불가능한 컴포넌트의 경우엔 두가지를 합쳐 작성하곤 하는데, 그렇게 하는 이유는 단순히 **복잡성**과 **확장성** 측면에서 이용가치가 없기 때문이다. 특정 페이지에서만 사용되는 UI와 상태 로직이 포함된 작은 컴포넌트는 나중에 재사용되지 않을 가능성이 크기 때문에, 불필요하게 Container와 Presentational로 분리하면 오히려 코드가 더 복잡해질 수 있다는 판단이 들었기도 했다.

디자인 패던에서 중요한 것은 코드의 복잡성을 줄이고, 필요할 때 패턴을 유연하게 적용하는 것이라고 한다. 패턴을 도구로서 활용하되, 상황에 맞는 최적의 방법을 찾는 것이 좋은 개발 습관이라고 하나, 일관성을 지키는 것도 중요하니 참 딜레마다.. ㅋ

</aside>

## 3. Container/Presentational 패턴의 한계와 트렌드

Container/Presentational 패턴은 사용하기 쉽고 명확한 soc가 가능하다는 점에서 큰 장점이 있지만 복잡한 어플리케이션 개발에 있어 한계가 있다 또, React Hooks와 Context API 같은 새로운 기술의 등장 이후 개발자들이 다른 패턴들을 선호하게 되는 경향이 있기도 하다

### 3-1. **복잡한 애플리케이션에서 발생할 수 있는 문제점**

- **지나친 분리로 인한 복잡성 증가**: 컴포넌트를 엄격하게 Container와 Presentational로 나누다 보면, 코드베이스가 지나치게 세분화되어 유지보수하기 어려워짐. 작은 컴포넌트조차 두 개의 파일로 나뉘어 관리된다면, 파일 간 이동이 잦아지고 구조가 복잡해질 수 있다.
- **관계가 복잡한 상태 관리**: 복잡한 상태를 다루는 애플리케이션에서는 상태 관리가 여러 컴포넌트 간에 공유되어야 할 때가 많다. 이 경우 Container 컴포넌트에 상태 관리 로직이 집중되면서 Container 컴포넌트 자체가 복잡해지고 비대해질 수 있는데, 이러한 상태 공유는 종종 Container 컴포넌트가 중첩되거나 서로 의존성이 강해지는 문제를 초래할 수 있다.
- **재사용성의 과대평가**: Presentational 컴포넌트를 재사용하기 위해 로직과 UI를 분리하는 것이 필수적이라고 생각할 수 있지만, 현실적으로 모든 컴포넌트가 재사용되지 않는 경우도 많다. 작은 컴포넌트까지 무조건 분리하려고 하면 오히려 코드 복잡도가 증가할 수 있다.

### 3-2. **React Hooks와 Context API 등장 이후의 변화**

`useState`, `useEffect`와 같은 React Hooks가 등장하면서, 함수형 컴포넌트에서도 상태 관리와 라이프사이클 로직을 쉽게 처리할 수 있게 되었다. 이로 인해 상태 관리 로직을 Container 컴포넌트에 집중시키지 않고, 개별 컴포넌트 내에서 처리할 수 있는 유연성이 커졌다.

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increase</button>
    </div>
  );
};

```

### **3-3. Context API의 사용 증가**

React의 **Context API**는 여러 컴포넌트 간에 상태를 쉽게 공유할 수 있는 방법을 제공한다. 이를 통해 글로벌 상태를 Container 컴포넌트가 아닌 Context를 통해 관리함으로써 Container 컴포넌트의 비대화를 방지할 수 있었다. 

Context API는 트리 구조에서 여러 컴포넌트 간의 데이터 전달을 간소화하기 때문에, Container 컴포넌트에 집중되었던 상태 관리를 더욱 효율적으로 분산시킬 수 있음!

### 3-4 .상태 관리 라이브러리 (Redux, MobX)와의 통합

단순한 상태 관리에서는 React Hooks와 Context API만으로도 충분하지만, **복잡한 전역 상태 관리**가 필요한 대규모 애플리케이션에서는 여전히 Redux, MobX 같은 전역 상태 관리 라이브러리가 중요한 역할을 한다. 이러한 라이브러리들은 Container/Presentational 패턴을 사용할 때나 Hook 기반 컴포넌트 구조에서 모두 효과적으로 통합될 수 있습니다.

### 결론

새로운 기술 트렌드의 등장과 함께 우리는 기존에 **Container/Presentational 패턴**처럼 흑과 백으로 역할을 나누는 방식이 항상 효율적인 것은 아니라는 것을 깨닫게 되었다. **React Hooks**, **Context API**, **Zustand**와 같은 새로운 도구들은 더 직관적이고 간결한 코드를 작성할 수 있는 가능성을 열어주었으며, 이제는 로직과 UI를 엄격하게 분리하기보다는 상황에 맞춰 유연하게 적용하는 것이 중요해진것을 깨달았다.

코드를 작성할 때는 단순히 패턴을 따르는 것이 정답이 아니라, **구조적 설계**, **생산성**, 그리고 **복잡성 관리**의 균형을 잘 잡아야 하며, 상황에 맞는 방향성을 설정하는 것이 성공적인 개발의 핵심인것 같다. **변화하는 기술 트렌드**에 맞춰 유연하고 현명한 선택을 하는 것이 앞으로 더 중요한 과제가 될 듯
