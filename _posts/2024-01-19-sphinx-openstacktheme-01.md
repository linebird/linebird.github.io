---
layout: post
title: "Python문서화 테스트 - Sphinx & openstacktheme"
date: 2024-01-19 19:20:00 +0900
categories: [SCore]
tags: [openstack, sphinx]
---

## 1. Sphinx 설치
Shell 명령으로 sphinx와 sphinx 테마 관련 라이브러리를 설치
```
# Sphinx 설치
pip install sphinx
# Sphinx 오픈스택 테마 설치
pip install openstackdocstheme
```

## 2.Python 소스 코드 관련 문서 만들기

### docs/ 만들기

다음과 같은 shell 명령을 이용해 Python 프로젝트 루트 경로 하위에 docs 경로를 생성한다.

```
sphinx-quickstart docs
```

위 명령을 실행하면 아래와 같이 몇 가지 옵션을 선택하는 과정이 실행된다. 우선 아래와 같이 간단하게 옵션을 선택해주면 프로젝트 루트 아래에 docs 경로가 만들어진다.


### src/my_package 만들기

프로젝트 루트 경로에 src라는 폴더를 하나 만들고, 그 아래에 Python 예제 모듈인 my_package를 만든다. 문서 생성과 웹 배포까지 정상적으로 테스트한 뒤에는 예제 모듈 대신 실제 문서화할 비즈니스 로직으로 대체하면 된다

### docs/source/conf.py 수정하기

conf.py 파일을 열면 아래 `# before` 와 같이 extensions는 빈 리스트, html_theme은 alabaster로 작성되어 있는 것을 확인할 수 있다. 이 값을 `# after` 에 적혀 있는 값으로 대체한다. Sphinx의 확장 기능과 문서 페이지의 테마를 설정해주는 부분이다. 확장 기능을 사용하지 않았을 때에는 빌드 오류가 발생할 수도 있다.
```
# Before
extensions = []
html_theme = 'alabaster'

# After
extensions = ['sphinx.ext.autodoc',  'openstackdocstheme']
html_theme = 'openstackdocs'
```

conf.py 파일 가장 하단에 아래 내용을 추가해준다. Python 소스 코드의 경로를 인식할 수 있도록 설정해준다.
```
## 추가
import os
import sys
sys.path.insert(0, os.path.abspath('../../src/my_package'))
```

### rst(Re-Structured-Text) 파일 만들기

프로젝트 루트 경로에서 다음 shell 명령을 실행하면 docs/source/ 경로 하위에 rst 파일들이 만들어진다.

```
sphinx-apidoc -f -o docs/source/ src/my_package
```

### docs/source/index.rst 파일 수정하기

모든 Python 모듈을 포함하기 위해서 index.rst에 modules를 아래와 같이 추가한다.
```
.. sphinx-test documentation master file, created by
   sphinx-quickstart on Thu Jan 18 19:22:30 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to sphinx-test's documentation!
=======================================
.. toctree::
   :maxdepth: 2
   :caption:

   modules

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```

### 문서 페이지 만들기

이제 docs 경로에서 shell 명령을 이용해 HTML 형식으로 구성된 문서 페이지들을 만든다.
아래와 같이 'distutils'가 없다고 에러 발생
```
PS C:\Projects\SCore\workspace\privates\sphinx-test> cd docs
PS C:\Projects\SCore\workspace\privates\sphinx-test\docs> ./make html
Running Sphinx v7.2.6
loading translations [ko]... done

Extension error:
Could not import extension openstackdocstheme (exception: No module named 'distutils')
```
위의 문제 해결을  위해, setuptools 설치
```
PS C:\Projects\SCore\workspace\privates\sphinx-test\docs> pip install setuptools
```
설치 후, make html 실행하면, 문서가 에러
```
PS C:\Projects\SCore\workspace\privates\sphinx-test\docs> ./make html
Running Sphinx v7.2.6
loading translations [ko]... done
[openstackdocstheme] version: 3.2.0
[openstackdocstheme] connecting html-page-context event handler
[openstackdocstheme] could not find a setup.cfg to extract project name from
[openstackdocstheme] could not find a setup.cfg to extract project name from
loading pickled environment... done
[openstackdocstheme] using theme from C:\Users\User\AppData\Local\Programs\Python\Python312\Lib\site-packages\openstackdocstheme\theme
[openstackdocstheme] no C:/Projects/SCore/workspace\.gitreview found
building [mo]: targets for 0 po files that are out of date
writing output... 
building [html]: targets for 3 source files that are out of date
updating environment: [extensions changed ('openstackdocstheme')] 3 added, 0 changed, 0 removed
reading sources... [100%] modules
looking for now-outdated files... none found
checking consistency... done
preparing documents... done
copying assets... copying static files... done
copying extra files... done
done
fatal: ambiguous argument 'HEAD': unknown revision or path not in the working tree.
Use '--' to separate paths from revisions, like this:
'git <command> [<revision>...] -- [<file>...]'
[openstackdocstheme] could not determine last_updated for 'base'
[openstackdocstheme] could not determine last_updated for 'index'
[openstackdocstheme] could not determine last_updated for 'modules'

WARNING: [openstackdocstheme] cannot get gitsha from git repository
generating indices... genindex py-modindex [openstackdocstheme] could not determine last_updated for 'py-modindex'
done
writing additional pages... search done
dumping search index in English (code: en)... done
dumping object inventory... done
build succeeded, 1 warning.

The HTML pages are in build\html.
```
