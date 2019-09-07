# Caching Interface

캐싱은 모든 프로젝트의 성능을 향상시키는 일반적인 방법이며, 많은 프레임워크 및 라이브러리는 캐싱 라이브러리를 기본 기능 중 하나로 만들게 됩니다.
이로 인해 많은 라이브러리가 다양한 수준의 기능을 갖춘 자체 캐싱 라이브러리를 사용하게 되었습니다.
이러한 상황으로 인해 개발자는 필요한 기능을 제공 할 수도 있고 제공하지 못할 수도 있는 여러 시스템을 배워야합니다.
또한 라이브러리를 캐싱하는 개발자는 제한된 수의 프레임워크 만 지원하거나 많은 수의 어댑터 클래스를 만드는 것 중에서 선택해야합니다.

캐싱 시스템을 위한 공통 인터페이스는 이러한 문제를 해결할 것 입니다.
라이브러리 및 프레임워크 개발자는 의도한대로 동작하는 캐싱 시스템에 의존 할 수 있습니다.
캐싱 시스템의 개발자는 전체 어댑터 모음보다는 단일 인터페이스 세트 만 구현하면됩니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 목표

이 PSR의 목표는 개발자가 빈틈없는 캐시 라이브러리를 만들 수 있도록 하여 맞춤 개발이 필요없이 기존 프레임 워크와 시스템에 통합 될 수 있도록 하는 것입니다.

## 정의

*    **호출 라이브러리(Calling Library)** - 실제로 캐시 서비스가 필요한 라이브러리 또는 코드.
                       이 라이브러리는이 표준의 인터페이스를 구현하는 캐싱 서비스를 활용할 것이며, 그렇지 않으면 이러한 캐싱 서비스의 구현에 대한 알지 못합니다. `역자주: 인터페이스를 구현한 캐싱 서비스를 사용만하고 구현을 하지 않는다.`

*    **구현 라이브러리(Implementing Library)** - 이 라이브러리는 모든 호출 라이브러리에 캐싱 서비스를 제공하기 위해이 표준을 구현합니다.
                        구현 라이브러리는 `Cache\CacheItemPoolInterface` 및 `Cache\CacheItemInterface` 인터페이스를 구현하는 클래스를 제공해야합니다.
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
구현 라이브러리은 추가 문자와 인코딩 또는 더 긴 길이를 지원할 수 있지만 최소한 그 최소값을 지원해야합니다(MAY).
라이브러리는 적절하게 키 문자열을 이스케이프 처리해야하지만 원래의 수정되지 않은 키 문자열을 반환 할 수 있어야합니다(MUST).
다음 문자는 미래의 확장을 위해 예약되어 있으며, 구현 라이브러리에서 지원해서는 안됩니다(MUST NOT) :`{} () / \ @ :`


*    **Hit** - 호출 라이브러리가 키로 Item을 요청하고 해당 키에 대해 일치하는 값이 발견되고 그 값이 만료되지 않았으며 값이 다른 이유로 무효하지 않은 경우 캐시 히트(Hit)가 발생합니다.
호출 라이브러리는 모든 `get()` 호출에 대해 `isHit()`를 확인해야합니다 (SHOULD).


*    **Miss** - 캐시 미스는 캐시 히트와 반대입니다.
호출 라이브러리가 키로 항목을 요청하고 해당 키에 해당 값이 없거나 값이 발견되었지만 만료되었거나 값이 다른 이유로 유효하지 않은 경우 캐시 미스가 발생합니다.
만료 된 값은 항상 캐시 미스로 간주되어야합니다 (MUST).


