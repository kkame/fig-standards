Event Dispatcher
================

Event Dispatching은 개발자가 어플리케이션에 로직을 쉽고 일관되게 주입 할 수있게 해주는 일반적이며 잘 테스트 된 메커니즘입니다.

이 PSR의 목표는 라이브러리와 컴포넌트가가 다양한 어플리케이션과 프레임워크간에 보다 자유롭게 재사용 될 수 있도록 Event 기반 확장 및 협업을 위한 공통 메커니즘을 확립하는 것입니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.

> 역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 목표

Event 발송 및 처리를 위한 공통 인터페이스를 사용하면 개발자가 공통된 방식으로 여러 프레임워크 및 기타 라이브러리와 상호 작용할 수 있는 라이브러리를 만들 수 있습니다.

몇 가지 예 :

* 사용자가 권한이 없을 때 데이터에 저장/액세스를 방지하는 보안 프레임워크.
* 일반적인 전체 페이지 캐싱 시스템.
* 다른 라이브러리를 확장하는 라이브러리. 프레임워크가 둘 다 통합되어 있는지 여부와 관계 없음.
* 어플리케이션 내에서 수행 된 모든 작업을 추적하는 로깅 패키지

## 정의

* **Event** - Event는 *Emitter*가 생성 한 메시지입니다. 임의의 PHP 객체 일 수 있습니다.
* **Listener** - Listener는 Event를 전달할 실행가능한 어떠한 PHP 입니다. 동일한 Event를 0 개 이상의 Listener에 전달할 수 있습니다. Listener는 원할 경우 다른 비동기 동작을 큐에 넣을 수 있습니다 (MAY).
* **Emitter** - Emitter는 Event를 보내고자 하는 임의의 코드입니다. 이것은 "호출 코드"라고도합니다. 이것은 특정 데이터 구조로 표현되지 않고 유스케이스를 가르킵니다.
* **Dispatcher** - Dispatcher는 Emitter에 의해 Event 객체를 전달하는 서비스 객체입니다. Dispatcher는 Event가 관련된 모든 Listener에게 전달되도록 보장하지만, 실행할 Listener를 결정하는 것은 Listener Provider에게 위임해야합니다.(MUST)
* **Listener Provider** - Listener Provider는 주어진 Event와 관련이있는 Listener를 결정할 책임이 있지만 Listener 자신을 호출해서는 안됩니다(MUST NOT). Listener Provider는 0 개 이상의 관련 Listener를 지정할 수 있습니다.

## 이벤트

Event는 Emitter와 해당하는 Listener 간의 통신 단위 역할을 하는 객체입니다.

Emitter에 정보를 다시 돌려주는 Listener를 호출하는 경우 Event 객체는 변경 될 수 있습니다(MAY).

그러나 그러한 양방향 통신이 필요하지 않은 경우 Event는 변경 불가능한 것으로 정의하는 것이 좋습니다(RECOMMENDED). 즉, 뮤 테이터 (mutator) 메소드가 존재하지 않도록 정의합니다.

구현체는 동일한 객체가 모든 Listener에게 반드시 전달된다고 가정해야합니다 (MUST).

Event 객체가 무손실 직렬화 및 비 직렬화를 지원한다는 것이 권장되지만(RECOMMENDED) 필수적이지는 않습니다(NOT REQUIRED). `$event == unserialize(serialize($event))`는 참이어야합니다 (SHOULD). 객체는 PHP의 `Serializable` 인터페이스, `__sleep()` 또는 `__wakeup()` 매직 메소드 또는 적절한 경우 언어(PHP) 내 유사한 기능을 활용 할 수 있습니다 (MAY).

## 멈출수 있는 Event-Stoppable Event

**Stoppable Event**는 더 많은 Listener가 호출되는 것을 방지하는 방법을 추가한, Event의 특별한 케이스입니다. 이것은 `StoppableEventInterface`를 구현하여 표현합니다.

