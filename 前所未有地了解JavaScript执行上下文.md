# 前所未有地了解JavaScript执行上下文


##### By [Tapas Adhikary](https://hashnode.com/@atapas) | Jun 10, 2020

[原文](https://blog.greenroots.info/understanding-javascript-execution-context-like-never-before-ckb8x246k00f56hs1nefzpysq)

## 介绍

执行上下文是JS最基本的组成部分。在本文中，我们将深入研究该概念，以发现它不仅是基础知识，而且非常容易理解。
从学习角度的理解和复杂性来看，在许多情况下，执行上下文的概念已被认为是JS深层次概念。
是的，如果不脚踏实地的学习JS基础，执行上下文的概念听起来可能很复杂。随之而来的是，每个JavaScript初学者都需要理解，
为什么对这个基本概念充满信心很重要？

这是该系列的第二篇文章，系列的名字叫《[JavaScript: Cracking the Nuts](https://hashnode.com/series/javascript-cracking-nuts-ckawn7vc5007dl6s164ogqplz)》。如果你尚未阅读该系列的第一篇文章，请尝试一下。 希望你喜欢阅读。

## 为什么这个概念很重要

一项研究说，人脑也许能够在其记忆中保存与整个互联网上一样多的信息。但是我们不应该认为这是理所当然的，对吧？
因此，一个有效的问题可能是，为什么这个概念对学习很重要？

JS的执行上下文是正确理解JS其他基础概念的基础。通常，相关概念中我们都会发现很多误解，
这是因为我们误解了执行上下文的运行机制。

* Hoisting（译者注：提升）
* Scope（译者注：作用域）
* Scope Chain（译者注：作用域链）
* Closure（译者注：闭包）
* Event Loop（译者注：事件循环）

作为JavaScript开发人员，一旦我们对这些概念有了很好的理解，我们就能使自己

* 不容易写bug
* 在代码审查中成为出色的审查员
* 擅长查bug
* 更擅长解决线上问题

## 不要不知所措

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591159946674/3zHAzDROQ.gif?auto=format,compress&gif-q=60)
上面图片出自 https://giphy.com/

我不确定为什么大多数编程语言都充满了繁琐的术语，有时可能会令人沮丧。这些术语背后的基本概念通常更容易理解。
以下是一些对这篇文章有用的信息：

* 解析器：解析器或语法解析器可逐行读取你的代码并了解其如何适合编程语言定义的语法以及其预期功能。正如我上一篇文章中所说，JS语法解析器采用令牌数组并创建一个抽象语法树（AST），以便可以对其进行进一步处理以创建可执行代码。
* 词法环境：词法的意思是一种语言的单词或词汇。词法环境是指代码是如何和在哪个地方被编写的。让我们以这段代码为例：

            function sayName() {
                var name = 'someName';
                console.log('The name is, ', name);
            }

    从上面的代码可以看到，name变量词法上是在sayName方法里面定义的。现在，需要特别注意的是，你的程序不会原样在机器上跑。它必须由编译器编译。因此，它必须正确地知道和映射词法上定义的变量的位置，以便编译后的输出也有意义且有效。请注意，通常，你的代码中将包含许多词法环境。 但是不会立即执行所有环境。 我们很快就会了解到。
* 上下文：将上下文这个概念可视化的最佳方法是，围绕你感兴趣的主题（或我们正在讨论的“上下文”）围成一个圆圈（或包装）。上下文是围绕特定事件，情况等的一组情况或事实。
* 执行上下文：这意味着，当前正在运行的代码及其周围的所有内容都有助于运行它。可能有很多词法环境可用，但是，当前正在运行的词法环境由执行上下文管理。以下是实例图片：![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591094842640/tQOPPAOPu.gif?auto=format,compress&gif-q=60)
    词法环境 vs 执行上下文
	
## 执行上下文

作为软件开发人员，我们喜欢（或希望）以这样一种方式来编写代码：看起来不太复杂，可以轻松维护，并遵循一些实践，标准等。执行上下文的设计目的也是类似的，执行上下文允许JS引擎以更好的方式维护代码并管理复杂性。
每当代码在JavaScript中运行时，它就会在执行上下文中运行，该上下文是您的代码以及JavaScript引擎完成的所有工作（标记化，解析，代码生成等）的组合。
	
## 全局执行上下文（GEC）

全局执行上下文也称为基本执行上下文。它提供了两个特别的东西。

* 全局对象，如果在浏览器环境就是Window，如果是在node环境，就是global。
* 全局变量，this

让我们用两个例子来理解GEC

### 加载一个空脚本

为了简单起见，请使用一个名为index.js的空JavaScript文件，并将其导入一个名为index.html的html文件中，如下所示：

        <html>
            <head>
                <script src="index.js" />
            </head>

            <body>
                I load an empty Script
            </body>
        </html>

在浏览器中加载此HTML后，将不会加载并执行任何JavaScript，但是，如果打开调试器控制台（Chrome浏览器为F12）并输入该代码，

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591255177028/a9BvMY1oL.png?auto=compress)

