로거 인터페이스
================

이 문서는 로깅 라이브러리에 대한 공통 인터페이스를 설명합니다.

가장 중요한 목표는 라이브러리가 `Psr\Log\LoggerInterface` 객체를 받아서 간단하고 보편적인 방법으로 로그를 쓸 수있게하는 것입니다.
커스텀 할 필요가 있는 프레임워크와 CMS는 자체적인 목적을 위해 인터페이스를 확장 할 수 있지만 이 문서와 호환 가능해야합니다 (SHOULD).
이렇게하면 프로그램에서 사용하는 타사 라이브러리가 프로그램의 중앙 집중식 로그에 쓸 수 있습니다. 

> 역자주: 타 벤더의 로그를 중앙집중형으로 모아서 남길 수 있다는 의미

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.

> 역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다

이 문서에서 `구현자(implementor)` 라는 단어는 로그 관련 라이브러리 또는 프레임워크에서 `LoggerInterface` 를 구현하는 누군가로 해석되어야합니다.
로거 사용자는 `사용자(user)`라고합니다.

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 1. 명세서

### 1.1 기본

- `LoggerInterface`는 로그를 8 개의 [RFC 5424] 레벨 (debug, info, notice, warning, error, critical, alert, emergency)에 맞게 쓸 수 있는 8 가지 method를 제공해야 합니다.
 
- 9 번째 method 인`log`는 첫 번째 인자로 로그 수준(level)을 입력받습니다.
  로그 수준 상수 중 하나를 사용하여 이 메서드를 호출하면 수준별 메서드를 호출 할 때와 동일한 결과를 가져야합니다.
  알지 못하는 수준(level)이거나 이 스펙에 정의되지 않은 수준(level)로 이 메소드를 호출하면 반드시 `Psr\Log\InvalidArgumentException` 을 던져야합니다 (MUST).
  사용자는 현재 구현이 이를 지원하는지 모른 채 사용자 지정 수준(level)을 사용해서는 안됩니다(SHOULD NOT).


[RFC 5424]: http://tools.ietf.org/html/rfc5424

### 1.2 메세지

- 모든 method는 문자열을 메시지로 받거나 `__toString ()` method를 가진 객체를 받아들입니다.
   구현자는 전달 된 객체를 특별한 형태로 처리 할 수도있다 (MAY).
   그렇지 않은 경우, 구현자는 그것을 문자열로 변환해야한다 (MUST).
   

- 메시지는 구현자가 문맥 배열의 값으로 대체 할 수 있는(MAY) Placeholder를 포함 할 수있습니다(MAY).
  Placeholder 이름은 컨텍스트 배열의 키와 일치해야합니다.
  Placeholder 이름은 하나의 여는 중괄호 `{` 와 하나의 닫는 중괄호 `}`로 구분해야합니다.
  구분 기호와 Placeholder 이름 사이에 공백이 없어야합니다 (MUST NOT).
  Placeholder 이름은 `A-Z`, `a-z`, `0-9`, 밑줄 `_` 및 마침표 `.` 만으로 구성되어야합니다 (SHOULD).
  다른 문자의 사용은 Placeholder 사양(specification)의 향후 수정을 위해 예약됩니다.
  구현자는 Placeholder를 사용하여 다양한 이스케이프 전략을 구현하고 표시를 위해 로그를 변환 할 수 있습니다 (MAY).
  사용자는 데이터가 표시 될 컨텍스트를 알 수 없으므로 Placeholder 값을 미리 이스케이프해서는 안됩니다.
 
  다음은 참조용으로만 제공되는 Placeholder 보간법의 구현 예입니다.

  ~~~php
  <?php

  /**
   * Interpolates context values into the message placeholders.
   */
  function interpolate($message, array $context = array())
  {
      // build a replacement array with braces around the context keys
      $replace = array();
      foreach ($context as $key => $val) {
          // check that the value can be casted to string
          if (!is_array($val) && (!is_object($val) || method_exists($val, '__toString'))) {
              $replace['{' . $key . '}'] = $val;
          }
      }

      // interpolate replacement values into the message and return
      return strtr($message, $replace);
  }

  // a message with brace-delimited placeholder names
  $message = "User {username} created";

  // a context array of placeholder names => replacement values
  $context = array('username' => 'bolivar');

  // echoes "User bolivar created"
  echo interpolate($message, $context);
  ~~~

### 1.3 문맥(Context)

- 모든 메소드는 배열을 컨텍스트 데이터로 허용합니다.
  이것은 문자열에 잘 맞지 않는 불필요한 정보를 보유하기위한 것입니다.
  배열은 무엇이든 포함 할 수 있습니다.
  구현자는 가능한 한 많은 관용으로 컨텍스트 데이터를 처리해야합니다 (MUST).
  문맥에서 주어진 값은 예외를 던지거나 PHP error, warning 또는 notice를 발생시켜서는 안됩니다(MUST NOT).