*    **지연됨(Deferred)** - 지연된 캐시 저장은 캐시 항목이 풀에 의해 즉시 유지되지 않을 수 있음을 나타냅니다.
풀 객체는 일부 저장소 엔진에서 지원하는 대량 세트 연산을 이용하기 위해 지연된 캐시 항목을 유지하는 것을 지연시킬 수 있습니다.
풀은 지연 캐시 항목이 결국 유지되고 데이터가 손실되지 않도록 보장해야하며 호출 라이브러리가 지속되도록 요청하기 전에이를 유지해야합니다.
호출 라이브러리가 `commit()` 메소드를 호출 할 때 모든 미해결 지연 항목을 유지해야합니다 (MUST).
구현 라이브러리는 객체 소멸자, `save()`, timeout 또는 max-items check 또는 다른 적절한 논리에서 모두 지속되는 것과 같이 지연된 항목을 언제 유지할 것인지를 결정하는 데 적합한 논리를 사용할 수 있습니다 (MAY).
지연된 캐시 항목에 대한 요청은 지연되었지만 아직 지속되지 않은 항목을 반환해야합니다 (MUST).


## Data

구현 라이브러리는 다음을 포함하여 모든 직렬화 가능한 PHP 데이터 유형을 지원해야합니다.

*    **Strings** - PHP 호환 인코딩의 임의의 크기를 갖는 문자열.
*    **Integers** - PHP가 지원하는 모든 크기의 정수 (최대 64 비트).
*    **Floats** - 부호가 있는(signed) 모든 부동소수점 값.
*    **Boolean** - True 와 False.
*    **Null** - 진짜 Null 값. `역자주: 진짜라는 표현을 하는 이유는 PSR-16의 Null과는 다른 의미라서 입니다`
*    **Arrays** - 인덱스된 임의의 깊이를 가진 연관 및 다차원 배열
*    **Object** - `$o == unserialize(serialize($o))` 와 같이 무손실 직렬화 및 직렬화 해제를 지원하는 객체.
객체는 PHP의 Serializable 인터페이스, 적절한 경우 `__sleep()` 또는 `__wakeup()` 매직 메서드 또는 유사한 언어 기능을 활용할 수 있습니다(MAY).

구현 라이브러리에 전달 된 모든 데이터는 전달 된 그대로 정확하게 반환되어야합니다(MUST).
여기에는 가변 유형이 포함됩니다. 즉, 만약 (int) 5가 저장된 값이었던 경우 (string) 5를 반환하는 것은 오류입니다.
구현 라이브러리는 PHP의 `serialize()`/`unserialize()` 함수를 내부적으로 사용할 수 있지만 꼭 그렇게 할 필요는 없습니다(MAY).
이들과의 호환성은 수용 가능한 객체 값의 기준선으로 사용됩니다.


어떠한 이유로든 저장된 정확한 값을 반환 할 수없는 경우 구현 라이브러리는 데이터가 손상되지 않게 캐시 미스로 응답해야합니다(MUST).

## Key Concepts

### Pool

풀은 캐싱 시스템의 항목 모음을 나타냅니다. 
풀은 포함 된 모든 항목의 논리적 저장소입니다.
캐시 가능한 모든 항목은 풀에서 항목 객체로 검색되고 캐시 된 객체의 전체 유니버스와의 모든 상호 작용은 풀을 통해 발생합니다.

### Items

Item은 풀 내의 단일 키 / 값 쌍을 나타냅니다.
키는 Item에 대한 주요 고유 식별자이며 변경할 수 없어야합니다 (MUST).
언제든지 값을 변경할 수 있습니다 (MAY).

## Error handling

캐싱은 종종 애플리케이션 성능의 중요한 부분이지만 애플리케이션 기능의 중요한 부분이되어서는 안됩니다.
따라서 캐시 시스템의 오류로 인해 프로그램이 실패(failure)하지 않아야합니다 (SHOULD NOT).
이런 이유로, 구현 라이브러리는 인터페이스에 의해 정의 된 것과 다른 예외를 던져서는 안되며, 기본 데이터 저장소에 의해 발생된 된 오류나 예외를 버리고 문제를 일으키지 않아야합니다 (SHOULD).

구현 라이브러리는 그러한 오류를 기록하거나 적절한 경우 이를 관리자에게 보고해야합니다 (SHOULD).

호출 라이브러리가 하나 이상의 항목을 삭제하도록 요청하거나 풀을 지우도록 요청한 경우 지정된 키가 존재하지 않으면 오류 상태로 간주해서는 안됩니다(MUST NOT).
다음의 조건(키가 존재하지 않거나 풀이 비어 있음)도 동일하며 오류 조건은 없습니다.

