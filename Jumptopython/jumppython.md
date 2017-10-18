[점프투 파이썬](https://wikidocs.net/6#fnref:indentation)정리.

리눅스 사용자라면 기본적으로 파이썬이 설치되어 있을 것이다.
$ python -V

클래스 개념.
-  함수 이긴 한데, 인스턴트를 쓸수 있는것.
-  객체에다 클래스를 부르면 그것이 인스턴트가 된다.  

>예

```python
class Calculator: #Calculator라는 class생성
  def __init__(self): #__init__는 인스턴트를 생성할때 마다 무조건 호출됨.
    self.result = 0 # self는 객체 id를 부르는 개념. 아래에서는 cal1, cal2에 해당됨
  def adder(self, num): #__init__에서 확인한 self id 랑 같은 인스턴트에 adder해준다.
	  self.result += num
		return self.result

cal1 = Calculator()
cal2 = Calculator()
print(cal1.adder(3))
print(cal1.adder(4))
print(cal2.adder(3))
print(cal2.adder(7))
```  


Caculator라는 클래스 안에 adder라는 함수가 있고, 이것을 cal1, cal2라는 객체에 인스턴트로 부르다. 그리고 cal1, cal2 인스턴트에 adder 함수를 호출하는 방식. 결국 함수를 정의 하고 사용하면 한번에 한 memory를 사용해야하지만, 인스턴스 방식은 호출, 사용이 자유로움.

```python
class Service:
  def __init__(self, name):
    self.name = name
  def sum(self, a, b):
    result = a + b
    print("%s님 %s + %s = %s입니다." % (self.name, a, b, result))
```  
위 코드에서는 pey = Service("이현수") 처럼 객체에 인스턴트를 부를때 name을 넣어줘야한다.

###클래스 상속
```python
class HousePark():
  lastname = "박"
  def __init__(self, name):
    fullname = lastname + self.name

class HouseKim(HousePark): #이게 상속. housepakr과 같은 함수기능을 형성함.
    lastname = "김" #대신 lastname만 김으로 바꿈.
```

###클래스 오버라이딩
클래스를 상속할때, 다른 함수를 추가하면 오버라이딩.
