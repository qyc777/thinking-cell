# ASP.NET CORE MVC 2 介绍 

对于Microsoft平台上的Web开发人员来说，使用ASP.NET Core MVC是一次彻底的转变。它强调整洁的架构，设计模式和可测试性，并且不会试图隐藏Web的工作方式。 

ASP.NET Core MVC is a radical shift for web developers using the Microsoft platform. It emphasizes clean architecture, design patterns, and testability, and it doesn’t try to conceal how the Web works. 

本书的第一部分旨在帮助你广泛理解MVC开发，包括 ASP.NET Core MVC中的新功能，以及在实践中体验框架的使用方式。

The first part of this book is designed to help you understand broadly the foundational ideas of MVC development, including the new features in ASP.NET Core MVC, and to experience in practice what the framework is like to use.

# 所谓ASP.NET Core MVC

ASP.NET Core MVC是一个来自Microsoft的Web应用程序开发框架，它结合了 ”模型/视图/控制器“（MVC）体系结构的有效性和整洁性、敏捷开发的观念以及.NET平台的最佳部分。本章将介绍Microsoft创建ASP.NET Core MVC的原因、它与其前身、竞争框架的比较，以及在ASP.NET Core MVC中的新增的内容。

ASP.NET Core MVC is a web application development framework from Microsoft that combines the effectiveness and tidiness of model-view-controller (MVC) architecture, ideas and techniques from agile development, and the best parts of the .NET platform. In this chapter, you’ll learn why Microsoft created ASP.NET Core MVC, see how it compares to its predecessors and alternatives, and, finally, get an overview of what’s new in ASP.NET Core MVC and what’s covered in this book.

## ASP.NET Core MVC的发展历史

2002年，微软推出了初代ASP.NET，当时，微软热衷于保护传统桌面应用程序开发的主导地位，并将互联网的蓬勃发展视为威胁。 当时微软的技术栈如下图所示所示：

// TODO 1-1

The original ASP.NET was introduced in 2002, at a time when Microsoft was keen to protect a dominant position in traditional desktop application development and saw the Internet as a threat. Figure 1-1 illustrates Microsoft’s technology stack as it appeared then.

### ASP.NET Web Forms

在Web Forms中，Microsoft试图通过将UI建模为具有层次结构的服务器端对象，来隐藏无状态的HTTP以及当时许多开发人员并不熟悉的HTML。每个控件都会追踪各个请求的状态，在需要时将其渲染为HTML，并自动地将客户端事件（比如点击按钮）与相应的服务器端事件处理代码连接起来。实际上，Web Forms是一个旨在于在Web上也提供传统的事件驱动型GUI的巨大抽象层。

With Web Forms, Microsoft attempted to hide both Hypertext Transfer Protocol (HTTP), with its intrinsic statelessness, and Hypertext Markup Language (HTML), which at the time was unfamiliar to many developers, by modeling the user interface (UI) as a hierarchy of server-side control objects. Each control kept track of its own state across requests, rendering itself as HTML when needed and automatically connecting client-side events (for example, a button click) with the corresponding server-side event handler code. In effect, Web Forms is a giant abstraction layer designed to deliver a classic event-driven graphical user interface (GUI) over the Web.

这种做法试图让开发人员在Web上也能拥有开发桌面应用一般的体验。开发人员可以基于有状态的UI进行思考，而不需要处理一系列独立的HTTP请求和响应。因此，微软可以无缝地使Windows桌面开发人员转变成Web应用开发人员。

The idea was to make web development feel just the same as developing a desktop application. Developers could think in terms of a stateful UI and didn’t need to work with a series of independent HTTP requests and responses. Microsoft could seamlessly transition the army of Windows desktop developers into the new world of web applications.

#### ASP.NET Web Forms做错了什么？

原则上来说，ASP.NET Web Forms并不坏，但现实却没有这么简单。

Traditional ASP.NET Web Forms development was good in principle, but reality proved more complicated.

* 过重的视图状态：需要跨HTTP请求来维护状态的实际处理机制（被称为View State,视图状态）导致在客户端和服务器之间需要传输大量数据。就算是在一个很简单的Web应用程序中，这些数据也有可能达到数百KB，并且随着每个请求在服务器和客户端之间来回移动，导致响应时间变慢，增加服务器的带宽消耗。

