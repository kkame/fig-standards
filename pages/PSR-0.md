---
title: PSR-0 Autoloading Standard
layout: "page"
order: 0
---


표준 Autoloading
====================

> **사용중단** - 2014-10-21을 기준으로 PSR-0은 사용이 중단되었습니다. 새로운 표준으로 [PSR-4]을 사용하시길 권장합니다.

[PSR-4]: http://www.php-fig.org/psr/psr-4/


다음은 오토로더 상호 운용성을 위해 반드시 준수해야하는 필수 요구 사항에 대해 설명합니다.

필수 요구사항
---------

* 정규화된 네임스페이스 및 클래스는 다음과 같은 구조를 따라야 합니다.
 `\<Vendor Name>\(<Namespace>\)*<Class Name>`
* 각 네임스페이스의 최상위 네임스페이스에는 `공급자(Vendor)의 이름`을 사용해야 합니다.
* 각 네임스페이스는 원하는 만큼의 하위 네임스페이스를 가질 수 있습니다.
* 각 네임스페이스의 구분기호는 `DIRECTORY_SEPARATOR`로 변환됩니다.
* CLASS 이름에서 각 `_` 문자는 `DIRECTORY_SEPARATOR`로 변환됩니다. `_`는 네임스페이스에서 특별한 의미가 없습니다.
* 규칙에 맞게 작성된 클래스를 파일시스템에서 불러올 때 `.php`가 마지막에 붙습니다.
* 공급자(Vendor) 이름, 네임 스페이스 및 클래스 이름의 알파벳 문자는 소문자와 대문자의 조합으로 구성 될 수 있습니다.

예제
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

밑줄이 있는 네임스페이와 클래스의 예제
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

고통받지 않고 오토로더를 사용하려면 최소한 이런 규약들을 지켜야 합니다. 
PHP 5.3 이상에서 아래와 같은 샘플 SplClassLoader 구현을 사용하여 이러한 표준을 따르고 있는지 테스트 할 수 있습니다.


구현 예제
----------------------

다음은 위에 제안 된 표준이 자동으로 로드되는 방식을 간단하게 보여주는 예제입니다.

~~~php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
~~~

SplClassLoader 구현예제
-----------------------------
다음 샘플은 위에 제안 된 오토로더 표준을 따를때 클래스를 자동으로 로드 할 수있는 SplClassLoader의 구현 예제입니다.

PHP 5.3에서 PSR-0을 따르는 클래스를 로드하는 방법 중 현재 권장되는 방법입니다.


* [http://gist.github.com/221634](http://gist.github.com/221634)

