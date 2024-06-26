# 第十六章：评估

# 第一章

1.  为什么您应该关心软件架构？

+   架构允许您实现和维护软件的必要特性。关注和关心它可以防止项目出现意外的架构，从而失去质量，并且可以防止软件腐败。

1.  在敏捷团队中，架构师应该是最终的决策者吗？

+   不。敏捷是关于赋予整个团队权力。架构师将他们的经验和知识带到了桌面上，但如果一个决定必须得到整个团队的接受，那么团队应该拥有它，而不仅仅是架构师。考虑利益相关者的需求在这里也非常重要。

1.  **单一责任原则**（SRP）与内聚性有何关系？

+   遵循 SRP 会导致更好的内聚性。如果一个组件开始具有多个责任，通常它的内聚性会降低。在这种情况下，最好将其重构为多个组件，每个组件都具有单一责任。这样，我们增加了内聚性，使代码更容易理解，开发和维护。

1.  在项目的生命周期的哪些阶段可以从拥有架构师中获得好处？

+   架构师可以从项目开始直到进入维护阶段为项目带来价值。最大的价值可以在项目开发的早期阶段实现，因为这是关于项目外观应该如何的关键决定。然而，这并不意味着架构师在开发过程中没有价值。他们可以确保项目走上正确的道路并保持在轨道上。通过协助决策和监督项目，他们确保代码不会出现意外的架构，并且不会受到软件腐败的影响。

1.  遵循 SRP 的好处是什么？

+   遵循 SRP 的代码更容易理解和维护。这也意味着它有更少的错误。

# 第二章

1.  RESTful 服务的特点是什么？

+   显然，使用 REST API。

+   无状态性-每个请求包含其处理所需的所有数据。请记住，这并不意味着 RESTful 服务不能使用数据库，恰恰相反。

+   使用 Cookie 而不是保持会话。

1.  您可以使用哪些工具包来帮助您创建弹性分布式架构？

+   Netflix 的 Simian Army。

1.  在微服务中应该使用集中式存储吗？为什么/为什么不？

+   微服务应该使用分散式存储。每个微服务应该选择最适合自己的存储类型，因为这会提高效率和可扩展性。

1.  何时应该编写有状态的服务而不是无状态的服务？

+   只有在没有理由使用无状态服务并且您不需要扩展时才需要有状态服务。例如，当客户端和服务必须保持它们的状态同步或者要发送的状态非常庞大时。

1.  经纪人和中介者有何不同？

+   中介者在服务之间“调解”，因此需要知道如何处理每个请求。经纪人只知道将每个请求发送到哪里，因此它是一个轻量级组件。它可以用来创建发布-订阅（pub-sub）架构。

1.  N 层架构和 N 层架构之间有什么区别？

+   层次是逻辑的，指定了代码的组织方式。层是物理的，指定了代码的运行方式。每个层必须与其他层分离，可以通过在不同的进程中运行，甚至在不同的机器上运行来实现。

1.  您应该如何处理用微服务架构替换单体架构？

+   逐步进行。从单体架构中剥离出小的微服务。您可以使用《第四章，架构和系统设计》中描述的窒息者模式来帮助您完成这项工作。

# 第三章

1.  质量属性是什么？

+   系统可能具有的特征或特质。通常被称为“ilities”，因为它们的名称中许多都有这个后缀，例如可移植性。

1.  在收集需求时应该使用哪些来源？

+   系统的上下文，现有文档和系统的利益相关者。

1.  当您定义一个函数时，如何能够判断一个需求是否具有架构重要性？

+   **架构重要需求**（**ASRs**）通常需要一个单独的软件组件，影响系统的大部分，难以实现，和/或迫使您做出权衡。

1.  您应该如何以图形方式记录各方可能对系统的功能需求？

+   准备一个用例图。

1.  开发视图文档何时有用？

+   在您开发具有许多模块并且需要将全局约束和常见设计选择传达给所有软件团队的大型系统的情况下。

1.  如何自动检查您的代码 API 文档是否过时？

+   Doxygen 具有内置检查，比如警告您关于函数签名和注释中的参数之间不匹配的检查。

1.  您应该如何在图表上显示给定操作是由系统的不同组件处理的？

+   为此目的使用 UML 交互图之一。序列图是一个不错的选择，尽管在某些情况下通信图也可以很好地完成。

# 第四章

1.  什么是事件溯源？

