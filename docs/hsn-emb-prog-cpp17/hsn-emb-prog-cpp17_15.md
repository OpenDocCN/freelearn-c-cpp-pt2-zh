# 第十二章：最佳实践

与每个软件项目一样，存在许多常见问题和陷阱。在嵌入式开发中，硬件方面增加了独特的问题。从资源管理问题到中断故障和硬件问题引起的奇怪行为，本附录向您展示如何预防和处理许多这些问题。此外，它还向您展示了各种优化方法以及需要注意的事项。在本附录中，我们将涵盖以下主题：

+   优化嵌入式代码的安全方法

+   如何避免和解决各种常见的软件和硬件相关问题

+   认识到硬件的不完美世界以及如何将其整合到设计中

# 所有最好的计划

与任何项目一样，预期设计与实际功能之间存在不可避免的差距。即使有最好的规划和丰富的经验，也总会有意想不到或未被注意到的问题。您能做的最好的事情就是尽可能做好准备。

第一步是要获得目标平台的所有可用信息，了解可用的工具，并拥有一个坚实的开发和测试计划。我们在本书中已经涵盖了许多这些方面。

在本附录中，我们将总结一些最佳实践，这些实践应该有助于避免一些更常见的问题。

# 与硬件合作

每个目标平台都有其自己的怪癖和特点。其中很大一部分是由于该平台的发展历史。对于 AVR 这样的平台，它相当一致，因为它是由一家公司（Atmel）在多年内开发的，因此在不同芯片和用于该平台的工具之间相当一致。

像 ESP8266（以及在某种程度上其 ESP32 后继者）这样的平台从未被设计为用作通用 MCU 系统，这在其相当零碎和分散的软件生态系统中表现出来。尽管在过去几年中情况有所好转，各种框架和开源工具平滑了最粗糙的地方，但由于缺乏文档、工具问题和芯片内调试的缺乏，这是一个容易犯错误的平台。

ARM MCU（Cortex-M）由众多制造商生产，配置繁多。尽管编程这些 MCU 往往是相当一致的，使用诸如 OpenOCD 之类的工具，但每个 MCU 添加的外设在制造商之间往往大不相同，我们将在下一节中进行讨论。

最后，ARM SoCs 和类似的平台与 ARM MCU 类似，但其体系结构更加复杂，外设较少。此外，ARM SoCs 还添加了复杂的初始化程序，需要全面的引导加载程序，这就是为什么大多数人选择使用现成的 Linux 镜像或类似的 SoC，并对其进行开发。

在这里，没有真正的对与错的答案。大部分取决于项目的需求，但重要的是您对所使用的硬件平台有一个良好的概述。

# 外设的混乱世界

ARM MCU 的一个非常有趣的现实是，它们具有不同且通常不兼容的外设，映射到内存空间中高度不同的区域。最糟糕的是定时器外设，其复杂性各不相同，它们通常能够在 GPIO 引脚上生成任何所需的输出信号，包括 PWM，以及作为基于中断的定时器来控制固件的执行。

配置定时器外设和类似的复杂外设并不是一件简单的事情。同样，使用内置 MAC 与外部 PHY（以太网物理接口）需要大量深入的知识来了解如何配置它们。阅读数据手册和应用笔记在这里是必不可少的。

依赖诸如 ST 的 CubeMX 软件之类的工具生成的代码，用于他们的 STM32 系列 ARM MCU，可能会导致你因为忘记在 CubeMX 编辑器中勾选一些选项而与非功能性代码搏斗，因为你不知道这些选项是用来做什么的。

使用这种自动生成工具或制造商提供的高级库没有错，因为它们可以显著地简化生活。然而，接受这一决定所带来的风险是至关重要的，因为这需要你相信提供的代码是正确的，或者花时间验证它确实是正确的。

为了使不同 MCU 和 SoC 上的外设使用不那么混乱，必须在某个地方添加一层抽象，以便实现代码的可移植性。关键是确保这确实会让生活变得更容易，而不仅仅是增加可能会使当前项目或未来项目受挫的另一个潜在问题。

# 了解你的工具

在嵌入式项目中工作时，你必须知道目标平台上存在哪些工具以及它们的工作原理。这包括通过 JTAG 或其他接口对 MCU 进行编程，并启动用于片上调试的调试会话，以及片上调试的限制。在使用工具之前，最好先阅读工具的手册或文档，并阅读其他开发人员对这些工具的经验。

我们在之前的章节中看过许多这样的工具，包括 MCU 和 SoC 平台，以及在将其刷入目标硬件之前验证 MCU 设计的方法。

# 选择异步方法

许多硬件设备和操作需要时间来完成。因此，选择使用中断和定时器进行异步操作而不是阻塞操作是有意义的。

在进行裸机编程时，你往往会使用一个单独的循环，其中包含中断例程和定时器，允许你响应和轮询事件。如果以完全异步的方式编程，这个主循环将有效地处理任务，而中断处理程序将更新需要处理的数据。

即使在 SoC 平台上，使用异步方法也是一个好主意，因为诸如网络操作和其他 I/O 操作之类的事情可能需要比预期更长的时间。处理操作未完成的方法是另一个可能出现的问题。

# 阅读数据表

