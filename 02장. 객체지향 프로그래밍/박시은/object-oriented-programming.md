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
