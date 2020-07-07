---
title: ASP.NET Core 2.1 的新增功能
author: isaac2004
description: 了解 ASP.NET Core 2.1 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: bf041965b009d5c10b96ad4f2a349c6efb346eb5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408157"
---
# <a name="whats-new-in-aspnet-core-21"></a>ASP.NET Core 2.1 的新增功能

本文重点介绍 ASP.NET Core 2.1 中最重要的更改，并提供相关文档的链接。

## SignalR

已针对 ASP.NET Core 2.1 重新编写 SignalR。 ASP.NET Core SignalR 包含大量改进：

* 简化横向扩展模型。
* 新 JavaScript 客户端不具有 jQuery 依赖项。
* 新紧凑型二进制协议基于 MessagePack。
* 支持自定义协议。
* 新的流式处理响应模型。
* 支持基于裸机 WebSocket 的客户端。

有关详细信息，请参阅 [ASP.NET Core SignalR](xref:signalr/introduction)。

## <a name="razor-class-libraries"></a>Razor 类库

通过 ASP.NET Core 2.1 可以更容易地在库中生成和包含基于 Razor 的 UI，并跨多个项目共享 UI。 新 Razor SDK 支持将 Razor 文件生成到可打包为 NuGet 包的类库项目中。 应用可以自动发现和覆盖库中的视图和页面。 通过将 Razor 编译集成到生成中：

* 应用启动时间可显著加快。
* 在迭代开发工作流过程中，仍可在运行时快速更新 Razor 视图和页面。

有关详细信息，请参阅[使用 Razor 类库项目创建可重用 UI](xref:razor-pages/ui-class)。

## <a name="identity-ui-library--scaffolding"></a>Identity UI 库和基架

ASP.NET Core 2.1 提供 [ASP.NET Core Identity](xref:security/authentication/identity) 作为 [Razor 类库](xref:razor-pages/ui-class)。 包含 Identity 的应用可以应用新的 Identity 基架，以便有选择地添加 Identity Razor 类库 (RCL) 中包含的源代码。 建议生成源代码，以便修改代码和更改行为。 例如，可以指示基架生成在注册过程中使用的代码。 生成的代码优先于 Identity RCL 中的相同代码。

不包含身份验证的应用可以应用 Identity 基架来添加 RCL Identity 包。 可以选择要生成的 Identity 代码。

有关详细信息，请参阅 [ASP.NET Core 项目中的基架 Identity](xref:security/authentication/scaffold-identity)。

## <a name="https"></a>HTTPS

随着对安全和隐私的关注度日益增加，为 Web 应用启用 HTTPS 变得非常重要。 Web 上强制使用 HTTPS 的要求日趋严格。 不使用 HTTPS 的站点被视为不安全的站点。 浏览器（Chromium、Mozilla）开始强制要求必须在安全的上下文中使用 Web 功能。 [GDPR](xref:security/gdpr) 要求使用 HTTPS 保护用户隐私。 不仅在生产环境中使用 HTTPS 至关重要，而且在开发环境中使用 HTTPS 还可以帮助防止部署中的各种问题（例如，不安全的链接）。 ASP.NET Core 2.1 包含大量改进，更方便在开发环境使用 HTTPS 和在生产环境配置 HTTPS。 有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl)。

### <a name="on-by-default"></a>默认开启

为了提高网站开发的安全性，现在默认启用 HTTPS。 从 2.1 开始，当本地具有开发证书时，Kestrel 将侦听 `https://localhost:5001`。 关于开发证书的创建：

* 首次使用 SDK 时，在首次运行 .NET Core SDK 时会创建开发证书。
* 手动使用新 `dev-certs` 工具。

运行 `dotnet dev-certs https --trust` 以信任证书。

### <a name="https-redirection-and-enforcement"></a>HTTPS 重定向和强制使用

Web 应用通常需要侦听 HTTP 和 HTTPS，但随后会将所有 HTTP 流量重定向到 HTTPS。 在 2.1 中，引入了专用的 HTTPS 重定向中间件，可根据配置或绑定服务器端口的存在智能执行重定向。

