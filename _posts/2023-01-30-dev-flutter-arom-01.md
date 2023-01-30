---
layout: post
title: "Flutter 개발자 가이드 01"
date: 2023-01-09 13:15:00 +0900
categories: [Blogging]
tags: [flutter]
---

## Naming convention

> Dart는 일단 카멜케이스가 기본. 카멜케이스를 기본으로 클래스 관련 네이밍에는 파스칼케이스를 사용하고, 폴더/파일명에는 스네이크케이스를 사용한다.

### snake_case

- folders/files
- 라이브러리, 패키지, 디렉토리, 소스파일 등

``` dart
library peg_parser.source_scanner;

import 'file_system.dart';
import 'slider_menu.dart';
```

``` dart
import 'dart:math' as math;
import 'package:angular_components/angular_components.dart' as angular_components;
import 'package:js/js.dart' as js;
```

### camelCase

- functions
- variables
- constants

``` dart
var count = 3;

HttpRequest httpRequest;

void align(bool clearItems) {
  // ...
}
```

상수 이름이나, enum 등에 lowerCamelCase를 사용한다.(c, java등과 다름)

``` dart
// good
const pi = 3.14;
const defaultTimeout = 1000;
final urlScheme = RegExp('^([a-z]+):');

class Dice {
  static final numberGenerator = Random();
}

// bad
const PI = 3.14;
const DefaultTimeout = 1000;
final URL_SCHEME = RegExp('^([a-z]+):');

class Dice {
  static final NUMBER_GENERATOR = Random();
}
```

### PascalCase

- classes
- extensions
- mixins

``` dart
class SliderMenu { ... }

class HttpRequest { ... }

typedef Predicate<T> = bool Function(T value);

extension MyFancyList<T> on List<T> { ... }
```