- `Exception` 객체가 컨텍스트 데이터에 전달되면, 그것은 `'exception'` 키에 있어야합니다 (MUST).
   로깅 예외(exceptios)는 일반적인 패턴이며, 이로 인해 구현자가 로그 백엔드가 지원할 때 예외에서 스택 추적을 추출 할 수 있습니다.
   구현자들은 무엇이든 포함 할 수 있기 때문에`'exception'` 키가 실제로 그것을 사용하기 전에 실제로 `Exception` 인지를 반드시 확인해야합니다 (MUST).


### 1.4 헬퍼 클래스와 인터페이스

- `Psr\Log\AbstractLogger` Class를 확장하고 일반적인 `log` 메소드를 구현함으로써 `LoggerInterface`를 매우 쉽게 구현할 수있게합니다.
   다른 8 가지 method는 메시지와 컨텍스트를 전달하는 것입니다.

- 마찬가지로 `Psr\Log\LoggerTrait` 만 사용하면 일반적으로 `log` 메소드를 구현해야합니다.
  이 trait은 인터페이스를 구현하지 않으므로 이 경우에는 여전히 `LoggerInterface`를 구현해야합니다.

- `Psr\Log\NullLogger`는 인터페이스와 함께 제공됩니다.
  로거가 제공되지 않으면 폴백 (back-back) "블랙홀"구현을 제공하기 위해 인터페이스 사용자가 이를 사용할 수 있습니다 (MAY).
  그러나 컨텍스트 데이터 작성 비용면에서 조건부 로깅이 더 나은 접근 방법 일 수 있습니다. 

  > 역자주: 아무것도 저장하지 않는 로거를 사용하는 것 보다 경우에 따라 로거를 호출 하도록 하는 편이 더 효율적


- `Psr\Log\LoggerAwareInterface` 는 `setLogger(LoggerInterface $logger)` method만을 포함하고 있으며, 임의의 인스턴스를 로거로 자동으로 연결하기 위해 프레임워크에서 사용할 수 있습니다.

- `Psr\Log\LoggerAwareTrait` trait은 모든 클래스에서 쉽게 동일한 인터페이스를 구현하는 데 사용할 수 있습니다.
  `$this->logger`에 접근 할 수 있습니다.

- `Psr\Log\LogLevel` 클래스는 8 개의 로그 레벨에 대한 상수를 가지고 있습니다.

## 2. Package

설명한 인터페이스와 클래스는 물론 관련 예외 클래스 및 구현을 확인하는 테스트가 [psr/log](https://packagist.org/packages/psr/log) 패키지의 일부로 제공됩니다.

## 3. `Psr\Log\LoggerInterface`

~~~php
<?php

namespace Psr\Log;

/**
 * Describes a logger instance.
 *
 * The message MUST be a string or object implementing __toString().
 *
 * The message MAY contain placeholders in the form: {foo} where foo
 * will be replaced by the context data in key "foo".
 *
 * The context array can contain arbitrary data, the only assumption that
 * can be made by implementors is that if an Exception instance is given
 * to produce a stack trace, it MUST be in a key named "exception".
 *
 * See https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md
 * for the full interface specification.
 */
interface LoggerInterface
{
    /**
     * System is unusable.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function emergency($message, array $context = array());

    /**
     * Action must be taken immediately.
     *
     * Example: Entire website down, database unavailable, etc. This should
     * trigger the SMS alerts and wake you up.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function alert($message, array $context = array());

    /**
     * Critical conditions.
     *
     * Example: Application component unavailable, unexpected exception.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function critical($message, array $context = array());

    /**
     * Runtime errors that do not require immediate action but should typically
     * be logged and monitored.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function error($message, array $context = array());

    /**
     * Exceptional occurrences that are not errors.
     *
     * Example: Use of deprecated APIs, poor use of an API, undesirable things
     * that are not necessarily wrong.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function warning($message, array $context = array());

    /**
     * Normal but significant events.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function notice($message, array $context = array());

    /**
     * Interesting events.
     *
     * Example: User logs in, SQL logs.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function info($message, array $context = array());

    /**
     * Detailed debug information.
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function debug($message, array $context = array());

    /**
     * Logs with an arbitrary level.
     *
     * @param mixed $level
     * @param string $message
     * @param array $context
     * @return void
     */
    public function log($level, $message, array $context = array());
}
~~~

## 4. `Psr\Log\LoggerAwareInterface`

~~~php
<?php

namespace Psr\Log;

/**
 * Describes a logger-aware instance.
 */
interface LoggerAwareInterface
{
    /**
     * Sets a logger instance on the object.
     *
     * @param LoggerInterface $logger
     * @return void
     */
    public function setLogger(LoggerInterface $logger);
}
~~~

## 5. `Psr\Log\LogLevel`

~~~php
<?php

namespace Psr\Log;

/**
 * Describes log levels.
 */
class LogLevel
{
    const EMERGENCY = 'emergency';
    const ALERT     = 'alert';
    const CRITICAL  = 'critical';
    const ERROR     = 'error';
    const WARNING   = 'warning';
    const NOTICE    = 'notice';
    const INFO      = 'info';
    const DEBUG     = 'debug';
}
~~~