+   这是一种架构模式，依赖于跟踪改变系统状态的事件，而不是跟踪状态*本身*。它带来了诸如较低的延迟、免费审计日志和可调试性等好处。

1.  CAP 定理的实际后果是什么？

+   随着网络分区的发生，如果您想要一个分布式系统，您需要在一致性和可用性之间做出选择。在分区的情况下，您可以返回陈旧的数据、错误，或者冒风险超时。

1.  您可以如何使用 Netflix 的 Chaos Monkey？

+   它可以帮助您为服务的意外停机做好准备。

1.  缓存可以应用在哪里？

+   可以在客户端的一侧，在 Web 服务器、数据库或应用程序的前面，或者在靠近潜在客户端的主机上，具体取决于您的需求。

1.  您应该如何防止应用程序在整个数据中心崩溃时？

+   通过使用地理。

1.  为什么应该使用 API 网关？

+   为了简化客户端代码，因为它不需要硬编码服务实例的地址。

1.  Envoy 如何帮助您实现各种架构目标？

+   它通过提供背压、断路器、自动重试和异常检测来帮助系统的容错性。

+   它通过允许金丝雀发布和蓝绿部署来帮助部署能力。

+   它还提供负载平衡、跟踪、监控和度量。

# 第五章

1.  当不再使用时，如何确保我们代码的每个打开的文件都会关闭？

+   通过使用 RAII 习惯用法；例如，通过使用`std::unique_ptr`，它将在其析构函数中关闭它。

1.  在 C++代码中什么时候应该使用“裸”指针？

+   仅用于传递可选（可空）引用。

1.  什么是推断指南？

+   一种告诉编译器应该为模板推断哪些参数���方法。它们可以是隐式的或用户定义的。

1.  何时应该使用`std::optional`，何时应该使用`gsl::not_null`？

+   前者是用于我们想要传递包含的值的情况。后者只是传递指向它的指针。此外，前者可以为空，而后者总是指向一个对象。

1.  范围算法与视图有何不同？

+   算法是急切的，而视图是懒惰的。算法还允许使用投影。

1.  在定义函数时，如何能够比仅指定概念名称更多地限制您的类型？

+   通过使用`requires`子句。

1.  `import X`与`import <X>`有何不同？

+   后者允许从导入的`X`头文件中可见宏。

# 第六章

1.  三、五和零的规则是什么？

+   编写具有可预测语义和更少错误的类型的最佳实践。

1.  何时应该使用 niebloids 而不是隐藏的朋友？

+   Niebloids“禁用”ADL，而隐藏的友元依赖于它被找到。因此，前者可以加快编译速度（考虑的重载更少），而后者可以帮助您实现定制点。

1.  如何改进`Array`接口以更适合生产？

+   应该添加`begin`，`end`及其常量和反向等效项，以便它可以像一个适当的容器一样使用。例如，`value_type`，`pointer`和`iterator`等特性可以在通用代码中重用。在成员中添加`constexpr`和`noexcept`可以增加安全性和性能。`operator[]`的`const`重载也是缺失的。

1.  折叠表达式是什么？

+   折叠表达式是指将参数包在二元函数器上折叠或减少的表达式。换句话说，这些语句将给定操作应用于所有传递的可变模板参数，以便产生单个值（或`void`）。

1.  何时不应该使用静态多态性？

+   当您需要为您的代码的消费者提供一种在运行时添加更多类型的方法时。

1.  在眨眼示例中如何节省一次额外的分配？

+   通过避免在添加元素时调整向量的大小。

# 第七章

1.  在 CMake 中安装和导出目标有什么区别？

+   出口意味着目标将对其他试图找到我们的软件包的项目可用，即使我们的代码没有安装。 CMake 的软件包注册表可用于存储有关导出目标位置的数据。二进制文件永远不会离开构建目录。安装需要将目标复制到某个地方，并且如果不是系统目录，则设置路径到配置文件或目标本身。

1.  如何使您的模板代码编译更快？

+   遵循 Chiel 的规则。

1.  如何在 Conan 中使用多个编译器？

+   使用 Conan 配置文件。

1.  如果您想使用先前的 C++11 GCC ABI 编译您的 Conan 依赖项，应该怎么做？

+   将`compiler.libcxx`设置为`libstdc++`而不是`libstdc++11`。

1.  在 CMake 中如何确保强制使用特定的 C++标准？

+   通过调用`set_target_properties(our_target PROPERTIES CXX_STANDARD our_required_cxx_standard CXX_STANDARD_REQUIRED YES CXX_EXTENSIONS NO)`。

