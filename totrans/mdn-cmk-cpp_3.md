# 2 CMake 语言

编写 CMake 语言有点棘手。当你第一次阅读列表文件时——你可能会觉得那里的语言如此简单，以至于不需要任何特殊的培训或准备。这种方法往往转化为实际尝试引入更改和实验代码，而不彻底理解它是如何工作的。我们，程序员通常非常忙碌，并且非常热衷于通过实践学习、猜测等方式来解决这些问题。解决技术问题的这种“技术”被称为*巫毒编程*。

CMake 提供了这种不幸的简单性，它创造了一种一切都是理所当然的错觉。在我们完成了小的添加、修复、hack 或“快速修复”之后——我们意识到有些事情并不完全正常。花在调试上的时间往往比实际更好地研究主题要长。幸运的是，这不是我们的命运——因为在我们面前的章节涵盖了大部分这种关键知识。

我们不仅将理解 CMake 语言的基本构建块：注释、命令、变量和控制结构，还将结合一些关于干净、现代 CMake 的背景信息。你会发现：CMake 让你处于一个独特的地位：一方面，你扮演着构建工程师的角色。你需要理解编译器、平台以及它们之间的所有复杂性。另一方面：你是一名开发者——你在编写生成构建系统的代码。编写好的代码是困难的，需要在多个层面上同时思考：它应该有效、易于阅读、易于推理、扩展和维护。这正是我们在这里要讨论的内容。

最后，我们将介绍一些最有用和最常见的命令。那些不经常使用的命令，你会在*附录*中找到（完整的字符串、列表和文件操作命令参考）。

在本章中，我们将涵盖以下主要主题：

+   CMake 语法基础

+   使用变量

+   使用列表

+   理解控制结构

+   有用的命令

## 技术要求

