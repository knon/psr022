<center><h1>PHP编码规范</h1></center>

<h1 id="PSR-0">PSR-0 自动加载</h1>

为了自动加载器互通性，以下强制要求的描述内容必须遵守。
强制性
-----
* 合格的命名空间和类必须遵守以下结构：

    `\<Vendor Name>\(<Namespace>\)*<Class Name>`

    `\<应用包名>\(<命名空间>\)*<类名>`

* 命名空间必须有一个顶级命名空间（“Vendor Name”）。
* 命名空间可以有多个子命名空间（“Namespace”）。
* 从文件系统中加载时，命名空间的分隔符将转成DIRECTORY_SEPARATOR。
* 类名中每个`_`将转成DIRECTORY_SEPARATOR。命名空间中的`_`没有特殊意义。
* 从文件系统中加载时，合格的命名空间和类将加以后缀.php。
* 应用包名，命名空间和类名中的字母可以是任意大小写。

实例
----

*`\Doctrine\Common\IsolatedClassLoader`=>`/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
*`\Symfony\Core\Request`=>`/path/to/project/lib/vendor/Symfony/Core/Request.php`
*`\Zend\Acl`=>`/path/to/project/lib/vendor/Zend/Acl.php`
*`\Zend\Mail\Message`=>`/path/to/project/lib/vendor/Zend/Mail/Message.php`

命名空间和类名中的下划线
---------------------

*`\namespace\package\Class_Name`=>`/path/to/project/lib/vendor/namespace/package/Class/Name.php`
*`\namespace\package_name\Class_Name`=>`/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

这些是为了容易实现自动加载互通性的最低标准。以下是可以加载PHP 5.3的类的加载器样例。
你可以调用该样例检查是否遵守标准。


样例
----

下面是一个简单方法，用来描述以上建议的标准是如何进行自动加载类。

```php
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
```

<h2 id="PSR-1">PSR-1 基本编码标准</h2>

为保证团队合作编码的高度互通性，这章包含了PHP元素标准。

文中所用关键字 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", 和 "OPTIONAL" 在[RFC 2119][]中描述.

MUST：`必须`

MUST NOT：`禁止`

REQUIRED：`必要`

SHALL：`将要`

SHALL NOT：`不得`

SHOULD：`应该`

SHOULD NOT：`不该`

RECOMMENDED：`推荐`

MAY：`可以`

OPTIONAL：`可选`

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: #PSR-0


1. 概述
-------

- 源文件内容`必须`只用`<?php`或`<?=`标签。

- PHP源文件内容`必须`只用不含 BOM 头的 UTF-8 字符编码。

- 源文件`应该`*不是* 声明符号 （类，函数和常量等）
  *就是* 辅助效应 (例如生成输出内容和修改.ini设置等)。
  但是`不该`两都皆有。

- 命名空间和类`必须`遵守[PSR-0][]标准。

- 类名`必须`按`StudlyCaps（首字母大写驼峰法）`来命名。

- 类的常量`必须`用大写字母与下划线来声明。

- 方法名`必须`按`camelCase（首字母小写驼峰法）`进行声明。


2. 源文件
--------

### 2.1. PHP标签

PHP代码`必须`使用长标签`<?php ?>`或短标签`<?= ?>`; 
`禁止`使用其他标签。

### 2.2. 字符编码

PHP代码`必须`只用不含 BOM 头的 UTF-8 字符编码。

### 2.3. 辅助效应

一个源文件`应该`声明新符号（类，函数和常量等）并不引起其他辅助效应，
或者`应该`作为辅助效应执行逻辑运算，但是`不该`两都皆有。

仅引入源文件，短语 "辅助效应" 表示逻辑运算执行，但不直接与声明类，函数和常量关联。

"辅助效应"包含但不局限于：生成输出内容，明确调用`require`或`include`，连接额外服务，
修改ini设置，抛出错误或异常，修改全局（global）或静态（static）变量，读取或修改文件等。

以下是一个既有声明又有辅助效应的源文件的例子，需要避免：

```php
<?php
// 辅助效应: 修改 ini 设置
ini_set('error_reporting', E_ALL);

// 辅助效应: 加载源文件
include "file.php";

// 辅助效应: 生成输出内容
echo "<html>\n";

// 声明
function foo()
{
    // 函数体
}
```

