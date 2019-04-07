HTTP Server Request Handlers
============================

이 문서에서는 [PSR-7][psr7] 또는 후속 PSR로 설명 된 HTTP 메시지를 사용하는 HTTP 서버 요청 처리기 ("요청 처리기") 및 HTTP 서버 미들웨어 구성 요소 ("미들웨어")에 대한 일반적인 인터페이스에 대해 설명합니다.

HTTP 요청 처리기는 모든 웹 응용 프로그램의 기본 요소입니다.
서버 측 코드는 요청 메시지를 수신하여 처리하고 응답 메시지를 생성합니다.
HTTP 미들웨어는 공통 요청 및 응답 처리를 응용 프로그램 계층에서 분리하는 한 방법입니다.

이 문서에서 설명하는 인터페이스는 요청 처리기 및 미들웨어에 대한 추상화입니다.

_참고 : "요청 처리기"와 "미들웨어"에 대한 모든 참조는 **서버 요청** 처리에만 해당됩니다 ._


이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [rfc2119]에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

[psr7]: http://www.php-fig.org/psr/psr-7/
[rfc2119]: http://tools.ietf.org/html/rfc2119

### References

- [PSR-7][psr7]
- [RFC 2119][rfc2119]

## 1. 명세서

### 1.1 Request Handlers

요청 처리기는 PSR-7에 정의 된대로 요청을 처리하고 응답을 생성하는 개별 구성 요소입니다.

요청 처리자가 응답을 생성하지 못하는 경우 요청 처리기가 예외를 던질 수 있습니다 (MAY).
예외 유형이 정의되지 않았습니다.

이 표준을 사용하는 요청 처리기는 다음 인터페이스를 구현해야합니다 (MUST).

- `Psr\Http\Server\RequestHandlerInterface`

### 1.2 Middleware

미들웨어 구성 요소는 들어오는 요청을 처리하고 PSR-7에 정의 된 결과 응답을 생성 할 때 다른 미들웨어 구성 요소와 함께 참여하는 개별 구성 요소입니다.

미들웨어 컴포넌트는 충분한 조건이 만족된다면 요청 처리자에게 위임하지 않고 응답을 생성하고 리턴 할 수 있습니다 (MAY).

이 표준을 사용하는 미들웨어는 다음 인터페이스를 구현해야합니다(MUST).

- `Psr\Http\Server\MiddlewareInterface`

### 1.3 Generating Responses

응답을 생성하는 미들웨어 또는 요청 처리기는 특정 HTTP 메시지 구현에 대한 의존을 방지하기 위해 PSR-7 `ResponseInterface` 의 프로토타입 또는 `ResponseInterface` 인스턴스를 생성 할 수있는 팩토리를 작성하는 것이 좋습니다(RECOMMENDED).

### 1.4 Handling Exceptions

미들웨어를 사용하는 모든 응용 프로그램에는 예외를 잡아서 응답으로 변환하는 구성 요소가 포함되는 것이 좋습니다(RECOMMENDED).
이 미들웨어는 실행 된 첫 번째 구성 요소 여야하며(SHOULD) 응답이 항상 생성되도록하기 위해 모든 추가 처리를 래핑해야합니다.

## 2. Interfaces

### 2.1 Psr\Http\Server\RequestHandlerInterface

요청 처리기는 다음의 인터페이스를 구현되어야합니다(MUST).

```php
namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

/**
 * Handles a server request and produces a response.
 *
 * An HTTP request handler process an HTTP request in order to produce an
 * HTTP response.
 */
interface RequestHandlerInterface
{
    /**
     * Handles a request and produces a response.
     *
     * May call other collaborating code to generate the response.
     */
    public function handle(ServerRequestInterface $request): ResponseInterface;
}
```

### 2.2 Psr\Http\Server\MiddlewareInterface

미들웨어 구성 요소는 다음 인터페이스에 호환 가능하게 구현해야합니다(MUST).

```php
namespace Psr\Http\Server;

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

/**
 * Participant in processing a server request and response.
 *
 * An HTTP middleware component participates in processing an HTTP message:
 * by acting on the request, generating the response, or forwarding the
 * request to a subsequent middleware and possibly acting on its response.
 */
interface MiddlewareInterface
{
    /**
     * Process an incoming server request.
     *
     * Processes an incoming server request in order to produce a response.
     * If unable to produce the response itself, it may delegate to the provided
     * request handler to do so.
     */
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface;
}
```
