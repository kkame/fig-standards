---
title: PSR-11 Container interface
layout: "page"
order: 11
---

# Container interface

이 문서는 의존성 주입 컨테이너에 대한 공통 인터페이스를 설명합니다.

`ContainerInterface`에 의해 설정된 목표는 프레임 워크와 라이브러리가 컨테이너를 사용하여 객체와 매개 변수 (이 문서의 나머지 부분에서 *항목*이라고 함)를 얻는 방법을 표준화하는 것입니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

이 문서에서 `implementor` 라는 단어는 의존성 삽입 관련 라이브러리나 프레임워크에서 `ContainerInterface`를 구현하는 누군가로 해석되어야합니다.
의존성 주입 컨테이너 (DIC)의 사용자는 `사용자`라고합니다.

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 1. 명세서

### 1.1 기본

#### 1.1.1 엔트리 식별자

엔트리 식별자는 컨테이너 내의 항목을 고유하게 식별하는 적어도 하나의 문자로 구성된 PHP에서 유효한 문자열입니다.
엔트리 식별자는 불투명 한 문자열이므로 호출자는 문자열의 구조가 의미를 전달한다고 가정해서는 안됩니다(SHOULD NOT).

#### 1.1.2 컨테이너에서 읽어오기

- `Psr\Container\ContainerInterface`는 `get`과 `has` 두가지 메서드를 제공합니다.

- `get` 은 하나의 필수 매개 변수를 가집니다. 엔트리 식별자는 반드시 문자열이어야 합니다 (MUST).
  `get` 은 어떤 값이든 (하나의 *mixed* 값) 반환 할 수 있고 식별자가 컨테이너에 알려지지 않은 경우에는 `NotFoundExceptionInterface` 예외를 던집니다.
  동일한 식별자를 가진 `get` 에 대한 두 번의 연속적인 호출은 동일한 값을 반환해야합니다 (SHOULD).
  그러나 `implementor` 디자인 또는 `user` 설정에 따라 다른 값들이 반환 될 수 있습니다. 그래서 `user`는 2번의 연속적인 호출에서 같은 값을 얻는 것에 의존하지 않아야합니다 (SHOULD NOT).

- `has` 는 하나의 고유 한 매개 변수를 가집니다. 엔트리 식별자는 반드시 문자열이어야 합니다(MUST).
  `has` 는 엔트리 식별자가 컨테이너에 알려지면 `true` 를 리턴하고 그렇지 않으면 `false` 를 리턴해야합니다 (MUST).
  `has($id)` 가 false 를 반환하면 `get($id)` 는 `NotFoundExceptionInterface` 예외를 던져야합니다.

### 1.2 Exceptions

컨테이너에 의해 직접 던져진 예외는 
[`Psr\Container\ContainerExceptionInterface`](#container-exception)을 상속해야한다(SHOULD).

존재하지 않는 id를 가진 `get` 메서드에 대한 호출은
[`Psr\Container\NotFoundExceptionInterface`](#not-found-exception) 예외를 던져야 한다(MUST).

### 1.3 권장 사용법

사용자는 객체가 *자신의 의존성*을 검색 할 수 있도록 컨테이너에 객체를 넘겨서는 안됩니다(SHOULD NOT).
즉, 컨테이너는 [Service Locator](https://en.wikipedia.org/wiki/Service_locator_pattern)로 사용됩니다.
이런 행동은 일반적으로 방해가 됩니다

자세한 내용은 META 문서의 섹션 4를 참조하십시오.

## 2. Package

설명한 인터페이스와 클래스 및 예외 사항은 [psr/container](https://packagist.org/packages/psr/container) 패키지의 일부로 제공됩니다.

PSR 컨테이너 구현체를 제공하는 패키지는 `psr/container-implementation` 을 제공한다고 선언해야합니다.

구현이 필요한 프로젝트에는 `psr/container-implementation`의 `1.0.0` 을 요구합니다.

## 3. Interfaces

<a name="container-interface"></a>
### 3.1. `Psr\Container\ContainerInterface`

~~~php
<?php
namespace Psr\Container;

/**
 * Describes the interface of a container that exposes methods to read its entries.
 */
interface ContainerInterface
{
    /**
     * Finds an entry of the container by its identifier and returns it.
     *
     * @param string $id Identifier of the entry to look for.
     *
     * @throws NotFoundExceptionInterface  No entry was found for **this** identifier.
     * @throws ContainerExceptionInterface Error while retrieving the entry.
     *
     * @return mixed Entry.
     */
    public function get($id);

    /**
     * Returns true if the container can return an entry for the given identifier.
     * Returns false otherwise.
     *
     * `has($id)` returning true does not mean that `get($id)` will not throw an exception.
     * It does however mean that `get($id)` will not throw a `NotFoundExceptionInterface`.
     *
     * @param string $id Identifier of the entry to look for.
     *
     * @return bool
     */
    public function has($id);
}
~~~

<a name="container-exception"></a>
### 3.2. `Psr\Container\ContainerExceptionInterface`

~~~php
<?php
namespace Psr\Container;

/**
 * Base interface representing a generic exception in a container.
 */
interface ContainerExceptionInterface
{
}
~~~

<a name="not-found-exception"></a>
### 3.3. `Psr\Container\NotFoundExceptionInterface`

~~~php
<?php
namespace Psr\Container;

/**
 * No entry was found in the container.
 */
interface NotFoundExceptionInterface extends ContainerExceptionInterface
{
}
~~~