你可以看见已经为你创建了this变量了，你也可以尝试输入Window，这一次将打印Window对象，

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591181570257/QN_BiTIuw.png?auto=compress)

是否注意到，在全局执行上下文中，Window对象和this变量相等？ 试试这个来确认，

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591255250562/YNwwj6zgE.png?auto=compress)

没有任何JavaScript代码的全局执行上下文可以显示为：

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591181755156/28U6pwTeR.png?auto=compress)

### 具有变量和函数

现在让我们将一些代码添加到我们的js文件中。我们添加了一个名为name的变量，并使用值Tom对其进行了初始化。我们还有一个名为sayName的函数，它仅打印name。

    var name = 'Tom';

    function sayName() {
        console.log(this.name); 
    }

您认为现在GEC会发生什么？让我们首先看看下面的图片演示，然后再解释。

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591709303093/CAhuK5gWl.gif?auto=format,compress&gif-q=60)

GEC阶段

* 在全局执行上下文中有两个阶段，即创建阶段和执行阶段。
* 创建阶段
    * 在此阶段，将创建两个特殊的东西，一个全局对象（例如用于浏览器的Window）和一个名为this的变量。
    * 为变量name和函数sayName分配内存。
    * 变量name被初始化为undefined，函数sayName被直接放置到内存中。在即将发布的文章（译者注：作者安利他的文章，变量提升在这不讲）中，我们将进一步了解有关提升的另一种概念。
* 执行阶段
    * 在此阶段，代码实际上开始执行。对于上面的示例，唯一发生的事情是，它将值Tom分配给变量name。请注意，尽管已定义函数，但我们没有调用它。因此，该函数将不会被执行。在下一节中，我们将介绍函数执行。

## 函数执行上下文（FEC）

调用函数时会创建一个函数执行上下文。
让我们看下面的例子来理解这个概念。在下面的示例中，我们有一个名为name的全局变量，该变量被分配了一个名为Tom的值。我们有一个名为tom的函数，用于打印name。最后，我们调用函数tom。

        var name = 'Tom';
        function tom() {
            console.log(this.name + ' Runs');
        }
        // Invoke the function tom()
        tom();

参阅以下演示，以一起了解函数执行上下文和全局执行上下文。

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1591100827047/mE1WCYtrt.gif?auto=format,compress&gif-q=60)

* 正如我们在上面看到的，全局执行上下文的创建阶段创建了全局对象，this变量，为变量和函数创建了一个内存。变量被初始化为undefined这个特殊值。
* 在执行阶段，它将值分配给变量并调用函数。因此，创建了函数执行上下文。
* 函数执行上下文经历相同的阶段，即创建和执行。需要注意的重要一点是，函数执行上下文可以访问一个称为arguments的特殊变量，这是调用时传递给函数的参数。 在我们的示例中，我们不传递任何参数。因此，arguments长度为0。
* 一个函数可以调用另一个函数，另一个函数还可以调用另一个，依此类推。对于每个函数调用，都会创建一个函数执行上下文。我们将在即将发表的文章中详细介绍这个Scope概念。

## 那么，全局和功能执行上下文之间有什么区别？

全局上下文-----------函数上下文
* 创建全局对象-----------不创建全局对象。 它创建一个arguments对象。
* 创建this变量-----------默认情况下，它指向Window对象（还有很多，我们将在以后的文章中看到）。
* 为全局定义的变量和函数设置内存-----------为函数内定义的变量和函数设置内存。
* 将所有函数声明放入内存时，为变量声明分配默认值undefined-----------将任何函数声明放入内存时，为变量声明分配默认值undefined。 另外，函数执行上下文创建其自己的执行堆栈。

## 结论

全局和函数执行上下文以及阶段（创建和执行）是要理解的重要概念。这将有助于轻松理解作用域，异步，闭包，变量提升等。在本系列的后续文章中，我们将详细介绍每个概念。

如果您不熟悉执行上下文的概念，请在学习此概念的同时并排使用JavaScript Visualizer Tool。 编写小函数，用户定义的变量，执行函数，并查看工具如何在执行上下文的各个阶段使用它。