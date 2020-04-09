---
title: 安全ASP.NET核心Blazor服务器应用
author: guardrex
description: 了解如何缓解对服务器应用的Blazor安全威胁。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626022"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>安全ASP.NET核心布拉佐服务器应用程序

哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)

Blazor Server 应用采用*有状态*的数据处理模型，其中服务器和客户端保持长期关系。 持久状态由电路保持，该[电路](xref:blazor/state-management)可以跨越可能寿命很长的连接。

当用户访问 Blazor Server 站点时，服务器会在服务器的内存中创建一个电路。 该电路向浏览器指示要呈现哪些内容并响应事件，例如当用户在 UI 中选择按钮时。 要执行这些操作，电路在用户的浏览器和服务器上的 .NET 方法中调用 JavaScript 函数。 这种基于JavaScript的双向交互称为[JavaScript互操作（JS互操作）。](xref:blazor/call-javascript-from-dotnet)

由于 JS 互通通过 Internet 进行，并且客户端使用远程浏览器，因此 Blazor Server 应用共享大多数 Web 应用安全问题。 本主题介绍对 Blazor Server 应用的常见威胁，并提供侧重于面向 Internet 的应用的威胁缓解指南。

在受限环境中（如公司网络或 Intranet 内部）中，一些缓解指南要么：

* 不适用于受约束的环境。
* 不值得花费成本来实现，因为安全风险在受限的环境中很低。

## <a name="blazor-server-project-template"></a>布拉佐服务器项目模板

布拉佐服务器项目模板可以配置为在创建项目时进行身份验证。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

按照 <xref:blazor/get-started> 一文中的 Visual Studio 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。

在“创建新的 ASP.NET Core Web 应用程序”**** 对话框中选择“Blazor 服务器应用”**** 模板后，在“身份验证”**** 下选择“更改”****。

此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：

