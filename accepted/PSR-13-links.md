# Link definition interfaces

하이퍼 미디어 링크는 HTML 컨텍스트와 다양한 API 형식 컨텍스트에서 점점 더 중요한 웹의 일부가 되고 있습니다.
그러나 일반적인 하이퍼 미디어 형식은 하나도 없으며 형식 간 링크를 나타내는 일반적인 방법도 없습니다. `역자주: 완전히 표준화되지 못했다는 의미`

이 스펙은 PHP 개발자에게 사용되는 직렬화 형식과는 별도로 하이퍼 미디어 링크를 표현하는 간단하고 일반적인 방법을 제공하는 것을 목표로 합니다.
그래서 시스템은 하이퍼 미디어 링크를 사용하여 하나 이상의 연결된 포맷으로 응답을 직렬화 할 수 있습니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

### References

- [RFC 2119](http://tools.ietf.org/html/rfc2119)
- [RFC 4287](https://tools.ietf.org/html/rfc4287)
- [RFC 5988](https://tools.ietf.org/html/rfc5988)
- [RFC 6570](https://tools.ietf.org/html/rfc6570)
- [IANA Link Relations Registry](http://www.iana.org/assignments/link-relations/link-relations.xhtml)
- [Microformats Relations List](http://microformats.org/wiki/existing-rel-values#HTML5_link_type_extensions)

## 1. 명세서

### 1.1 Basic links

하이퍼 미디어 링크는 최소한 다음으로 구성됩니다.
- 참조되는 대상 자원을 나타내는 URI입니다.
- 대상 자원과 관련된 소스의 관계를 정의합니다.

사용 된 형식에 따라 링크의 다양한 다른 속성이 존재할 수 있습니다.
추가 속성(additional attributes)은 표준화되거나 보편화되지 않았기 때문에 이 표준은 표준화하려고하지 않습니다.

이 명세서는 목정상 다음의 정의가 적용됩니다.

* **Implementing Object** - 이 명세에 정의 된 인터페이스 중 하나를 구현하는 객체.
* **Serializer** - 하나 이상의 Link 객체를 사용하고 정의 된 형식으로 직렬화 된 표현을 생성하는 라이브러리 또는 기타 시스템입니다.

### 1.2 Attributes

모든 링크는 URI와 관계를 넘어 0개 이상의 추가 속성을 포함 할 수있습니다(MAY).
여기에 허용되는 값의 정식 등록된 것이 없으며 값의 유효성은 컨텍스트 및 종종 특정 직렬화 형식에 따라 다릅니다.
일반적으로 지원되는 값에는 'hreflang', 'title'및 'type'이 포함됩니다.

직렬화 포맷에 의해 필요한 경우 링크 객체의 속성을 생략 할 수 있습니다(MAY).
그러나 serializers는 serialization 형식의 정의에 의해 방지되지 않는 한 사용자 확장을 허용하기 위해 가능한 모든 제공된 속성을 인코딩해야합니다 (SHOULD).

일부 속성 (일반적으로 `hreflang`)은 그들의 문맥에 두 번 이상 나타날 수 있습니다. 
따라서 속성 값은 단순한 값이 아닌 값의 배열 일 수 있습니다(MAY).
Serializers는 직렬화 된 형식 (공백으로 구분 된 목록, 쉼표로 구분 된 목록 등)에 적합한 형식으로 배열을 인코딩 할 수 있습니다 (MAY).
특정 속성이 특정 컨텍스트에서 다중 값을 가질 수 없는 경우, 직렬자는 제공된 첫 번째 값을 사용하고 모든 후속 값을 무시해야합니다 (MUST).

속성 값이 부울 `true`이면, serializers는 적절한 경우 축약형을 사용할 수 있고 직렬화 형식으로 지원할 수 있습니다 (MAY).
예를 들어, HTML은 속성의 존재가 부울 의미를 가질 때 속성이 값을 갖지 못하게합니다.
이 규칙은 속성이 부울 `true`인 경우에만 적용되며 정수 1과 같은 PHP의 "truthy"값은 적용되지 않습니다.

속성 값이 부울 `false` 인 경우, serializer는 속성의 의미를 변경하지 않는 한 속성을 완전히 생략해서는 안됩니다 (SHOULD).
이 규칙은 속성이 부울 `false` 인 경우에만 적용되며, 정수 0과 같은 PHP의 다른 "falsey"값에는 적용되지 않습니다.

### 1.3 Relationships

링크 관계는 문자열로 정의되며 공개적으로 정의 된 관계의 경우 간단한 키워드이고 비공개인 관계의 경우 절대 URI를 사용합니다.

간단한 키워드가 사용되는 경우 [IANA 레지스트리](http://www.iana.org/assignments/link-relations/link-relations.xhtml)의 키워드와 일치해야합니다(SHOULD).

선택적으로 [microformats.org](http://microformats.org/wiki/existing-rel-values) 레지스트리를 사용할 수도 있지만(MAY) 모든 컨텍스트에서 유효하지 않을 수 있습니다.

위의 레지스트리 중 하나 또는 이와 유사한 공개 레지스트리에 정의되지 않은 관계는 "비공개"으로 간주됩니다. 즉 특정 응용 프로그램 또는 유스 케이스에 한정됩니다.
그러한 관계는 반드시 절대 URI를 사용해야 합니다(MUST).

## 1.4 Link Templates

[RFC 6570](https://tools.ietf.org/html/rfc6570)은 URI 템플릿의 형식, 즉 클라이언트에서 제공 한 값으로 채워질 것으로 예상되는 URI의 패턴을 정의합니다.
일부 하이퍼 미디어 형식은 템플릿 링크를 지원하지만 다른 링크는 템플릿이 아니라는 것을 나타내는 특별한 방법이 있을 수 있습니다.
URI 템플릿을 지원하지 않는 형식의 Serializer는 템플릿 기반 링크가 발견되면 이를 무시해야 합니다 (MUST).

## 1.5 Evolvable providers

어떤 경우에는 링크 공급자가 추가 링크를 추가해야 할 수도 있습니다.
어떤 경우에는 링크 공급자가 반드시 읽기 전용이며 링크는 런타임시 다른 데이터 소스에서 파생됩니다.
이러한 이유로 수정 가능한 공급자는 선택적으로 구현 될 수있는 보조 인터페이스입니다.

또한 PSR-7 응답 객체 같은 일부 링크 공급자 객체는 의도적으로 변경할 수 없습니다.
이것은 메소드를 해당 링크에 추가하는 방법은 맞지 않는 다는 것을 의미합니다
따라서 `EvolvableLinkProviderInterface` 의 단일 메소드는 원본과 동일하지만 추가 링크 객체가 포함 된 새로운 객체가 반환되어야 합니다.

## 1.6 Evolvable link objects

링크 오브젝트는 대부분의 경우 값 오브젝트입니다.
따라서 PSR-7 값 객체와 동일한 방식으로 처리하도록 하는 것이 좋은 방식입니다. `역자주: 원문은 evolve이지만 의역하였습니다`
이러한 이유 때문에, 단일 변경으로 새 객체 인스턴스를 생성하는 메소드인 `EvolvableLinkInterface`가 추가적으로 제공됩니다.
같은 방식이 PSR-7에서 사용되었고, PHP의 copy-on-write 동작 덕분에 여전히 CPU와 메모리가 효율적입니다.

그러나 링크의 templated 된 값은 href 값에 기반하므로 templated에 대한 확장 가능한 방법은 없습니다. `역자주: evolvable지만 좀 더 좋은 단어를 찾지 못하였습니다`
이것은 독립적으로 설정하면 안되지만(MUST NOT), href 값이 RFC 6570 링크 템플릿인지에 따라서 결정됩니다.

## 2. Package

설명 한 인터페이스와 클래스는 [psr/link](https://packagist.org/packages/psr/link) 패키지의 일부로 제공됩니다.

## 3. Interfaces

### 3.1 `Psr\Link\LinkInterface`

~~~php
<?php

namespace Psr\Link;

/**
 * A readable link object.
 */
interface LinkInterface
{
    /**
     * Returns the target of the link.
     *
     * The target link must be one of:
     * - An absolute URI, as defined by RFC 5988.
     * - A relative URI, as defined by RFC 5988. The base of the relative link
     *     is assumed to be known based on context by the client.
     * - A URI template as defined by RFC 6570.
     *
     * If a URI template is returned, isTemplated() MUST return True.
     *
     * @return string
     */
    public function getHref();

    /**
     * Returns whether or not this is a templated link.
     *
     * @return bool
     *   True if this link object is templated, False otherwise.
     */
    public function isTemplated();

    /**
     * Returns the relationship type(s) of the link.
     *
     * This method returns 0 or more relationship types for a link, expressed
     * as an array of strings.
     *
     * @return string[]
     */
    public function getRels();

    /**
     * Returns a list of attributes that describe the target URI.
     *
     * @return array
     *   A key-value list of attributes, where the key is a string and the value
     *  is either a PHP primitive or an array of PHP strings. If no values are
     *  found an empty array MUST be returned.
     */
    public function getAttributes();
}
~~~

### 3.2 `Psr\Link\EvolvableLinkInterface`

~~~php
<?php

namespace Psr\Link;

/**
 * An evolvable link value object.
 */
interface EvolvableLinkInterface extends LinkInterface
{
    /**
     * Returns an instance with the specified href.
     *
     * @param string $href
     *   The href value to include.  It must be one of:
     *     - An absolute URI, as defined by RFC 5988.
     *     - A relative URI, as defined by RFC 5988. The base of the relative link
     *       is assumed to be known based on context by the client.
     *     - A URI template as defined by RFC 6570.
     *     - An object implementing __toString() that produces one of the above
     *       values.
     *
     * An implementing library SHOULD evaluate a passed object to a string
     * immediately rather than waiting for it to be returned later.
     *
     * @return static
     */
    public function withHref($href);

    /**
     * Returns an instance with the specified relationship included.
     *
     * If the specified rel is already present, this method MUST return
     * normally without errors, but without adding the rel a second time.
     *
     * @param string $rel
     *   The relationship value to add.
     * @return static
     */
    public function withRel($rel);

    /**
     * Returns an instance with the specified relationship excluded.
     *
     * If the specified rel is already not present, this method MUST return
     * normally without errors.
     *
     * @param string $rel
     *   The relationship value to exclude.
     * @return static
     */
    public function withoutRel($rel);

    /**
     * Returns an instance with the specified attribute added.
     *
     * If the specified attribute is already present, it will be overwritten
     * with the new value.
     *
     * @param string $attribute
     *   The attribute to include.
     * @param string $value
     *   The value of the attribute to set.
     * @return static
     */
    public function withAttribute($attribute, $value);

    /**
     * Returns an instance with the specified attribute excluded.
     *
     * If the specified attribute is not present, this method MUST return
     * normally without errors.
     *
     * @param string $attribute
     *   The attribute to remove.
     * @return static
     */
    public function withoutAttribute($attribute);
}
~~~

#### 3.2 `Psr\Link\LinkProviderInterface`

~~~php
<?php

namespace Psr\Link;

/**
 * A link provider object.
 */
interface LinkProviderInterface
{
    /**
     * Returns an iterable of LinkInterface objects.
     *
     * The iterable may be an array or any PHP \Traversable object. If no links
     * are available, an empty array or \Traversable MUST be returned.
     *
     * @return LinkInterface[]|\Traversable
     */
    public function getLinks();

    /**
     * Returns an iterable of LinkInterface objects that have a specific relationship.
     *
     * The iterable may be an array or any PHP \Traversable object. If no links
     * with that relationship are available, an empty array or \Traversable MUST be returned.
     *
     * @return LinkInterface[]|\Traversable
     */
    public function getLinksByRel($rel);
}
~~~

#### 3.3 `Psr\Link\EvolvableLinkProviderInterface`

~~~php
<?php

namespace Psr\Link;

/**
 * An evolvable link provider value object.
 */
interface EvolvableLinkProviderInterface extends LinkProviderInterface
{
    /**
     * Returns an instance with the specified link included.
     *
     * If the specified link is already present, this method MUST return normally
     * without errors. The link is present if $link is === identical to a link
     * object already in the collection.
     *
     * @param LinkInterface $link
     *   A link object that should be included in this collection.
     * @return static
     */
    public function withLink(LinkInterface $link);

    /**
     * Returns an instance with the specifed link removed.
     *
     * If the specified link is not present, this method MUST return normally
     * without errors. The link is present if $link is === identical to a link
     * object already in the collection.
     *
     * @param LinkInterface $link
     *   The link to remove.
     * @return static
     */
    public function withoutLink(LinkInterface $link);
}
~~~