* View State weight: The actual mechanism for maintaining state across requests (known as View State) resulted in large blocks of data being transferred between the client and server. This data could reach hundreds of kilobytes in even modest web applications, and it went back and forth with every request, leading to slower response times and increasing the bandwidth demands of the server.

* 页面的生命周期：连接客户端事件和服务器端事件处理代码（这是页面生命周期的一部分）的机制非常复杂，需要小心处理。很少有开发人员能在运行时成功对控件的层次结构进行操控，并且不会导致视图状态错误或使得某些事件处理代码莫名其妙地执行失败。

* Page life cycle: The mechanism for connecting client-side events with server-side event handler code, part of the page life cycle, could be complicated and delicate. Few developers had success manipulating the control hierarchy at runtime without creating View State errors or finding that some event handlers mysteriously failed to execute.

* 仿佛存在的关注点分离：ASP.NET Web Forms的代码隐藏模型（code-behind model）提供了一种将应用程序代码从HTML标记中取出并分解为单独的代码隐藏类的方法。这样做是为了分离逻辑和表示，但实际上，这鼓励开发人员把表示逻辑（操纵服务器端的控件等）和其对应的程序逻辑（操纵数据库等）混合在class中的一大坨代码里，导致其难以维护和理解。

* False sense of separation of concerns: ASP.NET Web Forms’ code-behind model provided a means to take application code out of its HTML markup and into a separate code-behind class. This was done to separate logic and presentation, but, in reality, developers were encouraged to mix presentation code (for example, manipulating the server-side control tree) with their application logic (for example, manipulating database data) in these same monstrous code-behind classes. The end result could be fragile and unintelligible.

* 对HTML的有心无力：服务器控件会把自己渲染为HTML，但这并不代表它一定就是你想要的HTML。在ASP.NET的早期版本中，渲染出来的HTML不符合Web标准，也很难使用CSS。并且，服务器控件会生成没办法预测也没办法用JavaScript访问的复杂ID属性。在最近的Web Forms版本中，这些问题已经得到了改善，但获得想要的HTML还是一件很难的事情。

* Limited control over HTML: Server controls rendered themselves as HTML, but not necessarily the HTML you wanted. In early versions of ASP.NET, the HTML output failed to meet web standards or make good use of Cascading Style Sheets (CSS), and server controls generated unpredictable and complex ID attributes that were hard to access using JavaScript. These problems have improved in recent Web Forms releases, but it can still be tricky to get the HTML you expect.

* 缺乏抽象：Web Forms会尽可能地隐藏HTML和HTTP，这导致在试图写一些自定义行为时，经常没办法进行抽象，而不得不重新设计事件回发机制，或者写一些很不优雅的代码来让它生成你想要的HTML。

* Leaky abstraction: Web Forms tried to hide HTML and HTTP wherever possible. As you tried to implement custom behaviors, you frequently fell out of the abstraction, which forced you to reverse-engineer the postback event mechanism or perform obtuse acts to make it generate the desired HTML.

* 难以测试：Web Forms的设计者没办法预料到在未来自动化测试将成为软件开发的重要组成部分。他们所设计的这个紧密耦合的架构不适合进行单元测试，集成测试也很困难。

* Low testability: The designers of Web Forms could not have anticipated that automated testing would become an essential component of software development. The tightly coupled architecture they designed was unsuitable for unit testing. Integration testing could be a challenge, too.

虽然Web Forms很烂，但它也并不是一无是处，并且最后微软致力于改进标准合规性，并简化开发流程。微软甚至从原始的ASP.NET MVC框架中汲取了一些功能，并将它们应用到Web Forms中去。当需要一个快速的开发结果时，Web Forms表现出色，并且在一天内就能写完并运行一个相当复杂的Web应用。但是，除非在开发过程中足够小心，否则你会发现你写出来的东西很难测试也很难维护。

Web Forms wasn’t all bad, and eventually, Microsoft put effort into improving standards compliance and simplifying the development process and even took some features from the original ASP.NET MVC Framework and applied them to Web Forms. Web Forms excelled when you needed quick results, and you could have a reasonably complex web app up and running within a day. But unless you were careful during development, you would find that the application you created was hard to test and hard to maintain.

