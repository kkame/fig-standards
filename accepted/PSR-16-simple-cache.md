Common Interface for Caching Libraries
======================================

이 문서는 캐시 항목과 캐시 드라이버에 대한 간단하면서도 확장 가능한 인터페이스를 설명합니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.

> 역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다

최종 구현체는 제시된 것보다 더 많은 기능을 가진 객체로 만들 수 있지만(MAY) 반드시 지정한 인터페이스/기능을 먼저 구현해야합니다(MUST).

[RFC 2119]: http://tools.ietf.org/html/rfc2119

# 1. 명세서

## 1.1 소개

캐싱은 모든 프로젝트의 성능을 향상시키는 일반적인 방법이며, 캐싱 라이브러리를 많은 프레임 워크 및 라이브러리의 가장 일반적인 기능 중 하나로 만듭니다.
여기서 말하는 상호 운용성은 라이브러리가 자체 캐싱 구현을 중단하고 프레임 워크 또는 다른 캐시 전용 라이브러리에 의해 제공되는 구현에 쉽게 의지 할 수 있음을 의미합니다.
 
PSR-6은 이미 이 문제를 해결했지만 단순한 유스 케이스에 필요한 이상으로 공식적이고 장황한 방법으로 해결합니다.
이 간단한 접근 방식은 일반적인 경우에 대해 표준화와 간소화 된 인터페이스를 구축하는 것을 목표로합니다.
이것은 PSR-6과 독립적이지만 PSR-6과의 호환성을 가진채 가능한 한 간단하게 만들도록 설계되었습니다.

## 1.2 정의

호출 라이브러리, 구현 라이브러리, TTL, 만료 및 키에 대한 정의는 PSR-6에서 복사 한 것과 동일한 것으로 가정합니다

*    **호출 라이브러리(Calling Library)** - 실제로 캐시 서비스가 필요한 라이브러리 또는 코드.
                       이 라이브러리는이 표준의 인터페이스를 구현하는 캐싱 서비스를 활용할 것이며, 그렇지 않으면 이러한 캐싱 서비스의 구현에 대한 알지 못합니다. 
     > 역자주: 인터페이스를 구현한 캐싱 서비스를 사용만하고 구현을 하지 않는다.

*    **구현 라이브러리(Implementing Library)** - 이 라이브러리는 모든 호출 라이브러리에 캐싱 서비스를 제공하기 위해이 표준을 구현합니다.
                        구현 라이브러리는 `Cache\CacheItemPoolInterface` 및 `Cache\CacheItemInterface` 인터페이스를 구현하는 클래스를 제공해야합니다(MUST).
                        라이브러리 구현은 전체 초 단위로 아래에 설명 된 최소 TTL 기능을 지원해야만 합니다 (MUST).

*    **TTL** - TTL (Time To Live)은 해당 항목이 저장하고 보관하는 것으로 간주되는 시간입니다.
               TTL은 일반적으로 초 단위의 시간을 나타내는 정수 또는 DateInterval 개체로 정의됩니다.

*    **만료(Expiration)** - 항목이 만료로 설정되는 실제 시간입니다. 
이것은 일반적으로 개체가 저장된 시간에 TTL을 추가하여 계산됩니다.
이것은 일반적으로 개체가 저장된 시간에 TTL을 추가하여 계산되지만 DateTime 개체로 명시 적으로 설정할 수도 있습니다.
1:30:00에 저장된 300 초 TTL을 가진 항목의 만료 시간은 1:35:00입니다.
구현 라이브러리는 요청한 만료 시간 전에 항목을 만료시킬 수도 있지만(MAY) 만료 시간에 도달하면 만료 된 것으로 간주해야합니다(MUST).
호출 라이브러리가 항목을 저장요청을 하지만 만료 시간을 지정하지 않거나 만료 시간 또는 TTL을 null로 지정되어 있다면 구현 라이브러리는 설정한 기본 기간을 사용할 수 있습니다 (MAY).
기본 기간이 설정되지 않은 경우 구현 라이브러리는 해당 항목을 영원히 캐시하는 요청으로 해석하거나 기본 구현이 지원하는 동안 저장하는 것으로 해석해야합니다(MUST).

