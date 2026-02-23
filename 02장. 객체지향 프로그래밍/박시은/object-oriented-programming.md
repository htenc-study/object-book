# 객체지향 프로그래밍을 향해
## 진정한 객체지향 패러다임으로의 전환
- 어떤 클래스가 필요한지를 고민하기 전에 어떤 객체들이 필요한지를 고민해라.
- 객체를 독립적인 존재가 아니라 기능을 구현하기 위해 협력하는 공동체의 일원으로 봐야 한다.
- 객체들의 모양과 윤곽이 잡히면 공통된 특성과 상태를 가진 객체들을 타입으로 분류하고 이 타입을 기반으로 클래스를 구현하라.

## 도메인
- 문제를 해결하기 위해 사용자가 프로그램을 사용하는 분야
- 클래스의 구조는 도메인의 구조와 유사한 형태를 띄어야 한다.
<img width="1313" height="330" alt="image" src="https://github.com/user-attachments/assets/5a407242-174b-4b6a-b466-90265e75559a" />

## 클래스 구현하기
```
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;

    public Screening(Movie movie, int sequence, LocalDateTime whenScreened) {
        this.movie = movie;
        this.sequence = sequence;
        this.whenScreened = whenScreened;
    }
}
```
- 인스턴스 변수의 가시성은 private이고 메서드의 가시성은 public이다.
- 클래스는 내부와 외부로 구분되며 훌륭한 클래스를 설계하기 위한 핵심은 어떤 부분을 외부에 공개하고 어떤 부분을 감출지를 결정하는 것이다.
### 클래스의 내부와 외부를 구분하는 이유
- 경계의 명확성이 객체의 자율성을 보장하기 때문이다.
- 프로그래머에게 구현의 자유를 제공하기 때문이다.
### 자율적인 객체
- 객체는 상태와 행동을 함께 가지는 복합적인 존재이다.
- 객체는 스스로 판단하고 행동하는 자율적인 존재이다.
- 데이터와 기능을 객체 내부로 함께 묶는 것을 캡슐화라고 한다.
- 접근 제어 : 외부에서 접근을 통제
- 객체의 상태는 숨기고 행동만 외부에 공개해야 한다.
- 접근 수정자 : public, protected, private
<img width="733" height="267" alt="image" src="https://github.com/user-attachments/assets/770c36a7-0e96-4545-9996-b1a6eda754e9" />

### 프로그래머의 자유
- 클래스 작성자 : 새로운 데이터 타입을 프로그램에 추가
- 클라이언트 프로그래머 : 클래스 작성자가 추가한 데이터 타입을 사용
### 구현 은닉
- 클라이언트 프로그래머는 내부의 구현은 무시한 채 인터페이스만 알고 있어도 클래스를 사용할 수 있기 때문에 머릿속에 담아둬야 하는 지식의 양을 줄일 수 있다.
- 클래스 작성자는 인터페이스를 바꾸지 않는 한 외부에 미치는 영향을 걱정하지 않고도 내부 구현을 마음대로 변경할 수 있다.

## 협력하는 객체들의 공동체
- 메시지를 전송 : 객체가 다른 객체와 상호작용할 수 있는 유일한 방법
- 메시지를 수신 : 객체에게 요청 도착
- 메서드 : 수신된 메시지를 처리하기 위한 자신만의 방법

# 할인 요금 구하기
## 할인 정책과 할인 조건
```
public abstract class DiscountPolicy {
    private List<DiscountCondition> conditions = new ArrayList<>();

    public DiscountPolicy(DiscountCondition ... conditions) {
        this.conditions = Arrays.asList(conditions);
    }

    public Money calculateDiscountAmount(Screening screening) {
        for(DiscountCondition each : conditions) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }

        return Money.ZERO;
    }

    abstract protected Money getDiscountAmount(Screening Screening);
}
```
- 부모 클래스인 DiscountPolicy 안에 중복 코드를 두고 AmountDiscountPolicy와 PercentDiscountPolicy가 이 클래스를 상속받게 한다.
- DiscountPolicy의 인스턴스를 생성할 필요가 없기 때문에 추상 클래스로 구현한다.
- DiscountPolicy는 할인 여부와 요금 계산에 필요한 전체적인 흐름은 정의하지만 실제로 요금을 계산하는 부분은 추상 메서드인 getDiscountAmount 메서드에게 위임한다.
- 실제로는 DiscountPolicy를 상속받은 클래스에서 오버라이딩한 메서드가 실행될 것이다.
### TEMPLATE METHOD 패턴
- 부모 클래스에 기본적인 알고리즘 흐름을 구현하고 중간에 필요한 처리를 자식 클래스에게 위임하는 디자인 패턴
<img width="1052" height="380" alt="image" src="https://github.com/user-attachments/assets/7862d0dc-a7ee-4169-83b6-f58b133c4370" />