### 原始的MVC框架

2007年10月，微软发布了一个基于现有ASP.NET平台的新开发平台，旨在于直接回应对Web Forms的批评和Ruby on Rails等竞争平台的普及。这个新平台被称为ASP.NET MVC，它反映了Web应用程序开发中的新兴趋势，如HTML和CSS标准化、RESTful的Web服务、有效的单元测试以及开发人员应该接受HTTP的无状态特性的想法。

In October 2007, Microsoft announced a new development platform, built on the existing ASP.NET platform, that was intended as a direct response to the criticisms of Web Forms and the popularity of competing platforms such as Ruby on Rails. The new platform was called the ASP.NET MVC Framework and reflected the emerging trends in web application development, such as HTML and CSS standardization, RESTful web services, effective unit testing, and the idea that developers should embrace the stateless nature of HTTP.

这些支撑着原始MVC框架的概念，在现在看起来是很自然的，但是在2007年的.NET Web开发领域，它却并不为人所知。ASP.NET MVC框架的引入使得微软重新再Web开发平台跟上时代。

The concepts that underpin the original MVC Framework seem natural and obvious now, but they were lacking from the world of .NET web development in 2007. The introduction of the ASP.NET MVC Framework brought Microsoft’s web development platform back into the modern age.

MVC框架的发布标志着微软的态度发生了重大变化。此前，微软试图控制Web应用程序工具链中的每个组件。借助MVC框架，Microsoft引入诸如jQuery之类的开源工具，从更成功的竞争的平台上接受了设计惯例和最佳实践，并将源代码发布到MVC框架中供开发人员进行检查。

The MVC Framework also signaled an important change in attitude from Microsoft, which had previously tried to control every component in the web application toolchain. With the MVC Framework, Microsoft built on open source tools such as jQuery, took on design conventions and best practices from competing (and more successful) platforms, and released the source code to the MVC Framework for developers to inspect.

#### 原始MVC框架又做错了什么？

当时，在ASP.NET平台已经有了很多可靠的底层功能，并且也已经为开发人员所熟知。对于微软而言，在此之上创建一个高层的MVC框架是很有意义的。

At the time the MVC Framework was created, it made sense for Microsoft to create it on top of the existing ASP.NET platform, which had a lot of solid low-level functionality that provided a head start in the development process and which was already well-known and understood by ASP.NET developers.

但是，为了将MVC嫁接到最初仅为Web Forms设计的平台上，开发人员需要付出很大努力和妥协。为了让网站正常工作，MVC框架开发人员变得习惯于通过不停调整配置文件或代码来完成一些功能的配置，哪怕这些对于业务来说，并没有任何直接影响。

But compromises were required to graft the MVC Framework onto a platform that was originally designed for Web Forms. MVC Framework developers became used to using configuration settings and code tweaks that disabled or reconfigured features that didn’t have any bearing on their web application but were required to get everything working.

随着MVC框架变得越来越流行，微软开始将其一部分核心功能添加到Web Forms中。结果是一切变得越来越奇怪。一些怪癖的MVC框架设计扩展到Web窗体，使得所有的奇怪的东西都融合在了一起。与此同时，微软开始使用新的框架来扩展ASP.NET，比如创建Web服务的Web API和支持实时通信的SignalR。新的框架增加了他们自己的配置和约定，且每个框架都有自己的长处和怪异之处，总体结果来看，就是一团糟。

As the MVC Framework grew in popularity, Microsoft started to take some of the core features and add them to Web Forms. The result was increasingly odd, where features with design quirks required to support the MVC Framework were extended to support Web Forms, with further design quirks to make everything fit together. At the same time, Microsoft started to expand ASP.NET with new frameworks for creating web services (Web API) and real-time communication (SignalR). The new frameworks added their own configuration and development conventions, each of which had its own benefits and oddities, and the overall result was a fragmented mess.

## ASP.NET Core

2015年，微软宣布了ASP.NET和MVC框架的新方向，最终形成了本书的主题 —— ASP.NET Core MVC。

In 2015, Microsoft announced a new direction for ASP.NET and the MVC Framework, which would eventually produce ASP.NET Core MVC, the topic of this book.

