---
layout: post
title: "OSCMP 주석 표준 관련"
date: 2024-01-19 19:40:00 +0900
categories: [SCore]
tags: [openstack, ysk]
---

## OSCMP 주석 표준 관련

참고 사이트
----------

```
- https://jh-bk.tistory.com/15
- https://velog.io/@jk01019/docstring-in-python
```

주석 달기 (책 "파이썬 코딩의 기술")
---------------------------------

- 모듈
  - 모듈의 내용과, 사용자가 알아야 하는 중요한 클래스나 함수를 기술한다.
- 클래스
  - 클래스 문 뒤에 바로 기술한다.
  - 동작, 중요한 Attribute, 하위 클래스의 동작을 기술한다.
- 함수와 메소드
  - 모든 인자, 반환값, 발생하는 예외, 기타 세부적인 동작을 기술한다

모듈 문서화
----------

- 각 모듈에는 소스 코드 첫 줄에 그 모듈에 대한 설명을 포함한다.
- 첫 줄
  - 모듈의 목적을 설명하는 한 문장을 작성한다.
- 다음 단락들
  - 모듈의 동작에 대해 알려줘야 하는 세부 사항
    - 중요한 클래스와 함수를 강조해도 좋다
    - argument (config)를 소개해도 좋다
    - 모듈이 명령줄 도구라면, 도구를 실행해 사용하는 방법을 작성한다.
 
  [예제 1]

    ```
    """ 단어의 언어 패턴을 찾을 때 쓸 수 있는 라이브러리

    여러 단어가 서로 어떤 연관 관계에 있는지 검사하는 것이 어려울 떄가 있다!
    이 모듈은 단어가 가지는 특별한 특성을 쉽게 결정할 수 있게 해준다.

    사용 가능 함수:
    _ palindrome: 주어진 단어가 회문인지 결정한다.
    _ check_anagram: 주어진 단어가 어구전철(똑같은 글자들로 순서가 바뀐 경우)인지 결정한다. 
    """
    ```

  [예제 2]
    ```
        """발사체 궤적 계산을 위한 라이브러리.

        이 모듈은 특정 조건 하에서의 발사체 움직임을 간단화된 방식으로 계산하는 것을 도와준다.

        Classes:
            Projectile: 발사체의 속성을 표현한다
            ...

        Functions:
            projectile_landing_position(velocity, angle, g_constant): 원점에서 출발한 물체의 착륙 지점을 계산한다
            ...
        """

        from math import sin, cos, pi

        class Projectile:
            """발사체의 속성을 표현한다.

            Projectile 을 상속받는 클래스는 'dynamics' 메서드를 오버라이드해
            물체의 세부 dynamics 를 정의해야 한다.

            Attributes:
                mass: 물체의 질량 (float)
                ...

            Methods:
                info: 물체의 속성을 출력
                dynamics: 물체의 세부 dynamics 로, 하위 클래스에서 내용을 정의
                ...
            """


        def projectile_landing_position(velocity, angle, g_constant):
            """원점에서 출발한 물체의 착륙 지점을 계산한다.

            XY 평면 (0,0) 좌표에서 X축 기준 angle 방향과
            velocity 값의 속력으로 출발한 물체가, -Y축 방향
            중력 가속도 g 하에 얼마의 x 값에서 y=0에 도달할 지 계산하는 함수.

            Args:
                velocity: 물체의 초기 속력 (0 < velocity)
                angle: 물체의 초기 이동 방향 (0 < angle < 180, degree)
                g_constant: 중력 가속도 (0 < g_constant)

            Raises:
                ValueError: 인자가 조건에 맞지 않는 경우

            Returns:
                landing_position: 물체의 y 좌표가 0에 도달했을 시점에서의 x 좌표
            """

            if velocity < 0:
                raise ValueError("velocity 는 0과 같거나 커야 합니다")
            if angle > 180 or angle < 0:
                raise ValueError("angle 은 [0~180] 범위의 값이어야 합니다")
            if g_constant <= 0:
                raise ValueError("g_constant 는 0보다 커야 합니다")

            velocity_x = velocity * cos(angle / 180 * pi)
            velocity_y = velocity * sin(angle / 180 * pi)

            terminal_time = velocity_y / g_constant
            landing_position = velocity_x * terminal_time

            return landing_position
    ```

