<img width="1087" height="362" alt="image" src="https://github.com/user-attachments/assets/2f43932b-1685-4038-bfa5-e20139e92e9a" /># 01 책임 주도 설계를 향해
## 데이터보다 행동을 먼저 결정하라
## 협력이라는 문맥 안에서 책임을 결정하라
- 메시지가 클라이언트의 의도를 표현한다는 사실을 주목하라.
- 객체를 결정하기 전에 객체가 수신할 메시지를 먼저 결정한다는 점을 주목하라.
- 메시지를 수신하기로 결정된 객체는 메시지를 처리할 "책임"을 할당받게 된다.

# 02 책임 할당을 위한 GRASP 패턴
- General Responsibility Assignment Software Pattern
- 일반적인 책임 할당을 위한 소프트웨어 패턴

## 도메인 개념에서 출발하기
<img width="1120" height="271" alt="image" src="https://github.com/user-attachments/assets/8eca7680-805f-488c-8459-2558f2e9169a" />

- 하나의 영화는 여러 번 상영될 수 있으며, 하나의 상영은 여러 번 예약될 수 있다.
- 영화는 다수의 할인 조건을 가질 수 있으며 할인 조건에는 순번 조건과 기간 조건이 존재한다.
- 할인 조건은 순번 조건과 기간 조건으로 분류되고 영화는 금액이나 비율에 따라 할인될 수 있지만 동시에 두 가지 할인 정책을 적용할 수 없다.
## INFORMATION EXPERT(정보 전문가) 패턴
- 객체에게 책임을 할당하는 첫 번째 원칙은 책임을 수행할 정보를 알고 있는 객체에게 책임을 할당하는 것
- 책임을 수행하는 객체가 정보를 '알고'있다고 해서 그 정보를 '저장'하고 있을 필요는 없다.
- 만약 스스로 처리할 수 없는 작업이 있다면 외부에 도움을 요청해야 한다. 이 요청이 외부로 전송해야하는 새로운 메시지가 되고, 최종적으로 이 메시지가 새로운 객체에게 책임으로 할당된다.
- 연쇄적인 메시지 전송과 수신을 통해 협력 공동체가 구성이 된다.
### 메시지를 전송할 객체는 무엇을 원하는가?
#### 1. 영화를 예매하라
#### 2. 가격을 계산하라
#### 3. 할인
### 메시지를 수신할 적합한 객체는 누구인가?
#### 1. '상영'
#### 2. '영화'
#### 3. '할인 조건'

## LOW COUPLING(낮은 결합도) 패턴
- Movie와 DiscountCondition은 이미 결합돼 있기 때문에 Movie를 DiscountCondition과 협력하게 하면 설계 전체적으로 결합도를 추가하지 않고도 협력을 완성할 수 있다.
- Screening이 DiscountCondition과 협력할 경우 Screening과 DiscountCondition 사이에 새로운 결합도가 추가된다.
- LOW COUPLING 패턴의 관점에서는 Screening의 DiscountCondition과 협력하는 것보다는 Movie가 DiscountCondition과 협력하는 것이 더 나은 설계 대안이다.

## HIGH COHESION(높은 응집도) 패턴
- Screening과 DiscountCondition과 협력하게 되면 Screening은 서로 다른 이유로 변경되는 책임을 짊어지게 되므로 응집도가 낮아진다.
- Movie의 주된 책임은 영화 요금을 계산하는 것으로 Movie와 DiscountCondition과 협력하는 것은 응집도에 아무런 해도 끼치지 않는다.
- HIGH COHESION 패턴 관점에서는 Movie가 DiscountCondition과 협력하는 것이 더 나은 설계 대안이다.

## CREATOR(창조자) 패턴
- 어떤 방식으로든 생성되는 객체와 연결되거나 관련될 필요가 있는 객체에 해당 객체를 생성할 책임을 맡기는 것이다.
- 생성될 객체에 대해 잘 알고 있어야 하거나 그 객체를 사용해야 하는 객체는 어떤 방식으로든 생성될 객체와 연결될 것이다.
- 이미 결합돼 있는 객체에게 생성 책임을 할당하는 것은 설계의 전체적인 결합도에 영향을 미치지 않는다.
- B가 A 객체를 포함하거나 참조한다.
- B가 A 객체를 기록한다.
- B가 A 객체를 긴밀하게 사용한다.
- B가 A 객체를 초기화하는 데 필요한 데이터를 가지고 있다(이 경우 B는 A에 대한 정보 전문가다)
<img width="1087" height="362" alt="image" src="https://github.com/user-attachments/assets/02bc5369-ef94-454f-a0d5-09fca8ce212f" />

- Screening은 예매 정보를 생성하는 데 필요한 영화, 상영 시간, 상영 순번 등의 정보에 대한 전문가이며, 예매 요금을 계산하는 데 필수적인 Movie를 알고 있다.
- Screening을 Reservation의 CREATOR로 선택하는 것이 적절하다.
