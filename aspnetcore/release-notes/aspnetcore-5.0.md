---
title: ASP.NET Core 5.0 的新增功能
author: rick-anderson
description: 了解 ASP.NET Core 5.0 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: 84747e2d13275a23e83dc2dc0f666cb0c8d001b1
ms.sourcegitcommit: 827e8be18cebbcc09b467c089e17fa6f5e430cb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94634621"
---
# <a name="whats-new-in-aspnet-core-50"></a>ASP.NET Core 5.0 的新增功能

本文重点介绍 ASP.NET Core 5.0 中最重要的更改，并提供相关文档的链接。

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC 和 Razor 改进

### <a name="model-binding-datetime-as-utc"></a>通过模型绑定将日期/时间绑定到 UTC

模型绑定现在支持将 UTC 时间字符串绑定到 `DateTime`。 如果请求包含 UTC 时间字符串，则模型绑定会将其绑定到 UTC `DateTime`。 例如，以下时间字符串会绑定到 UTC `DateTime`：`https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>模型绑定和验证与 C# 9 记录类型一起使用

[C# 9 记录类型](/dotnet/csharp/whats-new/csharp-9#record-types)可以与 MVC 控制器或 Razor 页面中的模型绑定一起使用。 记录类型是为通过网络传输的数据建模的好方法。

例如，以下 `PersonController` 将 `Person` 记录类型与模型绑定和窗体验证一起使用：

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

`Person/Index.cshtml` 文件：

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>对 DynamicRouteValueTransformer 的改进

ASP.NET Core 3.1 引入了 <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer>，作为使用自定义终结点动态选择 MVC 控制器操作或 Razor 页面的方法。 ASP.NET Core 5.0 应用可以将状态传递到 `DynamicRouteValueTransformer` 并筛选选定的终结点集。

### <a name="miscellaneous"></a>杂项

* [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 特性可应用于 Razor 页面模型上的属性。
* 默认情况下，从正文中绑定的参数和属性被视为必需。 <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Web API

### <a name="openapi-specification-on-by-default"></a>OpenAPI 规范默认开启

[OpenAPI 规范](http://spec.openapis.org/oas/v3.0.3)是一种行业标准，用于描述 HTTP API 并将其集成到复杂的业务流程中或与第三方集成。 OpenAPI 受到所有云提供程序和许多 API 注册表的广泛支持。 从 Web API 发出 OpenAPI 文档的应用具有各种可使用这些 API 的新机会。 与开放源代码项目 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) 的维护人员合作，ASP.NET Core API 模板包含对 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 的 NuGet 依赖关系。 Swashbuckle 是一个常用的开放源代码 NuGet 包，可动态发出 OpenAPI 文档。 Swashbuckle 通过 API 控制器进行自检并在运行时或在生成时使用 Swashbuckle CLI 生成 OpenAPI 文档来实现此目的。

在 ASP.NET Core 5.0 中，Web API 模板默认启用 OpenAPI 支持。 若要禁用 OpenAPI，请执行以下操作：

* 通过命令行：

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* 通过 Visual Studio：取消选中“启用 OpenAPI 支持”。

为 Web API 项目创建的所有 .csproj 文件都包含 [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet 包引用。

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

模板生成的代码在 `Startup.ConfigureServices` 中包含用于激活 OpenAPI 文档生成的代码：

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

`Startup.Configure` 方法将添加 Swashbuckle 中间件，这将启用：

* 文档生成过程。
* 在开发模式下默认为 Swagger UI 页。

在发布到生产环境时，模板生成的代码不会意外公开 API 的说明。

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Azure API 管理导入

ASP.NET Core API 项目启用 OpenAPI 时，Visual Studio 2019 版本 16.8 及更高版本将在发布流程中自动提供额外的步骤。 使用 [Azure API 管理](xref:tutorials/publish-to-azure-api-management-using-vs)的开发人员有机会在发布流程中自动将 API 导入到 Azure API 管理：

![Azure API 管理导入与发布](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>更佳的 Web API 项目启动体验

如果默认启用了 OpenAPI，则显著改进了 Web API 开发人员的应用启动体验 (F5)。 借助 ASP.NET Core 5.0，Web API 模板会预先配置为加载 Swagger UI 页。 Swagger UI 页提供为已发布的 API 添加的文档，并且单击一次即可测试 API。

![swagger/index.html 视图](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>性能改进

对于 .NET 5，通过特别着重于复杂的 UI 呈现和 JSON 序列化显著改进了 Blazor WebAssembly 运行时性能。 在我们的性能测试中，.NET 5 中 Blazor WebAssembly 的速度要比大多数情况快两到三倍。 有关详细信息，请参阅 [ASP.NET 博客：.NET 5 候选发布版本 1 中的 ASP.NET Core 更新](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements)。

### <a name="css-isolation"></a>CSS 隔离

Blazor 现在支持定义限定为给定组件的 CSS 样式。 使用组件特定的 CSS 样式，可以更轻松地了解应用中的样式，并避免全局样式的意外副作用。 有关详细信息，请参阅 <xref:blazor/components/css-isolation>。

### <a name="new-inputfile-component"></a>新的 `InputFile` 组件

`InputFile` 组件允许读取用户选择要上传的一个或多个文件。 有关详细信息，请参阅 <xref:blazor/file-uploads>。

### <a name="new-inputradio-and-inputradiogroup-components"></a>新的 `InputRadio` 和 `InputRadioGroup` 组件

Blazor 具有内置的 `InputRadio` 和 `InputRadioGroup` 组件，这些组件可简化通过集成验证将数据绑定到单选按钮组。 有关详细信息，请参阅 <xref:blazor/forms-validation>。

### <a name="component-virtualization"></a>组件虚拟化

使用 Blazor 框架的内置虚拟化支持提高组件呈现的感知性能。 有关详细信息，请参阅 <xref:blazor/forms-validation#radio-buttons>。

### <a name="ontoggle-event-support"></a>`ontoggle` 事件支持

Blazor 事件现在支持 `ontoggle` DOM 事件。 有关详细信息，请参阅 <xref:blazor/components/event-handling#event-argument-types>。

### <a name="set-ui-focus-in-no-locblazor-apps"></a>将 UI 焦点设置在 Blazor 应用中

对元素引用使用 `FocusAsync` 便捷方法，以便将 UI 焦点设置到该元素。 有关详细信息，请参阅 <xref:blazor/components/event-handling#focus-an-element>。

### <a name="custom-validation-class-attributes"></a>自定义验证类属性

与 CSS 框架集成时，自定义验证类名称非常有用，例如启动。 有关详细信息，请参阅 <xref:blazor/forms-validation#custom-validation-class-attributes>。

### <a name="iasyncdisposable-support"></a>IAsyncDisposable 支持

Blazor 组件现在支持已分配资源的异步版本的 <xref:System.IAsyncDisposable> 接口。

### <a name="javascript-isolation-and-object-references"></a> JavaScript 隔离和对象引用

Blazor 在标准 [JavaScript 模块](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中启用 JavaScript 隔离。 有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。

### <a name="form-components-support-display-name"></a>窗体组件支持显示名称

以下内置组件支持带有 `DisplayName` 参数的显示名称：

* `InputDate`
* `InputNumber`
* `InputSelect`

有关详细信息，请参阅 <xref:blazor/forms-validation#display-name-support>。

### <a name="catch-all-route-parameters"></a>catch-all 路由参数

组件支持可跨多个文件夹边界捕获路径的 catch-all 路由参数。 有关详细信息，请参阅 <xref:blazor/fundamentals/routing#catch-all-route-parameters>。

### <a name="debugging-improvements"></a>调试改进

调试 Blazor WebAssembly 应用在 ASP.NET Core 5.0 中得到了改进。 此外，Visual Studio for Mac 上现在也支持调试。 有关详细信息，请参阅 <xref:blazor/debug>。

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v2.0 和 MSAL v2.0

Blazor 安全性现在使用 Microsoft Identity v2.0（[`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) 和 [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)）和 MSAL v2.0。 有关详细信息，请参阅[Blazor 安全性和 Identity 节点](xref:blazor/security/index)中的主题。

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Blazor Server 受保护的浏览器存储

