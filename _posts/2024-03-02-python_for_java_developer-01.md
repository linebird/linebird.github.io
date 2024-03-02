---
layout: post
title: "Java 개발자를 위한 Python 소개 - 01"
date: 2024-03-02 10:47:00 +0900
categories: [Programming Language]
tags: [python, java]
---

## 문서의 목적

실제 현업에서 Java 개발자가 Python으로 개발 구현을 바로 실행할 경우에 필요한 지식을 전달하기 위하여 작성되었다.

## Python의 특징

아래의 특징들은 Python이 Java와 다르게 설계되었고, Java 개발자가 Python을 배우고 사용할 때 고려해야 할 중요한 점이다.

- 간결하고 읽기 쉬운 문법: Python은 간결하고 읽기 쉬운 문법을 가지고 있습니다. 이로 인해 코드를 작성하고 이해하기가 편리합니다. Java보다 훨씬 적은 코드로도 동일한 작업을 수행할 수 있습니다.

- **동적 타입 시스템**: Python은 동적 타입 시스템을 사용하여 변수의 타입을 선언하지 않아도 된다. 이는 코드를 작성할 때 유연성을 제공하며 개발자가 코드를 더 빠르게 작성할 수 있도록 도와주게된다. 그러나 이는 **타입 오류를 런타임에 발견할 수 있게 되므로 개발 시 주의가 필요**하다.

- 다양한 용도에 사용 가능: Python은 다양한 분야에서 사용되고 있다. Java와 비교하여 데이터 과학, 인공 지능, 자동화 등 여러 분야에서 더 활발하게 사용되고 있다.

- 풍부한 라이브러리 생태계: Python은 풍부한 표준 라이브러리와 다양한 외부 라이브러리를 제공한다. Java보다 더 큰 생태계가 구성되어 있다.

- 높은 가독성과 유지 보수성: Python은 읽기 쉽고 간결한 문법을 가지고 있어 코드의 가독성이 높다. 이는 코드를 이해하고 유지 보수하는 데 유리함.

- 동적 메모리 관리: Python은 개발자가 메모리 할당과 해제를 직접 다룰 필요가 없는 동적 메모리 관리를 제공. 이는 개발자가 메모리 관리에 대해 걱정하지 않고 코드를 작성할 수 있도록 도와준다.

- 플랫폼 독립성: Python 코드는 다양한 운영 체제와 플랫폼에서 실행될 수 있는 이식 가능한 언어이다.

## Java와 Python의 차이

|구분|Java|Python|
|---|---|---|
|Compilation|Compiled Language|**Interpreted Language**|
|Static or Dynamic|정적타입|**동적타입**|
|String operations|제한된 문자열 관련 기능을 제공|문자열과 관련된 다양한 기능을 제공|
|Learning curve|어렵다|쉽다.|
|다중 상속|다중 상속은 인터페이스를 통해 부분적으로 수행|단일 상속과 다중 상속을 모두 제공|
|Braces vs. Indentation|중괄호를 사용하여 각 함수 및 클래스 정의의 시작과 끝을 정의|Python은 들여쓰기를 사용하여 코드를 코드 블록으로 분리|
|Portability|Java 가상 머신을 실행할 수 있는 모든 컴퓨터 또는 모바일 장치는 Java 애플리케이션을 실행 가능|Python 프로그램에서는 Python 코드를 번역하기 위해 대상 컴퓨터에 인터프리터가 설치되어 있어야 한다. Java에 비해 Python은 이식성이 떨어진다.|
|Read file|Java에서는 Java의 파일을 읽으려면 10줄의 코드가 필요|Python 단 2줄의 코드|
|Architecture|Java Virtual Machine은 코드를 실행하고 바이트코드를 기계어로 변환하는 런타임 환경을 제공|Python의 경우 인터프리터는 소스 코드를 기계 독립적인 바이트코드로 변환한다.|
|Backend Frameworks|Spring, Blade|Django, Flask|
|Machine Learning Libraries|Weka, Mallet, Deeplearning4j, MOA|Tensorflow. Pytorch|
|이 기술을 사용하는 유명 기업|Airbnb, Netflix, Spotify, Instagram|Uber, Technologies, Dropbox, Google|