*    **Key** - 캐시 된 항목을 고유하게 식별하는 적어도 하나의 문자로 구성된 문자열입니다.
구현 라이브러리는`A-Z`,`a-z`,`0-9`,`_` 및`.` 문자로 구성된 키를 UTF-8 인코딩과 길이가 64 문자 이하의 순서로 지원해야합니다 (MUST).
구현 라이브러리은 추가 문자와 인코딩 또는 더 긴 길이를 지원할 수 있지만(MAY) 최소한 그 최소값을 지원해야합니다(MUST).
라이브러리는 적절하게 키 문자열을 이스케이프 처리해야하지만 원래의 수정되지 않은 키 문자열을 반환 할 수 있어야합니다(MUST).
다음 문자는 미래의 확장을 위해 예약되어 있으며, 구현 라이브러리에서 지원해서는 안됩니다(MUST NOT) :`{} () / \ @ :`

*    **Cache** - `Psr\SimpleCache\CacheInterface` 인터페이스를 구현하는 객체.

*    **Cache Misses** - 캐시 미스 (cache miss)는 널(null)을 리턴 할 것이고 따라서 `null`이 하나만 저장된 경우 구별이 불가능합니다.
이것은 PSR-6와 가장 큰 차이입니다.

## 1.3 Cache

구현체는 특정 캐시 항목에 대해 TTL을 지정되지 않은 경우 사용자가 기본 TTL을 지정하는 메커니즘을 제공 할 수 있습니다 (MAY).
사용자 지정 기본값도 제공되지 않으면 구현체은 기본 구현체에서 허용되는 최대 유효 값으로 기본 설정되어야합니다(MUST).
기본 구현체가 TTL을 지원하지 않으면 사용자 지정 TTL을 자동으로 무시해야합니다 (MUST).

## 1.4 Data

라이브러리를 구현할 때 다음을 포함하여 모든 직렬화 가능한 PHP 데이터 유형을 지원해야합니다 (MUST).

*    **Strings** - PHP 호환 인코딩의 임의의 크기를 갖는 문자열.
*    **Integers** - PHP가 지원하는 모든 크기의 정수 (최대 64 비트).
*    **Floats** - 부호가 있는(signed) 모든 부동소수점 값.
*    **Booleans** - True 와 False.
*    **Null** - null값 (캐시미스와 구별이 되지 않습니다).
*    **Arrays** - 인덱스된 임의의 깊이를 가진 연관 및 다차원 배열
*    **Objects** - `$o == unserialize(serialize($o))` 와 같이 무손실 직렬화 및 직렬화 해제를 지원하는 객체.
객체는 PHP의 Serializable 인터페이스, 적절한 경우 `__sleep()` 또는 `__wakeup()` 매직 메서드 또는 유사한 언어 기능을 활용할 수 있습니다(MAY).

구현 라이브러리에 전달 된 모든 데이터는 전달 된 그대로 정확하게 반환되어야합니다(MUST).
여기에는 가변 유형이 포함됩니다.
즉, 만약 (int) 5가 저장된 값이었던 경우 (string) 5를 반환하는 것은 오류입니다.
구현 라이브러리는 PHP의 serialize()/unserialize() 함수를 내부적으로 사용할 수 있지만 꼭 그렇게 할 필요는 없습니다(MAY).
이들과의 호환성은 수용 가능한 객체 값의 기준선으로 사용됩니다.

어떠한 이유로든 저장된 정확한 값을 반환 할 수없는 경우 구현 라이브러리는 데이터가 손상되지 않게 캐시 미스로 응답해야합니다(MUST).

# 2. Interfaces

## 2.1 CacheInterface

캐시 인터페이스는 각 캐시 항목의 기본 읽기, 쓰기 및 삭제를 수반하는 캐시 항목 모음에 대한 가장 기본적인 작업을 정의합니다.

또한 한 번에 여러 캐시 항목을 쓰거나, 읽거나 삭제하는 것과 같은 캐시 항목의 여러 세트를 처리하기위한 방법을 제공합니다.
이는 수행 할 캐시 읽기 / 쓰기가 많을 때 유용하며 대기 시간을 대폭 줄이는 캐시 서버에 대한 단일 호출로 작업을 수행 할 수 있습니다.

