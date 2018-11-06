# PSR-4 Autoloader

이 단어들 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 은 [RFC 2119](http://tools.ietf.org/html/rfc2119)에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

## 1. 개요

이 PSR은 Class [autoloading] 에서 파일 경로에 대한 사양(specification)을 설명합니다.
이 PSR은 완벽하게 상호 운용이 가능하며 [PSR-0]을 포함한 다른 오토로딩 사양과 함께 사용할 수 있습니다.
또한 이 PSR은 사양에 따라 오토로드되는 파일을 저장할 위치를 설명합니다.

## 2. 명세서

1. "Class"라는 용어는 Class, Interface, Trait 등의 기타 유사한 구조를 말합니다.

2. 정규화 된 클래스 이름의 형식은 다음과 같습니다.

        \<NamespaceName>(\<SubNamespaceNames>)*\<ClassName>

    1. 정규화 된 Class 이름은 "공급자 네임스페이스"라고도하는 최상위 네임스페이스를 가져야만 합니다(MUST). 

    2. 정규화 된 Class 이름은 하나 이상의 하위 네임스페이스를 가질 수 있습니다 (MAY).

    3. 정규화된 Class 이름은 마지막 클래스 이름(terminating class name)을 가져야합니다 (MUST). `역자주: 좀 더 좋은 의미의 표현을 찾지 못하였습니다`

    4. 밑줄은 정규화 된 클래스 이름의 어느 부분에도 특별한 의미가 없습니다.

    5. 정규화 된 클래스 이름의 알파벳 문자는 대소문자의 조합 일 수 있습니다 (MAY).

    6. 모든 클래스 이름은 대소문자를 구분하여 참조해야합니다 (MUST).

3. 정규화 된 클래스 이름에 해당하는 파일을 로드 할 때 ...

    1. 정규화 된 클래스 이름에서 최초의 네임스페이스 구분 기호를 제외하고 연속된 하나 이상의 상위 네임스페이스와 하위 네임스페이스로 구성된 ( "네임스페이스 접두사")는 적어도 하나의 "기본 디렉토리"에 대응합니다.
    `역자주: 표현을 더 다듬어야 할 듯 하지만 네임스페이스의 조합은 하나의 디렉토리와 매치가 된다는 의미로 이해하시면 될 것 같습니다`

    2. "네임스페이스 접두사" 다음의 이어지는 하위 네임스페이스 이름은 "기본 디렉토리" 내의 하위 디렉토리에 해당하며 네임스페이스 구분 기호는 디렉토리 구분 기호를 나타냅니다.
       하위 디렉토리 이름은 하위 네임스페이스 이름의 대소문자와 일치해야합니다.

    3. 마지막에 존재하는 Class 이름은 `.php` 로 끝나는 파일 이름과 같아야 합니다. `역자주: \Vendor\Packages\Class => Class.php`
       파일 이름은 Class 이름의 대소문자와 일치해야합니다(MUST).

4. 오토로더 구현체는 예외(exception)를 throw해서는 안되며(MUST NOT), 모든 레벨의 오류를 발생해서는 안되며(MUST NOT), 값을 반환해서는 안됩니다 (SHOULD NOT).

## 3. 예제

아래의 표는 주어진 정규화 된 Class 이름, 네임스페이스 접두사 및 기본 디렉토리에 해당하는 파일 경로를 보여줍니다.

| 정규화된 Class 이름             | Namespace 접두사    | 기본 디렉토리              | 결과 파일 경로
| ----------------------------- |--------------------|--------------------------|-------------------------------------------
| \Acme\Log\Writer\File_Writer  | Acme\Log\Writer    | ./acme-log-writer/lib/   | ./acme-log-writer/lib/File_Writer.php
| \Aura\Web\Response\Status     | Aura\Web           | /path/to/aura-web/src/   | /path/to/aura-web/src/Response/Status.php
| \Symfony\Core\Request         | Symfony\Core       | ./vendor/Symfony/Core/   | ./vendor/Symfony/Core/Request.php
| \Zend\Acl                     | Zend               | /usr/includes/Zend/      | /usr/includes/Zend/Acl.php

명세를 따르는 Autoloader의 구현 예제는 [examples file]을 참조하십시오.
예제 구현체는 명세(specification)의 일부로 간주되어서는 안되며(MUST NOT) 언제든지 변경 될 수 있습니다. 

[autoloading]: http://php.net/autoload
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[examples file]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-examples.md
