# 01 프로시저 추상화와 데이터 추상화
## 프로시저 추상화
- 소프트웨어가 무엇을 해야 하는지를 추상화한다.
### 기능 분해 (=알고리즘 분해)
## 데이터 추상화
- 소프트웨어가 무엇을 알아야 하는지를 추상화한다.
### 타입 추상화 (=추상 데이터 타입)
### 프로시저 추상화 (=객체지향)

# 02 프로시저 추상화와 기능 분해
## 메인 함수로서의 시스템
### 하향식 접근법
- 시스템을 구성하는 가장 최상위 기능을 정의하고, 이 최상위 기능을 좀 더 작은 단계의 하위 기능으로 분해해 나가는 방법
- 분해는 세분화된 마지막 하위 기능이 프로그래밍 언어로 구현가능한 수준이 될 때까지 계속된다.
## 급여 관리 시스템
- 급여 = 기본급 - (기본급 * 소득세율)
```
직원의 급여를 계산한다
    사용자로부터 소득세율을 입력받는다
        "세율을 입력하세요: "라는 문장을 화면에 출력한다
        키보드를 통해 세율을 입력받는다
    직원의 급여를 계산한다
        전역 변수에 저장된 직원의 기본급 정보를 얻는다
        급여를 계산한다
    양식에 맞게 결과를 출력한다
        "이름: {직원명}, 급여: {계산된 금액}" 형식에 따라 출력 문자열을 생성한다
```
<img width="500" alt="image" src="https://github.com/user-attachments/assets/321e100c-9dc9-4638-a78e-a326020eb942" />

- 기능 분해를 위한 하향식 접근법은 먼저 필요한 기능을 생각하고 이 기능을 분해하고 정제하는 과정에서 필요한 데이터의 종류와 저장 방식을 식별한다.
## 급여 관리 시스템 구현
```ruby
// 직원의 급여를 계산한다.
def main(name)
    // 사용자로부터 소득세육을 입력받는다.
    taxRate = getTaxRate()
    //  직원의 급여를 계산한다.
    pay = calculatePayFor(name, taxRate)
    // 양식에 맞게 결과를 출력한다.
    puts(describeResult(name,pay))
end

def getTaxRate()
    print("세율을 입력하세요: ")
    // 키보드를 통해 세율을 입력 받는다
    return gets().chomp().to_f()
end

def calculatePayFor(name, taxRate)
    // 전역 변수에 저장된 직원의 기본급 정보를 얻는다
    index = $employees.index(name)
    basePay = $basePays[index]
    // 급여를 계산한다.
    return basePay - (basePay * taxRate)
end

def describeResult(name, pay)
    return "이름: #{name}, 급여: #{pay}"
end
```
<img width="2504" height="945" alt="image" src="https://github.com/user-attachments/assets/9a50638e-5ff3-440e-b4f5-092c0dd7b4ae" />

## 하향식 기능 분해의 문제점
- 시스템은 하나의 메인 함수로 구성돼 있지 않다.
- 기능 추가나 요구사항 변경으로 인해 메인 함수를 빈번하게 수정해야 한다.
```ruby
// ex) 모든 직원들의 기본급의 총합을 구하는 기능을 추가
def main(operation, args{})
    case(operation)
    when :pay then calculatePay(args[:name])
    when :basePays then sumOfBasePays()
    end
end
```
- 비즈니스 로직이 사용자 인터페이스와 강하게 결합된다.
- 하향식 분해는 너무 이른 시기에 함수들의 실행 순서를 고정시키기 때문에 유연성과 재사용성이 저하된다.
- 데이터 형식이 변경될 경우 파급효과를 예측할 수 없다.
- 함수는 상위 함수가 강요하는 문맥에 강하게 결합된다.

# 03 모듈
## 정보 은닉
-  시스템을 모듈 단위로 분해하기 위한 기본 원리
-  시스템에서 자주 변경되는 부분으 상대적으로 덜 변경되는 안정적인 인터페이스 뒤로 감춰야 한다는 것
## 모듈
- 변경될 가능성이 있는 비밀을 내부로 감추고, 잘 정의되고 쉽게 변경되지 않을 퍼블릭 인터페이스를 외부에 제공해서 내부의 비밀에 함부로 접근하지 못하게 한다.
### 모듈이 감춰야 할 비밀
- 복잡성 : 모듈이 너무 복잡한 경우 이해하고 사용하기가 어렵다. 외부에 모듈을 추상화할 수 있는 간단한 인터페이스를 제공해서 모듈의 복잡도를 낮춘다.
- 변경 가능성 : 변경 가능한 설계 결정이 외부에 노출될 경우 실제로 변경이 발생했을 때 파급효과가 커진다. 변경 발생 시 하나의 모듈만 수정하면 되도록 변경 가능한 설계 결정을 모듈 내부로 감추고 외부에는 쉽게 변경되지 않을 인터페이스를 제공한다.
```ruby
module Employees
  $employees = ["직원A", "직원B", "직원C", "아르바이트D", "아르바이트E", "아르바이트F"]
  $basePays = [400, 300, 250, 1, 1, 1.5]
  $hourlys = [false, false, false, true, true, true]
  $timeCards = [0, 0, 0, 120, 120, 120]

  def Employees.calculatePay(name, taxRate)
    if (Employees.hourly?(name)) then
      pay = Employees.calculateHourlyPayFor(name, taxRate)
    else
      pay = Employees.calculatePayFor(name, taxRate)
    end
  end

  def Employees.hourly?(name)
    return $hourlys[$employees.index(name)]
  end

  def Employees.calculateHourlyPayFor(name, taxRate)
    index = $employees.index(name)
    basePay = $basePays[index] * $timeCards[index]
    return basePay - (basePay * taxRate)
  end

  def Employees.calculatePayFor(name, taxRate)
    return basePay - (basePay * taxRate)
  end
  
  def Employees.sumOfBasePays()
    result = 0
    for name in $employees
      if (not Employees.hourly?(name)) then
        result += $basePays[$employees.index(name)]
      end
    end
    return result
  end
end
```
- 모듈 외부에서는 직원 정보를 관리하는 데이터에 직접 접근할 수 없다.

