# Extended Coding Style Guide

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 개요

이 규칙은 코딩 스타일 가이드인 [PSR-2][]를 확장 및 대체하며, 기본 코딩 표준 인 [PSR-1][]을 준수해야합니다.

[PSR-2][]와 마찬가지로 이 사양의 목적은 다른 작성자의 코드를 읽을 때 이해하기 어려운 것을 줄이는 것입니다. 
이것은 PHP 코드의 형식을 지정하는 방법에 대한 규칙과 기대 사항을 공유하여 열거합니다. 
이 PSR은 코딩 스타일 도구가 구현할 수있는 설정 방법을 제공하기 위해 노력하고, 프로젝트에서 이 PSR의 준수를 선언함을 통해 개발자는 서로 다른 프로젝트와 쉽게 연동 할 수 있습니다.
다양한 저자가 여러 프로젝트에서 협업을 할 때, 모든 프로젝트에서 한가지의 가이드 라인을 사용하는 것이 도움이 됩니다.
따라서 이 가이드로 얻는 혜택은 규칙 자체가 아니라 이러한 규칙을 공유하는 것을 통해 얻을 수 있습니다.

[PSR-2][]는 2012 년에 승인되었으며 그 이후로 PHP에 많은 발전이 있었으므로 코딩 스타일 지침에 영향을 미쳤습니다.
[PSR-2]는 문서 작성 당시 존재했던 PHP 기능은 대부분 포함하고 있지만, 새로운 기능들은 매우 다양하게 해석 될 수 있습니다.
따라서 이 PSR은 새로운 기능을 사용하여 보다 현대적인 관점에서 PSR-2의 내용을 명확하게하고 PSR-2에 관한 오류의 개정본을 만듭니다.


### 이전 버전

이 문서 전체의 내용 중, 프로젝트에서 사용하는 PHP 버전에 없는 지침은 무시할 수 있습니다 (MAY).

### 예제

이 예제는 아래의 규칙 중 일부를 간략하게 설명합니다.

~~~php
<?php

declare(strict_types=1);

namespace Vendor\Package;

use Vendor\Package\{ClassA as A, ClassB, ClassC as C};
use Vendor\Package\SomeNamespace\ClassD as D;

use function Vendor\Package\{functionA, functionB, functionC};

use const Vendor\Package\{ConstantA, ConstantB, ConstantC};

