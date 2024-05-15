# 第三部分-将 LLVM 提升到下一个级别

在本节中，您将学习 LLVM 中指令选择的实现方式，并通过添加对新机器指令的支持来应用这些知识。LLVM 具有即时编译器（JIT），您将学习如何使用它以及如何根据自己的需求进行定制。您还将尝试各种工具和库，以帮助识别应用程序中的错误。最后，您将使用新的后端扩展 LLVM，这将使您具备利用 LLVM 尚未支持的新架构所需的知识。

本节包括以下章节：

+   第九章，指令选择

+   第十章，JIT 编译

+   第十一章，使用 LLVM 工具进行调试

+   第十二章，创建自己的后端