1.  在 CMake 中如何构建文档并将其与您的 RPM 软件包一起发布？

+   创建一个目标以生成文档，如[第三章](https://cdp.packtpub.com/hands_on_software_architecture_with_c__/wp-admin/post.php?post=26&action=edit)中所述，“功能和非功能需求”，将其安装到`CMAKE_INSTALL_DOCDIR`，然后确保路径未在`CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST`变量中指定。

# ��8 章

1.  测试金字塔的基础层是什么？

+   单元测试。

1.  有哪些非功能测试？

+   性能，耐久性，安全性，可用性，完整性和可用性。

1.  有一个著名的根本原因分析方法的名字是什么？

+   5 个为什么

1.  在 C++中是否可能测试编译时代码？

+   是的，例如使用`static_assert`。

1.  在为具有外部依赖项的代码编写单元测试时应该使用什么？

+   测试替身，比如模拟和伪造。

1.  单元测试在持续集成/持续部署中的作用是什么？

+   它们是一个门控机制的基础，并作为一个早期警告功能。

1.  列举一些允许测试基础设施代码的工具。

+   Serverspec，Testinfra，Goss。

1.  在单元测试中访问类的私有属性和方法是一个好主意吗？

+   您应该设计类，以便您永远不必直接访问它们的私有属性。

# 第九章

1.  持续集成在开发过程中如何节省时间？

+   它允许您在进入生产之前捕获错误并修复它们。

1.  你需要单独的工具来实现持续集成和持续部署吗？

+   流水线通常使用单个工具编写；实际测试和部署使用多个工具。

1.  在会议中进行代码审查何时是有意义的？

+   当异步代码审查时间太长时。

1.  在持续集成期间，您可以使用哪些工具来评估代码的质量？

+   测试，静态分析。

1.  谁参与指定 BDD 场景？

+   开发人员、QA、业务。

1.  何时应考虑使用不可变基础设施？何时应该排除它？

+   它最适用于无状态服务或可以使用数据库或网络存储外包存储的服务。不适用于有状态服务。

1.  您如何描述 Ansible、Packer 和 Terraform 之间的区别？

+   Ansible 旨在对现有 VM 的配置管理，Packer 用于构建云 VM 镜像，Terraform 用于构建云基础设施（如网络、VM 和负载均衡器）。

# 第十章

1.  为什么安全在现代系统中很重要？

+   现代系统通常连接到网络，因此可能容易受到外部攻击。

1.  并发性带来的一些挑战是什么？

+   代码更难设计和调试。可能会出现更新问题。

1.  C++核心指南是什么？

+   记录如何构建 C++系统的最佳实践。

1.  安全编码和防御性编码之间有什么区别？

+   安全编码为最终用户提供了健壮性，而防御性编码为接口消费者提供了健壮性。

1.  您应该如何检查您的软件是否包含已知的漏洞？

+   通过使用 CVE 数据库或自动扫描程序，如 OWASP Dependency-Check 或 Snyk。

1.  静态分析和动态分析之间有什么区别？

+   静态分析是在不执行源代码的情况下执行的。动态分析需要执行。

1.  静态链接和动态链接之间有什么区别？

+   使用静态链接，可执行文件包含运行应用程序所需的所有代码。使用动态链接，一些代码部分（动态库）在不同的可执行文件之间共享。

1.  您如何使用编译器来解决安全问题？

+   现代编译器包括检查某些缺陷的消毒剂。

1.  您如何在持续集成流程中实施安全意识？

+   通过使用扫描漏洞并执行各种静态和动态分析的自动化工具。

# 第十一章

1.  我们可以从本章微基准测试的性能结果中学到什么？

+   二分搜索比线性搜索快得多，即使要检查的元素数量并不那么多。这意味着计算复杂度（也称为大 O）很重要。可能在您的机器上，即使对于二分搜索的最大数据集上的最长搜索也比线性搜索的最短搜索还要快！

+   根据缓存大小，您可能还注意到当数据无法适应特定缓存级别时，增加所需内存会导致减速。

1.  我们如何遍历多维数组对性能重要吗？为什么/为什么不？

+   这是至关重要的，因为我们可能会在内存中线性访问数据，CPU 预取器会喜欢并奖励我们更好的性能，或者跳过内存，从而阻碍我们的性能。

1.  在我们的协程示例中，为什么我们不能在`do_routine_work`函数内创建我们的线程池？

+   因为寿命问题。

1.  我们如何重新设计我们的协程示例，以便它使用生成器而不仅仅是任务？

+   生成器的主体需要`co_yield`。此外，我们池中的线程需要同步，可能需要使用原子操作。

# 第十二章

1.  面向服务的架构中服务的属性是什么？

+   它是业务活动的代表，具有明确定义的结果。

+   它是自包含的。

+   它对用户是不透明的。

+   它可能由其他服务组成

1.  Web 服务的一些好处是什么？

+   它们易于使用常见工具进行调试，与防火墙配合良好，并且可以利用现有基础设施，如负载平衡、缓存和 CDN。

1.  微服务不是一个好选择的时候是什么时候？

+   当 RPC 和冗余成本超过收益时。

1.  消息队列的一些用例是什么？

+   IPC，事务服务，物联网。

1.  选择 JSON 而不是 XML 的一些好处是什么？

+   JSON 需要更低的开销，正在取代 XML 的流行，并且应该更容易被人类阅读。

1.  REST 如何建立在 Web 标准之上？

+   它使用 HTTP 动词和 URL 作为构建块。

1.  云平台与传统托管有何不同？

+   云平台提供易于使用的 API，这意味着可以编程资源。

# 第十三章

1.  微服务如何帮助您更好地利用系统资源？

+   仅扩展缺乏的资源比整个系统更容易。

1.  微服务和单体应用程序如何共存（在不断发展的系统中）？

+   新功能可以开发为微服务，而某些功能可以从单体中拆分和外包。

1.  哪些类型的团队最能从微服务中受益？

+   遵循 DevOps 原则的跨功能自主团队。

1.  在引入微服务时为什么需要成熟的 DevOps 方法？

+   通过单独的团队手动测试和部署大量微服务几乎是不可能的。

1.  统一的日志层是什么？

+   这是一个可配置的收集、处理和存储日志的设施。

1.  日志和跟踪有何不同？

+   日志通常是人类可读的，侧重于操作，而跟踪通常是机器可读的，侧重于调试。

1.  为什么 REST 可能不是连接微服务的最佳选择？

+   与 gRPC 相比，它可能提供更大的开销。

1.  微服务的部署策略是什么？每种策略的好处是什么？

+   每个主机一个服务-更容易调整机器以适应工作负载。

+   每个主机多个服务-更好地利用资源。

# 第十四章

1.  应用程序容器与操作系统容器有何不同？

+   应用程序容器旨在托管单个进程，而操作系统容器通常运行 Unix 系统中通常可用的所有进程。

1.  Unix 系统中沙盒环境的一些早期示例是什么？

+   chroot，BSD Jails，Solaris Zones。

1.  为什么容器非常适合微服务？

+   它们提供了一个统一的接口，可以运行应用程序，而不受基础技术的影响。

1.  容器和虚拟机之间的主要区别是什么？

+   容器更轻量级，因为它们不需要虚拟化程序、操作系统内核的副本，或者辅助进程，比如 init 系统或 syslog。

1.  应用程序容器何时不是一个好选择？

+   当您想将多进程应用程序放入单个容器中时。

1.  构建多平台容器映像的一些工具是什么？

+   manifest-tool，docker buildx。

1.  除了 Docker，还有哪些其他容器运行时？

+   Podman，containerd，CRI-O。

1.  一些流行的编排器是什么？

+   Kubernetes，Docker Swarm，Nomad。

# 第十五章

1.  在云中运行应用程序和使它们成为云原生应用程序有什么区别？

+   云原生设计包括现代技术，如容器和无服务器，打破了对虚拟机的依赖。

1.  您可以在本地运行云原生应用程序吗？

+   是的，例如可以使用 OpenStack 等解决方案。

1.  Kubernetes 的最小**高可用**（**HA**）集群大小是多少？

+   最小的 HA 集群需要控制平面中的三个节点和三个工作节点。

1.  哪个 Kubernetes 对象代表允许网络连接的微服务？

+   服务。

1.  为什么在分布式系统中日志不足？

+   在分布式系统中收集日志并查找它们之间的关联是有问题的。分布式跟踪更适合某些用例。

1.  服务网格如何帮助构建安全系统？

+   服务网格抽象了不同系统之间的连接，从而可以应用加密和审计。

1.  GitOps 如何提高生产力？

+   它使用一个熟悉的工具 Git 来处理 CI/CD，而无需编写专门的流水线。

1.  监控的标准 CNCF 项目是什么？

+   Prometheus。
