
# **1.** 简介

欢迎来到[Julia](https://julialang.org)的包管理器Pkg的文档。文档包含如管理软件包安装，开发软件包，使用软件包注册表等事项。

在整个手册中，示例使用了Pkg的REPL接口，即Pkg REPL模式。还有一个函数式 API，在非交互式工作时最好使用它。这个 API 在 [API Reference](@ref)部分有文档说明。

## 背景和设计

不像传统的包管理器，安装和管理一个全局的软件包集合，Pkg是围绕“environments(环境)”设计的： 独立的软件包集合可以是单个项目本地的，也可以通过名称共享和选择。
环境中的包和版本的确切集合被捕获在一个 _manifest_ 文件中，可以将其签入项目存储库并在版本控制中进行跟踪，从而显着提高项目的可复现性。如果您曾经尝试运行一段时间未使用的代码，却发现由于更新或卸载了项目正在使用的一些包而无法正常工作，那么您将了解这种方法的动机。在 Pkg 中，由于每个项目都维护自己独立的软件包版本集，因此您将永远不会再遇到此问题。此外，如果您在一个新系统上检出(checkout)一个项目，您可以简单地实现其 manifest 文件描述的环境，立即启动并运行一组已知良好的依赖项。

由于环境是相互独立管理和更新的 ，因此 Pkg 中的[“依赖地狱”](https://en.wikipedia.org/wiki/Dependency_hell)得到了显着缓解。如果你想在一个新项目中使用某个包的最新和最好的版本，但是你在另一个项目中被困在一个旧版本上，那也没问题——因为它们有不同的环境，它们可以使用不同的版本，这两者同时安装在系统的不同位置。每个包版本的位置都是规范的，所以当环境使用相同版本的包时，它们可以共享安装，避免不必要的包重复。不再被任何环境使用的旧版本包由包管理器定期进行“垃圾收集”。

Pkg 处理本地环境的方法对于使用过 Python `virtualenv` 或 Ruby `bundler` 的人来说可能比较熟悉。在 Julia 中，我们没有hacking语言的代码加载机制来支持环境，而是受益于 Julia 原生地理解它们。此外，Julia 环境是“stackable”的：您可以用一个环境覆盖另一个环境，从而可以访问主环境之外的其他包。这使得在提供主要环境的项目上工作变得容易，同时仍然可以从 REPL 访问所有常用的开发工具，如分析器、调试器等，只需在 load path 中有一个包含这些开发工具的环境。

最后但同样重要的是，Pkg 旨在支持联合的包注册表。这意味着它允许由不同方管理的多个注册表无缝交互。特别是，这包括可以存在于公司防火墙后面的私有注册表。您可以使用与安装和管理官方 Julia 包完全相同的工具和工作流程，从私有注册表安装和更新自己的包。如果您迫切需要为您公司的重要产品使用的公共软件包应用补丁程序，您可以在公司的内部注册表中标记它的私有版本，并轻松快速地为您的开发人员和运营团队获取修复程序，而无需等待上游补丁的接收和发布。并且，一旦官方发布了修复程序，您只需升级您的依赖项，就可以再次回到官方的发行版本上。

