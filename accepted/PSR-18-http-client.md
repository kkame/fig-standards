HTTP Client
===========

이 문서에서는 HTTP 요청을 보내고 HTTP 응답을받는 일반적인 인터페이스에 대해 설명합니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119](http://tools.ietf.org/html/rfc2119)에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

## 목표

이 PSR의 목표는 개발자가 HTTP 클라이언트 구현에서 분리 된 라이브러리를 만들 수있게하는 것입니다.
이렇게하면 종속성 수가 줄어들고 버전 충돌의 가능성이 줄어들기 때문에 라이브러리를 더 많이 재사용 할 수 있습니다.

두 번째 목표는 HTTP 클라이언트가 [Liskov 치환 원칙][Liskov]에 따라 대체 될 수 있다는 것입니다.
즉, 요청을 보낼 때 모든 클라이언트가 동일한 방식으로 작동해야합니다.

## 정의

* **클라이언트** - 클라이언트는 PSR-7 호환 HTTP 요청 메시지를 보내고 PSR-7 호환 HTTP 응답 메시지를 호출 라이브러리에 반환하기 위해 이 사양을 구현하는 라이브러리입니다.
* **호출 라이브러리** - 호출 라이브러리는 클라이언트를 사용하는 모든 코드입니다. 이 문서에 존재하는 인터페이스를 구현하지 않지만 이를 구현하는 객체 (클라이언트)를 사용합니다.

## 클라이언트

클라이언트는 `ClientInterface`를 구현 한 객체입니다.

클라이언트는 다음과 같은 것을 할 수 있습니다.(MAY) :

* 제공된 HTTP 요청을 변경하여 보낼지 선택합니다. 예를 들어, 보내는 메시지 본문을 압축 할 수 있습니다.
* 수신 된 HTTP 응답을 호출 라이브러리로 되돌리기 전에 변경할 것인지 선택합니다. 예를 들어 들어오는 메시지 본문의 압축을 풀 수 있습니다.

클라이언트가 HTTP 요청이나 HTTP 응답을 변경하기로 결정한 경우, 객체가 내부적으로 일관성을 유지하는지 확인해야합니다 (MUST).
 예를 들어, 클라이언트가 메시지 본문을 압축 해제하기로 선택한 경우, 반드시 `Content-Encoding` 헤더를 제거하고`Content-Length` 헤더를 조정해야합니다.

결과적으로 [PSR-7 객체는 불변이므로](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-7-http-message-meta.md#why-value-objects), 호출 라이브러리는 `ClientInterface::sendRequest()`에 전달 된 객체가 실제로 전송 된 것과 동일한 PHP 객체가 될 것이라고 가정해서는 안됩니다.
예를 들어, 예외에 의해 반환 된 Request 객체는 `sendRequest()`에 전달 된 것과 다른 객체 일 수 있기 때문에 참조 (===)로 비교할 수 없습니다.

클라이언트는 반드시 다음을 지켜야합니다.(MUST) :

* 호출 라이브러리에 반환되는 내용이 상태 코드 200 이상의 유효한 HTTP 응답이 되도록 여러개의 HTTP 1xx 응답 자체를 재구성하십시오

## 오류 처리

클라이언트는 올바른 형식의 HTTP 요청이나 HTTP 응답을 오류 조건으로 취급해서는 안됩니다 (MUST NOT).

예를 들어 400 및 500 범위의 응답 상태 코드는 예외를 발생시키지 않아야하며 정상적으로 호출 라이브러리에 반환되어야합니다 (MUST).

클라이언트가 HTTP 요청을 전혀 보낼 수 없거나 HTTP 응답을 PSR-7 응답 객체로 구문 분석을 할 수없는 경우에만 클라이언트는 `Psr\Http\Client\ClientExceptionInterface` 인스턴스를 던져야합니다.

요청 메시지가 올바른 형식의 HTTP 요청이 아니거나 일부 중요한 정보 (예 : 호스트 또는 메서드)가 없어서 요청을 보낼 수없는 경우, 클라이언트는 `Psr\Http\Client\RequestExceptionInterface`의 인스턴스를 던져야만 합니다.(MUST)

타임 아웃을 포함하여 어떤 종류의 네트워크 장애로 인해 요청을 보낼 수없는 경우, 클라이언트는 `Psr\Http\Client\NetworkExceptionInterface`의 인스턴스를 던져야만 합니다 (MUST).

클라이언트는 위에서 정의된 인터페이스를 적합하게 구현한다면 여기에 정의 된 것보다 더 구체적인 예외 (예 :`TimeOutException` 또는 `HostNotFoundException`)를 던질 수도 있습니다 (MAY).

## Interfaces

### ClientInterface

```php
namespace Psr\Http\Client;

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\ResponseInterface;

interface ClientInterface
{
    /**
     * Sends a PSR-7 request and returns a PSR-7 response.
     *
     * @param RequestInterface $request
     *
     * @return ResponseInterface
     *
     * @throws \Psr\Http\Client\ClientExceptionInterface If an error happens while processing the request.
     */
    public function sendRequest(RequestInterface $request): ResponseInterface;
}
```

### ClientExceptionInterface

```php
namespace Psr\Http\Client;

/**
 * Every HTTP client related exception MUST implement this interface.
 */
interface ClientExceptionInterface extends \Throwable
{
}
```

### RequestExceptionInterface

```php
namespace Psr\Http\Client;

use Psr\Http\Message\RequestInterface;

/**
 * Exception for when a request failed.
 *
 * Examples:
 *      - Request is invalid (e.g. method is missing)
 *      - Runtime request errors (e.g. the body stream is not seekable)
 */
interface RequestExceptionInterface extends ClientExceptionInterface
{
    /**
     * Returns the request.
     *
     * The request object MAY be a different object from the one passed to ClientInterface::sendRequest()
     *
     * @return RequestInterface
     */
    public function getRequest(): RequestInterface;
}
```

### NetworkExceptionInterface

```php
namespace Psr\Http\Client;

use Psr\Http\Message\RequestInterface;

/**
 * Thrown when the request cannot be completed because of network issues.
 *
 * There is no response object as this exception is thrown when no response has been received.
 *
 * Example: the target host name can not be resolved or the connection failed.
 */
interface NetworkExceptionInterface extends ClientExceptionInterface
{
    /**
     * Returns the request.
     *
     * The request object MAY be a different object from the one passed to ClientInterface::sendRequest()
     *
     * @return RequestInterface
     */
    public function getRequest(): RequestInterface;
}
```

[Liskov]: https://en.wikipedia.org/wiki/Liskov_substitution_principle
