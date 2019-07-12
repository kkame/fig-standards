# HTTP message interfaces

이 문서는 [RFC 7230](http://tools.ietf.org/html/rfc7230) 및 [RFC 7231](http://tools.ietf.org/html/rfc7231)에 설명한 대로 
HTTP 메시지를 처리하는 일반적인 인터페이스 및 [RFC 3986](http://tools.ietf.org/html/rfc3986)에 설명한 대로 HTTP 메시지와 함께 사용할 URI에 관한 내용을 설명합니다

HTTP 메시지는 웹 개발의 기반(foundation)입니다.
웹 브라우저와 cURL과 같은 HTTP 클라이언트는 HTTP 요청 메시지를 작성하여 HTTP 응답 메시지를 제공하는 웹 서버로 전송합니다.
서버 측 코드는 HTTP 요청 메시지를 수신하고 HTTP 응답 메시지를 반환합니다.

HTTP 메시지는 일반적으로 최종 사용자로부터 요청(abstracted)되지만 개발자들은 일반적으로 HTTP API에 요청을 하거나 들어오는 요청을 처리하는 등의 작업을 수행하기 위해 구조화 방법과 이를 액세스하거나 조작하는 방법을 알아야합니다.

모든 HTTP 요청 메시지에는 다음과 같은 특정 형식이 있습니다.

~~~http
POST /path HTTP/1.1
Host: example.com

foo=bar&baz=bat
~~~

첫 번째 요청 줄은 "요청 줄(request line)"이며 HTTP 요청 방법(request method), 요청 대상 (일반적으로 URI의 절대값 또는 웹 서버의 경로) 및 HTTP 프로토콜 버전을 순서대로 포함합니다.
그 뒤에는 하나 이상의 HTTP 헤더, 빈 행 및 메시지 본문이옵니다.

HTTP 응답 메시지는 다음과 유사한 구조를 가집니다.

~~~http
HTTP/1.1 200 OK
Content-Type: text/plain

This is the response body
~~~
 
첫 번째 줄은 "상태 줄"이며 HTTP 프로토콜 버전, HTTP 상태 코드 및 사람이 읽을 수있는 상태 코드 설명 인 "이유 구문"을 순서대로 포함합니다.
HTTP 요청 메시지와 마찬가지로 하나 이상의 HTTP 헤더, 빈 행 및 메시지 본문이옵니다.

이 문서에서 설명하는 인터페이스는 HTTP 메시지 및 HTTP 메시지를 구성하는 요소를 추상화 한 것입니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119](http://tools.ietf.org/html/rfc2119)에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

### References

- [RFC 2119](http://tools.ietf.org/html/rfc2119)
- [RFC 3986](http://tools.ietf.org/html/rfc3986)
- [RFC 7230](http://tools.ietf.org/html/rfc7230)
- [RFC 7231](http://tools.ietf.org/html/rfc7231)

## 1. 명세서

### 1.1 Messages

HTTP 메시지는 클라이언트에서 서버로의 요청 또는 서버에서 클라이언트로의 응답을 의미합니다.
이 사양은 HTTP 메시지 `Psr\Http\Message\RequestInterface` 와 `Psr\Http\Message\ResponseInterface` 에 대한 인터페이스를 각각 정의합니다.

`Psr\Http\Message\RequestInterface` 와 `Psr\Http\Message\ResponseInterface` 는 모두 `Psr\Http\Message\MessageInterface`를 상속받습니다(extend).
`Psr\Http\Message\MessageInterface` 는 직접 구현 해도 되지만(MAY), 구현자는 `Psr\Http\Message\RequestInterface` 와 `Psr\Http\Message\ResponseInterface` 를 구현해야합니다 (SHOULD).

앞으로 이 인터페이스들을 말 할 때 `Psr\Http\Message` 네임 스페이스는 생략 할 것입니다.

### 1.2 HTTP Headers

#### 대소문자를 구분하지 않는 헤더 필드 이름

HTTP 메시지에는 대소문자를 구분하지 않는 헤더 필드 이름을 포함합니다.
`MessageInterface` 를 구현하는 클래스에서 헤더를 대문자와 소문자를 구분하지 않는 이름으로 검색합니다.
예를 들어 `foo` 헤더를 검색하는 것은 `FoO` 헤더를 검색하는 것과 같은 결과를 반환합니다.
비슷하게 `Foo` 헤더를 설정하면 이전에 설정된 `foo` 헤더 값을 덮어 씁니다.

~~~php
$message = $message->withHeader('foo', 'bar');

echo $message->getHeaderLine('foo');
// Outputs: bar

echo $message->getHeaderLine('FOO');
// Outputs: bar

$message = $message->withHeader('fOO', 'baz');
echo $message->getHeaderLine('foo');
// Outputs: baz
~~~

헤더는 대소문자의 구분 없이 검색할 수 있지만, `getHeaders()` 로 가져올 경우는 원래의 대소문자가 유지되어야만 합니다 (MUST).

부적합한 HTTP 프로그램은 특정 경우에 따라 달라질 수 있으므로 사용자가 요청이나 응답을 만들 때 HTTP 헤더의 대소문자를 지정할 수 있는 것이 좋습니다.

#### 여러개의 값을 가진 헤더

여러개의 값을 가진 헤더를 수용하면서도 헤더로 문자열 작업을 할 수있는 편리함을 제공하기 위해, 배열이나 문자열로 `MessageInterface` 의 인스턴스에서 헤더를 검색 할 수 있습니다.
`getHeaderLine()` 메서드를 사용하여, 대소문자를 구분하지 않는 헤더의 이름으로 검색한 결과 값을 모두 쉼표로 연결한 문자열을 가져옵니다.
대문자와 소문자를 구분하지 않는 헤더의 모든 헤더 값의 배열을 이름으로 검색하려면 `getHeader()`를 사용하십시오.

~~~php
$message = $message
    ->withHeader('foo', 'bar')
    ->withAddedHeader('foo', 'baz');

$header = $message->getHeaderLine('foo');
// $header contains: 'bar, baz'

$header = $message->getHeader('foo');
// ['bar', 'baz']
~~~

참고 : 모든 헤더 값을 쉼표 (예 :`Set-Cookie`)를 사용하여 연결할 수있는 것은 아닙니다.
이러한 헤더로 작업 할 때 `MessageInterface` 기반의 클래스는 이러한 다중 값 헤더를 검색하기 위해 `getHeader()` 메소드에 의존해야합니다 (SHOULD).

#### Host 헤더

요청에서 `Host` 헤더는 일반적으로 URI의 호스트 구성 부분만 아니라 TCP 연결 할 때 사용하는 호스트를 포함합니다.
그러나 HTTP 사양(specification)에서는 `Host` 헤더가 각각 다른 것을 허용하고 있습니다. `역자주: TCP 연결에 사용된 URI와 Http Requeser의 Host Header의 값이 다른 것을 허용합니다`

그래서 구현시 `Host` 헤더가 제공되지 않으면 제공된 URI에서 `Host` 헤더를 가져와야 합니다 (MUST).

기본적으로 `RequestInterface::withUri()`은 요청의 `Host` 헤더의 값을 전달 된 `UriInterface` 의 호스트 구성 요소와 일치하는 `Host` 헤더로 대체합니다.

두 번째 인수(`$preserveHost`)에 `true` 를 전달하여 `Host` 헤더의 원래 상태를 보존하도록 선택할 수 있습니다.
이 인수를 `true` 로 설정하면 반환 하는 메시지의 `Host` 헤더를 업데이트하지 않습니다. 
단, 메시지에 `Host` 헤더가 포함되어 있지 않으면 예외입니다.

이 테이블은 다양한 초기 요청과 URI에 대해 `$preserveHost` 인자가 `true` 로 설정된 `withUri()` 에 의해 반환 된 요청에 대해 `getHeaderLine('Host')` 가 반환하는 것을 보여줍니다.


Request Host header<sup>[1](#rhh)</sup> | Request host component<sup>[2](#rhc)</sup> | URI host component<sup>[3](#uhc)</sup> | Result
----------------------------------------|--------------------------------------------|----------------------------------------|--------
''                                      | ''                                         | ''                                     | ''
''                                      | foo.com                                    | ''                                     | foo.com
''                                      | foo.com                                    | bar.com                                | foo.com
foo.com                                 | ''                                         | bar.com                                | foo.com
foo.com                                 | bar.com                                    | baz.com                                | foo.com

- <sup id="rhh">1</sup> 변경이 일어나기 전의 `Host` 헤더 값
- <sup id="rhc">2</sup> 변경 전에 요청으로 작성된 URI의 Host 구성 요소.
- <sup id="uhc">3</sup> `withUri()` 를 통해 주입되는 URI의 Host 구성 요소.

### 1.3 Streams

HTTP 메시지는 시작 줄, 헤더 및 본문으로 구성됩니다.
HTTP 메시지 본문은 매우 작거나 매우 클 수 있습니다.
본문의 메시지를 문자열로 변환하려고하면 본문이 완전히 메모리에 저장되어야하기 때문에 의도 한 것보다 많은 메모리를 쉽게 써버릴 수 있습니다.
요청이나 응답의 본문을 메모리에 저장하려고 하면 배제되어 해당 구현을 사용하여 큰 메시지 본문을 처리 할 수 있게됩니다. `역자주: 자연스럽게 번역하려면 배제된다는 표현을 다른 것으로 바꾸는게 좋아보이지만 답을 못찾았습니다`
`StreamInterface`는 데이터 스트림을 읽거나 쓸 때 구현 세부 사항을 감추기 위해 사용합니다.
문자열이 적절한 메시지 구현이된다면, `php://memory` 와 `php://temp`와 같은 내장 스트림을 사용할 수 있습니다.

`StreamInterface`는 스트림을 효율적으로 읽고, 쓰고, 지나갈 수 있게 해주는 몇가지 메소드를 제공줍니다.

스트림은 `isReadable()`, `isWritable()` 및 `isSeekable()`의 세 가지 메소드를 사용합니다
이 메소드는 스트림 작업자가 스트림이 자신의 요구 사항을 충족하는지 확인하는 데 사용할 수 있습니다.

각 스트림 인스턴스에는 읽기 전용, 쓰기 전용 또는 읽기 / 쓰기가 가능한 다양한 기능이 있습니다.
또한 임의의 임의 액세스 (모든 위치를 앞뒤로 검색) 또는 순차 액세스 (예 : 소켓, 파이프 또는 콜백 기반 스트림의 경우) 만 허용 할 수 있습니다.

마지막으로, `StreamInterface` 는 전체 본문 내용을 즉시 검색하거나 내보내는 것을 단순화하는 `__toString ()` 메소드를 제공합니다.

요청과 응답 인터페이스와 달리,`StreamInterface`는 불변성을 모델링하지 않습니다.
실제 PHP 스트림이 래핑 된 상황에서는 자원과 상호 작용하는 모든 코드가 커서 상태, 내용 등을 포함하여 상태를 변경시킬 수 있으므로 변경이 불가능합니다.
구현시 서버 측 요청 및 클라이언트 측 응답에 읽기 전용 스트림을 사용하는 것이 좋습니다.
사용자는 스트림 인스턴스가 변경 될 수 있으며 메시지 상태를 변경할 수 있다는 사실을 알고 있어야합니다. 의심스럽다면 새 스트림 인스턴스를 만들어 메시지에 첨부하여 상태를 적용합니다.


### 1.4 Request Targets and URIs

RFC 7230에 따라 요청 메시지에는 요청 줄의 두 번째 세그먼트로 "요청 대상"이 포함됩니다.
요청 대상은 다음 형식 중 하나 일 수 있습니다.

- **origin-form** : 경로와 쿼리스트링 (있을 경우)으로 구성됩니다. 이를 보통 상대 URL이라고 합니다.
  TCP를 통해 전송되는 메시지는 일반적으로 **origin-form**입니다. 스키마(scheme) 및 권한 데이터는 일반적으로 CGI 변수를 통해서만 표현합니다.
- **absolute-form** : ("[user-info@]호스트[:port]", 대괄호 안의 항목은 선택 사항), 경로 (있는 경우), 쿼리스트링 (있는 경우) 및 fragment (있는 경우)으로 구성됩니다. `역자주: fragment는 URI의 #(hash) 문자열로 생각하시면 됩니다`
  이것은 절대 URI라고도하며 RFC 3986에서 지정하는 유일한 URI 형식입니다.
  이 양식은 HTTP 프록시에 요청할 때 일반적으로 사용됩니다.
- **authority-form** : 권한으로만 구성되어있습니다. 이것은 일반적으로 HTTP 클라이언트와 프록시 서버 사이의 연결을 설정하기 위한 CONNECT 요청에만 사용됩니다.
- **asterisk-form** : 이것은 문자열 `*` 로만 구성되며 웹 서버의 일반적인 기능을 결정하기 위해 OPTIONS 메서드와 함께 사용됩니다.

이러한 요청 대상 외에도 요청 대상과는 별도로 '유효한(effective) URL'이 있는 경우가 종종 있습니다.
유효한(effective) URL은 HTTP 메시지 내에서 전송되지 않지만 요청을 하기위한 프로토콜 (http/https), 포트 및 호스트 이름을 결정하는 데 사용됩니다.

유효한(effective) URL은 `UriInterface` 에 의해 표현됩니다.
`UriInterface` 는 RFC 3986 (첫번째 사용 사례)에 명시된대로 HTTP와 HTTPS URI를 모델링합니다.
이 인터페이스는 다양한 URI 부분과 상호 작용하기위한 메소드를 제공하여 URI의 반복 구문 분석의 필요성을 없애줍니다.
또한 모델링 된 URI를 문자열 표현으로 변환하기위한 `__toString()` 메소드를 제공합니다.

`getRequestTarget()`으로 request-target을 검색 할 때, 이 메소드는 기본적으로 URI 객체를 사용하고 **origin-form**을 구성하는 데 필요한 모든 구성 요소를 추출합니다.
**origin-form**은 가장 일반적인 요청 대상입니다.

최종 사용자가 다른 세 가지 형식 중 하나를 사용하기를 원하거나 사용자가 명시적으로 요청 대상을 덮어쓰고 싶다면 `withRequestTarget()`을 사용하여 이를 처리 할 수 있습니다. `역자주: 세가지 형식이란 위에 나오는 네가지 형식 중 **origin-form**을 제외한 세가지를 말합니다`

이 메소드를 호출하면 `getUri()`로부터 반환받는 URI에 영향을 주지 않습니다.

예를 들어, 사용자는 아래와 같이 서버에 **asterisk-form** 요청을 할 수 있습니다.

~~~php
$request = $request
    ->withMethod('OPTIONS')
    ->withRequestTarget('*')
    ->withUri(new Uri('https://example.org/'));
~~~

이 예제는 최종적으로 다음과 같은 결과를 얻을 수 있습니다.
~~~http
OPTIONS * HTTP/1.1
~~~

하지만 HTTP 클라이언트는 유효한(effective) URL (`getUri()`로부터)을 사용하여 프로토콜, 호스트 이름 및 TCP 포트를 결정할 수 있습니다.

HTTP 클라이언트는 반드시 `Uri::getPath()` 와 `Uri::getQuery()` 의 값을 무시해야하며(MUST), 대신 `getRequestTarget()` 에 의해 반환 된 값을 사용해야합니다. 기본값은 이 두 값을 연결한 것입니다.

네 개의 요청 대상 폼 중 하나 이상을 구현하지 않기로 결정한 클라이언트는 여전히 `getRequestTarget()` 을 사용해야한다(MUST).
이들 클라이언트는 지원하지 않는 요청 대상을 거부해야하며(MUST) 반드시 `getUri()` 의 값으로 폴백해서는 안된다(MUST NOT).

`RequestInterface`는 요청 대상을 검색하거나 제공된 요청 대상으로 새로운 인스턴스를 생성하기위한 메소드를 제공합니다.
기본적으로 요청 대상이 인스턴스에서 특별히 구성되지 않으면, `getRequestTarget()`은 구성된 URI의 **origin-form**을 반환할 것입니다. (URI가 구성되지 않으면 "/").
`withRequestTarget($requestTarget)` 은 지정된 요청 대상으로 새로운 인스턴스를 생성하고, 개발자가 다른 3 개의 요청 대상 형식 (absolute-form, authority-form 및 asterisk-form)을 나타내는 요청 메시지를 생성 할 수 있도록합니다.
사용되는 경우 구성된 URI 인스턴스는 여전히 클라이언트에서 사용할 수 있습니다.이 인스턴스는 서버에 대한 연결을 만드는 데 사용될 수 있습니다.

### 1.5 Server-side Requests

`RequestInterface` 는 HTTP 요청 메시지의 일반적인 표현을 제공합니다.
그러나 서버 측 요청의 경우 서버 측 환경의 특성상 추가 처리가 필요합니다.
서버 측 프로세싱은 CGI (Common Gateway Interface)를 고려해야하며 PHP의 SAPI(Server APIs)를 통한 CGI의 추상화 및 확장이 필요합니다.
PHP는 다음과 같은 슈퍼 전역 변수를 통해 입력을 정렬하고 단순화했습니다.

- `$ _COOKIE` : 역직렬화된 HTTP 쿠키에 대한 단순화 된 접근을 제공합니다.
- `$ _GET` : 역직렬화된 쿼리스트링 인수에 대한 단순화 된 접근을 제공합니다.
- `$ _POST` : 역직렬화된 HTTP POST를 통해 제출 된 urlencoded 매개 변수에 대해 단순화 된 액세스를 제공합니다. 일반적으로 메시지 본문을 파싱 한 결과로 간주 될 수 있습니다.
- `$ _FILES` : 파일 업로드와 관련된 일련의 메타 데이터를 제공합니다.
- `$ _SERVER` : CGI / SAPI 환경 변수에 대한 액세스를 제공합니다. 일반적으로 요청 메소드, 요청 스킴, 요청 URI 및 헤더가 포함됩니다.

`ServerRequestInterface` 는 `RequestInterface` 를 확장하여 이러한 다양한 슈퍼 전역 변수를 추상화합니다.
이 방법은 사용자가 슈퍼 전역변수에 연결(coupling)하는 것을 줄이고 요청 사용자가 테스트하는 것을 권장하고 도와줍니다.
서버 요청은 "속성(attributes)"이라는 추가 속성 하나를 제공하여 소비자가 응용 프로그램 관련 규칙 (예 : 경로(path) 일치, scheme 일치, 호스트 일치 등)에 대한 인트로스펙트(introspect), 분해(decompose) 및 일치(match) 기능을 제공합니다.
따라서 서버 요청은 여러 요청자간에 메시징을 제공 할 수도 있습니다.

### 1.6 Uploaded files

`ServerRequestInterface` 는 각 가지(leaf)가 `UploadedFileInterface` 인스턴스로 정규화 된 구조로 업로드 파일 트리를 검색하는 메소드를 제공합니다.

As an example, if you have a form that submits an array of files — e.g., the input name "files", submitting `files[0]` and `files[1]` — PHP will represent this as:
`$_FILES` 슈퍼 전역변수는 파일 입력 배열을 다룰 때 잘 알려진 문제점을 가지고 있습니다.
예를 들어, 파일의 배열 (예 : 입력 이름이 "files"인 경우, `files[0]` 및 `files[1]`)을 제출하는 폼이 있는 경우 PHP는 다음과 같이 표현합니다.

~~~php
array(
    'files' => array(
        'name' => array(
            0 => 'file0.txt',
            1 => 'file1.html',
        ),
        'type' => array(
            0 => 'text/plain',
            1 => 'text/html',
        ),
        /* etc. */
    ),
)
~~~

아래와 같이 예상되는 것 대신 말이죠

~~~php
array(
    'files' => array(
        0 => array(
            'name' => 'file0.txt',
            'type' => 'text/plain',
            /* etc. */
        ),
        1 => array(
            'name' => 'file1.html',
            'type' => 'text/html',
            /* etc. */
        ),
    ),
)
~~~

결과적으로 사용자는 이 언어 구현 세부 사항을 알아야하고 주어진 업로드에 대한 데이터를 수집하기위한 코드를 작성해야합니다.

또한 아래와 같은 파일 업로드를 했을 때 `$ _FILES` 가 비어있는 경우가 있습니다

- HTTP method가 `POST` 아닐 때.
- 유닛 테스팅 중일 때.
- [ReactPHP](http://reactphp.org)와 같은 SAPI가 아닌 환경에서 작동 할 때.

이 경우 데이터를 다르게 시드해야합니다.
예를 들면 다음과 같습니다.

- 프로세스가 메시지 본문을 구문 분석하여 파일 업로드를 발견 할 수 있습니다.
이 경우 구현은 파일 업로드를 파일 시스템에 쓰지 *않고* 대신 메모리, I/O 및 저장소 오버 헤드를 줄이기 위해 스트림으로 래핑하도록 선택할 수 있습니다.
- 단위 테스트 시나리오에서 개발자는 다른 시나리오의 유효성을 검사하고 검증하기 위해 파일 업로드 메타 데이터를 스텁(stub) 또는 모의(mock) 할 수 있어야합니다.

`getUploadedFiles ()`는 사용자를 위해 정규화 된 구조를 제공합니다.
구현은 다음을 기대 합니다.

- 주어진 파일 업로드에 대한 모든 정보를 모아서 `Psr\Http\Message\UploadedFileInterface` 인스턴스를 채웁니다.
- 전송된 트리 구조를 재작성합니다. 각 리프는 트리의 주어진 위치에 대한 적절한 `Psr\Http\Message\UploadedFileInterface` 인스턴스입니다.

참조 된 트리 구조는 파일이 제출 된 명명 구조를 모방해야합니다.

가장 단순한 예를 들어서, 이것은 다음과 같이 제출 된 단일 명명 된 양식 요소(form element) 일 수 있습니다.

~~~html
<input type="file" name="avatar" />
~~~

이 경우, `$_FILES`의 구조는 다음과 같습니다

~~~php
array(
    'avatar' => array(
        'tmp_name' => 'phpUxcOty',
        'name' => 'my-avatar.png',
        'size' => 90996,
        'type' => 'image/png',
        'error' => 0,
    ),
)
~~~

`getUploadedFiles()` 에 의해 반환된 정규화된 양식은 다음과 같습니다

~~~php
array(
    'avatar' => /* UploadedFileInterface instance */
)
~~~

이름에 배열 표기법을 사용하는 입력의 경우

~~~html
<input type="file" name="my-form[details][avatar]" />
~~~

`$_FILES`은 다음과 같이 보입니다

~~~php
array (
    'my-form' => array (
        'name' => array (
            'details' => array (
                'avatar' => 'my-avatar.png',
            ),
        ),
        'type' => array (
            'details' => array (
                'avatar' => 'image/png',
            ),
        ),
        'tmp_name' => array (
            'details' => array (
                'avatar' => 'phpmFLrzD',
            ),
        ),
        'error' => array (
            'details' => array (
                'avatar' => 0,
            ),
        ),
        'size' => array (
            'details' => array (
                'avatar' => 90996,
            ),
        ),
    ),
)
~~~

그리고 `getUploadedFiles()` 에 의해 반환 된 트리는 다음과 같아야합니다

~~~php
array(
    'my-form' => array(
        'details' => array(
            'avatar' => /* UploadedFileInterface instance */
        ),
    ),
)
~~~

경우에 따라 다음과 같이 파일 배열을 지정할 수 있습니다.

~~~html
Upload an avatar: <input type="file" name="my-form[details][avatars][]" />
Upload an avatar: <input type="file" name="my-form[details][avatars][]" />
~~~

(예를 들어 자바스크립트를 사용해 추가 파일 업로드 인풋을 생성하여 한 번에 여러 파일을 업로드 할 수 있습니다.)

이 경우, 스펙 구현은 주어진 인덱스에있는 파일과 관련된 모든 정보를 집계해야합니다.
왜냐하면 `$_FILES` 는 다음과 같은 경우 정상적인 구조에서 벗어났기 때문입니다

~~~php
array (
    'my-form' => array (
        'name' => array (
            'details' => array (
                'avatar' => array (
                    0 => 'my-avatar.png',
                    1 => 'my-avatar2.png',
                    2 => 'my-avatar3.png',
                ),
            ),
        ),
        'type' => array (
            'details' => array (
                'avatar' => array (
                    0 => 'image/png',
                    1 => 'image/png',
                    2 => 'image/png',
                ),
            ),
        ),
        'tmp_name' => array (
            'details' => array (
                'avatar' => array (
                    0 => 'phpmFLrzD',
                    1 => 'phpV2pBil',
                    2 => 'php8RUG8v',
                ),
            ),
        ),
        'error' => array (
            'details' => array (
                'avatar' => array (
                    0 => 0,
                    1 => 0,
                    2 => 0,
                ),
            ),
        ),
        'size' => array (
            'details' => array (
                'avatar' => array (
                    0 => 90996,
                    1 => 90996,
                    3 => 90996,
                ),
            ),
        ),
    ),
)
~~~

위의 `$_FILES` 배열은 `getUploadedFiles()` 에 의해 반환 될 경우 다음 구조체에 해당합니다

~~~php
array(
    'my-form' => array(
        'details' => array(
            'avatars' => array(
                0 => /* UploadedFileInterface instance */,
                1 => /* UploadedFileInterface instance */,
                2 => /* UploadedFileInterface instance */,
            ),
        ),
    ),
)
~~~

사용자는 다음을 사용하여 중첩 배열의 인덱스 `1`에 접근합니다.

~~~php
$request->getUploadedFiles()['my-form']['details']['avatars'][1];
~~~

업로드 된 파일 데이터는 파생물이므로 ( `$_FILES` 또는 요청 본문에서 파생 된), mutator 메소드 인 `withUploadedFiles()` 또한 인터페이스에 존재하므로 다른 프로세스에 대한 정규화를 위임 할 수 있습니다.

원래 예제의 경우 사용은 다음과 유사합니다.

~~~php
$file0 = $request->getUploadedFiles()['files'][0];
$file1 = $request->getUploadedFiles()['files'][1];

printf(
    "Received the files %s and %s",
    $file0->getClientFilename(),
    $file1->getClientFilename()
);

// "Received the files file0.txt and file1.html"
~~~

이 제안은 또한 구현이 비 SAPI 환경에서 작동 할 수 있음을 보장(recognizes)합니다.
따라서 `UploadedFileInterface` 는 환경에 관계없이 작동이 작동 할 수 있도록 보장하는 메소드를 제공합니다.
특히

- `moveTo($targetPath)` 는 임시 업로드 파일에 직접 `move_uploaded_file()`을 호출하는 안전하고 권장되는 대안으로 제공됩니다.
구현체는 환경에 따라 사용할 올바른 작업을 감지합니다.
- `getStream()` 은 `StreamInterface` 인스턴스를 리턴합니다.
비 SAPI 환경에서 개별 업로드 파일을 직접 파일 대신에 `php://temp` 스트림으로 파싱하는 것이 제안되었습니다. 이 경우 업로드 파일이 없습니다.
`getStream()`은 환경에 관계없이 작동하도록 보장됩니다.

예를 들면 다음과 같습니다.
~~~
// Move a file to an upload directory
$filename = sprintf(
    '%s.%s',
    create_uuid(),
    pathinfo($file0->getClientFilename(), PATHINFO_EXTENSION)
);
$file0->moveTo(DATA_DIR . '/' . $filename);

// Stream a file to Amazon S3.
// Assume $s3wrapper is a PHP stream that will write to S3, and that
// Psr7StreamWrapper is a class that will decorate a StreamInterface as a PHP
// StreamWrapper.
$stream = new Psr7StreamWrapper($file1->getStream());
stream_copy_to_stream($stream, $s3wrapper);
~~~

## 2. Package

설명 된 인터페이스와 클래스는 [psr/http-message](https://packagist.org/packages/psr/http-message) 패키지의 일부로 제공됩니다.

## 3. Interfaces

### 3.1 `Psr\Http\Message\MessageInterface`

~~~php
<?php
namespace Psr\Http\Message;

/**
 * HTTP messages consist of requests from a client to a server and responses
 * from a server to a client. This interface defines the methods common to
 * each.
 *
 * Messages are considered immutable; all methods that might change state MUST
 * be implemented such that they retain the internal state of the current
 * message and return an instance that contains the changed state.
 *
 * @see http://www.ietf.org/rfc/rfc7230.txt
 * @see http://www.ietf.org/rfc/rfc7231.txt
 */
interface MessageInterface
{
    /**
     * Retrieves the HTTP protocol version as a string.
     *
     * The string MUST contain only the HTTP version number (e.g., "1.1", "1.0").
     *
     * @return string HTTP protocol version.
     */
    public function getProtocolVersion();

    /**
     * Return an instance with the specified HTTP protocol version.
     *
     * The version string MUST contain only the HTTP version number (e.g.,
     * "1.1", "1.0").
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * new protocol version.
     *
     * @param string $version HTTP protocol version
     * @return static
     */
    public function withProtocolVersion($version);

    /**
     * Retrieves all message header values.
     *
     * The keys represent the header name as it will be sent over the wire, and
     * each value is an array of strings associated with the header.
     *
     *     // Represent the headers as a string
     *     foreach ($message->getHeaders() as $name => $values) {
     *         echo $name . ': ' . implode(', ', $values);
     *     }
     *
     *     // Emit headers iteratively:
     *     foreach ($message->getHeaders() as $name => $values) {
     *         foreach ($values as $value) {
     *             header(sprintf('%s: %s', $name, $value), false);
     *         }
     *     }
     *
     * While header names are not case-sensitive, getHeaders() will preserve the
     * exact case in which headers were originally specified.
     *
     * @return string[][] Returns an associative array of the message's headers.
     *     Each key MUST be a header name, and each value MUST be an array of
     *     strings for that header.
     */
    public function getHeaders();

    /**
     * Checks if a header exists by the given case-insensitive name.
     *
     * @param string $name Case-insensitive header field name.
     * @return bool Returns true if any header names match the given header
     *     name using a case-insensitive string comparison. Returns false if
     *     no matching header name is found in the message.
     */
    public function hasHeader($name);

    /**
     * Retrieves a message header value by the given case-insensitive name.
     *
     * This method returns an array of all the header values of the given
     * case-insensitive header name.
     *
     * If the header does not appear in the message, this method MUST return an
     * empty array.
     *
     * @param string $name Case-insensitive header field name.
     * @return string[] An array of string values as provided for the given
     *    header. If the header does not appear in the message, this method MUST
     *    return an empty array.
     */
    public function getHeader($name);

    /**
     * Retrieves a comma-separated string of the values for a single header.
     *
     * This method returns all of the header values of the given
     * case-insensitive header name as a string concatenated together using
     * a comma.
     *
     * NOTE: Not all header values may be appropriately represented using
     * comma concatenation. For such headers, use getHeader() instead
     * and supply your own delimiter when concatenating.
     *
     * If the header does not appear in the message, this method MUST return
     * an empty string.
     *
     * @param string $name Case-insensitive header field name.
     * @return string A string of values as provided for the given header
     *    concatenated together using a comma. If the header does not appear in
     *    the message, this method MUST return an empty string.
     */
    public function getHeaderLine($name);

    /**
     * Return an instance with the provided value replacing the specified header.
     *
     * While header names are case-insensitive, the casing of the header will
     * be preserved by this function, and returned from getHeaders().
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * new and/or updated header and value.
     *
     * @param string $name Case-insensitive header field name.
     * @param string|string[] $value Header value(s).
     * @return static
     * @throws \InvalidArgumentException for invalid header names or values.
     */
    public function withHeader($name, $value);

    /**
     * Return an instance with the specified header appended with the given value.
     *
     * Existing values for the specified header will be maintained. The new
     * value(s) will be appended to the existing list. If the header did not
     * exist previously, it will be added.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * new header and/or value.
     *
     * @param string $name Case-insensitive header field name to add.
     * @param string|string[] $value Header value(s).
     * @return static
     * @throws \InvalidArgumentException for invalid header names.
     * @throws \InvalidArgumentException for invalid header values.
     */
    public function withAddedHeader($name, $value);

    /**
     * Return an instance without the specified header.
     *
     * Header resolution MUST be done without case-sensitivity.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that removes
     * the named header.
     *
     * @param string $name Case-insensitive header field name to remove.
     * @return static
     */
    public function withoutHeader($name);

    /**
     * Gets the body of the message.
     *
     * @return StreamInterface Returns the body as a stream.
     */
    public function getBody();

    /**
     * Return an instance with the specified message body.
     *
     * The body MUST be a StreamInterface object.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return a new instance that has the
     * new body stream.
     *
     * @param StreamInterface $body Body.
     * @return static
     * @throws \InvalidArgumentException When the body is not valid.
     */
    public function withBody(StreamInterface $body);
}
~~~

### 3.2 `Psr\Http\Message\RequestInterface`

~~~php
<?php
namespace Psr\Http\Message;

/**
 * Representation of an outgoing, client-side request.
 *
 * Per the HTTP specification, this interface includes properties for
 * each of the following:
 *
 * - Protocol version
 * - HTTP method
 * - URI
 * - Headers
 * - Message body
 *
 * During construction, implementations MUST attempt to set the Host header from
 * a provided URI if no Host header is provided.
 *
 * Requests are considered immutable; all methods that might change state MUST
 * be implemented such that they retain the internal state of the current
 * message and return an instance that contains the changed state.
 */
interface RequestInterface extends MessageInterface
{
    /**
     * Retrieves the message's request target.
     *
     * Retrieves the message's request-target either as it will appear (for
     * clients), as it appeared at request (for servers), or as it was
     * specified for the instance (see withRequestTarget()).
     *
     * In most cases, this will be the origin-form of the composed URI,
     * unless a value was provided to the concrete implementation (see
     * withRequestTarget() below).
     *
     * If no URI is available, and no request-target has been specifically
     * provided, this method MUST return the string "/".
     *
     * @return string
     */
    public function getRequestTarget();

    /**
     * Return an instance with the specific request-target.
     *
     * If the request needs a non-origin-form request-target — e.g., for
     * specifying an absolute-form, authority-form, or asterisk-form —
     * this method may be used to create an instance with the specified
     * request-target, verbatim.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * changed request target.
     *
     * @see http://tools.ietf.org/html/rfc7230#section-5.3 (for the various
     *     request-target forms allowed in request messages)
     * @param mixed $requestTarget
     * @return static
     */
    public function withRequestTarget($requestTarget);

    /**
     * Retrieves the HTTP method of the request.
     *
     * @return string Returns the request method.
     */
    public function getMethod();

    /**
     * Return an instance with the provided HTTP method.
     *
     * While HTTP method names are typically all uppercase characters, HTTP
     * method names are case-sensitive and thus implementations SHOULD NOT
     * modify the given string.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * changed request method.
     *
     * @param string $method Case-sensitive method.
     * @return static
     * @throws \InvalidArgumentException for invalid HTTP methods.
     */
    public function withMethod($method);

    /**
     * Retrieves the URI instance.
     *
     * This method MUST return a UriInterface instance.
     *
     * @see http://tools.ietf.org/html/rfc3986#section-4.3
     * @return UriInterface Returns a UriInterface instance
     *     representing the URI of the request.
     */
    public function getUri();

    /**
     * Returns an instance with the provided URI.
     *
     * This method MUST update the Host header of the returned request by
     * default if the URI contains a host component. If the URI does not
     * contain a host component, any pre-existing Host header MUST be carried
     * over to the returned request.
     *
     * You can opt-in to preserving the original state of the Host header by
     * setting `$preserveHost` to `true`. When `$preserveHost` is set to
     * `true`, this method interacts with the Host header in the following ways:
     *
     * - If the Host header is missing or empty, and the new URI contains
     *   a host component, this method MUST update the Host header in the returned
     *   request.
     * - If the Host header is missing or empty, and the new URI does not contain a
     *   host component, this method MUST NOT update the Host header in the returned
     *   request.
     * - If a Host header is present and non-empty, this method MUST NOT update
     *   the Host header in the returned request.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * new UriInterface instance.
     *
     * @see http://tools.ietf.org/html/rfc3986#section-4.3
     * @param UriInterface $uri New request URI to use.
     * @param bool $preserveHost Preserve the original state of the Host header.
     * @return static
     */
    public function withUri(UriInterface $uri, $preserveHost = false);
}
~~~

#### 3.2.1 `Psr\Http\Message\ServerRequestInterface`

~~~php
<?php
namespace Psr\Http\Message;

/**
 * Representation of an incoming, server-side HTTP request.
 *
 * Per the HTTP specification, this interface includes properties for
 * each of the following:
 *
 * - Protocol version
 * - HTTP method
 * - URI
 * - Headers
 * - Message body
 *
 * Additionally, it encapsulates all data as it has arrived at the
 * application from the CGI and/or PHP environment, including:
 *
 * - The values represented in $_SERVER.
 * - Any cookies provided (generally via $_COOKIE)
 * - Query string arguments (generally via $_GET, or as parsed via parse_str())
 * - Upload files, if any (as represented by $_FILES)
 * - Deserialized body parameters (generally from $_POST)
 *
 * $_SERVER values MUST be treated as immutable, as they represent application
 * state at the time of request; as such, no methods are provided to allow
 * modification of those values. The other values provide such methods, as they
 * can be restored from $_SERVER or the request body, and may need treatment
 * during the application (e.g., body parameters may be deserialized based on
 * content type).
 *
 * Additionally, this interface recognizes the utility of introspecting a
 * request to derive and match additional parameters (e.g., via URI path
 * matching, decrypting cookie values, deserializing non-form-encoded body
 * content, matching authorization headers to users, etc). These parameters
 * are stored in an "attributes" property.
 *
 * Requests are considered immutable; all methods that might change state MUST
 * be implemented such that they retain the internal state of the current
 * message and return an instance that contains the changed state.
 */
interface ServerRequestInterface extends RequestInterface
{
    /**
     * Retrieve server parameters.
     *
     * Retrieves data related to the incoming request environment,
     * typically derived from PHP's $_SERVER superglobal. The data IS NOT
     * REQUIRED to originate from $_SERVER.
     *
     * @return array
     */
    public function getServerParams();

    /**
     * Retrieve cookies.
     *
     * Retrieves cookies sent by the client to the server.
     *
     * The data MUST be compatible with the structure of the $_COOKIE
     * superglobal.
     *
     * @return array
     */
    public function getCookieParams();

    /**
     * Return an instance with the specified cookies.
     *
     * The data IS NOT REQUIRED to come from the $_COOKIE superglobal, but MUST
     * be compatible with the structure of $_COOKIE. Typically, this data will
     * be injected at instantiation.
     *
     * This method MUST NOT update the related Cookie header of the request
     * instance, nor related values in the server params.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * updated cookie values.
     *
     * @param array $cookies Array of key/value pairs representing cookies.
     * @return static
     */
    public function withCookieParams(array $cookies);

    /**
     * Retrieve query string arguments.
     *
     * Retrieves the deserialized query string arguments, if any.
     *
     * Note: the query params might not be in sync with the URI or server
     * params. If you need to ensure you are only getting the original
     * values, you may need to parse the query string from `getUri()->getQuery()`
     * or from the `QUERY_STRING` server param.
     *
     * @return array
     */
    public function getQueryParams();

    /**
     * Return an instance with the specified query string arguments.
     *
     * These values SHOULD remain immutable over the course of the incoming
     * request. They MAY be injected during instantiation, such as from PHP's
     * $_GET superglobal, or MAY be derived from some other value such as the
     * URI. In cases where the arguments are parsed from the URI, the data
     * MUST be compatible with what PHP's parse_str() would return for
     * purposes of how duplicate query parameters are handled, and how nested
     * sets are handled.
     *
     * Setting query string arguments MUST NOT change the URI stored by the
     * request, nor the values in the server params.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * updated query string arguments.
     *
     * @param array $query Array of query string arguments, typically from
     *     $_GET.
     * @return static
     */
    public function withQueryParams(array $query);

    /**
     * Retrieve normalized file upload data.
     *
     * This method returns upload metadata in a normalized tree, with each leaf
     * an instance of Psr\Http\Message\UploadedFileInterface.
     *
     * These values MAY be prepared from $_FILES or the message body during
     * instantiation, or MAY be injected via withUploadedFiles().
     *
     * @return array An array tree of UploadedFileInterface instances; an empty
     *     array MUST be returned if no data is present.
     */
    public function getUploadedFiles();

    /**
     * Create a new instance with the specified uploaded files.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * updated body parameters.
     *
     * @param array $uploadedFiles An array tree of UploadedFileInterface instances.
     * @return static
     * @throws \InvalidArgumentException if an invalid structure is provided.
     */
    public function withUploadedFiles(array $uploadedFiles);

    /**
     * Retrieve any parameters provided in the request body.
     *
     * If the request Content-Type is either application/x-www-form-urlencoded
     * or multipart/form-data, and the request method is POST, this method MUST
     * return the contents of $_POST.
     *
     * Otherwise, this method may return any results of deserializing
     * the request body content; as parsing returns structured content, the
     * potential types MUST be arrays or objects only. A null value indicates
     * the absence of body content.
     *
     * @return null|array|object The deserialized body parameters, if any.
     *     These will typically be an array or object.
     */
    public function getParsedBody();

    /**
     * Return an instance with the specified body parameters.
     *
     * These MAY be injected during instantiation.
     *
     * If the request Content-Type is either application/x-www-form-urlencoded
     * or multipart/form-data, and the request method is POST, use this method
     * ONLY to inject the contents of $_POST.
     *
     * The data IS NOT REQUIRED to come from $_POST, but MUST be the results of
     * deserializing the request body content. Deserialization/parsing returns
     * structured data, and, as such, this method ONLY accepts arrays or objects,
     * or a null value if nothing was available to parse.
     *
     * As an example, if content negotiation determines that the request data
     * is a JSON payload, this method could be used to create a request
     * instance with the deserialized parameters.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * updated body parameters.
     *
     * @param null|array|object $data The deserialized body data. This will
     *     typically be in an array or object.
     * @return static
     * @throws \InvalidArgumentException if an unsupported argument type is
     *     provided.
     */
    public function withParsedBody($data);

    /**
     * Retrieve attributes derived from the request.
     *
     * The request "attributes" may be used to allow injection of any
     * parameters derived from the request: e.g., the results of path
     * match operations; the results of decrypting cookies; the results of
     * deserializing non-form-encoded message bodies; etc. Attributes
     * will be application and request specific, and CAN be mutable.
     *
     * @return mixed[] Attributes derived from the request.
     */
    public function getAttributes();

    /**
     * Retrieve a single derived request attribute.
     *
     * Retrieves a single derived request attribute as described in
     * getAttributes(). If the attribute has not been previously set, returns
     * the default value as provided.
     *
     * This method obviates the need for a hasAttribute() method, as it allows
     * specifying a default value to return if the attribute is not found.
     *
     * @see getAttributes()
     * @param string $name The attribute name.
     * @param mixed $default Default value to return if the attribute does not exist.
     * @return mixed
     */
    public function getAttribute($name, $default = null);

    /**
     * Return an instance with the specified derived request attribute.
     *
     * This method allows setting a single derived request attribute as
     * described in getAttributes().
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * updated attribute.
     *
     * @see getAttributes()
     * @param string $name The attribute name.
     * @param mixed $value The value of the attribute.
     * @return static
     */
    public function withAttribute($name, $value);

    /**
     * Return an instance that removes the specified derived request attribute.
     *
     * This method allows removing a single derived request attribute as
     * described in getAttributes().
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that removes
     * the attribute.
     *
     * @see getAttributes()
     * @param string $name The attribute name.
     * @return static
     */
    public function withoutAttribute($name);
}
~~~

### 3.3 `Psr\Http\Message\ResponseInterface`

~~~php
<?php
namespace Psr\Http\Message;

/**
 * Representation of an outgoing, server-side response.
 *
 * Per the HTTP specification, this interface includes properties for
 * each of the following:
 *
 * - Protocol version
 * - Status code and reason phrase
 * - Headers
 * - Message body
 *
 * Responses are considered immutable; all methods that might change state MUST
 * be implemented such that they retain the internal state of the current
 * message and return an instance that contains the changed state.
 */
interface ResponseInterface extends MessageInterface
{
    /**
     * Gets the response status code.
     *
     * The status code is a 3-digit integer result code of the server's attempt
     * to understand and satisfy the request.
     *
     * @return int Status code.
     */
    public function getStatusCode();

    /**
     * Return an instance with the specified status code and, optionally, reason phrase.
     *
     * If no reason phrase is specified, implementations MAY choose to default
     * to the RFC 7231 or IANA recommended reason phrase for the response's
     * status code.
     *
     * This method MUST be implemented in such a way as to retain the
     * immutability of the message, and MUST return an instance that has the
     * updated status and reason phrase.
     *
     * @see http://tools.ietf.org/html/rfc7231#section-6
     * @see http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml
     * @param int $code The 3-digit integer result code to set.
     * @param string $reasonPhrase The reason phrase to use with the
     *     provided status code; if none is provided, implementations MAY
     *     use the defaults as suggested in the HTTP specification.
     * @return static
     * @throws \InvalidArgumentException For invalid status code arguments.
     */
    public function withStatus($code, $reasonPhrase = '');

    /**
     * Gets the response reason phrase associated with the status code.
     *
     * Because a reason phrase is not a required element in a response
     * status line, the reason phrase value MAY be empty. Implementations MAY
     * choose to return the default RFC 7231 recommended reason phrase (or those
     * listed in the IANA HTTP Status Code Registry) for the response's
     * status code.
     *
     * @see http://tools.ietf.org/html/rfc7231#section-6
     * @see http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml
     * @return string Reason phrase; must return an empty string if none present.
     */
    public function getReasonPhrase();
}
~~~

### 3.4 `Psr\Http\Message\StreamInterface`

~~~php
<?php
namespace Psr\Http\Message;

/**
 * Describes a data stream.
 *
 * Typically, an instance will wrap a PHP stream; this interface provides
 * a wrapper around the most common operations, including serialization of
 * the entire stream to a string.
 */
interface StreamInterface
{
    /**
     * Reads all data from the stream into a string, from the beginning to end.
     *
     * This method MUST attempt to seek to the beginning of the stream before
     * reading data and read the stream until the end is reached.
     *
     * Warning: This could attempt to load a large amount of data into memory.
     *
     * This method MUST NOT raise an exception in order to conform with PHP's
     * string casting operations.
     *
     * @see http://php.net/manual/en/language.oop5.magic.php#object.tostring
     * @return string
     */
    public function __toString();

    /**
     * Closes the stream and any underlying resources.
     *
     * @return void
     */
    public function close();

    /**
     * Separates any underlying resources from the stream.
     *
     * After the stream has been detached, the stream is in an unusable state.
     *
     * @return resource|null Underlying PHP stream, if any
     */
    public function detach();

    /**
     * Get the size of the stream if known.
     *
     * @return int|null Returns the size in bytes if known, or null if unknown.
     */
    public function getSize();

    /**
     * Returns the current position of the file read/write pointer
     *
     * @return int Position of the file pointer
     * @throws \RuntimeException on error.
     */
    public function tell();

    /**
     * Returns true if the stream is at the end of the stream.
     *
     * @return bool
     */
    public function eof();

    /**
     * Returns whether or not the stream is seekable.
     *
     * @return bool
     */
    public function isSeekable();

    /**
     * Seek to a position in the stream.
     *
     * @see http://www.php.net/manual/en/function.fseek.php
     * @param int $offset Stream offset
     * @param int $whence Specifies how the cursor position will be calculated
     *     based on the seek offset. Valid values are identical to the built-in
     *     PHP $whence values for `fseek()`.  SEEK_SET: Set position equal to
     *     offset bytes SEEK_CUR: Set position to current location plus offset
     *     SEEK_END: Set position to end-of-stream plus offset.
     * @throws \RuntimeException on failure.
     */
    public function seek($offset, $whence = SEEK_SET);

    /**
     * Seek to the beginning of the stream.
     *
     * If the stream is not seekable, this method will raise an exception;
     * otherwise, it will perform a seek(0).
     *
     * @see seek()
     * @see http://www.php.net/manual/en/function.fseek.php
     * @throws \RuntimeException on failure.
     */
    public function rewind();

    /**
     * Returns whether or not the stream is writable.
     *
     * @return bool
     */
    public function isWritable();

    /**
     * Write data to the stream.
     *
     * @param string $string The string that is to be written.
     * @return int Returns the number of bytes written to the stream.
     * @throws \RuntimeException on failure.
     */
    public function write($string);

    /**
     * Returns whether or not the stream is readable.
     *
     * @return bool
     */
    public function isReadable();

    /**
     * Read data from the stream.
     *
     * @param int $length Read up to $length bytes from the object and return
     *     them. Fewer than $length bytes may be returned if underlying stream
     *     call returns fewer bytes.
     * @return string Returns the data read from the stream, or an empty string
     *     if no bytes are available.
     * @throws \RuntimeException if an error occurs.
     */
    public function read($length);

    /**
     * Returns the remaining contents in a string
     *
     * @return string
     * @throws \RuntimeException if unable to read.
     * @throws \RuntimeException if error occurs while reading.
     */
    public function getContents();

    /**
     * Get stream metadata as an associative array or retrieve a specific key.
     *
     * The keys returned are identical to the keys returned from PHP's
     * stream_get_meta_data() function.
     *
     * @see http://php.net/manual/en/function.stream-get-meta-data.php
     * @param string $key Specific metadata to retrieve.
     * @return array|mixed|null Returns an associative array if no key is
     *     provided. Returns a specific key value if a key is provided and the
     *     value is found, or null if the key is not found.
     */
    public function getMetadata($key = null);
}
~~~

### 3.5 `Psr\Http\Message\UriInterface`

~~~php
<?php
namespace Psr\Http\Message;

/**
 * Value object representing a URI.
 *
 * This interface is meant to represent URIs according to RFC 3986 and to
 * provide methods for most common operations. Additional functionality for
 * working with URIs can be provided on top of the interface or externally.
 * Its primary use is for HTTP requests, but may also be used in other
 * contexts.
 *
 * Instances of this interface are considered immutable; all methods that
 * might change state MUST be implemented such that they retain the internal
 * state of the current instance and return an instance that contains the
 * changed state.
 *
 * Typically the Host header will also be present in the request message.
 * For server-side requests, the scheme will typically be discoverable in the
 * server parameters.
 *
 * @see http://tools.ietf.org/html/rfc3986 (the URI specification)
 */
interface UriInterface
{
    /**
     * Retrieve the scheme component of the URI.
     *
     * If no scheme is present, this method MUST return an empty string.
     *
     * The value returned MUST be normalized to lowercase, per RFC 3986
     * Section 3.1.
     *
     * The trailing ":" character is not part of the scheme and MUST NOT be
     * added.
     *
     * @see https://tools.ietf.org/html/rfc3986#section-3.1
     * @return string The URI scheme.
     */
    public function getScheme();

    /**
     * Retrieve the authority component of the URI.
     *
     * If no authority information is present, this method MUST return an empty
     * string.
     *
     * The authority syntax of the URI is:
     *
     * <pre>
     * [user-info@]host[:port]
     * </pre>
     *
     * If the port component is not set or is the standard port for the current
     * scheme, it SHOULD NOT be included.
     *
     * @see https://tools.ietf.org/html/rfc3986#section-3.2
     * @return string The URI authority, in "[user-info@]host[:port]" format.
     */
    public function getAuthority();

    /**
     * Retrieve the user information component of the URI.
     *
     * If no user information is present, this method MUST return an empty
     * string.
     *
     * If a user is present in the URI, this will return that value;
     * additionally, if the password is also present, it will be appended to the
     * user value, with a colon (":") separating the values.
     *
     * The trailing "@" character is not part of the user information and MUST
     * NOT be added.
     *
     * @return string The URI user information, in "username[:password]" format.
     */
    public function getUserInfo();

    /**
     * Retrieve the host component of the URI.
     *
     * If no host is present, this method MUST return an empty string.
     *
     * The value returned MUST be normalized to lowercase, per RFC 3986
     * Section 3.2.2.
     *
     * @see http://tools.ietf.org/html/rfc3986#section-3.2.2
     * @return string The URI host.
     */
    public function getHost();

    /**
     * Retrieve the port component of the URI.
     *
     * If a port is present, and it is non-standard for the current scheme,
     * this method MUST return it as an integer. If the port is the standard port
     * used with the current scheme, this method SHOULD return null.
     *
     * If no port is present, and no scheme is present, this method MUST return
     * a null value.
     *
     * If no port is present, but a scheme is present, this method MAY return
     * the standard port for that scheme, but SHOULD return null.
     *
     * @return null|int The URI port.
     */
    public function getPort();

    /**
     * Retrieve the path component of the URI.
     *
     * The path can either be empty or absolute (starting with a slash) or
     * rootless (not starting with a slash). Implementations MUST support all
     * three syntaxes.
     *
     * Normally, the empty path "" and absolute path "/" are considered equal as
     * defined in RFC 7230 Section 2.7.3. But this method MUST NOT automatically
     * do this normalization because in contexts with a trimmed base path, e.g.
     * the front controller, this difference becomes significant. It's the task
     * of the user to handle both "" and "/".
     *
     * The value returned MUST be percent-encoded, but MUST NOT double-encode
     * any characters. To determine what characters to encode, please refer to
     * RFC 3986, Sections 2 and 3.3.
     *
     * As an example, if the value should include a slash ("/") not intended as
     * delimiter between path segments, that value MUST be passed in encoded
     * form (e.g., "%2F") to the instance.
     *
     * @see https://tools.ietf.org/html/rfc3986#section-2
     * @see https://tools.ietf.org/html/rfc3986#section-3.3
     * @return string The URI path.
     */
    public function getPath();

    /**
     * Retrieve the query string of the URI.
     *
     * If no query string is present, this method MUST return an empty string.
     *
     * The leading "?" character is not part of the query and MUST NOT be
     * added.
     *
     * The value returned MUST be percent-encoded, but MUST NOT double-encode
     * any characters. To determine what characters to encode, please refer to
     * RFC 3986, Sections 2 and 3.4.
     *
     * As an example, if a value in a key/value pair of the query string should
     * include an ampersand ("&") not intended as a delimiter between values,
     * that value MUST be passed in encoded form (e.g., "%26") to the instance.
     *
     * @see https://tools.ietf.org/html/rfc3986#section-2
     * @see https://tools.ietf.org/html/rfc3986#section-3.4
     * @return string The URI query string.
     */
    public function getQuery();

    /**
     * Retrieve the fragment component of the URI.
     *
     * If no fragment is present, this method MUST return an empty string.
     *
     * The leading "#" character is not part of the fragment and MUST NOT be
     * added.
     *
     * The value returned MUST be percent-encoded, but MUST NOT double-encode
     * any characters. To determine what characters to encode, please refer to
     * RFC 3986, Sections 2 and 3.5.
     *
     * @see https://tools.ietf.org/html/rfc3986#section-2
     * @see https://tools.ietf.org/html/rfc3986#section-3.5
     * @return string The URI fragment.
     */
    public function getFragment();

    /**
     * Return an instance with the specified scheme.
     *
     * This method MUST retain the state of the current instance, and return
     * an instance that contains the specified scheme.
     *
     * Implementations MUST support the schemes "http" and "https" case
     * insensitively, and MAY accommodate other schemes if required.
     *
     * An empty scheme is equivalent to removing the scheme.
     *
     * @param string $scheme The scheme to use with the new instance.
     * @return static A new instance with the specified scheme.
     * @throws \InvalidArgumentException for invalid schemes.
     * @throws \InvalidArgumentException for unsupported schemes.
     */
    public function withScheme($scheme);

    /**
     * Return an instance with the specified user information.
     *
     * This method MUST retain the state of the current instance, and return
     * an instance that contains the specified user information.
     *
     * Password is optional, but the user information MUST include the
     * user; an empty string for the user is equivalent to removing user
     * information.
     *
     * @param string $user The user name to use for authority.
     * @param null|string $password The password associated with $user.
     * @return static A new instance with the specified user information.
     */
    public function withUserInfo($user, $password = null);

    /**
     * Return an instance with the specified host.
     *
     * This method MUST retain the state of the current instance, and return
     * an instance that contains the specified host.
     *
     * An empty host value is equivalent to removing the host.
     *
     * @param string $host The hostname to use with the new instance.
     * @return static A new instance with the specified host.
     * @throws \InvalidArgumentException for invalid hostnames.
     */
    public function withHost($host);

    /**
     * Return an instance with the specified port.
     *
     * This method MUST retain the state of the current instance, and return
     * an instance that contains the specified port.
     *
     * Implementations MUST raise an exception for ports outside the
     * established TCP and UDP port ranges.
     *
     * A null value provided for the port is equivalent to removing the port
     * information.
     *
     * @param null|int $port The port to use with the new instance; a null value
     *     removes the port information.
     * @return static A new instance with the specified port.
     * @throws \InvalidArgumentException for invalid ports.
     */
    public function withPort($port);

    /**
     * Return an instance with the specified path.
     *
     * This method MUST retain the state of the current instance, and return
     * an instance that contains the specified path.
     *
     * The path can either be empty or absolute (starting with a slash) or
     * rootless (not starting with a slash). Implementations MUST support all
     * three syntaxes.
     *
     * If an HTTP path is intended to be host-relative rather than path-relative
     * then it must begin with a slash ("/"). HTTP paths not starting with a slash
     * are assumed to be relative to some base path known to the application or
     * consumer.
     *
     * Users can provide both encoded and decoded path characters.
     * Implementations ensure the correct encoding as outlined in getPath().
     *
     * @param string $path The path to use with the new instance.
     * @return static A new instance with the specified path.
     * @throws \InvalidArgumentException for invalid paths.
     */
    public function withPath($path);

    /**
     * Return an instance with the specified query string.
     *
     * This method MUST retain the state of the current instance, and return
     * an instance that contains the specified query string.
     *
     * Users can provide both encoded and decoded query characters.
     * Implementations ensure the correct encoding as outlined in getQuery().
     *
     * An empty query string value is equivalent to removing the query string.
     *
     * @param string $query The query string to use with the new instance.
     * @return static A new instance with the specified query string.
     * @throws \InvalidArgumentException for invalid query strings.
     */
    public function withQuery($query);

    /**
     * Return an instance with the specified URI fragment.
     *
     * This method MUST retain the state of the current instance, and return
     * an instance that contains the specified URI fragment.
     *
     * Users can provide both encoded and decoded fragment characters.
     * Implementations ensure the correct encoding as outlined in getFragment().
     *
     * An empty fragment value is equivalent to removing the fragment.
     *
     * @param string $fragment The fragment to use with the new instance.
     * @return static A new instance with the specified fragment.
     */
    public function withFragment($fragment);

    /**
     * Return the string representation as a URI reference.
     *
     * Depending on which components of the URI are present, the resulting
     * string is either a full URI or relative reference according to RFC 3986,
     * Section 4.1. The method concatenates the various components of the URI,
     * using the appropriate delimiters:
     *
     * - If a scheme is present, it MUST be suffixed by ":".
     * - If an authority is present, it MUST be prefixed by "//".
     * - The path can be concatenated without delimiters. But there are two
     *   cases where the path has to be adjusted to make the URI reference
     *   valid as PHP does not allow to throw an exception in __toString():
     *     - If the path is rootless and an authority is present, the path MUST
     *       be prefixed by "/".
     *     - If the path is starting with more than one "/" and no authority is
     *       present, the starting slashes MUST be reduced to one.
     * - If a query is present, it MUST be prefixed by "?".
     * - If a fragment is present, it MUST be prefixed by "#".
     *
     * @see http://tools.ietf.org/html/rfc3986#section-4.1
     * @return string
     */
    public function __toString();
}
~~~

### 3.6 `Psr\Http\Message\UploadedFileInterface`

~~~php
<?php
namespace Psr\Http\Message;

/**
 * Value object representing a file uploaded through an HTTP request.
 *
 * Instances of this interface are considered immutable; all methods that
 * might change state MUST be implemented such that they retain the internal
 * state of the current instance and return an instance that contains the
 * changed state.
 */
interface UploadedFileInterface
{
    /**
     * Retrieve a stream representing the uploaded file.
     *
     * This method MUST return a StreamInterface instance, representing the
     * uploaded file. The purpose of this method is to allow utilizing native PHP
     * stream functionality to manipulate the file upload, such as
     * stream_copy_to_stream() (though the result will need to be decorated in a
     * native PHP stream wrapper to work with such functions).
     *
     * If the moveTo() method has been called previously, this method MUST raise
     * an exception.
     *
     * @return StreamInterface Stream representation of the uploaded file.
     * @throws \RuntimeException in cases when no stream is available.
     * @throws \RuntimeException in cases when no stream can be created.
     */
    public function getStream();

    /**
     * Move the uploaded file to a new location.
     *
     * Use this method as an alternative to move_uploaded_file(). This method is
     * guaranteed to work in both SAPI and non-SAPI environments.
     * Implementations must determine which environment they are in, and use the
     * appropriate method (move_uploaded_file(), rename(), or a stream
     * operation) to perform the operation.
     *
     * $targetPath may be an absolute path, or a relative path. If it is a
     * relative path, resolution should be the same as used by PHP's rename()
     * function.
     *
     * The original file or stream MUST be removed on completion.
     *
     * If this method is called more than once, any subsequent calls MUST raise
     * an exception.
     *
     * When used in an SAPI environment where $_FILES is populated, when writing
     * files via moveTo(), is_uploaded_file() and move_uploaded_file() SHOULD be
     * used to ensure permissions and upload status are verified correctly.
     *
     * If you wish to move to a stream, use getStream(), as SAPI operations
     * cannot guarantee writing to stream destinations.
     *
     * @see http://php.net/is_uploaded_file
     * @see http://php.net/move_uploaded_file
     * @param string $targetPath Path to which to move the uploaded file.
     * @throws \InvalidArgumentException if the $targetPath specified is invalid.
     * @throws \RuntimeException on any error during the move operation.
     * @throws \RuntimeException on the second or subsequent call to the method.
     */
    public function moveTo($targetPath);

    /**
     * Retrieve the file size.
     *
     * Implementations SHOULD return the value stored in the "size" key of
     * the file in the $_FILES array if available, as PHP calculates this based
     * on the actual size transmitted.
     *
     * @return int|null The file size in bytes or null if unknown.
     */
    public function getSize();

    /**
     * Retrieve the error associated with the uploaded file.
     *
     * The return value MUST be one of PHP's UPLOAD_ERR_XXX constants.
     *
     * If the file was uploaded successfully, this method MUST return
     * UPLOAD_ERR_OK.
     *
     * Implementations SHOULD return the value stored in the "error" key of
     * the file in the $_FILES array.
     *
     * @see http://php.net/manual/en/features.file-upload.errors.php
     * @return int One of PHP's UPLOAD_ERR_XXX constants.
     */
    public function getError();

    /**
     * Retrieve the filename sent by the client.
     *
     * Do not trust the value returned by this method. A client could send
     * a malicious filename with the intention to corrupt or hack your
     * application.
     *
     * Implementations SHOULD return the value stored in the "name" key of
     * the file in the $_FILES array.
     *
     * @return string|null The filename sent by the client or null if none
     *     was provided.
     */
    public function getClientFilename();

    /**
     * Retrieve the media type sent by the client.
     *
     * Do not trust the value returned by this method. A client could send
     * a malicious media type with the intention to corrupt or hack your
     * application.
     *
     * Implementations SHOULD return the value stored in the "type" key of
     * the file in the $_FILES array.
     *
     * @return string|null The media type sent by the client or null if none
     *     was provided.
     */
    public function getClientMediaType();
}
~~~
