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