class Foo extends Bar implements FooInterface
{
    public function sampleFunction(int $a, int $b = null): array
    {
        if ($a === $b) {
            bar();
        } elseif ($a > $b) {
            $foo->bar($arg1);
        } else {
            BazClass::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // method body
    }
}
~~~

## 2. 일반

### 2.1 기본 코딩 표준

코드는 [PSR-1]에 요약 된 모든 규칙을 따라야합니다.

PSR-1의 '*StudlyCaps*'라는 용어는 *PascalCase*로 해석해야하며(MUST) 첫 단어가 첫 문자를 포함하여 대문자로 표시되어야합니다.

### 2.2 Files

모든 PHP 파일은 Unix LF (linefeed) 줄만 사용해야합니다 (MUST).

모든 PHP 파일은 단일 LF로 끝나는 비어 있지 않은 라인으로 끝나야합니다 (MUST).

PHP code만 존재하는 파일에서는 닫는 `?>`태그를 생략해야합니다 (MUST).

### 2.3 Lines

라인 길이에 엄격한 제한이 있어서는 안됩니다(MUST NOT).

한줄에 들어가는 글자수에 대한 가벼운 제한은 120자 여야합니다. (MUST).

한줄은 80자를 넘지 않아야합니다 (SHOULD NOT). 그보다 긴 줄은 각각 80자 이하의 여러 줄으로 나눠야합니다 (SHOULD).

HEREDOC/NOWDOC 구문을 제외하고 줄의 끝에 공백이 있으면 안됩니다(MUST NOT).

명시적으로 금지 된 경우를 제외하고 가독성을 높이고 관련 코드 블록을 표시하기 위해 빈 줄을 추가 할 수 있습니다 (MAY).

한 줄에 하나 이상의 문장이 있어서는 안됩니다 (MUST NOT).

### 2.4 들여쓰기

코드는 4개의 스페이스로 들여쓰기를 사용해야만하며(MUST), 탭을 사용하지 않아야만 합니다 (MUST NOT).

### 2.5 Keyword와 Type

모든 PHP 예약 키워드와 타입 [[1]][keywords][[2]][types]은 소문자 여야만합니다 (MUST).

향후 PHP 버전에 추가되는 모든 새로운 유형과 키워드는 반드시 소문자 여야만합니다 (MUST).

반드시 `boolean` 대신 `bool`, `integer` 대신 `int` 등 짧은 형태의 타입 키워드를 사용해야합니다 (MUST).

## 3. Declare 선언문, 네임 스페이스 및 Import 선언문

PHP 파일의 머릿글은 여러 블록으로 구성 될 수 있습니다. 
이미 존재한다면, 아래의 각 블록은 하나의 빈 행으로 분리되어야하며 빈 행을 포함해서는 안됩니다 (MUST NOT). 
관련이 없는 블록은 생략 할 수 있지만(MUST) 각 블록은 아래에 나열된 순서 여야합니다.

1. `<?php`로 태그 열기.
2. 파일 수준의 docblock.
3. 하나 이상의 declare 선언문.
4. 파일의 네임 스페이스 선언.
5. 하나 이상의 클래스 기반 `use` import 문.
6. 하나 이상의 함수 기반 `use` import 문.
7. 하나 이상의 상수 기반 `use` import 문.
8. 파일의 나머지 코드.

파일에 HTML과 PHP가 섞여 있다면 위 섹션 중 하나라도 여전히 사용할 수 있습니다. 
그럴땐 파일의 최상단에는 declare 문이 있어야 하며 (MUST), 나머지 부분은 닫는 PHP 태그와 HTML과 PHP가 섞인 코드가 있어야합니다.

열기 태그 `<?php`가 파일의 첫 번째 줄에 있을 때, PHP 열기 및 닫기 태그 외부에 마크업을 포함하는 파일이 아니라면 다른 문장이 없는 자체 라인 상에 있어야 합니다(MUST).
`역자주: 설명이 어려우니 그냥 아래에 나오는 예제를 참고하세요`

Import 문은 항상 정규화된 형식이어야하므로 백 슬래시로 시작해서는 안됩니다 (MUST).

다음은 모든 블록의 전체 목록의 예제를 보여줍니다.

~~~php
<?php

/**
 * This file contains an example of coding styles.
 */

declare(strict_types=1);

namespace Vendor\Package;

use Vendor\Package\{ClassA as A, ClassB, ClassC as C};
use Vendor\Package\SomeNamespace\ClassD as D;
use Vendor\Package\AnotherNamespace\ClassE as E;

use function Vendor\Package\{functionA, functionB, functionC};
use function Another\Vendor\functionD;

use const Vendor\Package\{CONSTANT_A, CONSTANT_B, CONSTANT_C};
use const Another\Vendor\CONSTANT_D;

/**
 * FooBar is an example class.
 */
class FooBar
{
    // ... additional PHP code ...
}

~~~

두 단계 이상의 복합 네임 스페이스는 사용되어서는 안됩니다 (MUST NOT). 그러므로 허용하는 최대 복합 단계는 다음과 같습니다.

~~~php
<?php

use Vendor\Package\SomeNamespace\{
    SubnamespaceOne\ClassA,
    SubnamespaceOne\ClassB,
    SubnamespaceTwo\ClassY,
    ClassZ,
};
~~~

그리고 다음은 허용되지 않습니다(MUST NOT).

~~~php
<?php

use Vendor\Package\SomeNamespace\{
    SubnamespaceOne\AnotherNamespace\ClassA,
    SubnamespaceOne\ClassB,
    ClassZ,
};
~~~


PHP의 열고 닫는 태그 외부에서 마크 업을 포함하는 파일에 엄격한 유형을 선언하고자 할 때, 선언문은 파일의 첫 번째 줄에 있어야하며 (MUST), 여는 PHP 태그와 strict types 선언과 closing 태그를 포함해야합니다.

예 :

~~~php
<?php declare(strict_types=1) ?>
<html>
<body>
    <?php
        // ... additional PHP code ...
    ?>
</body>
</html>
~~~

Declare 문은 공백을 포함하지 않아야하며(MUST) `declare(strict_types=1)` (선택 항목 인 세미콜론 종결자 포함)이어야합니다(MUST).

블록 선언문은 허용되며 반드시 아래와 같은 형식이여야합니다(MUST). 중괄호와 간격의 위치 참고 :

~~~php
declare(ticks=1) {
    // some code
}
~~~

## 4. 클래스, 프로퍼티, 메소드

"클래스"라는 용어는 모든 클래스, 인터페이스 및 특성-trait을 나타냅니다.

닫는 중괄호는 같은 줄의 주석이나 명령문 다음에 와서는 안됩니다(MUST NOT).

새 클래스를 인스턴스화 할 때 생성자에 전달 된 인수가없는 경우에도 항상 괄호가 있어야합니다(MUST).
`역자주 : new Foo; 같은걸 하지 말라는 의미`

~~~php
new Foo();
~~~

### 4.1 확장과 구현

`extends`와 `implements` 키워드는 클래스 이름과 같은 줄에 선언되어야합니다 (MUST).

해당 클래스의 여는 중괄호는 같은 줄에 있어야합니다(MUST). 클래스의 닫는 중괄호는 본문 뒤의 다음 줄로 가야합니다(MUST).

여는 중괄호는 반드시(MUST) 같은 줄에 있어야하며 앞에 빈 줄을 붙여서는 안됩니다.

닫는 중괄호는 반드시(MUST) 같은 줄에 있어야하며 빈 줄을 앞에 두어서는 안됩니다(MUST NOT).


~~~php
<?php

namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // constants, properties, methods
}
~~~