클래스 문서화
------------

- 첫 줄
  - 클래스의 목적을 설명하는 한 문장
- 다음 단락들
  - 중요한 공개 attribute와 method를 강조하면 좋다.
  - 이 클래스를 상속하는 하위 클래스가, 보호 attribute나 method와 상호작용하는 방법을 인내해야 한다.

  [예제]

    ```
    class Player:
        """게임 플레이어를 표현하다.
        
        하위 클래스는 'tick' 메서드를 오버라이드해서, 플레이어의 파워 레벨 등에 맞는 움직임 에니메이션을 제공할 수 있다.
        
        공개 attribute
        - power: 사용하지 않은 파워업들 (0과 1사이 float).
        - coins: 현재 레벨에서 발견한 코인 개수 (integer).
        """
    ```

함수 문서화하기
--------------

- 모든 공개 함수와 method에는 docstring을 포함시켜야 한다.

- 첫 줄
  - 함수가 하는 일을 설명한다.

- 다음 단락들
  - 함수 argument나 함수의 동작에 대해 구체적으로 설명한다.
  - 함수의 인터페이스에 속해 있으며, 함수를 호출하는 쪽에서 꼭 처리해야 하는 예외도 설명해야 한다.

- 함수 독스트링을 작성할 때 지켜야 할 규칙들
  - 함수에 argument가 없고 return만 있다면, 설명은 한 줄로도 충분하다.
  - 함수가 아무 값도 반환하지 않는다면, 반환 값에 대한 설명을 제외하라.
  - 함수 인터페이스에 예외 발생이 포함된다면, 발생하는 예외와 예외가 발생하는 상황에 대한 설명을 반드시 포함시켜야 한다.
  - 함수가 가변 인자나 키워드 인자를 받는다면, 문서화한 인자 목록에 *args나 **kwargs 를 사용하고 각각의 목적을 설명하라.
  - 함수에 디폴트 값이 있는 argument가 있다면, 디폴트 값을 언급해야 한다.
  - 함수가 generator 이라면, 독스트링에는 이 generator을 iteration할 때 어떤 값이 발생하는지 기술해야 한다.
  - 함수가 비동기 코루틴이라면, 독스트링에 언제 이 코루틴의 비동기 실행이 중단되는지 설명해야 한다.

  [예제]

    ```
    1) google docstring

    def find_anagrams(word: str, dictionary: Container[str]) -> List[str]:
        """주어진 단어의 모든 어구전철을 찾는다.
        
        이 함수는 '딕셔너리' 컨테이너의 원소 검사만큼 빠른 속도로 실행된다.
        
        Args:
            word: 대상 단어
            dictionary: 모든 단어가 들어 있는 collections.abc.Container 컬렉션.
        
        Returns:
            찾은 어구전철들로 이뤄진 리스트. 아무것도 찾지 못한 경우 Empty.
        """
        pass
    ```

    ```
    - Sphinx와 호환되고, autodoc이 인지하게 만드려면, conf.py 파일에 sphinx.ext.napoleon 확장자를 추가해라.
    
    - VS Code에서 해당 style을 이용하고 싶다면, https://marketplace.visualstudio.com/items?itemName=njpwerner.autodocstring 을 써라.
    ```

    ```
    1) Sphinx docstring

    def sphinx_docstrings(num1, num2) -> int:
        """Add up two integer numbers.

        This function simply wraps the ``+`` operator, and does not
        do anything interesting, except for illustrating what
        the docstring of a very simple function looks like.

        :param int num1: First number to add.
        :param int num2: Second number to add.
        :returns:  The sum of ``num1`` and ``num2``.
        :rtype: int
        :raises AnyError: If anything bad happens.
        """
        return num1 + num2
    ```

주석 style 3가지 소개 및 비교
-----------------------

