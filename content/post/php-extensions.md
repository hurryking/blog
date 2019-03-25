---
title: "如何给　PHP 添加新特性"
date: 2019-03-24T15:32:28+08:00
archives: "2019"
tags: ['php']
author: Mr King
---


### 如何给 PHP 添加新的语法特性(译)

最近有好多人问我怎么给 PHP 添加新语法特性。我仔细想了想，确实没有这方面的教程，接下来我会阐述整个流程。同时这篇文章也是对 Zend 引擎的一个简介。

我提前为这篇过长的文章道歉。

这篇文章假设你已经掌握了一些 C 的基本知识，并且了解 PHP 的一些基本概念(像 zvals 结构体)。如果你不具备这些条件，建议先去了解一下。

我将使用你可能从其他语言获知的 ``in`` 运算符作为一个例子。它表现如下:

```
$words = ['hello', 'world', 'foo', 'bar'];
var_dump('hello' in $words); // true
var_dump('foo' in $words);   // true
var_dump('blub' in $words);  // false

$string = 'PHP is fun!';
var_dump('PHP' in $string);    // true
var_dump('Python' in $string); // false
```

基本上来说，``in`` 操作符和 ``in_array`` 函数在数组中的使用一样(但是没有 needle/haystack 问题)，和字符函数 ``false != strpos($str2, $str1)`` 也类似。

#### 准备工作

在开始之前，你必须检出并编译 PHP。所以接下来我们需要安装一些工具。大部分可能都预先在系统上安装好了，但是你必须使用自己选择的包管理工具安装 "re2c" 和 “bison”。如果你用的是 Ubuntu:

```
$ sudo apt-get install re2c
$ sudo apt-get install bison
``` 

接下来，从 git 上克隆 php-src 并进行编译:

```
// 获取源码
$ git clone http://git.php.net/repository/php-src.git
$ cd php-src
// 创建新分支
$ git checkout -b addInOperator
// 构建 ./configure (预编译)脚本
$ ./buildconf
// 使用　debug　模式和　线程安全模式　预编译
$ ./configure --disable-all --enable-debug --enable-maintainer-zts
// 编译 (4 是你拥有的核心数)
$ make -j4
``` 

PHP 二进制包应该在 ``sapi/cli/php``。你可以尝试以下操作:

```
$ sapi/cli/php -v
$ sapi/cli/php -r 'echo "Hallo World!";'
```

现在你可能已经有了一个编译过的 PHP，接下来我们看下 PHP 在运行一个脚本的时候都做了哪些事。

#### PHP 脚本的生命周期

运行一个 PHP 脚本有三个主要阶段:

1. Tokenization(符号化)
2. Parsing & Compilation(解析和编译)
3. Execute(运行)

接下来我会详细解释每个阶段都在做什么，如何实现以及我们需要修改什么地方才能让 ``in`` 操作符运行。

#### 符号化

第一阶段 PHP 读取源代码，把源码切分成更小的 “token” 单元。举个例子```<?php echo "Hello World!";```会被拆解成下面的 token:

```
T_OPEN_TAG (<?php )
T_ECHO (echo)
T_WHITESPACE ( )
T_CONSTANT_ENCAPSED_STRING ("Hello World!")
';'
```