`implements`의 목록과 인터페이스의 경우, `extends`는 여러 줄에 걸쳐 나뉘어 질 수 있으며(MAY), 각 줄은 한번의 들여쓰기를 합니다.
 그렇게 할 때 목록의 첫 번째 항목은 다음 줄에 있어야하며 한 줄에 하나의 인터페이스 만 있어야합니다.

~~~php
<?php

namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // constants, properties, methods
}
~~~

### 4.2 특성-trait 사용

특성-trait을 구현하기 위해 클래스 내에서 사용되는 `use` 키워드는 클래스를 여는 중괄호 다음 줄에 선언해야합니다 (MUST).

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;

class ClassName
{
    use FirstTrait;
}
~~~

클래스로 가져온 각각의 특성-trait은 반드시 한 줄에 하나씩 포함해야하며(MUST), 각 포함내역은 반드시 고유한 `use` import 문을 가져야합니다 (MUST).

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;
use Vendor\Package\SecondTrait;
use Vendor\Package\ThirdTrait;

class ClassName
{
    use FirstTrait;
    use SecondTrait;
    use ThirdTrait;
}
~~~

클래스에 `use` import 문 다음에 아무것도 없는 경우, 클래스를 닫는 중괄호는 마지막 `use` import 문 다음에 있어야합니다 (MUST).

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;

class ClassName
{
    use FirstTrait;
}
~~~

그렇지 않으면 마지막 `use` import 문 다음에 빈 줄이 있어야합니다(MUST).

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;

class ClassName
{
    use FirstTrait;

    private $property;
}
~~~

`insteadof` 와 `as`연산자를 사용할 때는 들여 쓰기, 간격 및 새 줄을 반드시(MUST) 다음과 같이 사용해야합니다.

~~~php
<?php

class Talker
{
    use A, B, C {
        B::smallTalk insteadof A;
        A::bigTalk insteadof C;
        C::mediumTalk as FooBar;
    }
}
~~~

