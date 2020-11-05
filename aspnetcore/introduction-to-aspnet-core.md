---
title: ASP.NET Core 简介
author: rick-anderson
description: 获取 ASP.NET Core 的简介，它是一个跨平台的高性能开源框架，用于生成启用云且连接 Internet 的新式应用。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: 4301e0d59364573767ab4cae25a4818ff84b9abc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052222"
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core 简介

作者：[Daniel Roth](https://github.com/danroth27)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 是一个跨平台的高性能[开源](https://github.com/dotnet/aspnetcore)框架，用于生成启用云且连接 Internet 的新式应用。 使用 ASP.NET Core，您可以：

* 生成 Web 应用和服务、[物联网 (IoT)](https://www.microsoft.com/internet-of-things/) 应用和移动后端。
* 在 Windows、macOS 和 Linux 上使用喜爱的开发工具。
* 部署到云或本地。
* 在 [.NET Core](/dotnet/core/introduction) 上运行。

## <a name="why-choose-aspnet-core"></a>为何选择 ASP.NET Core？

数百万开发人员在使用或使用过 [ASP.NET 4.x](/aspnet/overview) 创建 Web 应用。 ASP.NET Core 是对 ASP.NET 4.x 的重新设计，其中包括体系结构上的更改，产生了更精简、更模块化的框架。

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>使用 ASP.NET Core MVC 生成 Web API 和 Web UI

ASP.NET Core MVC 提供生成 [Web API](xref:tutorials/first-web-api) 和 [Web 应用](xref:tutorials/razor-pages/index)所需的功能：

* [Model-View-Controller (MVC) 模式](xref:mvc/overview) 使 Web API 和 Web 应用可测试。
* [Razor Pages](xref:razor-pages/index) 是基于页面的编程模型，它让 Web UI 的生成更加简单高效。
* [Razor 标记](xref:mvc/views/razor)提供了适用于 [Razor Pages](xref:razor-pages/index) 和 [MVC 视图](xref:mvc/views/overview)的高效语法。
* [标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。
* 内置的[多数据格式和内容协商](xref:web-api/advanced/formatting)支持使 Web API 可访问多种客户端，包括浏览器和移动设备。
* [模型绑定](xref:mvc/models/model-binding)自动将 HTTP 请求中的数据映射到操作方法参数。
* [模型验证](xref:mvc/models/validation)自动执行客户端和服务器端验证。

## <a name="client-side-development"></a>客户端开发

ASP.NET Core 与常用客户端框架和库（包括 [Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react) 和 [Bootstrap](https://getbootstrap.com/)）无缝集成。 有关详细信息，请参阅 <xref:blazor/index> 和“客户端开发”下的相关主题。

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Core 目标框架

ASP.NET Core 3.x 和更高版本只能面向 .NET Core。 通常，ASP.NET Core 由 [.NET Standard](/dotnet/standard/net-standard) 库组成。 使用 .NET Standard 2.0 编写的库在[实现 .NET Standard 2.0 的任何 .NET 平台](/dotnet/standard/net-standard#net-implementation-support)上运行。

面向 .NET Core 有以下几个优势，并且这些优势会随着每次发布增加。 与 .NET Framework 相比，.NET Core 的部分优势包括：

* 跨平台。 在 Windows、macOS 和 Linux 上运行。
* 性能更强
* [并行版本控制](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* 新 API
* 开源

## <a name="recommended-learning-path"></a>推荐的学习路径

建议通过以下一系列教程来了解如何开发 ASP.NET Core 应用：

1. 按照你要开发或维护的应用类型的教程操作：

   |应用类型  |方案  |教程  |
   |----------|----------|----------|
   |Web 应用                   | 新的服务器端 Web UI 开发 |[Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start) |
   |Web 应用                   | 维护 MVC 应用 |[MVC 入门](xref:tutorials/first-mvc-app/start-mvc)|
   |Web 应用                   | 客户端 Web UI 开发 |[开始使用 Blazor](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |Web API                   | RESTful HTTP 服务 |[创建 Web API](xref:tutorials/first-web-api)&dagger; |
   |远程过程调用应用 | 使用协议缓冲区的协定优先服务 |[开始使用 gRPC 服务](xref:tutorials/grpc/grpc-start) |
   |实时应用             | 服务器和连接的客户端之间的双向通信 |[开始使用 SignalR](xref:tutorials/signalr) |

1. 按照介绍如何进行基本数据访问的教程操作。

   |方案  |教程  |
   |----------|----------|
   |新的开发        |[带 Entity Framework Core 的 Razor 页面](xref:data/ef-rp/intro) |
   |维护 MVC 应用 |[结合使用 MVC 和 Entity Framework Core](xref:data/ef-mvc/intro) |

1. 阅读适用于所有应用类型的 ASP.NET Core [基础知识](xref:fundamentals/index)的概述。

1. 浏览目录以了解其他感兴趣的主题。

&dagger;此外，还提供了一个[交互式 Web API 教程](/learn/modules/build-web-api-net-core)。 无需在本地安装开发工具。 代码在浏览器中的 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 中运行，并且 [curl](https://curl.haxx.se/) 用于测试。

## <a name="migrate-from-net-framework"></a>从 .NET Framework 迁移

有关将 ASP.NET 4.x 应用迁移到 ASP.NET Core 的参考指南，请参阅 <xref:migration/proper-to-2x/index>。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 是一个跨平台的高性能[开源](https://github.com/dotnet/aspnetcore)框架，用于生成启用云且连接 Internet 的新式应用。 使用 ASP.NET Core，您可以：

* 生成 Web 应用和服务、[物联网 (IoT)](https://www.microsoft.com/internet-of-things/) 应用和移动后端。
* 在 Windows、macOS 和 Linux 上使用喜爱的开发工具。
* 部署到云或本地。
* 在 [.NET Core 或 .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) 上运行。

## <a name="why-choose-aspnet-core"></a>为何选择 ASP.NET Core？

数百万开发人员在使用或使用过 [ASP.NET 4.x](/aspnet/overview) 创建 Web 应用。 ASP.NET Core 是对 ASP.NET 4.x 的重新设计，通过体系结构上的更改，产生了更精简、更模块化的框架。

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>使用 ASP.NET Core MVC 生成 Web API 和 Web UI

ASP.NET Core MVC 提供生成 [Web API](xref:tutorials/first-web-api) 和 [Web 应用](xref:tutorials/razor-pages/index)所需的功能：

* [Model-View-Controller (MVC) 模式](xref:mvc/overview) 使 Web API 和 Web 应用可测试。
* [Razor Pages](xref:razor-pages/index) 是基于页面的编程模型，它让 Web UI 的生成更加简单高效。
* [Razor 标记](xref:mvc/views/razor)提供了适用于 [Razor Pages](xref:razor-pages/index) 和 [MVC 视图](xref:mvc/views/overview)的高效语法。
* [标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。
* 内置的[多数据格式和内容协商](xref:web-api/advanced/formatting)支持使 Web API 可访问多种客户端，包括浏览器和移动设备。
* [模型绑定](xref:mvc/models/model-binding)自动将 HTTP 请求中的数据映射到操作方法参数。
* [模型验证](xref:mvc/models/validation)自动执行客户端和服务器端验证。

## <a name="client-side-development"></a>客户端开发

ASP.NET Core 与常用客户端框架和库（包括 [Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react) 和 [Bootstrap](https://getbootstrap.com/)）无缝集成。 有关详细信息，请参阅 <xref:blazor/index> 和“客户端开发”下的相关主题。

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>面向 .NET Framework 的 ASP.NET Core

ASP.NET Core 2.x 可以面向 .NET Core 或 .NET Framework。 面向 .NET Framework 的 ASP.NET Core 应用无法跨平台，它们仅在 Windows 上运行。 通常，ASP.NET Core 2.x 由 [.NET Standard](/dotnet/standard/net-standard) 库组成。 使用 .NET Standard 2.0 编写的库在[实现 .NET Standard 2.0 的任何 .NET 平台](/dotnet/standard/net-standard#net-implementation-support)上运行。

ASP.NET Core 2.x 在实现 .NET Standard 2.0 的 .NET Framework 版本上受支持：

* 建议使用最新版本的 .NET Framework。
* .NET Framework 4.6.1 及更高版本。

ASP.NET Core 3.0 以及更高版本只能在 .NET Core 中运行。 有关此更改的详细信息，请参阅 [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/)（抢先了解 ASP.NET Core 3.0 即将推出的更改）。

面向 .NET Core 有以下几个优势，并且这些优势会随着每次发布增加。 与 .NET Framework 相比，.NET Core 的部分优势包括：

* 跨平台。 在 macOS、Linux 和 Windows 上运行。
* 性能更强
* [并行版本控制](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* 新 API
* 开源

为了帮助缩小从 .NET Framework 到 .NET Core 的 API 差距，[Windows 兼容包](/dotnet/core/porting/windows-compat-pack)使数千个仅可在 Windows 中运行的 API 可在 .NET Core 中使用。 这些 API 在 .NET Core 1.x 中不可用。

## <a name="recommended-learning-path"></a>推荐的学习路径

建议通过以下一系列教程和文章来了解如何开发 ASP.NET Core 应用：

1. 按照你要开发或维护的应用类型的教程操作。

   |应用类型  |方案  |教程  |
   |----------|----------|----------|
   |Web 应用                   | 用于新的开发        |[Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start) |
   |Web 应用                   | 用于维护 MVC 应用 |[MVC 入门](xref:tutorials/first-mvc-app/start-mvc)|
   |Web API                   |                            |[创建 Web API](xref:tutorials/first-web-api)&dagger; |
   |实时应用             |                            |[开始使用 SignalR](xref:tutorials/signalr) |

1. 按照介绍如何进行基本数据访问的教程操作。

   |方案  |教程  |
   |----------|----------|
   | 用于新的开发        |[带 Entity Framework Core 的 Razor 页面](xref:data/ef-rp/intro) |
   | 用于维护 MVC 应用 |[结合使用 MVC 和 Entity Framework Core](xref:data/ef-mvc/intro) |

1. 阅读适用于所有应用类型的 ASP.NET Core [基础知识](xref:fundamentals/index)的概述。

1. 浏览目录以了解其他感兴趣的主题。

&dagger;此外，还提供了一个 [Web API 教程](/learn/modules/build-web-api-net-core)，需要在浏览器中完全遵循，无需在本地安装 IDE。 代码在 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 中运行，并且 [curl](https://curl.haxx.se/) 用于测试。

## <a name="migrate-from-net-framework"></a>从 .NET Framework 迁移

有关将 ASP.NET 应用迁移到 ASP.NET Core 的参考指南，请参阅 <xref:migration/proper-to-2x/index>。

::: moniker-end

## <a name="how-to-download-a-sample"></a>如何下载示例

很多文章和教程中都包含有示例代码链接。

1. [下载 ASP.NET 存储库 zip 文件](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master)。
1. 解压缩 Docs-master.zip 文件。
1. 使用示例链接中的 URL 帮助你导航到示例目录。

### <a name="preprocessor-directives-in-sample-code"></a>示例代码中的预处理器指令

为了演示多个方案，示例应用使用 `#define` 和 `#if-#else/#elif-#endif` 预处理器指令选择性地编译和运行示例代码中不同的片段。 对于那些利用此方法的示例，请将 C# 文件顶部的 `#define` 指令设置为定义与你想要运行的方案相关联的符号。 一些示例要求在多个文件的顶部定义符号才能运行方案。

例如，以下 `#define` 符号列表指示四个方案可用（每个符号一个方案）。 当前示例配置运行 `TemplateCode` 方案：

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

若要更改示例以运行 `ExpandDefault` 方案，请定义 `ExpandDefault` 符号并保留剩余的符号处于被注释掉的状态：

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

若要详细了解如何使用 [C# 预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/)选择性地编译代码段，请参阅 [#define（C# 参考）](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define)和 [#if（C# 参考）](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)。

### <a name="regions-in-sample-code"></a>示例代码中的区域

一些示例应用包含由 [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) 和 [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# 指令包围的代码片段。 文档生成系统会将这些区域注入到所呈现的文档主题中。  

区域名称通常包含“代码段”一词。 下面的示例显示了一个名为 `snippet_WebHostDefaults` 的区域：

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

主题的 Markdown 文件在以下行中引用了前面的 C# 代码片段：

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

可放心忽略（或删除）代码两侧的 `#region` 和 `#endregion` 指令。 如果计划运行主题中所述的示例方案，请不要更改这些指令中的代码。 试用其他方案时，可随时更改代码。

有关详细信息，请参阅[参与 ASP.NET 文档：代码片段](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets)。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [ASP.NET Core 基础知识](xref:fundamentals/index)
* [每周 ASP.NET Community Standup](https://live.asp.net/) 介绍了团队的工作进度和计划。 它以新博客和第三方软件为重点。
