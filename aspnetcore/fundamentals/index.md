---
title: ASP.NET Core 基础知识
author: rick-anderson
description: 了解生成 ASP.NET Core 应用的基础概念。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/index
ms.openlocfilehash: a70d6aa05a2c92d19076b8d6e4ea24d7554368b6
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007113"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core 基础知识

本文概述了了解如何开发 ASP.NET Core 应用的关键主题。

## <a name="the-startup-class"></a>Startup 类

`Startup` 类位于：

* 已配置应用所需的服务。
* 已定义请求处理管道。

服务是应用使用的组件  。 例如，日志记录组件就是一项服务。 将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中  。

请求处理管道由一系列中间件组件组成  。 例如，中间件可能处理对静态文件的请求或将 HTTP 请求重定向到 HTTPS。 每个中间件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。 将配置请求处理管道的代码添加到 `Startup.Configure` 方法中。

下面是 `Startup` 类示例：

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

有关详细信息，请参阅 <xref:fundamentals/startup>。

## <a name="dependency-injection-services"></a>依赖关系注入（服务）

ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可使配置的服务适用于应用的类。 在类中获取服务实例的一种方法是使用所需类型的参数创建构造函数。 参数可以是服务类型或接口。 DI 系统在运行时提供服务。

下面是使用 DI 获取 Entity Framework Core 上下文对象的类。 突出显示的行是构造函数注入的示例：

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

虽然 DI 是内置的，但旨在允许插入第三方控制反转 (IoC) 容器（根据需要）。

有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。

## <a name="middleware"></a>中间件

请求处理管道由一系列中间件组件组成。 每个组件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。

按照惯例，通过在 `Startup.Configure` 方法中调用其 `Use...` 扩展方法，向管道添加中间件组件。 例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。

以下示例中突出显示的代码配置请求处理管道：

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

ASP.NET Core 包含一组丰富的内置中间件，并且你也可以编写自定义中间件。

有关详细信息，请参阅 <xref:fundamentals/middleware/index>。

## <a name="host"></a>主机

ASP.NET Core 应用在启动时构建主机  。 主机是封装所有应用资源的对象，例如：

* HTTP 服务器实现
* 中间件组件
* Logging
* DI
* 配置

一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。

::: moniker range=">= aspnetcore-3.0"

提供两个主机：通用主机和 Web 主机。 通用主机是推荐的机型，而 Web 主机仅很适用于向后兼容。

要创建主机的代码位于 `Program.Main` 中：

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

`CreateDefaultBuilder` 和 `ConfigureWebHostDefaults` 方法配置具有常用选项的主机，如下所示：

* 将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。
* 从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置   。
* 将日志记录输出发送到控制台并调试提供程序。

有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

提供两个主机：Web 主机和通用主机。 在 ASP.NET Core 2.x 上，通用主机仅用于非 Web 方案。

要创建主机的代码位于 `Program.Main` 中：

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

`CreateDefaultBuilder` 方法配置具有常用选项的主机，如下所示：

* 将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。
* 从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置   。
* 将日志记录输出发送到控制台并调试提供程序。

有关详细信息，请参阅 <xref:fundamentals/host/web-host>。

::: moniker-end

### <a name="non-web-scenarios"></a>非 Web 方案

其他类型的应用可通过通用主机使用横切框架扩展，例如日志记录、依赖项注入 (DI)、配置和应用生命周期管理。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和 <xref:fundamentals/host/hosted-services>。

## <a name="servers"></a>服务器

ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。 服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core 提供以下服务器实现：

* Kestrel 是跨平台 Web 服务器  。 Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。 在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。
* IIS HTTP 服务器是适用于使用 IIS 的 Windows 的服务器  。 借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。
* HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器  。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core 提供 Kestrel 跨平台服务器实现  。 在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。 Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core 提供 Kestrel 跨平台服务器实现  。 在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。 Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core 提供以下服务器实现：

* Kestrel 是跨平台 Web 服务器  。 Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。 在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。
* HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器  。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core 提供 Kestrel 跨平台服务器实现  。 在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。 Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core 提供 Kestrel 跨平台服务器实现  。 在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。 Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。

---

::: moniker-end

有关详细信息，请参阅 <xref:fundamentals/servers/index>。

## <a name="configuration"></a>配置

ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。 有适用于各种源的内置配置提供程序，例如 .json 文件、.xml 文件、环境变量和命令行参数   。 此外可以编写自定义配置提供程序。

例如，可以指定配置来自 appsettings.json 和环境变量  。 然后，当请求 ConnectionString 的值时，框架首先在 appsettings.json 文件中查找   。 如果也在环境变量中找到了值，那么来自环境变量的值将优先使用。

