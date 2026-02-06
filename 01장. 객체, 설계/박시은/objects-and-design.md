# 이론 대 실무
- 실무가 어느 정도 발전하고 난 다음에야 비로소 실무의 실용성을 입증할 수 있는 이론이 서서히 그 모습을 갖춰가기 시작하고, 해당 분야가 충분히 성숙해지는 시점에 이르러서야 이론이 실무를 추월하게 된다는 것이다.
- 실무는 훌륭한 소프트웨어를 설계하기 위해 필요한 다양한 기법과 도구를 초기부터 성공적으로 발전시켜 왔던 것이다.
- 대부분의 설계 원칙과 개념 역시 실무에서 반복적으로 적용되던 기법들을 이론화한 것들이 대부분이다.
- 추상적인 개념과 이론은 훌륭한 코드를 작성하는 데 필요한 도구이다.
- 개발자는 구체적인 코드를 만지며 손을 더럽힐 때 가장 많은것을 얻어가는 존재다.

# 티켓 판매 애플리케이션 구현하기
<img width="1123" height="445" alt="image" src="https://github.com/user-attachments/assets/745bc141-86e3-4c89-a90d-033f092fe4ef" />

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