CacheInterface의 인스턴스는 단일 키 네임 스페이스가있는 캐시 항목의 단일 모음에 해당하며 PSR-6의 "풀"과 동일합니다.
서로 다른 CacheInterface 인스턴스는 동일한 데이터 저장소에 의해 백업 될 수 있지만 (MAY) 반드시 논리적으로 독립적이어야합니다 (MUST).

~~~php
<?php

namespace Psr\SimpleCache;

interface CacheInterface
{
    /**
     * Fetches a value from the cache.
     *
     * @param string $key     The unique key of this item in the cache.
     * @param mixed  $default Default value to return if the key does not exist.
     *
     * @return mixed The value of the item from the cache, or $default in case of cache miss.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function get($key, $default = null);

    /**
     * Persists data in the cache, uniquely referenced by a key with an optional expiration TTL time.
     *
     * @param string                 $key   The key of the item to store.
     * @param mixed                  $value The value of the item to store. Must be serializable.
     * @param null|int|\DateInterval $ttl   Optional. The TTL value of this item. If no value is sent and
     *                                      the driver supports TTL then the library may set a default value
     *                                      for it or let the driver take care of that.
     *
     * @return bool True on success and false on failure.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function set($key, $value, $ttl = null);

    /**
     * Delete an item from the cache by its unique key.
     *
     * @param string $key The unique cache key of the item to delete.
     *
     * @return bool True if the item was successfully removed. False if there was an error.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function delete($key);

    /**
     * Wipes clean the entire cache's keys.
     *
     * @return bool True on success and false on failure.
     */
    public function clear();

    /**
     * Obtains multiple cache items by their unique keys.
     *
     * @param iterable $keys    A list of keys that can obtained in a single operation.
     * @param mixed    $default Default value to return for keys that do not exist.
     *
     * @return iterable A list of key => value pairs. Cache keys that do not exist or are stale will have $default as value.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $keys is neither an array nor a Traversable,
     *   or if any of the $keys are not a legal value.
     */
    public function getMultiple($keys, $default = null);

    /**
     * Persists a set of key => value pairs in the cache, with an optional TTL.
     *
     * @param iterable               $values A list of key => value pairs for a multiple-set operation.
     * @param null|int|\DateInterval $ttl    Optional. The TTL value of this item. If no value is sent and
     *                                       the driver supports TTL then the library may set a default value
     *                                       for it or let the driver take care of that.
     *
     * @return bool True on success and false on failure.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $values is neither an array nor a Traversable,
     *   or if any of the $values are not a legal value.
     */
    public function setMultiple($values, $ttl = null);

    /**
     * Deletes multiple cache items in a single operation.
     *
     * @param iterable $keys A list of string-based keys to be deleted.
     *
     * @return bool True if the items were successfully removed. False if there was an error.
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if $keys is neither an array nor a Traversable,
     *   or if any of the $keys are not a legal value.
     */
    public function deleteMultiple($keys);

    /**
     * Determines whether an item is present in the cache.
     *
     * NOTE: It is recommended that has() is only to be used for cache warming type purposes
     * and not to be used within your live applications operations for get/set, as this method
     * is subject to a race condition where your has() will return true and immediately after,
     * another script can remove it, making the state of your app out of date.
     *
     * @param string $key The cache item key.
     *
     * @return bool
     *
     * @throws \Psr\SimpleCache\InvalidArgumentException
     *   MUST be thrown if the $key string is not a legal value.
     */
    public function has($key);
}
~~~

## 2.2 CacheException

~~~php

<?php

namespace Psr\SimpleCache;

/**
 * Interface used for all types of exceptions thrown by the implementing library.
 */
interface CacheException
{
}
~~~

## 2.3 InvalidArgumentException

~~~php
<?php

namespace Psr\SimpleCache;

/**
 * Exception interface for invalid cache arguments.
 *
 * When an invalid argument is passed it, must throw an exception which implements
 * this interface.
 */
interface InvalidArgumentException extends CacheException
{
}
~~~
