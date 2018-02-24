![file](https://www.phpdish.com/media/uploads/2018/0223/12c1dfc6ce6629502c88bbbce674a5a1.png)
Twig 作为一门优雅灵活的模板引擎诞生已久，但从国内的使用率来看并不容乐观；为了帮助更多的 twig 的爱好者，希望大家可以参与到文档的翻译中来；

## 翻译准备


Twig 的文档是采用 reStructuredText 所以你需要简单的了解下 reStructuredText 的语法；

### 语法参考：

- [reStructuredText(rst)快速入门语法说明](https://www.jianshu.com/p/1885d5570b37)
- [Sphinx 使用手册 - reStructuredText 简介](http://zh-sphinx-doc.readthedocs.io/en/latest/contents.html)


### 下载编辑器

理论上任意纯文本编辑器皆可，这里推荐 [Visual Studio Code](https://code.visualstudio.com/) 搭配 reStructuredText 扩展进行翻译。



## 开始翻译

### fork 翻译工程

项目地址 [phpdish/twig-docs](https://github.com/phpdish/twig-docs)

### 认领翻译

由于翻译文件过于零碎，本次翻译时对文件进行了组合，具体的组合结果如下

| 名称 | 包含 | 认领 |
| --- | --- | --- |
| 介绍/安装 | [Introduction](https://github.com/phpdish/twig-docs/blob/master/intro.rst)，[Installation](https://github.com/phpdish/twig-docs/blob/master/installation.rst) | [Slince](https://www.phpdish.com/users/slince)|
|模板设计师篇|[Twig for Template Designers](https://github.com/phpdish/twig-docs/blob/master/templates.rst)| [Slince](https://www.phpdish.com/users/slince)|
|开发者篇|[Twig for Developers](https://github.com/phpdish/twig-docs/blob/master/api.rst)|  [TonyGao](https://www.phpdish.com/users/TonyGao)  |
|扩展 Twig|[Extending Twig](https://github.com/phpdish/twig-docs/blob/master/advanced.rst)| [TonyGao](https://www.phpdish.com/users/TonyGao) |
|Twig 内部实现/废弃功能|[Twig Internals](https://github.com/phpdish/twig-docs/blob/master/internals.rst), [Deprecated Features](https://github.com/phpdish/twig-docs/blob/master/deprecated.rst)|  - |
|Twig技巧/代码规范|[Twig Recipes](https://github.com/phpdish/twig-docs/blob/master/recipes.rst)， [Coding Standards](https://github.com/phpdish/twig-docs/blob/master/coding_standards.rst)| - |
|标签<一>|autoescape, block, do, embed| - |
|标签<二>|extends, filter, flush, for| - |
|标签<三>|from, if, import, include| - |
|标签<四>|macro, sandbox, set, spaceless| - |
|标签<五>|use, verbatim| - |
|过滤器<一>|abs, batch, capitalize, convert_encoding, date, date_modify, default| - |
|过滤器<二>|escape, first, format, join, json_encode, keys, last| - |
|过滤器<三>|length, lower, merge, nl2br, number_format, raw, replace| - |
|过滤器<四>|reverse, round, slice, sort, split, striptags, title, trim, upper, url_encode| - |
|函数<一>|attribute, block, constant, cycle, date, dump, include| - |
|函数<二>|max, min, parent, random, range, source, template_from_string| - |
|测试|constant, defined, divisible by, empty, even, iterable, null, odd, same as| - |


> 由于标签与过滤器翻译内容过多故分开成多个部分；

参与者请到原帖下回复 “[Twig 2.x 文档翻译工作召集](https://www.phpdish.com/topics/49)”

```
我要认领“名称”
```

比如我要认领第一节则回复

```
我要认领“介绍/安装”
```

每个人可以认领一个或者多个模块，由自己时间精力自行决定，但一旦认领完成请务必完成翻译工作。


认领模块翻译完成之后请PR到[phpdish/twig-docs](https://github.com/phpdish/twig-docs)；人人都可以参与翻译的review工作；review
完成之后将会被合并到主库。

### 翻译要求

- 所有出现的函数名，标签名，过滤器，测试均不要翻译
- 双引号使用中文状态下的双引号
- 英文中的“.”翻译成句号
- 英文单词前后空一格
- 翻译完成之后请自行review，不可有模棱两可的翻译。

感谢大家的参与，我们的愿景是将国外的优秀作品翻译过来，帮助国内的开发者更轻松的学习，Twig 只是第一步；
开源社区需要我们共同维护。

文档翻译之后将建立 Twig 中文站，与 https://twig.symfony.com/ 风格一致。