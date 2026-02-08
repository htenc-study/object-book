# 이론 대 실무
- 실무가 어느 정도 발전하고 난 다음에야 비로소 실무의 실용성을 입증할 수 있는 이론이 서서히 그 모습을 갖춰가기 시작하고, 해당 분야가 충분히 성숙해지는 시점에 이르러서야 이론이 실무를 추월하게 된다는 것이다.
- 실무는 훌륭한 소프트웨어를 설계하기 위해 필요한 다양한 기법과 도구를 초기부터 성공적으로 발전시켜 왔던 것이다.
- 대부분의 설계 원칙과 개념 역시 실무에서 반복적으로 적용되던 기법들을 이론화한 것들이 대부분이다.
- 추상적인 개념과 이론은 훌륭한 코드를 작성하는 데 필요한 도구이다.
- 개발자는 구체적인 코드를 만지며 손을 더럽힐 때 가장 많은것을 얻어가는 존재다.

# 01 티켓 판매 애플리케이션 구현하기
<img width="1414" height="769" alt="image" src="https://github.com/user-attachments/assets/625fb485-d0e9-4198-b33b-809a2b30ba9c" />

```
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

# 02 무엇이 문제인가
## 소프트웨어 모듈의 세 가지 목적
- 실행 중에 제대로 동작하는 것
- 변경을 위해 존재하는 것
- 코드를 읽는 사람과 의사소통하는 것
## 의존성과 결합도
- 의존성은 변경에 대한 영향을 암시한다.
- 객체 사이의 의존성이 과한 경우를 가리켜 결합도가 높다고 말한다.
- 두 객체 사이의 결합도가 높으면 높을수록 함께 변경될 확률도 높아지기 때문에 변경하기 어려워진다.
### 설계의 목표
- 애플리케이션의 기능을 구현하는 데 필요한 최소한의 의존성만 유지하고 불필요한 의존성을 제거하는 것.
- 객체 사이의 결합도를 낮춰 변경이 용이한 설계를 만드는 것.

# 03 설계 개선하기
<img width="1100" height="638" alt="image" src="https://github.com/user-attachments/assets/4363e8ea-dd66-4ce1-b57b-a5caef410189" />

```
package org.eternity.theater.step03;

public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        ticketSeller.sellTo(audience);
    }
}
```
- Theater은 TicketOffice에 접근하지 않는다.
- Theater은 오직 TicketSeller의 인터페이스에만 의존한다.
```
public class Bag {
    private Long amount;
    private Ticket ticket;
    private Invitation invitation;

    public Long hold(Ticket ticket) {
        if (hasInvitation()) {
            setTicket(ticket);
            return 0L;
        } else {
            setTicket(ticket);
            minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }

    private void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    private boolean hasInvitation() {
        return invitation != null;
    }

    private void minusAmount(Long amount) {
        this.amount -= amount;
    }
}
```
- Bag의 내부 상태에 접근하는 모든 로직을 Bag안으로 캡슐화해서 결합도를 낮춘다.
- 객체를 인터페이스와 구현으로 나누고 인터페이스만을 공개하는 것은 객체 사이의 결합도를 낮추고 변경하기 쉬운 코드를 작성하기 위해 따라야 하는 가장 기본적인 설계 원칙이다.
## 응집도
- 밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에 위임하는 객체를 가리켜 응집도가 높다고 말한다.
- 객체는 자신의 데이터를 스스로 처리하는 자율적인 존재여야 한다.
## 절차적 프로그래밍
- 프로세스와 데이터를 별도의 모듈에 위치시키는 방식
- 프로세스가 필요한 모든 데이터에 의존해야 한다는 근본적인 문제점 때문에 변경에 취약할 수밖에 없다.
## 객체지향 프로그래밍
- 데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍하는 방식
- 절차지향에 비해 변경에 좀 더 유연하다.
- 불필요한 세부사항을 캡슐화하는 자율적인 객체들이 낮은 결합도와 높은 응집도를 가지고 협력하도록 최소한의 의존성만을 남기는 설계


