# 객체지향 설계
- 올바른 객체에게 올바른 책임을 할당하면서 낮은 결합도와 높은 응집도를 가진 구조를 창조하는 활동
- 객체지향 설계의 핵심은 책임이다.
- 책임을 할당하는 작업이 응집도와 결합도 같은 설계 품질과 깊이 연관돼 있다.
- 객체를 단순한 데이터의 집합으로 바라보는 시각은 객체의 내부 구현을 퍼블릭 인터페이스에 노출시킨다.

# 01 데이터 중심의 영화 예매 시스템
- 객체의 상태는 구현에 속한다.
- 구현은 불안정하기 때문에 변하기 쉽다.
- 데이터에 초점을 맞추는 설계는 변경에 취약할 수밖에 없다.
- 객체의 책임은 인터페이스에 속한다.
- 캡슐화를 만드는 가장 간단한 방법은 내부의 데이터를 반환하는 접근자와 데이터를 변경하는 수정자를 추가하는 것이다.
```java
package org.eternity.movie.step01;

import org.eternity.money.Money;

public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer,
                               int audienceCount) {
        Movie movie = screening.getMovie();

        boolean discountable = false;
        for(DiscountCondition condition : movie.getDiscountConditions()) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                        condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                        condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
            } else {
                discountable = condition.getSequence() == screening.getSequence();
            }

            if (discountable) {
                break;
            }
        }

        Money fee;
        if (discountable) {
            Money discountAmount = Money.ZERO;
            switch(movie.getMovieType()) {
                case AMOUNT_DISCOUNT:
                    discountAmount = movie.getDiscountAmount();
                    break;
                case PERCENT_DISCOUNT:
                    discountAmount = movie.getFee().times(movie.getDiscountPercent());
                    break;
                case NONE_DISCOUNT:
                    discountAmount = Money.ZERO;
                    break;
            }

            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        } else {
            fee = movie.getFee().times(audienceCount);
        }

        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```
- 책임이 몰려있음 (할인 조건 검사, 할인 정책 계산, 가격 계산)
- Movie 내부 정보를 직접 사용 (Movie가 바뀔 시 ReservationAgency도 수정해야한다.)
  <image src="image.png" style="width:70%">
- switch 사용 (OCP 위반)

# 02 응집도와 결합도
- 캡슐화를 지키면 모듈 안의 응집도는 높아지고 모듈 사이의 결합도는 낮아진다.
## 응집도
- 변경이 발생할 때 모듈 내부에서 발생하는 변경의 정도
- 하나의 변경을 수용하기 위해 모듈 전체가 함께 변경된다면 응집도가 높은 것이고 모듈의 일부만 변경된다면 응집도가 낮은 것이다.
- 하나의 변경에 대해 하나의 모듈만 변경된다면 응집도가 높지만 다수의 모듈이 함께 변경돼야 한다면 응집도가 낮은 것이다.
## 결합도
- 한 모둘이 변경되기 위해서 다른 모듈의 변경을 요구하는 정도
- 하나의 모듈을 수정할 때 얼마나 많은 모듈을 함께 수정해야 하는지
### 결합도가 높아도 상관 없는 경우
- 일반적으로 변경될 확률이 매우 적은 안정적인 모듈에 의존하는 것은 아무런 문제가 되지 않는다.
- 표준 라이브러리에 포함된 모듈이나 성숙 단계에 접어든 프레임 워크에 의존하는 경우
- ex) java : String, ArrayList

# 03 데이터 중심의 영화 예매 시스템의 문제점
## 캡슐화 위반
```java
public class Movie {
    public Money getFee() {
        return fee;
    }

    public void setFee(Money fee) {
        this.fee = fee;
    }
}
```
- getFee 메서드와 setFee 메서드는 Movie 내부에 Money 타입의 fee라는 이름의 인스턴스 변수가 존재한다는 사실을 퍼블릭 인터페이스에 노골적으로 드러낸다.
- 만약 fee의 타입을 ㅂㄴ경한다고 가정하면 getFee 메서드의 반환타입, getFee 메서드를 호출하는 ReservationAgency의 구현을 변경된 타입에 맞게 함께 수정해야 한다.
### 추측에 의한 설계 전략
- 접근자와 수정자에 과도하게 의존하는 설계 방식
- 이 전략은 객체가 사용될 협력을 고려하지 않고 객체가 다양한 상황에서 사용될 수 있을 것이라는 막연한 추측을 기반으로 설계를 진행한다.
## 높은 결합도
  <image src="image4_4.png" style="width:70%">
  - ResevationAgency는 모든 의존성이 모이는 결합도의 집결자다.
  - 시스템의 어떤 변경도 ReservationAgency의 변경을 유발한다.

## 낮은 응집도
### ReservationAgency의 코드를 수정해야 하는 경우
- 할인 정책이 추가될 경우
- 할인 정책별로 할인 요금을 계산하는 방법이 변경될 경우
- 할인 조건이 추가되는 경우
- 할인 조건별로 할인 여부를 판단하는 방법이 변경될 경우
- 예매 요금을 계산하는 방법이 변경될 경우
# 04 자율적인 객체를 향해
```java
boolean discountable = false;
for(DiscountCondition condition : movie.getDiscountConditions()) {
    if (condition.getType() == DiscountConditionType.PERIOD) {
        discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
    } else {
        discountable = condition.getSequence() == screening.getSequence();
    }

    if (discountable) {
        break;
    }
}
```
```java
public class DiscountCondition {
    private DiscountConditionType type;

    private int sequence;

    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public DiscountCondition(int sequence){
        this.type = DiscountConditionType.SEQUENCE;
        this.sequence = sequence;
    }

    public DiscountCondition(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime){
        this.type = DiscountConditionType.PERIOD;
        this.dayOfWeek= dayOfWeek;
        this.startTime = startTime;
        this.endTime = endTime;
    }

    public DiscountConditionType getType() {
        return type;
    }

    public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime time) {
        if (type != DiscountConditionType.PERIOD) {
            throw new IllegalArgumentException();
        }

        return this.dayOfWeek.equals(dayOfWeek) &&
                this.startTime.compareTo(time) <= 0 &&
                this.endTime.compareTo(time) >= 0;
    }

    public boolean isDiscountable(int sequence) {
        if (type != DiscountConditionType.SEQUENCE) {
            throw new IllegalArgumentException();
        }

        return this.sequence == sequence;
    }
}
```
- 할인 조건 판단 로직이 ReservationAgency에서 DiscountCondition으로 이동하면서 데이터와 행위가 한 객체 안에 모였고 DiscountCondition의 응집도가 높아졌다.
```java
Money fee;
if (discountable) {
    Money discountAmount = Money.ZERO;
    switch(movie.getMovieType()) {
        case AMOUNT_DISCOUNT:
            discountAmount = movie.getDiscountAmount();
            break;
        case PERCENT_DISCOUNT:
            discountAmount = movie.getFee().times(movie.getDiscountPercent());
            break;
        case NONE_DISCOUNT:
            discountAmount = Money.ZERO;
            break;
    }

    fee = movie.getFee().minus(discountAmount).times(audienceCount);
} else {
    fee = movie.getFee().times(audienceCount);
}
```
```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        Money fee = screening.calculateFee(audienceCount);
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```
- 기존에는 ReservationAgency가 할인 정책과 금액 계산 로직을 직접 처리했지만, 변경 후에는 Screening에게 요금 계산 책임을 위임하도록 바뀌었다.
  <image src="image4_5.png" style="width:70%">
  