Blazor Server 应用现在可以使用内置支持在浏览器中存储应用状态，这已受到保护，无法使用 ASP.NET Core 数据保护进行篡改。 数据可以存储在本地浏览器存储或会话存储中。 有关详细信息，请参阅 <xref:blazor/state-management>。

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly 预呈现

跨托管模型改进了组件集成，Blazor WebAssembly 应用现在可以在服务器上预呈现输出。 <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>剪裁/链接改进

Blazor WebAssembly 在生成期间执行中间语言 (IL) 剪裁/链接，以从应用的输出程序集中剪裁不必要的 IL。 随着 ASP.NET Core 5.0 的发布，Blazor WebAssembly 通过其他配置选项来执行改进的剪裁。 有关详细信息，请参阅 <xref:blazor/host-and-deploy/configure-trimmer> 和[剪裁选项](/dotnet/core/deploying/trimming-options)。

### <a name="browser-compatibility-analyzer"></a>浏览器兼容性分析器

Blazor WebAssembly 应用面向整个 .NET API 外围应用，但由于浏览器沙盒约束，并非所有 .NET API 在 WebAssembly 上都受支持。 在 WebAssembly 上运行时，不支持的 API 将引发 <xref:System.PlatformNotSupportedException>。 当应用使用应用目标平台不支持的 API 时，平台兼容性分析器会向开发人员发出警告。 有关详细信息，请参阅 <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>。

