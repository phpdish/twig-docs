介绍
============

Twig 是一个灵活，快速，并且安全的 PHP 模板引擎。

如果你接触过其它的基于文本的模板引擎，例如 Smarty, Django, 或者 Jinja，那么你应该对
Twig 感到亲切。由于坚持PHP的原则，为模板环境添加有用的功能，所以对模板设计师和开发人员
都是非常友善的。


关键功能有...

* *快速*:Twig将模板编译为纯粹的，最优化的PHP代码。它的开销与常规的PHP代码相比，
已经降到了极低。

* *安全*: Twig 有一个沙盒模板来评估不受信任的模板代码。这使得Twig可以用于允许用户自行修改
模板设计的应用程序中。

* *灵活*: Twig采用灵活的词法分析器和解析器. 这允许开发者定义自己的标签以及过滤器并创建自己的
DSL

Twig已被用于许多开源项目，比如Symfony, Drupal8, eZPublish,phpBB, Piwik, OroCRM；并且许多框架也支持它，
例如Slim, Yii, Laravel, Codeigniter and Kohana — 就举这几个例子。


要求
-------------

Twig 需要 **PHP 7.0.0** 以上才能运行（2.x以上，1.x不需要）.

安装
------------

推荐使用 Composer 来安装 Twig:

.. code-block:: bash

    composer require "twig/twig:^2.0"

.. note::

    想要了解更多安装方式, 阅读
    :doc:`installation<installation>` 一节; 它还解释了如何安装Twig C扩展。

基本 API Us用法age
---------------

本节将简要介绍Twig的PHP API。

.. code-block:: php

    require_once '/path/to/vendor/autoload.php';

    $loader = new Twig_Loader_Array(array(
        'index' => 'Hello {{ name }}!',
    ));
    $twig = new Twig_Environment($loader);

    echo $twig->render('index', array('name' => 'Fabien'));

Twig 使用加载器（loader） (``Twig_Loader_Array``) 来定位模板, 使用环境（environment） 
(``Twig_Environment``) 来保存配置.

 ``render()`` 方法第一个参数传递模板，第二个参数传递变量。

由于模板文件通常存在文件系统中，Twig 因此提供了一个文件系统加载器::

    $loader = new Twig_Loader_Filesystem('/path/to/templates');
    $twig = new Twig_Environment($loader, array(
        'cache' => '/path/to/compilation_cache',
    ));

    echo $twig->render('index.html', array('name' => 'Fabien'));