---
> * Python Doctring은 기본적으로 reStructureText포맷을 사용한다. 즉, RST확장자를 가진 파일을 핸들링한다.
> 
> * Python의 공식 문서들은 모두 Sphinx로 만들어졌다고 한다.
---

### <u>1. google style</u>
  
  - Sphinx와 호환된다.
  - autodoc이 인지하게 만드려면, conf.py 파일에 sphinx.ext.napoleon 확장자를 추가
  - VS Code에서 해당 style을 이용하고 싶다면, https://marketplace.visualstudio.com/items?itemName=njpwerner.autodocstring을 플러그인으로 설치
  - 예제 사이트: https://www.sphinx-doc.org/en/master/usage/extensions/example_google.html

  - Sphinx에서의 사용 가능성
    - "sphinx.ext.napoleon" extension이 Sphinx 1.3부터 import해서 사용 가능
      - Sphinx 툴킷에서 "Sphinx Docstring대신 "Google Docstring"을사용 가능함 
      - sphinx.ext.napoleon" extension은 NumPy and Google style docstring을 지원
  
  - 장점 및 비교
    - 아래 NumPy, Sphinx 방식에 비해 가독성(Readability)가 가장 좋다.
    - 아래 NumPy, Sphinx 방식에 비해 가장 개발자가 많이 사용하는 방식이다.
    - 
    - 구글 방식이 Sphinx과 호환되므로 Sphinx 대신에 사용이 가능하다
      - 참고로 OpenStack 프로젝트의 소스코드에서 Sphinx Docstring 방식 사용함
      - Goodle Docstring이 Sphinx Docstring이 방식 보다 적은 라인으로 작성 가능함
        - Sphinx 방식은 ":param"라인과 ":type"라인을 두개로 작성해야 한다.
        - 구글 방식은 "param1 (int, optional): The first parameter. Defaults to None" 형식으로 간단하게 한줄로 작성이 가능하다.
  
        ```
        -----------------<<< Sphinx 방식 >>>-----------------

        def getCharacteristics(self, startHnd=1, endHnd=0xFFFF, uuids=None):
        """Returns a list containing :class:`bluepy.btle.Characteristic`
        objects for the peripheral. If no arguments are given, will return all
        characteristics. If startHnd and/or endHnd are given, the list is
        restricted to characteristics whose handles are within the given range.

        :param startHnd: Start index, defaults to 1
        :type startHnd: int, optional
        :param endHnd: End index, defaults to 0xFFFF
        :type endHnd: int, optional
        :param uuids: a list of UUID strings, defaults to None
        :type uuids: list, optional
        :return: List of returned :class:`bluepy.btle.Characteristic` objects
        :rtype: list
        """
        self._characteristics = []
        if(uuids is not None):
            for uuid in uuids:
                try:
                    characteristic = super().getCharacteristics(
                        startHnd, endHnd, uuid)[0]
                    self._characteristics.append(characteristic)
                except BTLEException:
                    pass
        else:
            self._characteristics = super().getCharacteristics(startHnd,
                                                               endHnd)
        return self._characteristics

        -----------------<<< 구글 방식 >>>-----------------

        def module_level_function(param1, param2=None, *args, **kwargs):
        """This is an example of a module level function.

        Function parameters should be documented in the ``Args`` section. The name
        of each parameter is required. The type and description of each parameter
        is optional, but should be included if not obvious.

        If ``*args`` or ``**kwargs`` are accepted,
        they should be listed as ``*args`` and ``**kwargs``.

        The format for a parameter is::

            name (type): description
                The description may span multiple lines. Following
                lines should be indented. The "(type)" is optional.

                Multiple paragraphs are supported in parameter
                descriptions.

        Args:
            param1 (int): The first parameter.
            param2 (:obj:`str`, optional): The second parameter. Defaults to None.
                Second line of description should be indented.
            *args: Variable length argument list.
            **kwargs: Arbitrary keyword arguments.

        Returns:
            bool: True if successful, False otherwise.

            The return type is optional and may be specified at the beginning of
            the ``Returns`` section followed by a colon.

            The ``Returns`` section may span multiple lines and paragraphs.
            Following lines should be indented to match the first line.

            The ``Returns`` section supports any reStructuredText formatting,
            including literal blocks::

                {
                    'param1': param1,
                    'param2': param2
                }

        Raises:
            AttributeError: The ``Raises`` section is a list of all exceptions
                that are relevant to the interface.
            ValueError: If `param2` is equal to `param1`.

        """
        if param1 == param2:
            raise ValueError('param1 may not be equal to param2')
        return True
        ```
  
  ```
  >> Google Docstring <<

    {{! Google Docstring Template }}
    {{summaryPlaceholder}}

    {{extendedSummaryPlaceholder}}
    {{#parametersExist}}

    Args:
    {{#args}}
        {{var}} ({{typePlaceholder}}): {{descriptionPlaceholder}}
    {{/args}}
    {{#kwargs}}
        {{var}} ({{typePlaceholder}}, optional): {{descriptionPlaceholder}}. Defaults to {{&default}}.
    {{/kwargs}}
    {{/parametersExist}}
    {{#exceptionsExist}}

    Raises:
    {{#exceptions}}
        {{type}}: {{descriptionPlaceholder}}
    {{/exceptions}}
    {{/exceptionsExist}}
    {{#returnsExist}}

    Returns:
    {{#returns}}
        {{typePlaceholder}}: {{descriptionPlaceholder}}
    {{/returns}}
    {{/returnsExist}}
    {{#yieldsExist}}

    Yields:
    {{#yields}}
        {{typePlaceholder}}: {{descriptionPlaceholder}}
    {{/yields}}
    {{/yieldsExist}}  
  ```

  ```
  def google_docstrings(num1, num2
                      ) -> int:
    """Add up two integer numbers.

    This function simply wraps the ``+`` operator, and does not
    do anything interesting, except for illustrating what
    the docstring of a very simple function looks like.

    Args:
        num1 (int) : First number to add.
        num2 (int) : Second number to add.

    Returns:
        The sum of ``num1`` and ``num2``.

    Raises:
        AnyError: If anything bad happens.
    """
    return num1 + num2
  ```

