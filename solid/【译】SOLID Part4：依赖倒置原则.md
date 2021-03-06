# 【译】SOLID Part 4：依赖倒置原则

[原文地址](https://code.tutsplus.com/tutorials/solid-part-4-the-dependency-inversion-principle--net-36872)

作者：Patkos Csaba

> 这篇博客是 [SOLID 原则](http://blog.csdn.net/u013538630/article/details/53143277) 的一部分
>
> [<< SOLID: Part 3 - 里氏代换原则 & 接口隔离原则](http://blog.csdn.net/u013538630/article/details/53887848)

[单一职责（SRP）](http://blog.csdn.net/u013538630/article/details/53118717)，[开闭原则](http://blog.csdn.net/u013538630/article/details/53151962)，[里氏代换原则，接口隔离原则](http://blog.csdn.net/u013538630/article/details/53887848)以及依赖倒转原则。在编程的过程中应当牢记这五种敏捷原则。

​	谈论 SOLID 原则中那个更重要是不公平的。但是可能没有哪个能比依赖倒置原则（下面简称 DIP）对你的代码造成更多的影响。如果你发现其它的原则难以运用，那就从依赖倒置原则开始，

## 定义

> A. 高层模块不应该依赖低层模块。两者都应该依赖于抽象
>
> B. 抽象不应该依赖于细节。

​	[Robert C. Martin](http://www.8thlight.com/our-team/robert-martin) 在他的书 [Agile Software Development, Principles, Patterns, and Practices](http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445/ref=sr_1_1?s=books&ie=UTF8&qid=1378755964&sr=1-1&keywords=robert+c+martin) 中定义了这个原则。它是 SOLID 原则的最后一个。



## 现实世界中的 DIP

​	在开始写代码之前，我想告诉你一个故事。我们在 Syneto 工作的时候并不是从一开始就很关心我们的代码质量。几年前我们知道的不多并且觉得自己已经尽力了，我们的一些工程也做的不怎么样。我们来来回回的失败和尝试。

​	Uncle Bob（Robert C. Martin）的 SOLID 原则和整洁架构改变了我们的工作方式，将我们的代码从一种难以名状的状态拯救了回来。我将会通过讨论伴以例证的方式证明 DIP 能够带给你的进步。

​	大多数网页工程包含三个主要的技术：HTML，PHP 和 SQL。具体是哪个版本和哪种数据库和我们讨论的问题没有关系。我们关注的是网页上显示的内容来自数据库，两者通过 PHP 交互。

​	首先我们需要知道一些基础的概念，这三种技术代表了架构上的三个层面：UI 界面，业务逻辑以及持久层。我们将简单的讨论一下各个层的含义。现在，让我们看一些奇怪但是经常遇到的让这几种技术协同工作的方案。

​	我见过很多工程在 HTML 的 PHP 标签里写 SQL 语句，或者是用 PHP 输出页面，然后在页面里读取 `$_GET` 和 `$_POST`。但是为什么这种写法是错的？

![](https://cdn.tutsplus.com/net/uploads/2014/02/html-php-sql-cross-dependencies.png)

​	上图形象的展示了刚刚讨论的情况。箭头代表显示的依赖，可以这么说，各个组件都依赖了其它所有组件。如果我们改变了数据库里的一张表，我们可能连 HTML 文件都要改。或者改变了 HTML 的一个 field，结果是要给数据库中的一个列改名字。再看第二张图表，我们需要改 PHP 文件如果 HTML 变了，在一些糟糕的情况下，当我们将 HTML 的内容都放在 PHP 文件里时，我们肯定要通过修改 PHP 文件来改变网页内容。毫无疑问的是，依赖存在于类和模块之间。

![](https://cdn.tutsplus.com/net/uploads/2014/02/html-php-sql-stored-procedures.png)

​	在上面这张图中，数据库查询返回了由数据库数据生成的 PHP 代码。这段代码执行了其他的数据库查询，也返回了一段 PHP 代码，这个循环不断继续知道最后获得了一些数据，可能是 UI。

​	我知道这种方式对于大部分人来说听起来有点离谱，但是在你将来的职业生涯中一定会遇到用这种方式构建的工程。大部分现有的工程，不管它用的是哪种语言，都是一些不关心或者不知道怎么做更好的程序员按照旧的原则来做的。如果你读完了这些教程，你将会站在一个更高的层次。你做好了准备面对你的工作，写出更好的代码。

​	另一种选择时重复前辈犯的错误，一直就这么下去。在 Syneto 工作的时候，当我们的一个工程由于它老旧的交叉依赖架构到了一个没法控制的地步时，我们不得不彻底地抛弃它。那时我们决定永远都不要在走回这条路。从那时开始，我们努力实现遵循了 SOLID 原则，特别是实现了依赖倒置的简洁架构。

![HighLevelDesign](https://cdn.tutsplus.com/net/uploads/2014/02/HighLevelDesign.png)

这个架构让人感到惊叹的是它依赖的指向：

- 用户接口（大多数情况下是 MVC 框架）或者其它的传递机制，这些都依赖于业务逻辑。业务逻辑是很抽象的一个东西。用户接口很具体。UI 只是工程的一个细节，并且它很易变。没有什么应该依赖 UI 或者依赖你的 MVC 框架。
- 另一个有趣的发现是你的持久化数据和数据库依赖于业务逻辑。你的业务逻辑是不应该知道数据库的。这允许你按照你的意愿修改持久层。如果明天你想将 MySql 换成 PostgreSQL 或者纯文本，那你就能够这样的。当然了，你得实现一个特定的持久层，但是你业务逻辑一行代码都不用改。这里有一个关于持久数据的更详细的教程 [Evolving Toward a Persistence Layer](http://code.tutsplus.com/tutorials/evolving-toward-a-persistence-layer--net-27138)
- 最后，在业务逻辑的右边，是我们所有关于创建业务逻辑的类。这些是在程序入口创建的类和类的工厂。很多人认为这些应该属于业务逻辑，但是它们唯一的职责就是创建了业务对象。它们的就是帮助我们创建别的类的类。它们创建的业务对象和工厂本身是相互独立的。我们也可以使用别的模式，比如简单工厂等。这都不重要，只要业务对象生成后能工作就好了。

# 看代码

​	 如果你已经学过了课程 [agile design patterns](https://tutsplus.com/course/agile-design-patterns/)，你就会发现在架构层面应用依赖导致原则是一件很简单的事。同时在业务逻辑里检测它也是一件简单且有趣的事。让我们考虑一个电子书阅读器程序。

```php
class Test extends PHPUnit_Framework_TestCase {
 
    function testItCanReadAPDFBook() {
        $b = new PDFBook();
        $r = new PDFReader($b);
 
        $this->assertRegExp('/pdf book/', $r->read());
    }
 
}
 
class PDFReader {
 
    private $book;
 
    function __construct(PDFBook $book) {
        $this->book = $book;
    }
 
    function read() {
        return $this->book->read();
    }
 
}
 
class PDFBook {
 
    function read() {
        return "reading a pdf book.";
    }
}
```

​	我们开始开发一个 PDF 阅读器。当目前位置一切都没什么问题。我们有了一个使用 `PDFBook` 的`PDFReader`。 它的方法 `read()`  调用了书籍的 `read()` 方法。我们只是对 `PDFBook` 的 `reader()` 方法的返回值做了一个简单的正则校验。

​	请记住这只是一个例子。我们将不会真的去实现 PDF 文件的读取逻辑。这就是为什么我们只简单的检验一部分字符串。如果我们写的是真的应用，唯一区别就是怎么验证文件类型。我们程序的依赖模型是非常简单的。

![pdfreader-pdfbook](https://cdn.tutsplus.com/net/uploads/2014/02/pdfreader-pdfbook.png)

​	一个 PDFReader 使用 PDFBook，这看上去是一个合理的方案，如果我们的应用只是用来阅读 PDF 的话。但是我们想写的是一个通用的电子书阅读器，支持包括 PDF 在内的多种格式。现在让我们重命名一下我们的类。



```php
class Test extends PHPUnit_Framework_TestCase {
 
    function testItCanReadAPDFBook() {
        $b = new PDFBook();
        $r = new EBookReader($b);
 
        $this->assertRegExp('/pdf book/', $r->read());
    }
 
}
 
class EBookReader {
 
    private $book;
 
    function __construct(PDFBook $book) {
        $this->book = $book;
    }
 
    function read() {
        return $this->book->read();
    }
 
}
 
class PDFBook {
 
    function read() {
        return "reading a pdf book.";
    }
}
```

重命名没有影响到函数内容，测试依然没问题。

```
Testing started at 1:04 PM ...
PHPUnit 3.7.28 by Sebastian Bergmann.
Time: 13 ms, Memory: 2.50Mb
OK (1 test, 1 assertion)
Process finished with exit code 0
```

但是对于设计产生了严重的影响

![ebookreader-pdfbook](https://cdn.tutsplus.com/net/uploads/2014/02/ebookreader-pdfbook.png)

​	我们的阅读器变得更抽象了。但是我们的通用类 `EbookReader` 仍然在使用 `PDFBook`。在这里抽象依赖具体。事实上我们的 PDF 应该只是一个实例，没有哪个应该依赖它。

```php
class Test extends PHPUnit_Framework_TestCase {
 
    function testItCanReadAPDFBook() {
        $b = new PDFBook();
        $r = new EBookReader($b);
 
        $this->assertRegExp('/pdf book/', $r->read());
    }
 
}
 
interface EBook {
    function read();
}
 
class EBookReader {
 
    private $book;
 
    function __construct(EBook $book) {
        $this->book = $book;
    }
 
    function read() {
        return $this->book->read();
    }
 
}
 
class PDFBook implements EBook{
 
    function read() {
        return "reading a pdf book.";
    }
}
```

​	最常见的用来反转依赖关系的解决方案是在我们的工程里引入更多的抽象模块。“在 OOP 里最抽象的组件是接口。所以，任何类都可以依赖接口同时遵循 DIP”。

​	让我们为我们的读者创建接口。这个接口命名为  `EBook`，它是 `EBookReader` 需要的东西。这是为了实现 [接口隔离原则](http://blog.csdn.net/u013538630/article/details/53887848) 的直接结果，促进了接口应该反映用户需求的想法。接口是提供给用户使用的，所以它们的命名应该能反映用户的需求并包含用户需要的方法。那么很自然的，对于一个 `EBookReader` 需要一个具有 `read()` 方法的 `EBookReader` 接口。

![](https://cdn.tutsplus.com/net/uploads/2014/02/ebookreader-ebookinterface-pdfbook.png)

我们现在有两个依赖关系了。

- 第一个依赖从 `EBookReader` 指向 `EBook` 接口，它的类型是应用，`EBookReader` 使用了 `EBook`。

- 第二个有所不同，它从 `PDFBook` 指向了 `EBook`，它的类型是实现。`PDFBook` 仅仅只是 `EBook` 的一种特殊类型，所以接口的实现是为了满足用户的需求。

  ​这个解决方案允许我们将不同类型的电子书插到我们的阅读器里去，唯一的条件就是所有的电子书都要实现 `EBook` 接口。

```PHP
class Test extends PHPUnit_Framework_TestCase {
 
    function testItCanReadAPDFBook() {
        $b = new PDFBook();
        $r = new EBookReader($b);
 
        $this->assertRegExp('/pdf book/', $r->read());
    }
 
    function testItCanReadAMobiBook() {
        $b = new MobiBook();
        $r = new EBookReader($b);
 
        $this->assertRegExp('/mobi book/', $r->read());
    }
 
}
 
interface EBook {
    function read();
}
 
class EBookReader {
 
    private $book;
 
    function __construct(EBook $book) {
        $this->book = $book;
    }
 
    function read() {
        return $this->book->read();
    }
 
}
 
class PDFBook implements EBook {
 
    function read() {
        return "reading a pdf book.";
    }
}
 
class MobiBook implements EBook {
 
    function read() {
        return "reading a mobi book.";
    }
}
```

​	这么做反过来帮助我们实现了 [开闭原则](http://blog.csdn.net/u013538630/article/details/53151962)。

开闭原则是一个能够帮助我们实现其它原则的原则。遵循 DIP 将会：

- 几乎强迫你完成 OCP。
- 允许你分离职责。
- 让你正确的使用子类。
- 让你有机会分离接口。

## 最后的思考

​	我们已经讲完了所有的东西了，所有关于 SOLID 原则的教程都在这儿了。对于我个人来说，发现并且在工程中实现这些原则是一个很大的进步。我曾经关于设计和架构的观念被彻底推翻，并且我可以说从那时开始我做的工程变得越来越易于管理和理解。

​	我认为 SOLID 原则是面向对象编程中最基础的概念之一。这些概念一定会让我们的代码更加完善并且让我们作为一个程序员过得更轻松。设计合理的代码更容易理解。电脑很聪明，不管你的代码多复杂它都能理解，但是人不一样，你能牢记在脑子里的东西是有限的。 更具体的说，能记住的东西的数量是 [The Magical Number Seven, Plus or Minus Two](http://www.musanim.com/miller1956/)。

​	我们应该尽力限制我们代码的架构数量，这里有很多方法帮助我们完成这个目标。方法不超过四行（算上定义5行），这样我们一下就能看完它。大括号层数不要超过5层。一个类不要超过9个方法。我们的图表中的高层架构用了四到五个概念。我们介绍了5个 SOLID 原则，每一个需要5 - 9 个子概念/模块/类来例证。一个团队的合理人数是5-9人。一个公司里合适的团队数是5-9。

​	你也看到了，7是一个多么神奇的数字，上下加减2就在我们的身边，那么你的代码有什么理由不一样。	