`StoppableEventInterface`를 구현 한 Event는 해당하는 Event가 완료되면 `isPropagationStopped()`로부터 `true`를 리턴해야합니다 (MUST). 그것이 언제인지를 결정하는 것은 클래스를 구현하는 자의 몫입니다. 예를 들어, PSR-7의 `RequestInterface` 객체가 대응하는 `ResponseInterface` 객체와 일치하도록 요청하는 Event는 Listener가 호출 할 `setResponse(ResponseInterface $res)`메소드를 가질 수 있습니다. 이것은 `isPropagationStopped()` 가 `true`를 반환합니다.

## 리스너-Listener

Listener는 실행가능한 어떤 PHP 일 것입니다. Listener는 하나의 매개 변수만을 가져야하며(MUST), 이것은 리스너가 반응해야하는 Event입니다.
 
Listener는 해당 유즈 케이스와 관련하여 파라메터에 구체적으로 타입 힌트를 입력해야합니다 (SHOULD).
 
즉, Listener는 인터페이스에 대해 타입 힌트를 입력하여 해당 인터페이스를 구현하는 모든 Event 유형 또는 해당 인터페이스의 특정 구현과 호환 가능하다는 것을 표현합니다.

Listener는 `void`리턴을 가져야하고 (SHOULD), 명시 적으로 리턴하는 타입 힌트를 입력해야합니다 (SHOULD). Dispatcher는 Listener의 리턴 값을 무시해야합니다 (MUST).

Listener는 다른 코드에 행동을 위임 할 수 있습니다(MAY). 이것은 실제 비즈니스 로직을 실행하는 객체 둘러싼 얇은 래퍼(wrapper) 인 Listener가 포함됩니다.

Listener는 cron, 큐 서버 또는 유사한 기술을 사용하는 보조 프로세스를 통해 나중에 처리하기 위해 Event로부터 정보를 큐에 넣을 수 있습니다(MAY). 
그러기 위해 Event 객체 자체를 직렬화 할 수있습니다. 그러나 모든 Event 객체가 안전하게 직렬화 될 수 있는 것은 아니므로주의해야합니다. 
보조 프로세스는 Event 객체에 대한 모든 변경 사항이 다른 Listener에 전파되지 않는다고 가정해야합니다 (MUST).

## 발송자-Dispatcher

Dispatcher는 `EventDispatcherInterface`를 구현 한 서비스 객체입니다. Listener Provider로부터 전달 된 Event에 대한 Listener를 찾아서 해당 Event와 함께 각 Listener를 호출합니다.

Dispatcher는 :

* ListenerProvider에서 반환 된 순서대로 Listener를 동기적으로 호출해야합니다(MUST).
* Listener를 호출 한 후 전달 된 것과 동일한 Event 객체를 반환해야합니다(MUST).
* 모든 Listener가 실행완료될 때까지 Emitter로 돌아 가지 않아야합니다(MUST NOT).

Dispatcher는 Stoppable Event가 전달되면  

* 각 Listener가 호출되기 전에 Event에 대해 반드시 `isPropagationStopped()`를 호출해야합니다 (MUST). 그 메소드가 `true`를 리턴하면, 반드시 Event를 Emitter에게 즉시 반환해야하고 더 이상의 Listener를 호출해서는 안됩니다. 이것은 `isPropagationStopped()`에서 항상 `true`를 반환하는 Event가 Dispatcher에 전달되면 0개의 Listener가 호출됨을 의미합니다.

Dispatcher는 Listener 제공자로부터 리턴 된 Listener가 type-safe하다고 가정해야합니다 (SHOULD). 즉, Dispatcher는 `$listener($event)`호출이 `TypeError`를 생성하지 않아야한다고 가정해야합니다 (SHOULD).

[Promise object]: https://promisesaplus.com/

### Error handling

Listener가 던진 예외 또는 오류는 이후의 모든 Listener의 실행을 차단해야합니다(MUST). Listener에 의해 던져진 오류 또는 예외는 Emitter로 전달 되야합니다(MUST).

