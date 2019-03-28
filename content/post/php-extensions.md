---
title: "如何给 PHP 添加新特性"
date: 2019-03-24T15:32:28+08:00
archives: "2019"
tags: ['php']
author: Mr King
---


### 如何给 PHP 添加新的语法特性(译)

最近有好多人问我怎么给 PHP 添加新语法特性。我仔细想了想，确实没有这方面的教程，接下来我会阐述整个流程。同时这篇文章也是对 Zend 引擎的一个简介。

我提前为这篇过长的文章道歉。

这篇文章假设你已经掌握了一些 C 的基本知识，并且了解 PHP 的一些基本概念(像 zvals 结构体)。如果你不具备这些条件，建议先去了解一下。

我将使用你可能从其他语言获知的 `in` 运算符作为一个例子。它表现如下:

```
$words = ['hello', 'world', 'foo', 'bar'];
var_dump('hello' in $words); // true
var_dump('foo' in $words);   // true
var_dump('blub' in $words);  // false

$string = 'PHP is fun!';
var_dump('PHP' in $string);    // true
var_dump('Python' in $string); // false
```

基本上来说，`in` 操作符和 `in_array` 函数在数组中的使用一样(但是没有 needle/haystack 问题)，和字符函数 `false != strpos($str2, $str1)` 也类似。

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

PHP 二进制包应该在 `sapi/cli/php`。你可以尝试以下操作:

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

接下来我会详细解释每个阶段都在做什么，如何实现以及我们需要修改什么地方才能让 `in` 操作符运行。

#### 符号化

第一阶段 PHP 读取源代码，把源码切分成更小的 “token” 单元。举个例子 `<?php echo "Hello World!";` 会被拆解成下面的 token:

```
T_OPEN_TAG (<?php )
T_ECHO (echo)
T_WHITESPACE ( )
T_CONSTANT_ENCAPSED_STRING ("Hello World!")
';'
```

