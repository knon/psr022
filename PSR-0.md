<h1 id="PSR-0">PSR-0 自动加载</h1>

`ecdo.cc® 2012 - 2013©`

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