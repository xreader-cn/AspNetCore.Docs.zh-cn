---
title: ASP.NET Core Blazor Server 的威胁缓解指南
author: guardrex
description: 了解如何缓解 Blazor Server 应用面临的安全威胁。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/05/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 5c3a002a8e3df030d53c8625597342a68ca0d4b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055407"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-no-locblazor-server"></a>ASP.NET Core Blazor Server 的威胁缓解指南

作者：[Javier Calvarro Nelson](https://github.com/javiercn)

Blazor Server 应用采用有状态数据处理模型，其中服务器和客户端保持长期的关系。 持久状态通过[线路](xref:blazor/state-management)维持，该线路可在同样可能长期存在的连接中持续。

当用户访问 Blazor Server 站点时，服务器会在服务器内存中创建一条线路。 该线路向浏览器指示要呈现的内容和对事件的响应，例如当用户在 UI 中选择按钮时。 为执行这些操作，线路会在用户的浏览器中调用 JavaScript 函数，在服务器上调用 .NET 方法。 这种基于 JavaScript 的双向交互被称为 [JavaScript 互操作（JS 互操作）](xref:blazor/call-javascript-from-dotnet)。

由于 JS 互操作发生在 Internet 上，且客户端使用远程浏览器，因此 Blazor Server 应用中的大多数 Web 应用安全问题是一样的。 本主题介绍 Blazor Server 应用面临的常见威胁，并提供重点讲解面向 Internet 的应用的威胁缓解指南。

在受限制的环境中（例如公司网络或 Intranet），该缓解指南的部分内容：

* 不适用于受限制的环境。
* 由于受限制的环境中的安全风险很低，因此不值得花费成本来实施。

## <a name="no-locblazor-and-shared-state"></a>Blazor 和共享状态

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>资源耗尽

当客户端与服务器交互并导致服务器使用过多资源时，可能会发生资源耗尽的情况。 资源过度消耗会主要影响：

* [CPU](#cpu)
* [内存](#memory)
* [客户端连接](#client-connections)

拒绝服务 (DoS) 攻击通常企图耗尽应用或服务器的资源。 然而，资源耗尽并不一定是系统受到攻击而导致的。 例如，如果资源有限而用户需求高，则资源可能会被耗尽。 DoS 将在[拒绝服务 (DoS) 攻击](#denial-of-service-dos-attacks)部分得到进一步讲解。

Blazor 框架外部的资源，例如数据库和文件句柄（用于读取和写入文件），也可能会耗尽资源。 有关详细信息，请参阅 <xref:performance/performance-best-practices>。

### <a name="cpu"></a>CPU

当一个或多个客户端强制服务器执行大量消耗 CPU 的工作时，可能会发生 CPU 耗尽的情况。

例如，假设有一个 Blazor Server 应用要计算 Fibonnacci 编号。 Fibonnacci 编号是根据 Fibonnacci 序列生成的，其中序列中的每个编号都是前两个编号之和。 获得答案所需的工作量取决于序列的长度和初始值的大小。 如果应用不对客户端的请求进行限制，CPU 密集型计算可能会占用 CPU 的时间，并降低其他任务的性能。 资源过度消耗是一项影响可用性的安全问题。

而 CPU 耗尽是所有面向公众的应用面临的一个问题。 在常规的 Web 应用中，请求和连接会超时（这用于保障安全），但是 Blazor Server 应用不提供这样的安全措施。 Blazor Server 应用必须包含适当的检查和限制，然后再执行可能会大量消耗 CPU 的工作。

### <a name="memory"></a>内存

当一个或多个客户端强制服务器消耗大量内存时，可能会发生内存耗尽的情况。

例如，假设有一个 Blazor 服务器端应用，它的组件会接受和显示一个项列表。 如果 Blazor 应用不限制允许的项数或返回给客户端的项数，则服务器的内存可能主要由内存密集型处理和呈现占用，以致于降低服务器性能。 服务器可能会崩溃，或者速度慢到看起来已经崩溃的程度。

请思考下面的场景，维持和显示一个项列表，而该场景可能会耗尽服务器上的内存：

* `List<MyItem>` 属性或字段中的项使用服务器的内存。 如果应用允许项列表无限扩充，则存在服务器内存不足的风险。 内存不足会导致当前会话结束（崩溃），并导致该服务器实例中的所有并发会话收到“内存不足”异常。 为防止这种情况发生，应用必须使用一种数据结构来对并发用户施加项限制。
* 如果呈现时不采用分页方案，则 UI 中不可见的对象将在服务器上占用额外的内存。 如果不对项数进行限制，内存需求可能会导致可用服务器内存被耗尽。 若要防止这种情况，请使用以下方法之一：
  * 在呈现时使用分页列表。
  * 仅显示前 100 到 1000 项，并要求用户输入搜索条件来查找所显示项之外的项。
  * 对于更高级的呈现方案，实现支持虚拟化的列表或网格。 使用虚拟化时，列表只呈现用户当前可查看的部分项。 当用户与 UI 中的滚动条交互时，组件只呈现显示所需的项。 当前不需要显示的项可保存在辅助存储器中，这是理想的方法。 未显示的项也可保存在内存中，这种方法不太理想。

Blazor Server 应用为有状态应用（如 WPF、Windows 窗体或 Blazor WebAssembly）提供了与其他 UI 框架类似的编程模型。 主要区别是，在一些 UI 框架中，应用消耗的内存属于客户端，只影响单个客户端。 例如，Blazor WebAssembly 应用完全在客户端上运行，只使用客户端内存资源。 在 Blazor Server 方案中，应用消耗的内存属于服务器，并在服务器实例上的客户端之间共享。

所有 Blazor Server 应用都要考虑到服务器端内存需求。 但是，大多数 Web 应用都是无状态的，在返回响应时，处理请求时使用的内存会被释放。 一般建议是，禁止客户端分配未绑定的内存量，就像在长期维持客户端连接的其他任何服务器端应用中一样。 在单个请求被处理后，Blazor Server 应用消耗的内存还要等很长时间才会被释放。

> [!NOTE]
> 在开发过程中，可使用探查器或捕获跟踪来评估客户端的内存需求。 探查器或跟踪不会捕获分配给特定客户端的内存。 若要在开发期间捕获特定客户端的内存使用情况，请捕获转储并检查根在用户线路上的所有对象的内存需求。

### <a name="client-connections"></a>客户端连接

当一个或多个客户端打开太多到服务器的并发连接，导致其他客户端无法建立新连接时，可能会出现连接耗尽的情况。

Blazor 客户端会为每个会话建立一个连接，并且只要浏览器窗口不关闭，连接一直打开。 在服务器上维护所有连接的需求并不特定于 Blazor 应用。 鉴于连接是持久的且 Blazor Server 应用具有状态，连接耗尽更可能影响应用的可用性。

默认情况下，每位用户可在 Blazor Server 应用上建立任意数量的连接。 如果应用要求设定连接限制，请采用下面一种或多种方法：

* 要求进行身份验证，这自然会限制未经授权的用户连接到应用的能力。 若要使此方案有效，必须防止用户随意预配新用户。
* 限制每位用户的连接数。 限制连接数可通过以下方法来实现。 请在允许合法用户访问应用时小心谨慎（例如，当基于客户端的 IP 地址设定连接限制时）。
  * 在应用级别：
    * 终结点路由扩展性。
    * 需要身份验证才能连接到应用并跟踪每位用户的活动会话。
    * 达到限制后拒绝新会话。
    * 代理 WebSocket 通过使用代理连接到应用，例如使用 [Azure SignalR 服务](/azure/azure-signalr/signalr-overview)，它将从客户端到应用的连接进行多路复用处理。 这样，应用拥有的连接容量比单个客户端可建立的大，从而防止客户端耗尽与服务器的连接。
  * 在服务器级别：先使用代理/网关，再使用应用。 例如，通过 [Azure Front Door](/azure/frontdoor/front-door-overview)，可定义、管理和监视 Web 流量到应用的全局路由。

## <a name="denial-of-service-dos-attacks"></a>拒绝服务 (DoS) 攻击

拒绝服务 (DoS) 攻击包括客户端导致服务器耗尽其一个或多个资源，从而使应用不可用。 Blazor Server 应用包含一些默认限制，并依赖其他 ASP.NET Core 和 SignalR 限制来防范 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 上设置的 DoS 攻击。

| Blazor Server 应用限制 | 描述 | 默认 |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 给定服务器在内存中一次保留的断开连接的线路数上限。 | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 断开连接的线路被移除前在内存中保留的最长时间。 | 3 分钟 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 服务器在使异步 JavaScript 函数调用超时之前等待的最长时间。 | 1 分钟 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 在给定时间内，服务器在内存中对每条线路保留用以支持重新连接可靠的未确认的呈现批处理的最大数量。 达到限制后，服务器会停止生成新的呈现批处理，直到客户端确认了一个或多个批处理。 | 10 |

使用 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> 设置单个传入中心消息的最大消息大小。

| SignalR 和 ASP.NET Core 限制 | 描述 | 默认 |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | 单个消息的大小。 | 32 KB |

## <a name="interactions-with-the-browser-client"></a>与浏览器（客户端）的交互

客户端通过 JS 互操作事件调度和呈现完成来与服务器交互。 JS 互操作通信在 JavaScript 和 .NET 之间双向进行：

* 浏览器事件以异步方式从客户端发送到服务器。
* 服务器以异步方式进行响应，从而根据需要重新呈现 UI。

### <a name="javascript-functions-invoked-from-net"></a>从 .NET 调用的 JavaScript 函数

对于从 .NET 方法到 JavaScript 的调用：

* 所有调用都具有可配置的超时时间，在此超时之后它们将失败，并向调用方返回 <xref:System.OperationCanceledException>。
  * 调用 (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) 的默认超时为 1 分钟。 若要配置此限制，请参阅 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>。
  * 可提供取消令牌，根据每次调用来控制相应的取消操作。 如果可能，请采用默认调用超时；如果提供了取消令牌，则采用对客户端的任何调用的时间限制。
* 不能信任 JavaScript 调用的结果。 在浏览器中运行的 Blazor 应用客户端会搜索要调用的 JavaScript 函数。 函数会被调用，并生成结果或错误。 恶意客户端可能会尝试：
  * 通过从 JavaScript 函数返回错误，在应用中引发问题。
  * 通过从 JavaScript 函数返回意外结果，在服务器上引发意外行为。

请采取以下预防措施来防止出现上述情况：

* 在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中包装 JS 互操作调用，以处理调用期间可能发生的错误。 有关详细信息，请参阅 <xref:blazor/fundamentals/handle-errors#javascript-interop>。
* 在执行任何操作之前，请验证从 JS 互操作调用返回的数据（包括错误消息）。

### <a name="net-methods-invoked-from-the-browser"></a>从浏览器调用的 .NET 方法

不要信任从 JavaScript 到 .NET 方法的调用。 当 .NET 方法公开给 JavaScript 时，请思考如何来调用 .NET 方法：

* 将所有公开给 JavaScript 的 .NET 方法都看做是应用的公共终结点。
  * 验证输入的内容。
    * 确保值在预期范围内。
    * 确保用户有权执行请求的操作。
  * 不要在 .NET 方法调用期间分配太多资源。 例如，执行检查并限制 CPU 和内存的使用。
  * 要考虑到静态和实例方法可公开给 JavaScript 客户端。 不要跨会话共享状态，除非设计要求在设有适当约束的情况下共享状态。
    * 如果实例方法通过 `DotNetReference` 对象公开，而这些对象最初是通过依赖注入 (DI) 创建的，那么应将这些对象注册为范围对象。 这适用于 Blazor Server 应用使用的任何 DI 服务。
    * 对于静态方法，请不要建立范围无法限定为客户端的状态，除非应用根据设计在服务器实例上跨所有用户明确共享状态。
  * 不要在参数中向 JavaScript 调用传递用户提供的数据。 如果一定需要在参数中传递数据，请确保 JavaScript 代码负责数据的传递时不引入[跨站点脚本 (XSS)](#cross-site-scripting-xss) 漏洞。 例如，不要通过设置元素的 `innerHTML` 属性将用户提供的数据写入文档对象模型 (DOM)。 请考虑使用[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 来禁用 `eval` 以及其他不安全的 JavaScript 基元。
* 不要在框架的调度实现之上实现 .NET 调用的自定义调度。 向浏览器公开 .NET 方法是一种高级方案，不建议用于常规的 Blazor 开发。

### <a name="events"></a>事件

事件提供到 Blazor Server 应用的入口点。 在 Web 应用中用来保护终结点的规则也适用于 Blazor Server 应用中的事件处理。 恶意客户端可能会发送其希望作为事件有效负载发送的任何数据。

例如：

* `<select>` 的更改事件可能会发送一个在应用提供给客户端的选项中没有的值。
* `<input>` 可能会向服务器发送任何文本数据，试图绕过客户端验证。

应用必须对应用处理的任何事件的数据进行验证。 基本验证由 Blazor 框架[窗体组件](xref:blazor/forms-validation)负责执行。 如果应用使用自定义窗体组件，则必须根据需要编写自定义代码来验证事件数据。

Blazor Server 事件是异步的，因此在应用有时间通过生成新的呈现来作出回应之前，可将多个事件调度到服务器。 这需要考虑一些安全问题。 应用中对客户端操作的限制必须在事件处理程序内执行，而不依赖于当前呈现的视图状态。

假设有一个计数器组件，它应该允许用户最多递增三次计数器。 根据 `count` 的值，用于递增计数器的按钮需遵循相应条件：

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

在框架生成该组件的新呈现之前，客户端可调度一个或多个递增事件。 结果就是用户可将 `count` 递增三次以上，因为 UI 没有足够快地删除该按钮。 以下示例显示了实现三次 `count` 递增限制的正确方法：

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

通过在处理程序中添加 `if (count < 3) { ... }` 检查，根据当前应用状态来决定递增 `count`。 该决定并不像前一示例中是根据 UI 的状态作出的，该状态可能暂时无效。

### <a name="guard-against-multiple-dispatches"></a>避免多次调度

如果事件回调以异步方式调用长期运行的操作，例如从外部服务或数据库提取数据，请考虑采用保护措施。 该保护可防止用户在操作进行过程中对多个操作排队，并提供可视反馈。 当 `GetForecastAsync` 从服务器获取数据时，下列组件代码会将 `isLoading` 设置为 `true`。 当 `isLoading` 为 `true` 时，该按钮在 UI 中被禁用：

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

如果后台操作使用 `async`-`await` 模式异步执行，则前面示例中演示的保护模式将发挥作用。

### <a name="cancel-early-and-avoid-use-after-dispose"></a>提前取消，避免释放后使用

除了使用[避免多次调度](#guard-against-multiple-dispatches)部分中描述的保护之外，还可考虑在释放组件时使用 <xref:System.Threading.CancellationToken> 取消长时间运行的操作。 该方法还有一个好处，就是避免组件中的“释放后使用”：

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

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>避免产生大量数据的事件

某些 DOM 事件（例如 `oninput` 或 `onscroll`）可能会生成大量数据。 不要在 Blazor 服务器应用中使用这些事件。

## <a name="additional-security-guidance"></a>其他安全指南

ASP.NET Core 应用的保护指南适用于 Blazor Server 应用，将在以下部分进行介绍：

* [日志记录和敏感数据](#logging-and-sensitive-data)
* [使用 HTTPS 保护传输中的信息](#protect-information-in-transit-with-https)
* [跨站点脚本 (XSS)](#cross-site-scripting-xss)
* [跨源保护](#cross-origin-protection)
* [点击劫持](#click-jacking)
* [打开重定向](#open-redirects)

### <a name="logging-and-sensitive-data"></a>日志记录和敏感数据

客户端和服务器之间的 JS 互操作交互通过 <xref:Microsoft.Extensions.Logging.ILogger> 实例记录在服务器的日志中。 Blazor 避免记录敏感信息，例如实际事件或 JS 互操作输入和输出。

当服务器上发生错误时，框架会通知客户端并关闭会话。 默认情况下，客户端会收到一条通用错误消息，可在浏览器的开发人员工具中查看。

客户端错误不包括调用堆栈，也不提供有关错误原因的详细信息，但服务器日志的确包含此类信息。 出于开发目的，可通过启用详细错误向客户端提供敏感错误信息。

使用以下项在 JavaScript 中启用详细错误：

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>。
* `DetailedErrors` 配置键设置为 `true`，在应用设置文件 (`appsettings.json`) 中可进行此设置。 也可使用值为 `true` 的 `ASPNETCORE_DETAILEDERRORS` 环境变量设置此键。

> [!WARNING]
> 在 Internet 上向客户端公开错误信息是一项始终应该避免的安全风险。

### <a name="protect-information-in-transit-with-https"></a>使用 HTTPS 保护传输中的信息

Blazor Server 使用 SignalR 在客户端和服务器之间进行通信。 Blazor Server 通常使用 SignalR 协商的传输，这通常是 WebSocket。

Blazor Server 无法确保服务器和客户端之间发送的数据的完整性和机密性。 请始终使用 HTTPS。

### <a name="cross-site-scripting-xss"></a>跨站点脚本 (XSS)

跨站点脚本 (XSS) 允许未经授权的一方在浏览器的上下文中执行任意逻辑。 遭到入侵的应用可能会在客户端上运行任意代码。 该漏洞可能会被利用来对服务器执行大量恶意操作，例如：

* 向服务器发送虚假/无效事件。
* 调度失败/无效的呈现完成。
* 避免调度呈现完成。
* 调度从 JavaScript 对 .NET 的互操作调用。
* 修改从 .NET 到 JavaScript 的互操作调用的响应。
* 避免调度从 .NET 到 JS 的互操作结果。

Blazor Server 框架采取了一些步骤来防范前面的部分威胁：

* 如果客户端未确认呈现批处理，则停止生成新的 UI 更新。 配置了 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>。
* 如果没有收到来自客户端的响应，则任何从 .NET 到 JavaScript 的调用一分钟后都会超时。 配置了 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>。
* 在 JS 互操作期间对来自浏览器的所有输入执行基本验证以确保：
  * .NET 引用有效，且是 .NET 方法所需的类型。
  * 数据格式正确。
  * 方法的参数数目是有效负载中是正确的。
  * 在调用方法之前，可适当地对参数或结果进行反序列化处理。
* 在来已调度事件的浏览器的所有输入中执行基本验证以确保：
  * 事件的类型有效。
  * 可对事件的数据进行反序列化处理。
  * 存在与事件关联的事件处理程序。

除了框架实现的安全措施外，开发人员还必须对应用进行编码，以防范威胁并采取适当操作，例如：

* 处理事件时始终验证数据。
* 接收无效数据时采取适当的操作：
  * 忽略数据并返回。 这允许应用继续处理请求。
  * 如果应用确定输入是非法的并且无法由合法客户端生成，则引发异常。 引发异常会中断线路并结束会话。
* 不要信任日志中包含的呈现批处理完成所提供的错误消息。 该错误由客户端提供，通常不可信任，因为客户端可能已遭到入侵。
* 不要在 JavaScript 和 .NET 方法之间的任意方向信任 JS 互操作调用的输入。
* 应用负责验证参数和结果的内容是否有效，即使参数或结果已正确反序列化也要验证。

要使 XSS 漏洞存在，应用必须将用户输入合并到呈现的页面中。 Blazor Server 组件会执行一个编译时步骤，其中 `.razor` 文件中的标记转换为过程 C# 逻辑。 在运行时，C# 逻辑会生成一个呈现树来描述元素、文本和子组件。 它通过一系列 JavaScript 指令应用于浏览器的 DOM（如果预呈现，则序列化为 HTML）：

* 通过常规 Razor 语法（例如 `@someStringValue`）呈现的用户输入不会公开 XSS 漏洞，因为 Razor 语法通过只能写入文本的命令添加到 DOM 中。 即使该值包含 HTML 标记，它也显示为静态文本。 预呈现时，输出经过 HTML 编码，它还将内容显示为静态文本。
* 禁止使用脚本标记，也不得在应用的组件呈现树中包含它们。 如果组件标记中包含脚本标记，则会生成编译时错误。
* 组件作者无需使用 Razor 就可在 C# 中编写组件。 组件作者负责在发出输出时使用正确的 API。 例如，使用 `builder.AddContent(0, someUserSuppliedString)` 而不是 `builder.AddMarkupContent(0, someUserSuppliedString)`，因为后者可能会导致出现 XSS 漏洞。

作为防范 XSS 攻击的一部分，请考虑实施 XSS 缓解措施，例如[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)。

有关详细信息，请参阅 <xref:security/cross-site-scripting>。

### <a name="cross-origin-protection"></a>跨源保护

跨源攻击涉及到来自不同源的客户端对服务器执行操作。 恶意操作通常是 GET 请求或窗体 POST（跨站点请求伪造，简称为 CSRF），但也可能是打开恶意 WebSocket。 Blazor Server 应用提供[与使用中心协议产品/服务的任何其他 SignalR 应用相同的保证](xref:signalr/security)：

* 可跨源访问 Blazor Server 应用，除非采取额外的措施进行阻止。 若要禁用跨源访问，请将 CORS 中间件添加到管道并将 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 添加到 Blazor 终结点元数据来禁用终结点中的 CORS，或者[配置 SignalR 实现跨源资源共享](xref:signalr/security#cross-origin-resource-sharing)来仅允许跨一组来源进行访问。
* 如果启用了 CORS，则可能需要执行额外的步骤来保护应用，具体取决于 CORS 配置。 如果 CORS 已全局启用，则可在终结点路由生成器上调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 之后，将 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 元数据添加到终结点元数据来为 Blazor Server 中心禁用 CORS。

有关详细信息，请参阅 <xref:security/anti-request-forgery>。

### <a name="click-jacking"></a>点击劫持

点击劫持涉及到将一个站点呈现为来自不同来源的站点内的 `<iframe>`，以诱骗用户在受到攻击的站点上执行操作。

若要防止应用在 `<iframe>` 内部呈现，请使用[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 和 `X-Frame-Options` 标头。 有关详细信息，请参阅 [MDN Web 文档：X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。

### <a name="open-redirects"></a>打开重定向

当 Blazor Server 应用会话启动时，服务器对会话启动期间发送的 URL 执行基本验证。 在建立线路之前，框架会检查基 URL 是否是当前 URL 的父 URL。 此框架不会执行其他检查。

当用户选择客户端上的链接时，该链接的 URL 会发送到服务器，后者将确定要采取的操作。 例如，应用可能会执行客户端导航，也可能会指示浏览器转到新位置。

组件还可使用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 以编程的方式触发导航请求。 在这种情况下，应用可能会执行客户端导航或指示浏览器转到新位置。

组件必须：

* 避免将用户输入用作导航调用参数的一部分。
* 验证参数以确保应用允许目标。

否则，恶意用户可能会强制浏览器转到受攻击者控制的站点。 在这种情况下，攻击者会诱使应用使用某些用户输入作为 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 方法调用的一部分。

当将链接作为应用的一部分呈现时，此建议也适用：

* 如果可能，请使用相对链接。
* 在将绝对链接目标包含在页面中之前，请验证这些目标是否有效。

有关详细信息，请参阅 <xref:security/preventing-open-redirects>。

## <a name="security-checklist"></a>安全清单

下面仅列出了部分安全注意事项：

* 验证来自事件的参数。
* 验证 JS 互操作调用的输入和结果。
* 避免对 .NET 到 JS 的互操作调用使用（或预先验证）用户输入。
* 阻止客户端分配未绑定的内存量。
  * 组件中的数据。
  * 返回给客户端的 `DotNetObject` 引用。
* 避免多次调度。
* 释放组件时，取消长时间运行的操作。
* 避免产生大量数据的事件。
* 避免将用户输入用作对 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 调用的一部分；如果必须这样做，请先对照一组允许的来源验证 URL 的用户输入。
* 只根据组件状态做出授权决策，不要依据 UI 的状态。
* 考虑使用[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 来阻止 XSS 攻击。
* 请考虑使用 CSP 和 [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) 来阻止点击劫持。
* 在启用 CORS 或显式禁用 Blazor 应用的 CORS 时，请确保 CORS 设置是恰当的。
* 进行测试，确保 Blazor 应用的服务器端限制提供可接受的用户体验，不会带来不可接受的风险级别。