- Docstring Sections

    > * [참고사이트] 
      - https://www.sphinx-doc.org/en/master/usage/extensions/napoleon.html
      - https://sphinxcontrib-napoleon.readthedocs.io/en/latest/
    
    ```
    。Args (alias of Parameters)
    。Arguments (alias of Parameters)
    。Attention
    。Attributes
    。Caution
    。Danger
    。Error
    。Example
    。Examples
    。Hint
    。Important
    。Keyword Args (alias of Keyword Arguments)
    。Keyword Arguments
    。Methods
    。Note
    。Notes
    。Other Parameters
    。Parameters
    。Return (alias of Returns)
    。Returns
    。Raise (alias of Raises)
    。Raises
    。References
    。See Also
    。Tip
    。Todo
    。Warning
    。Warnings (alias of Warning)
    。Warn (alias of Warns)
    。Warns
    。Yield (alias of Yields)
    。Yields
    
    ```

### <u>2. NumPy Docstring</u>

  - google vs numpy 비교
    - 두 문서 문자열의 출력은 유사,
    - 두 스타일의 주요 차이점은 구글이 섹션을 구분하기 위해 들여쓰기를 사용하는 반면, NumPy는 밑줄을 사용한다.
    - NumPy 스타일은 더 많은 수직 공간을 필요로 하는 반면, 구글 스타일은 더 많은 수평 공간을 사용한다.
    - 구글 스타일은 짧고 간단한 문서 문자열을 읽는 것이 더 쉽다
    - NumPy 스타일은 길고 심층적인 문서 문자열을 읽는 것이 더 쉽다.
  
  ```
  >> Numpy Docstring <<

    {{! Numpy Docstring Template }}
    {{summaryPlaceholder}}

    {{extendedSummaryPlaceholder}}
    {{#parametersExist}}

    Parameters
    ----------
    {{#args}}
    {{var}} : {{typePlaceholder}}
        {{descriptionPlaceholder}}
    {{/args}}
    {{#kwargs}}
    {{var}} : {{typePlaceholder}}, optional
        {{descriptionPlaceholder}}, by default {{&default}}
    {{/kwargs}}
    {{/parametersExist}}
    {{#returnsExist}}

    Returns
    -------
    {{#returns}}
    {{typePlaceholder}}
        {{descriptionPlaceholder}}
    {{/returns}}
    {{/returnsExist}}
    {{#yieldsExist}}

    Yields
    ------
    {{#yields}}
    {{typePlaceholder}}
        {{descriptionPlaceholder}}
    {{/yields}}
    {{/yieldsExist}}
    {{#exceptionsExist}}

    Raises
    ------
    {{#exceptions}}
    {{type}}
        {{descriptionPlaceholder}}
    {{/exceptions}}
    {{/exceptionsExist}} 
  ```

  ```
  def numpy_docstrings(num1, num2) -> int:
    """
    Add up two integer numbers.

    This function simply wraps the ``+`` operator, and does not
    do anything interesting, except for illustrating what
    the docstring of a very simple function looks like.

    Parameters
    ----------
    num1 : int
        First number to add.
    num2 : int
        Second number to add.

    Returns
    -------
    int
        The sum of ``num1`` and ``num2``.

    Raises
    ======
     MyException
        if anything bad happens

    See Also
    --------
    subtract : Subtract one integer from another.

    Examples
    --------
    >>> add(2, 2)
    4
    >>> add(25, 0)
    25
    >>> add(10, -10)
    0
    """
    return num1 + num2
  ```