## Interfaces

### CacheItemInterface

`CacheItemInterface`는 캐시 시스템 내부의 항목을 정의합니다.
각 Item 객체는 구현 시스템에 따라 설정 될 수 있는 특정 키와 연결되어야하며(MUST) 일반적으로 `Cache\CacheItemPoolInterface` 객체에 의해 전달됩니다.

`Cache\CacheItemInterface` 객체는 캐시 항목의 저장 및 검색을 캡슐화합니다.
각 `Cache\CacheItemInterface`는 `Cache\CacheItemPoolInterface` 오브젝트에 의해 생성되며, 필요한 모든 설정을 담당 할 뿐만 아니라 오브젝트를 고유 키와 연관시킵니다.
`Cache\CacheItemInterface` 객체는 이 문서의 데이터 섹션에 정의 된 모든 유형의 PHP 값을 저장하고 검색 할 수 있어야 합니다.

호출 라이브러리는 Item 객체 자체를 인스턴스화해서는 안됩니다.
`getItem()` 메소드를 통해 Pool 객체에서만 요청할 수 있습니다.
라이브러리 호출은 하나의 구현 라이브러리에 의해 생성 된 항목이 다른 구현 라이브러리의 풀과 호환 가능하다고 가정해서는 안됩니다 (SHOULD NOT).


~~~php
<?php

namespace Psr\Cache;

/**
 * CacheItemInterface defines an interface for interacting with objects inside a cache.
 */
interface CacheItemInterface
{
    /**
     * Returns the key for the current cache item.
     *
     * The key is loaded by the Implementing Library, but should be available to
     * the higher level callers when needed.
     *
     * @return string
     *   The key string for this cache item.
     */
    public function getKey();

    /**
     * Retrieves the value of the item from the cache associated with this object's key.
     *
     * The value returned must be identical to the value originally stored by set().
     *
     * If isHit() returns false, this method MUST return null. Note that null
     * is a legitimate cached value, so the isHit() method SHOULD be used to
     * differentiate between "null value was found" and "no value was found."
     *
     * @return mixed
     *   The value corresponding to this cache item's key, or null if not found.
     */
    public function get();

    /**
     * Confirms if the cache item lookup resulted in a cache hit.
     *
     * Note: This method MUST NOT have a race condition between calling isHit()
     * and calling get().
     *
     * @return bool
     *   True if the request resulted in a cache hit. False otherwise.
     */
    public function isHit();

    /**
     * Sets the value represented by this cache item.
     *
     * The $value argument may be any item that can be serialized by PHP,
     * although the method of serialization is left up to the Implementing
     * Library.
     *
     * @param mixed $value
     *   The serializable value to be stored.
     *
     * @return static
     *   The invoked object.
     */
    public function set($value);

    /**
     * Sets the expiration time for this cache item.
     *
     * @param \DateTimeInterface|null $expiration
     *   The point in time after which the item MUST be considered expired.
     *   If null is passed explicitly, a default value MAY be used. If none is set,
     *   the value should be stored permanently or for as long as the
     *   implementation allows.
     *
     * @return static
     *   The called object.
     */
    public function expiresAt($expiration);

    /**
     * Sets the expiration time for this cache item.
     *
     * @param int|\DateInterval|null $time
     *   The period of time from the present after which the item MUST be considered
     *   expired. An integer parameter is understood to be the time in seconds until
     *   expiration. If null is passed explicitly, a default value MAY be used.
     *   If none is set, the value should be stored permanently or for as long as the
     *   implementation allows.
     *
     * @return static
     *   The called object.
     */
    public function expiresAfter($time);

}
~~~

### CacheItemPoolInterface

`Cache\CacheItemPoolInterface`의 기본 목적은 호출 라이브러리에서 키를 승인하고 연관된 `Cache\CacheItemInterface` 오브젝트를 리턴하는 것입니다.
또한 전체 캐시 컬렉션과의 상호 작용의 주요 지점이기도합니다.
풀(Pool)의 모든 구성 및 초기화는 구현 라이브러리에 맡깁니다.