ASP.NET Core建立在.NET Core之上。它是.NET Framework的跨平台版本，不包含任何Windows平台特定的API。目前，虽然Windows仍然是市场上占主导地位的操作系统，但是Web应用程序越来越多地被托管在云平台中的小而轻的容器中。通过跨平台的方法，微软扩展了.NET的范围，使得将ASP.NEY Core部署在更广泛的环境中成为可能，并且开发人员甚至可以在Linux和MacOS上创建ASP.NET Core Web应用程序。

ASP.NET Core is built on .NET Core, which is a cross-platform version of the .NET Framework without the Windows-specific application programming interfaces (APIs). Windows is still a dominant operating system, but web applications are increasingly hosted in small and simple containers in cloud platforms, and by embracing a cross-platform approach, Microsoft has extended the reach of .NET, made it possible to deploy ASP.NET Core applications to a broader set of hosting environments, and, as a bonus, made it possible for developers to create ASP.NET Core web applications on Linux and macOS.

ASP.NET Core是一个全新的框架。它更简单，易于使用，并且甩掉了来自Web Forms的历史遗留问题。而且，它基于.NET Core，因此支持在各种平台和容器上开发Web应用程序和部署。

ASP.NET Core is a completely new framework. It is simpler, it is easier to work with, and it is free of the legacy that comes from Web Forms. And, since it is based on .NET Core, it supports the development of web applications on a range of platforms and containers.

ASP.NET Core MVC提供了在新的ASP.NET Core平台上构建ASP.NET MVC Framework的功能。 它集成了之前由Web API提供的功能，使用了一种更自然的方式来生成复杂的内容，并使得单元测试等重要的开发任务变得更简单和可预测。

ASP.NET Core MVC provides the functionality of the original ASP.NET MVC Framework built on the new ASP.NET Core platform. It integrates the features that were previously provided by Web API, it includes a more natural way of generating complex content, and it makes key development tasks, such as unit testing, simpler and more predictable.

### ASP.NET Core MVC 2 中的新内容

ASP.NET Core MVC 2的重点关注是功能的巩固和整合，更好地协同在其早期版本中引入的工具和平台进行工作。ASP.NET Core MVC 2需要.NET Core 2的支持，.NET Core 2具有很多扩展的API，并且受Linux发行版的支持。有用的更改还包括一简化了的NuGet包管理系统，一个新的ASP.NET Core配置系统，和对Entity Framework Core 2的支持。最大的新功能是Razor Pages，这是一个使用更具现代化开发风格的，对Web页面进行重建的尝试。但对MVC开发人员对Razor Pages可能并不感兴趣（我在本书中也没有描述它）。

The ASP.NET Core MVC 2 release focuses on consolidation, working through some of the tooling and platform changes that were introduced in earlier versions. ASP.NET Core MVC 2 requires .NET Core 2, which has a much-expanded API surface and is now supported on additional Linux distributions. Useful changes include a new meta-package system, which simplifies the management of NuGet packages, a new configuration system for ASP.NET Core, and support for Entity Framework Core 2. The biggest new feature is Razor Pages, which is an attempt to re-create the development style associated with Web Pages using a more modern platform, but Razor Pages will not be of interest for MVC developers (and I do not describe it in this book).

### ASP.NET Core MVC的主要优点

以下部分将简要介绍新的MVC平台是如何克服Web Forms和原始MVC框架的遗留问题，以及如何将ASP.NET带回潮流最前端的。

The following sections briefly describe how the new MVC platform overcomes the legacy of Web Forms and the original MVC Framework and how it has brought ASP.NET back to the cutting edge.

#### MVC架构

ASP.NET Core MVC遵循一种称为模型/视图/控制器(model-view-controller)的模式，它指定了ASP.NET Web应用程序的形态以及它所包含的组件之间的交互方式。

ASP.NET Core MVC follows a pattern called model-view-controller, which guides the shape of an ASP.NET web application and the interactions between the components it contains.

区分MVC体系结构模式和ASP.NET Core MVC的具体实现是很重要的。MVC模式并不是什么新鲜玩意儿，它可以追溯到1978年的Xerox PARC的Smalltalk项目。但是，由于以下原因，它现在作为一种Web应用程序的模式更受欢迎：

