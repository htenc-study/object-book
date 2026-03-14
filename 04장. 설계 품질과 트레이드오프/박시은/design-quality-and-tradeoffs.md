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
