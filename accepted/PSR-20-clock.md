Common Interface for Accessing the Clock
========================================

이 문서는 시스템의 시간을 읽기 위한 간단한 인터페이스에 대해 설명합니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다.
이것은 [RFC 2119](http://tools.ietf.org/html/rfc2119)에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

최종 구현은 제안된 것보다 더 많은 기능을 가진 개체로 만들 수도 있지만, 표시한 인터페이스/기능을 먼저 구현해야 합니다.

# 1. 명세서

## 1.1 소개

시간에 접근하기위한 표준 메서드를 만들면, 시점에 기반한 사이드이펙트가 있는 동작을 테스트할 때, 상호 운용성을 가지게 됩니다.
`역자주: 특정한 시간에만 동작하는 코드를 테스트할 때, 강제로 시간을 바꿔서 해당 코드가 동작하거나 하지 않는 테스트를 하기 좋아진다는 의미입니다`

일반적으로 현재 시간을 가져오는 방법으로는 `\time()` 또는 `new \DateTimeImmutable('now')`을 사용하는 것이 포함됩니다.
그러나 이것은 일부 상황에서 현재 시간을 모킹-mocking하는 것을 불가능하게 만듭니다.
`역자주: 이 메서드들은 강제로 시간을 바꾸는 기능을 지원하지 않습니다. 따라서 시점에 따른 코드의 동작을 테스트 할 수 없습니다`

## 1.2 정의

* **Clock** - clock은 현재 시간과 날짜를 읽을 수 있습니다.

* **Timestamp** - Jan 1, 1970 00:00:00 UTC 이후의 초-second로 표시되는 현재 시간

### 1.3 사용법

**현재 타임스탬프 가져오기**

이 작업은 반환된 `\DateTimeImmutable`에서 `getTimestamp()` 메서드를 사용해서 처리해야 합니다.

```php
$timestamp = $clock->now()->getTimestamp();
```

# 2. Interfaces

## 2.1 ClockInterface

clock 인터페이스는 clock에서 현재 시간과 날짜를 읽는 가장 기본적인 동작을 정의합니다. 

이것은 반드시 `\DateTimeImmutable`로 시간을 반환해야 합니다 (MUST).

```php
<?php

namespace Psr\Clock;

interface ClockInterface
{
    /**
     * Returns the current time as a DateTimeImmutable Object
     */
    public function now(): \DateTimeImmutable;

}
```