(**译者注: 这里是官方的 [token表](http://php.net/manual/zh/tokens.php)**)

如你所见原始代码被切分成具有语义的　token。处理过程被称为符号化，扫描和词法解析的实现在　`Zend` 目录下的 `zend_language_scanner.l` 文件。

如果你打开文件向下滚动到差不多 1000 行(**译者注: php 8.0.0 在 1261 行**)，你会发现大量的 token 定义语句像下面这样:

```
<ST_IN_SCRIPTING>"exit" {
    return T_EXIT;
}
```

上述代码的意思很明显是: 如果在源代码中遇到了 `exit` ，lexer 应该标记它为 `T_EXIT`。`<` 和　`>`　中间的内容是文本应该被匹配的状态。

`ST_IN_SCRIPTING` 是对　PHP　源码来说是正常状态。还有一些其他的状态像 `ST_DOUBLE_QUOTE` (在双引号中间)，`ST_HEREDOC` (在　heredoc 字符串中间)，等等。

另一个可以在扫描期间做的是指定一个“语义”值(也可以称为"lower case" 或者简称"lval")。下面是例子:

```
<ST_IN_SCRIPTING,ST_VAR_OFFSET>{LABEL} {
    zend_copy_value(zendlval, yytext, yyleng);
    zendlval->type = IS_STRING;
```

`{LABEL}` 匹配一个 PHP 标识(可以被定义为`[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*`)，代码返回　token `T_STRING`。另外它复制　token 的文本到　`zendlval`。所以如果　lexer 遇到一个标识像 `FooBarClass`，它将设置 `FooBarClass` 作为lval。字符串，数字和变量名称也一样。

幸运的是 `in` 操作符并不需要深层次的 lexer 知识。我们只需要添加以下代码段到文件中(与上面的 `exit`　类似):

```
<ST_IN_SCRIPTING>"in" {
    return T_IN;
}
```

(**译者注: 新版已经不是上面的写法了**)

除此之外我们需要让引擎知道我们添加了一个新的 token。打开 `zend_language_parser.y` 加入下面的行在它的类似代码中(在定义操作符的代码段中):

```
%token T_IN "in (T_IN)"
```

现在你应该用 `make -j4` 重新编译下　PHP (必须在顶级目录 `php-src` 中执行，不是 `Zend/`)。这会产生一个新由 re2c　生成的　lexer 并编译它。为了测试我们的修改是否生效。需要执行以下命令:

```
$ sapi/cli/php -r 'in'
```

这将会给出一个解析错误:

```
Parse error: syntax error, unexpected 'in' (T_IN) in Command line code on line 1
```

我们需要做的最后一件事就是使用 [Tokenizer 扩展](https://secure.php.net/tokenizer)　重新生成数据，你需要使用 `cd` 进入 `ext/tokenizer` 目录并且执行 `./tokenizer_data_gen.sh`。

如果你运行 git diff --stat，你会看见下面的信息:

```
Zend/zend_language_parser.y       |    1 +
Zend/zend_language_scanner.c      | 1765 +++++++++++++++++++------------------
Zend/zend_language_scanner.l      |    4 +
Zend/zend_language_scanner_defs.h |    2 +-
ext/tokenizer/tokenizer_data.c    |    4 +-
5 files changed, 904 insertions(+), 872 deletions(-)
```

`zend_language_scanner.c` 内容的变更是 re2C 重新生成的 lexer。因为它包含了行号信息，每个对 lexer 的改变都会产生巨大的不同。所以不用担心;)

#### 解析和编译

目前为止源码已经被分解成有含义的 token，PHP已经可以识别更大的结构像"this is an `if` block"或者"you are defining function here"。这个过程被称为解析，规则被定义在 `zend_language_parser.y` 文件中。这只是一个定义文件，真正的解析器还是由　bison　生成的。

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

想知道什么是“methid modifer”，你需要去看　`method_modifier` 的定义。这就相当直白了。

为了让解析器支持 `in`，我们需要把 `expr T_IN expr` 规则加到　`expr_without_variable`　里面:

```
expr_without_variable:
    ...
    |   expr T_IN expr
    ...
;
```

如果你运行 `make -j4`，bison 会尝试重新构建解析器，但是会报以下的错误:

```
conflicts: 87 shift/reduce
/some/path/php-src/Zend/zend_language_parser.y: expected 3 shift/reduce conflicts
make: *** [/some/path/php-src/Zend/zend_language_parser.c] Error 1
```

shift/reduce 意思是解析器在某些情况下不知道怎么去做。PHP 语法有 3 个　shift/reduce 自相矛盾的冲突(意料之中，因为类似　elseif/else 的歧义)。其余的 84 个冲突是因为新规则造成的。　

原因是我们没有规定 `in` 如何在其他运算符之间运行。举个例子:

```
// if you write
$foo in $bar && $someOtherCond
// should PHP interpret this as
($foo in $bar) && $someOtherCond
// or as
$foo in ($bar && $someOtherCond)
```

上述被成为”运算符的优先级“。还有一个相关的概念是”运算符的关联性“，它决定了你写`$foo in $bar in $baz`时会发生什么。

为了解决　shift/reduce 的冲突，你需要在解析器的开始处找到下面的行并把 `T_IN` 追加在这行后面

```
%nonassoc '<' T_IS_SMALLER_OR_EQUAL '>' T_IS_GREATER_OR_EQUAL
```

这意味着　`in` 和　`<` 比较运算符有相同的优先级，而且没有关联性。下面是 `in` 如何运行的一些示例:

```
$foo in $bar && $someOtherCond
// 被解释为
($foo in $bar) && $someOtherCond
// because `&&` has lower precedence than `in`

$foo in ['abc', 'def'] + ['ghi', 'jkl']
// 被解释为
$foo in (['abc', 'def'] + ['ghi', 'jkl'])
// 因为 `+` 的优先级比 `in`　高

$foo in $bar in $baz
// 会抛出解析异常，因为 `in` 是无关联性的
```

如果运行 `make -j4`，会发现报错没了。然后你可以尝试运行 `sapi/cli/php -r '"foo" in "bar";'`。这什么也不会做，除了打印除一个内存泄漏信息:

```
[Thu Jul 26 22:33:14 2012]  Script:  '-'
Zend/zend_language_scanner.l(876) :  Freeing 0xB777E7AC (4 bytes), script=-
=== Total 1 memory leaks detected ===
```

预料之中，因为到目前为止我们还没有告诉解析器匹配到 `in` 的时候该怎么做。这就是花括号里的内容的作用(**译者注: 还记得上面讲解析器定义的时候简化的花括号吗**)，接下来我们用下面的内容替换掉 `expr T_IN expr`:

```
expr T_IN expr { zend_do_binary_op(ZEND_IN, &$$, &$1, &$3 TSRMLS_CC); }
```

花括号里的内容被成为语义动作，在解析器匹配到固定规则的时候运行。`$$`，`$1` 和　`$3`　这些看起来奇奇怪怪的东西是节点。`$1` 关联第一个　`expr`，`$3` 关联第二个 `expr`(`$3`　是规则里的第三个元素)，`$$` 是存储结果的节点。

`zend_do_binary_op` 是一个编译器指令。它告诉编译器发行　`ZEND_IN` 操作指令，指令将会把 `$1` 和　`$3` 作为操作数，将计算结果存入 `$$`　中。

编译指令在　`zend_compole.c` 中定义(里面带有 zend_compile.h 头文件)。 `zend_do_binary_op` 定义如下:

```

void zend_do_binary_op(zend_uchar op, znode *result, const znode *op1, const znode *op2 TSRMLS_DC)
{
    zend_op *opline = get_next_op(CG(active_op_array) TSRMLS_CC);

    opline->opcode = op;
    opline->result_type = IS_TMP_VAR;
    opline->result.var = get_temporary_variable(CG(active_op_array));
    SET_NODE(opline->op1, op1);
    SET_NODE(opline->op2, op2);
    GET_NODE(result, opline->result);
}

```

代码应该比较好理解，下节我们会把它放到一个有上下文的环境中。最后提醒一件事，在大多数情况下当你想要添加自己的语法的时候，你必须添加自己的 `_do_*` 方法。添加一个二进制操作符是为数不多的情况中的一个。如果你必须要加一个新的  `_do_*` 函数，先看看现存的函数能不能满足你的需求。它们中的大部分都挺简单的。

#### 执行

在上节我提到了编译器在发行操作码。接下来我们近距离看下这些操作码(看 zend_compile.h):

```
struct _zend_op {
    opcode_handler_t handler;
    znode_op op1;
    znode_op op2;
    znode_op result;
    ulong extended_value;
    uint lineno;
    zend_uchar opcode;
    zend_uchar op1_type;
    zend_uchar op2_type;
    zend_uchar result_type;
};
```

对上述结构一个简短的介绍:

* `opcode`: 这是一个真正被执行的操作。可以用 `ZEND_ADD` 或者 `ZEND_SUB` 当例子。

* `op1`，`op2`，　`result`: 每个操作最多可以拥有两个操作数(它可以只选择其中用一个或者一会也不用)和一个结果节点。`op1_type`，`op2_type` 和 `result_type` 决定了节点的类型。稍后我们会去了解节点和节点的类型。

* `extended_value`: 扩展值用来存储标记和一些别的整型值。比如说变量获取指定用它存储变量的类型(像 `ZEND_FETCH_LOCAL` 或者 `ZEND_FETCH_GLOBAL`)

* `handler`: 用来优化操作码的执行，它存储处理函数与操作码和操作数类型相关。
这是自动确定的，因此不必在编译代码中设置。

* `lineno`: 这就不多说了..

这里有五种基本的类型可以详细解释 `*_type` 属性:

* `IS_TMP_VAR`: 临时变量，通常用在一些表达式的结果像 `$foor + $bar`上。临时变量不能共享，所以不能使用引用计数。它们的生命周期很短，所以在使用完成后马上被销毁。临时变量通常被写成 `~n`，`~0` 表示第一个临时变量，`~1` 表示第二个，以此类推。

* `IS_CV`: 编译变量。用来存储哈希表查询结果，PHP 缓存简单变量的位置像 `$foo` 在数组中的地址(C 数组)。此外，编译变量允许 PHP 完全优化哈希表。编译变量使用 `!n` 表示(`n` 表示编译变量数组的偏移量)

* `IS_VAR`: 只是一些简单的变量可以被转换为编译变量。
所有其他类型的变量访问，如 `$foo['bar']` 或 `$foo->bar` 返回一个 `IS_VAR` 变量。它基本上就是一个正常的 zval (有引用计数和其他的所有属性)。Vars 这样 `$n` 表示。

* `IS_CONST`: 常量在代码中的表示比较随意。举个例子，`"foo"` 或者 `3.141` 都是 `IS_CONST` 类型。常量允许更近一步的优化，像复用 zvals，预先计算哈希值。

* `id_UNUSED`: 操作数没有被使用。

与此相关的 `znode_op` 的结构:

```
typedef union _znode_op {
    zend_uint      constant;
    zend_uint      var;
    zend_uint      num;
    zend_ulong     hash;
    zend_uint      opline_num;
    zend_op       *jmp_addr;
    zval          *zv;
    zend_literal  *literal;
    void          *ptr;
} znode_op;
```

我们可以看到节点就是一个联合体。它可以包含上述元素中的一个(只有一个)，具体哪个取决于上下文。比如 `zv` 用来存储 `IS_CONST` zvals，`var` 用来存储 `IS_CV`，`IS_VAR` 和 `IS_TMP_VAR` 变量。剩下的使用在不同的特殊环境下。例如 `jmp_addr` 和 `JMP*` 指令结合使用(在循环和条件判断中使用)。其余都只在编译期间使用，不是在执行期间(像 `constant`)。

现在我们了解了单个操作码的结构，唯一剩下的问题就是这些操作码存在什么地方: PHP 为每个函数(和文件)创建一个 `zend_op_array`，里面存储了操作码和很多其他的信息。我不想深入去讲每个部分都是干什么的，你只需要了解这个结构体存在就行了。

接下来我们回到 `in` 操作符的实现！我们已经指示编译器去发行一个 `ZEND_IN` 操作码。现在我们需要定义这个操作码可以干什么。

这部分在 `zend_vm_def.h` 中实现。如果你看过这个文件，你会发现里面全是下面这样的定义:

```
ZEND_VM_HANDLER(1, ZEND_ADD, CONST|TMP|VAR|CV, CONST|TMP|VAR|CV)
{
    USE_OPLINE
    zend_free_op free_op1, free_op2;

    SAVE_OPLINE();
    fast_add_function(&EX_T(opline->result.var).tmp_var,
        GET_OP1_ZVAL_PTR(BP_VAR_R),
        GET_OP2_ZVAL_PTR(BP_VAR_R) TSRMLS_CC);
    FREE_OP1();
    FREE_OP2();
    CHECK_EXCEPTION();
    ZEND_VM_NEXT_OPCODE();
}
```

`ZEND_IN` 操作码的定义和这个基本一样，所以我们来了解下这个定在在干什么。我会逐行解释:

```
// 头部定义个四个事情:
//   1. 这是一个 ID 为 1 的操作码
//   2. 这个操作码叫 ZEND_ADD
//   3. 这个操作码接受 CONST, TMP, VAR 和 CV 作为第一个操作数
//   4. 这个操作码接受 CONST, TMP, VAR 和 CV 作为第二个操作数
ZEND_VM_HANDLER(1, ZEND_ADD, CONST|TMP|VAR|CV, CONST|TMP|VAR|CV)
{
    // USE_OPLINE 意味着我们想像 `opline` 一样操作 zend_op.
    // 这个对所有存取操作数或者设置返回值的操作码都是必须的
    USE_OPLINE
    // For every operand that is accessed a free_op* variable has to be defined.
    // 这个用来判断操作数是否需要释放.
    zend_free_op free_op1, free_op2;

    // SAVE_OPLINE() 加载 zend_op 到 `opline`。
    // USE_OPLINE 只是声明。
    SAVE_OPLINE();
    // 调用 fast add 函数
    fast_add_function(
        // 告诉函数把结果放在 tmp_var 里
        // EX_T 使用 ID opline->result.var 来操作临时变量
        &EX_T(opline->result.var).tmp_var,
        // 以读取模式获取第一个操作数 ( R 在 BP_VAR_R 的含义是读取，read 的缩写)
        GET_OP1_ZVAL_PTR(BP_VAR_R),
        // 以读取模式获取第二个操作数
        GET_OP2_ZVAL_PTR(BP_VAR_R) TSRMLS_CC);
    // 释放两个操作数 (必须的情况下)
    FREE_OP1();
    FREE_OP2();
    // 检查异常。异常可能发生在任何地方，所以必须在所有操作码中检查异常。
    // 如果有疑问，加上异常检测。
    CHECK_EXCEPTION();
    // 处理下一个操作码
    ZEND_VM_NEXT_OPCODE();
}
```

你可能会注意到这个文件里的东西大部分都是 `大写` 的。因为 `zend_vm_def.h` 只是一个定义文件。真正的 ZEND VM 根据它生成，最终存储在 `zend_vm_execute.h`(巨...大的一个文件)。PHP 有三个不同的虚拟机类型，`CALL`(默认) `GOTO` `SWITCH`。因为他们有不同的实现细节，定义文件使用了大量的伪宏(像 `USE_OPLINE` )，它们最终会被具体实现替代掉。

进一步说，生成的 VM 为所有可能的操作数类型的组合创建专门的实现。所以最后不会只有一个 ZEND_ADD 函数，会有不同的函数实现，像 `ZEND_ADD_CONST_CONST`，`ZEND_ADD_CONST_TMP`，`ZEND_ADD_CONST_VAR`…

Now, in order to implement the ZEND_IN opcode, you should add a new opcode definition skeleton at the end of the zend_vm_def.h file:
现在为了实现 `ZEND_IN` 操作码，你应该在 `zend_vm_def.h` 文件结尾处新增一个操作码定义框架。

```
// 159 是我这里下个没有被使用的操作码编号。 或许你需要选择一个更大的数字。
ZEND_VM_HANDLER(159, ZEND_IN, CONST|TMP|VAR|CV, CONST|TMP|VAR|CV)
{
    USE_OPLINE
    zend_free_op free_op1, free_op2;
    zval *op1, *op2;

    SAVE_OPLINE();
    op1 = GET_OP1_ZVAL_PTR(BP_VAR_R);
    op2 = GET_OP2_ZVAL_PTR(BP_VAR_R);

    /* TODO */

    FREE_OP1();
    FREE_OP2();
    CHECK_EXCEPTION();
    ZEND_VM_NEXT_OPCODE();
}
```

上面的代码只会获取操作数然后丢弃。

为了生成一个新的 VM ，你需要在 `Zend/` 目录内运行 `php_zend_vm_gen.php`。(如果它给了你一堆 /e modifier being deprecated 警告，忽略掉就行了)。运行完以后，去顶级目录运行 `make -j4` 重新编译。

终于，我们能实现真正的逻辑了。我们开始写字符串类型的情况吧:

```
if (Z_TYPE_P(op2) == IS_STRING) {
    zval op1_copy;
    int use_copy;

    // 把要 needle(要找的数据) 转换为 string 
    zend_make_printable_zval(op1, &op1_copy, &use_copy);

    if (Z_STRLEN_P(op1) == 0) {
        /* 空的 needle 直接返回 true */
        ZVAL_TRUE(&EX_T(opline->result.var).tmp_var);
    } else {
        char *found = zend_memnstr(
            Z_STRVAL_P(op2),                  /* haystack */
            Z_STRVAL_P(op1),                  /* needle */
            Z_STRLEN_P(op1),                  /* needle length */
            Z_STRVAL_P(op2) + Z_STRLEN_P(op2) /* haystack end ptr */
        );

        ZVAL_BOOL(&EX_T(opline->result.var).tmp_var, found != NULL);
    }

    /* Free copy */
    if (use_copy) {
        zval_dtor(&op1_copy);
    }
}
```

This function may either have to create a new zval, or not. That’s why we pass op1_copy and use_copy into it. If the function copied the value we just put it into the op1 variable (so we don’t have to deal with two different variables everywhere). Furthermore the copy has to be freed at the end (what the last three lines do).

最难的部分是把 needle 转换成字符串，这里使用了 `zend_make_printable_zval`。这个函数也许会创建一个新的 zval。这就是我们传 `op1_copy` 和 `use_copy` 的原因。

#### Conclusion

未完待续

[原文链接](http://nikic.github.io/2012/07/27/How-to-add-new-syntactic-features-to-PHP.html#the-life-of-a-php-script)

> 沧海一声笑 
滔滔两岸潮
浮沉随浪 
只记今朝