使用 [HTTP 严格传输安全协议 (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) 可进一步强制使用 HTTPS。 HSTS 指示浏览器始终通过 HTTPS 访问站点。 ASP.NET Core 2.1 添加 HSTS 中间件，该中间件支持选择最大年限、子域和 HSTS 预加载列表。

### <a name="configuration-for-production"></a>适用于生产环境的配置

在生产环境中，必须显式配置 HTTPS。 在 2.1 中，添加了针对 Kestrel 配置 HTTPS 的默认配置架构。 可以将应用配置为使用：

* 多个终结点（包括 URL）。 有关详细信息，请参阅 [Kestrel Web 服务器实现：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。
* 来自于磁盘上的文件或证书存储中的用于 HTTPS 的证书。

## <a name="gdpr"></a>GDPR

ASP.NET Core 提供 API 和模板，帮助满足[欧盟一般数据保护条例 (GDPR)](https://www.eugdpr.org/) 的部分要求。 有关详细信息，请参阅 [ASP.NET Core 中的 GDPR 支持](xref:security/gdpr)。 [示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)演示如何使用并允许测试已添加到 ASP.NET Core 2.1 模板中的大多数 GDPR 扩展点和 API。

## <a name="integration-tests"></a>集成测试

引入了可简化创建和执行测试的新包。 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) 包可处理以下任务：

* 将依赖项文件 (\*.deps) 从已测试的应用复制到测试项目的 bin 文件夹中 。
* 将内容根目录设置为已测试应用的项目根目录，以便可在执行测试时找到静态文件和页面/视图。
* 提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 类，以简化已测试应用在 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 中的启动过程。

以下测试使用 [xUnit](https://xunit.github.io/) 检查索引页是否加载了成功状态代码和正确的 Content-Type 标头：

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

有关详细信息，请参阅[集成测试](xref:test/integration-tests)主题。

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2.1 添加了新编程约定，方便构建简洁的说明性 Web API。 `ActionResult<T>` 是一个新增类型，可允许应用返回响应类型，或返回任何其他操作结果（与 IActionResult 相似），同时仍然指示响应类型。 此外还添加了 `[ApiController]` 特性，作为选择加入特定于 Web API 的约定和行为的方式。

有关详细信息，请参阅[使用 ASP.NET Core 构建 Web API](xref:web-api/index)。

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 引入了新的 `IHttpClientFactory` 服务，方便在应用中配置和使用 `HttpClient` 的实例。 `HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。 工厂可以：

* 使根据已命名客户端注册 `HttpClient` 的实例变得更直观。
* 实现一个 Polly 处理程序，允许将 Polly 策略用于 Retry、CircuitBreakers 等。

有关详细信息，请参阅[启动 HTTP 请求](xref:fundamentals/http-requests)。

## <a name="kestrel-transport-configuration"></a>Kestrel 传输配置

对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。 有关详细信息，请参阅 [Kestrel Web 服务器实现：传输配置](xref:fundamentals/servers/kestrel#transport-configuration)。

## <a name="generic-host-builder"></a>通用主机生成器

引入了通用主机生成器 (`HostBuilder`)。 此生成器可用于不处理 HTTP 请求（消息传送、后台任务等等）的应用。

有关详细信息，请参阅 [.NET 通用主机](xref:fundamentals/host/generic-host)。

## <a name="updated-spa-templates"></a>更新的 SPA 模板

更新了适用于 Angular、React 和 React 结合 Redux 的单页应用程序模板，以使用标准项目结构和为每个框架构建系统。

Angular 模板基于 Angular CLI，而 React 模板基于 create-react-app。

有关详情，请参阅：

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Razor Pages 搜索 Razor 资产

在 2.1 中，Razor Pages 按所列顺序搜索以下目录中的 Razor 资产（例如布局和分区）：

1. 当前 Pages 文件夹。
1. /Pages/Shared/
1. /Views/Shared/

## <a name="razor-pages-in-an-area"></a>某个区域内的 Razor Pages

Razor Pages 现在支持[区域](xref:mvc/controllers/areas)。 要查看区域示例，请使用个人用户帐户创建新 Razor Pages Web 应用。 使用个人用户帐户的 Razor Pages Web 应用包括 /Areas/Identity/Pages。

## <a name="mvc-compatibility-version"></a>MVC 兼容性版本

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法允许应用选择加入或退出 ASP.NET Core MVC 2.1 或更高版本中引入的潜在中断行为变更。

有关详细信息，请参阅 <xref:mvc/compatibility-version>。

## <a name="migrate-from-20-to-21"></a>从 2.0 迁移到 2.1

请参阅[从 ASP.NET Core 2.0 迁移到 2.1](xref:migration/20_21)。

## <a name="additional-information"></a>其他信息

要获取完整的更改列表，请参阅 [ASP.NET Core 2.1 发行说明](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0)。