## 모듈의 장점과 한계
### 장점
- 모듈 내부의 변수가 변경되더라도 모듈 내부에만 영향을 미친다
- 비지니스 로직과 사용자 인터페이스에 대한 관심사를 분리한다.
- 전역 변수와 전역 함수를 제거함으로써 네임스페이스 오염을 방지한다.

### 단점
- 인스턴스의 개념을 제공하지 않는다.
- 좀 더 높은 추상화를 위해서는 직원 전체가 아니라 개별 직원을 독립적인 단위로 다룰 수 있어야 한다.

# 04 데이터 추상화와 추상 데이터 타입
## 추상 데이터 타입을 구현하기 위한 조건
- 타입 정의를 선언할 수 있어야 한다.
- 타입의 인스턴스를 다루기 위해 사용할 수 있는 오퍼레이션의 집합을 정의할 수 있어야 한다.
- 제공된 오퍼레이션을 통해서만 조작할 수 있도록 데이터를 외부로부터 보호할 수 있어야 한다.
- 타입에 대해 여러 개의 인스턴스를 생성할 수 있어야 한다.

## 데이터 타입 구현
```ruby
Employee = Struct.new(:name, :basePay, :houly, :timeCard) do
End
```
- 이름, 기본급, 알바 여부, 작업시간을 비밀로 가지는 추상 데이터 타입 Employee를 선언한다.

# 05 클래스
## 추상 데이터 타입과 클래스의 차이
- 클래스는 상속과 다형성을 지원하는 데 비해 추상 데이터 타입은 지원하지 못한다.

## 객체지향
<img width="584" height="195" alt="image" src="https://github.com/user-attachments/assets/b72c2a47-e882-44f5-9115-adcf2ddd2526" />
- 객체지향은 타입을 기준으로 오퍼레이션을 묶는다.
- 정규 직원과 아르바이트 직원이라는 두 가지 클래스로 분리할 경우 공통 로직을 포함할 부모 클래스를 정의하고 두 직원 유형의 클래스가 부모 클래스를 상속받게 하는 것이다.
- 클라이언트는 부모 클래스의 참조자에 대해 메시지를 전송하면 실제 클래스가 무엇인가에 따라 적절한 절차가 실행된다. (=> 다형성)
- 객체지향은 절차 추상화이다.

## 클래스 구현
```ruby
class SalariedEmployee < Employee
  def initialize(name, basePay)
    super(name, basePay)
  end
    
  def calculatePay(taxRate)
    return basePay - (basePay * taxRate)
  end
  
  def monthlyBasePay()
    return basePay
  end
end

class HourlyEmployee < Employee
  attr_reader :timeCard
  def initialize(name, basePay, timeCard)
    super(name, basePay)
    @timeCard = timeCard
  end
  
  def calculatePay(taxRate)
    return (basePay * timeCard) - (basePay * timeCard) * taxRate
  end
  
  def monthlyBasePay()
    return 0
  end  
end
```
- 객체지향에서는 타입 변수를 이용한 조건문을 다형성으로 대체한다.
- 개방-폐쇄 원칙(Open-Closed Principle) : 기존 코드에 아무런 영향도 미치지 않고 새로운 객체 유형과 행위를 추가할 수 있는 특성을 

## 협력이 중요하다
- 객체가 참여할 협력을 결정하고 협력에 필요한 책임을 수행하기 위해 어떤 객체가 필요한지에 관해 고민하라.
- 그 책임을 다양한 방식으로 수행해야 할 때만 타입 계층 안에 각 절차를 추상화하라.
- 타입 계층과 다형성은 협력이라는 문맥 안에서 책임을 수행하는 방법에 관해 고민한 결과물이어야 하며 그 자체가 목적이 되어서는 안 된다.