~~~php
<?php

namespace Psr\Cache;

/**
 * CacheItemPoolInterface generates CacheItemInterface objects.
 */
interface CacheItemPoolInterface
{
    /**
     * Returns a Cache Item representing the specified key.
     *
     * This method must always return a CacheItemInterface object, even in case of
     * a cache miss. It MUST NOT return null.
     *
     * @param string $key
     *   The key for which to return the corresponding Cache Item.
     *
     * @throws InvalidArgumentException
     *   If the $key string is not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return CacheItemInterface
     *   The corresponding Cache Item.
     */
    public function getItem($key);

    /**
     * Returns a traversable set of cache items.
     *
     * @param string[] $keys
     *   An indexed array of keys of items to retrieve.
     *
     * @throws InvalidArgumentException
     *   If any of the keys in $keys are not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return array|\Traversable
     *   A traversable collection of Cache Items keyed by the cache keys of
     *   each item. A Cache item will be returned for each key, even if that
     *   key is not found. However, if no keys are specified then an empty
     *   traversable MUST be returned instead.
     */
    public function getItems(array $keys = array());

    /**
     * Confirms if the cache contains specified cache item.
     *
     * Note: This method MAY avoid retrieving the cached value for performance reasons.
     * This could result in a race condition with CacheItemInterface::get(). To avoid
     * such situation use CacheItemInterface::isHit() instead.
     *
     * @param string $key
     *   The key for which to check existence.
     *
     * @throws InvalidArgumentException
     *   If the $key string is not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return bool
     *   True if item exists in the cache, false otherwise.
     */
    public function hasItem($key);

    /**
     * Deletes all items in the pool.
     *
     * @return bool
     *   True if the pool was successfully cleared. False if there was an error.
     */
    public function clear();

    /**
     * Removes the item from the pool.
     *
     * @param string $key
     *   The key to delete.
     *
     * @throws InvalidArgumentException
     *   If the $key string is not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return bool
     *   True if the item was successfully removed. False if there was an error.
     */
    public function deleteItem($key);

    /**
     * Removes multiple items from the pool.
     *
     * @param string[] $keys
     *   An array of keys that should be removed from the pool.
     *
     * @throws InvalidArgumentException
     *   If any of the keys in $keys are not a legal value a \Psr\Cache\InvalidArgumentException
     *   MUST be thrown.
     *
     * @return bool
     *   True if the items were successfully removed. False if there was an error.
     */
    public function deleteItems(array $keys);

    /**
     * Persists a cache item immediately.
     *
     * @param CacheItemInterface $item
     *   The cache item to save.
     *
     * @return bool
     *   True if the item was successfully persisted. False if there was an error.
     */
    public function save(CacheItemInterface $item);

    /**
     * Sets a cache item to be persisted later.
     *
     * @param CacheItemInterface $item
     *   The cache item to save.
     *
     * @return bool
     *   False if the item could not be queued or if a commit was attempted and failed. True otherwise.
     */
    public function saveDeferred(CacheItemInterface $item);

    /**
     * Persists any deferred cache items.
     *
     * @return bool
     *   True if all not-yet-saved items were successfully saved or there were none. False otherwise.
     */
    public function commit();
}
~~~

### CacheException

이 예외 인터페이스는 캐시 서버에 연결하거나 제공된 잘못된 자격 증명과 같은 *캐시 설정*을 포함하여 치명적인 오류가 발생할 때 사용하기위한 것입니다.
구현 라이브러리에 의해 던져진 예외는 이 인터페이스를 구현해야합니다 (MUST).

~~~php
<?php

namespace Psr\Cache;

/**
 * Exception interface for all exceptions thrown by an Implementing Library.
 */
interface CacheException
{
}
~~~

### InvalidArgumentException

~~~php
<?php

namespace Psr\Cache;

/**
 * Exception interface for invalid cache arguments.
 *
 * Any time an invalid argument is passed into a method it must throw an
 * exception class which implements Psr\Cache\InvalidArgumentException.
 */
interface InvalidArgumentException extends CacheException
{
}
~~~