### 4.3 속성-property 과 상수-constant

모든 속성-property에서 가시성을 반드시(MUST) 선언해야합니다.

프로젝트의 PHP 최소 버전이 상수의 가시성 (PHP 7.1.0 이상)을 지원하는 경우 모든 상수에 대한 가시성을 반드시(MUST) 선언해야합니다.

`var` 키워드는 속성-property를 선언하는데 사용되어서는 안됩니다 (MUST NOT).

선언문마다 하나 이상의 속성-property이 선언되어서는 안됩니다(MUST NOT).

protected나 private 가시성을 나타 내기 위해 속성 이름 앞에 하나의 밑줄을 사용해서는 안됩니다 (MUST NOT). 즉, 밑줄 접두사는 명시적인 의미가 없습니다.

형식 선언과 속성-property 이름 사이에 공백이 있어야합니다(MUST).

속성-property 선언은 다음과 같아야 합니다.

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public $foo = null;
    public static int $bar = 0;
}
~~~

### 4.4 메소드와 함수

모든 메소드에서 가시성을 선언해야합니다 (MUST).

메소드 이름은 protected나 private 가시성을 나타 내기 위해 하나의 밑줄로 접두어를 붙여서는 안됩니다 (MUST NOT). 즉, 밑줄 접두사는 명시적인 의미가 없습니다.

메서드와 함수 이름은 메서드 이름 다음에 공백으로 선언되어서는 안됩니다 (MUST NOT). 여는 중괄호는 반드시(MUST) 같은 줄에 있어야하며 닫는 중괄호는 반드시(MUST) 그 다음 줄에 있어야합니다. 여는 괄호 뒤에 공백이 있으면 안되며(MUST NOT) 닫는 괄호 앞에 공백이 있어서는 안됩니다(MUST NOT).

메소드 선언은 다음과 같습니다. 괄호, 쉼표, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [], &...$arg4)
    {
        // method body
    }
}
~~~

함수 선언은 다음과 같습니다. 괄호, 쉼표, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

function fooBarBaz($arg1, &$arg2, $arg3 = [], &...$arg4)
{
    // function body
}
~~~

### 4.5 메서드 및 함수 인수

인수 목록에는 각 쉼표 앞에 공백이 있으면 안되며(MUST NOT) 각 쉼표 뒤에 하나의 공백이 있어야합니다(MUST).

기본값을 가진 메소드 및 함수 인수는 가변 길이 인수 목록의 앞이면서 인수 목록의 끝에 있어야합니다(MUST).

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public function foo(int $arg1, &$arg2, $arg3 = [], &...$arg4)
    {
        // method body
    }
}
~~~

인수 목록은 여러 줄에 걸쳐 나뉘어 질 수 있으며(MAY), 각 줄은 한 번 들여 쓰여질 수 있습니다.
그렇게 할 때 목록의 첫 번째 항목은 다음 줄에 있어야하며(MUST) 한 줄에 하나의 인수 만 있어야합니다(MUST).

인수 목록이 여러 줄에 걸쳐 분할되어 있으면 닫는 괄호와 여는 중괄호는 공백을 한개 사용하여 같은 줄에 함께 있어야합니다 (MUST).

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public function aVeryLongMethodName(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    ) {
        // method body
    }
}
~~~

When you have a return type declaration present, there MUST be one space after the colon followed by the type declaration. 
The colon and declaration MUST be on the same line as the argument list closing parenthesis with no spaces between the two characters.

반환형식(return type) 선언이 있으면 콜론 뒤에 공백이 하나 있어야하며(MUST) 그 다음에 형식 선언이옵니다. 
콜론과 반환형식 선언은 두 문자 사이에 공백이 없는 인수 목록을 닫는 괄호와 같은 줄에 있어야합니다(MUST).

~~~php
<?php

declare(strict_types=1);

namespace Vendor\Package;

class ReturnTypeVariations
{
    public function functionName(int $arg1, $arg2): string
    {
        return 'foo';
    }

