#데코레이터 #파이썬 #class
# 데코레이터
- 데코레이터는 장식하다의 의미로 Class 메서
- 데코레이터는 다른 함수나 메서드를 감싸서(wrapping) 그 동작을 수정하거나 추가 기능을 부여하는 함수입니다.  즉, 데코레이터는 기존의 함수를 인자로 받아 새로운 함수를 반환하는 함수입니다.  `@데코레이터명`이라는 구문은 아래와 같이 작성한 것과 동일한 효과를 가집니다.
- 여기서 내가 찾아본 정보는 @classmethod에 관한 정보이다.
- classmethod는 **클래스 메서드를 정의할 때 사용하는 데코레이터 이다.**
- 클래스 매서드는 클래스 자체를 첫 번째 인자로 받는다.
```
class MyClass:
    def instance_method(self):
        print("인스턴스 메서드 호출")
        print("self =", self)
    
    @classmethod
    def class_method(cls):
        print("클래스 메서드 호출")
        print("cls =", cls)

# 인스턴스를 통해 인스턴스 메서드 호출
obj = MyClass()
obj.instance_method()
# 출력:
# 인스턴스 메서드 호출
# self = <__main__.MyClass object at 0x...>

# 클래스 메서드는 인스턴스 없이 클래스에서 직접 호출 가능
MyClass.class_method()
# 출력:
# 클래스 메서드 호출
# cls = <class '__main__.MyClass'>

```
- 위와 같이 classmethod는 작업을 도와 준다. 
	- 기존의 방식은 classs 인스턴스를 호출을 해야 안의 함수들을 사용가능
	- @classmethod를 사용하면 굳이
		- 함수안에 class 데코레이션을 지정을 해서 
https://velog.io/@doondoony/Python-Decorator-101
https://dojang.io/mod/page/view.php?id=2427)