以下是一个只有声明没有辅助效应的的源文件的例子，需要效仿：

```php
<?php
// 声明
function foo()
{
    // 函数体
}

// 条件声明*不*是辅助效应
if (! function_exists('bar')) {
    function bar()
    {
        // 函数体
    }
}
```


3. 命名空间和类的名称
-------------------

命名空间和类`必须`遵守[PSR-0][]标准，即一个类一个源文件，
并且至少在一级命名空间里——顶级应用包名。

类名`必须`按`StudlyCaps（首字母大写驼峰法）`来声明。

PHP 5.3及以后版本`必须`用正规的命名空间进行代码输写，例如：

```php
<?php
// PHP 5.3及以后版本:
namespace Vendor\Model;

class Foo
{
}
```

PHP 5.2.x及早期版本`应该`用伪命名空间：`应用包名_（Vendor_）`作为类名前缀。

```php
<?php
// PHP 5.2.x及早期版本:
class Vendor_Model_Foo
{
}
```

4. 类的常量，属性和方法
--------------------

术语"类（class）"提及所有类（classes），接口（interfaces）和特征（traits）。

### 4.1. 常量

类的常量`必须`用大写字母与下划线来声明，例如：

```php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. 属性

关于用`$StudlyCaps（首字母大写驼峰法）`，`$camelCase（首字母小写驼峰法）`
和`$under_score（下划线法）命名属性，这份指导故意回避任何建议。

无论命名习惯如何使用，`应该`一贯应用一个合理的范围，可以是应用包级（vendor-level），
包级（package-level），类级（class-level）或方法级（method-level）。

### 4.3. 方法

方法名`必须`按`camelCase()（首字母小写驼峰法）`命名。

<h2 id="PSR-2">PSR-2 编码样式指导</h2>

这份指导基于基本编码标准[PSR-1][]并扩充。

这份指导的意图是为了在查看来自不同作者的代码时减少阅读障碍。
关于如何格式化PHP代码，文中列举了一套共享法则和期望。

此处的样式法则是从各种团队项目中的共性派生出来的，用于当不同作者交叉于多个项目进行合作时，
有一套用于那些项目中的指导。因此，这份指导的好处不是在法则中，而是在遵守那些法则中。

文中所用关键字`必须`,`禁止`,`必要`,`将要`,`不得`,`应该`,
`不该`,`推荐`,`可以`, 和`可选`在[RFC 2119][]中描述.

[PSR-1]: #PSR-1


1. 概述
-----------

- 代码`必须`遵守 [PSR-1][]。

- 代码`必须`用4个空格而不是制表符进行缩进。

- 行宽（行长度）`禁止`硬性限制（自动换行）; 软性限制（标尺）`必须`是120个字符; 
每行内容`应该`在80个字符以内。

-`命名空间（namespace）`声明之后`必须`空一行，
并且`引用（use）`声明代码块之后`必须`空一行。

- 类后的`{``必须`另起一行，并且类体后的`}`也`必须`另起一行。

- 方法后的`{``必须`另起一行，并且方法体后的`}`也`必须`另起一行。

- 所有属性和方法的访问权限（public, protected, private）`必须`声明; 
`abstract`和`final``必须`在访问权限前声明; 
`static``必须`在访问权限后声明。

- 控制结构关键字后面`必须`留一个空格; 方法和函数调用时`禁止`。

- 控制结构的`{``必须`在同一行，并且结构体后的`}``必须`另起一行。

- 控制结构后的`(`后面`禁止`留空格，并且控制结构的`)`前面`禁止`留空格。

### 1.1. 样例

作为简要概述，这个例子包括了后面的法则。

```php
<?php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    public function sampleFunction($a, $b = null)
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
        // 方法体
    }
}
```

2. 常规
-------

### 2.1 基本编码标准

代码`必须`遵守[PSR-1][]描述的所有法则。

### 2.2 源文件

所有PHP源文件`必须`采用Unix LF (linefeed)换行符。

所有PHP源文件`必须`在文件末空一行。

只含有PHP代码的源文件中，结束标签`?>``必须`省略。

### 2.3. 代码行

