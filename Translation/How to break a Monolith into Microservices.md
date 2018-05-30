# 从单体系统到微服务的正确打开方式

> 原文标题：How to break a Monolith into Microservices
> 原文链接：https://martinfowler.com/articles/break-monolith-into-microservices.html
> 注：每一段译文后跟作者原文，原文中可能包含着作者所提到的内容的跳转超链接。

## 解耦目标和解耦时机

随着整个系统变得过于庞大而难以应对，许多企业开始倾向于将其分解开来，转变为微服务架构。这是一个很有价值的旅程，但也并不是一件很容易完成的事。我们了解到，为了实现这个目标，需要从一个简单的服务开始，然后根据各部分的垂直业务功能，不断抽出对于系统业务来说至关重要并需要经常变更的各种服务。在最初阶段，这些服务应该会很大，并且最好不依赖于尚未分离的大型单体系统本体。我们应该确保每一步迁移都是对整个架构的原子性演进。

As monolithic systems become too large to deal with, many enterprises are drawn to breaking them down into the microservices architectural style. It is a worthwhile journey, but not an easy one. We've learned that to do this well, we need to start with a simple service, but then draw out services that are based on vertical capabilities that are important to the business and subject to frequent change. These services should be large at first and preferably not dependent upon the remaining monolith. We should ensure that each step of migration represents an atomic improvement to the overall architecture.



从一整块单体系统迁移到微服务生态系统简直是一段史诗般的旅程。走上这条征途的人们期望能扩张业务规模，加快改变步伐，并且避免高成本的变革。他们希望扩张团队的数量，并且使各个团队能够并行且独立地产出价值。他们希望快速对其核心业务功能点进行尝试，并更快地产生价值。 他们也希望摆脱与修改和维护现有大型单体系统相关的高成本。