It is important to distinguish between the MVC architectural pattern and the ASP.NET Core MVC implementation. The MVC pattern is not new—it dates back to 1978 and the Smalltalk project at Xerox PARC—but it has gained popularity today as a pattern for web applications for the following reasons:

* 遵循MVC模式的应用程序可以与用户交互形成一个自然的循环：用户发出动作，应用程序更改其数据模型并向用户传递更新的视图作为相应，循环往复。这很适合Web应用程序用来处理一系列HTTP请求及提供响应。

* User interaction with an application that adheres to the MVC pattern follows a natural cycle: the user takes an action, and in response, the application changes its data model and delivers an updated view to the user. Then the cycle repeats. This is a convenient fit for web applications delivered as a series of HTTP requests and responses.

* Web应用程序需要结合多种技术（例如数据库、HTML和业务逻辑代码等），这通常被分层构建。这些组合产生的模式可以很自然地映射到MVC模式的概念。

* Web applications necessitate combining several technologies (databases, HTML, and executable code, for example), usually split into a set of tiers or layers. The patterns that arise from these combinations map naturally onto the concepts in the MVC pattern.

ASP.NET Core MVC实现了MVC模式，与Web Forms相比，这种做法大大有助于实现关注点分离。事实上，ASP.NET Core MVC实现了一些特别适用于Web应用程序的MVC模式的变体。第3章将更多地介绍这种体系结构的理论和实践。

ASP.NET Core MVC implements the MVC pattern and, in doing so, provides a greatly improved separation of concerns when compared to Web Forms. In fact, ASP.NET Core MVC implements a variant of the MVC pattern that is especially suitable for web applications. You will learn more about the theory and practice of this architecture in Chapter 3.

#### 可扩展性

ASP.NET Core和ASP.NET Core MVC被构建为一系列具有明确定义的、实现了.NET接口或构建于抽象基类上的独立组件。并且可以轻松地使用自定义的实现来替换关键组件。一般来说，ASP.NET Core MVC为每个组件提供了以下三个选项：

ASP.NET Core and ASP.NET Core MVC are built as a series of independent components that have well-defined characteristics, satisfy a .NET interface, or are built on an abstract base class. You can easily replace key components with ones of your own implementation. In general, ASP.NET Core MVC gives you these three options for each component:

* 直接使用默认实现（对于大部分应用程序来说已经足够了）。

* Use the default implementation of the component as it stands (which should be enough for most applications).

* 继承默认实现类，自定义子类以调整其的行为。

* Derive a subclass of the default implementation to tweak its behavior.

* 重新实现接口或者抽象类，以完整地替换掉默认实现。

* Replace the component entirely with a new implementation of the interface or
abstract base class.

第14章将介绍各种组件以及想替换他们的原因以及具体方法。

You’ll learn all about the various components and how and why you might want to tweak or replace
each of them, starting in Chapter 14.

#### 对HTML和HTTP的严格控制

ASP.NET Core MVC会生成整洁的、符合标准的标签。其内置的tag helper可以生成符合标准的输出。与Web Forms相比，其中有一个更重要的理念变化。ASP.NET Core MVC鼓励用户写出更简单、优雅的HTML和CSS，而不是生成大量无法控制的HTML。

ASP.NET Core MVC produces clean, standards-compliant markup. Its built-in tag helpers produce standards-compliant output, but there is a more significant philosophical change compared with Web Forms. Instead of generating out swathes of HTML over which you have little control, ASP.NET Core MVC encourages you to craft simple, elegant markup styled with CSS.

当然，如果你想为复杂的UI元素（如日期选择器或级联菜单）使用一些现成的小部件，那么ASP.NET Core MVC中所采用的“无特殊要求”的方法就可以轻松地使用这些最佳解决方案，包括jQuery、Angular、React或Bootstrap之类的客户端库。ASP.NET Core MVC与这些库进行了良好的匹配，便于微软包含它们的模板，以启动新的开发项目。

Of course, if you do want to throw in some ready-made widgets for complex UI elements such as date pickers or cascading menus, the “no special requirements” approach taken by ASP.NET Core MVC makes it easy to use best-of-breed client-side libraries such as jQuery, Angular, React, or the Bootstrap CSS library. ASP.NET Core MVC meshes so well with these libraries that Microsoft includes templates that incorporate them to jump-start new development projects.