行宽`禁止`硬性限制。

行宽的软性限制`必须`是120个字符; 
自动代码样式检查工具`必须`警告软性限制，但`禁止`报错。

每行内容`不该`超过80个字符; 超过80字符的代码行必须进行换行。

非空行后面`禁止`有空白（空格，制表符等）内容。

添加空白行`可以`提高可读性和表明相关代码块。

`禁止`每行多条语句。

### 2.4. 缩进

代码`必须`采用4个空格进行缩进，而`禁止`采用制表符进行缩进。

> 说明： 只采用空格，而不是空格与制表符混用，有助于避免对比（diffs），补丁（patches），
> 历史（history）和注释（annotations）产生的问题。采用空格也使得在跨行对齐中插入精细
> 的子缩进符更容易。

### 2.5. 关键词和 True/False/Null

PHP [关键词][]`必须`小写。

PHP 常量`true`，`false`和`null``必须`小写。

[关键词]: http://php.net/manual/zh/reserved.keywords.php

3. 命名空间（namespace）和引用（use）声明
-------------------

当存在`命名空间`声明，后面`必须`留空一行。

当存在`命名空间`声明，所有的`引用`声明`必须`在`命名空间`声明后面。

每个`引用`声明`必须`只有一个`use`关键字。

`引用`代码块后`必须`空一行。

例如：

```php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

// ... 其他 PHP 代码 ...

```

4. 类（Classes），属性（Properties）和方法（Methods）
-------------------------------------------------

术语"类（class）"提及所有类（classes），接口（interfaces）和特征（traits）。

### 4.1. 继承（Extends）和实现（Implements）

`extends`和`implements`关键字`必须`在类名同行进行声明。

类的`{``必须`在类名所在行; 类的`}``必须`在类体后另起一行。

```php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // 常量, 属性, 方法
}
```

`实现`列`可以`分割成多行，每行一倍缩进。
如此编写时，第一个`实现``必须`另起一行，并且每行`必须`只有一个接口（interface）。

```php
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
    // 常量, 属性, 方法
}
```

### 4.2. 属性

所有属性`必须`声明访问权限。

`var`关键字`禁止`用于声明属性。

每条语句`禁止`声明多个属性。

属性名前`不该`加单个`_`来区分 protected 或 private 访问权限。

属性声明如下：

```php
<?php
namespace Vendor\Package;

class ClassName
{
    public $foo = null;
}
```

### 4.3. 方法

所有方法`必须`声明访问权限。

方法名前`不该`加单个`_`来区分 protected 或 private 访问权限。

声明方法时，`禁止`方法名后有空格。
`{``必须`在方法名同行，并且`}``必须`在方法体后另起一行。
`(`后`禁止`有空格，并且`)`前`禁止`有空格。

方法声明如下。注意`()`,`,`,`空格`和`{}`的位置：

```php
<?php
namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [])
    {
        // 方法体
    }
}
```   

### 4.4. 方法参数

多个参数时，每个`,`前`禁止`有空格，并且每个`,`后`必须`有一个空格。

方法中带默认值的参数`必须`在参数列的末列。

```php
<?php
namespace Vendor\Package;

class ClassName
{
    public function foo($arg1, &$arg2, $arg3 = [])
    {
        // 方法体
    }
}
```

参数列`可以`分割成多行，每行一倍缩进。
如此编写时，第一个参数`必须`另起一行，并且每行`必须`只有一个参数。

当参数列分割成多行时，`)`和`{``必须`在同一行并隔一个空格。

```php
<?php
namespace Vendor\Package;

class ClassName
{
    public function aVeryLongMethodName(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    ) {
        // 方法体
    }
}
```

### 4.5.`abstract`,`final`和`static`

当存在`abstract`和`final`声明时，`必须`在访问权限声明前面。

当存在`static`声明时，`必须`在访问权限声明后面。

```php
<?php
namespace Vendor\Package;

abstract class ClassName
{
    protected static $foo;

    abstract protected function zim();

    final public static function bar()
    {
        // 方法体
    }
}
```

### 4.6. 方法（Method）和函数（Function）调用

当进行调用方法或函数时，方法或函数名与`(`之间`禁止`有空格，`(`后`禁止`有空格，
并且`)`前`禁止`有空格。
参数列中，每个`,`前`禁止`有空格，并且每个`,`后`必须`有一个空格。