    public function anotherFunction(
        string $foo,
        string $bar,
        int $baz
    ): string {
        return 'foo';
    }
}
~~~

nullable type 선언에서는 물음표와 type 사이에 공백이 없어야합니다(MUST NOT).

~~~php
<?php

declare(strict_types=1);

namespace Vendor\Package;

class ReturnTypeVariations
{
    public function functionName(?string $arg1, ?int &$arg2): ?string
    {
        return 'foo';
    }
}
~~~

인수 앞에 참조 연산자 `&`를 사용하면 앞의 예시와 같이 뒤에 공백이 있어서는 안됩니다(MUST NOT).

가변 3 도트 연산자와 인자 이름 사이에는 공백이 없어야합니다(MUST NOT).

```php
public function process(string $algorithm, ...$parts)
{
    // processing
}
```

참조 연산자와 가변 3 도트 연산자를 결합 할 때 두 연산자 사이에 공백이 없어야합니다(MUST NOT).

```php
public function process(string $algorithm, &...$parts)
{
    // processing
}
```

### 4.6 `abstract`, `final`, 과 `static`

`abstract`와 `final` 선언이 있을 경우, 가시성 선언 앞에 와야합니다 (MUST).

`static` 선언이 있을 경우, 가시성 선언 뒤에 와야합니다(MUST).

~~~php
<?php

namespace Vendor\Package;

abstract class ClassName
{
    protected static $foo;

    abstract protected function zim();

    final public static function bar()
    {
        // method body
    }
}
~~~

### 4.7 메서드와 함수 호출

메서드나 함수를 호출 할 때 메서드나 함수 이름과 여는 괄호 사이에 공백이 없어야합니다(MUST NOT). 여는 괄호 뒤에 공백이 있으면 안되며(MUST NOT) 닫는 괄호 앞에 공백이 있어서는 안됩니다(MUST NOT). 인수 목록에는 각 쉼표 앞에 공백이 있으면 안되(MUST NOT)며 각 쉼표 뒤에 하나의 공백이 있어야합니다(MUST).

~~~php
<?php

bar();
bar(...['foo','bar']);
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
~~~

인수 목록은 여러 줄에 걸쳐 나뉘어 질 수 있으며(MAY), 각 줄은 한 번 들여쓰기를 합니다.
그렇게 할 때 목록의 첫 번째 항목은 다음 줄에 있어야하며(MUST) 한 줄에 하나의 인수 만 있어야합니다(MUST). 
(익명함수나 배열의 경우처럼) 여러 줄로 분할되는 단일 인수는 인수 목록 자체를 분할하는 이것에는 해당하지 않습니다.

~~~php
<?php

$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
~~~

~~~php
<?php

somefunction($foo, $bar, [
  // ...
], $baz);

$app->get('/hello/{name}', function ($name) use ($app) {
    return 'Hello ' . $app->escape($name);
});
~~~

## 5. 제어구조

제어 구조의 일반적인 스타일 규칙은 다음과 같습니다.

- 제어 구조 키워드 다음에 하나의 공백이 있어야합니다(MUST).
- 여는 괄호 뒤에 공백이 있으면 안됩니다(MUST NOT).
- 닫는 괄호 앞에 공백이 없어야 합니다(MUST NOT).
- 닫는 괄호와 여는 중괄호 사이에 하나의 공백이 있어야 합니다(MUST).
- 본문은 한 번 들여 쓰기되어야합니다(MUST).
- 닫는 중괄호는 몸체 뒤의 다음 줄에 있어야합니다(MUST).

각 구조의 본문은 중괄호로 묶어야합니다(MUST). 이것은 구조가 어떻게 보이는지 표준화하고 새로운 라인을 본문에 추가 할 때 오류가 발생할 가능성을 줄입니다.

### 5.1 `if`, `elseif`, `else`

`if` 구조는 다음과 같습니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오. `else`와 `elseif`는 이전 본문의 닫는 중괄호와 같은 줄에 있습니다.

~~~php
<?php

