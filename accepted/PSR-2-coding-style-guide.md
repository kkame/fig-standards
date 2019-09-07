# 코딩 스타일 가이드

> **사용중단** - 2019-08-10을 기준으로 PSR-2은 사용이 중단되었습니다. 새로운 표준으로 [PSR-12]을 사용하시길 권장합니다.

[PSR-12]: accepted/PSR-12-extended-coding-style-guide.md

이 가이드는 기본 코딩 표준인 [PSR-1]을 확장입니다.

이 가이드의 의도는 다른 저자의 코드를 볼 때 가독성을 해치는 것을 줄이는 것입니다. 
PHP 코드의 형식을 지정하는 방법에 대한 여러가지 규칙과 기대 사항을 나열함으로써 이를 달성합니다.

이 스타일 규칙은 다양한 회원 프로젝트 간의 공통점에서 파생되었습니다.
다양한 저자가 여러 프로젝트에서 공동 작업을 수행 할 때 모든 프로젝트 중에서 한 가지의 가이드을 사용하는 것이 도움이 됩니다.
따라서 이 가이드의 이점은 규칙 자체가 아니라 이러한 규칙을 공유하는 것입니다.

이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.
`역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다`

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[PSR-1]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md

## 1. 개요

- 코드는 반드시(MUST) "코딩 스타일 가이드" PSR [[PSR-1]]을 따라야합니다.

- 코드는 들여쓰기에 반드시(MUST) 탭이 아니라 스페이스 4칸을 사용해야합니다.

- 한줄에 들어가는 문자열의 길이에 엄격한 제한이 있으면 안됩니다. 가벼운 제한은 한줄에 120자입니다 (MUST). 가능하다면 한줄은 80자 이하 여야합니다 (SHOULD).

- `namespace` 선언 다음에 빈 줄이 하나 있어야만 하며(MUST) 그리고 `use` 선언 블록 뒤에 빈 줄이 하나 있어야만 합니다 (MUST).

- 클래스의 여는 중괄호는 다음 줄로 가야만 하며(MUST), 닫는 중괄호는 본문 뒤의 다음 줄로 가야만 합니다(MUST).

- 메서드의 여는 중괄호는 다음 줄로 가야만 하며(MUST), 닫는 중괄호는 본문 뒤의 다음 줄로 가야만 합니다(MUST).

- 모든 속성 및 메서드에서 가시성을 선언해야만 합니다 (MUST). `abstract`와`final`은 가시성 (visibility) 전에 선언되어야만 합니다(MUST). `static`은 가시성 뒤에 선언되어야만 합니다 (MUST).

- 제어문 키워드는 그 뒤에 하나의 공백을 가져야만 합니다 (MUST). 메서드와 함수에서 해서는 안됩니다(MUST NOT). `역자주: if, switch, try, catch, foreach 등`

- 제어문를 여는 중괄호는 같은 줄에 있어야만 하며(MUST), 닫는 중괄호는 반드시 본문 뒤의 다음 줄로 가야만 합니다(MUST).

- 제어문를 여는 괄호는 그 뒤에 공백이 없어야만 하며(MUST NOT), 제어문에 대한 닫는 괄호는 전에는 공백이 없어야만 합니다(MUST NOT).

### 1.1. 예제

이 예는 아래의 규칙 중 일부를 간략하게 설명합니다.

~~~php
<?php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    public function sampleMethod($a, $b = null)
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

## 2. General

### 2.1. Basic Coding Standard

코드는 반드시(MUST) [PSR-1]에 요약 된 모든 규칙을 따라야합니다.

### 2.2. Files

모든 PHP 파일은 Unix LF (linefeed) 줄 끝을 사용해야합니다.

모든 PHP 파일은 하나의 빈 줄을 가진 채로 끝나야합니다.

닫는 `?>` 태그는 PHP만 포함 된 파일일 경우 생략해야합니다 (MUST).

### 2.3. 라인 (Lines)

라인 길이에 엄격한 제한이 있어서는 안됩니다(MUST NOT).