你可以在 GitHub 上找到本章中出现的代码文件，地址是[`github.com/PacktPublishing/Modern-CMake-for-Cpp`](https://github.com/PacktPublishing/Modern-CMake-for-Cpp)

## CMake 语法基础

编写 CMake 代码非常类似于编写任何其他命令式语言：从上到下、从左到右执行行，偶尔进入一个包含的文件或调用的函数。根据模式（参见*第一章 - 命令行*），执行从源树的根文件（`CMakeLists.txt`）或作为`cmake`参数传递的`.cmake`脚本文件开始。

正如我们在上一章中讨论的，脚本支持大多数 CMake 语言（项目相关功能除外）。因此，它们非常适合早期练习语法本身，这就是我们在这里使用它们的原因。在熟悉编写基本列表文件后 - 我们将开始准备实际的项目文件（在下一章）。提醒一下 - 脚本可以通过以下命令行运行：

`cmake -P script.cmake`

> **注意**
> 
> CMake 支持 7 位 ASCII 文本文件，以便在所有平台上实现可移植性。您可以使用`\n`或`\r\n`行结束符。CMake 3.0 以上版本支持带有可选字节顺序标记的 UTF-8，CMake 3.2 以上版本支持 UTF-16。

CMake 列表文件中的所有内容要么是命令调用，要么是注释。

### 注释

（注释有两种类型：单行注释和括号（多行）注释，就像在 C++中一样。但与 C++不同的是，括号注释可以嵌套。让我展示一下语法：

```cpp
# single-line comments start with a hash sign "#"
# they can be placed on an empty line
message("Hi"); # or after a command like here.
#[=[ 
bracket comment
  #[[
    nested bracket comment
  #]]
#]=]
```

多行注释因其符号而得名，它们以一个开方括号、任意数量的等号和另一个括号开始：`[=[`。要关闭方括号注释，**请使用相同数量的**等号，并反向括号，如下所示：`]=]`。

在开方括号标记前加上`#`是可选的，并允许通过在方括号注释的第一行添加另一个`#`来快速取消注释多行注释，如下所示：

```cpp
##[=[ this is a single-line comment now
no longer commented
  #[[
    still, a nested comment
  #]]
#]=] this is a single-line comment now
```

这是一个巧妙的技巧，但在我们的 CMake 文件中何时以及如何使用注释？由于编写列表文件本质上是在编程，因此将最佳编码实践引入它们也是一个好主意。遵循这些实践的代码通常被称为“干净” - 这是多年来由软件开发大师如罗伯特·C·马丁、马丁·福勒和其他许多作者创造的术语。被认为是有帮助和有害的常常存在很大争议，正如你所猜测的 - 注释作为主题出现不止一次。

一切都应根据具体情况进行判断，但普遍认同的指导原则是，好的注释至少提供以下一项：

+   **信息**，解开复杂性，如正则表达式模式或格式化字符串

+   **意图**，从实现或接口中不明显

+   **澄清**，解释无法轻易重构或更改的概念

+   **后果警告**，特别是在可能破坏其他事物的代码周围

+   **强调**，强调难以用代码表达的想法的重要性

+   **法律条款**。一种必要的恶，通常不是程序员的领域

如果可以，避免注释，用更好的命名、重构或更正代码来代替。如果可能，避免以下类型的注释：

+   **强制性**，添加以完整性，但不是真正重要

+   **冗余**，重复代码中已经清楚写明的内容

+   **误导性**，过时或不正确，因为它们没有跟随代码变化

+   **日志**，记录更改的内容和时间（使用版本控制系统进行此操作）

+   **分隔符**，标记部分或以其他方式

不带注释编写优雅的代码是困难的，但它能提升读者的体验。由于我们花在阅读上的时间比写作多 - 我们应该坚持编写可读性强的代码，而不是快速编写的代码。我建议查看本章末尾的*进一步阅读*部分，以获取有关清洁代码的一些好参考资料。如果你对注释特别感兴趣 - 你会在我的许多 YouTube 视频中找到一个深入探讨这个主题的链接。

### 命令调用

是时候采取行动了！调用命令是 CMake listfiles 的基础。要执行命令，你必须提供其名称和括号，在其中你可以包含一个空格分隔的**命令参数**列表。

![图 2.1：命令示例](img/file5.png)

图 2.1：命令示例

命令名称不区分大小写，但 CMake 社区有一个约定，即在名称中使用`snake_case`（小写字母单词用下划线连接）。你还可以定义自己的命令 - 我们将在本章的*控制结构*部分介绍这一点。

与 C++相比，特别引人注目的是**CMake 中的命令调用不是表达式**。你不能将另一个命令作为参数提供给被调用的命令，因为*所有内容*在括号内都被解释为该命令的参数。

更令人愤怒的是，CMake 命令在调用结束时不需要分号。这可能是因为源文件的每一行可以包含**一个命令调用**，后面可以跟一个可选的单行注释。或者，整行必须是括号注释的一部分。所以，这些是唯一允许的格式：

```cpp
command(argument1 "argument2" argument3) # comment
[[ 
multiline comment ]] 
```

在括号注释后放置命令是不允许的：

```cpp
[[ bracket 
]] command()
```

删除注释、空格和空行后 - 我们得到一个命令调用列表。这创造了一个有趣的视角：CMake 语法真的很简单，但这就足够了吗？我们如何处理变量？或者如何控制执行流程？

CMake 提供了上述命令以及更多。为了使事情变得更容易，我们将随着我们通过不同的主题介绍相关的命令，它们可以分为三个类别：

+   脚本命令始终可用，它们改变命令处理器的状

+   项目命令在项目中可用，它们操作项目状态和构建目标

+   CTest 命令在 CTest 脚本中可用，它们管理测试

本章将介绍最常用的脚本命令（因为它们在项目中也非常有用）。项目和 CTest 命令将在后续章节中讨论，因为我们将介绍构建目标的概念（*第三章，设置你的第一个 CMake 项目*）和测试框架（*第八章，测试框架*）。

实际上，每个命令都依赖于语言的其他元素来发挥作用：变量、条件语句，最重要的是：命令行参数。让我们看看应该如何使用它们。

### 命令参数

许多命令需要以空格分隔的参数来参数化它们的行为。正如您在图 2.1 中看到的，参数周围的引号有些奇怪。一些参数有引号，而其他参数没有 - 这是怎么回事？

在底层，CMake 唯一识别的类型是字符串。这就是为什么每个命令都期望为其参数提供零个或多个字符串。但是，普通的静态字符串并不是很有用，特别是当我们不能嵌套命令调用时。这就是参数发挥作用的地方：CMake 将对每个参数求值为静态字符串，然后将它们传递给命令。求值意味着字符串插值，或者简单地说：用另一个值替换字符串的部分。这可能意味着替换*转义序列*，扩展**变量引用**（也称为变量插值）和解包列表。

根据上下文，我们可能希望根据需要启用这种求值，为此，CMake 提供了三种类型的参数：

+   括号参数

+   引号参数

+   未引用的参数

每种类型都提供不同级别的求值，并且有一些小的怪癖。

#### 括号参数

括号参数不会被求值，因为它们用于**原样传递多行字符串**，作为命令的单个参数。这意味着它将包含制表符和换行符等空白字符。

这些参数的结构与注释完全相同：以`[=[`打开并以`]=]`闭合，其中等号在标记中的数量必须匹配（跳过等号也可以）。与注释的唯一区别是 - 您不能嵌套括起来的参数。

这是使用`message()`命令的此类参数的示例，该命令在屏幕上打印所有传递的参数：

#### chapter02/01-arguments/bracket.cmake

```cpp
message([[multiline
bracket
argument
]])
message([==[
  because we used two equal-signs "=="
  following is still a single argument:
  { "petsArray" = [["mouse","cat"],["dog"]] }
]==])
```

在上面的示例中，我们可以看到不同形式的括号参数。第一个跳过等号。请注意，将闭合标签放在单独的行中在输出中显示为空行：

```cpp
$ cmake -P chapter02/01-arguments/bracket.cmake
multiline
bracket
argument
  because we used two equal-signs "=="
  following is still a single argument:
  { "petsArray" = [["mouse","cat"],["dog"]] }
```

第二种形式在我们传递包含双括号`]]`（突出显示）的文本时很有用，因此它们不会被解释为参数的闭合。

这种括号参数的使用有限 - 通常用于包含较长的文本块。在大多数情况下，我们需要更动态的内容，如引号参数。

#### 引号参数

第二种参数类似于常规的*C++字符串*：它们将多个字符（包括空格）组合在一起，并将扩展*转义序列*。与*C++字符串*一样，它们以双引号字符`"`打开和关闭，要使用文字引号，必须用反斜杠转义它：`\"`。其他广为人知的转义序列也得到支持：`\\`表示文字反斜杠，`\t`是制表符，`\n`是换行符，`\r`是回车符。

这与*C++字符串*的相似之处到此为止。相比之下，加引号的参数可以跨越多行，并且它们会插值变量引用。可以将它们视为内置了*C*中的`sprintf`或*C++20*中的`std::format`。要在参数中插入变量引用，请将变量名包裹在如下标记中：`${name}`。我们将在*变量*部分详细讨论变量引用。

让我们尝试这些参数的实际应用：

#### 第二章/01-参数/quoted.cmake

```cpp
message("1\. escape sequence: \" \n in a quoted argument")
message("2\. multi...
line")
message("3\. and a variable reference: ${CMAKE_VERSION}")
```

你能猜出上述脚本的输出会有多少行吗？

```cpp
$ cmake -P chapter02/01-arguments/quoted.cmake
1\. escape sequence: "
 in a quoted argument
2\. multi...
line
3\. and a variable reference: 3.16.3
```

没错 - 我们有一个转义的引号字符、转义的换行符和一个文字换行符 - 它们都将被打印在输出中。我们还访问了一个内置变量`CMAKE_VERSION`，我们可以看到它在最后一行正确地插值了。

#### 未加引号的参数

最后一种参数类型在编程世界中确实有点罕见。我们已经习惯了字符串必须以某种方式分隔：使用单引号、双引号或反引号。CMake 偏离了这一惯例，引入了未加引号的参数。有人可能会争辩说，省略定界符更容易阅读，就像跳过分号一样。这是真的吗？我会让你形成自己的观点。

未加引号的参数同时评估*转义序列*和变量引用。但是要小心分号`;`：它在这里被视为分隔符。CMake 会将包含它的参数分割成多个参数。如果需要使用它，请用反斜杠转义它（`\;`）。这就是 CMake 管理列表的方式。我将在*列表*部分详细解释这一点。

你可能会发现这些参数是最令人困惑的工作，所以这里有一些视觉帮助来澄清这些参数是如何划分的：

![图 2.2：转义序列使得单独的标记被解释为单个参数](img/file6.png)

图 2.2：转义序列使得单独的标记被解释为单个参数

> **问题**
> 
> 为什么一个值作为单个参数传递还是多个参数传递会有所不同？
> 
> 一些 CMake 命令会逐个消耗参数。如果你的参数意外地被分隔开，你将会得到难以调试的错误。

未加引号的参数不能包含未转义的引号`"`, 井号`#`和反斜杠`\`。如果这些规则还不够：括号`()`只有在其形成正确、匹配的配对时才被允许。也就是说：你必须从一个开括号开始，并在结束命令参数列表之前将其闭合。

让我们来看看上述所有规则的示例：

#### 第二章/01-参数/unquoted.cmake

```cpp
message(a\ single\ argument)
message(two arguments)
message(three;separated;arguments)
message(${CMAKE_VERSION})  # a variable reference
message(()()())            # matching parentheses 
```

上述的输出会是什么？

```cpp
$ cmake -P chapter02/01-arguments/unquoted.cmake
a single argument
twoarguments
threeseparatedarguments
3.16.3
()()() 
```

即使是像`message()`这样简单的命令，对于分隔的未加引号的参数也非常挑剔：

+   在**单个参数**中的空格在明确转义时被正确打印

+   然而**twoarguments**和**threeseparatearguments**被“粘合”在一起 - 因为`message()`本身不会添加任何空格。

既然我们了解了如何处理 CMake 参数的复杂性和特殊性——我们准备好探讨下一个有趣的课题：在 CMake 中处理各种变量。

## 处理变量

CMake 中的变量是一个出人意料的复杂主题。不仅存在三种类别的变量：普通、缓存和环境变量，而且它们还存在于不同的作用域中，有着特定的规则来决定一个作用域如何影响另一个作用域。对这些规则的糟糕理解往往是错误和头痛的来源。我建议你仔细研究这一部分，并确保在继续之前你已经掌握了所有概念。

让我们从关于 CMake 变量的关键事实开始：

+   变量名是区分大小写的，并且几乎可以使用任何字符构建。

+   所有变量在内部都存储为字符串，即使某些命令可以将它们解释为其他类型的值（甚至是列表！）。

+   基本的变量操作命令是`set()`和`unset()`，但还有其他命令可以影响变量，如`string()`和`list()`。

要设置一个变量，我们只需调用`set()`，提供其名称和值：

#### chapter02/02-variables/set.cmake

```cpp
set(MyString1 "Text1")
set([[My String2]] "Text2")
set("My String 3" "Text3")
message(${MyString1})
message(${My\ String2})
message(${My\ String\ 3})
```

如你所见，使用括号和引号参数允许变量名中包含空格。然而，在引用时，我们必须用反斜杠`\`转义空格。因此，建议在变量名中仅使用字母数字字符、`-`和`_`。

还要避免使用以`CMAKE_`、`_CMAKE_`、下划线`_`开头的保留名称（大写、小写或混合大小写），后跟任何 CMake 命令的名称。

> **注意**
> 
> `set()`命令接受变量的纯文本名称作为第一个参数，但`message()`使用包裹在`${}`语法中的变量引用。如果我们向`set()`命令提供包裹在`${}`语法中的变量会发生什么？为了回答这个问题，我们需要更好地理解变量引用。

要取消设置一个变量，我们可以使用`unset()`，如下所示：`unset(MyString1)`。

### 变量引用

在*命令参数*部分，我已简要提及了引用——因为它们对于带引号和不带引号的参数都会被评估。我们了解到，要创建对已定义变量的引用，我们需要使用这样的语法：`message(${MyString1})`。

在评估时，CMake 将遍历作用域栈（我稍后会解释），并用值或空字符串替换`${MyString1}`（如果没有找到变量，则不会报告错误）。这个过程称为变量评估、展开或插值。

这种插值是以由内而外的方式进行的，这意味着两件事：

+   首先，如果遇到这样的引用：`${MyOuter${MyInner}}`，CMake 会尝试先评估`MyInner`，而不是寻找名为`MyOuter${MyInner}`的变量。

+   其次：如果`MyInner`变量成功展开——CMake 将重复展开过程，直到没有进一步的展开是可能的。

让我们考虑以下变量的一个例子：

+   `MyInner` 的值为 `Hello`

+   `MyOuter` 的值为 `${My`

当我们调用命令：`message("${MyOuter}Inner} World")`时，我们将收到的输出是`Hello World,`，这是因为`${MyOuter}`被替换为字面值`${My`，它与顶层的`Inner}`结合，创建了另一个变量引用：`${MyInner}`。

CMake 将完全执行这种扩展，然后将生成的值作为参数传递给命令。这就是为什么当我们调用`set(${MyInner} "Hi")`时，我们实际上并没有改变`MyInner`变量，而是改变了`Hello`变量。CMake 将`${MyInner}`扩展为`Hello`，并将该字符串作为第一个参数传递给`set()`命令以及新值`Hi`。这种情况往往不是我们想要的。

变量引用在涉及变量类别时的工作方式有点特殊，但总的来说：

+   `${}`语法用于引用普通*或缓存变量*

+   `$ENV{}`语法用于引用环境变量

+   `$CACHE{}`语法用于引用缓存变量

没错，使用`${}`你可能从一个类别或另一个类别获取值，我将在*Scope*部分解释这一点。但首先让我们介绍其他类别的变量，这样我们就能清楚地了解它们是什么。

> **注意**
> 
> 请记住，你可以通过命令行在`--`标记后传递参数给脚本。
> 
> 值将位于`CMAKE_ARGV<n>`中，传递的参数数量位于`CMAKE_ARGC`中。

### 使用环境变量

这是最不复杂的变量类别。CMake 对用于启动`cmake`进程的环境中的变量进行复制，并将它们在一个单一的全局范围内提供。要引用这些变量，请使用`$ENV{<name>}`语法。

CMake 还允许你`set()`和`unset()`这些变量，但这些更改只会对运行中的`cmake`进程的本地副本生效，而不是实际的系统环境，这些更改对后续的构建或测试运行是不可见的。

要修改或创建变量，请使用`set(ENV{<variable>} <value>)`命令，如下所示：

```cpp
set(ENV{CXX} "clang++")
```

要清除环境变量，请使用`unset(ENV{<variable>})`，如下所示：

```cpp
unset(ENV{VERBOSE})
```

有一些环境变量会影响 CMake 的行为，控制构建和 CTest。CXX 是其中之一 - 它指定用于编译 C++文件的可执行文件。我们将在它们变得相关时介绍它们。完整的列表可在文档中找到：

[`cmake.org/cmake/help/latest/manual/cmake-env-variables.7.html`](https://cmake.org/cmake/help/latest/manual/cmake-env-variables.7.html)

如果你使用环境变量作为命令的参数，这些值将在生成构建系统时被插值。这意味着它们将被烘焙到构建树中，并且在构建阶段更改环境不会有任何影响。

以以下项目文件为例：

#### 章节 02/03-环境/CMakeLists.txt

```cpp
cmake_minimum_required(VERSION 3.20.0)
project(Environment)
message("generated with " $ENV{myenv})
add_custom_target(EchoEnv ALL COMMAND echo "myenv in build is" $ENV{myenv})
```

上述项目有两个步骤：它将在配置期间打印`myenv`环境变量，并通过`add_custom_target()`添加一个构建步骤，该步骤在构建过程中回显同一变量。我们可以通过一个使用配置阶段和构建阶段不同值的 bash 脚本来测试会发生什么：

#### 第二章/03-环境/build.sh

```cpp
#!/bin/bash
export myenv=first
echo myenv is now $myenv
cmake -B build .
cd build
export myenv=second
echo myenv is now $myenv
cmake --build .
```

运行上述命令可以清楚地看到，配置期间设置的值在生成的构建系统中得以保留：

```cpp
$ ./build.sh | grep -v "\-\-"
myenv is now first
generated with first
myenv is now second
Scanning dependencies of target EchoEnv
myenv in build is first
Built target EchoEnv
```

### 使用缓存变量

我们首次提到缓存变量是在讨论`cmake`的命令行选项时，在第一章中。本质上，它们是存储在构建树中的`CMakeCache.txt`文件中的持久化变量。它们包含在项目配置阶段收集的信息——既来自系统（编译器、链接器、工具的路径；以及其他），也来自用户通过 GUI。缓存变量在脚本中不可用（因为没有`CMakeCache.txt`），它们只存在于项目中。

缓存变量可以使用`$CACHE{<name>}`语法引用。

要设置缓存变量，请使用带有以下语法的`set()`：

`set(<variable> <value> CACHE <type> <docstring> [FORCE])`

如您所见，与普通变量的`set()`相比，有一些新的必需参数，还引入了一些第一个关键字：`CACHE`和`FORCE`。

将`CACHE`指定为`set()`的参数意味着我们打算更改配置阶段提供的内容，并要求提供变量`<type>`和`docstring`。这是因为这些变量可由用户配置，GUI 需要知道如何显示它们。接受的类型包括：

+   `BOOL` - 布尔 ON/OFF 值。GUI 将显示一个复选框。

+   `FILEPATH` - 磁盘上的文件路径。GUI 将打开一个文件对话框。

+   `PATH` - 磁盘上的目录路径。GUI 将打开一个目录对话框。

+   `STRING` - 一行文本。如果设置了`STRINGS`缓存条目属性（可以使用`set_property()`完成），GUI 将提供一个文本字段或一个下拉选择。

+   `INTERNAL` - 一行文本。GUI 会跳过内部条目。它们可用于在多次运行之间持久存储变量。使用此类型意味着 FORCE。

`<doctring>`只是一个标签，GUI 将在字段旁边显示该标签，以向用户提供有关此设置的更多详细信息。即使是`INTERNAL`类型，这也是必需的。

设置缓存变量在一定程度上遵循环境变量的规则：值仅在 CMake 当前执行期间被覆盖。请看这个例子：

```cpp
set(FOO "BAR" CACHE STRING "interesting value")
```

上述调用如果没有在缓存中存在变量，则没有永久效果。但是，如果缓存中不存在值或指定了可选的`FORCE`参数，则该值将被持久化：

```cpp
set(FOO "BAR" CACHE STRING "interesting value" FORCE)
```

设置缓存变量有一些不明显的含义。即：任何同名的普通变量都将被移除。我们将在下一节中找出原因。

提醒一下，缓存变量也可以从命令行管理，请查看第一章中的相应部分。

### 如何在 CMake 中正确使用变量作用域

变量作用域可能是整个概念中最难的部分。也许是因为我们习惯了在支持命名空间和作用域操作符的更高级语言中事物是如何完成的。CMake 没有这些机制，所以它以自己有点特定的方式处理这个问题。

为了澄清：变量作用域作为一个通用概念，旨在分离不同的抽象层次，以便当用户定义的函数被调用时 - 在该函数中设置的变量是局部的。这些局部变量即使与全局变量名称完全相同，也不会影响全局作用域。如果需要，函数应该具有对全局变量的读/写访问权限。这种变量（或作用域）的分离必须在多个层次上工作 - 当一个函数调用另一个函数时，适用相同的分离规则。

CMake 支持两种作用域：

+   **函数作用域**：当使用 `function()` 定义的自定义函数被执行时

+   **目录作用域**：当从 `add_subdirectory()` 命令执行嵌套目录中的 `CMakeLists.txt` 列表文件时

我们将在本书后面介绍上述命令，首先我们需要了解变量作用域的概念是如何实现的。当创建嵌套作用域时，CMake 只是用当前作用域中所有变量的副本填充它。后续命令将影响这些副本。但是，一旦嵌套作用域完成 - 所有副本都会被删除，原始的父作用域会被恢复。

让我们考虑以下场景：

+   父作用域将变量 `VAR` 设置为 `ONE`

+   嵌套作用域开始，`VAR` 被打印到控制台

+   `VAR` 被设置为 `TWO`，`VAR` 被打印到控制台

+   嵌套作用域结束，`VAR` 被打印到控制台

控制台的输出将如下所示：`ONE`，`TWO`，`ONE`。这是因为复制变量 `VAR` 在嵌套作用域结束后被丢弃。

CMake 中作用域的工作方式具有有趣的含义，这在其他语言中并不常见。如果在嵌套作用域中执行时 `unset()` 一个在父作用域中创建的变量 - 它将在嵌套作用域中消失。当嵌套作用域完成时 - 变量将恢复到其先前的值。

这使我们了解了变量引用的行为，以及 `${}` 语法。每当我们尝试访问普通变量时，CMake 将访问当前作用域的变量，如果定义了具有该名称的变量 - 它将返回其值。到目前为止，一切都很好。然而，当 CMake 找不到具有该名称的变量（它不存在，或者被 `unset()`）** - 它将访问缓存变量并在找到匹配项时返回其值**。

这是一个可能的陷阱，如果我们有一个嵌套作用域调用 `unset()`。取决于我们在哪里引用该变量：在内层还是外层作用域，我们将访问缓存或原始值。

但是，如果我们真的需要在调用的父作用域中改变变量，该怎么办？CMake 有一个 `PARENT_SCOPE` 标志，你可以将其添加到 `set()` 和 `unset()` 命令的末尾：

```cpp
set(MyVariable "New Value" PARENT_SCOPE)
unset(MyVariable PARENT_SCOPE) 
```

这种解决方法有些局限，因为它不允许访问超过一个层级的变量。另一个值得注意的是，使用 `PARENT_SCOPE` 并不会改变当前作用域中的变量。

让我们看看变量作用域在实践中是如何工作的，并考虑以下示例：

#### chapter02/04-scope/CMakeLists.txt

```cpp
function(Inner)
  message("  > Inner: ${V}")
  set(V 3)
  message("  < Inner: ${V}")
endfunction()
function(Outer)
  message(" > Outer: ${V}")
  set(V 2)
  Inner()
  message(" < Outer: ${V}")
endfunction()
set(V 1)
message("> Global: ${V}")
Outer()
message("< Global: ${V}")
```

我们将全局变量 `V` 设置为 `1`，然后调用 `Outer` 函数，将 `V` 设置为 `2`，调用 `Inner` 函数并将 `V` 设置为 `3`。在每一步之后，我们将变量打印到控制台：

```cpp
> Global: 1
 > Outer: 1
  > Inner: 2
  < Inner: 3
 < Outer: 2
< Global: 1
```

正如我们之前解释的：当我们深入函数时——变量值被复制到嵌套的作用域，但当我们退出作用域时——它们的原始值被恢复。

如果我们改变 `Inner` 函数的 `set()` 命令，使其在父作用域中操作：`set(V 3 PARENT_SCOPE)`，输出会是什么？

```cpp
> Global: 1
 > Outer: 1
  > Inner: 2
  < Inner: 2
 < Outer: 3
< Global: 1
```

我们影响了 `Outer` 函数的作用域，但没有影响 `Inner` 作用域或全局作用域！

CMake 文档还提到，CMake 脚本在单个目录作用域中绑定变量（这有点多余，因为实际上创建目录作用域的命令 `add_subdirectory()` 不允许在脚本中使用）。

由于所有变量都存储为字符串，CMake 必须采用更具创意的方法来处理更复杂的数据结构，如列表。

## 使用列表

为了存储一个列表，CMake 会将所有元素拼接成一个以分号为分隔符的字符串，如下所示：`a;list;of;5;elements`。你可以在元素中使用反斜杠来转义分号，例如：`a\;single\;element`。

我们可以使用 `set()` 命令创建一个列表：`set(myList a list of five elements)`。由于列表的存储方式，以下命令将产生完全相同的效果：

+   `set(myList "a;list;of;five;elements")`

+   `set(myList a list "of;five;elements")`

CMake 自动在未加引号的参数中解包列表。像这样传递一个未加引号的 `myList` 引用：`message("the list is:" ${myList})` 会导致 `message()` 命令接收 6 个参数：`"the list is:", "a", "list", "of", "five", "elements"`。当然，输出将不会在参数之间打印任何额外的空格：

```cpp
the list is:alistoffiveelements
```

如你所见——这是一个非常简单的机制，应该谨慎对待。

CMake 提供了一个 `list()` 命令，它提供了多种子命令来读取、搜索、修改和排序列表。这里是一个简短的总结：

```cpp
list(LENGTH <list> <out-var>)
list(GET <list> <element index> [<index> ...] <out-var>)
list(JOIN <list> <glue> <out-var>)
list(SUBLIST <list> <begin> <length> <out-var>)
list(FIND <list> <value> <out-var>)
list(APPEND <list> [<element>...])
list(FILTER <list> {INCLUDE | EXCLUDE} REGEX <regex>)
list(INSERT <list> <index> [<element>...])
list(POP_BACK <list> [<out-var>...])
list(POP_FRONT <list> [<out-var>...])
list(PREPEND <list> [<element>...])
list(REMOVE_ITEM <list> <value>...)
list(REMOVE_AT <list> <index>...)
list(REMOVE_DUPLICATES <list>)
list(TRANSFORM <list> <ACTION> [...])
list(REVERSE <list>)
list(SORT <list> [...])
```

大多数情况下，我们在项目中并不真正需要使用列表。然而，如果你发现自己处于那种罕见的情况，这个概念会带来便利——你会在*附录*中找到关于 `list()` 命令的更深入参考。

现在我们知道如何处理各种列表和变量——让我们将注意力转移到执行流程的控制上，并学习 CMake 中可用的控制结构。

## 理解控制结构

（没有控制结构的 CMake 语言是不完整的！就像其他一切一样，它们以命令的形式提供，并分为三类：条件块、循环和命令定义。控制结构在脚本中执行，并在项目构建系统生成期间执行。）

### （条件块）

（CMake 中唯一支持的条件块是简单的`if()`。每个这样的块都必须用`endif()`命令关闭，并且可以有任意数量的`elseif()`命令和一个可选的`else()`命令，顺序如下：）

```cpp
if(<condition>)
  <commands>
elseif(<condition>) # optional block, can be repeated
  <commands>
else()              # optional block
  <commands>
endif()
```

（就像许多其他命令式语言一样，if-块控制将执行哪些命令集：）

+   （如果`if()`命令中指定的`<条件>`满足，则将执行第一个代码段。）

+   （否则，CMake 将执行属于该块中第一个满足其`<条件>`的`elseif()`命令的代码段中的命令。）

+   （如果没有任何此类命令，CMake 将检查是否提供了`else()`命令，并执行该代码段中的任何命令。）

+   （如果上述条件都不满足，执行将继续在`endif()`之后。）

（提供的`<条件>`根据非常简单的语法进行评估。）

#### （条件语法）

`if()`、`elseif()`和`while()`命令同样适用相同的语法。

##### （逻辑运算）

（`if()`条件支持`NOT`、`AND`和`OR`逻辑运算符，如下所示：）

+   （`NOT <条件>`）

+   `<条件> AND <条件>`

+   `<条件> OR <条件>`

（此外，可以使用匹配的括号对`()`嵌套条件。像所有体面的语言一样，CMake 尊重评估顺序，并从最内层的括号开始：）

+   `(<条件>) AND ((<条件>) OR (<条件>))`

##### （字符串和变量的评估）

（由于历史原因（因为变量引用`${}`语法并不总是存在），CMake 将尝试将未加引号的参数评估为变量引用。换句话说：在条件中使用简单的变量名`VAR`等于写`${VAR}`。这里有一个例子供你考虑，还有一个陷阱：）

```cpp
set(VAR1 FALSE)
set(VAR2 "VAR1")
if(${VAR2})
```

（`if()`条件在这里以一种有些复杂的方式工作：首先，它将`${VAR2}`评估为`VAR1`，这是一个已识别的变量，然后又评估为`FALSE`字符串。**只有当字符串等于以下任何常量时，它们才被认为是布尔真**（比较时不区分大小写）：）

（`ON`、`Y`、`YES`、`TRUE`）

+   （非零数字）

（这使我们得出结论，上述示例中的条件将被评估为假。）

（然而，这里有一个问题：对于一个未加引号的参数，其名称是一个包含值如`BAR`的变量，这个条件的评估结果会是什么？）

```cpp
set(FOO BAR)
if(FOO)
```

根据我们目前所说的，这将是假的，因为字符串`BAR`不符合布尔值真的标准。不幸的是，情况并非如此，因为**CMake 在未加引号的变量引用方面做出了例外**。是的，显式的`if("BAR")`将被视为布尔值假，但由于该值存储在变量中。但是，CMake 只有在以下常量之一时才会将`if(FOO)`评估为假（比较不区分大小写）：

+   `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`

以`-NOTFOUND`结尾的字符串

+   空字符串

+   零

因此，简单地询问未定义的变量将被评估为**假**：

```cpp
if (FOO)
```

但是，事先定义变量会改变情况，条件将被评估为**真**：

```cpp
set(FOO "FOO")
if (FOO)
```

> **注意**
> 
> 如果你认为未加引号的参数的行为令人困惑，请将变量引用包裹在引号中：`if ("${FOO}")`。这将导致在传递给`if()`命令之前对参数进行评估，行为将与字符串的评估一致。

换句话说，CMake 假设用户是在询问变量是否已定义（且不是显式假）。幸运的是，我们可以明确检查这一事实（而不必担心内部值）：

```cpp
if(DEFINED <name>)
if(DEFINED CACHE{<name>})
if(DEFINED ENV{<name>})
```

##### 比较值

比较操作支持以下操作符：

`EQUAL`, `LESS`, `LESS_EQUAL`, `GREATER`, `GREATER_EQUAL`

它们可以用来比较数值，如下所示：

```cpp
if (1 LESS 2) 
```

> **注意**
> 
> CMake 的文档指出，如果其中一个操作数不是数字，则值将为假。但实际实验表明，以数字开头的字符串比较可以正确工作：`if (20 EQUALS "20 GB")`

通过在任何操作符前添加`VERSION_`前缀，按照`major[.minor[.patch[.tweak]]]`格式比较软件版本：

```cpp
if (1.3.4 VERSION_LESS_EQUAL 1.4)
```

省略的组件被视为零，非整数版本组件将比较的字符串截断到该点。

对于字典序字符串比较，我们需要在操作符前加上`STR`前缀（注意没有下划线）：

```cpp
if ("A" STREQUAL "${B}")
```

正如我们经常发现的那样，这还不够，幸运的是，CMake 还支持 POSIX 正则表达式匹配（文档暗示 ERE 风味，但没有提到字符类支持）。使用`MATCHES`操作符，如下所示：

`<VARIABLE|STRING> MATCHES <regex>`

任何匹配的组都捕获在 CMAKE_MATCH_<n>变量中。

##### 简单检查

我们之前已经提到过一种简单的检查`DEFINED`，但还有其他一些检查，如果满足简单条件，则直接返回真。

我们可以检查：

+   值在列表中：`<VARIABLE|STRING> IN_LIST <VARIABLE>`

+   可用于调用的命令：`COMMAND <command-name>`

+   CMake 政策存在：`POLICY <policy-id>`（在*第三章*中介绍）

+   使用`add_test()`添加了 CTest 测试：`TEST <test-name>`

+   如果构建目标已定义：`TARGET <target-name>`

-   我们将在*第四章，使用目标工作*中介绍构建目标，现在我们只能说，目标是在项目中使用`add_executable()`，`add_library()`或`add_custom_target()`命令创建的构建过程的逻辑单元，该命令已经被调用。

##### -   检查文件系统

-   CMake 提供了许多处理文件的方法。我们很少需要直接操作它们，我们更愿意使用更高层次的方法。本书将在*附录*中提供文件相关命令的简短参考。但大多数情况下，只需要以下操作符（只有对于完整路径，行为才是明确定义的）：

+   -   检查文件或目录是否存在：`EXISTS <path-to-file-or-directory>`它解析符号链接（如果符号链接的目标存在，则返回 true）。

+   -   检查哪个文件更新：`<file1> IS_NEWER_THAN <file2>`如果 file1 比 file2 更新（或相等），或者两个文件中有一个不存在，则返回 true。

+   -   检查路径是否为目录：`IS_DIRECTORY path-to-directory`

-   检查路径是否为符号链接：`IS_SYMLINK file-name`

检查路径是否为绝对路径：`IS_ABSOLUTE path`

### -   循环

CMake 中的循环相当直接，我们可以使用`while()`或`foreach()`来重复执行同一组命令。这两个命令都支持循环控制机制：

+   -   `break()`停止执行剩余的块并从封闭的循环中退出。

+   `continue()` 停止当前迭代的执行，并从下一次迭代的顶部开始。

#### -   While

-   循环块以`while()`命令打开，以`endwhile()`命令关闭。只要`while()`中提供的`<condition>`为 true，就会执行任何封闭的命令。表述条件的语法与`if()`命令相同。

```cpp
while(<condition>)
  <commands>
endwhile()
```

-   您可能已经猜到，通过一些额外的变量，while 循环可以替换 for 循环。实际上，使用`forach()`循环来做这件事要容易得多 - 让我们来看看。

#### -   Foreach

-   Foreach 块有几种变体，它们为每个值执行封闭的命令。与其他块一样，它有打开和关闭命令：`foreach()`和`endforeach()`。

-   最简单的 foreach 形式旨在提供 C++风格的 for 循环：

```cpp
foreach(<loop_var> RANGE <max>)
  <commands>
endforeach()
```

-   CMake 将从 0 迭代到`<max>`（包括）。如果我们需要更多控制，我们可以使用第二种变体，提供`<min>`，`<max>`和可选的`<step>`。所有参数必须是正整数。此外，`<min>`必须小于`<max>`。

```cpp
foreach(<loop_var> RANGE <min> <max> [<step>])
```

-   然而，`foreach()`在处理列表时才真正展现其能力：

```cpp
foreach(<loop_variable> IN [LISTS <lists>] [ITEMS <items>])
```

-   CMake 将从所有提供的`<lists>`后面跟着所有明确声明的`<items>`中取出项目，并将它们存储在`<loop variable>`中，为每个项目逐一执行`<commands>`。您可以选择只提供列表，只提供项目，或两者都提供：

#### chapter02/06-loops/foreach.cmake

```cpp
set(MY_LIST 1 2 3)
foreach(VAR IN LISTS MY_LIST ITEMS e f)
  message(${VAR})
endforeach()
```

-   这将打印

```cpp
1
2
3
e
f
```

-   或者使用简短版本（跳过`IN`）以获得相同的结果：

```cpp
foreach(VAR 1 2 3 e f)
```

-   自版本 3.17 起，`foreach()`已经学会了如何`ZIP_LISTS`：

```cpp
foreach(<loop_var>... IN ZIP_LISTS <lists>)
```

压缩列表意味着简单地遍历多个列表并对具有相同索引的相应项进行操作。让我们看一个例子：

#### 章节 02/06-循环/foreach.cmake

```cpp
set(L1 "one;two;three;four")
set(L2 "1;2;3;4;5")
foreach(num IN ZIP_LISTS L1 L2)
    message("num_0=${num_0}, num_1=${num_1}")
endforeach()
```

CMake 将创建`num_<N>`变量，每个提供的列表一个，它将用每个列表的项填充它们。你可以传递多个`<loop_var>`（每个列表一个） - 每个列表将使用一个单独的变量来存储其项：

```cpp
foreach(word num IN ZIP_LISTS L1 L2)
    message("word=${word}, num=${num}")
```

如果列表之间的项数不同 - CMake 不会为较短的列表定义变量。

这就是关于循环的所有内容。

### 命令定义

定义自己的命令有两种方法：使用`macro()`或`function()`命令。解释它们之间差异的最简单方法是将它们与 C 风格的预处理器宏和实际的 C++函数进行比较：

+   `macro()`更像是一个查找和替换指令，而不是像`function()`那样具有自己的调用堆栈入口的实际子程序调用。这意味着在宏中调用`return()`将返回到比函数调用高一级的调用语句（如果我们在顶层作用域中，可能会终止执行）。

+   只有`function()`为局部变量创建一个单独的作用域。`macro()`在调用作用域中工作 - 这可能导致令人困惑的结果。我们将在下一节讨论细节。

两种方法都接受参数，你可以在命令块内命名和引用这些参数。此外，CMake 允许你使用以下引用访问传递给命令调用的参数：

+   `${ARGC}` - 参数计数

+   `${ARGV}` - 所有参数的列表

+   `${ARG0}`，`${ARG1}`，`${ARG2}`… - 特定索引处的参数值

+   `${ARGN}` - 传递给最后一个预期参数之后的参数列表

访问超出`ARGC`边界的数字参数索引是未定义的行为。

如果你决定使用命名参数定义命令 - 每次调用都必须传递所有参数，否则将无效。

#### 宏

定义宏类似于任何其他块：

```cpp
macro(<name> [<argument>…])
  <commands>
endmacro()
```

在如此声明之后，我们可以通过调用其名称（函数调用不区分大小写）来执行我们的宏。

以下示例解释了宏中变量作用域的所有问题：

#### 章节 02/08-定义/macro.cmake

```cpp
macro(MyMacro myVar)
  set(myVar "new value")
  message("argument: ${myVar}")
endmacro()
set(myVar "first value")
message("myVar is now: ${myVar}")
MyMacro("called value")
message("myVar is now: ${myVar}")
```

这是该脚本的输出：

```cpp
$ cmake -P chapter02/08-definitions/macro.cmake
myVar is now: first value
argument: called value
myVar is now: new value
```

发生了什么？尽管明确地将`myVar`设置为`new value` - 但它并没有影响`message("argument: ${myVar}")`的输出！这是因为传递给宏的参数不被视为真正的变量，而是被视为常量的查找和替换指令。

另一方面 - 全局作用域中的变量`myVar`从`first value`更改为`new value`。这种行为被称为副作用，被认为是不良实践，因为很难判断哪些变量可能会受到这种宏的影响，而无需阅读它。

我建议尽可能使用函数。这很可能会为你节省很多头疼的问题。

#### 函数

要声明命令作为函数，请遵循此语法：

```cpp
function(<name> [<argument>…])
  <commands>
endfunction()
```

函数需要一个名称，并且可以选择性地接受一组必需的参数。如前所述——函数打开自己的作用域。您可以调用`set()`并提供函数的一个命名参数，更改将是局部的（除非指定了`PARENT_SCOPE`，正如我们在*变量：作用域*部分讨论的那样）。

函数遵循调用堆栈的规则，允许使用`return()`命令返回到调用作用域。

CMake 为每个函数设置以下变量（自版本 3.17 起可用）：

+   `CMAKE_CURRENT_FUNCTION`

+   `CMAKE_CURRENT_FUNCTION_LIST_DIR`

+   `CMAKE_CURRENT_FUNCTION_LIST_FILE`

+   `CMAKE_CURRENT_FUNCTION_LIST_LINE`

让我们来看看实际中的函数：

#### chapter02/08-definitions/function.cmake

```cpp
function(MyFunction FirstArg)
  message("Function: ${CMAKE_CURRENT_FUNCTION}")
  message("File: ${CMAKE_CURRENT_FUNCTION_LIST_FILE}")
  message("FirstArg: ${FirstArg}")
  set(FirstArg "new value")
  message("FirstArg again: ${FirstArg}")
  message("ARGV0: ${ARGV0} ARGV1: ${ARGV1} ARGC: ${ARGC}")
endfunction()
set(FirstArg "first value")
MyFunction("Value1" "Value2")
message("FirstArg in global scope: ${FirstArg}")
```

打印出以下输出：

```cpp
Function: MyFunction
File: /home/root/chapter02/08-definitions/function.cmake
FirstArg: Value1
FirstArg again: new value
ARGV0: Value1 ARGV1: Value2 ARGC: 2
FirstArg in global scope: first value
```

如您所见，函数的一般语法和概念与宏非常相似，但这次——它确实有效。

#### CMake 中的过程范式

让我们想象一下，如果我们想像编写 C++程序那样编写 CMake 代码。我们将有一个`CMakeLists.txt`列表文件，它将调用三个已定义的命令，这些命令可能调用它们自己的已定义命令。

![图 2.3：过程调用图](img/file7.png)

图 2.3：过程调用图

在 CMake 中以这种过程式风格编写代码存在一些问题：您需要提前提供计划使用的命令定义。CMake 解析器不会接受其他方式。您的代码可能看起来像这样：

```cpp
cmake_minimum_required(...)
project(Procedural)
function(pull_shared_protobuf)
function(setup_first_target)
function(calculate_version)
function(setup_second_target)
function(setup_tests)
setup_first_target()
setup_second_target()
setup_tests()
```

真是噩梦！一切都颠倒了！这段代码非常难以阅读，因为最微小的细节都位于文件的顶部。结构良好的代码首先列出最一般的步骤，然后提供更详细的子程序，并将最详细的步骤推到文件的末尾。

有解决这个问题的方法：将命令定义移动到其他文件，跨目录划分作用域。但也有一个简单而优雅的方法：在文件顶部声明一个入口点宏，并在文件的最后调用它：

```cpp
macro(main)
function(...) # key steps
function(...) # details
function(...) # fine details
main()
```

采用这种方法，我们的代码是按照逐渐缩小的作用域编写的，而且由于我们实际上直到最后才调用`main()`宏——CMake 不会抱怨执行未定义的命令！

最后一个问题仍然存在：为什么要使用宏而不是推荐的功能？在这种情况下，拥有对全局变量的不受限制的访问是很好的，而且由于我们没有向主函数传递任何参数——我们不必担心通常的注意事项。

您可以在`chapter-02/09-procedural/CMakeLists.txt`列表文件中找到这个概念的一个简单示例。

#### 关于命名的一点说明

命名在软件开发中是出了名的难，但仍然非常重要，以维护易于阅读和理解的解决方案。当涉及到 CMake 脚本和项目时，所有干净代码的规则都适用，就像它们适用于正常的软件开发解决方案一样：

+   遵循一致的命名风格（`snake_case`是 CMake 社区接受的规范）。

+   使用简短但有意义的名称（避免使用`func()`、`f()`等）。

+   避免在命名中使用双关语和机智。

+   使用可发音的、可搜索的名称，不需要心理映射。

既然我们已经知道如何正确调用命令并使用正确的语法，那么让我们找出哪些命令将是最有益的开始。

## 有用的命令

CMake 提供了许多脚本命令，允许你使用变量和环境。其中一些在*附录*中得到了广泛介绍：`list()`、`string()`、`file()`（为了避免在我们通往项目的路上拖慢我们）。其他的，如`find_...()`更适合在讨论管理依赖关系的章节中。在本节中，我们将简要介绍脚本中最有用的命令。

### message()

我们已经知道并喜欢我们可靠的`message()`命令，它将文本打印到标准输出。然而，它远不止于此。通过提供一个`MODE`参数，你可以自定义输出的样式，并且在出现错误时停止代码的执行：`message(<MODE> "text")`。

识别的模式：

+   `FATAL_ERROR` - 停止处理和生成。

+   `SEND_ERROR` - 继续处理，但跳过生成。

+   `WARNING` - 继续处理

+   `AUTHOR_WARNING` - CMake 警告（开发中），继续处理。

+   `DEPRECATION` - 如果启用了变量`CMAKE_ERROR_DEPRECATED`或`CMAKE_WARN_DEPRECATED`，则相应地工作。

+   `NOTICE` 或（无）- 消息打印到 stderr 以吸引用户的注意。

+   `STATUS`

+   `VERBOSE`

+   `DEBUG`

+   `TRACE`

以下示例在第一个消息后停止执行：

#### chapter02/10-useful/message_error.cmake

```cpp
message(FATAL_ERROR "Stop processing")
message("Won't print this.")
```

消息将根据当前的日志级别（默认为`STATUS`）打印。我们在上一章的*选项：调试与跟踪*部分讨论了如何更改它。然后我承诺会讨论使用`CMAKE_MESSAGE_CONTEXT`进行调试 - 让我们开始吧。从那时起，我们获得了这个难题的三个重要部分：列表、作用域和函数。

当我们启用命令行标志`cmake --log-context`时，我们的消息将用点分隔的上下文装饰，该上下文存储在`CMAKE_MESSAGE_CONTEXT`列表中。考虑以下示例：

#### chapter02/10-useful/message_context.cmake

```cpp
function(foo)
  list(APPEND CMAKE_MESSAGE_CONTEXT "foo")
  message("foo message")
endfunction()
list(APPEND CMAKE_MESSAGE_CONTEXT "top")
message("Before `foo`")
foo()
message("After `foo`")
```

上述脚本的输出将如下所示：

```cpp
$ cmake -P message_context.cmake --log-context
[top] Before `foo`
[top.foo] foo message
[top] After `foo`
```

函数的初始作用域是从父作用域复制的（父作用域已经在列表中有一个项：`top`）。`foo`中的第一个命令向`CMAKE_MESSAGE_CONTEXT`添加了一个新项，其名称为`foo`。消息被打印出来，函数作用域结束，丢弃了本地复制的变量，以及之前的（没有`foo`）作用域被恢复。

这种方法在非常复杂的项目中，有许多嵌套函数时非常有用。希望你永远不需要它，但我认为这是一个很好的例子，说明了函数作用域在实践中是如何工作的。

`message()`的另一个很酷的技巧是向`CMAKE_MESSAGE_INDENT`列表添加缩进（与`CMAKE_MESSAGE_CONTEXT`完全相同）：

```cpp
list(APPEND CMAKE_MESSAGE_INDENT "  ")
```

我们的脚本输出看起来会更清晰一些：

```cpp
Before `foo`
  foo message
After `foo`
```

由于 CMake 没有提供任何真正的带有断点或其他工具的调试器，因此能够生成清晰的日志消息在事情没有完全按计划进行时非常有用。

### include()

我们可以将我们的 CMake 代码分成单独的文件，以保持事物的有序和…嗯，分开。然后我们可以通过调用`include()`从我们的父列表文件中引用它们，如下所示：

```cpp
include(<file|module> [OPTIONAL] [RESULT_VARIABLE <var>])
```

如果我们提供了一个带有`.cmake`扩展名的文件名（路径），CMake 将尝试打开并执行它。请注意，不会创建嵌套的、独立的范围，因此该文件中对变量所做的任何更改都会影响调用范围。

CMake 会在文件不存在的情况下抛出错误，除非我们指定它是`OPTIONAL`。如果我们需要知道包含是否成功，我们可以提供一个`RESULT_VARIABLE`关键字和一个变量名。如果成功，它将被填充为包含文件的完整路径，或者在失败时填充为`NOTFOUND`。

在脚本模式下运行时，任何相对路径都将从当前工作目录解析。要强制在相对于脚本本身的位置搜索，请提供一个绝对路径：

```cpp
include("${CMAKE_CURRENT_LIST_DIR}/<filename>.cmake") 
```

如果我们不提供路径，而是提供一个模块的名称（不带`.cmake`或其他后缀），CMake 将尝试找到该模块并包含它。CMake 会在`CMAKE_MODULE_PATH`中搜索名为`<module>.cmake`的文件，然后在 CMake 模块目录中搜索。

### include_guard()

当我们包含具有副作用的文件时，我们可能希望限制它们只被包含一次。这时`include_guard([DIRECTORY|GLOBAL])`就派上用场了。

在包含的文件顶部放置`include_guard()`。当 CMake 第一次遇到它时，它会在当前范围内记录这一事实。如果文件再次被包含（可能是因为我们不控制项目中的所有文件），它将不会被进一步处理。

如果我们想防止在彼此不嵌套的范围内包含，我们应该提供`DIRECTORY`或`GLOBAL`参数。顾名思义，`DIRECTORY`保护将适用于当前目录及其下，而`GLOBAL`适用于整个构建。

### file()

为了给你一个关于 CMake 脚本可以做什么的提示，让我们快速看一下文件操作命令的最有用变体：

```cpp
file(READ <filename> <out-var> [...])
file({WRITE | APPEND} <filename> <content>...)
file(DOWNLOAD <url> [<file>] [...])
```

简而言之，`file()`命令将允许你以系统独立的方式读取、写入、传输文件，处理文件系统、文件锁、路径和存档；所有这些都在系统独立的方式下进行。详情请参阅*附录*。

### execute_process()

有时你将需要求助于系统中可用的工具（毕竟 CMake 主要是一个构建系统生成器）。CMake 为此提供了一个命令：你可以使用`execute_process()`来运行其他进程并收集它们的输出。这个命令非常适合脚本，也可以在项目配置阶段使用。这里是一般形式：

```cpp
execute_process(COMMAND <cmd1> [<arguments>]… [OPTIONS])
```

CMake 将使用操作系统的 API 来创建一个子进程（因此像`&&`、`||`和`>`这样的 shell 操作符将不起作用）。然而，你仍然可以通过多次提供`COMMAND <cmd> <arguments>`参数来链接命令并将一个命令的输出传递给另一个。

你可以选择使用`TIMEOUT <seconds>`来终止进程，如果它在限定时间内未完成任务，并根据需要设置`WORKING_DIRECTORY <directory>`。

所有任务的退出代码可以通过提供`RESULTS_VARIABLE <variable>`参数来收集到一个列表中。如果你只对最后一个执行的命令的结果感兴趣，请使用单数形式：`RESULT_VARIABLE <variable>`。

为了收集输出，CMake 提供了两个参数：`OUTPUT_VARIABLE`和`ERROR_VARIABLE`，使用方式类似。如果你想要合并`stdout`和`stderr`，可以使用同一个变量作为这两个参数。

记住，当你为其他用户编写项目时，你应该确保你计划使用的命令在你声称支持的平台上可用。

## 总结

本章打开了使用 CMake 进行实际编程的大门——你现在能够编写出色的、信息丰富的注释；调用内置命令，并理解如何正确地向它们提供各种参数。仅凭这些知识，就能帮助你理解在其他项目中可能见到的 CMake listfiles 的略显奇特的语法。

接下来，我们介绍了 CMake 中的变量：如何引用、设置和取消普通、缓存和环境变量。我们深入探讨了目录和函数作用域的工作原理，以及嵌套作用域的问题（及其解决方法）。

我们还介绍了列表和控制结构。我们讨论了条件的语法、逻辑操作、未加引号的参数的评估、字符串和变量。我们学会了如何比较值、进行简单检查以及检查系统中文件的状态。这使我们能够编写条件块和 while 循环。说到 while 循环，我们也掌握了 foreach 的语法。

我相信，知道如何使用宏和函数语句定义自己的命令将帮助你编写更简洁的代码，并以更过程化的风格编写。我们还分享了一些关于如何更好地组织我们的代码和想出更具可读性的名称的提示。

最后，我们正式介绍了 message()及其多个日志级别。我们还研究了如何划分和包含 listfiles，并发现了一些其他有用的命令。我确信，有了这些材料，我们已准备好迎接下一章，并编写我们的第一个 CMake 项目。

## 进一步阅读

你可以参考以下内容以获取更多信息：

+   [《代码整洁之道：程序员的敏捷软件工艺手册》](https://amzn.to/3cm69DD)（Robert C. Martin）

+   [重构：改善现有代码的设计](https://amzn.to/3cmWk8o)（Martin Fowler）

+   [你的代码中哪些注释是好的？](https://youtu.be/4t9bpo0THb8)（Rafał Świdzinski）

+   StackOverflow - CMake 语法设置和使用变量：[`stackoverflow.com/questions/31037882/whats-the-cmake-syntax-to-set-and-use-variables`](https://stackoverflow.com/questions/31037882/whats-the-cmake-syntax-to-set-and-use-variables)