ASP.NET Core MVC与HTTP工作得十分协调顺畅。可以直接控制浏览器和服务器之间的请求传递，因此可以尽可能多地调整用户的使用体验。如第20章所述，Ajax经过了简化，使得创建Web服务来接收浏览器HTTP请求成为一件很简单的事。

ASP.NET Core MVC works in tune with HTTP. You have control over the requests passing between the browser and server, so you can fine-tune your user experience as much as you like. Ajax is made easy, and creating web services to receive browser HTTP requests is a simple process, as described in Chapter 20.

#### 可测试性

ASP.NET Core MVC体系结构为应用程序变得可维护和可测试提供了一个良好的开始，因为可以自然地将不同的问题分解为独立的部分。此外，每个ASP.NET Core平台和ASP.NET Core MVC框架都可以被隔离并替换为单元测试，这可以使用任何流行的开源测试框架来执行，比如在第一章中将介绍的xUnit 7。

The ASP.NET Core MVC architecture gives you a great start in making your application maintainable and testable because you naturally separate different application concerns into independent pieces. In addition, each piece of the ASP.NET Core platform and the ASP.NET Core MVC framework can be isolated and replaced for unit testing, which can be performed using any popular open source testing framework, such as xUnit, which I introduce in Chapter 7.

在本书中，将介绍如何使用各种测试和模拟策略，来为ASP.NET MVC控制器编写干净、简单的单元测试，以及提供框架组件的Mock实现来模拟任何场景。即使你以前从未写过单元测试，这也是一个好的开始。

In this book, you will see examples of how to write clean, simple unit tests for ASP.NET MVC controllers and actions that supply fake or mock implementations of framework components to simulate any scenario, using a variety of testing and mocking strategies. Even if you have never written a unit test before, you will be off to a great start.

可测试性不仅仅只是单元测试的问题。ASP.NET Core MVC应用程序也可以与UI自动化测试工具一起使用。可以编写模拟用户交互的测试脚本，而不用去猜测框架将生成哪些HTML元素结构、CSS和ID，并且不必担心结构的意外更改。

Testability is not only a matter of unit testing. ASP.NET Core MVC applications work well with UI automation testing tools, too. You can write test scripts that simulate user interactions without needing to guess which HTML element structures, CSS classes, or IDs the framework will generate, and you do not have to worry about the structure changing unexpectedly.

#### 强大的路由系统

统一资源定位符（URL）的风格随着Web应用程序技术的改进而发展。这样的网址：


---

/App_v2/User/Page.aspx?action=show%20prop&prop_id=82742


---

已经越来越少见，取而代之的是更简单、更清晰的格式：

---

/to-rent/chicago/2303-silver-street

---

The style of uniform resource locators (URLs) has evolved as web application technology has improved. URLs like this one: 

---

/App_v2/User/Page.aspx?action=show%20prop&prop_id=82742

---

are increasingly rare, replaced by a simpler, cleaner format like this:

---

/to-rent/chicago/2303-silver-street

---

关注URL的结构有一些很好的理由。首先，搜索引擎会对URL中的关键字进行加权。搜索“芝加哥租房”更容易出现更简单的网址。其次，现在许多网络用户已经足够了解URL，并且更愿意直接在浏览器的地址栏中输入地址的方式进行导航。第三，当有人了解URL的结构时，他们更有可能点它，并分享出去，甚至在电话中将其读出来。第四，它不会将应用程序的技术细节，文件夹和文件名结构公开到互联网上，因此可以自由更改底层实现而不会中断所有传入链接。

There are some good reasons for caring about the structure of URLs. First, search engines give weight
to keywords found in a URL. A search for “rent in Chicago” is much more likely to turn up the simpler URL. Second, many web users are now savvy enough to understand a URL and appreciate the option of navigating by typing it into their browser’s address bar. Third, when someone understands the structure of a URL, they are more likely to link to it, share it with a friend, or even read it aloud over the phone. Fourth, it doesn’t expose the technical details, folder, and file name structure of your application to the public Internet, so you are free to change the underlying implementation without breaking all your incoming links.