Migrating a monolithic system to an [ecosystem of microservices](https://martinfowler.com/articles/microservices.html) is an epic journey. The ones who embark on this journey have aspirations such as increasing the scale of operation, accelerating the pace of change and escaping the high cost of change. They want to grow their number of teams while enabling them to deliver value in parallel and independently of each other. They want to rapidly experiment with their business's core capabilities and deliver value faster. They also want to escape the high cost associated with making changes to their existing monolithic systems.



在向微服务架构迁移的过程中，决定在什么时候以什么样的方式进行增量迁移是一个架构上的挑战。 在这篇文章中，我将分享一些技巧，可以指导交付团队（包括开发人员、架构师和技术经理 ）做出这些决定。

Deciding what capability to decouple when and how to migrate incrementally are some of the architectural challenges of decomposing a monolith to an ecosystem of microservices. In this write-up, I share a few techniques that can guide the delivery teams - developers, architects, technical managers - to make these decomposition decisions along the journey.



为了阐述本文所关注的技术，我将以一个三层架构的在线零售系统为例，这个系统的UI层、业务逻辑层和数据层紧紧地耦合在了一起。我选择它的原因，是因为这个架构具有很多企业正在运营的大型单体系统的代表性，并且其所采用的技术栈足够现代，可以说明我们应该对其解耦，而不是重写或者直接替换。

To clarify the techniques I use a multitier online retail application. This application tightly couples user facing, business logic and data layer. The reason I have chosen this example is that its architecture has the characteristics of monolithic applications that many businesses run and its technology stack is modern enough to justify decomposition instead of a complete rewrite and replacement.



## 微服务生态系统的目标

在我们开始之前，每个人都对微服务生态系统有一个共同的理解是很重要的。所谓微服务生态系统，是指封装了各个业务功能的服务平台。而所谓业务功能，是指企业在特定领域为实现特定目标和责任所做的事情。每个微服务都将公开一个API，开发人员可以以"自助"的方式发现和使用这些API。微服务具有独立的生命周期，开发人员们可以独立构建、测试和发布各个微服务。在微服务生态系统的组织结构内，各个团队都是独立并长期负责一个或多个服务的。与普遍认知的微服务的“微”字可能不同的是，各个服务的规模可能因组织的运营成熟度而异。正如Martin Fowler所说，“微服务是一种标签，而不是描述”。

Before embarking, it is critical that everyone has a common understanding of a [microservices ecosystem](https://martinfowler.com/articles/microservices.html). Microservices ecosystem is a platform of services each encapsulating a business capability. A business capability represents what a business does in a particular domain to fulfill its objectives and responsibilities. Each microservice expose an API that developers can discover and use in a self-serve manner. Microservices have independent lifecycle. Developers can build, test and release each microservice independently. The microservices ecosystem enforces an organizational structure of autonomous long standing teams, each responsible for one or multiple services. Contrary to general perception and ‘micro’ in microservices, the size of each service matters least and may vary depending on the operational maturity of the organization. As Martin Fowler puts it, "microservices is a label and not the description".

![img](https://martinfowler.com/articles/break-monolith-into-microservices/ecosystem.svg)

图1：服务封装了业务功能，并通过自助的服务API公开数据和功能

Figure 1: Services encapsulate business capabilities, expose data and functionality through self-serve APIs



## 旅途指南

在深入研究之前，重要的是要知道将现有系统分解为微服务可能会带来很高的总体成本，并且可能需要经过很多次迭代才能实现。 开发人员和架构师必须仔细评估是否应该对当前系统进行分解，以及判断对于当前系统来说微服务本身是否是正确的发展方向。在想清楚这个问题之后，咱们开始吧。

Before diving into the guide, it is important to know that there is a high overall cost associated with decomposing an existing system to microservices and it may take many iterations. It is necessary for developers and architects to closely evaluate whether the decomposition of an existing monolith is the right path, and whether the microservices itself is the [right destination](https://martinfowler.com/articles/microservice-trade-offs.html). Having cleared that out, let’s go through the guide.



### 从简单而基础的功能点开始热身吧

只要开始了微服务，就至少需要有最低水平的运维。这需要部署按需访问的环境、需要构建新的持续集成系统以实现服务的独立构建、测试和部署，以及需要一个安全的、可调试的、可监控的分布式体系结构。无论我们正在构建新的服务，还是在对现行系统进行解耦，对于运维能力都是有要求的。有关这一点的更多信息，请参阅Martin Fowler关于微服务的先决条件的文章。好消息是，自从Martin撰文以来，微服务架构的运维技术有了迅速发展，其中包括创建被称为“服务网络”(Service Mesh)的专用基础设施层，可以运行快速、可靠和安全的微服务网络，可以创建提供更高级别的基础设施抽象的“容器编排系统"、以及诸如GoCD等在容器中构建、测试和部署微服务的持续集成系统的发展。

Starting down a microservices path requires a minimum level of operational readiness. It requires on demand access to deployment environment, building new kinds of continuous delivery pipelines to independently build, test, and deploy executable services, and the ability to secure, debug and monitor a distributed architecture. Operational readiness maturity is required whether we are building greenfield services or decomposing an existing system. For more on this operational readiness see Martin Fowler’s article on [Microservices prerequisites](https://martinfowler.com/bliki/MicroservicePrerequisites.html). The good news is that since Martin’s article, the technology to operate a microservices architecture has evolved rapidly. This includes creation of [Service Mesh](https://www.thoughtworks.com/radar/techniques/service-mesh), a dedicated infrastructure layer to run fast, reliable and secure network of microservices, [container orchestration systems](https://www.thoughtworks.com/radar/platforms/kubernetes) to provide a higher level of deployment infrastructure abstraction, and evolution of continuous delivery systems such as [GoCD](https://www.gocd.org/kubernetes/?gclid=EAIaIQobChMIwu-o1_3E2gIVjcVkCh3XUAkoEAAYASAAEgJPQ_D_BwE) to build, test and deploy microservices as containers.



我的建议是，开发人员和运维团队首先可以从微服务的底层基础设施，诸如持续集成系统和API管理系统开始构建。从这些与老系统分离的功能点开始构建微服务，不需要对当前正在运行的面向用户的业务进行更改，甚至可能都不需要进行数据存储。对于交付团队来说，此时需要不断优化的是对他们的交付方法进行验证、对团队成员能力进行提升、构建出最基本的可以独立部署的安全服务的基础设施，从而可以对外公开服务的API。例如对于在线零售系统来说，首先可以从老系统中分离出来的是用户认证鉴权服务，以及对于新的客户程序来说可以提供更好的直观展现的“客户信息”服务。

My suggestion is for developers and operation teams to build out the underlying infrastructure, continuous delivery pipelines and the API management system with the first and second service that they decompose or build new. Start with capabilities that are fairly decoupled from the monolith, they don’t require changes to many client facing applications that are currently using the monolith and possibly don’t need a data store. What the delivery teams are optimizing for at the point is validating their delivery approaches, upskilling the team members, and building out minimum infrastructure needed to deliver independently deployable secure services that expose self-serve APIs. As an example, for an online retail application, the first service can be the ‘end user authentication’ service that the monolith could call to authenticate the end users, and the second service could be the ‘customer profile’ service, a facade service providing a better view of the customers for new client applications.



首先，我建议从简单的边缘服务开始解耦。在此之后，再采用不同的方法深入单体系统解耦其余功能。我建议先做边缘服务开始的原因，是因为在刚开始时，交付团队的最大风险是无法正确地掌握微服务的运维。因此，为了掌握必须的“运维先决条件”，从边缘服务开始练手是一个很好的选择。而 一旦这个问题得到解决，剩下的问题就可以迎刃而解。

First I recommended decoupling simple edge services. Next we take a different approach decoupling capabilities deeply embedded in the monolithic system. I advise doing edge services first because at the beginning of the journey, the delivery teams' biggest risk is failing to operate the microservices properly. So it’s good to use the edge services to practice the [operational prerequisites](https://martinfowler.com/bliki/MicroservicePrerequisites.html) they need. Once they have addressed that, they can then address the key problem of splitting the monolith.

![img](https://martinfowler.com/articles/break-monolith-into-microservices/warming-up.svg)

图2：从简单功能开始锻炼运维能力

Figure 2: Warming up with a simple capability that has a small radius of change to build our operational readiness



### 减少对老系统的依赖

将新的微服务系统与老系统之间的依赖关系最小化是一个基本原则。微服务的一个主要优点是具有快速和独立的发布周期。一旦与老系统之间有任何依赖，如数据、逻辑或API，都将导致微服务系统与老系统的发布周期相耦合，从而使得微服务的发布优势不复存在。通常，摆脱老系统的主要动机就是其成本之高昂，以及深锁于其中的业务功能的更新之缓慢。所以，我们希望逐步通过减少对老系统的依赖关系的方式，将核心业务解耦。如果团队能够遵循这些原则，将业务功能写进自己的服务中，依赖关系就能从对单体系统的依赖转变为对微服务的依赖。这是一个理想的依赖方向，因为它将不会拖慢新服务的更新速度。

As a founding principle the delivery teams need to minimize the dependencies of newly formed microservices to the monolith. A major benefit of microservices is to have a fast and independent release cycle. Having dependencies to the monolith - data, logic, APIs - couples the service to the monolith's release cycle, prohibiting this benefit. Often the main motivation for moving away from the monolith is the high cost and slow pace of change of the capabilities locked in it, so we want to progressively move in a direction that decouples these core capabilities by removing dependencies to the monolith. If the teams follow this guideline as they build out capabilities into their own services, what they find is instead, dependencies in the reverse direction, from the monolith to the services. This is a desired dependency direction as it does not slow down the pace of change for new services.



在在线零售系统中，购买和促销是核心功能。购买功能将在结算过程中使用促销功能，为顾客提供他们可以获得的最佳促销方案，并给他们所购买的商品。如果我们需要决定在这两种功能之中哪一个先解耦，我的建议是先解耦促销，再解耦购买。因为通过这个顺序，我们可以减少对老系统的依赖。在这个顺序中，购买功能将会先被锁定在整体结构中，并依赖于新的促销微服务。

Consider in a retail online system, where ‘buy’ and ‘promotions’ are core capabilities. ‘buy’ uses ‘promotions’ during the checkout process to offer the customers the best promotions that they qualify for, given the items they are buying. If we need to decide which of these two capabilities to decouple next, I suggest to start with decoupling ‘promotions’ first and then 'buy'. Because in this order we reduce the dependencies back to the monolith. In this order ‘buy’ first remains locked in the monolith with a dependency out to the new ‘promotions’ microservice.



下一条准则提供了另一些方法来决定服务的解耦顺序。这意味着，可能并不是总能找到一个可以彻底避免对老系统的依赖的方案。如果新服务最终依然还是调用到了老系统，我建议在老系统中开发新的API，并通过新服务的防腐层来访问这些API，以确保老系统中的概念不会直接暴露出来。就算老系统的内部可能实现并不是这样的，也应该致力于定义一个能够反应领域的明确概念和其结构的API。在这种不幸的情况下，交付团队可能需要直面困难，承担修改老系统的成本，进行测试和发布与老系统耦合在一起的新服务。

Next guidelines offer other ways for deciding the order in which developers decouple services. This means that they may not be always able to avoid dependencies back to the monolith. In cases where a new service ends up with a call back to the monolith, I suggest to expose a new API from the monolith, and access the API through an [anti-corruption](https://martinfowler.com/articles/refactoring-external-service.html#SeparatingTheYoutubeDataStructureIntoAGateway) layer in the new service to make sure that the monolith concepts do not leak out. Strive to define the API reflecting the well defined domain concepts and structures, even though the monolith’s internal implementation might be otherwise. In this unfortunate case the delivery teams will be bearing the cost and difficulty of changing the monolith, testing and releasing the new services coupled with the monolith release.

![img](https://martinfowler.com/articles/break-monolith-into-microservices/preferred-dependency.svg)

图3：解耦没有依赖关系的服务，并尽量减少对老系统的修改

Figure 3: Decouple the service that doesn’t require a dependency back to the monolith first and minimize changes to the monolith



### 尽早解耦粘性功能

首先我们假设，交付团队乐意于构建微服务，并准备解决遇到的棘手的问题。然而，交付团队很可能会发现自己缺乏将服务解耦到对老系统不再有依赖关系的能力。造成这种情况的根本原因，往往是因为在整体架构中的某个功能模块的设计上存在问题，没有被很好地定义为领域概念，导致系统中大量的功能都依赖于它。为了能够将解耦进行下去，开发人员需要识别出这些“粘性功能(Sticky Capabilities)”，将其解构为拥有良好定义的领域概念，并将这些领域概念转化为单独的服务。

I am assuming that at this point the delivery teams are comfortable with building microservices and ready to attack the sticky problems. However they may find themselves limited with the capabilities that they can decouple next without a dependency back to the monolith. The root cause of this, is often a capability within the monolith that is leaky, not well defined as a domain concept, with many of the monolith capabilities depending on it. In order to be able to progress, the developers need to identify the sticky capability, deconstruct it into well defined domain concepts and then [reify](https://en.wikipedia.org/wiki/Reification) those domain concepts into separate services.



比如在一个网站的单体系统中，会话（Session）是最常见的耦合因素之一。在在线零售系统的示例中，Session中通常存放着许多东西，从跨越了多个域边界的用户偏好（比如物流和支付的偏好设置）到用户的意图和用户交互（比如最近访问的页面，浏览过的产品和愿望清单）。如果我们不进行解耦、重构当前的会话概念，我们将很难解耦剩下的更多功能，因为它们通过四处弥漫着的Session与老系统紧紧地耦合在了一起。 同时，我也不鼓励在整体框架之外另外创建一个“会话”服务，因为它只会导致类似的强耦合。相比之下，目前这种耦合关系仅存在于整体的业务流程之中，更糟糕的是，让它散布到业务流程之外乃至整个网络中。

For example in a web based monolith, the notion of ‘(web) session’ is one of those most common coupling factors. In the online retail example, the session is often a bucket for many attributes ranging from user preferences across different domain boundaries such as shipping and payment preferences, to user intentions and interactions such as recently visited pages, clicked products, and wish list. Unless we tackle decoupling, deconstructing and reifying the current notion of ‘session’, we will struggle to decouple many of the future capabilities as they will be entangled with the monolith through the leaky session concepts. I also discourage creating a ‘session’ service outside of the monolith, as it will just result in a similar tight coupling that currently exist within the monolith process, only worse, out of process and across the network.



开发人员可以逐步地从粘性功能中将微服务提取出来，即一次只提供一个服务。例如，首先重构“愿望清单”并将其提取到新服务中，然后将“默认付款方式”重构为另一个微服务，并以此类推。

Developers can incrementally extract microservices from the sticky capability, one service at time. As an example, refactor 'customer wish list' first and extract that into a new service, then refactor 'customer payment preferences' into another microservice and repeat.

![img](https://martinfowler.com/articles/break-monolith-into-microservices/decouple-most.svg)

图4：找到最多的耦合概念并将其解耦重构为具体的领域服务

Figure 4: Identify the most coupling concept and decouple, deconstruct and reify into concrete domain services

> 使用依赖性和结构化代码分析工具（如Structure101）来确定整体结构中最具耦合性和约束性的业务功能。

> Use dependency and structural code analysis tools such as Structure101 to identify the most coupling and constraining factor capabilities in the monolith.



### 垂直解耦并且提前发布数据

解耦业务功能的主要驱动力就是让它们可以独立发布。它作为第一原则，指导开发人员如何进行解耦的每一个决定。单体系统通常由多个紧密集成的层结构甚至是多个子系统组成，而这些系统需要一起发布，并且具有很脆弱的相互依赖性。例如，一个在线零售系统可能由若干个的直接面向客户的前端应用程序，和一个实现了许多业务功能的集中式数据存储的后端系统组成。

The main driver for decoupling capabilities out of a monolith is to be able to release them independently. This first principle should guide every decision that developers make around how to perform the decoupling. A monolithic system often is composed of tightly integrated layers or even multiple systems that need to be released together and have brittle interdependencies. For example, in an online retail system, the monolith composed of one or multiple customer facing online shopping applications, a back-end system implementing many of the business capabilities with a centrally integrated data store to hold state.



大多数的所谓解耦尝试，只是试图将一些面向用户的组件分离出来，并通过外观模式为前端提供易于使用的API，而数据依然被深锁于单体系统之中。这种方法虽然能快速取得一些成效，比如使得系统可以更频繁地更改UI，但是一旦涉及到系统的核心功能，更新速度便立刻受限于整个系统中更新最慢的部分——单体系统本身及它的数据存储。简而言之，没有对数据本身进行分离的架构并不是微服务。把所有数据都存储在一起的思想本身就与微服务的“分布式数据管理”思想相悖。

Most decoupling attempts start with extracting the user facing components and a few facade services to provide developer friendly APIs for the modern UIs, while the data remains locked in one schema and storage system. Though this approach gives some quick wins such as changing the UI more frequently, when it comes to core capabilities the delivery teams can only move as fast as the slowest part, the monolith and its monolithic data store. Simply put, without decoupling the data, the architecture is not microservices. Keeping all the data in the same data store is counter to the [Decentralized Data Management](https://martinfowler.com/articles/microservices.html#DecentralizedDataManagement) characteristic of microservices.



解决方案：垂直地分离各个业务功能，连业务逻辑带数据一起进行解耦，并且各个前端应用的调用重定向到新的API上。

The strategy is to move out capabilities vertically, decouple the core capability with its data and redirect all front-end applications to the new APIs.



对于这种数据加服务一起解耦的方法而言，其解耦过程中的主要障碍就是那些需要对集中存储的共享数据进行并发读写的各个应用程序。针对这种情况，交付团队需要提供合适的数据迁移策略，具体取决于他们能否在同时对所有数据的读写做重定向和迁移。Stripe所写的“数据迁移的四阶段策略”适用于许多需要进行数据增量迁移的环境，并且可以保证所有正在进行迁移的系统可以不中断地运作。

Having multiple applications writing and reading to and from the centrally shared data is the main blocker to decoupling the data along with the service. The delivery teams need to incorporate a data migration strategy that suits their environment depending on whether they are able to redirect and migrate all the data readers/writers at the same time or not. Stripe’s [four phase data migration strategy](https://robertheaton.com/2015/08/31/migrating-bajillions-of-database-records-at-stripe) is one that applies to many environments that require to incrementally migrate the applications that integrate through the database, while all the systems under change need to run continuously.



![img](https://martinfowler.com/articles/break-monolith-into-microservices/decouple-capability.svg)

图5：将数据和服务迁移到微服务中，并将所有调用方调整和重定向至新API

Figure 5: Decouple capability with its data to a microservice exposing a new interface, modify and redirect consumers to the new API

> 避免只解耦调用接口或后端服务而不解耦数据的反模式

> Avoid the anti pattern of only decoupling facades, only decoupling the backend service and never decoupling data.



### 解耦重要而易变的业务功能

从单体系统中将业务功能解耦出来是一件很难的事情。我听说，Neal Ford将其比作“一台小心翼翼的器官手术”。从在线零售系统中提取业务功能时，需要仔细地将业务逻辑、数据、UI组件提取出来并将它们重定向到新服务。因为其所需的工作量不是一点两点，所以开发者们需要不断地评估进行解耦的成本与其能够带来的好处，比如，是想要追求更快的开发速度，还是要追求规模上的成长。举个例子，如果交付团队的目标是想在修改单体系统中的现有业务功能时耗时更少，那么他们必须找到修改最耗时的业务功能并将其分离。把代码中不断发生变化的部分单独分离出来，可以从开发人员那里得到很多的爱，还可以让他们可以最快速地产出价值。交付团队还可以通过对代码的提交记录做分析，找到那些变化得最多的部分，并将其与产品路线图、产品组合一起分析，以确定在将来最期望的功能，并将其分离出来。他们需要与业务经理、产品经理一起交谈，以了解对他们而言真正重要的差异化能力。

Decoupling capabilities from the monolith is hard. I’ve heard [Neal Ford](http://nealford.com/) use the analogy of a careful organ surgery. In the online retail application, extracting a capability involves carefully extracting the capability’s data, logic, user facing components and redirecting them to the new service. Because this is a non-trivial amount of work, the developers need to continuously evaluate the cost of decoupling against the benefits that they get, e.g. going faster or growing in scale. For example, if the delivery teams' objective is to accelerate the modifications to existing capabilities locked in a monolith, then they must identify the capability that is being modified the most to take out. Decouple parts of the code that are continuously undergoing change and getting a lot of love from the developers and are constraining them most to deliver value fast. The delivery teams can analyse the code commit patterns to find out what has historically changed most, and overlay that with the product roadmap and portfolio to understand the most desired capabilities that will be getting attention in near future. They need to talk to the business and product managers to understand the differentiating capabilities that really matter to them.

例如，在一个在线零售系统中，“客户个性化”是一项为了提供最佳用户体验的、经过了大量实验的功能。因为它是一项事关客户体验的非常重要的业务功能，并且经常需要修改，所以它是一个进行解耦的良好目标。

For example in an online retail system, ‘customer personalization’ is a capability that goes under a lot of experimentation to provide the best experience to the customer and is a good candidate for decoupling. It is a capability that matters to business a lot, customer experience, and gets modified frequently.



![img](https://martinfowler.com/articles/break-monolith-into-microservices/matters-most.svg)

图6：找到最重要的业务功能并解耦：在定期迭代中创建最多的业务和用户价值

Figure 6: Identify and decouple the capability that matters most: creates most value for business and customer, while changing regularly.

> 利用社交代码分析工具（如CodeScene）来查找代码中最活跃更改的组件。如果自动构建系统会在每次提交时触发或自动生成代码，请确保过滤掉这些噪声。将经常发生更改的代码与产品路线图上预计进行的更改叠加，并找到进行解耦的交点。

> Use [social code analysis](https://www.thoughtworks.com/radar/techniques/social-code-analysis) tools such as [CodeScene](https://www.empear.com/) to find the most lively components. Make sure to filter signal from the noise if the build system happens to touch or auto-generate code on every commit. Overlay the frequently changed code with the product roadmap upcoming changes and find the intersection to decouple.



### 解耦业务功能，不是解耦业务代码

无论何时，只要开发人员想要从系统中解耦一项服务出来，他们都有两种可行的办法：提取代码，或是直接重写。

Whenever developers want to extract a service out of an existing system, they have two ways to go about it: extract code or rewrite capability.



一般来说，服务的提取，或是单体系统的解构都默认被设想为是通过重用现有的实现方案并将其提取到单独的服务中来实现。其中的部分原因是因为，我们对我们所亲自设计和编写的出来的代码有着认知偏好。对于我们所辛苦付出了劳动而收获的结果，无论过程有多么痛苦，结果有多么不完善，我们都会怀有热爱。这实际上被称为“宜家效应”。不幸的是，这种认知偏好会阻碍我们在分解单体系统所付出的努力。它使得开发人员和更重要的技术管理人员忽略了提取和重用现有代码的高成本和低价值。

Often by default the service extraction or monolith decomposition is imagined as a case of reusing the existing implementation as-is and extracting it into a separate service. Partly because we have a cognitive bias towards the code we design and write. The labor of building, no matter how painful the process or imperfect the result, make us grow love for it. This is in fact known as the [IKEA Effect](https://en.wikipedia.org/wiki/IKEA_effect). Unfortunately this bias is going to hold the monolith decomposition effort back. It causes the developers and more importantly technical managers to disregard the high cost and low value of extracting and reusing the code.



比如说，在零售系统中，“定价和促销”功能是一段相当复杂的高难度代码，它可以动态地配置和应用促销规则，并且根据各种参数（例如客户行为，忠诚度，产品捆绑等）提供折扣和优惠。

For example in the retail system, the ‘pricing and promotion’ capability is an intellectually complex piece of code. It enables dynamic configuration and application of pricing and promotion rules, providing discounts and offers based on a variety of parameters such as customer behavior, loyalty, product bundles, etc.



像“定价和促销”这样的业务功能，就是进行“重用及提取”的完美选择。而相比之下，“客户信息”就是一个很简单的CRUD功能点，主要也就包括序列化、数据存储以及相关配置之类的一些模板功能，因此，它是进行“重写和淘汰”的理想对象。

This capability is arguably a good candidate for reuse and extraction. In contrast, ‘customer profile’ is a simple [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) capability that is mostly composed of boilerplate code for serialization, handling storage and configuration, hence, it is a good candidate for rewrite and retire.



根据我的经验，在大多数系统解构的情况中，考虑到重用的高成本和低价值，交付团队应当将业务功能重写为新的服务并淘汰掉旧代码。理由如下：

* 老代码中存在大量的“模板代码”，用于处理一些诸如“在运行时获取应用程序配置”、“访问数据存储”、“数据缓存”之类的环境依赖，并且这些代码还都是用的老框架写的。而大部分的这些模板代码，都需要重写。承载微服务的新基础架构与这些跑了好几十年的老代码有很大不同，并且需要相差甚远的新的模板代码来做这些事。
* 现有的业务功能，很有可能并不是围绕着一个清晰的领域概念而构建的。这导致了进行传输和存储的数据结构并不能直接反映新的领域模型，并且需要进行彻底的重构。
* 经历了许多变化和迭代而长期存在的遗留代码，很有可能具有很高的“代码毒性”，并且重用价值很低。

In my experience, in majority of the decomposition scenarios, the teams are better off to rewrite the capability as a new service and retire the old code. This is considering the high cost and low value of reuse, due to reasons such as below:

* There is a large amount of boilerplate code that deals with environmental dependencies, such as accessing application configuration at runtime, accessing data stores, caching, and is built with old frameworks. Most of this boilerplate code needs to be rewritten. The new infrastructure to host a microservice is very different from the decades old application runtime and will require a very different kind of boilerplate code.
* It is very likely that the existing capabilities are not built around clear domain concepts. This results in transporting or storing data structures that are not reflecting the new domain models and require undergoing a big restructuring.
* A long lived legacy code that has gone through many iterations of change could have a high [code toxicity](https://erik.doernenburg.com/2008/11/how-toxic-is-your-code) level and low value for reuse.



除非待解耦的业务功能与清晰的领域概念相一致，并且逻辑精巧复杂，否则我强烈建议进行重写，并淘汰老代码。

Unless the capability is relevant, aligned with a clear domain concept and has high intellectual property, I strongly recommend a rewrite and retiring of the old code.



![img](https://martinfowler.com/articles/break-monolith-into-microservices/reuse-rewrite.svg)



图7：重用和提取低毒性的高价值代码，重写和淘汰高毒性的低价值代码

Figure 7: Reuse and Extract high value code with low toxicity, Rewrite and Retire low value code with high toxicity



>使用代码毒性分析工具（如CheckStyle）来做出关于重写与重用的决策。

>Use code toxicity analysis tools such as [CheckStyle](http://checkstyle.sourceforge.net/) to make decisions around rewrite vs. reuse.



###  先宏观，再微观

从遗留单体系统中寻找领域边界，既是一门艺术，又是一门科学。而作为一个具有普适性的规则，利用领域驱动设计的概念来寻找“上下文边界”来定义微服务的边界是一个很好的开始。我承认，我经常看到一些单体系统的解耦在粒度上“矫枉过正”，将一个过大的系统切分为了过小的服务，而这些过小的服务常常是从现有数据的视角上出发的。而这种的微服务识别方法，几乎总是会导致大量与资源的CURD直接相关的贫血服务的“寒武纪生物大爆发”。对于新的微服务架构来说，这将创建一个高度摩擦的环境，并且最终无法独立发布测试，也无法提供服务。这种方式创建了一个难以调试的分布式系统，一个跨越事务边界的分布式系统，因此很难保持一致，这一切对于运维来说，太复杂了。尽管存在一些关于微服务粒度的“启发方法”，诸如团队大小，重写服务的时间，必须包括多少行为等等，我的建议是，微服务的规模将取决于运维团队可以独立发布、监控和运营的服务数量。首先，围绕领域概念构建一个大型服务，待团队的微服务运维能力提升之后再将其分解为多个小服务。

Finding the domain boundaries in a legacy monolith is both an art and science. As a general rule applying domain driven design techniques to find the [bounded contexts](https://martinfowler.com/bliki/BoundedContext.html) defining microservices boundaries is a good place to start. I admit, far too often I see an overcorrection from large monolith to really small services, really small services whose design is inspired and driven by the existing normalized view of the data. This approach to identifying service boundaries almost always leads to a cambrian explosion of large number of [anemic services](https://www.martinfowler.com/bliki/AnemicDomainModel.html) for CRUD resources. For many new to the microservices architecture, this creates a high friction environment that ultimately fails the test of independent release and execution of the services. It creates a distributed system that is hard to debug, a distributed system that is broken across transactional boundaries and hence difficult to keep consistent, a system that is too complex for the operational maturity of the organization. Though there are [some heuristics](https://www.youtube.com/watch?v=UfQTNtbq170) on how ‘micro’ should be the microservice: the size of the team, the time to rewrite the service, how much behavior it must encapsulate, etc. My advice is that the size depends on how many services the delivery and operation teams can independently release, monitor and operate. Start with larger services around a logical domain concept, and break the service down into multiple services when the teams are operationally ready.



例如，在解耦零售系统的过程中，开发者可以从“购买”服务开始解耦。“购买”服务内封装了“购物袋”的功能，还封装了购买一个真正的购物袋的功能，即“结账”。而随着他们组建团队并发布更多服务的能力不断增强，他们可以将“购物袋”功能从“结账”中解耦出来，成为一个单独的服务。

For example, on the journey decoupling the retail system, developers may start with one service ‘buy’ that encapsulates both the content of a ‘shopping bag’ as well as capability of buying the shopping bag, i.e ‘check out’. As their ability to form smaller teams and release larger number of services grows then they can decouple ‘shopping bag’ from ‘check out’ into a separate service.



![img](https://martinfowler.com/articles/break-monolith-into-microservices/macro-micro.svg)



图8：围绕丰富的领域概念从宏观角度上进行解耦，在准备就绪后，将服务细分为更小的领域概念

Figure 8: Decouple macro services around rich domain concepts and when ready, breakdown services to smaller domain concepts

> 使用Richardson L3成熟度模型和超链接，可以在不影响调用方的情况下实现未来的服务分离，即调用方可以发现如何结账，并且需要提前知道。

> Use [Richardson Maturity Model L3](https://martinfowler.com/articles/richardsonMaturityModel.html) and hyperlinks to enable future decoupling of services without impacting callers, i.e. caller discovers how to checkout and does not know in advanced.



### 原子性地按步迁移

想直接将一个巨型单体系统解构成一个精心设计的微服务系统，从而让其在空气中消失，是一件是不可能的事。任何一个经验丰富的工程师都可以分享一些关于尝试进行遗留系统的升级和迁移的故事。而这些尝试，在最开始都是以非常乐观的方式被计划和启动的，然而它们最好的结局一般都是“在一个足够好的时间点被及时放弃”。由于一个宏观条件的变化，这些系统迁移的长期计划会被放弃，比如项目资金耗尽、高层的关注重点转移，或者支持这个项目的领导走人了。所以，现实是，团队应该如何做规划，来开始这场庞大的微服务解耦之旅。这种方法，我称之为“架构演进的原子性按步迁移”。迁移的每一步，都应使架构更接近其目标状态。而每一次的架构演进，都应该是原子性的——无论是一个小步骤还是一个大飞跃，要么完成，要么回滚。这是非常重要的，因为我们正在采取迭代和渐进的方法来改进整体架构和进行服务解耦。就完成架构目标而言，每个改变都必须让我们处于更好的位置。使用“演进架构”的适应度函数进行隐喻的话，演进的每个原子步骤之后的架构适应度函数都应该对架构的目标产生更高价值。

The idea of vanishing a legacy monolith into thin air by decoupling it into beautifully designed microservices is somewhat of a myth and arguably undesirable. Any seasoned engineer can share stories of legacy migration and modernization attempts that got planned and initiated with over optimism of total completion, and at best got abandoned at a good enough point in time. Long term plans of such endeavors get abandoned because the macro conditions change: the program runs out of money, the organization pivots its focus to something else or leadership in support of it leaves. So this reality should be designed in how the teams approach the monolith to microservices journey. I call this approach 'migration in atomic steps of architecture evolution', where every step of the migration should take the architecture closer to its target state. Each unit of evolution might be a small step or a large leap but is atomic, either completes or reverts. This is specially important as we are taking an iterative and incremental approach to improving the overall architecture and decoupling services. Every increment must leave us in a better place in terms of the architecture goal. Using the [evolutionary architecture](https://www.thoughtworks.com/books/building-evolutionary-architectures)fitness function metaphor, the architecture fitness function after every atomic step of migration should generate a closer value to the architecture’s goal.



让我举一个例子来说明这一点。 想象一下，微服务架构的目标是提高开发人员的修改系统以提供价值的速度。团队决定将最终用户身份验证解耦为一个基于OAuth 2.0协议的单独服务。此服务旨在替代现有老架构中的用户身份验证系统，并以微服务的新体系结构来进行身份验证。 我们将这种增量改变称为“导入鉴权服务”。导入这项新服务的一种方法是：

（1）构建鉴权服务，实现OAuth 2.0协议。

（2）在老系统后端添加一个新的认证路径，以调用鉴权服务来认证最终用户。

Let me illustrate this point with an example. Imagine the microservice architecture goal is to increase the speed of developers modifying the overall system to deliver value. The team decides to decouple the end user authentication into a separate service based on OAuth 2.0 protocol. This service is intended to both replace how the existing (old architecture) client application authenticates the end user, as well as new architecture microservices validate the end user. Let's call this increment in the evolution, ‘Auth service introduction’. One way to introduce the new service is to go through these steps first:

(1) Build the Auth service, implementing OAuth 2.0 protocol.

(2) Add a new authentication path in the monolith back end to call Auth service for authenticating the end user on whose behalf it is processing a request.



如果团队至此而止，转而去开发一些其他服务或功能的话，他们会将整体架构置于熵增状态。因为在此状态下，有两种实现用户身份验证的方式，即新的OAuth 2.0路径和旧客户端的基于密码/会话的路径。在这一点上，团队实际上远离了实现更快更改的总体目标。对于新来的老系统开发人员来说，现在需要处理两条代码路径了。这实际上增加了熟悉代码的工作量，还导致更改和测试代码的过程变得更慢了。

If the team stops here and pivots into building some other service or feature, they leave the overall architecture in a state of increased entropy. In this state there are two ways of authenticating the user, the new OAuth 2.0 base path, and old client’s password/session based path. At this point the teams are actually further away from their overall goal of making changes faster. Any new developer to the monolith code needs to deal with two code paths, increased cognitive load of understanding the code, and slower process of changing and testing it.



相反，团队可以在我们的原子演进单元中包含以下步骤：

（3）将旧客户端的基于密码/会话的身份验证替换为OAuth 2.0路径

（4）从老系统中删除旧的验证路径

Instead the team can include the following steps in our atomic unit of evolution:

(3) Replace old client’s password/session based authentication with OAuth 2.0 path

(4) Retire the old authentication code path from the monolith



在这一点上，我们可以争辩说，在这一点上，团队已经接近目标架构。

At this point we can argue that the teams have gotten closer to the target architecture.



![img](https://martinfowler.com/articles/break-monolith-into-microservices/fitness-function.svg)

图9：使用原子演进步骤将体系结构演进为微服务。即使中间代码更改可能会导致其远离其目标，但每步完成后，整体体系结构都朝向目标方向改进

Figure 9: Evolve the architecture towards microservices with atomic steps of architecture evolution where after each step the overall architecture is improved towards its goal even though intermediary code changes might take it further away from its fitness objective



> 单体系统解构的原子单元包括：
>
> - 解耦新服务
> - 将调用方重定向至新服务
> - 从单体系统中删除老代码
> - 反模式：解耦新服务给新的调用方调用，但是却不淘汰旧服务



> The atomic unit of monolith decomposition includes:
>
> * Decouple the new service
> * Redirect all consumers to new service
> * Retire the old code path in the monolith.
> * The anti-pattern: Decouple the new service, use for new consumers and never retire the old.



我经常发现，团队完成了一个业务功能的迁移，并且在新的业务功能建立之后立即宣告胜利，而不淘汰旧的代码路径，即上述的反模式。论其主要原因，一是注重引入新功能带来的短期收益，二为淘汰旧实现所需的总体工作量，同时还面临着开发新功能的优先级竞争。为了做正确的事情，我们需要使演进的原子步骤尽可能小。

\I often find teams end migration of a capability out of the monolith and claim victory as soon as the new capability is built without retiring the old code path, the anti-pattern described above. The main reasons for this are (a) the focus on short-term benefits of introducing a new capability and (b) the total amount of effort required to retire the old implementations while facing competing priorities for building new features. In order to do the right thing, we need to strive for making the atomic steps as small as possible.



通过这种方式进行迁移，我们可以在解耦的旅途中小憩，也可以安全地停下来休养生息，并在这漫长的旅程中幸存下来，彻底消灭旧系统。

Migrating with this approach we can break up the journey to shorter trips. We can safely stop, revive and survive this long journey, slaying the monolith.