```php
<?php
bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
```

参数列`可以`分割成多行，每行一倍缩进。
如此编写时，第一个参数`必须`另起一行，并且每行`必须`只有一个参数。

```php
<?php
$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
```

5. 控制结构
----------

控制结构通常的样式规则如下：

- 控制结构关键字后`必须`有一个空格。
-`(`后`禁止`有空格。
-`)`前`禁止`有空格。
-`)`和`{`间`必须`有一个空格。
- 控制结构体`必须`一倍缩进。
- 控制结构体后的`}``必须`另起一行。

每个控制结构体`必须`用`{}`封闭。
这规范了控制结构的格式，并且减少引入如在控制结构体中添加一行的错误的可能性。

### 5.1.`if`,`elseif`,`else`

`if`结构如下。注意`()`,`空格`和`{}`的位置;
并且`else`和`elseif`与前面的`}`在同一行。

```php
<?php
if ($expr1) {
    // if 结构体
} elseif ($expr2) {
    // elseif 结构体
} else {
    // else 结构体;
}
```

关键字`elseif``应该`代替`else if`以致所有的控制结构关键字看上都是单词。


### 5.2.`switch`,`case`

`switch`结构如下。注意`()`,`空格`和`{}`的位置。
`case`语句`必须`相对`switch`一倍缩进，`break`关键字（或者其他结束关键字）
`必须`与`case`体对齐缩进。
从非空`case`体故意进入后续`case`体时，`必须`有一个如`// no break`的注释。

```php
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
```


### 5.3.`while`,`do while`

`while`语句如下。注意`()`，`空格`和`{}`的位置。

```php
<?php
while ($expr) {
    // 结构体
}
```

类似地，`do while`语句如下。注意`()`，`空格`和`{}`的位置。

```php
<?php
do {
    // 结构体
} while ($expr);
```

### 5.4.`for`

`for`语句如下。注意`()`，`空格`和`{}`的位置。

```php
<?php
for ($i = 0; $i < 10; $i++) {
    // for 体
}
```

### 5.5.`foreach`

`foreach`语句如下。注意`()`，`空格`和`{}`的位置。

```php
<?php
foreach ($iterable as $key => $value) {
    // foreach 体
}
```

### 5.6.`try`,`catch`

`try catch`块如下。注意`()`，`空格`和`{}`的位置：

```php
<?php
try {
    // try 体
} catch (FirstExceptionType $e) {
    // catch 体
} catch (OtherExceptionType $e) {
    // catch 体
}
```

6. 闭包（Closures）
-----------

闭包声明时`必须`在`function`关键字后面有一个空格，
并且在`use`关键字前后各有一个空格。

`{``必须`在闭包同行，并且`}``必须`在闭包体后另起一行。

参数列或变量列的`(`后面`禁止`有空格，并且参数列或变量列的`)`前面`禁止`有空格。

参数列或变量列里，每个`,`前`禁止`有空格，并且每个`,`后`必须`有一个空格。

闭包中带默认值的参数`必须`在参数列末列。

闭包声明如下。注意`()`，`,`，`空格`和`{}`的位置：

```php
<?php
$closureWithArgs = function ($arg1, $arg2) {
    // 体内容
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // 体内容
};
```

参数列和变量列`可以`分割成多行，每行一倍缩进。
如此编写时，第一个参数或变量`必须`另起一行，并且每行`必须`只有一个参数或变量。

当结束列（两者或参数或变量）分割成多行时，`)`和`{``必须`在同一行并隔一个空格。

以下是带和不带分割多行的参数列和变量列的闭包样例：

```php
<?php
$longArgs_noVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) {
   // 体内容
};

$noArgs_longVars = function () use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // 体内容
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
   // 体内容
};

$longArgs_shortVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use ($var1) {
   // 体内容
};

$shortArgs_longVars = function ($arg) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // 体内容
};
```

注意当闭包作为参数直接用在函数或方法调用中，格式化规则也要遵守。

```php
<?php
$foo->bar(
    $arg1,
    function ($arg2) use ($var1) {
        // 体内容
    },
    $arg3
);
```