### <a name="lazy-load-assemblies"></a>延迟加载程序集

通过推迟某些应用程序程序集的加载，直到需要时才加载，来提高 Blazor WebAssembly 应用启动性能。 有关详细信息，请参阅 <xref:blazor/webassembly-lazy-load-assemblies>。

### <a name="updated-globalization-support"></a>已更新的全球化支持

全球化支持适用于基于 International Components for Unicode (ICU) 的 Blazor WebAssembly。 有关详细信息，请参阅 <xref:blazor/globalization-localization>。

## <a name="grpc"></a>gRPC

在 [gRPC](https://grpc.io/) 中进行了许多性能改进。 有关详细信息，请参阅 [.NET 5 中的 gRPC 性能改进](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/)。

有关 gRPC 的详细信息，请参阅 <xref:grpc/index>。

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR 中心筛选器

SignalR 中心筛选器（在 ASP.NET SignalR 中称为“中心管道”）是一项功能，它允许代码在调用中心方法之前和之后运行。 在调用中心方法之前和之后运行代码类似于中间件在 HTTP 请求之前和之后运行代码。 常见用途包括日志记录、错误处理和参数验证。

有关详细信息，请参阅[在 ASP.NET Core SignalR 中使用中心筛选器](xref:signalr/hub-filters)。

### <a name="no-locsignalr-parallel-hub-invocations"></a>SignalR 并行中心调用

ASP.NET Core SignalR 现在能够处理并行中心调用。 可以更改默认行为，以允许客户端一次调用多个中心方法：

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>在 SignalR Java 客户端中添加了 Messagepack 支持

新包 ([com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack)) 将 MessagePack 支持添加到 SignalR Java 客户端。 若要使用 MessagePack 中心协议，请将 `.withHubProtocol(new MessagePackHubProtocol())` 添加到连接生成器：

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* 通过配置可重载的终结点：Kestrel 可以检测对传递到 [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) 的配置所做的更改，从现有终结点取消绑定并绑定到新终结点，而无需在 `reloadOnChange` 参数 `true` 时重新启动应用程序。 默认情况下，在使用 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> 或 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 时，Kestrel 将绑定到启用了 `reloadOnChange` 的[“Kestrel”](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226)配置子节。 手动调用 `KestrelServerOptions.Configure` 以获取可重载终结点时，应用必须传递 `reloadOnChange: true`。
* HTTP/2 响应标头改进。 有关详细信息，请参阅下一部分中的[性能改进](#performance-improvements)。
* 支持套接字传输中的其他终结点类型：添加到 <xref:System.Net.Sockets?displayProperty=nameWithType> 中引入的新 API，[Kestrel](xref:fundamentals/servers/kestrel) 中的套接字默认传输允许绑定到现有文件句柄和 Unix 域套接字。 如果支持绑定到现有文件句柄，则无需 `libuv` 传输即可使用现有 `Systemd` 集成。
* Kestrel 中的自定义标头解码：应用可以指定使用哪个 <xref:System.Text.Encoding> 来基于标头名称解释传入标头，而不是默认为 UTF-8。 在包含身份验证方案、帐户名和身份验证签名的字符串中设置 <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` 属性用于指定要使用的编码：
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>通过配置的 Kestrel 终结点特定选项

添加了通过[配置](xref:fundamentals/configuration/index)来配置 Kestrel 的终结点特定选项的支持。 终结点特定的配置包括：

* 已使用 HTTP 协议
* 已使用 TLS 协议
* 已选择证书
* 客户端证书模式

配置允许指定基于指定服务器名称选择的证书。 服务器名称是客户端所指示的 TLS 协议的服务器名称指示 (SNI) 扩展的一部分。 Kestrel 的配置还支持在主机名中使用通配符前缀。

下面的示例演示如何使用配置文件指定终结点特定的配置：

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

服务器名称指示 (SNI) 是一种 TLS 扩展，可将虚拟域作为 SSL 协商的一部分包括在内。 这实际上表示虚拟域名或主机名可用于标识网络终结点。

## <a name="performance-improvements"></a>性能改进

### <a name="http2"></a>HTTP/2

* 显著减少了 HTTP/2 代码路径中的分配。
* 支持 [Kestrel](xref:fundamentals/servers/kestrel) 中的 HTTP/2 响应标头的 [HPack 动态压缩](https://tools.ietf.org/html/rfc7541)。 有关详细信息，请参阅[标头表大小](xref:fundamentals/servers/kestrel#header-table-size)和 [HPACK：HTTP/2 的静默杀手锏](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/)。
* 发送 HTTP/2 PING 帧：HTTP/2 有一种机制，用于发送 PING 帧以确保空闲连接仍然正常工作。 当使用经常空闲但仅可间歇查看活动的长生存期流（例如，gRPC 流）时，确保可行连接特别有用。 应用可以通过对 <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> 设置限制，在 [Kestrel](xref:fundamentals/servers/kestrel) 中发送定期 PING 帧：

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>容器

在 .NET 5.0 之前，生成并发布用于 ASP.NET Core 应用的 Dockerfile 需要提取整个 .NET Core SDK 和 ASP.NET Core 映像。 在此版本中，将减少提取 SDK 图像字节数，并大大消除为 ASP.NET Core 映像提取的字节数。 有关详细信息，请参阅[此 GitHub 问题注释](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)。

## <a name="authentication-and-authorization"></a>身份验证和授权

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>使用 Microsoft.Identity.Web 的 Azure Active Directory 身份验证

ASP.NET Core 项目模板现在与 <xref:Microsoft.Identity.Web?displayProperty=fullName> 集成，以处理使用 [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) 的身份验证。 [Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) 提供：

* 通过 Azure AD 进行身份验证的更好体验。
* 代表用户访问 Azure 资源的一种更简单方法，包括 [Microsoft Graph](/graph/overview)。 请参阅 [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)，它从基本登录开始，通过多租户（使用 Azure API、使用 Microsoft Graph 并保护你自己的 API）前进。 `Microsoft.Identity.Web` 与 .NET 5 一起提供。

### <a name="allow-anonymous-access-to-an-endpoint"></a>允许匿名访问终结点

`AllowAnonymous` 扩展方法允许匿名访问终结点：

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>自定义处理授权失败

现在，使用由[授权](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A)[中间件](xref:fundamentals/middleware/index)调用的新 [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) 接口可以更轻松地自定义处理授权失败。 默认实现保持不变，但可以在“依赖关系注入”中注册自定义处理程序，这允许基于授权失败的原因发出自定义 HTTP 响应。 请参阅用于演示 `IAuthorizationMiddlewareResultHandler` 的使用情况的[此示例](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs)。

### <a name="authorization-when-using-endpoint-routing"></a>使用终结点路由时的授权

使用终结点路由时的授权现在会接收 `HttpContext` 而不是终结点实例。 这允许授权中间件访问 `RouteData` 以及无法通过 <xref:Microsoft.AspNetCore.Http.Endpoint> 类访问的 `HttpContext` 的其他属性。 可以使用 [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A) 从上下文中提取终结点。

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>使用 Linux 上的 Kerberos 身份验证和 LDAP 的基于角色的访问控制

请参阅 [Kerberos 身份验证和基于角色的访问控制 (RBAC)](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>API 改进

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>用于 HttpRequest 和 HttpResponse 的 JSON 扩展方法

可以使用新的 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 和 `WriteAsJsonAsync` 扩展方法从 `HttpRequest` 和 `HttpResponse` 读取和写入 JSON 数据。 这些扩展方法使用 [System.Text.Json](xref:System.Text.Json) 序列化程序来处理 JSON 数据。 新的 `HasJsonContentType` 扩展方法还可以检查请求是否具有 JSON 内容类型。

JSON 扩展方法可与[终结点路由](xref:fundamentals/routing)结合使用，以编程方式创建 JSON API，我们称之为“路由到代码”*_。 这是一个新选项，适用于想要以轻量级方式创建基本 JSON API 的开发人员。 例如，只有少量终结点的 Web 应用可能选择使用路由到代码，而不是 ASP.NET Core MVC 的全部功能：

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

有关新 JSON 扩展方法以及路由到代码的详细信息，请参阅[此 .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code)。

### <a name="systemdiagnosticsactivity"></a>System.Diagnostics.Activity

<xref:System.Diagnostics.Activity?displayProperty=fullName> 的默认格式现在默认为 W3C 格式。 默认情况下，这使得 ASP.NET Core 中的分布式跟踪支持可与更多框架进行互操作。

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> 现在支持配置一个允许将这些参数或属性视为可选的选项：

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>其他改进

我们已开始将[可以为 null 的注释](/dotnet/csharp/nullable-references#attributes-describe-apis)应用到 ASP.NET Core 程序集。 我们计划为 .NET 5 Framework 的大多数常见公共 API 图面添加批注。 <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>控制 Startup 类激活

添加了额外的 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> 重载，使应用能够提供工厂方法来控制 `Startup` 类激活。 控制 `Startup` 类激活有助于将附加参数传递给与主机一起初始化的 `Startup`：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>使用 dotnet watch 自动刷新

在 .NET 5 中，对 ASP.NET Core 项目运行 [dotnet watch](xref:tutorials/dotnet-watch) 将启动默认浏览器，并在对代码进行更改时自动刷新浏览器。 这意味着可以执行下列操作：

_ 在文本编辑器中打开 ASP.NET Core 项目。
* 运行 `dotnet watch`。
* 专注于代码更改，而工具处理应用的重新生成、重新启动和重新加载。

### <a name="console-logger-formatter"></a>控制台记录器格式化程序

已对 `Microsoft.Extensions.Logging` 库中的控制台日志提供程序进行了改进。 开发人员现在可以实现自定义 `ConsoleFormatter`，以对控制台输出的格式设置和着色进行完全控制。 格式化程序 API 通过实现 VT-100 转义序列的子集进行丰富的格式设置。 VT-100 受大多数新式终端支持。 控制台记录器可以对不受支持的终端分析转义序列，使开发人员可以为所有终端创作单个格式化程序。

### <a name="json-console-logger"></a>JSON 控制台记录器

除了对自定义格式化程序的支持外，我们还添加了一个内置 JSON 格式化程序，用于向控制台发出结构化 JSON 日志。 下面的代码演示如何从默认记录器切换到 JSON：

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

发出到控制台的日志消息为 JSON 格式：

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
