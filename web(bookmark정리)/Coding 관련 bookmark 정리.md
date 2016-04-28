# Coding 관련 bookmark 정리

https://wikidocs.net/6#fnref:indentation
점프투 파이썬. 파이썬에 대한 위키 스타일의 전차책.
> 리눅스 사용자라면 기본적으로 파이썬이 설치되어 있을 것이다.
$ python -V

클래스 개념.
-  함수 이긴 한데, 인스턴트를 쓸수 있는것.
-  객체에다 클래스를 부르면 그것이 인스턴트가 된다.  
> 예.  

```python
  class Calculator:
	  		def __init__(self):
				self.result = 0
			def adder(self, num):
				self.result += num
				return self.result

    cal1 = Calculator()
    cal2 = Calculator()
    print(cal1.adder(3))
    print(cal1.adder(4))
    print(cal2.adder(3))
    print(cal2.adder(7))


Caculator라는 클래스 안에 adder라는 함수가 있고, 이것을 cal1, cal2라는 객체에 인스턴트로 부르다. 그리고 cal1, cal2 인스턴트에 adder 함수를 호출하는 방식. 결국 함수를 정의 하고 사용하면 한번에 한 memory를 사용해야하지만, 인스턴스 방식은 호출, 사용이 자유로움.
