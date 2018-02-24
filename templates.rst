模板设计师篇
===========================

本文档介绍模板引擎的语法和语义，对创建 Twig 模板的人来说这是最有用的参考。

概要
--------

模板是一个简单的纯文本文件。 它可以是任意的文本格式（HTML, XML, CSV, LaTeX, 等等 ）。
它没有特定的扩展名，``.html`` 或者``.xml`` 都行。

一个模板包含 **变量variables** 或者 **表达式expressions**, 这些在评估模板时都会被替换,
以及控制着模板逻辑的**标签tags**。

下面是一个简单的模板。后面我们会介绍更多细节：

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head>
            <title>My Webpage</title>
        </head>
        <body>
            <ul id="navigation">
            {% for item in navigation %}
                <li><a href="{{ item.href }}">{{ item.caption }}</a></li>
            {% endfor %}
            </ul>

            <h1>My Webpage</h1>
            {{ a_variable }}
        </body>
    </html>

有两种形式的分隔符: ``{% ... %}`` 和 ``{{ ... }}``.第一个用于执行语句，比如 for 循环，
后者用于将表达式的结果输出到模板里。

IDEs 集成
----------------

很多IDE都支持Twig的语法高亮和自动完成

* *Textmate* 通过 `Twig bundle`_
* *Vim* 通过 `Jinja syntax plugin`_ 或者 `vim-twig plugin`_
* *Netbeans* 通过 `Twig syntax plugin`_ (until 7.1, native as of 7.2)
* *PhpStorm* (自2.1起)
* *Eclipse* 通过 `Twig plugin`_
* *Sublime Text* 通过 `Twig bundle`_
* *GtkSourceView* 通过 `Twig language definition`_ (used by gedit and other projects)
* *Coda* 和 *SubEthaEdit* 通过 `Twig syntax mode`_
* *Coda 2* 通过 `other Twig syntax mode`_
* *Komodo* 和 *Komodo Edit* 通过 Twig highlight/syntax check mode
* *Notepad++* 通过 `Notepad++ Twig Highlighter`_
* *Emacs* 通过 `web-mode.el`_
* *Atom* 通过 `PHP-twig for atom`_
* *Visual Studio Code* 通过 `Twig pack`_

此外, `TwigFiddle`_ 是一个在线服务允许你在浏览器里执行 Twig 的模板；它支持 Twig 的所有版本。

变量
---------

应用程序传递变量到模板里来进行处理。变量可以有很多可以访问的属性或者元素。变量的可视化表示在
很大程度上取决于提供变量的应用程序。