为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器工具](xref:security/app-secrets)。 对于生产机密，建议使用 [Azure 密钥保管库](xref:security/key-vault-configuration)。

有关详细信息，请参阅 <xref:fundamentals/configuration/index>。

## <a name="options"></a>选项

在可能的情况下，ASP.NET Core 按照选项模式来存储和检索配置值  。 选项模式使用类来表示相关设置的组。

例如，以下代码可以设置 WebSocket 选项：

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

有关详细信息，请参阅 <xref:fundamentals/configuration/options>。

## <a name="environments"></a>环境

执行环境（例如“开发”、“暂存”和“生产”）是 ASP.NET Core 中的高级概念    。 可以通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定运行应用的环境。 ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IHostingEnvironment` 实现中。 可通过 DI 在应用的任何位置使用环境对象。

以下来自 `Startup` 类的示例代码将应用配置为仅在开发过程中运行时提供详细的错误信息：

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

有关详细信息，请参阅 <xref:fundamentals/environments>。

## <a name="logging"></a>Logging

ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。 可用的提供程序包括：

* 控制台
* 调试
* Windows 事件跟踪
* Windows 事件日志
* TraceSource
* Azure 应用服务
* Azure Application Insights

通过从 DI 获取 `ILogger` 对象并调用日志方法，从应用代码中的任何位置写入日志。

下面是使用 `ILogger` 对象的示例代码，其中突出显示了构造函数注入和日志记录方法调用。

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

通过 `ILogger` 接口，可将任意数量的字段传递给日志提供程序。 字段通常用于构造消息字符串，但提供程序还可将其作为单独的字段发送到数据存储。 此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

有关详细信息，请参阅 <xref:fundamentals/logging/index>。

## <a name="routing"></a>路由

路由是映射到处理程序的 URL 模式  。 处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。 借助 ASP.NET Core 路由，可以控制应用使用的 URL。

有关详细信息，请参阅 <xref:fundamentals/routing>。

## <a name="error-handling"></a>错误处理

ASP.NET Core 具有用于处理错误的内置功能，例如：

* 开发人员异常页
* 自定义错误页
* 静态状态代码页
* 启动异常处理

有关详细信息，请参阅 <xref:fundamentals/error-handling>。

## <a name="make-http-requests"></a>发出 HTTP 请求

`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。 工厂可以：

* 提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。 例如，可以注册 github  客户端，并将它配置为访问 GitHub。 可以注册一个默认客户端用于其他用途。
* 支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。 此模式类似于 ASP.NET Core 中的入站中间件管道。 此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。
* 与 Polly 集成，这是用于瞬时故障处理的常用第三方库  。
* 管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。
* （通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。

有关详细信息，请参阅 <xref:fundamentals/http-requests>。

## <a name="content-root"></a>内容根

内容根是指向以下内容的基本路径：

* 托管应用程序的可执行文件 (.exe  )。
* 构成应用程序的已编译程序集 (.dll  )。
* 应用使用的非代码内容文件，例如：
  * Razor 文件（.cshtml  、.razor  ）
  * 配置文件（.json  、.xml  ）
  * 数据文件 (.db  )
* [Web 根目录](#web-root)，通常是已发布的 wwwroot  文件夹。

在开发过程中：

* 内容根默认为项目的根目录。
* 项目的根目录用于创建：
  * 项目根目录中应用的非代码内容文件的路径。
  * [Web 根目录](#web-root)，通常是项目根目录中的 wwwroot  文件夹。

::: moniker range=">= aspnetcore-3.0"

在[构建主机](#host)时，可以指定备用内容根路径。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host#contentrootpath>。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在[构建主机](#host)时，可以指定备用内容根路径。 有关详细信息，请参阅 <xref:fundamentals/host/web-host#content-root>。

::: moniker-end

## <a name="web-root"></a>Web 根

Web 根目录是公共、非代码、静态资源文件的基本路径，例如：

* 样式表 (.css  )
* JavaScript (.js  )
* 图像（.png  、.jpg  ）

默认情况下，静态文件仅从 Web 根目录（及子目录）提供。

::: moniker range=">= aspnetcore-3.0"

Web 根路径默认为 {content root}/wwwroot  ，但[构建主机](#host)时可以指定其他 Web 根路径。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host#webroot>。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Web 根路径默认为 {content root}/wwwroot  ，但[构建主机](#host)时可以指定其他 Web 根路径。 有关详细信息，请参阅 [Web 根目录](xref:fundamentals/host/web-host#web-root)。

::: moniker-end

在 Razor (.cshtml  ) 文件中，波浪号斜杠 (`~/`) 指向 Web 根目录。 以 `~/` 开头的路径称为虚拟路径  。

有关详细信息，请参阅 <xref:fundamentals/static-files>。