整洁的URL在早期的框架中很难实现。但ASP.NET Core MVC使用一种称为URL路由的功能默认提供干净的URL。并且可以通过控制URL模式与应用程序的对应关系替换默认模式，来自由创建对用户有意义且有用的URL模式。当然，这意味着如果你愿意，你可以轻松定义现代REST风格的URL模式。在第15章和第16章中可以找到关于URL路由的完整描述。

Clean URLs were hard to implement in earlier frameworks, but ASP.NET Core MVC uses a feature known as URL routing to provide clean URLs by default. This gives you control over your URL schema and its relationship to your application, offering you the freedom to create a pattern of URLs that is meaningful and useful to your users, without the need to conform to a predefined pattern. And, of course, this means you can easily define a modern REST-style URL schema if you want. You’ll find a thorough description of URL routing in Chapters 15 and 16.

#### 现代化API

微软的.NET平台随着每个主要版本的发展而发展，支持了乃至定义了现代编程的最新技术。ASP.NET Core MVC是为.NET Core构建的，因此它的API可充分利用C#程序员熟悉的语言和运行时创新，包括await关键字、扩展方法、lambda表达式、匿名类型、动态类型以及LINQ。

Microsoft’s .NET platform has evolved with each major release, supporting—and even defining—the state-of-the-art aspects of modern programming. ASP.NET Core MVC is built for .NET Core, so its API can take full advantage of language and runtime innovations familiar to C# programmers, including the await keyword, extension methods, lambda expressions, anonymous and dynamic types, and Language Integrated Query (LINQ).

ASP.NET Core MVC API方法和编码模式遵循比以前的平台更清晰、更具表现力的组成方式。如果你对最新的C#语言功能不熟悉，不要担心，在第4章中提供了MVC开发最重要的C#特性的总结。

Many of the ASP.NET Core MVC API methods and coding patterns follow a cleaner, more expressive composition than was possible with earlier platforms. Don’t worry if you are not up to speed with the latest C# language features; I provide a summary of the most important C# features for MVC development in Chapter 4.

#### 跨平台

以前的ASP.NET版本特定于Windows，需要在Windows中编写Web应用程序和Windows服务器才能部署和运行它们。微软为ASP.NET Core提供了跨平台的开发和部署方案。.NET Core可用于不同的平台，包括MacOS和一系列流行的Linux发行版。跨平台支持使得部署ASP.NET Core MVC应用程序变得更加容易，并且可以很好地支持Docker等应用程序容器平台。

Previous versions of ASP.NET were specific to Windows, requiring a Windows desktop to write web applications and a Windows server to deploy and run them. Microsoft made ASP.NET Core cross- platform, both for development and for deployment. .NET Core is available for different platforms, including macOS and a range of popular Linux distributions. Cross-platform support makes it easier to deploy ASP.NET Core MVC applications, and there is good support for working with application container platforms, such as Docker.

大多数ASP.NET Core MVC开发很可能都会使用Visual Studio来完成，但微软也创建了一个名为Visual Studio Code的跨平台开发工具，这意味着ASP.NET Core MVC开发不再局限于Windows。

Most ASP.NET Core MVC development is likely to be done using Visual Studio for the immediate future, but Microsoft has also created a cross-platform development tool called Visual Studio Code, which means that ASP.NET Core MVC development is no longer restricted to Windows.

#### ASP.NET Core MVC是开源的

与以前的Microsoft Web开发平台不同，你可以自由下载ASP.NET Core和ASP.NET Core MVC的源代码，甚至修改和编译自己的版本。当你的调试跟踪进入到系统组件中，并且想要进入其代码（甚至读取原始程序员的注释）时，这是非常宝贵的。 如果正在构建高级组件，并希望查看存在哪些开发可能性或内置组件实际如何工作，这也很有用。
可以从 https://github.com/aspnet 下载ASP.NET Core和ASP.NET Core MVC的源代码。

Unlike previous Microsoft web development platforms, you are free to download the source code for ASP. NET Core and ASP.NET Core MVC and even modify and compile your own version of it. This is invaluable when your debugging trail leads into a system component and you want to step into its code (and even read the original programmer’s comments). It is also useful if you are building an advanced component and want to see what development possibilities exist or how the built-in components actually work.
You can download the ASP.NET Core and ASP.NET Core MVC source code from https://github.com/ aspnet.

