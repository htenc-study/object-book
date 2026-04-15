# 01 의존성 이해하기
## 변경과 의존성
- 실행 시점 : 의존하는 객체가 정상적으로 동작하기 위해서는 실행 시에 의존 대상 객체가 반드시 존재해야 한다.
- 구현 시점 : 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경된다.
- 의존성은 방향성을 가지며 항상 단방향이다.

## 의존성 정의
  <img width="1280" height="163" alt="image" src="https://github.com/user-attachments/assets/9775de62-91c6-4563-9bdd-2d1d81bfc5e1" />
- 의존성이 실제 전의될지 여부는 변경의 방향과 캡슐화의 정도에 따라 달라진다.
### 의존성의 종류
- 직접 의존성 : 한 요소가 다른 요소에 직접 의존하는 경우
- 간접 의존성 : 직접적인 관계는 존재하지 않지만 의존성 전이에 의해 영향이 전파되는 경우를 가르킨다.

## 런타임 의존성과 컴파일타임 의존성
### 런타임 의존성
- 애플리케이션이 실행되는 시점
- 런타임의 주인공은 객체이다.
<img width="1280" height="249" alt="image" src="https://github.com/user-attachments/assets/468aca8c-a8ff-41f2-95e6-9a44e94461b6" />
- 코드를 작성하는 시점의 Movie 클래스는 AmountDiscountPolicy 클래스와 PercentDiscountPolicy 클래스의 존재에 대해 전혀 알지 못하지만 실행시점의 Movie 인스턴스는 AmountDiscountPolicy 인스턴스와 PercentDiscountPolicy 인스턴스와 협력할 수 있어야 한다.

### 컴파일타임 의존성
- 컴파일타임 의존성이 중요하게 생각하는 것은 시간이 아니라 우리가 작성한 코드의 구조이다.
  <img width="1280" height="249" alt="image" src="https://github.com/user-attachments/assets/2fc3131e-690f-4485-86d8-a12dd168e54d" />
- Movie 클래스에서 AmountDiscountPolicy 클래스와 PercentDiscountPolicy 클래스로 향하는 어떤 의존성도 존재하지 않는다.

### 컴파일타임 구조와 런타임 구조 사이의 거리가 멀면 멀수록 설계가 유연해지고 재사용 가능해진다.

## 컨텍스트 독립성
- 클래스가 특정한 문맥에 강하게 결합될수록 다른 문맥에서 사용하기는 더 어려워진다.

## 의존성 해결하기
- 컴파일타임 의존성을 실행 컨텍스트에 맞는 적절한 런타임 의존성으로 교체하는 것
### 의존성 해결하는 방법
- 객체를 생성하는 시점에 생성자를 통해 의존성 해결
- 객체 생성 후 setter 메서드를 통해 의존성 해결
```java
// 생성자 방식과 setter방식을 혼합
Movie avatar = new Movie(..., new PercentDiscount(...)));
...
avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
```
- 메서드 실행 시 인자를 이용해 의존성 해결
```java
public class Movie {
  public Money calculateMovieFee(Screening screening, DiscountPolicy discountPolicy) {
    return fee.minus(discountPolicy.calculateDiscountAmount(screening));
  }
}
```
# 02 유연한 설계
## 의존성과 결합도
- 의존성은 재사용성과 관련이 있다.
- 두 요소 사이에 존재하는 의존성이 바람직할 때 두 요소가 느슨한 결합도 또는 약한 결합도를 가진다고 말한다.
- 두 요소 사이이 의존성이 바람직하지 못할 때 단단한 결합도 또는 강한 결합도를 가진다고 말한다.

## 추상화에 의존하라
- 결합도 : 구체 클래스 의존성 > 추상 클래스 의존성 > 인터페이스 의존성
- 인터페이스에 의존하면 상속 계층을 모르더라도 협력이 가능해진다.

## 명시적인 의존성
```java
public class Movie {
  ...
  private DiscountPolicy discountPolicy;

  public Movie(String title, Duration runningTime, Money fee) {
    ...
    this.discountPolicy = new AmountDiscountPolicy(...);
  }
}
```
<img width="1902" height="583" alt="image" src="https://github.com/user-attachments/assets/7b7276ca-b377-4d26-b307-3e4a64995ce3" />

- Movie는 추상 클래스인 DiscountPolicy뿐만 아니라 구체 클래스인 AmountDiscountPolicy에도 의존하게 된다.
```java
public class Movie {
  ...
  private DiscountPolicy discountPolicy'

  public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
    ...
    this.discountPolicy = discountPolicy;
  }
}
```
- 생성자의 인자가 추상 클래스 타입으로 선언됐기 때문에 이제 객체를 생성할 때 생성자의 인자로 DiscountPolicy의 자식 클래스 중 어떤 것이라도 전달할 수 있다.
- 의존성의 대상을 생성자의 인자로 전달받는 방법과 생성자 안에서 직접 생성하는 방법 사이의 가장 큰 차이점은 퍼블릭 인터페이스를 통해 할인 정책을 설정할 수 있는 방법을 제공하는지 여부이다.
- 명시적인 의존성 : 모든 경우에 의존성은 명시적으로 퍼블릭 인터페이스에 노출된다.
- 숨겨진 의존성 : 의존성이 퍼블릭 인터페이스에 표현되지 않는다.
- 의존성이 명시적이지 않으면 의존성을 파악하기 위해 내부 구현을 직접 살펴볼 수밖에 없고 클래스를 다른 컨텍스트에서 재사용하기 위해 내부 구현을 직접 변경해야 한다.