if ($expr1) {
    // if body
} elseif ($expr2) {
    // elseif body
} else {
    // else body;
}
~~~

`else if` 키워드 대신 `elseif` 키워드를 사용하여 모든 제어 키워드가 하나의 단어처럼 보이도록해야합니다 (SHOULD).
`역자주: phpstorm과 같은 ide에서 else if로 사용하고 코드 자동정렬을 할 경우 else { if(){} } 같은 형태로 분할 해버리기도 합니다. 이때는 설정을 수정할 수도 있지만 저는 elseif 로 사용하시는 것을 권장합니다`

괄호 안의 표현은 여러 줄에 걸쳐 나뉘어 질 수 있으며(MAY), 그 다음 줄은 적어도 한 번 들여 쓰여질 수 있습니다.
그렇게 할 때 첫 번째 조건은 반드시(MUST) 다음 줄에 있어야합니다.
닫는 괄호와 여는 중괄호는 한개 공백과 함께 한 줄에 함께 있어야합니다 (MUST).
조건 사이의 부울-boolean 연산자는 항상 줄의 시작 또는 끝에 있어야하며 둘 다를 혼합해서는 안됩니다(MUST).

~~~php
<?php

if (
    $expr1
    && $expr2
) {
    // if body
} elseif (
    $expr3
    && $expr4
) {
    // elseif body
}
~~~

### 5.2 `switch`, `case`

`switch` 구조체는 다음과 같습니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.
`case` 문은 `switch`에서 한 번 들여 쓰기되어야하며(MUST) `break` 키워드 (또는 다른 종료 키워드)는 `case` 본문과 같은 수준에서 들여 쓰기되어야합니다 (MUST). 
비어 있지 않은 `case` 본문에서 다음 case를 의도적으로 실행시킬 경우(fall-through) `// no break`와 같은 주석이 있어야합니다(MUST).

~~~php
<?php

switch ($expr) {
    case 0:
        echo 'First case, with a break';
        break;
    case 1:
        echo 'Second case, which falls through';
        // no break `역자주: 다음case를 실행시키기 위해 의도적으로 break를 넣지 않은 경우 반드시 추가해야하는 주석`
    case 2:
    case 3:
    case 4:
        echo 'Third case, return instead of break';
        return;
    default:
        echo 'Default case';
        break;
}
~~~

괄호 안의 표현은 여러 행에 걸쳐 나뉘어 질 수 있으며(MAY), 그 다음 행은 적어도 한 번 들여쓰기를 합니다.
그럴 경우 첫 번째 조건은 반드시 다음 줄에 있어야 합니다(MUST). 
닫는 괄호와 여는 중괄호는 하나의 공백과 함께 한 줄에 함께 있어야 합니다 (MUST). 
조건 사이의 부울-boolean 연산자는 항상 줄의 시작 또는 끝에 있어야하며 둘 다를 혼합해서는 안됩니다(MUST).

~~~php
<?php

switch (
    $expr1
    && $expr2
) {
    // structure body
}
~~~

### 5.3 `while`, `do while`

`while` 문은 다음과 같습니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

while ($expr) {
    // structure body
}
~~~

괄호 안의 표현은 여러 행에 걸쳐 나뉘어 질 수 있으며(MAY), 그 다음 행은 적어도 한 번 들여쓰기를 합니다.
그럴 경우 첫 번째 조건은 반드시 다음 줄에 있어야 합니다(MUST). 
닫는 괄호와 여는 중괄호는 하나의 공백과 함께 한 줄에 함께 있어야합니다 (MUST). 
조건 사이의 부울-boolean 연산자는 항상 줄의 시작 또는 끝에 있어야하며 둘 다를 혼합해서는 안됩니다(MUST).

~~~php
<?php

while (
    $expr1
    && $expr2
) {
    // structure body
}
~~~

비슷하게, `do while` 문은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

do {
    // structure body;
} while ($expr);
~~~