(**译者注: 这里是官方的 [token表](http://php.net/manual/zh/tokens.php)**)

如你所见原始代码被切分成具有语义的　token。处理过程被称为符号化，扫描和词法解析的实现在　``Zend`` 目录下的 ``zend_language_scanner.l`` 文件。

如果你打开文件向下滚动到差不多 1000 行(**译者注: php 8.0.0 在 1261 行**)，你会发现大量的 token 定义语句像下面这样:

```
<ST_IN_SCRIPTING>"exit" {
    return T_EXIT;
}
```

上述代码的意思很明显是: 如果在源代码中遇到了 ``exit`` ，lexer 应该标记它为 ``T_EXIT``。``<`` 和　``>``　中间的内容是文本应该被匹配的状态。
``ST_IN_SCRIPTING`` 是对　PHP　源码来说是正常状态。还有一些其他的状态像 ``ST_DOUBLE_QUOTE`` (在双引号中间)，``ST_HEREDOC`` (在　heredoc 字符串中间)，等等。

另一个可以在扫描期间做的是指定一个“语义”值(也可以称为"lower case" 或者简称"lval")。下面是例子:

```
<ST_IN_SCRIPTING,ST_VAR_OFFSET>{LABEL} {
    zend_copy_value(zendlval, yytext, yyleng);
    zendlval->type = IS_STRING;
```

``{LABEL}`` 匹配一个 PHP 标识(可以被定义为``[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*``)，代码返回　token ``T_STRING``。另外它复制　token 的文本到　``zendlval``。所以如果　lexer 遇到一个标识像 ``FooBarClass``，它将设置 ``FooBarClass`` 作为lval。字符串，数字和变量名称也一样。

幸运的是 ``in`` 操作符并不需要深层次的 lexer 知识。我们只需要添加以下代码段到文件中(与上面的 ``exit``　类似):

```
<ST_IN_SCRIPTING>"in" {
    return T_IN;
}
```

(**译者注: 新版已经不是上面的写法了**)

除此之外我们需要让引擎知道我们添加了一个新的 token。打开 ``zend_language_parser.y`` 加入下面的行在它的类似代码中(在定义操作符的代码段中):

```
%token T_IN "in (T_IN)"
```

现在你应该用 ``make -j4`` 重新编译下　PHP (必须在顶级目录 ``php-src`` 中执行，不是 ``Zend/``)。这会产生一个新由 re2c　生成的　lexer 并编译它。为了测试我们的修改是否生效。需要执行以下命令:

```
$ sapi/cli/php -r 'in'
```

这将会给出一个解析错误:
```
Parse error: syntax error, unexpected 'in' (T_IN) in Command line code on line 1
```

我们需要做的最后一件事就是使用 [Tokenizer 扩展](https://secure.php.net/tokenizer)　重新生成数据，你需要使用 ``cd`` 进入 ``ext/tokenizer`` 目录并且执行 ``./tokenizer_data_gen.sh``。

如果你运行 git diff --stat，你会看见下面的信息:

```
Zend/zend_language_parser.y       |    1 +
Zend/zend_language_scanner.c      | 1765 +++++++++++++++++++------------------
Zend/zend_language_scanner.l      |    4 +
Zend/zend_language_scanner_defs.h |    2 +-
ext/tokenizer/tokenizer_data.c    |    4 +-
5 files changed, 904 insertions(+), 872 deletions(-)
```

`` zend_language_scanner.c`` 内容的变更是 re2C 重新生成的 lexer。因为它包含了行号信息，每个对 lexer 的改变都会产生巨大的不同。所以不用担心;)

#### 解析和执行

目前为止源码已经被分解成有含义的 token，PHP已经可以识别更大的结构像"this is an ``if`` block"或者"you are defining function here"。这个过程被称为解析，规则被定义在 ``zend_language_parser.y`` 文件中。这只是一个定义文件，真正的解析器还是由　bison　生成的。

为了了解解析器的定义是如何运行的，我们来看个例子:

```
class_statement:
        variable_modifiers { CG(access_type) = Z_LVAL($1.u.constant); } class_variable_declaration ';'
    |   class_constant_declaration ';'
    |   trait_use_statement
    |   method_modifiers function is_reference T_STRING { zend_do_begin_function_declaration(&$2, &$4, 1, $3.op_type, &$1 TSRMLS_CC); } '('
           parameter_list ')' method_body { zend_do_abstract_method(&$4, &$1, &$9 TSRMLS_CC); zend_do_end_function_declaration(&$2 TSRMLS_CC); }
;
```

我们把花括号中的内容去掉，剩下的内容如下:

```
class_statement:
        variable_modifiers class_variable_declaration ';'
    |   class_constant_declaration ';'
    |   trait_use_statement
    |   method_modifiers function is_reference T_STRING '(' parameter_list ')' method_body
;
```

你可以这样解读:
```
A class statement is
        a variable declaration (with access modifier)
    or  a class constant declaration
    or  a trait use statement
    or  a method (with method modifier, optional return-by-ref, method name, parameter list and method body)
```

想知道什么是“methid modifer”，你需要去看　```method_modifier``` 的定义。这就相当直白了。

为了让解析器支持 ```in```，我们需要把 ```expr T_IN expr``` 规则加到　```expr_without_variable```　里面:

```
expr_without_variable:
    ...
    |   expr T_IN expr
    ...
;
```

如果你运行 ```make -j4```，bison 会尝试重新构建解析器，但是会报以下的错误:

```
conflicts: 87 shift/reduce
/some/path/php-src/Zend/zend_language_parser.y: expected 3 shift/reduce conflicts
make: *** [/some/path/php-src/Zend/zend_language_parser.c] Error 1
```

shift/reduce 意思是解析器在某些情况下不知道怎么去做。PHP 语法有 3 个　shift/reduce 自相矛盾的冲突。其余的 84 个冲突是因为新规则造成的。　
　

#### Conclusion

未完待续

[原文链接](http://nikic.github.io/2012/07/27/How-to-add-new-syntactic-features-to-PHP.html#the-life-of-a-php-script)

> 沧海一声笑 
滔滔两岸潮
浮沉随浪 
只记今朝