* **无身份验证**
* **个人用户帐户** &ndash; 可以通过以下方式存储用户帐户：
  * 使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统在应用程序中存储。
  * 使用[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。
* **工作或学校帐户**
* **Windows 身份验证**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按照 <xref:blazor/get-started> 一文中的 Visual Studio Code 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。

| 身份验证机制                                                                 | `{AUTHENTICATION}` 值 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| 无身份验证                                                                        | `None`                   |
| 个人<br>使用 ASP.NET Core Identity 将用户存储在应用程序中。                        | `Individual`             |
| 个人<br>用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中。 | `IndividualB2C`          |
| 工作或学校帐户<br>对一个租户进行组织身份验证。            | `SingleOrg`              |
| 工作或学校帐户<br>对多个租户进行组织身份验证。           | `MultiOrg`               |
| Windows 身份验证                                                                   | `Windows`                |

该命令创建一个文件夹，它将 `{APP NAME}` 占位符提供的值作为名称，并使用文件夹名称作为应用程序的名称。 有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 请按照"视觉工作室"了解本文中的<xref:blazor/get-started>Mac 指南。

1. 在 **"配置新的 Blazor 服务器应用**"步骤中，从 **"身份验证**"下拉列表中选择 **"个人身份验证（应用内"）。**

1. 该应用程序是为存储在应用中的具有ASP.NET核心标识的单个用户创建的。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

按照<xref:blazor/get-started>本文中的 .NET Core CLI 指南创建具有身份验证机制的新 Blazor Server 项目：

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。

| 身份验证机制                                                                 | `{AUTHENTICATION}` 值 |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| 无身份验证                                                                        | `None`                   |
| 个人<br>使用 ASP.NET Core Identity 将用户存储在应用程序中。                        | `Individual`             |
| 个人<br>用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中。 | `IndividualB2C`          |
| 工作或学校帐户<br>对一个租户进行组织身份验证。            | `SingleOrg`              |
| 工作或学校帐户<br>对多个租户进行组织身份验证。           | `MultiOrg`               |
| Windows 身份验证                                                                   | `Windows`                |

该命令创建一个文件夹，它将 `{APP NAME}` 占位符提供的值作为名称，并使用文件夹名称作为应用程序的名称。 有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>将令牌传递到 Blazor 服务器应用

使用常规剃刀页面或 MVC 应用验证 Blazor Server 应用。 预配令牌并将其保存到身份验证 Cookie。 例如：

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

有关示例代码（包括完整`Startup.ConfigureServices`示例），请参阅[将令牌传递到服务器端 Blazor 应用程序](https://github.com/javiercn/blazor-server-aad-sample)。

定义要在初始应用状态中传递的类，并带有访问和刷新令牌：

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

定义可在 Blazor 应用中使用**的范围范围**令牌提供程序服务来解决 DI 中的令牌：

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

在`Startup.ConfigureServices`中，添加服务：

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

在 *_Host.cshtml*文件中，创建 和`InitialApplicationState`实例并将其作为参数传递给应用：

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

在`App`组件 *（App.razor）* 中，解析服务，并使用参数中的数据初始化该服务：

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

在发出安全 API 请求的服务中，注入令牌提供程序并检索令牌以调用 API：

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>资源耗尽

当客户端与服务器交互并导致服务器消耗过多资源时，可能会出现资源耗尽。 过多的资源消耗主要影响：

* CPU[](#cpu)
* [内存](#memory)
* [客户端连接](#client-connections)

拒绝服务 （DoS） 攻击通常会消耗应用或服务器的资源。 但是，资源耗尽不一定是系统攻击的结果。 例如，由于用户需求高，有限资源可能会耗尽。 DoS 在[拒绝服务 （DoS） 攻击](#denial-of-service-dos-attacks)部分中进一步介绍。

Blazor 框架外部的资源（如数据库和文件句柄（用于读取和写入文件）也可能经历资源耗尽。 有关详细信息，请参阅 <xref:performance/performance-best-practices>。

### <a name="cpu"></a>CPU

当一个或多个客户端强制服务器执行密集的 CPU 工作时，可能会出现 CPU 耗尽。

例如，考虑一个布拉佐服务器应用程序，它计算*一个斐波纳奇数字*。 菲博纳奇数字由菲博纳奇序列生成，其中序列中的每个数字都是前两个数字的总和。 到达答案所需的工作量取决于序列的长度和初始值的大小。 如果应用未对客户端的请求进行限制，则 CPU 密集型计算可能会支配 CPU 的时间，并降低其他任务的性能。 过多的资源消耗是影响可用性的安全问题。

CPU 耗尽是所有面向公众的应用的问题。 在常规 Web 应用中，请求和连接超时作为一种保护措施，但 Blazor Server 应用不提供相同的安全措施。 Blazor Server 应用在执行潜在的 CPU 密集型工作之前必须包括适当的检查和限制。

### <a name="memory"></a>内存

当一个或多个客户端强制服务器消耗大量内存时，可能会出现内存耗尽。

例如，请考虑 Blazor 服务器端应用，该应用具有接受并显示项目列表的组件。 如果 Blazor 应用没有限制允许的项目数或呈现回客户端的项目数，则内存密集型处理和呈现可能会支配服务器的内存，使其达到服务器性能受损程度。 服务器可能会崩溃或慢速到它似乎已崩溃的点。

请考虑以下方案，用于维护和显示与服务器上的潜在内存耗尽方案相关的项目列表：

* 属性或字段中的项目`List<MyItem>`使用服务器的内存。 如果应用允许项目列表无限制增长，则存在服务器内存不足的风险。 内存不足会导致当前会话结束（崩溃），并且该服务器实例中的所有并发会话都会收到内存不足异常。 为了防止发生此情况，应用必须使用对并发用户施加项限制的数据结构。
* 如果分页方案不用于呈现，则服务器对 UI 中不可见的对象使用其他内存。 如果项目数量没有限制，内存需求可能会耗尽可用的服务器内存。 要防止这种情况，请使用以下方法之一：
  * 渲染时使用分页列表。
  * 仅显示前 100 到 1，000 个项目，并要求用户输入搜索条件以查找显示的项目以外的项目。
  * 对于更高级的呈现方案，实现支持*虚拟化*的列表或网格。 使用虚拟化，列表仅呈现当前对用户可见的项子集。 当用户与 UI 中的滚动条交互时，组件仅呈现显示所需的项。 显示当前不需要的项目可以保存在辅助存储中，这是理想的方法。 未显示的项目也可以记在内存中，这不太理想。

Blazor Server 应用为有状态应用（如 WPF、Windows 窗体或 Blazor WebAssembly）提供与其他 UI 框架类似的编程模型。 主要区别是，在几个 UI 框架中，应用使用的内存属于客户端，并且只影响该单个客户端。 例如，Blazor WebAssembly 应用完全在客户端上运行，并且仅使用客户端内存资源。 在 Blazor Server 方案中，应用使用的内存属于服务器，并在服务器实例上的客户端之间共享。

服务器端内存需求是所有 Blazor Server 应用的考虑因素。 但是，大多数 Web 应用都是无状态的，在返回响应时，在处理请求时使用的内存将被释放。 作为一般建议，不允许客户端分配未绑定的内存量，就像保留客户端连接的任何其他服务器端应用一样。 Blazor Server 应用使用的内存保留的时间比单个请求长。

> [!NOTE]
> 在开发过程中，可以使用探查器或捕获跟踪来评估客户端的内存需求。 探查器或跟踪不会捕获分配给特定客户端的内存。 要捕获开发过程中特定客户端的内存使用情况，捕获转储并检查根植于用户电路中的所有对象的内存需求。

### <a name="client-connections"></a>客户端连接

当一个或多个客户端打开与服务器的并发连接过多，从而阻止其他客户端建立新连接时，可能会导致连接耗尽。

Blazor 客户端为每个会话建立单个连接，并在浏览器窗口打开时保持连接打开状态。 对服务器维护所有连接的要求并不特定于 Blazor 应用。 鉴于连接的持久性和 Blazor Server 应用的状态性，连接耗尽对应用的可用性风险更大。

默认情况下，Blazor Server 应用的每位用户的连接数没有限制。 如果应用需要连接限制，请采用以下一种或多种方法：

* 需要身份验证，这自然会限制未经授权的用户连接到应用的能力。 要使此方案有效，必须防止用户以任何意愿预配新用户。
* 限制每个用户的连接数。 限制连接可以通过以下方法完成。 练习谨慎以允许合法用户访问应用（例如，当基于客户端的 IP 地址建立连接限制时）。
  * 在应用级别：
    * 端点路由可扩展性。
    * 需要身份验证才能连接到应用并跟踪每个用户的活动会话。
    * 达到限制时拒绝新会话。
    * 使用代理（如[Azure SignalR 服务](/azure/azure-signalr/signalr-overview)）与应用的连接，该服务将连接从客户端到应用进行多路复用。 这为具有比单个客户端可以建立的连接容量更大的应用提供了，从而防止客户端耗尽与服务器的连接。
  * 在服务器级别：在应用前面使用代理/网关。 例如[，Azure 前门](/azure/frontdoor/front-door-overview)使您能够定义、管理和监视 Web 流量到应用的全局路由。

## <a name="denial-of-service-dos-attacks"></a>拒绝服务 （DoS） 攻击

拒绝服务 （DoS） 攻击涉及客户端，导致服务器耗尽其一个或多个资源，使应用不可用。 Blazor Server 应用包含一些默认限制，并依赖于其他ASP.NET核心和信号R限制来抵御 DoS 攻击：

| 布拉佐服务器应用程序限制                            | 说明 | 默认 |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | 给定服务器一次在内存中保存的最大断开连接电路数。 | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | 断开电路在断开之前在内存中保持最大时间。 | 3 分钟 |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | 服务器在超时异步 JavaScript 函数调用之前等待的时间最长。 | 1 分钟 |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | 服务器在给定时间每个电路的内存中保留的最大未确认呈现批处理数，以支持可靠的重新连接。 达到限制后，服务器将停止生成新的呈现批处理，直到客户端确认一个或多个批处理。 | 10 |


| 信号R 和 ASP.NET核心限制             | 说明 | 默认 |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | 单个邮件的消息大小。 | 32 KB |

## <a name="interactions-with-the-browser-client"></a>与浏览器（客户端）的交互

客户端通过 JS 互通事件调度和呈现完成与服务器交互。 JS 互通通信在 JavaScript 和 .NET 之间双向进行：

* 浏览器事件以异步方式从客户端发送到服务器。
* 服务器根据需要异步重新呈现 UI。

### <a name="javascript-functions-invoked-from-net"></a>从 .NET 调用的 JavaScript 函数

对于从 .NET 方法到 JavaScript 的调用：

* 所有调用都有可配置的超时，之后它们将返回<xref:System.OperationCanceledException>给调用方。
  * 呼叫的默认超时 （`CircuitOptions.JSInteropDefaultCallTimeout`） 为一分钟。 要配置此限制，请参阅<xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>。
  * 可以提供取消令牌以按每次呼叫控制取消。 如果提供了取消令牌，则尽可能依赖默认调用超时，并规定对客户端的任何调用。
* 不能信任 JavaScript 调用的结果。 在Blazor浏览器中运行的应用客户端搜索要调用的 JavaScript 函数。 调用该函数，并生成结果或错误。 恶意客户端可以尝试：
  * 通过从 JavaScript 函数返回错误，在应用中导致问题。
  * 通过从 JavaScript 函数返回意外结果，在服务器上引发意外行为。

采取以下预防措施，防止上述情况：

* 在[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中包装 JS 互操作调用，以考虑调用期间可能发生的错误。 有关详细信息，请参阅 <xref:blazor/handle-errors#javascript-interop>。
* 在采取任何操作之前，验证从 JS 互操作调用返回的数据，包括错误消息。

### <a name="net-methods-invoked-from-the-browser"></a>从浏览器调用 .NET 方法

不要信任从 JavaScript 到 .NET 方法的调用。 当 .NET 方法向 JavaScript 公开时，请考虑如何调用 .NET 方法：

* 对待任何向 JavaScript 公开的任何 .NET 方法，就像将应用的公共终结点一样。
  * 验证输入。
    * 确保值在预期范围内。
    * 确保用户具有执行请求的操作的权限。
  * 不要将过多的资源分配为 .NET 方法调用的一部分。 例如，执行检查并限制 CPU 和内存使用。
  * 考虑到静态和实例方法可以公开给 JavaScript 客户端。 除非设计要求共享具有适当约束的状态，否则应避免跨会话共享状态。
    * 对于最初通过`DotNetReference`依赖项注入 （DI） 创建的对象公开的方法，这些对象应注册为作用域对象。 这适用于Blazor服务器应用使用的任何 DI 服务。
    * 对于静态方法，避免建立无法限定到客户端的状态，除非应用在服务器实例上的所有用户之间显式共享状态。
  * 避免将参数中用户提供的数据传递给 JavaScript 调用。 如果绝对需要传入参数中的数据，请确保 JavaScript 代码处理传递数据，而不会引入[跨站点脚本 （XSS）](#cross-site-scripting-xss)漏洞。 例如，不要通过设置元素`innerHTML`的属性将用户提供的数据写入文档对象模型 （DOM）。 请考虑使用[内容安全策略 （CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP) `eval`来禁用和其他不安全的 JavaScript 基元。
* 避免在框架的调度实现之上实现自定义的 .NET 调用。 向浏览器公开 .NET 方法是一种高级方案，不建议用于Blazor一般开发。

### <a name="events"></a>事件

事件为Blazor服务器应用提供入口点。 保护 Web 应用中终结点的相同规则适用于服务器应用中Blazor的事件处理。 恶意客户端可以发送它希望作为事件的有效负载发送的任何数据。

例如：

* 的更改事件`<select>`可以发送不在应用向客户端显示的选项中的值。
* 可以`<input>`绕过客户端验证向服务器发送任何文本数据。

应用必须验证应用处理的任何事件的数据。 框架Blazor[窗体组件](xref:blazor/forms-validation)执行基本验证。 如果应用使用自定义窗体组件，则必须编写自定义代码以根据需要验证事件数据。

Blazor服务器事件是异步的，因此在应用有时间通过生成新的呈现来做出反应之前，可以将多个事件调度到服务器。 这需要考虑一些安全问题。 必须在事件处理程序内执行限制应用中的客户端操作，而不是依赖于当前呈现的视图状态。

考虑一个计数器组件，该组件应允许用户将计数器增量最多三次。 用于增加计数器的按钮基于`count`： 的值有条件地基于 ：

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

客户端可以在框架生成此组件的新呈现之前调度一个或多个增量事件。 结果是，用户可以增量`count`*三次以上*，因为 UI 未足够快地删除该按钮。 实现三`count`个增量限制的正确方法如下示例所示：

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

通过在处理程序中`if (count < 3) { ... }`添加检查，增量`count`决定基于当前应用状态。 决策不像以前示例中那样基于 UI 的状态，后者可能暂时过时。

### <a name="guard-against-multiple-dispatches"></a>防止多个调度

如果事件回调异步调用长时间运行的操作（例如从外部服务或数据库提取数据），请考虑使用保护。 在操作进行中，使用视觉反馈，保护可以防止用户排队执行多个操作。 从服务器获取数据时`isLoading``GetForecastAsync`，`true`以下组件代码将集到。 当`isLoading``true`是 时，该按钮在 UI 中禁用：

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

如果后台操作使用`async`-`await`模式异步执行，则上述示例中演示的防护模式有效。

### <a name="cancel-early-and-avoid-use-after-dispose"></a>提前取消，避免处置后使用

除了使用["防护"中所述的防护装置（如针对多个调度部分](#guard-against-multiple-dispatches)）外，请考虑<xref:System.Threading.CancellationToken>在释放组件时使用 取消长时间运行的操作。 此方法具有避免组件在*处置后使用*的额外好处：

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>避免生成大量数据的事件

某些 DOM 事件（`oninput`如`onscroll`或 ）可以生成大量数据。 避免在Blazor服务器应用中使用这些事件。

## <a name="additional-security-guidance"></a>其他安全指南

保护ASP.NET核心应用的指南适用于Blazor服务器应用，并涵盖以下各节：

* [日志记录和敏感数据](#logging-and-sensitive-data)
* [使用 HTTPS 保护传输中的信息](#protect-information-in-transit-with-https)
* [跨站点脚本 （XSS）](#cross-site-scripting-xss)）
* [跨源保护](#cross-origin-protection)
* [单击劫持](#click-jacking)
* [打开重定向](#open-redirects)

### <a name="logging-and-sensitive-data"></a>日志记录和敏感数据

客户端和服务器之间的 JS 交互记录在服务器的日志中，并与实例一起<xref:Microsoft.Extensions.Logging.ILogger>记录。 Blazor避免记录敏感信息，如实际事件或 JS 互通输入和输出。

当服务器上发生错误时，框架会通知客户端并撕下会话。 默认情况下，客户端会收到一条通用错误消息，可在浏览器的开发人员工具中看到。

客户端错误不包括调用堆栈，并且不提供有关错误原因的详细信息，但服务器日志确实包含此类信息。 出于开发目的，可以通过启用详细的错误来向客户端提供敏感的错误信息。

通过：

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`配置密钥。 例如，将`ASPNETCORE_DETAILEDERRORS`环境变量设置为`true`的值。

> [!WARNING]
> 向 Internet 上的客户端公开错误信息是应始终避免的安全风险。

### <a name="protect-information-in-transit-with-https"></a>使用 HTTPS 保护传输中的信息

Blazor服务器用于SignalR客户端和服务器之间的通信。 Blazor服务器通常使用协商的SignalR传输，通常是 WebSocket。

Blazor服务器不能确保服务器和客户端之间发送的数据的完整性和机密性。 始终使用 HTTPS。

### <a name="cross-site-scripting-xss"></a>跨站点脚本 （XSS）

跨站点脚本 （XSS） 允许未经授权的一方在浏览器上下文中执行任意逻辑。 受攻击的应用可能在客户端上运行任意代码。 该漏洞可用于对服务器执行许多恶意操作：

* 向服务器发送虚假/无效事件。
* 派单失败/无效呈现完成。
* 避免调度渲染完成。
* 将跨攻呼叫从 JavaScript 调度到 .NET。
* 修改从 .NET 到 JavaScript 的互操作调用的响应。
* 避免将 .NET 调度到 JS 互通结果。

Blazor服务器框架采取措施防止上述威胁：

* 如果客户端未确认呈现批处理，则停止生成新的 UI 更新。 配置了`CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`。
* 在一分钟后超时任何 .NET 到 JavaScript 调用，而不会收到客户端的响应。 配置了`CircuitOptions.JSInteropDefaultCallTimeout`。
* 在 JS 互通期间对来自浏览器的所有输入执行基本验证：
  * .NET 引用有效，且为 .NET 方法预期的类型。
  * 数据没有格式错误。
  * 有效负载中存在该方法的正确参数数。
  * 在调用 方法之前，可以正确反序列化参数或结果。
* 在来自来自来自调度事件的所有输入中执行基本验证：
  * 事件具有有效类型。
  * 可以对事件的数据进行反序列化。
  * 有一个事件处理程序与事件相关联。

除了框架实现的安全措施外，开发人员还必须对应用进行编码，以防止威胁并采取适当操作：

* 在处理事件时，始终验证数据。
* 在接收无效数据时采取适当的操作：
  * 忽略数据并返回。 这允许应用继续处理请求。
  * 如果应用确定输入是非法的，并且无法由合法客户端生成，则引发异常。 引发异常会撕裂电路并结束会话。
* 不要相信日志中包含的呈现批处理完成提供的错误消息。 该错误由客户端提供，通常不能信任，因为客户端可能会受到威胁。
* 不要信任在 JavaScript 和 .NET 方法之间朝任一方向对 JS 互操作调用的输入。
* 应用负责验证参数和结果的内容是否有效，即使参数或结果已正确反序列化。

要存在 XSS 漏洞，应用必须在呈现的页面中包含用户输入。 Blazor服务器组件执行编译时间步骤，其中 *.razor*文件中的标记转换为过程 C# 逻辑。 在运行时，C# 逻辑生成描述元素、文本和子组件的*呈现树*。 这通过一系列 JavaScript 指令应用于浏览器的 DOM（或在预渲染的情况下序列化为 HTML）：

* 通过普通 Razor 语法（例如），`@someStringValue`呈现的用户输入不会公开 XSS 漏洞，因为 Razor 语法通过只能写入文本的命令添加到 DOM。 即使该值包含 HTML 标记，该值也会显示为静态文本。 预渲染时，输出由 HTML 编码，该输出还会将内容显示为静态文本。
* 不允许脚本标记，不应包含在应用的组件呈现树中。 如果组件的标记中包含脚本标记，则生成编译时间错误。
* 组件作者无需使用 Razor 即可创作 C# 中的组件。 组件作者负责在发出输出时使用正确的 API。 例如，使用`builder.AddContent(0, someUserSuppliedString)`*而不是*`builder.AddMarkupContent(0, someUserSuppliedString)`，因为后者可能会创建 XSS 漏洞。

作为防范 XSS 攻击的一部分，请考虑实施 XSS 缓解措施，如[内容安全策略 （CSP）。](https://developer.mozilla.org/docs/Web/HTTP/CSP)

有关详细信息，请参阅 <xref:security/cross-site-scripting>。

### <a name="cross-origin-protection"></a>跨源保护

跨源攻击涉及来自不同来源的客户端对服务器执行操作。 恶意操作通常是 GET 请求或表单 POST（跨站点请求伪造、CSRF），但也可以打开恶意 WebSocket。 Blazor服务器应用提供[与使用中心协议的任何其他SignalR应用相同的保证：](xref:signalr/security)

* Blazor除非采取其他措施防止服务器应用跨源访问，否则可以跨源访问服务器应用。 要禁用跨源访问，请通过将 CORS 中间件添加到管道并将 添加到`DisableCorsAttribute`Blazor终结点元数据或通过[配置跨源资源共享SignalR来](xref:signalr/security#cross-origin-resource-sharing)限制允许源集，从而禁用终结点中的 CORS。
* 如果启用了 CORS，则可能需要执行额外的步骤来保护应用，具体取决于 CORS 配置。 如果 CORS 已全局启用，则可以通过在调用Blazor`DisableCorsAttribute``hub.MapBlazorHub()`后将元数据添加到终结点元数据来禁用服务器中心。

有关详细信息，请参阅 <xref:security/anti-request-forgery>。

### <a name="click-jacking"></a>单击劫持

单击劫持涉及将网站从不同来源呈现为`<iframe>`站点内部，以诱使用户在受到攻击的站点上执行操作。

要保护应用不在 中`<iframe>`呈现，请使用[内容安全策略 （CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)和`X-Frame-Options`标头。 有关详细信息，请参阅[MDN Web 文档：X 帧选项](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。

### <a name="open-redirects"></a>打开重定向

当Blazor服务器应用会话启动时，服务器将执行作为启动会话的一部分发送的 URL 的基本验证。 在建立电路之前，框架会检查基本 URL 是否是当前 URL 的父 URL。 框架不执行其他检查。

当用户选择客户端上的链接时，链接的 URL 将发送到服务器，从而确定要执行的操作。 例如，应用可以执行客户端导航或指示浏览器转到新位置。

组件还可以使用 以编程方式触发导航请求`NavigationManager`。 在这种情况下，应用可能会执行客户端导航或指示浏览器转到新位置。

组件必须：

* 避免将用户输入用作导航调用参数的一部分。
* 验证参数以确保应用允许目标。

否则，恶意用户可以强制浏览器转到攻击者控制的站点。 在这种情况下，攻击者会诱使应用使用某些用户输入作为`NavigationManager.Navigate`方法调用的一部分。

在将链接作为应用的一部分呈现时，此建议也适用于：

* 如果可能，请使用相对链接。
* 在将绝对链接目标包括在页面中之前，验证其绝对链接目标是否有效。

有关详细信息，请参阅 <xref:security/preventing-open-redirects>。

## <a name="authentication-and-authorization"></a>身份验证和授权

有关身份验证和授权的指南，请参阅<xref:security/blazor/index>。

## <a name="security-checklist"></a>安全清单

以下安全注意事项列表并不全面：

* 验证事件参数。
* 验证 JS 互通调用的输入和结果。
* 避免使用 （或事先验证） 用户输入 .NET 到 JS 互通调用。
* 防止客户端分配未绑定的内存量。
  * 组件中的数据。
  * `DotNetObject`返回给客户端的引用。
* 防止多个调度。
* 释放组件时取消长时间运行的操作。
* 避免生成大量数据的事件。
* 避免将用户输入用作调用`NavigationManager.Navigate`URL 的一部分，如果不可避免，请先根据一组允许的原点验证 URL 的用户输入。
* 不要根据 UI 的状态做出授权决策，而只能根据组件状态做出授权决策。
* 请考虑使用[内容安全策略 （CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)来抵御 XSS 攻击。
* 请考虑使用 CSP 和[X 帧选项](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)来防止咔嗒声劫持。
* 在启用 CORS 或显式禁用应用Blazor的 CORS 时，确保 CORS 设置是合适的。
* 测试以确保Blazor应用的服务器端限制提供可接受的用户体验，而不会造成不可接受的风险级别。