特别是对于 MCU，数据表为我们提供了关于硬件工作方式的许多宝贵信息，例如如何配置内部系统时钟，各个外设的工作方式，以及可用的寄存器及其含义。

即使你使用的是现有的板而不是自定义硬件系统，了解底层硬件也是值得的，即使只是从对 MCU 或 SoC 数据表的粗略阅读中。

# 保持中断处理程序的简短

中断的本质决定了它会打断处理器的正常执行，转而执行中断处理程序。我们在中断处理程序中花费的每一微秒都意味着我们无法运行其他例程或处理其他中断。

为了防止由此产生的任何问题，中断处理程序(ISR)应尽可能保持简短，最好只是在结束 ISR 并恢复正常操作之前以快速和安全的方式更新单个值。

# 8 位意味着 8 位

毫不奇怪，在 8 位 MCU 上使用 16 位和 32 位整数非常慢。这是因为系统必须对相同的整数值执行多次操作，因为它一次只能将 8 位装入其寄存器。

同样，对于没有浮点单元(FPU)的系统使用浮点变量意味着这样的操作非常适合使系统变得非常缓慢，因为只有整数处理器在努力跟上旨在模拟浮点操作的指令流。

# 不要重复造轮子

如果存在一个质量良好且适用于目标平台和项目许可的库或框架，那么使用它而不是编写自己的实现。

保留一个常用片段和示例的库作为参考，不仅是为了自己，也是为了其他团队成员。记住可以找到某个功能示例的地方比记住该功能的确切实现细节更容易。

# 在优化之前三思

优化代码的诀窍在于，在没有充分了解你提出的改变会产生什么影响的情况下，你不应该尝试这样做。仅仅有一种感觉或模糊的想法可能不够好。

虽然基于 SoC 的平台通常会给你更多的余地，但对于 MCU 平台来说，了解添加一个关键字或使用不同的数据结构来存储一些信息将意味着什么是至关重要的。

在这里最糟糕的事情是假设你在 SBC 和台式系统上使用的优化方法会对 MCU 平台产生类似的效果。由于修改后的哈佛架构和 AVR 等平台的各种怪癖，这些方法很可能会适得其反，或者幸运的话，只是无效。

在这里，为（MCU）平台提供的应用程序说明对于了解如何优化硬件非常有用。这里的要点是在进行优化之前进行研究，就像在考虑项目设计之前不会只是开始编写代码一样。

# 需求不是可选的

在没有为项目制定明确的需求的情况下编写嵌入式软件，就好像开始建造新房子却不清楚应该有多少个房间，窗户和门应该在哪里，以及管道和电线应该走在哪里。

虽然你完全可以开始编写可工作的代码，并在短时间内制作出一个功能原型，但现实是，这些原型通常会在没有充分考虑产品生命周期或那些将不得不在未来几年内不断修补固件以添加原始固件代码从未设计的功能的时间内投入生产。

完成产品需满足的需求后，这些需求被转化为架构（应用程序的整体结构），然后转化为设计（将要实现的内容）。设计然后被转化为实际的代码。

这种方法的优势在于，不仅需要回答很多关于为什么以某种特定方式完成某事的问题，而且还会产生大量文档，一旦项目完成，这些文档就可以实际使用。

此外，在嵌入式项目中，具备完整的需求集可以节省大量时间和金钱，因为它可以让人们在不必为了“以防万一”而在更强大的芯片上花费更多金钱，而是可以为项目选择合适的 MCU 或 SoC。它还可以防止尴尬的中期发现，即“遗忘”的功能突然需要改变硬件设计。

# 文档能拯救生命

程序员不喜欢编写文档，因此他们称自己编写的代码为“自我说明的代码”，这已经成为了一个笑话。事实是，如果没有清晰的设计需求、架构概述、设计计划和 API 文档，你就会冒着项目的未来以及依赖软件运行的其他开发人员和最终用户的风险。

在你开始编写第一行代码之前，按照程序进行所有无聊的文书工作似乎是完全令人沮丧的。不幸的是，现实情况是，如果没有这种努力，这些知识将继续锁在项目开发人员的头脑中，这会使固件集成到嵌入式项目的其余部分变得复杂，并且使未来的维护，特别是如果转移到不同的团队，成为一个艰巨的前景。

事实很简单，没有代码是自我说明的，即使是这样，也没有硬件工程师会浏览成千上万行的代码，以弄清在特定的输入条件下在特定的 GPIO 引脚上输出了什么样的信号。

# 测试代码意味着试图摧毁它

编写测试时的一个常见错误是编写你期望能够正常工作的测试场景。这样做是错过了重点。虽然当一个特定的解析例程在处理完美格式的数据时做到了它应该做的事情是很美妙的，但在现实场景中并没有多大帮助。

虽然你可能会得到完美的数据，但同样有可能在你的代码中得到完全损坏甚至垃圾数据。目标是确保无论你对输入数据做了多么可怕的事情，它都不会对系统的其余部分产生负面影响。

所有输入都应该经过验证和检查。如果有什么不对劲，就应该拒绝，而不是允许它在以后的代码中引起问题。

# 总结

在这个附录中，我们列举了在嵌入式软件设计中可能出现的一些常见问题和陷阱。

读者现在应该知道项目中存在哪些阶段，以及在项目的每一步都有记录的原因。
