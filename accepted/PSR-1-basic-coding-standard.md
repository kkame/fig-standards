# PSR-1 기본 코딩 표준

이 섹션은 공유되는 PHP 코드 간의 높은 수준의 기술적 상호 호환성을 보장하는 데 필요한 표준 코딩 사항으로 간주되어야 하는 것을 포함합니다.



이 문서에서 핵심이 되는 단어는 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", "OPTIONAL" 입니다. 
이것은 [RFC 2119]에 설명 된대로 해석해야 합니다.

> 역자주: 위의 키워드는 아래의 번역문에 괄호안에 표시하였습니다



[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[PSR-4]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md

## 1. 개요

- 반드시(MUST) `<?php` 와 `<?=` 태그만을 사용해야 합니다.

- 반드시(MUST) BOM이 없는 UTF-8로만 PHP코드를 작성해야 합니다.

- 각 파일은 무언가를 선언하거나 (클래스, 함수, 상수 등) *또는* 사이드이펙트의 원인이 되는(예 : 출력 생성, .ini 설정 변경 등) 형태로 작성해야하지만(SHOULD) 둘 모두를 수행하면 안됩니다(SHOULD NOT).

- 네임스페이스와 클래스는 반드시(MUST) "autoloading"에 관한 PSR: [[PSR-0], [PSR-4]]을 따라 작성해야 합니다.

- Class 는 반드시(MUST) `StudlyCaps`에 따라 작성해야합니다. 
  > 역자주: StudlyCaps 라는것은 단어의 첫글자가 대문자인 규칙을 말합니다. ex: DefaultClass

- Class의 상수(constants)는 반드시(MUST) 전부 대문자와 밑줄`_`만으로 작성해야합니다.

- Method는 반드시(MUST) `camelCase`로 작성해야합니다.

## 2. 파일

### 2.1. PHP Tags

PHP 코드는 반드시(MUST)  긴 `<?php ?>` 태그나  짧은 `<?= ?>` 태그를 사용해야합니다
그외의 태그를 사용해서는 안됩니다 (MUST NOT).

### 2.2. Character Encoding
PHP 코드는 반드시(MUST) BOM이 없는 UTF-8 문자열만 사용해야합니다

### 2.3. 사이드이펙트

각 파일은 새로운 무언가 (클래스, 함수, 상수 등)를 선언하고 다른 사이드이펙트를 일으키지 않거나,
사이드이펙트가 있는 로직를 실행하지만(SHOULD) 둘 다 수행하면 안됩니다(SHOULD NOT).

"사이드이펙트"는 클래스, 함수, 상수 등을 선언하는 것과 직접적으로 관련이없는 논리를 파일에 단순히 포함하는 것부터 실행하는 것 전부를 의미합니다.

> 역자주: 일반적으로 프로그래밍에서 사용되는 사이드이펙트의 의미와 이 문장에서 사용되는 사이드이펙트의 의미는 약간의 차이가 있습니다

"사이드이펙트"에는 Output 생성, 명시적인 "require"또는 "include"의 사용, 외부 서비스 연결, ini 설정 변경, 오류 또는 예외 발생, 전역 변수 또는 정적 변수 변경, 파일의 읽기 또는 쓰기 등이 있습니다.

다음은 선언과 사이드이펙트가 모두 포함 된 파일의 예입니다.

예제:

~~~php
<?php
// side effect: change ini settings
ini_set('error_reporting', E_ALL);

// side effect: loads a file
include "file.php";

// side effect: generates output
echo "<html>\n";

// declaration
function foo()
{
    // function body
}
~~~

다음 예제는 사이드이펙트가 없이 작성된 파일입니다.

예제:

~~~php
<?php
// declaration
function foo()
{
    // function body
}

// conditional declaration is *not* a side effect
if (! function_exists('bar')) {
    function bar()
    {
        // function body
    }
}
~~~

## 3. Namespace 와 Class 

네임 스페이스와 Class는 반드시(MUST) [자동 로딩 (autoloading)] PSR [[PSR-0], [PSR-4]]을 따라야합니다.

이것은 클래스가 각각의 개별 파일에 작성되어있으며, 공급자(vendor)의 이름으로 시작하는 네임스페이스를 사용했다는 것을 의미합니다. 

클래스 이름은 반드시(MUST) `StudlyCaps` 규칙으로 작성되어야 합니다.

PHP 5.3 및 이후 버전 용으로 작성된 코드는 정식 네임 스페이스를 사용해야합니다.

예제:

~~~php
<?php
// PHP 5.3 and later:
namespace Vendor\Model;

class Foo
{
}
~~~


5.2.x 용으로 작성된 코드에서 네임 스페이스와 유사한 규칙을 사용하기 위해
클래스 이름에 `Vendor_` 접두어를 붙입니다(SHOULD).



~~~php
<?php
// PHP 5.2.x and earlier:
class Vendor_Model_Foo
{
}
~~~

## 4. Class의 상수(Constants), Properties, Methods

"Class"라는 용어는 모든 Class, Interface 및 Trait을 의미합니다.


### 4.1. 상수(Constants)

클래스 상수는 모두 대문자 또는 밑줄`_`로 선언해야합니다 (MUST).

예제:

~~~php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
~~~

### 4.2. Properties

이 가이드는 의도적으로 `$StudlyCaps` , `$camelCase` 또는 `$under_score` 같은 형태로 property 이름을 권장하는 것을 피하고 있습니다.

Properties는 어떤 규칙이 사용 되든 상관없지만 합리적인 범위 내에서 일관되게 적용되어야 합니다(SHOULD).
이 범위는 공급자 수준(vendor-level), 패키지 수준(package-level), Class 수준(class-level) 또는 Method(method-level) 수준 일 수 있습니다.

### 4.3. Methods

Method의 이름은 반드시(MUST) `camelCase`로 작성해야 합니다.