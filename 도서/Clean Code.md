# 목차
- [1장 깨끗한 코드](#1장-깨끗한-코드)
- [2장 의미 있는 이름](#2장-의미-있는-이름)
- [3장 함수](#3장-함수)
- [4장 주석](#4장-주석)
- [5장 형식 맞추기](#5장-형식-맞추기)
- [6장 객체와 자료 구조](#6장-객체와-자료-구조)
- [7장 오류 처리](#7장-오류-처리)
- [8장 경계](#8장-경계)
- [9장 단위 테스트](#9장-단위-테스트)
- [10장 클래스](#10장-클래스)
- [11장 시스템](#11장-시스템)
- [12장 창발성](#12장-창발성)
- [13장 동시성](#13장-동시성)
- [14장 점진적인 개선](#14장-점진적인-개선)
- [15장 JUnit 들여다보기](#15장-JUnit-들여다보기)
- [16장 SerialDate 리팩터링](#16장-SerialDate-리팩터링)
- [17장 냄새와 휴리스틱](#17장-냄새와-휴리스틱)
# 1장 깨끗한 코드
깨끗한 코드란
- 효율적인 코드
- 한 가지를 잘 하는 코드
- 가독성이 좋은 코드
- 다른 사람이 고치기 쉬운 코드
- 중복을 피하고 작게 추상화한 코드  

보이스카우트 규칙 
- 캠프장은 처음 왔을 때보다 더 깨끗하게 해놓고 떠나라
# 2장 의미 있는 이름
- 의도를 분명히 밝혀라  
- 그릇된 정보를 피하라
    - 실제 그 자료구조를 사용하지 않았다면 이름에 넣지 않는다
        - ex) List 자료구조를 쓰지 않았는데 accountList라고 짓지 말기
    - l (소문자 L)이나 O (대문자 o) 사용하지 말기
- 의미 있게 구분하라
    - ex) NameString은 Name보다 의미적으로 나은 점이 없다
- 발음하기 쉬운 이름을 사용하라
- 검색하기 쉬운 이름을 사용하라
    - 이름 길이는 범위 크기에 비례해야 한다
    - 자주 쓰는 정수는 const로 이름을 설정
- 클래스 이름
    - 명사나 명사구가 적합하다
- 메서드 이름
    - 동사나 동사구가 적합하다
- 한 개념에 한 단어를 사용하라
    - ex) 가져온다는 개념에 get, fetch, retrieve 등 여러개를 사용하지 않는다
- 해법 영역에서 가져온 이름을 사용하라
- 의미 있는 맥락을 추가하라
    - 클래스, 함수, 이름 공간에 넣어 맥락을 추가한다
- 불필요한 맥락을 없애라
# 3장 함수
- 작게 만들어라
    - 가능하면 4줄 이내로
    - if / else / while 등에 들어가는 블록은 한 줄이 적합
    - 들여쓰기가 1단이나 2단을 넘지 않도록 한다
- 한 가지만 해라
    - 추상화 수준이 하나여야 한다
    - 함수의 내용을 다른 함수로 만들 수 있다면 여러 일을 하는 것이다
- 서술적인 이름을 사용하라
- 함수 인수
    - 0항이 가장 이상적이다
    - 1항은 인수에 질문을 던지거나 인수를 변환해 리턴하는 경우만 사용한다
    - 2항은 사용해야 한다면 인수의 순서를 잘 알 수 있게 함수명을 지어 사용하자
    - 3항부터는 되도록 사용하지 말고 사용해야 한다면 여러 인수를 독자적인 클래스 변수로 선언하는걸 고려한다
        - ex) (double x, double y, double radius) -> (Point center, double radius)
- 부수 효과를 일으키지 마라
    - 함수 이름에서 유추할 수 없는 부수 효과를 만들지 말자
- 출력 인수
    - 출력 인수는 사용하지 말고 this를 이용해 객체 상태를 변경한다
- 명령과 조회를 분리하라
    - 명령(뭔가를 수행)과 조회(뭔가에 답함) 중 하나만 해야한다
- 오류 코드보다 예외를 사용하라
    - 예외를 사용하고, 예외가 발생한 부분과 발생하지 않은 부분을 각각 함수로 나타내면 코드가 이해하기 쉬워진다
- 반복하지 마라
# 4장 주석
**주석은 나쁜 코드를 보완하지 않는다(최대한 코드를 잘 짜자)**
### 좋은 주석
- 법적인 주석
- 정보를 제공하는 주석
- 의도를 설명하는 주석
- 의미를 명료하게 밝히는 주석
- 결과를 경고하는 주석
- TODO 주석
- 중요성을 강조하는 주석
### 나쁜 주석
- 같은 이야기를 중복하는 주석(코드보다 많은 내용을 제공하지 않는)
- 오해할 여지가 있는 주석
- 이력을 기록하는 주석, 공로를 돌리는 주석
    - 대신 version control을 사용하자
- 위치를 표시하는 주석
    - 반드시 필요할 때만 사용한다
- 닫는 괄호에 다는 주석
- 전역 정보를 기술하는 주석
    - 주석을 달아야 한다면 근처에 있는 코드만 설명해야 한다
# 5장 형식 맞추기
- 적절한 행 길이를 유지하라
    - 대체로 파일 하나당 200줄 미만이 적당
    - 서로 밀집한 개념은 세로로 가까이 둔다
- 변수는 사용하는 위치에 최대한 가까이 선언한다
    - 짧은 함수에서 지역변수는 맨 처음에 선언한다
    - 드물지만 긴 함수에서 블록이나 루프 전에 선언한다
    - 예외: **인스턴스 변수**의 경우 항상 클래스의 맨 처음에 선언한다
- 함수는 종속 관계에 있거나 개념적으로 유사하면 세로로 가까이 둔다
    - 호출하는 함수를 호출되는 함수보다 먼저 배치한다
- 한 행은 짧을수록 좋고 대부분 80자 이하이며 120자를 넘지 않게 한다
- 가로 정렬은 안하는게 더 좋다
- 짧은 코드라도 들여쓰기는 하는게 좋다
# 6장 객체와 자료구조
- 자료를 세세히 공개하기보다 추상화하는것이 좋다
- 자료/객체 비대칭
    - 절차적인 코드는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다
    - 객체지향코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다
- 디미터 법칙
    - 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다
- 절차적과 객체적이 합쳐진 구조는 잡종 구조로 피하는게 좋다
# 7장 오류 처리
- 오류 코드보다 예외를 사용하라
- Try-Catch-Finally 문부터 작성하라
    - 강제로 예외를 일으키는 테스트 케이스를 작성하고 이를 해결하는 방향 권장(TDD)
- 확인된(checked) 예외뿐만 아니라 미확인(unchecked) 예외를 사용하라
- 예외가 생기지 않게 클래스나 객체를 통해 캡슐화해도 좋다
    - 이를 특수 사례 패턴이라 한다
- null을 반환하지 마라
    -  리턴값이 null인지 확인하기 번거로우므로 대신 예외나 특수 사례 객체를 반환한다
- null을 (인수로) 전달하지 마라
# 8장 경계
- 경계 살피고 익히기
    - 외부 코드를 사용할 때는 간단한 테스트 케이스를 작성해 외부 코드를 익히는게 좋다
    - 이를 학습 테스트라고 한다
# 9장 단위 테스트
- TDD 법칙 세 가지
    1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다
    2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다
    3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다
- 깨끗한 테스트 코드
    - 가독성이 제일 중요하다
    - 테스트 코드는 효율보다는 가독성에 중점을 둔다
- 테스트 함수 하나당 하나의 개념만 테스트하고 assert는 최대한 줄인다
- F.I.R.S.T.
    - Fast: 테스트는 빨라야 한다
    - Independent: 각 테스트는 서로 의존하면 안된다
    - Repeatable: 테스트는 어떤 환경에서도 반복 가능해야 한다
    - Self-Validating: 테스트는 bool 값으로 결과를 내야 한다
    - Timely: 테스트는 테스트하려는 실제 코드를 작성하기 직전에 구현한다
# 10장 클래스
- 클래스 체계
    - static public 변수, static private 변수, 인스턴스 변수, public 함수, private 함수 순으로 작성한다
- 클래스는 작아야 한다!
    - SRP: 클래스를 **변경할 이유**가 하나뿐이여야 한다는 원칙
    - 큰 클래스 몇개가 아니라 작은 클래스 여럿으로 이루어진 시스템이 더 바람직하다
# 11장 시스템
- 시스템 제작과 시스템 사용을 분리하라
# 12장 창발성
- **단순한 설계** 규칙 네 가지를 따르자(중요도 순)
    - 모든 테스트를 실행한다
    - 중복을 없앤다
    - 프로그래머 의도를 표현한다
    - 클래스와 메서드 수를 최소로 줄인다
# 13장 동시성
- 동시성 방어 원칙
    - 스레드를 아는 코드(다중 스레드 사용)와 스레드를 모르는 코드를 분리한다
    - 동기화하는 부분을 작게 만들어라(세마포어, 뮤텍스)
    - 많은 플랫폼에서 여러번 테스트한다
# 14장 점진적인 개선
# 15장 JUnit 들여다보기
# 16장 SerialDate 리팩터링
- 책의 코드를 다시 확인하자(14장 ~ 16장)
# 17장 냄새와 휴리스틱
### 환경
- 빌드는 간단히 한 단계로 끝나야 한다
- 모든 단위 테스트는 한 명령으로 돌려야 한다
### 함수
- 플래그 인수는 피한다
### 일반
- 한 소스 파일에 최대한 적은 언어를 사용한다
- 최소 놀람의 법칙에 의거해 이름 등에서 유추할 수 있는 당연한 동작을 구현해야 한다
- 중복된 코드를 추상화로 정리해서 해결한다
- 변수와 함수는 사용되는 위치에 가깝게 정의한다
- 함수에서 선택자 인수(true, false) 사용을 지양한다
- 매직 숫자는 명명된 상수로 교체하라
- 부정 조건은 피하라
### 이름
- 긴 범위는 긴 이름을 사용하라
### 테스트
- 실패 패턴을 살펴라
- 테스트는 빨라야 한다