## java 개발자가 이해해야 할 python 언어 문법적 차이

1. 들여쓰기(indentation):
   Python은 ***들여쓰기를 통해 코드 블록을 구분***한다. 일반적으로는 4개의 공백이나 탭 문자를 사용한다.
2. 변수 선언과 타입:
    Python은 동적 타입 언어이므로 변수를 선언할 때 타입을 명시적으로 지정할 필요가 없다. Java의 int, String, boolean 등의 타입을 대신하여 Python은 단순히 변수를 선언하면 된다.
3. 함수 정의:
    Python은 def 키워드를 사용하여 함수를 정의한다. 함수 본문은 들여쓰기로 구분된다.
    Java에서는 메서드를 정의할 때 public static void와 같은 형태로 선언하지만, Python에서는 그냥 함수를 정의한다.
4. 조건문과 반복문:
    Python은 if, elif, else와 같은 조건문과 for, while과 같은 반복문을 사용한다. Java와 마찬가지로 조건문과 반복문의 본문은 들여쓰기로 구분된다.
5. 리스트, 튜플, 딕셔너리:
    Python은 리스트(list), 튜플(tuple), 딕셔너리(dictionary)와 같은 자료 구조를 제공한다. 이러한 자료 구조는 Java의 배열, 리스트, 맵과 유사한 역할을 한다.
6. 클래스와 객체지향 프로그래밍:
    Python도 객체지향 프로그래밍을 지원하며, 클래스를 정의하여 객체를 생성하고 사용할 수 있다. Java와 비슷하게 클래스, 메서드, 생성자 등을 정의하고 객체를 생성하여 사용한다.
7. 모듈과 패키지:
    Python은 모듈(module)과 패키지(package)를 사용하여 코드를 구성한다. 모듈은 .py 파일로, 패키지는 여러 모듈을 포함하는 디렉토리이다. 아무런 내용도 없는 "&#95;&#95;init&#95;&#95;.py" 파일을 디렉토리에 생성해서 패키지를 구분한다.   ***&#95;&#95;init&#95;&#95;.py 파일은 디렉터리가 파이썬 패키지의 일부임을 알려주는 역할을 하는데, 여러 Python 모듈을 import 하는 메커니즘을 제공한다.*** Python 3.3 이후부터는 필수적인 파일이 아니게 되었으나 하위 버전 간의 호환성과 패키지의 명확성을 위해 생성하는 것을 권장한다.  

    예를 들어 "mypackage"라는 이름의 패키지를 구성하려고하면, &#95;&#95;init&#95;&#95;.py를 추가하여 패키지를 구성할 수 있다.

    ```shell
    mypackage/
        __init__.py
        module1.py
        module2.py
    ```

    이렇게 하면 "mypackage"패키지를 아래와 같이 import하여 사용할 수 있다.

    ```python
    import mypackage

    # mypackage 모듈의 함수 사용
    mypackage.module1.some_function()
    # mypackage 패키지의 모듈내의 변수 사용
    print(mypackage.module1.some_variable) 
    ```

## "Pythonic"한 문법적 특징을 갖춘 구체적인 코드 예시

1. 리스트 컴프리헨션(List Comprehension):

   ```python
   # 리스트 컴프리헨션을 사용한 리스트 생성
   numbers = [1, 2, 3, 4, 5]
   squared_numbers = [x ** 2 for x in numbers]
   print(squared_numbers)  # 출력: [1, 4, 9, 16, 25]
   ```

2. 람다 함수(Lambda Functions):

    ```python
    # 람다 함수를 사용한 간단한 함수 정의
    add = lambda x, y: x + y
    print(add(2, 3))  # 출력: 5
    ```

3. 제너레이터(Generators):

    ```python
    # 제너레이터를 사용하여 무한한 시퀀스 생성
    def infinite_sequence():
        num = 0
        while True:
            yield num
            num += 1

    # 제너레이터에서 값을 가져오기
    gen = infinite_sequence()
    print(next(gen))  # 출력: 0
    print(next(gen))  # 출력: 1
    ```

4. 문자열 처리(String Handling):

    ```python
    # f-문자열을 사용한 문자열 포맷팅
    name = "Alice"
    age = 30
    formatted_string = f"My name is {name} and I'm {age} years old."
    print(formatted_string)  # 출력: "My name is Alice and I'm 30 years old."
    ```