### <u>3. Sphinx docstring</u>
  

  - Openstack 소스코드 작성에 모두 Sphinx 방식을 사용한다.
  - Pycharm 사용: Preference --> Tools --> Python Integrated Tools에서 설정한다.
  - Visual Code 사용: https://marketplace.visualstudio.com/items?itemName=njpwerner.autodocstring 플러그인 설치한다.
  - 작성 예제 사이트: https://sphinx-rtd-tutorial.readthedocs.io/en/latest/docstrings.html

  - 장점
    - sphinx-quickstart를 이용하여 필요한 디렉토리 구조 및 파일들을 생성해 준다.
      - Prokject, version, release, Langauge, file suffix, master doc name, epub support, Makefile, make.bat, conf.py, index.rst, docs directory 등
    - sphinx-apidoc으로 API 문서화가 가능하다
        ```
        $ sphinx-apidoc -F -o docs my_package/ --separate

        Creating file docs/my_package.rst.

        Creating file docs/my_package.my_module.rst.
        File docs/conf.py already exists, skipping.
        File docs/index.rst already exists, skipping.
        File docs/Makefile already exists, skipping.
        File docs/make.bat already exists, skipping.
        ```

  ```
  >> Sphinx Docstring <<

    {{! Sphinx Docstring Template }}
    {{summaryPlaceholder}}

    {{extendedSummaryPlaceholder}}

    {{#args}}
    :param {{var}}: {{descriptionPlaceholder}}
    :type {{var}}: {{typePlaceholder}}
    {{/args}}

    {{#kwargs}}
    :param {{var}}: {{descriptionPlaceholder}}, defaults to {{&default}}
    :type {{var}}: {{typePlaceholder}}, optional
    {{/kwargs}}
    
    {{#exceptions}}
    :raises {{type}}: {{descriptionPlaceholder}}
    {{/exceptions}}
    
    {{#returns}}
    :return: {{descriptionPlaceholder}}
    :rtype: {{typePlaceholder}}
    {{/returns}}
    
    {{#yields}}
    :yield: {{descriptionPlaceholder}}
    :rtype: {{typePlaceholder}}
    {{/yields}}

  ```

  ```
  def sphinx_docstrings(num1, num2) -> int:
    """Add up two integer numbers.

    This function simply wraps the ``+`` operator, and does not
    do anything interesting, except for illustrating what
    the docstring of a very simple function looks like.

    :param int num1: First number to add.
    :param int num2: Second number to add.
    :returns:  The sum of ``num1`` and ``num2``.
    :rtype: int
    :raises AnyError: If anything bad happens.
    """
    return num1 + num2
  ```