### 오버라이딩과 오버로딩
- 오버라이딩 : 부모 클래스에 정의된 같은 이름, 같은 파라미터 목록을 가진 메서드를 자식 클래스에서 재정의하는 경우를 가리킨다.
- 오버로딩 : 메서드의 이름은 같지만 제공되는 파라미터 목록이 다르다.

# 상속과 다형성
## 컴파일 시간 의존성과 실행 시간 의존성
<img width="1016" height="408" alt="image" src="https://github.com/user-attachments/assets/bd93a984-0190-4e19-8b68-bf9bda57099a" />
- Movie클래스는 오직 추상 클래스인 DiscountPolicy에만 의존하고 있다.
- 코드의 의존성과 실행 시점의 의존성이 서로 다를 수 있다.
- 코드의 의존성과 실행 시점의 의존성이 다르면 다를수록 코드를 이해하기 어려워지는 반면에 코드는 더 유연해지고 확장 가능해진다.
- 클래스 사이의 의존성과 객체 사이의 의존성은 동일하지 않을 수 있다.

## 차이에 의한 프로그래밍
- 부모 클래스와 다른 부분만을 추가해서 새로운 클래스를 쉽고 빠르게 만드는 방법

## 상속과 인터페이스
### 상속
- 객체지향에서 코드를 재사용하기 위해 가정 널리 사용되는 방법
- 클래스 사이에 관계를 설정하는 것만으로 기존 클래스가 가지고 있는 모든 속성과 행동을 새로운 클래스로 포함시킬 수 있다.
- 부모 클래스의 구현은 공유하면서도 행동이 다른 자식 클래스를 쉽게 추가할 수 있다.
### 인터페이스
- 객체가 이해할 수 있는 메시지의 목록
- 상태를 가질 수 없음
### 업캐스팅
- 자식 클래스가 부모 클래스를 대신하는 것
- 컴파일러는 코드 상에서 부모 클래스가 나오는 모든 장소에서 자식 클래스를 사용하는 것을 허용한다.

## 다형성
- 동일한 메시지를 수신했을 때 객체의 타입에 따라 다르게 응답할 수 있는 능력
- 메시지를 응답하기 위해 실행될 메서드를 컴파일 시점이 아닌 실행 시점에 결정한다.
- 하나의 메시지를 선택적으로 서로 다른 메서드에 연결할 수 있는 이유는 바로 지연 바인딩 메커니즘을 사용하기 때문이다.

### 구현 상속과 인터페이스 상속
- 구현 상속 (=서브클래싱) : 코드를 재사용하기 위한 목적으로 상속을 사용하는 것
- 인터페이스 상속 (=서브타이핑) : 다형적인 협력을 위해 부모 클래스와 자식 클래스가 인터페이스를 공유할 수 있도록 상속을 이용하는 것

# 추상화와 유연성
<img width="1305" height="191" alt="image" src="https://github.com/user-attachments/assets/43ccc04a-421c-4545-bd4c-698ab30cf3f5" />
- 추상화의 계층만 따로 떼어 놓고 살펴보면 요구사항의 정책을 높은 수준에서 서술한다.
- 추상화를 이용하면 설계가 좀 더 유연해진다.
- 디자인 패턴이나 프레임워크 모두 추상화를 이용해 상위정책을 정의하는 객체지향의 메커니즘을 활용한다.

## 추상클래스와 인터페이스 트레이드오프
<img width="1961" height="627" alt="image" src="https://github.com/user-attachments/assets/b3a52cf2-807f-407c-86fb-0640439b00df" />
<img width="1090" height="492" alt="image" src="https://github.com/user-attachments/assets/d5b288e9-2e37-439b-9546-0de3a1e71ca9" />
- DiscountPolicy를 인터페이스로 바꾸고 NoneDiscountPolicy가 DiscountPolicy의 getDiscountAmount() 메서드가 아닌 calculateDiscountAmount() 오퍼레이션을 오버라이딩하도록 변경
- 구현과 모든 것들이 트레이드오프의 대상이 될 수 있다.

## 상속의 문제점
- 캡슐화를 위반한다.
- 설계를 유연하게 하지 못하게 만든다.
- 상속을 과도하게 사용한 코드는 변경하기도 어려워진다.
```
Movie avatar = new Movie("아바타",
    Duration.offMinutes(120),
    Money.wons(10000),
    new AmountDiscountPolicy(Money.wons(800),...));

avatar.changeDiscountPolicy(new PercentDiscountPolicy(0.1, ...));
```
- 상속보다 인스턴스 변수로 관계를 연결한 원래의 설계가 더 유연하다.

## 합성
- 다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함해서 재사용하는 방법
- 인터페이스에 정의된 메시지를 통해서만 코드를 재사용하는 방법
- 의존하는 인스턴스를 교체하는 것이 비교적 쉽기 때문에 설계를 느슨하게 만든다.