Dispatcher는 던져진 객체를 잡아서 기록 할 수 있고(MAY), 추가적인 조치가 행동을 하도록 허용하지만 반드시 원래의 Throwable을 다시 던져야합니다 (MUST).

## Listener Provider

Listener Provider는, Listener가 어느 Event에 관련이 있는지, 어떤 Listener가 호출되어야 하는지를 결정하는 서비스 객체입니다. Listener가 무엇을 의미하는지 그리고 Listener가 선택하는 방법에 따라 Listener를 돌려주는 순서를 결정할 수 있습니다. 다음이 포함될 수 있습니다(MAY):

* 구현자가 고정 된 순서로 Event에 Listener를 할당 할 수 있도록 일부 등록 메커니즘을 허용합니다.
* Event 유형 과 구현 된 인터페이스를 기반으로 리플렉션을 통해 해당 Listener 목록을 만듭니다.
* 런타임에 참조 될 수 있는 미리 컴파일 된 Listener의 목록 생성합니다.
* 현재 사용자에게 특정 권한이 있는 경우에만 특정 Listener가 호출되도록 액세스 제어 형식을 구현합니다.
* 엔티티와 같이 Event에 의해 참조되는 객체에서 일부 정보를 추출하고 해당 객체에 대해 미리 정의 된 라이프 사이클 메소드를 호출합니다.
* 임의의 로직를 사용하여 하나 이상의 다른 Listener Provider에게 책임을 위임합니다.

위의 메커니즘이나 다른 메커니즘을 원하는대로 사용할 수 있습니다(MAY).

Listener Provider는 Event의 클래스 이름을 사용하여 Event를 다른 Event와 구별해야합니다 (SHOULD). 또한 Event에 대한 다른 정보를 적절하게 고려할 수 있습니다(MAY).

Listener Provider는 Listener 적용 가능 여부를 결정할 때 부모 유형을 Event의 자체 유형과 동일하게 처리해야합니다(MUST). 

다음과 같은 경우 :

```php
class A {}

class B extends A {}

$b = new B();

function listener(A $event): void {};
```

Listener Provider는 리스너의 다른 조건에 의해 타입이 호환되지 않는 한 호환성이 유효하므로, `$b`의 Listener로서 `listener()`를 처리 해야합니다 (MUST).

## 오브젝트 구성-Object composition

Dispatcher는 관련된 Listener를 판별하기 위해 Listener Provider를 구성해야합니다 (SHOULD). Listener Provider가 Dispatcher와는 별개의 오브젝트로 구현되지만 반드시 요구되지는 않는 것이 좋습니다 (RECOMMENDED).

## Interfaces

```php
namespace Psr\EventDispatcher;

/**
 * Defines a dispatcher for events.
 */
interface EventDispatcherInterface
{
    /**
     * Provide all relevant listeners with an event to process.
     *
     * @param object $event
     *   The object to process.
     *
     * @return object
     *   The Event that was passed, now modified by listeners.
     */
    public function dispatch(object $event);
}
```

```php
namespace Psr\EventDispatcher;

/**
 * Mapper from an event to the listeners that are applicable to that event.
 */
interface ListenerProviderInterface
{
    /**
     * @param object $event
     *   An event for which to return the relevant listeners.
     * @return iterable[callable]
     *   An iterable (array, iterator, or generator) of callables.  Each
     *   callable MUST be type-compatible with $event.
     */
    public function getListenersForEvent(object $event) : iterable;
}
```

```php
namespace Psr\EventDispatcher;

/**
 * An Event whose processing may be interrupted when the event has been handled.
 *
 * A Dispatcher implementation MUST check to determine if an Event
 * is marked as stopped after each listener is called.  If it is then it should
 * return immediately without calling any further Listeners.
 */
interface StoppableEventInterface
{
    /**
     * Is propagation stopped?
     *
     * This will typically only be used by the Dispatcher to determine if the
     * previous listener halted propagation.
     *
     * @return bool
     *   True if the Event is complete and no further listeners should be called.
     *   False to continue calling listeners.
     */
    public function isPropagationStopped() : bool;
}
```