5. 동적 속성과 메서드(Dynamic Attributes and Methods):

    ```python
    # 동적으로 속성 및 메서드 추가
    class Person:
        pass

    person = Person()

    # 동적으로 속성 추가
    person.name = "Alice"
    print(person.name)  # 출력: "Alice"

    # 동적으로 메서드 추가
    def greet(self):
        return f"Hello, my name is {self.name}!"

    from types import MethodType
    person.greet = MethodType(greet, person)
    print(person.greet())  # 출력: "Hello, my name is Alice!"
    ```

## Python class

Java와 다른 점을 아래 소스를 보며 확인한다.

```python
class MyClass:
    # 클래스 변수(속성)
    class_variable = 10

    # 생성자(Constructor)
    def __init__(self, name):
        self.name = name

    # 메서드
    def greet(self):
        return f"Hello, {self.name}!"
    
    def _private_method(self):
        pass
```

1. 생성자(Constructor):
    - Java에서는 생성자는 클래스명과 동일한 이름을 가진다
    - Python에서는 생성자는 **&#95;&#95;init&#95;&#95;** 메서드로 정의된다.
2. 메서드 정의:
   - Java에서는 메서드 정의 시 반환 타입을 명시하고, 메서드 이름과 매개변수를 지정
   - Python에서는 메서드 정의 시 반환 타입을 명시하지 않으며, 메서드 이름과 매개변수만을 지정
3. python 속성과 메서드:
   - 속성: 클래스의 속성은 해당 클래스의 모든 인스턴스에 대해 공유되는 값이다. 속성은 객체의 상태를 나타내며, '**self**' 키워드를 사용하여 정의된다.
   - 메서드: 클래스의 메서드는 해당 클래스의 인스턴스에 대해 작동하는 함수. 메서드는 클래스 내부에서 정의되며, 첫 번째 매개변수로 '**self**'를 사용하여 현재 객체를 참조한다.
4. **self**: Python에서 '**self**'는 현재 객체를 가리키는 레퍼런스. Java에서는 this와 비슷한 역할을 한다. '**self**'를 사용하여 클래스의 속성과 메서드에 접근할 수 있다. '**self**'는 메서드의 첫 번째 매개변수로 선언되며, 관례적으로 '**self**'라는 이름을 사용한다(Python에서 강제되는 예약어가 아니다).
5. 접근 제어: Python에서는 접근 제어에 대한 특별한 키워드를 제공하지 않는다. 대신 속성 및 메서드의 이름 앞에 ***언더바 _를 사용하여 비공개(private) 속성 및 메서드로 표시***할 수 있다. 이는 관례적인 방법이며, 강제되는 것은 아니다. 위의 소스에서 _private_method(self)
6. 특수 메서드 (매직 메서드):
Python은 클래스에서 특정한 행동을 정의하기 위해 미리 정의된 특수한 메서드를 제공한다. 이러한 메서드들은 매직 메서드 또는 던더(double underscore) 메서드라고도 부른다. 위의 예에서 **&#95;&#95;init&#95;&#95;** 메서드가 그 예이다.

    |매직 메서드|설명|
    |---|--|
    |&#95;&#95;init&#95;&#95;(self, ...)|생성자(Constructor)로, 클래스의 인스턴스가 생성될 때 호출된다|
    |&#95;&#95;str&#95;&#95;(self)|'str()' 함수가 객체를 문자열로 변환할 때 호출된다.|
    |&#95;&#95;repr&#95;&#95;(self)|'repr()' 함수가 객체를 표현할 때 호출된다|
    |&#95;&#95;len&#95;&#95;(self)|'len()' 함수가 객체를 표현할 때 호출된다|
    |&#95;&#95;add&#95;&#95;(self)|'+' 연산자가 두 개의 객체를 더할 때 호출된다|
    |&#95;&#95;sub&#95;&#95;(self)|'-' 연산자가 두 개의 객체를 뺄 때 호출된다|
    |&#95;&#95;eq&#95;&#95;(self)|'==' 함수가 객체를 표현할 때 호출된다|