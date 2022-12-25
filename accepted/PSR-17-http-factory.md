HTTP Factories
==============

이 문서는 [PSR-7][psr7]에 호환하는 HTTP 객체를 만드는 팩토리의 공통 표준을 설명합니다.

PSR-7은 HTTP 객체를 만드는 방법에 대한 권장 사항을 포함하지 않았기 때문에 PSR-7의 특정 구현과 관련되지 않은 구성 요소 내에 새로운 HTTP 객체를 생성해야 할 때 어려움을 겪습니다.

이 문서에 설명 된 인터페이스는 PSR-7 객체를 인스턴스화 할 수 있는 방법을 설명합니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119][rfc2119]에 설명 된대로 해석해야 합니다.

> 역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다

[psr7]: https://www.php-fig.org/psr/psr-7/
[rfc2119]: https://tools.ietf.org/html/rfc2119

## 1. 명세서

HTTP 팩토리는 PSR-7에 정의 된대로 새 HTTP 객체를 만드는 방법입니다.
HTTP 팩토리는 패키지가 제공하는 각 객체 유형에 대해 이러한 인터페이스를 구현해야합니다 (MUST).

## 2. Interfaces

다음 인터페이스들은 단일 클래스 내에서 또는 별도의 클래스들 내에서 함께 구현 될 수있습니다 (MAY).

### 2.1 RequestFactoryInterface

클라이언트 요청을 생성하는 기능

```php
namespace Psr\Http\Message;

use Psr\Http\Message\RequestInterface;
use Psr\Http\Message\UriInterface;

interface RequestFactoryInterface
{
    /**
     * Create a new request.
     *
     * @param string $method The HTTP method associated with the request.
     * @param UriInterface|string $uri The URI associated with the request. 
     */
    public function createRequest(string $method, $uri): RequestInterface;
}
```

### 2.2 ResponseFactoryInterface

응답을 생성하는 하는 기능

```php
namespace Psr\Http\Message;

use Psr\Http\Message\ResponseInterface;

interface ResponseFactoryInterface
{
    /**
     * Create a new response.
     *
     * @param int $code The HTTP status code. Defaults to 200.
     * @param string $reasonPhrase The reason phrase to associate with the status code
     *     in the generated response. If none is provided, implementations MAY use
     *     the defaults as suggested in the HTTP specification.
     */
    public function createResponse(int $code = 200, string $reasonPhrase = ''): ResponseInterface;
}
```

### 2.3 ServerRequestFactoryInterface

서버 요청을 생성하는 기능

```php
namespace Psr\Http\Message;

use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Message\UriInterface;

interface ServerRequestFactoryInterface
{
    /**
     * Create a new server request.
     *
     * Note that server parameters are taken precisely as given - no parsing/processing
     * of the given values is performed. In particular, no attempt is made to
     * determine the HTTP method or URI, which must be provided explicitly.
     *
     * @param string $method The HTTP method associated with the request.
     * @param UriInterface|string $uri The URI associated with the request. 
     * @param array $serverParams An array of Server API (SAPI) parameters with
     *     which to seed the generated request instance.
     */
    public function createServerRequest(string $method, $uri, array $serverParams = []): ServerRequestInterface;
}
```

### 2.4 StreamFactoryInterface

요청 및 응답을 위한 스트림을 생성하는 기능

```php
namespace Psr\Http\Message;

use Psr\Http\Message\StreamInterface;

interface StreamFactoryInterface
{
    /**
     * Create a new stream from a string.
     *
     * The stream SHOULD be created with a temporary resource.
     *
     * @param string $content String content with which to populate the stream.
     */
    public function createStream(string $content = ''): StreamInterface;

    /**
     * Create a stream from an existing file.
     *
     * The file MUST be opened using the given mode, which may be any mode
     * supported by the `fopen` function.
     *
     * The `$filename` MAY be any string supported by `fopen()`.
     *
     * @param string $filename The filename or stream URI to use as basis of stream.
     * @param string $mode The mode with which to open the underlying filename/stream.
     *
     * @throws \RuntimeException If the file cannot be opened.
     * @throws \InvalidArgumentException If the mode is invalid.
     */
    public function createStreamFromFile(string $filename, string $mode = 'r'): StreamInterface;

    /**
     * Create a new stream from an existing resource.
     *
     * The stream MUST be readable and may be writable.
     *
     * @param resource $resource The PHP resource to use as the basis for the stream.
     */
    public function createStreamFromResource($resource): StreamInterface;
}
```

이 인터페이스의 구현은 문자열에서 리소스를 만들 때 임시 스트림을 사용해야합니다 (SHOULD).
이렇게하는 권장 방법은 다음과 같습니다 (RECOMMENDED).

```php
$resource = fopen('php://temp', 'r+');
```

### 2.5 UploadedFileFactoryInterface

업로드 된 파일의 스트림을 만들 수 있는 기능

```php
namespace Psr\Http\Message;

use Psr\Http\Message\StreamInterface;
use Psr\Http\Message\UploadedFileInterface;

interface UploadedFileFactoryInterface
{
    /**
     * Create a new uploaded file.
     *
     * If a size is not provided it will be determined by checking the size of
     * the stream.
     *
     * @link http://php.net/manual/features.file-upload.post-method.php
     * @link http://php.net/manual/features.file-upload.errors.php
     *
     * @param StreamInterface $stream The underlying stream representing the
     *     uploaded file content.
     * @param int $size The size of the file in bytes.
     * @param int $error The PHP file upload error.
     * @param string $clientFilename The filename as provided by the client, if any.
     * @param string $clientMediaType The media type as provided by the client, if any.
     *
     * @throws \InvalidArgumentException If the file resource is not readable.
     */
    public function createUploadedFile(
        StreamInterface $stream,
        int $size = null,
        int $error = \UPLOAD_ERR_OK,
        string $clientFilename = null,
        string $clientMediaType = null
    ): UploadedFileInterface;
}
```

### 2.6 UriFactoryInterface

클라이언트 및 서버 요청에 대한 URI를 생성하는 기능

```php
namespace Psr\Http\Message;

use Psr\Http\Message\UriInterface;

interface UriFactoryInterface
{
    /**
     * Create a new URI.
     *
     * @param string $uri The URI to parse.
     *
     * @throws \InvalidArgumentException If the given URI cannot be parsed.
     */
    public function createUri(string $uri = '') : UriInterface;
}
```
