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


