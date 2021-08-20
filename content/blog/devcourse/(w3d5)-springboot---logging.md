---
title: (W3D5) SpringBoot - Logging
date: 2021-08-21 00:08:40
category: devcourse
draft: false
---

## Logging

시스템을 작동할 때 시스템의 작동 상태의 기록과 보존, 이용자의 습성 조사 및 시스템 동작의 분석 등을 하기 위해 작동 중의 각종 정보를 기록해 둘 필요가 있다. 이 기록을 만드는 것을 로깅이라고 한다.

Loggin Framework

- Logback
- SLF4J : Facade Pattern을 이용한 Logging Framework

SLF4F : 다양한 로깅 프레임워크를 지원하는데 바인딩 모듈을 통해서 처리된다. 바인딩 모듈은 로깅 프레임워크를 연결하는 역할을 한다.

애플리케이션은 SLF4J를 이용해서 로깅 라이브러리가 어떤 것이든 같은 방법으로 로그를 남기게 해준다.

필요한 만큼의 로그를 남긴다.

로그를 남기는 행위 자체도 성능에 영향을 미친다. -> Log level을 바꿔가면서 로그를 저장한다.

실제 로그 기록하는 행위를 Logger를 통해서 한다. Logger는 이름 기반으로 생성이 된다.

package 별로 log level 설정

### Logback 설정

logback 설정을 통해서 로깅 설정

다양한 파일 읽을 수 있고 순서로 읽을 수 있다.

1. logback-test.xml 파일을 찾는다. -> test/resources/
2. 없으면 logback.groovy 찾는다.
3. 없으면 logback.xml 찾는다.
4. 모두 없으면 기본 설정 전략을 따른다.

### Log Appender 설정

- ConsoleAppender
- FileAppender
- RollingFileAppender

appender는 어디에, 어떻게 로그를 찍을 것인지에 대한 설정을 하는 부분이다.
logger는 appender들을 참조하며 해당 logger가 사용될 패키지와 log level을 지정한다.

### PatternLayout

Logback이 기본적으로 제공하는 패턴레이아웃을 이용하면 로거에서 만든 로깅 이벤트를 가지고 문자열을 변환할 수 있다. 문자열로 변환될 때 conversion specifiers 라는 것을 이용한다.

1. %d - 로깅 이벤트의 날짜를 출력한다.
2. %logger{length} - Logger name의 이름을 축약할 수 있다. {length}는 최대 자릿수
3. %thread - 현재 Thread name
4. %-5level - log level. -5는 출력폭 고정값
5. %msg - log message
6. %n - new line

일별로 로그파일을 구분한다. -> RollingFileAppender 설정