라인 길이에 대한 소프트 제한은 120 자 여야합니다(MUST). 자동화 된 스타일 검사기는 반드시(MUST) 경고해야하지만 소프트 한도에서 오류를 표시해서는 안됩니다(MUST NOT).

라인은 80자를 넘지 않아야합니다 (SHOULD NOT). 그보다 긴 행은 각각 80 문자 이하의 여러 행으로 나눠 져야합니다 (SHOULD).

비어 있지 않은 라인 끝에는 공백 문자가 없어야합니다(MUST NOT).

가독성을 높이고 관련 코드 블록을 나타 내기 위해 빈 라인을 추가 할 수 있습니다 (MAY).

한 라인에 하나 이상의 문장이 있어서는 안됩니다(MUST NOT).

### 2.4. 들여쓰기 (Indenting)

코드는 반드시(MUST) 4 스페이스의 들여 쓰기를 사용해야하며, 들여 쓰기에는 탭을 사용하지 않아야합니다(MUST NOT).

> 공백 만 사용하고 탭과 공백을 섞지 않으면 diff, 패치, 히스토리 및 주석 문제를 피할 수 있습니다.
> 공백을 사용하면 행간 정렬을 위해 세분화 된 하위 들여 쓰기를 쉽게 삽입 할 수 있습니다.

### 2.5. 예약어(Keywords)와 True/False/Null

PHP [예약어]는 소문자 여야합니다.

PHP 상수 `true`,`false` 및 `null`은 반드시(MUST) 소문자 여야합니다.

[예약어]: http://php.net/manual/en/reserved.keywords.php

## 3. 네임 스페이스 및 사용 선언

네임스페이스가 존재할 때 `namespace` 선언 다음에 하나의 빈 라인이 있어야 합니다(MUST).

네임스페이스가 존재할 때, 모든 `use` 선언은 반드시 `namespace` 선언을 따라야 만합니다(MUST).

선언 하나당 하나의 `use` 키워드가 있어야합니다(MUST).

`use` 블록 뒤에 빈 줄이 하나 있어야합니다(MUST).

예제 :

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

// ... additional PHP code ...

~~~

## 4. 클래스, 프로퍼티(Properties) 및 메서드

"클래스"라는 용어는 모든 클래스, 인터페이스 및 trait을 나타냅니다.

### 4.1. 확장 및 구현

`extends`와 `implements` 키워드는 클래스 이름과 같은 라인에서 선언되어야합니다 (MUST).

해당 클래스의 여는 중괄호는 자체 줄에 있어야합니다(MUST).
클래스의 닫는 중괄호는 본문 뒤의 다음 줄로 가야합니다(MUST).

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

`implements`의 목록은 여러 줄에 걸쳐 나뉘어 질 수 있습니다. 각 줄은 한 번 들여 쓰기를 합니다.
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

### 4.2. 프로퍼티(Properties)

모든 프로퍼티에서 가시성을 반드시 선언해야합니다(MUST).

`var` 키워드는 프로퍼티를 선언하는데 사용되어서는 안됩니다 (MUST NOT).

명령문마다 하나 이상의 프로퍼티가 선언되어서는 안됩니다(MUST NOT).

프로퍼티 이름은 protected 또는 private 가시성을 나타 내기 위해 하나의 밑줄이 붙어서는 안됩니다 (SHOULD NOT).

속성 선언은 다음과 같습니다.

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public $foo = null;
}
~~~

### 4.3. Methods

모든 메서드는 가시성을 선언해야합니다 (MUST).

메서드 이름은 protected 또는 private 가시성을 표현하기 위해 하나의 밑줄로 접두어를 사용해서는 안됩니다 (SHOULD NOT).

메서드 이름은 메서드 이름 다음에 공백으로 선언하면 안됩니다 (MUST NOT).
여는 중괄호는 반드시 자신의 줄에 있어야하며(MUST) 닫는 중괄호는 반드시 그 다음 줄에 있어야합니다(MUST).
여는 괄호 뒤에 공백이 있으면 안되며(MUST NOT) 닫는 괄호 앞에 공백이 있어서는 안됩니다(MUST NOT).

메서드 선언은 다음과 같습니다.
괄호, 쉼표, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~

### 4.4. 메서드 인수(Method Arguments)