괄호 안의 표현은 여러 행에 걸쳐 나뉘어 질 수 있으며(MAY), 그 다음 행은 적어도 한 번 들여쓰기를 합니다.
그럴 경우 첫 번째 조건은 반드시 다음 줄에 있어야 합니다(MUST). 
조건들 사이의 부울 연산자는 언제나 둘의 혼합이 아닌 라인의 시작 또는 끝 부분에 있어야합니다 (MUST).

~~~php
<?php

do {
    // structure body;
} while (
    $expr1
    && $expr2
);
~~~

### 5.4 `for`

`for` 문은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

for ($i = 0; $i < 10; $i++) {
    // for body
}
~~~

괄호 안의 표현은 여러 행에 걸쳐 나뉘어 질 수 있으며(MAY), 그 다음 행은 적어도 한 번 들여쓰기를 합니다.
그럴 경우 첫 번째 조건은 반드시 다음 줄에 있어야 합니다(MUST). 
닫는 괄호와 여는 중괄호는 하나의 공백과 함께 한 줄에 함께 있어야합니다 (MUST).

~~~php
<?php

for (
    $i = 0;
    $i < 10;
    $i++
) {
    // for body
}
~~~

### 5.5 `foreach`

`foreach` 문은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

foreach ($iterable as $key => $value) {
    // foreach body
}
~~~

### 5.6 `try`, `catch`, `finally`

`try-catch-finally` 블록은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

try {
    // try body
} catch (FirstThrowableType $e) {
    // catch body
} catch (OtherThrowableType | AnotherThrowableType $e) {
    // catch body
} finally {
    // finally body
}
~~~

## 6. 연산자

연산자의 스타일 규칙은 arity (피연산자 수)별로 그룹화됩니다.

연산자 주변에 공백이 허용되며, 가독성을 위해 여러 공백이 있을 수 있습니다(MAY).

여기에 설명되지 않은 모든 연산자는 정의되지 않은 상태로 남아 있습니다.

### 6.1. 단항 연산자

증가/감소 연산자, 논리 not 연산자, 변환 및 부정 연산자는 연산자와 피연산자 사이에 공백이 없어야합니다(MUST NOT).

~~~php
$value++;
--$value;
$a = !$b;
$c = +$d;
$e = -$f;
~~~

타입 캐스팅 연산자는 괄호 안에 공백이 없어야합니다(MUST NOT).

~~~php
$intValue = (int) $input;
~~~

### 6.2. Binary operators

All binary [arithmetic][], [comparison][], [assignment][], [bitwise][], [logical][], [string][], and [type][] operators MUST be preceded and followed by at least one space:

### 6.2. 이진 연산자

모든 이진 [산술-arithmetic][], [비교-comparison][], [할당-assignment][], [비트-bitwise][], [논리-logical][], [문자열-string][] 및 [type][] 연산자의 앞뒤에는 최소한 하나 이상의 공백이 있어야 합니다(MUST).

~~~php
if ($a === $b) {
    $foo = $bar ?? $a ?? $b;
} elseif ($a > $b) {
    $foo = $a + $b * $c;
} elseif ($a <=> $c) {
    $foo += $bar % $a & $b ** $c;
}
~~~

### 6.3. Ternary operators

The conditional operator, also known simply as the ternary operator, MUST be preceded and followed by at least one space around both the `?` and `:` characters:


### 6.3. 삼항-Ternary 연산자

삼항 연산자라고도하는 조건부 연산자 `?` 및 `:`문자 앞뒤에는 하나 이상의 공백이 와야합니다(MUST).

~~~php
$variable = $foo ? 'foo' : 'bar';
~~~

조건부 연산자의 중간 피연산자가 생략되면 연산자는 다른 이진 [비교-comparison][] 연산자와 동일한 스타일 규칙을 따라야합니다(MUST).

~~~php
$variable = $foo ?: 'bar';
~~~

## 7. 클로저-Closures

클로저는 `function` 키워드 뒤의 공백과 `use` 키워드 앞뒤에 공백으로 선언해야합니다 (MUST).

여는 중괄호는 반드시(MUST) 같은 줄에 있어야하며 닫는 중괄호는 반드시(MUST) 그 다음 줄에 있어야합니다.