### 我需要知道什么？

为了充分利用本书，你应该熟悉Web开发的基础知识，了解HTML和CSS的工作原理，并掌握C#的相关知识。如果你对客户端细节（如JavaScript）有点模糊不要担心，我的重点是本书中的服务器端开发，可以通过示例找到所需要的内容。在第4章中，我总结了MVC开发最有用的C#语言特性，如果你正在迁移到最新的.NET版本，那么你会发现它很有用。

To get the most from this book, you should be familiar with the basics of web development, understand how HTML and CSS work, and have a working knowledge of C#. Don’t worry if you are a little hazy on the client- side details, such as JavaScript. My emphasis is on server-side development in this book, and you can pick up what you need through the examples. In Chapter 4, I summarize the most useful C# language features for MVC development, which you’ll find useful if you are moving to the latest .NET versions from an earlier release.

### 本书的结构如何？

本书分为两部分，每一部分都包括了一系列相关话题。

This book is split into two parts, each of which covers a set of related topics.

Part 1: ASP.NET Core MVC的介绍

我将从”所谓ASP.NET Core MVC“开始撰写本书。我将解释了MVC模式的优点和实际影响，介绍ASP.NET Core MVC适用于现代Web开发的方式，并描述了每个ASP.NET Core MVC程序员都需要的工具和C#语言的功能。

Part 1: Introducing ASP.NET Core MVC

I start this book by putting ASP.NET Core MVC in context. I explain the benefits and practical impact of the MVC pattern, cover the way in which ASP.NET Core MVC fits into modern web development, and describe the tools and C# language features that every ASP.NET Core MVC programmer needs.

在第2章中，将通过创建一个简单的Web应用程序，深入了解主要组件和功能模块，以及如何将他们组合在一起。这本书的大部分内容交给了一个名为SportsStore的项目的开发，通过这个项目，我将向你展示从开始到部署的实际开发过程，并涉及ASP.NET Core MVC的主要功能。

In Chapter 2, you will dive right in by creating a simple web application and will get an idea of what the major components and building blocks are and how they fit together. Most of this part of the book, however, is given over to the development of a project called SportsStore, through which I show you a realistic development process from inception to deployment, touching on the major features of ASP.NET Core MVC.

Part 2: ASP.NET Core MVC的详细说明

在第2部分中，我解释了用于构建SportsStore应用程序的ASP.NET Core MVC功能的内部工作原理。我向你展示每个功能的工作原理，说明它所扮演的角色，并向你展示可用的配置和自定义选项。在第1部分中设定了广泛的语境之后，我深入了解了第2部分中的细节。

Part 2: ASP.NET Core MVC in Detail

In Part 2, I explain the inner workings of ASP.NET Core MVC features that I used to build the SportsStore application. I show you how each feature works, explain the role it plays, and show you the configuration and customization options that are available. Having set the broad context in Part 1, I dig right into the details in Part 2.

### 哪里可以得到示例代码？

可以从 https://github.com/apress/pro-asp.net-core-mvc-2 下载本书中所有章节的示例项目。该下载是免费的，并包含重新创建示例所需的所有支持资源，而无需输入它们。下载代码不是必须的，但它是实验的最简单方法，可以轻松地将代码复制并粘贴到自己的项目中。

You can download the example projects for all the chapters in this book from https://github.com/apress/ pro-asp.net-core-mvc-2. The download is available without charge and includes all the supporting resources that are required to re-create the examples without having to type them in. You don’t have to download the code, but it is the easiest way of experimenting with the examples and makes it easy to copy and paste code into your own projects.

### 总结

在本章中，我解释了什么是ASP.NET Core MVC以及它是如何从Web Forms和原始的ASP.NET MVC Framework发展而来的。我描述了使用ASP.NET Core MVC的好处以及本书的结构。在下一章中，将讲解ASP.NET Core MVC的实际操作，并简要演示了提供这些优势的功能。

In this chapter, I explained the context in which ASP.NET Core MVC exists and how it has evolved from Web Forms and the original ASP.NET MVC Framework. I described the benefits of using the ASP.NET Core MVC and the structure of this book. In the next chapter, you’ll see ASP.NET Core MVC in action in a simple demonstration of the features that deliver these benefits.