인수 목록에는 각 쉼표 앞에 공백이 있으면 안되며(MUST NOT) 각 쉼표 뒤에 하나의 공백이 있어야합니다(MUST).

디폴트 값을 가진 메서드 인수는 인수 목록의 끝에 와야합니다 (MUST).

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function foo($arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~

인수 목록은 여러 줄에 걸쳐 나뉘어 질 수 있으며(MAY), 각 줄은 한 번 들여 쓰여질 수 있습니다.
그렇게 할 때 목록의 첫 번째 항목은 다음 줄에 있어야하며(MUST) 한 줄에 하나의 인수 만 있어야합니다(MUST).

인수 목록이 여러 줄에 걸쳐 분할되어 있으면 닫는 괄호와 여는 중괄호는 한 줄의 공백을 사용하여 각각의 줄에 함께 있어야합니다 (MUST).

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

### 4.5. `abstract`, `final`, and `static`

`abstract`와`final` 선언은 가시성 선언 앞에 와야합니다(MUST).

`static` 선언은 가시성 선언 뒤에 와야합니다(MUST).

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

### 4.6. 메소드(Method) 호출과 함수(Function) 호출

메서드 나 함수 호출을 할 때 메서드 나 함수 이름과 여는 괄호 사이에 공백이 없어야합니다(MUST NOT). 
여는 괄호 뒤에 공백이 있으면 안되며(MUST NO) 닫는 괄호 앞에 공백이 있어서는 안됩니다(MUST NOT).
인수 목록에는 각 쉼표 앞에 공백이 있으면 안되며(MUST NOT) 각 쉼표 뒤에 하나의 공백이 있어야합니다(MUST).

~~~php
<?php
bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
~~~

인수 목록은 여러 줄에 걸쳐 나뉘어 질 수 있으며(MAY), 각 줄은 한 번 들여 쓰여질 수 있습니다.
그렇게 할 때 목록의 첫 번째 항목은 다음 줄에 있어야하며(MUST) 한 줄에 하나의 인수 만 있어야합니다(MUST).

~~~php
<?php
$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
~~~

## 5. 제어문(Control Structures)

제어문의 일반적인 스타일 규칙은 다음과 같습니다.

- 제어문 키워드 다음에 하나의 공백이 있어야합니다 (MUST).
- 여는 괄호 뒤에 공백이 있으면 안됩니다 (MUST NOT).
- 닫는 괄호 앞에 공백이 없어야합니다 (MUST NOT).
- 닫는 괄호와 여는 중괄호 사이에 하나의 공백이 있어야합니다 (MUST).
- 구조체는 한 번 들여 쓰기되어야합니다 (MUST).
- 닫는 중괄호는 제어문의 다음 줄에 있어야합니다 (MUST).

각 제어문는 중괄호로 묶어야합니다 (MUST).
이것은 구조가 어떻게 보이는지를 표준화하고, 새로운 라인이 제어문에 추가 될 때 오류가 발생할 가능성을 줄입니다.

### 5.1. `if`, `elseif`, `else`

`if` 구조는 다음과 같습니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오. 
`else`와 `elseif`는 이전 제어문의 닫는 중괄호와 같은 줄에 있습니다.

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

`else if` 키워드 대신 `elseif` 키워드를 사용하여 모든 제어 키워드가 단일 단어처럼 보이도록 해야 합니다 (SHOULD).

### 5.2. `switch`, `case`

`switch` 구조체는 다음과 같습니다.  괄호, 공백 및 중괄호의 배치에 유의하십시오. 
`case` 문은 `switch`에서 한 번 들여 쓰기되어야하며(MUST) `break` 키워드 (또는 다른 종결 키워드)는 `case` 문과 같은 수준에서 들여 쓰기되어야합니다 (MUST).
비어 있지 않은 `case` 문에서 의도적으로 다음으로 넘기는 경우 `// no break`와 같은 주석이 있어야합니다(MUST).

~~~php
<?php
switch ($expr) {
    case 0:
        echo 'First case, with a break';
        break;
    case 1:
        echo 'Second case, which falls through';
        // no break
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

### 5.3. `while`, `do while`

`while` 문은 다음과 같습니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php
while ($expr) {
    // structure body
}
~~~

비슷한 경우로, do while 문은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php
do {
    // structure body;
} while ($expr);
~~~

### 5.4. `for`

`for` 문은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php
for ($i = 0; $i < 10; $i++) {
    // for body
}
~~~

### 5.5. `foreach`

`foreach` 문은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php
foreach ($iterable as $key => $value) {
    // foreach body
}
~~~

### 5.6. `try`, `catch`

`try catch` 블록은 다음과 같이 보입니다. 괄호, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php
try {
    // try body
} catch (FirstExceptionType $e) {
    // catch body
} catch (OtherExceptionType $e) {
    // catch body
}
~~~

## 6. 클로저(Closures)

클로저는 `function` 키워드 뒤의 공백과 `use` 키워드 앞뒤 공백으로 선언해야합니다 (MUST).

여는 중괄호는 반드시 같은 줄에 있어야하며(MUST) 닫는 중괄호는 반드시 그 다음 줄에 있어야합니다(MUST).

인수 목록이나 변수 목록의 여는 괄호 다음에 공백이 있으면 안되며(MUST NOT) 인수 목록이나 변수 목록의 닫는 괄호 앞에 공백이 있으면 안됩니다(MUST NOT).

인수 목록과 변수 목록에는 각 쉼표 앞에 공백이 있어서는 안되며(MUST NOT) 각 쉼표 뒤에 하나의 공백이 있어야합니다(MUST).

기본값을 가진 클로저 인수는 인수 목록의 끝에 와야합니다 (MUST).

클로저 선언은 다음과 같습니다. 괄호, 쉼표, 공백 및 중괄호의 배치에 유의하십시오.

~~~php
<?php
$closureWithArgs = function ($arg1, $arg2) {
    // body
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // body
};
~~~

인수 목록과 변수 목록은 여러 행에 걸쳐 나뉘어 질 수 있습니다(MAY). 각 행은 한 번씩 들여 쓰기됩니다.
그렇게 할 경우 첫 번째 항목은 다음 줄에 있어야만하며(MUST) 한 줄에 하나의 인수 또는 변수 만 있어야합니다(MUST).

마지막 리스트 (인수 또는 변수의 여부)가 여러 줄로 나뉘어 질 때, 닫는 괄호와 여는 중괄호는 하나의 공백을 사용하여 한줄에 있어야합니다 (MUST).

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

## 7. 결론

이 가이드에는 의도적으로 생략 한 스타일과 연습의 많은 요소가 있습니다. 여기에는 다음이 포함되지만 이에 국한되지는 않습니다 :

- 전역 변수 및 전역 상수 선언

- 함수 선언

- 운영자 및 과제

- 라인 간 정렬

- 주석 및 문서 블록

- 클래스 이름 접두사 및 접미사

- 모범 사례

향후 권장 사항은 스타일이나 실습의 요소 또는 기타 요소를 다루기 위해이 가이드를 수정하고 확장 할 수 있습니다.

## 부록 A. 설문조사

이 스타일 가이드를 작성하면서이 그룹은 공통 실천을 결정하기 위해 회원 프로젝트에 대한 설문 조사를 실시했습니다. 조사는 후일을 위해 여기에 유지됩니다.

### A.1. 설문조사 자료

    url,http://www.horde.org/apps/horde/docs/CODING_STANDARDS,http://pear.php.net/manual/en/standards.php,http://solarphp.com/manual/appendix-standards.style,http://framework.zend.com/manual/en/coding-standard.html,https://symfony.com/doc/2.0/contributing/code/standards.html,http://www.ppi.io/docs/coding-standards.html,https://github.com/ezsystems/ezp-next/wiki/codingstandards,http://book.cakephp.org/2.0/en/contributing/cakephp-coding-conventions.html,https://github.com/UnionOfRAD/lithium/wiki/Spec%3A-Coding,http://drupal.org/coding-standards,http://code.google.com/p/sabredav/,http://area51.phpbb.com/docs/31x/coding-guidelines.html,https://docs.google.com/a/zikula.org/document/edit?authkey=CPCU0Us&hgd=1&id=1fcqb93Sn-hR9c0mkN6m_tyWnmEvoswKBtSc0tKkZmJA,http://www.chisimba.com,n/a,https://github.com/Respect/project-info/blob/master/coding-standards-sample.php,n/a,Object Calisthenics for PHP,http://doc.nette.org/en/coding-standard,http://flow3.typo3.org,https://github.com/propelorm/Propel2/wiki/Coding-Standards,http://developer.joomla.org/coding-standards.html
    voting,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,no,no,no,?,yes,no,yes
    indent_type,4,4,4,4,4,tab,4,tab,tab,2,4,tab,4,4,4,4,4,4,tab,tab,4,tab
    line_length_limit_soft,75,75,75,75,no,85,120,120,80,80,80,no,100,80,80,?,?,120,80,120,no,150
    line_length_limit_hard,85,85,85,85,no,no,no,no,100,?,no,no,no,100,100,?,120,120,no,no,no,no
    class_names,studly,studly,studly,studly,studly,studly,studly,studly,studly,studly,studly,lower_under,studly,lower,studly,studly,studly,studly,?,studly,studly,studly
    class_brace_line,next,next,next,next,next,same,next,same,same,same,same,next,next,next,next,next,next,next,next,same,next,next
    constant_names,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper
    true_false_null,lower,lower,lower,lower,lower,lower,lower,lower,lower,upper,lower,lower,lower,upper,lower,lower,lower,lower,lower,upper,lower,lower
    method_names,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel,lower_under,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel
    method_brace_line,next,next,next,next,next,same,next,same,same,same,same,next,next,same,next,next,next,next,next,same,next,next
    control_brace_line,same,same,same,same,same,same,next,same,same,same,same,next,same,same,next,same,same,same,same,same,same,next
    control_space_after,yes,yes,yes,yes,yes,no,yes,yes,yes,yes,no,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes
    always_use_control_braces,yes,yes,yes,yes,yes,yes,no,yes,yes,yes,no,yes,yes,yes,yes,no,yes,yes,yes,yes,yes,yes
    else_elseif_line,same,same,same,same,same,same,next,same,same,next,same,next,same,next,next,same,same,same,same,same,same,next
    case_break_indent_from_switch,0/1,0/1,0/1,1/2,1/2,1/2,1/2,1/1,1/1,1/2,1/2,1/1,1/2,1/2,1/2,1/2,1/2,1/2,0/1,1/1,1/2,1/2
    function_space_after,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no
    closing_php_tag_required,no,no,no,no,no,no,no,no,yes,no,no,no,no,yes,no,no,no,no,no,yes,no,no
    line_endings,LF,LF,LF,LF,LF,LF,LF,LF,?,LF,?,LF,LF,LF,LF,?,,LF,?,LF,LF,LF
    static_or_visibility_first,static,?,static,either,either,either,visibility,visibility,visibility,either,static,either,?,visibility,?,?,either,either,visibility,visibility,static,?
    control_space_parens,no,no,no,no,no,no,yes,no,no,no,no,no,no,yes,?,no,no,no,no,no,no,no
    blank_line_after_php,no,no,no,no,yes,no,no,no,no,yes,yes,no,no,yes,?,yes,yes,no,yes,no,yes,no
    class_method_control_brace,next/next/same,next/next/same,next/next/same,next/next/same,next/next/same,same/same/same,next/next/next,same/same/same,same/same/same,same/same/same,same/same/same,next/next/next,next/next/same,next/same/same,next/next/next,next/next/same,next/next/same,next/next/same,next/next/same,same/same/same,next/next/same,next/next/next

### A.2. 설문 조사 범례

`indent_type`:
들여 쓰기의 유형. `tab`= "탭 사용", `2`또는 `4`= "공백 개수"

`line_length_limit_soft`:
"소프트"줄 길이 제한 (문자 수). `?`= 식별 할 수 없거나 응답이 없으면 `아니오`는 제한이 없음을 의미합니다.

`line_length_limit_hard`:
"하드"줄 길이 제한 (문자 수). `?`= 식별 할 수 없거나 응답이 없으면 `아니오`는 제한이 없음을 의미합니다.

`class_names`:
클래스 이름 지정 방법. `lower` = 소문자 만, `lower_under` = 밑줄 구분 기호가있는 소문자, `studly` = StudlyCase.

`class_brace_line`:
클래스의 여는 중괄호가 class 키워드와 `같은` 줄에 있거나, 그 `다음` 줄에 있습니까?

`constant_names`:
클래스 상수는 어떻게 명명됩니까? `upper` = 밑줄 구분자가있는 대문자.

`true_false_null`:
`true`, `false` 및 `null` 키워드는 모두 `lower`또는 모두 `upper`로 철자가 맞습니까?

`method_names`:
메소드은 어떻게 명명됩니까? `camel` =`camelCase`,`lower_under` = 밑줄 구분 기호가있는 소문자.

`method_brace_line`:
메소드의 여는 중괄호가 메소드 이름과 `같은` 줄 또는 `다음` 줄에 있습니까?

`control_brace_line`:
제어문의 여는 중괄호가 `같은` 줄 또는 `다음` 줄에 있습니까?

`control_space_after`:
제어문 키워드 다음에 공백이 있습니까?

`always_use_control_braces`:
제어문은 항상 중괄호를 사용합니까?

`else_elseif_line`:
`else` 나`elseif`를 사용할 때, 이전 닫는 중괄호와 `같은` 줄로 가야합니까, 아니면 `다음` 줄로 가나 요?

`case_break_indent_from_switch`:
`switch` 문에서 `case`와 `break`가 몇 번 들여 쓰여졌습니까?

`function_space_after`:
함수 호출은 함수 이름 뒤에 여는 괄호 앞에 공백이 있습니까?

`closing_php_tag_required`:
PHP 만 포함 된 파일에서 닫는 `?>`태그가 필요합니까?

`line_endings`:
어떤 유형의 종료 라인이 사용됩니까?

`static_or_visibility_first`:
메서드를 선언 할 때 `static`이 먼저 오는가, 아니면 가시성이 먼저 오는가?

`control_space_parens`:
제어 구조 표현식에서 여는 괄호 뒤에 공백이 있고 닫는 괄호 앞에 공백이 있습니까? `yes` =`if ( $expr )`,`no` =`if ($expr)`입니다.

`blank_line_after_php`:
여는 PHP 태그 뒤에 빈 줄이 있습니까?

`class_method_control_brace`:
클래스, 메서드 및 컨트롤 구조에 대해 여는 중괄호가 어떤 줄로 표시되는지 요약합니다.

### A.3. 설문조사 결과

    indent_type:
        tab: 7
        2: 1
        4: 14
    line_length_limit_soft:
        ?: 2
        no: 3
        75: 4
        80: 6
        85: 1
        100: 1
        120: 4
        150: 1
    line_length_limit_hard:
        ?: 2
        no: 11
        85: 4
        100: 3
        120: 2
    class_names:
        ?: 1
        lower: 1
        lower_under: 1
        studly: 19
    class_brace_line:
        next: 16
        same: 6
    constant_names:
        upper: 22
    true_false_null:
        lower: 19
        upper: 3
    method_names:
        camel: 21
        lower_under: 1
    method_brace_line:
        next: 15
        same: 7
    control_brace_line:
        next: 4
        same: 18
    control_space_after:
        no: 2
        yes: 20
    always_use_control_braces:
        no: 3
        yes: 19
    else_elseif_line:
        next: 6
        same: 16
    case_break_indent_from_switch:
        0/1: 4
        1/1: 4
        1/2: 14
    function_space_after:
        no: 22
    closing_php_tag_required:
        no: 19
        yes: 3
    line_endings:
        ?: 5
        LF: 17
    static_or_visibility_first:
        ?: 5
        either: 7
        static: 4
        visibility: 6
    control_space_parens:
        ?: 1
        no: 19
        yes: 2
    blank_line_after_php:
        ?: 1
        no: 13
        yes: 8
    class_method_control_brace:
        next/next/next: 4
        next/next/same: 11
        next/same/same: 1
        same/same/same: 6