你可以使用点号 (``.``) 来访问变量的属性 (方法或者 PHP 对象的属性，又或者 PHP 数组元素,也可以使用
所谓的 “标 subscript” 语法(``[]``):

.. code-block:: jinja

    {{ foo.bar }}
    {{ foo['bar'] }}

当属性包含特殊字符时 (比如 ``-`` 将被解析为减号), 使用 ``attribute`` 函数代替去访问变量的属性:

.. code-block:: jinja

    {# equivalent to the non-working foo.data-foo #}
    {{ attribute(foo, 'data-foo') }}

.. note::

    你必须知道花括号并*不是* 变量的一部分，只是一个打印的声明。当在标签内访问变量的时候，不要将其放在
    花括号中。

如果一个变量或者属性不存在，当  ``strict_variables`` 选项被设置为 ``false`` 时你会收到 ``null`` 值。
另外，如果 ``strict_variables`` 被设置了，Twig将抛出一个错误(查看 :ref:`环境选项<environment_options>`)。

.. sidebar:: 实现

    为了方便 ``foo.bar`` 在 PHP 层做了这些事情:

    * 检查 ``foo`` 是否是数组， ``bar`` 是不是有效的元素;
    * 如果不是, 检查 ``foo`` 是否是对象, 检查 ``bar`` 是不是有效属性;
    * 如果不是, 检查 ``foo`` 是否是对象, 检查 ``bar`` 是不是有效方法
      (即使 ``bar`` 是构造函数 - 使用 ``__construct()`` 代替);
    * 如果不是, 检查 ``foo`` 是否是对象, 检查 ``getBar`` 是不是有效方法;
    * 如果不是, 检查 ``foo`` 是否是对象, 检查 ``isBar`` 是不是有效方法;
    * 如果不是, 检查 ``foo`` 是否是对象, 检查 ``hasBar`` 是不是有效方法;
    * 如果不是, 返回 ``null`` 值。

    ``foo['bar']`` 在另一方面值适用于PHP 数组:

    *检查 ``foo`` 是否是数组， ``bar`` 是不是有效的元素;
    * 如果不是, 返回 ``null`` 值。

.. note::

    如果你想访问变量的动态属性, 使用 :doc:`attribute<functions/attribute>` 函数代替.

全局变量
~~~~~~~~~~~~~~~~

以下变量在模板中始终可用:

* ``_self``: 引用当前模板;
* ``_context``: 引用当前上下文;
* ``_charset``: 引用当前字符集。

设置变量
~~~~~~~~~~~~~~~~~

你可以在代码块里为变量赋值，使用 :doc:`set<tags/set>` 标签:

.. code-block:: jinja

    {% set foo = 'foo' %}
    {% set foo = [1, 2] %}
    {% set foo = {'foo': 'bar'} %}

过滤器
-------

变量乐意通过 **filters** 进行修改. 过滤器通过竖线(``|``)与变量分割开来,可能有多个可选参数，写在
括号里即可。 多个过滤器可以串联在一齐。一个过滤器的输出结果将应用为下一个过滤器。

下面的例子展示了移除 ``name`` 中所有的 html 标签并将单词首字母大写:

.. code-block:: jinja

    {{ name|striptags|title }}

过滤器可以在括号里接收参数。下面的例子展示了怎么样用逗号连接一个列表:

.. code-block:: jinja

    {{ list|join(', ') }}

要在一段代码中应用过滤器的话，你需要把它们包裹在标签 :doc:`filter<tags/filter>` 里:

.. code-block:: jinja

    {% filter upper %}
        This text becomes uppercase
    {% endfilter %}

查看 :doc:`filters<filters/index>` 章节了解更多内建的过滤器。

函数
---------

函数可以用于生产一段内容。通过函数名进行调用，紧跟括号(``()``)，如果有参数则在其中设置参数。


例如, 函数 ``range`` 返回一个包含整数运算级数的列表:

.. code-block:: jinja

    {% for i in range(0, 3) %}
        {{ i }},
    {% endfor %}

参考 :doc:`functions<functions/index>` 页面了解更多内建函数。

命名参数
---------------

.. code-block:: jinja

    {% for i in range(low=1, high=10, step=2) %}
        {{ i }},
    {% endfor %}

使用命名参数可以将你的模板中的传递的参数的意义变得更加清晰：

.. code-block:: jinja

    {{ data|convert_encoding('UTF-8', 'iso-2022-jp') }}

    {# versus #}

    {{ data|convert_encoding(from='iso-2022-jp', to='UTF-8') }}

命名参数也允许你跳过一些你不想改变默认值的参数：

.. code-block:: jinja

    {# 第一个参数是日期格式（format），如果传递的是空值，它将是默认的全局日期格式 #}
    {{ "now"|date(null, "Europe/Paris") }}

    {# 或者通过为时区使用一个命名实参来跳过日期格式（format） #}
    {{ "now"|date(timezone="Europe/Paris") }}

你也可以在一次调用中同时使用位置和命名参数。这种情况下位置参数必须一致在命名参数
的前面:

.. code-block:: jinja

    {{ "now"|date('d/m/Y H:i', timezone="Europe/Paris") }}

.. tip::

    每个函数和过滤器的文档页面都有一节专门介绍他们支持的所有的参数。

控制结构
-----------------

控制结构是指所有的控制程序流程的东西 - 条件语句 (例如：``if``/``elseif``/``else``)
``for`` 循环以及程序块。控制结构必须放置在  ``{% ... %}`` 块中。

例如，要显示变量``users`` 中提供的所有的用户。使用 :doc:`for<tags/for>` 标签:

.. code-block:: jinja

    <h1>Members</h1>
    <ul>
        {% for user in users %}
            <li>{{ user.username|e }}</li>
        {% endfor %}
    </ul>

:doc:`if<tags/if>` 标签可以用来测试一段表达式:

.. code-block:: jinja

    {% if users|length > 0 %}
        <ul>
            {% for user in users %}
                <li>{{ user.username|e }}</li>
            {% endfor %}
        </ul>
    {% endif %}

参阅 :doc:`tags<tags/index>` 页面了解更多内置的标签

注释
--------

要注释模板中的一行或多行请使用注释语法  ``{# ... #}``。在调试或者给其它开发者也或你自己
添加注释信息时非常有用:

.. code-block:: jinja

    {# note: disabled template because we no longer use this
        {% for user in users %}
            ...
        {% endfor %}
    #}

引入其它模板
-------------------------

:doc:`include<functions/include>` 函数用于引入一个模板并返回那个模板的渲染结果到当前模板中:

.. code-block:: jinja

    {{ include('sidebar.html') }}

默认情况下，被引入的模板可以访问引入它们的模板的上下文(context)。这表示主模板中定义的任意变量在
被引入的模板中也是可用的：

.. code-block:: jinja

    {% for box in boxes %}
        {{ include('render_box.html') }}
    {% endfor %}

被引入的模板 ``render_box.html`` 可以访问 ``box`` 变量。

传参的模板文件名取决于你设置的模板加载器。例如，``Twig_Loader_Filesystem`` 加载器允许你通过给定
的文件名访问其它模板。你可以使用斜线来访问子目录内的模板：

.. code-block:: jinja

    {{ include('sections/articles/sidebar.html') }}

这种行为取决于内嵌 Twig 的应用。

模板继承
--------------------

Twig 最强大的一部分就是模板集成。模板集成可以让你建立一个包含你网站所有通用元素的 "骨架（skeleton）" 
模板。定义 **blocks** ，这允许你在子模板里覆盖。

听起来很复杂但用起来很简单。通过一个例子会更直白点。

假设我们定义了一个基本模板 ``base.html``，它是一个简单的 HTMl 骨架文件，你可以在一个简单的两栏页面上
用到它:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
        <head>
            {% block head %}
                <link rel="stylesheet" href="style.css" />
                <title>{% block title %}{% endblock %} - My Webpage</title>
            {% endblock %}
        </head>
        <body>
            <div id="content">{% block content %}{% endblock %}</div>
            <div id="footer">
                {% block footer %}
                    &copy; Copyright 2011 by <a href="http://domain.invalid/">you</a>.
                {% endblock %}
            </div>
        </body>
    </html>

这个例子中定义了四个标签 :doc:`block<tags/block>`，可以在子模板中填充。所有的 ``block`` 标签都是
用来告诉模板引擎这部分内容可以让子模板覆写。

子模板大概是这个样子:

.. code-block:: jinja

    {% extends "base.html" %}

    {% block title %}Index{% endblock %}
    {% block head %}
        {{ parent() }}
        <style type="text/css">
            .important { color: #336699; }
        </style>
    {% endblock %}
    {% block content %}
        <h1>Index</h1>
        <p class="important">
            Welcome to my awesome homepage.
        </p>
    {% endblock %}

:doc:`extends<tags/extends>` 标签在这里很关键. 它用来告诉模板引擎这个模板 "继承extends"
了其它模板。当模板系统评估到这个模板的时候回首先定位到父模板。继承标签必须是模板的第一个标签。

注意子模板没有定义 ``footer`` 块，所以这部分内容会使用父模板里的对应的块的内容代替。

可以使用 :doc:`parent<functions/parent>` 函数来渲染父级块，这回返回父级块的渲染结果:

.. code-block:: jinja

    {% block sidebar %}
        <h3>Table Of Contents</h3>
        ...
        {{ parent() }}
    {% endblock %}

.. 提示::

    在 :doc:`extends<tags/extends>` 标签的文档页面描述了更多高级特性，像 块嵌套，范围，动态继承
    以及条件继承等。

.. 注意::

    在 :doc:`use<tags/use>` 标签的帮助下，Twig也支持多继承以及所谓的横向重用（horizontal reuse）
    这是个高级特性，在一般模板里很难用的到。

HTML 转移
-------------

当从模板生成 HTML 的时候，一直存在一种风险，某个变量里包含了会影响 HTMl 结果的字符。有两种方法可以避免：
手动转移每个变量或者默认自动地转移全部变量。

Twig 支持两种方式, 默认启用自动转义。

自动转义策略可以通过配置 :ref:`autoescape<environment_options>` 选项来开启。默认是 ``html``。


Working with Manual Escaping
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If manual escaping is enabled, it is **your** responsibility to escape
variables if needed. What to escape? Any variable you don't trust.

Escaping works by piping the variable through the
:doc:`escape<filters/escape>` or ``e`` filter:

.. code-block:: jinja

    {{ user.username|e }}

By default, the ``escape`` filter uses the ``html`` strategy, but depending on
the escaping context, you might want to explicitly use any other available
strategies:

.. code-block:: jinja

    {{ user.username|e('js') }}
    {{ user.username|e('css') }}
    {{ user.username|e('url') }}
    {{ user.username|e('html_attr') }}

Working with Automatic Escaping
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Whether automatic escaping is enabled or not, you can mark a section of a
template to be escaped or not by using the :doc:`autoescape<tags/autoescape>`
tag:

.. code-block:: jinja

    {% autoescape %}
        Everything will be automatically escaped in this block (using the HTML strategy)
    {% endautoescape %}

By default, auto-escaping uses the ``html`` escaping strategy. If you output
variables in other contexts, you need to explicitly escape them with the
appropriate escaping strategy:

.. code-block:: jinja

    {% autoescape 'js' %}
        Everything will be automatically escaped in this block (using the JS strategy)
    {% endautoescape %}

Escaping
--------

It is sometimes desirable or even necessary to have Twig ignore parts it would
otherwise handle as variables or blocks. For example if the default syntax is
used and you want to use ``{{`` as raw string in the template and not start a
variable you have to use a trick.

The easiest way is to output the variable delimiter (``{{``) by using a variable
expression:

.. code-block:: jinja

    {{ '{{' }}

For bigger sections it makes sense to mark a block
:doc:`verbatim<tags/verbatim>`.

Macros
------

Macros are comparable with functions in regular programming languages. They
are useful to reuse often used HTML fragments to not repeat yourself.

A macro is defined via the :doc:`macro<tags/macro>` tag. Here is a small example
(subsequently called ``forms.html``) of a macro that renders a form element:

.. code-block:: jinja

    {% macro input(name, value, type, size) %}
        <input type="{{ type|default('text') }}" name="{{ name }}" value="{{ value|e }}" size="{{ size|default(20) }}" />
    {% endmacro %}

Macros can be defined in any template, and need to be "imported" via the
:doc:`import<tags/import>` tag before being used:

.. code-block:: jinja

    {% import "forms.html" as forms %}

    <p>{{ forms.input('username') }}</p>

Alternatively, you can import individual macro names from a template into the
current namespace via the :doc:`from<tags/from>` tag and optionally alias them:

.. code-block:: jinja

    {% from 'forms.html' import input as input_field %}

    <dl>
        <dt>Username</dt>
        <dd>{{ input_field('username') }}</dd>
        <dt>Password</dt>
        <dd>{{ input_field('password', '', 'password') }}</dd>
    </dl>

A default value can also be defined for macro arguments when not provided in a
macro call:

.. code-block:: jinja

    {% macro input(name, value = "", type = "text", size = 20) %}
        <input type="{{ type }}" name="{{ name }}" value="{{ value|e }}" size="{{ size }}" />
    {% endmacro %}

If extra positional arguments are passed to a macro call, they end up in the
special ``varargs`` variable as a list of values.

.. _twig-expressions:

Expressions
-----------

Twig allows expressions everywhere. These work very similar to regular PHP and
even if you're not working with PHP you should feel comfortable with it.

.. note::

    The operator precedence is as follows, with the lowest-precedence
    operators listed first: ``b-and``, ``b-xor``, ``b-or``, ``or``, ``and``,
    ``==``, ``!=``, ``<``, ``>``, ``>=``, ``<=``, ``in``, ``matches``,
    ``starts with``, ``ends with``, ``..``, ``+``, ``-``, ``~``, ``*``, ``/``,
    ``//``, ``%``, ``is``, ``**``, ``|``, ``[]``, and ``.``:

    .. code-block:: jinja

        {% set greeting = 'Hello ' %}
        {% set name = 'Fabien' %}

        {{ greeting ~ name|lower }}   {# Hello fabien #}

        {# use parenthesis to change precedence #}
        {{ (greeting ~ name)|lower }} {# hello fabien #}

Literals
~~~~~~~~

The simplest form of expressions are literals. Literals are representations
for PHP types such as strings, numbers, and arrays. The following literals
exist:

* ``"Hello World"``: Everything between two double or single quotes is a
  string. They are useful whenever you need a string in the template (for
  example as arguments to function calls, filters or just to extend or include
  a template). A string can contain a delimiter if it is preceded by a
  backslash (``\``) -- like in ``'It\'s good'``. If the string contains a
  backslash (e.g. ``'c:\Program Files'``) escape it by doubling it
  (e.g. ``'c:\\Program Files'``).

* ``42`` / ``42.23``: Integers and floating point numbers are created by just
  writing the number down. If a dot is present the number is a float,
  otherwise an integer.

* ``["foo", "bar"]``: Arrays are defined by a sequence of expressions
  separated by a comma (``,``) and wrapped with squared brackets (``[]``).

* ``{"foo": "bar"}``: Hashes are defined by a list of keys and values
  separated by a comma (``,``) and wrapped with curly braces (``{}``):

  .. code-block:: jinja

    {# keys as string #}
    { 'foo': 'foo', 'bar': 'bar' }

    {# keys as names (equivalent to the previous hash) #}
    { foo: 'foo', bar: 'bar' }

    {# keys as integer #}
    { 2: 'foo', 4: 'bar' }

    {# keys as expressions (the expression must be enclosed into parentheses) #}
    {% set foo = 'foo' %}
    { (foo): 'foo', (1 + 1): 'bar', (foo ~ 'b'): 'baz' }

* ``true`` / ``false``: ``true`` represents the true value, ``false``
  represents the false value.

* ``null``: ``null`` represents no specific value. This is the value returned
  when a variable does not exist. ``none`` is an alias for ``null``.

Arrays and hashes can be nested:

.. code-block:: jinja

    {% set foo = [1, {"foo": "bar"}] %}

.. tip::

    Using double-quoted or single-quoted strings has no impact on performance
    but string interpolation is only supported in double-quoted strings.

Math
~~~~

Twig allows you to calculate with values. This is rarely useful in templates
but exists for completeness' sake. The following operators are supported:

* ``+``: Adds two objects together (the operands are casted to numbers). ``{{
  1 + 1 }}`` is ``2``.

* ``-``: Subtracts the second number from the first one. ``{{ 3 - 2 }}`` is
  ``1``.

* ``/``: Divides two numbers. The returned value will be a floating point
  number. ``{{ 1 / 2 }}`` is ``{{ 0.5 }}``.

* ``%``: Calculates the remainder of an integer division. ``{{ 11 % 7 }}`` is
  ``4``.

* ``//``: Divides two numbers and returns the floored integer result. ``{{ 20
  // 7 }}`` is ``2``, ``{{ -20  // 7 }}`` is ``-3`` (this is just syntactic
  sugar for the :doc:`round<filters/round>` filter).

* ``*``: Multiplies the left operand with the right one. ``{{ 2 * 2 }}`` would
  return ``4``.

* ``**``: Raises the left operand to the power of the right operand. ``{{ 2 **
  3 }}`` would return ``8``.

Logic
~~~~~

You can combine multiple expressions with the following operators:

* ``and``: Returns true if the left and the right operands are both true.

* ``or``: Returns true if the left or the right operand is true.

* ``not``: Negates a statement.

* ``(expr)``: Groups an expression.

.. note::

    Twig also support bitwise operators (``b-and``, ``b-xor``, and ``b-or``).

.. note::

    Operators are case sensitive.

Comparisons
~~~~~~~~~~~

The following comparison operators are supported in any expression: ``==``,
``!=``, ``<``, ``>``, ``>=``, and ``<=``.

You can also check if a string ``starts with`` or ``ends with`` another
string:

.. code-block:: jinja

    {% if 'Fabien' starts with 'F' %}
    {% endif %}

    {% if 'Fabien' ends with 'n' %}
    {% endif %}

.. note::

    For complex string comparisons, the ``matches`` operator allows you to use
    `regular expressions`_:

    .. code-block:: jinja

        {% if phone matches '/^[\\d\\.]+$/' %}
        {% endif %}

Containment Operator
~~~~~~~~~~~~~~~~~~~~

The ``in`` operator performs containment test.

It returns ``true`` if the left operand is contained in the right:

.. code-block:: jinja

    {# returns true #}

    {{ 1 in [1, 2, 3] }}

    {{ 'cd' in 'abcde' }}

.. tip::

    You can use this filter to perform a containment test on strings, arrays,
    or objects implementing the ``Traversable`` interface.

To perform a negative test, use the ``not in`` operator:

.. code-block:: jinja

    {% if 1 not in [1, 2, 3] %}

    {# is equivalent to #}
    {% if not (1 in [1, 2, 3]) %}

Test Operator
~~~~~~~~~~~~~

The ``is`` operator performs tests. Tests can be used to test a variable against
a common expression. The right operand is name of the test:

.. code-block:: jinja

    {# find out if a variable is odd #}

    {{ name is odd }}

Tests can accept arguments too:

.. code-block:: jinja

    {% if post.status is constant('Post::PUBLISHED') %}

Tests can be negated by using the ``is not`` operator:

.. code-block:: jinja

    {% if post.status is not constant('Post::PUBLISHED') %}

    {# is equivalent to #}
    {% if not (post.status is constant('Post::PUBLISHED')) %}

Go to the :doc:`tests<tests/index>` page to learn more about the built-in
tests.

Other Operators
~~~~~~~~~~~~~~~

The following operators don't fit into any of the other categories:

* ``|``: Applies a filter.

* ``..``: Creates a sequence based on the operand before and after the operator
  (this is just syntactic sugar for the :doc:`range<functions/range>` function):

  .. code-block:: jinja

      {{ 1..5 }}

      {# equivalent to #}
      {{ range(1, 5) }}

  Note that you must use parentheses when combining it with the filter operator
  due to the :ref:`operator precedence rules <twig-expressions>`:

  .. code-block:: jinja

      (1..5)|join(', ')

* ``~``: Converts all operands into strings and concatenates them. ``{{ "Hello
  " ~ name ~ "!" }}`` would return (assuming ``name`` is ``'John'``) ``Hello
  John!``.

* ``.``, ``[]``: Gets an attribute of an object.

* ``?:``: The ternary operator:

  .. code-block:: jinja

      {{ foo ? 'yes' : 'no' }}
      {{ foo ?: 'no' }} is the same as {{ foo ? foo : 'no' }}
      {{ foo ? 'yes' }} is the same as {{ foo ? 'yes' : '' }}

* ``??``: The null-coalescing operator:

  .. code-block:: jinja

      {# returns the value of foo if it is defined and not null, 'no' otherwise #}
      {{ foo ?? 'no' }}

String Interpolation
~~~~~~~~~~~~~~~~~~~~

String interpolation (``#{expression}``) allows any valid expression to appear
within a *double-quoted string*. The result of evaluating that expression is
inserted into the string:

.. code-block:: jinja

    {{ "foo #{bar} baz" }}
    {{ "foo #{1 + 2} baz" }}

.. _templates-whitespace-control:

Whitespace Control
------------------

The first newline after a template tag is removed automatically (like in PHP.)
Whitespace is not further modified by the template engine, so each whitespace
(spaces, tabs, newlines etc.) is returned unchanged.

Use the ``spaceless`` tag to remove whitespace *between HTML tags*:

.. code-block:: jinja

    {% spaceless %}
        <div>
            <strong>foo bar</strong>
        </div>
    {% endspaceless %}

    {# output will be <div><strong>foo bar</strong></div> #}

In addition to the spaceless tag you can also control whitespace on a per tag
level. By using the whitespace control modifier on your tags, you can trim
leading and or trailing whitespace:

.. code-block:: jinja

    {% set value = 'no spaces' %}
    {#- No leading/trailing whitespace -#}
    {%- if true -%}
        {{- value -}}
    {%- endif -%}

    {# output 'no spaces' #}

The above sample shows the default whitespace control modifier, and how you can
use it to remove whitespace around tags. Trimming space will consume all whitespace
for that side of the tag.  It is possible to use whitespace trimming on one side
of a tag:

.. code-block:: jinja

    {% set value = 'no spaces' %}
    <li>    {{- value }}    </li>

    {# outputs '<li>no spaces    </li>' #}

Extensions
----------

Twig can be easily extended.

If you are looking for new tags, filters, or functions, have a look at the Twig official
`extension repository`_.

If you want to create your own, read the :ref:`Creating an
Extension<creating_extensions>` chapter.

.. _`Twig bundle`:                https://github.com/Anomareh/PHP-Twig.tmbundle
.. _`Jinja syntax plugin`:        http://jinja.pocoo.org/docs/integration/#vim
.. _`vim-twig plugin`:            https://github.com/lumiliet/vim-twig
.. _`Twig syntax plugin`:         http://plugins.netbeans.org/plugin/37069/php-twig
.. _`Twig plugin`:                https://github.com/pulse00/Twig-Eclipse-Plugin
.. _`Twig language definition`:   https://github.com/gabrielcorpse/gedit-twig-template-language
.. _`extension repository`:       http://github.com/twigphp/Twig-extensions
.. _`Twig syntax mode`:           https://github.com/bobthecow/Twig-HTML.mode
.. _`other Twig syntax mode`:     https://github.com/muxx/Twig-HTML.mode
.. _`Notepad++ Twig Highlighter`: https://github.com/Banane9/notepadplusplus-twig
.. _`web-mode.el`:                http://web-mode.org/
.. _`regular expressions`:        http://php.net/manual/en/pcre.pattern.php
.. _`PHP-twig for atom`:          https://github.com/reesef/php-twig
.. _`TwigFiddle`:                 http://twigfiddle.com/
.. _`Twig pack`:                  https://marketplace.visualstudio.com/items?itemName=bajdzis.vscode-twig-pack
