# [Observer 패턴](https://velog.io/@iberis/Observer-패턴)

## 1. Observer 패턴이란
Observer 패턴은 특정 객체의 상태 변화가 있을 때 이를 구독하고 있는 객체들에게 자동으로 알림을 전달하는 디자인 패턴입니다. Observable(혹은 Subject)은 특정 이벤트가 발생할 때 이를 Observer들에게 전파합니다.

주로 이벤트 기반 프로그래밍에서 사용되며, 예를 들어 블로그의 새 글 알림 시스템에서는 새로운 글이 등록될 때 모든 구독자에게 알림을 보내는 식으로 구현할 수 있습니다.

### 패턴의 흐름
Observer 패턴의 기본 구조는 **관찰 대상(Subject)** 과 여러 **관찰자(Observer)** 로 구성된 일대다 관계입니다.\
구체적인 흐름은 다음과 같습니다.

1. **구독 및 해지**: Observer는 언제든 Subject에 구독 또는 구독 해지를 할 수 있습니다.
2. **알림 전파**: Subject는 상태가 변경되면 등록된 모든 Observer에게 알림을 전송합니다.
3. **Observer 반응**: 알림을 받은 Observer는 변경된 정보를 바탕으로 적절히 대응합니다.

이러한 구조는 객체 간 결합도를 낮추어 코드의 유연성과 유지보수성을 높이는 데 유용합니다.

## 2. JavaScript에서의 Observer 패턴 구현
JavaScript에서는 Observer 패턴을 통해 이벤트 리스너를 추가하고, 이벤트 발생 시 동작을 수행하게 할 수 있습니다. 

간단한 예제를 통해 Observer 패턴을 구현해보겠습니다.

```ts
// 주체가 되는 Subject 클래스
class Subject {
  private observers: Function[];
  
  constructor(){
    this.observer = [];
  }

  // Observer 구독
  subscribe<T>(observer: (param: T) => void){
    this.observer.push(observer);
  }

  // Observer 구독 해제
  unsubscribe<T>(observer: (param: T) => void){
    this.observer = this.observer.filter(obs => obs !== observer);
  }

  // 알림 전송
  notify<T>(data: T){
    this.observer.forEach(observer => observer(data));
  }
}
```

subscribe 메서드를 통해 Observer를 등록하고, unsubscribe 메서드를 통해 Observer의 구독을 해지할 수 있습니다. notify 메서드를 호출하면 등록된 모든 Observer에게 이벤트가 전파됩니다.

```ts
// 사용 예시
const subject = new Subject();

const observer1 = (data: string) => { console.log(`Observer 1 received data: ${data}`) };
const observer2 = (data: string) => { console.log(`Observer 2 received data: ${data}`) };

subject.subscribe(observer1);
subject.subscribe(observer2);

// 알림 발생
subject.notify('hello'); 
/*
Observer 1 received data: hello
Observer 2 received data: hello
*/

// observer1을 구독 해제 후 알림 재전송
subject.unsubscribe(observer1);
subject.notify('second message'); 
/* Observer 2 received data: second message */
```

위 예제에서는 notify 메서드를 호출하면 등록된 Observer의 함수가 실행되어 Observer 1, Observer 2 모두 데이터 알림을 수신하게 됩니다.

## 3. Observer 패턴의 장점과 단점
### 장점
- **자동 상태 감지**: Observer는 Subject의 상태 변화를 주기적으로 확인할 필요 없이 자동으로 알림을 받습니다.
- **개방-폐쇄 원칙 준수**: 구독자 클래스 추가 시 Subject의 코드 변경 없이도 새 구독자를 추가할 수 있습니다.
- **느슨한 결합**: Subject와 Observer 간의 결합도를 낮춰 각 객체가 독립적으로 유지될 수 있습니다.

### 단점
- **알림 순서 제어 불가**: 구독자는 무작위 순서로 알림을 받을 수 있습니다. 이를 제어하는 것은 복잡도를 높일 수 있습니다.
- **코드 복잡도 증가**: Observer 패턴을 무분별하게 사용할 경우 코드가 복잡해질 수 있으며, 유지보수가 어려워질 수 있습니다.
- **메모리 누수 위험**: 많은 Observer 객체를 등록하고 해지하지 않는다면 메모리 누수가 발생할 수 있습니다.
- **성능 저하**: 많은 Observer가 있는 경우 이벤트가 빈번히 발생하면 성능 저하가 발생할 수 있습니다.

## 4. Observer 패턴의 실제 사용 사례
- 이벤트 시스템: 브라우저의 DOM 이벤트와 같이 이벤트가 발생할 때마다 다수의 이벤트 리스너가 반응하도록 하는 구조에 사용됩니다.
- 실시간 데이터 업데이트: 뉴스 피드, 채팅 시스템 등 실시간으로 데이터가 업데이트되는 애플리케이션에서 Observer 패턴을 사용하여 각 구독자에게 즉시 반영할 수 있습니다.

## 마무리
Observer 패턴은 이벤트 기반 애플리케이션에서 객체 간 결합도를 낮추면서 이벤트를 효율적으로 처리할 수 있는 강력한 패턴입니다. JavaScript의 객체 지향 기능을 활용하여 Observer 패턴을 쉽게 구현할 수 있으며, 이를 통해 코드의 재사용성과 유지보수성을 높일 수 있습니다.


---
참고 
[Design Patterns - Observer Pattern](https://patterns-dev-kr.github.io/design-patterns/observer-pattern/)
[Inpa 블로그 - 옵저버 패턴](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%98%B5%EC%A0%80%EB%B2%84Observer-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)