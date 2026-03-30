# 01 협력과 메시지
## 클라이언트-서버 모델
- 협력안에서 메시지를 전송하는 객체를 클라이언트, 메시지를 수신하는 객체를 서버라고 한다.
- 협력은 클라이언트가 서버의 서비스를 요청하는 단방향 상호작용이다.
<img width="1513" height="485" alt="image" src="https://github.com/user-attachments/assets/30ac1466-dbc0-4432-91be-8fcdeb2c44d4" />

- 객체는 협력에 참여하는 동안 클라이언트와 서버의 역할을 동시에 수행하는 것이 일반적이다.
- 협력의 관점에서 객체는 수신하는 메시지의 집합이고 외부의 객체에게 전송하는 메시지의 집합이다.

## 메시지
- 메시지 전송은 메시지 수신자, 오퍼레이션명(operation name)과 인자(argument)의 조합이다.
- 메시지가 수신했을 때 실제로 실행되는 함수 또는 프로시저를 메서드라고 부른다.
- 코드 상에서 동일한 이름의 변수에게 동일한 메시지를 전송하더라도 객체의 타입에 따라 실행되는 메서드가 달라질 수 있다는 것이다.
- 메시지 전송자는 자신이 어떤 메시지를 전송해야 하는지만 알면 된다.
- 메시지 수신자 역시 누가 메시지를 전송하는지 알 필요가 없다.
- 실행 시점에 메시지와 메서드를 바인딩하는 메커니즘은 두 객체 사이의 결합도를 낮춤으로써 유연하고 확장 가능한 코드를 작성할 수 있게 만든다.

## 퍼블릭 인터페이스와 오퍼레이션
<img width="2162" height="649" alt="image" src="https://github.com/user-attachments/assets/0d84bae4-042b-4894-b911-1197312439be" />

- 퍼블릭 인터페이스 : 객체가 의사소통을 위해 외부에 공개하는 메시지의 집합
- 오퍼레이션 : 퍼블릭 인터페이스에 포함된 메시지
- 메서드 : 메시지를 수신했을 때 실제로 실행되는 코드
- 시그니처 : 오퍼레이션의 이름과 파라미터 목록을 합친 것

# 02 인터페이스와 설계 품질
## 디미터 법칙
- 객체의 내부 구조에 강하게 결합되지 않도록 협력 경로를 제한하는 것
- 낯선 자에게 말하지 말라
- 오직 인접한 이웃하고만 말하라
- 오직 하나의 도트만 사용하라
### 디미터 법칙을 위반하는 코드
```java
screening.getMovie().getDiscountConditions();
```
- 기차 충돌 : 클래스의 내부 구현이 외부로 노출됐을 때 나타나는 전형적인 형태로 메시지 전송자는 메시지 수신자의 내부 정보를 자세히 알게 된다.

## 의도를 드러내는 인터페이스
### 메서드가 작업을 '어떻게' 수행하는지
```java
public class PeriodCondition {
  public boolean isSatisfiedByPeriod(Screening screening) { ... } 
}

public class SequenceCondition {
  public boolean isSatisfiedBySequence(Screening screening) { ... }
}
```
- 메서드에 대해 제대로 커뮤니케이션하지 못한다. 두 메서드의 내부 구현을 정확하게 이해하지 못한다면 두 메서드가 동일한 작업을 수행한다는 사실을 알아채기 어렵다.
- 메서드 수준에서 캡슐화를 위반한다는 것이다. 이 메서드들은 클라이언트로 하여금 협력하는 객체의 종류를 알도록 강요한다.
- 조건 타입에 따라 분기문이 필요하다.
### '무엇'을 하는지
```java
public class PeriodCondition implements DiscountCondition {
  public boolean isSatisfiedBy(Screening screening) { ... }
}

public class SequenceCondition implements DiscountCondition {
  public boolean isSatisfiedBy (Screening screening) { ... }
}
```
- PeriodCondition의 isSatisfiedBy 메서드와 SequenceCondition의 isSatisfiedBy 메서드가 동일한 목적을 가진다는 것을 메서드의 이름을 통해 명확하게 표현한다.
- 클라이언트 입장에서 두 메서드는 동일한 메시지를 서로 다른 방법으로 처리하기 때문에 서로 대체 가능하다.

### 의도를 드러내는 선택자
- 어떻게 하느냐가 아니라 무엇을 하느냐에 따라 메서드의 이름을 짓는 패턴

### 의도를 드러내는 인터페이스
- 수행 방법에 관해서는 언급하지말고 결과와 목적만을 포함하도록 클래스와 오퍼레이션의 이름을 부여하라.
- 방법이 아닌 의도를 표현하는 추상적인 인터페이스 뒤로 모든 까다로운 메커니즘을 캡슐화해야 한다.

## 함께 모으기
### 디미터 법칙
```java
screening.getWhenScreened.getDayofWeek()
```
- 객체 내부 구조를 따라 깊게 접근
```java
screening.calculateFee(audienceCount)
```
### 묻지 말고 시켜라
```java
if (condition.getType() == PERIOD) {

}
```
- 데이터 꺼내서 외부에서 로직 수행
```java
condition.isDiscountable(...)
movie.isDiscountable(...)
screening.calculateFee(...)
```
- Movie → DiscountCondition에게 시킴
- Screening → Movie에게 시킴
- ReservationAgency → Screening에게 시킴
### 의도를 드러내는 인터페이스
```java
getFee()
getDiscountPercent()
getSequence()
```
- 데이터 중심, 조회, 구조 노출
```java
calculateFee()
isDiscountable()
calculateAmountDiscountedFee()
```
- 행동 중심, 요청, 의도 표현