인수 목록이나 변수 목록의 여는 괄호 다음에 공백이 있으면 안되며(MUST NOT), 인수 목록이나 변수 목록의 닫는 괄호 앞에 공백이 있으면 안됩니다(MUST NOT).

인수 목록과 변수 목록에는 각 쉼표 앞에 공백이 있어서는 안되며(MUST NOT) 각 쉼표 뒤에 하나의 공백이 있어야합니다(MUST).

기본값을 가진 클로저 인수는 인수 목록의 끝에 와야합니다 (MUST).

반환유형(return type)이 있는 경우 일반 함수 및 메소드와 동일한 규칙을 따라야 하며 `use` 키워드가 존재한다면, 콜론은 두 문자 사이에 공백없이 `use` 리스트의 닫는 괄호에 따라와야합니다 (MUST).

클로저 선언은 다음과 같습니다. 괄호, 쉼표, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php

$closureWithArgs = function ($arg1, $arg2) {
    // body
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // body
};

$closureWithArgsVarsAndReturn = function ($arg1, $arg2) use ($var1, $var2): bool {
    // body
};
~~~

인수 목록과 변수 목록은 여러 행에 걸쳐 나뉘어 질 수 있으며(MAY), 각 행은 한 번 들여 쓰기됩니다. 

그럴경우 목록의 첫 번째 항목은 다음 줄에 있어야하며(MUST), 한 줄에 하나의 인수 또는 변수 만 있어야합니다(MUST).

마지막 리스트 (인수 또는 변수의 여부)가 여러 줄로 나뉘어 질 때 닫는 괄호와 여는 중괄호는 하나의 공백을 사용하여 같은 줄에 함께 있어야합니다 (MUST).

다음은 인수 목록이 있거나없는 클로저의 예와 여러 줄에 걸쳐있는 변수 목록입니다.

~~~php
<?php

$longArgs_noVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) {
   // body
};

$noArgs_longVars = function () use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // body
};

$longArgs_longVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // body
};

$longArgs_shortVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use ($var1) {
   // body
};

$shortArgs_longVars = function ($arg) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // body
};
~~~

형식 지정 규칙은 함수 또는 메소드 호출에서 클로저가 직접 인수로 사용될 때도 적용됩니다.

~~~php
<?php

$foo->bar(
    $arg1,
    function ($arg2) use ($var1) {
        // body
    },
    $arg3
);
~~~

## 8. 익명 클래스

익명 클래스는 위 섹션의 클로저와 동일한 지침과 원칙을 따라야합니다(MUST).

~~~php
<?php

$instance = new class {};
~~~

`implements` 인터페이스 목록이 감싸지 않는 한 여는 중괄호는 `class` 키워드와 같은 줄에 있을 수 있습니다(MAY). 인터페이스 목록이 감쌀 경우 중괄호는 마지막 인터페이스 바로 다음 행에 있어야합니다 (MUST).

~~~php
<?php

// Brace on the same line
$instance = new class extends \Foo implements \HandleableInterface {
    // Class content
};

// Brace on the next line
$instance = new class extends \Foo implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // Class content
};
~~~

~~~php
$intValue = (int) $input;
~~~

[PSR-1]: http://www.php-fig.org/psr/psr-1/
[PSR-2]: http://www.php-fig.org/psr/psr-2/
[keywords]: http://php.net/manual/en/reserved.keywords.php
[types]: http://php.net/manual/en/reserved.other-reserved-words.php
[arithmetic]: http://php.net/manual/en/language.operators.arithmetic.php
[assignment]: http://php.net/manual/en/language.operators.assignment.php
[comparison]: http://php.net/manual/en/language.operators.comparison.php
[bitwise]: http://php.net/manual/en/language.operators.bitwise.php
[logical]: http://php.net/manual/en/language.operators.logical.php
[string]: http://php.net/manual/en/language.operators.string.php
[type]: http://php.net/manual/en/language.operators.type.php