# 03 원칙의 함정
- 원칙이 현재 상황에 부적합하다고 판단된다면 과감하게 원칙을 무시하라.
- 소프트웨어 설계에 법칙이란 존재하지 않는다.
- 경우에 따라 다르다.
## 디미터 법칙은 하나의 도트(.)를 강제하는 규칙이 아니다
```java
IntStream.of(1, 15, 20, 3, 9).filter(x -> x > 10).discount().count();
```
- 위 코드에서 of, filter, distinct 메서드는 모두 IntStream이라는 동일한 클래스의 인스턴스를 반환한다.
- 디미터 법칙은 결합도와 관련된 것이며, 이 결합도가 문제가 되는것은 객체의 내부 구조가 외부로 노출되는 경우로 한정된다.
- 하나 이상의 도트(.)를 사용하는 모든 케이스가 디미터 법친 위반인 것은 아니다.
## 결합도와 응집도의 충돌
```java
public class PeriodCondition implements DiscountCondition {

    public boolean isSatisfiedBy(Screening screening) {
        return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
                startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0 &&
                endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
    }
}
```
- 얼핏 보기에는 Screening의 내부 상태를 가져와서 사용하기 때문에 캡슐화를 위반한 것으로 보일 수 있다.
```java
public class Screening {

    public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime) {
        return whenScreened.getDayOfWeek().equals(dayOfWeek) &&
                startTime.compareTo(whenScreened.toLocalTime()) <= 0 &&
                endTime.compareTo(whenScreened.toLocalTime()) >= 0;
    }
}

public class PeriodCondition implements DiscountCondition {

    public boolean isSatisfiedBy(Screening screening) {
        return screening.isDiscountable(dayOfWeek, startTime, endTime);
    }
}
```
- Screening이 기간에 따른 할인 조건을 판단하는 책임을 떠안게 되고 응집도가 낮아진다.
- Screening이 PeriodCondition의 인스턴스 변수를 인자로 받기 때문에 두 객체 사이의 결합도를 높인다.
- Screening의 캡슐화를 향상시키는 것보다 Screening의 응집도를 높이고 Screening과 PeriodCondition 사이의 결합도를 낮추는 것이 전체적인 관점에서 더 좋은 방법이다.

# 04 명령-쿼리 분리 원칙
- 루틴(routine) : 어떤 절차를 묶어 호출 가능하도록 이름을 부여한 기능 모듈
## 프로시저(procedure)
- 정해진 절차에 따라 내부의 상태를 변경하는 루틴
- 부수효과를 발생시킬 수 있지만 값을 반환할 수 없다.
## 함수(function)
- 어떤 절차에 따라 필요한 값을 계산해서 반환하는 루틴
- 값을 반환할 수 있지만 부수효과를 발생시킬 수 없다.

## 명령(Command)
- 객체의 상태를 수정하는 오퍼레이션
- 반환값을 가질 수 없다.
## 쿼리(Query)
- 객체와 관련된 정보를 반환하는 오퍼레이션
- 상태를 변경할 수 없다.

## 반복 일정의 명령과 쿼리 분리하기
```java
public class Event {
    private String subject;
    private LocalDateTime from;
    private Duration duration;

    public Event(String subject, LocalDateTime from, Duration duration) {
        this.subject = subject;
        this.from = from;
        this.duration = duration;
    }

    public boolean isSatisfied(RecurringSchedule schedule) {
        if (from.getDayOfWeek() != schedule.getDayOfWeek() ||
                !from.toLocalTime().equals(schedule.getFrom()) ||
                !duration.equals(schedule.getDuration())) {
            reschedule(schedule);
            return false;
        }

        return true;
    }

    private void reschedule(RecurringSchedule schedule) {
        from = LocalDateTime.of(from.toLocalDate().plusDays(daysDistance(schedule)),
                schedule.getFrom());
        duration = schedule.getDuration();
    }

    private long daysDistance(RecurringSchedule schedule) {
        return schedule.getDayOfWeek().getValue() - from.getDayOfWeek().getValue();
    }
}
```
- reschedule 메서드는 Event 일정을 인자로 전달된 RecurringSchedule의 조건에 맞게 변경한 후 false를 반환한다. 
- 버그를 찾기 어려운 이유는 isSatisfied가 명령과 쿼리의 두 가지 역할을 동시에 수행하고 있었기 때문이다.

## 명령-쿼리 분리와 참조투명성
- 참조 투명성 : 어떤 표현식 e가 있을 때  e의 값으로 e가 나타나는 모든 위치를 교체하더라도 결과가 달라지지 않는 특성
- 불변성 : 어떤 값이 변하지 않는 성질. 부수효과가 발생하지 않는다.
### 참조 투명성을 만족하는 식의 장점
- 모든 함수를 이미 알고 있는 하나의 결과값으로 대체할 수 있기 때문에 식을 쉽게 계산할 수 있다.
- 모든곳에서 함수의 결과값이 동일하기 때문에 식의 순서를 변경하더라도 각 식의 결과는 달라지지 않는다.
