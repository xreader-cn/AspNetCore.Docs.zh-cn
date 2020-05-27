---
标题： "ASP.NET Core 服务器的威胁缓解指导 Blazor " 作者：说明： "了解如何缓解对服务器应用的安全威胁 Blazor "。
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="threat-mitigation-guidance-for-aspnet-core-blazor-server"></a>ASP.NET Core Server 的威胁缓解指南 Blazor

作者：[Javier Calvarro Nelson](https://github.com/javiercn)

Blazor服务器应用采用有*状态*数据处理模型，其中服务器和客户端保持长期的关系。 持久状态由线路维护，[线路](xref:blazor/state-management)可以跨也可能长期生存期的连接。

当用户访问 Blazor 服务器站点时，服务器将在服务器的内存中创建线路。 线路向浏览器指示要呈现的内容并响应事件，例如当用户在 UI 中选择一个按钮时。 若要执行这些操作，线路会在用户的浏览器和服务器上的 .NET 方法中调用 JavaScript 函数。 这种基于 JavaScript 的双向交互称为[javascript 互操作（JS 互操作）](xref:blazor/call-javascript-from-dotnet)。

由于在 Internet 上发生了 JS 互操作，并且客户端使用远程浏览器，因此， Blazor 服务器应用程序共享大多数 web 应用安全问题。 本主题介绍了对 Blazor 服务器应用程序的常见威胁，并提供了针对面向 Internet 的应用的威胁缓解指导指导。

在受约束的环境（如公司网络或 intranet 内部）中，一些缓解指导原则如下：

* 不适用于受约束的环境中。
* 由于受约束的环境中的安全风险不足，因此不值得实施。

## <a name="blazor-and-shared-state"></a>Blazor 和共享状态

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>资源耗尽

当客户端与服务器交互并导致服务器消耗过多资源时，可能会发生资源耗尽。 过度消耗资源主要影响：

* [CPU](#cpu)
* [内存](#memory)
* [客户端连接](#client-connections)

拒绝服务（DoS）攻击通常会设法排出应用或服务器的资源。 但是，资源耗尽并不一定是对系统的攻击。 例如，由于用户需求较高，因此有限资源可能会耗尽。 [拒绝服务（DoS）攻击](#denial-of-service-dos-attacks)部分进一步介绍了 DoS。

框架外部资源（ Blazor 例如数据库和文件句柄（用于读取和写入文件））可能还会遇到资源耗尽。 有关详细信息，请参阅 <xref:performance/performance-best-practices>。

### <a name="cpu"></a>CPU

当一个或多个客户端强制服务器执行密集型 CPU 工作时，就会出现 CPU 消耗。

例如，考虑一个 Blazor 计算*Fibonnacci 数字*的服务器应用程序。 Fibonnacci 数由 Fibonnacci 序列生成，其中序列中的每个数字都是上述两个数字之和。 达到该应答值所需的工作量取决于序列的长度和初始值的大小。 如果应用不会对客户端的请求施加限制，CPU 密集型计算可能会占用 CPU 时间，并降低其他任务的性能。 过度消耗资源是影响可用性的安全问题。

对于所有面向公众的应用，都需要考虑 CPU 消耗。 在常规 web 应用中，请求和连接将作为一项安全措施超时，但 Blazor 服务器应用不提供相同的安全措施。 Blazor在执行可能占用 CPU 的工作之前，服务器应用必须包括适当的检查和限制。

### <a name="memory"></a>内存

当一个或多个客户端强制服务器消耗大量内存时，可能会出现内存耗尽。

例如，假设有一个 Blazor 服务器端应用程序，该应用程序具有接受并显示项列表的组件。 如果 Blazor 应用不会对允许的项数或向客户端呈现的项数施加限制，则内存密集型处理和呈现可能会将服务器的内存占用到服务器性能的点。 服务器可能会崩溃或速度太慢，因为似乎已崩溃。

请考虑以下方案来维护和显示与服务器上可能的内存消耗方案相关的项列表：

* `List<MyItem>`属性或字段中的项使用服务器的内存。 如果应用允许项列表无限制地增长，则服务器的内存不足。 内存不足会导致当前会话结束（崩溃），并且该服务器实例中的所有并发会话都将收到内存不足异常。 若要防止这种情况发生，应用程序必须使用在并发用户上施加项限制的数据结构。
* 如果分页方案不用于呈现，则服务器将为用户界面中不可见的对象使用额外内存。 如果项目数量没有限制，内存需求可能会耗尽可用的服务器内存。 若要防止这种情况，请使用以下方法之一：
  * 在呈现时使用分页列表。
  * 仅显示前100到1000项，并要求用户输入搜索条件以查找超出所显示项的项。
  * 对于更高级的渲染方案，实现支持*虚拟化*的列表或网格。 使用虚拟化，列表只呈现用户当前可见的项的子集。 当用户与 UI 中的滚动条交互时，组件仅呈现显示所需的项。 当前不需要的显示内容可保存在辅助存储中，这是理想的方法。 副标题项还可以保存在内存中，这种情况不太理想。

Blazor服务器应用程序为有状态应用程序（如 WPF、Windows 窗体或 WebAssembly）的其他 UI 框架提供了类似的编程模型 Blazor 。 主要区别在于，在多个 UI 框架中，应用使用的内存属于客户端，仅影响单个客户端。 例如， Blazor WebAssembly 应用完全在客户端上运行，并且只使用客户端内存资源。 在 Blazor 服务器方案中，应用使用的内存属于服务器，并在服务器实例上的客户端之间共享。

服务器端内存需求是所有服务器应用的注意事项 Blazor 。 但是，大多数 web 应用都是无状态的，并且在返回响应时，将释放在处理请求时使用的内存。 作为一般建议，不允许客户端分配未绑定的内存量，就像在任何其他服务器端应用程序中保留客户端连接。 服务器应用使用的内存 Blazor 比单个请求长时间保留。

> [!NOTE]
> 在开发过程中，可以使用探查器或捕获捕获的跟踪来评估客户端的内存需求。 探查器或跟踪不会捕获分配给特定客户端的内存。 若要捕获开发期间特定客户端的内存使用情况，请捕获转储，并检查以用户线路为根的所有对象的内存需求。

### <a name="client-connections"></a>客户端连接

当一个或多个客户端与服务器建立的并发连接太多时，可能会发生连接耗尽，这会阻止其他客户端建立新连接。

Blazor只要浏览器窗口处于打开状态，客户端就会为每个会话建立单个连接并保持连接打开状态。 维护所有连接的服务器上的要求并不特定于 Blazor 应用。 由于连接的持久性性质和服务器应用的有状态性质 Blazor ，连接用尽对于应用的可用性有更大的风险。

默认情况下，服务器应用的每个用户的连接数没有限制 Blazor 。 如果应用需要连接限制，请采用以下一种或多种方法：

* 要求身份验证，这自然会限制未经授权的用户连接到应用的能力。 要使这种情况生效，用户必须能够在不设置新用户的情况下进行设置。
* 限制每个用户的连接数。 可以通过以下方法来限制连接的限制。 请注意允许合法用户访问应用（例如，在基于客户端的 IP 地址建立连接限制时）。
  * 在应用级别：
    * 终结点路由扩展性。
    * 要求进行身份验证以连接到应用并跟踪每个用户的活动会话。
    * 达到限制时拒绝新会话。
    * 代理 WebSocket 通过使用代理连接到应用，例如，将客户端多路复用连接到应用的[Azure SignalR 服务](/azure/azure-signalr/signalr-overview)。 这为应用提供的连接容量比单个客户端可以建立的连接容量大，从而防止客户端耗尽连接到服务器。
  * 在服务器级别：在应用前使用代理/网关。 例如， [Azure 前门](/azure/frontdoor/front-door-overview)使你可以定义、管理和监视 web 流量到应用的全局路由。

## <a name="denial-of-service-dos-attacks"></a>拒绝服务（DoS）攻击

拒绝服务（DoS）攻击涉及客户端导致服务器耗尽其一个或多个资源，使应用不可用。 Blazor服务器应用包括一些默认限制，并依赖于其他 ASP.NET Core 和 SignalR 限制来防范设置的 DoS 攻击 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 。

| Blazor服务器应用程序限制 | 说明 | 默认 |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | 给定服务器一次在内存中保留的最大断开连接电路数。 | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | 断开连接线路之前在内存中保留的最大时间。 | 3 分钟 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | 服务器在超时异步 JavaScript 函数调用之前等待的最长时间。 | 1 分钟 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | 服务器在给定时间为每个线路保存在内存中的未确认呈现批处理的最大数目，以支持可靠的重新连接。 达到该限制后，服务器将停止生成新的呈现批，直到客户端确认了一个或多个批处理。 | 10 |

使用设置单个传入集线器消息的最大消息大小 <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> 。

| SignalR和 ASP.NET Core 限制 | 说明 | 默认 |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | 单个消息的消息大小。 | 32 KB |

## <a name="interactions-with-the-browser-client"></a>与浏览器（客户端）的交互

客户端通过 JS 互操作事件调度与服务器交互，并呈现完成。 在 JavaScript 和 .NET 之间，JS 互操作通信是双向的：

* 浏览器事件以异步方式从客户端调度到服务器。
* 服务器根据需要以异步方式 rerendering UI。

### <a name="javascript-functions-invoked-from-net"></a>从 .NET 调用的 JavaScript 函数

对于从 .NET 方法到 JavaScript 的调用：

* 所有调用都具有可配置的超时时间，在此之后，将返回 <xref:System.OperationCanceledException> 到调用方。
  * 调用的默认超时值（ <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> ）为一分钟。 若要配置此限制，请参阅 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls> 。
  * 可以提供取消标记以按调用控制取消。 如果提供了取消标记，则使用默认调用超时，如果有可能，则依赖于对客户端的任何调用。
* JavaScript 调用的结果不能受信任。 Blazor在浏览器中运行的应用程序客户端将搜索要调用的 JavaScript 函数。 调用函数，并生成结果或错误。 恶意客户端可以尝试：
  * 通过从 JavaScript 函数返回错误，导致应用中出现问题。
  * 通过从 JavaScript 函数返回意外的结果，在服务器上引发意外的行为。

请采取以下预防措施来防范前述方案：

* 将 JS 互操作调用包装在[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中，以考虑在调用期间可能发生的错误。 有关详细信息，请参阅 <xref:blazor/handle-errors#javascript-interop>。
* 在执行任何操作之前，验证从 JS 互操作调用返回的数据，包括错误消息。

### <a name="net-methods-invoked-from-the-browser"></a>从浏览器调用的 .NET 方法

不要信任从 JavaScript 到 .NET 方法的调用。 向 JavaScript 公开 .NET 方法时，请考虑 .NET 方法的调用方式：

* 像对应用程序的公共终结点一样对待公开给 JavaScript 的任何 .NET 方法。
  * 验证输入。
    * 确保值在预期范围内。
    * 确保用户有权执行所请求的操作。
  * 在 .NET 方法调用中，不要分配过多的资源。 例如，对 CPU 和内存使用执行检查并施加限制。
  * 请考虑可向 JavaScript 客户端公开静态和实例方法。 避免在不同的会话中共享状态，除非设计调用具有相应约束的共享状态。
    * 对于通过 `DotNetReference` 依赖关系注入（DI）最初创建的对象公开的实例方法，应将这些对象注册为范围对象。 这适用于服务器应用使用的任何 DI 服务 Blazor 。
    * 对于静态方法，请避免建立不能作用于客户端的状态，除非应用在服务器实例上的所有用户之间通过设计显式共享状态。
  * 避免将用户提供的数据传入参数到 JavaScript 调用。 如果绝对需要在参数中传递数据，请确保 JavaScript 代码处理数据，而不引入[跨站点脚本（XSS）](#cross-site-scripting-xss)漏洞。 例如，不要通过设置元素的属性将用户提供的数据写入文档对象模型（DOM） `innerHTML` 。 请考虑使用[内容安全策略（CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)来禁用 `eval` 和其他不安全的 JavaScript 基元。
* 避免在框架的调度实现之上实现 .NET 调用的自定义调度。 将 .NET 方法公开给浏览器是一种高级方案，不建议用于常规 Blazor 开发。

### <a name="events"></a>事件

事件提供 Blazor 服务器应用程序的入口点。 用于保护 web 应用中的终结点的相同规则适用于服务器应用中的事件处理 Blazor 。 恶意客户端可以将其希望发送的任何数据发送为事件的负载。

例如：

* 的更改事件 `<select>` 可能会发送一个值，该值不在应用提供给客户端的选项中。
* `<input>`可以跳过客户端验证，将任何文本数据发送到服务器。

应用必须验证应用处理的任何事件的数据。 Blazor框架[窗体组件](xref:blazor/forms-validation)执行基本验证。 如果应用使用自定义窗体组件，则必须编写自定义代码以根据需要验证事件数据。

Blazor服务器事件是异步的，因此在应用程序有时间通过生成新的呈现方式之前，可以将多个事件分派给服务器。 这有一些需要注意的安全问题。 必须在事件处理程序中执行限制应用程序中的客户端操作，而不是依赖于当前呈现的视图状态。

假设有一个计数器组件，该组件应该允许用户最多递增三次计数器。 根据的值，有条件地递增计数器的按钮 `count` ：

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

在框架生成此组件的新呈现之前，客户端可以调度一个或多个增量事件。 因此， `count` 用户可以在*三次*后递增，因为用户界面不会快速删除该按钮。 下面的示例显示了实现三个增量限制的正确方法 `count` ：

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

通过在 `if (count < 3) { ... }` 处理程序内添加检查，可以根据 `count` 当前应用程序状态进行增量决定。 该决策不基于 UI 的状态，这可能是暂时过时的。

### <a name="guard-against-multiple-dispatches"></a>防范多个派单

如果事件回调异步调用长时间运行的操作（例如从外部服务或数据库提取数据），请考虑使用临界。 当操作正在进行时，该保护可阻止用户在执行多个操作的同时进行可视反馈。 以下组件代码将设置 `isLoading` 为， `true` 同时 `GetForecastAsync` 从服务器获取数据。 当 `isLoading` 为时 `true` ，该按钮在 UI 中处于禁用状态：

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

如果后台操作以模式异步执行，则上述示例中演示的临界模式将起作用 `async` - `await` 。

### <a name="cancel-early-and-avoid-use-after-dispose"></a>尽早取消并避免使用-dispose

除了使用防范[多个派单](#guard-against-multiple-dispatches)部分中所述的临界外，还应考虑使用在 <xref:System.Threading.CancellationToken> 释放组件时取消长时间运行的操作。 此方法的优点在于避免在组件中*使用后期*处理：

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

某些 DOM 事件（如 `oninput` 或 `onscroll` ）可能会生成大量的数据。 避免在服务器应用中使用这些事件 Blazor 。

## <a name="additional-security-guidance"></a>其他安全指南

有关保护 ASP.NET Core 应用的指南适用于 Blazor 服务器应用，请在以下各节中进行介绍：

* [日志记录和敏感数据](#logging-and-sensitive-data)
* [通过 HTTPS 保护传输中的信息](#protect-information-in-transit-with-https)
* [跨站点脚本（XSS）](#cross-site-scripting-xss)
* [跨源保护](#cross-origin-protection)
* [单击-点击劫持](#click-jacking)
* [打开重定向](#open-redirects)

### <a name="logging-and-sensitive-data"></a>日志记录和敏感数据

客户端和服务器之间的 JS 互操作交互记录在具有实例的服务器日志中 <xref:Microsoft.Extensions.Logging.ILogger> 。 Blazor避免记录敏感信息，如实际事件或 JS 互操作输入和输出。

服务器上发生错误时，框架会通知客户端，并泪水会话。 默认情况下，客户端会收到一般错误消息，可以在浏览器的开发人员工具中查看。

客户端错误不包括调用堆栈，并且不提供有关错误原因的详细信息，但服务器日志包含这样的信息。 出于开发目的，可以通过启用详细错误，使敏感错误信息可供客户端使用。

使用以下内容启用 JavaScript 中的详细错误：

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* `DetailedErrors`设置为的配置项 `true` ，可在应用程序设置文件（*appsettings*）中进行设置。 还可以使用 `ASPNETCORE_DETAILEDERRORS` 值为的环境变量设置密钥 `true` 。

> [!WARNING]
> 向 Internet 上的客户端公开错误信息是应始终避免的安全风险。

### <a name="protect-information-in-transit-with-https"></a>通过 HTTPS 保护传输中的信息

Blazor服务器 SignalR 用于客户端和服务器之间的通信。 Blazor服务器通常使用协商的传输 SignalR ，这通常是 websocket。

Blazor服务器不确保在服务器和客户端之间发送的数据的完整性和保密性。 始终使用 HTTPS。

### <a name="cross-site-scripting-xss"></a>跨站点脚本（XSS）

跨站点脚本（XSS）允许未经授权的参与方在浏览器的上下文中执行任意逻辑。 受损的应用可能会在客户端上运行任意代码。 此漏洞可用于对服务器执行大量恶意操作：

* 向服务器发送虚假/无效事件。
* 调度失败/呈现完成无效。
* 避免调度呈现完成。
* 从 JavaScript 向 .NET 调度互操作调用。
* 修改从 .NET 到 JavaScript 的互操作调用的响应。
* 避免将 .NET 调度到 JS 互操作结果。

Blazor服务器框架采用一些步骤来防范前面的一些威胁：

* 如果客户端未确认呈现批次，则停止生成新的 UI 更新。 配置了 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> 。
* 在一分钟后无需收到客户端响应的情况下，任何 .NET 到 JavaScript 调用。 配置了 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> 。
* 对在 JS 互操作期间来自浏览器的所有输入执行基本验证：
  * .NET 引用是有效的，并且是 .NET 方法所需的类型。
  * 数据的格式不正确。
  * 有效负载中存在方法的参数的正确数目。
  * 参数或结果可以在调用方法之前正确地进行反序列化。
* 在来自浏览器的事件的所有输入中执行基本验证：
  * 事件的类型有效。
  * 可对事件的数据进行反序列化。
  * 存在与事件关联的事件处理程序。

除了框架实现的保护机制，开发人员还必须对应用程序进行编码以防范威胁并采取适当的措施：

* 处理事件时始终验证数据。
* 接收无效数据时采取适当的措施：
  * 忽略数据并返回。 这允许应用继续处理请求。
  * 如果应用确定输入是非法的且无法由合法的客户端生成，将引发异常。 引发异常泪水线路并结束会话。
* 不要信任日志中包含的呈现批处理完成所提供的错误消息。 此错误是由客户端提供的，并且通常无法信任，因为客户端可能会受到威胁。
* 不要在 JavaScript 和 .NET 方法之间的任意方向上信任对 JS 互操作调用的输入。
* 应用负责验证参数和结果的内容是否有效，即使参数或结果已正确反序列化。

要使 XSS 漏洞存在，应用必须将用户输入合并到呈现的页面中。 Blazor服务器组件执行编译时步骤，在该步骤中，将*razor*文件中的标记转换为过程 c # 逻辑。 在运行时，c # 逻辑生成描述元素、文本和子组件的*呈现树*。 此操作通过一系列 JavaScript 指令应用于浏览器的 DOM （或者在预呈现时序列化为 HTML）：

* 通过常规语法呈现的用户输入 Razor （例如 `@someStringValue` ）不会公开 XSS 漏洞，因为该 Razor 语法通过只能写入文本的命令添加到 DOM。 即使该值包含 HTML 标记，该值也会显示为静态文本。 预呈现时，输出是 HTML 编码的，它还将内容显示为静态文本。
* 不允许使用脚本标记，也不应将其包含在应用的组件呈现树中。 如果某个脚本标记包含在组件的标记中，则会生成编译时错误。
* 组件作者可以使用 c # 编写组件，而无需使用 Razor 。 组件作者负责发出输出时使用正确的 Api。 例如，使用 `builder.AddContent(0, someUserSuppliedString)` 而*不*是 `builder.AddMarkupContent(0, someUserSuppliedString)` ，因为后者可能会创建 XSS 漏洞。

作为防范 XSS 攻击的一部分，请考虑实施 XSS 缓解，如[内容安全策略（CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)。

有关详细信息，请参阅 <xref:security/cross-site-scripting>。

### <a name="cross-origin-protection"></a>跨源保护

跨域攻击涉及到针对服务器执行操作的不同源中的客户端。 恶意操作通常是 GET 请求或窗体 POST （跨站点请求伪造，CSRF），但也可以打开恶意 WebSocket。 Blazor服务器应用[ SignalR 通过使用集线器协议的任何其他应用提供相同的保证](xref:signalr/security)：

* Blazor可以跨源访问服务器应用，除非采取其他措施来阻止服务器应用。 若要禁用跨域访问，请在终结点中禁用 CORS，方法是将 CORS 中间件添加到管道，将添加 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 到 Blazor 终结点元数据，或通过[配置 SignalR 跨域资源共享](xref:signalr/security#cross-origin-resource-sharing)来限制允许的来源集。
* 如果启用了 CORS，则可能需要执行额外的步骤来保护应用，具体情况视 CORS 配置而定。 如果 CORS 处于全局启用状态，则 Blazor <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 在对终结点路由生成器调用之后，可以通过将元数据添加到终结点元数据来对服务器中心禁用 cors。

有关详细信息，请参阅 <xref:security/anti-request-forgery>。

### <a name="click-jacking"></a>单击-点击劫持

单击 "-点击劫持" 会将站点作为站点中的站点从不同的来源呈现，以便 `<iframe>` 诱使用户在受攻击的站点上执行操作。

若要保护应用程序不会呈现在中 `<iframe>` ，请使用[内容安全策略（CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)和 `X-Frame-Options` 标头。 有关详细信息，请参阅[MDN web 文档： X 框架-选项](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。

### <a name="open-redirects"></a>打开重定向

Blazor服务器应用会话启动时，服务器将对作为启动会话的一部分发送的 url 执行基本验证。 在建立线路之前，框架将检查基 URL 是否为当前 URL 的父级。 此框架不会执行其他检查。

当用户在客户端上选择某个链接时，该链接的 URL 将发送到服务器，这将确定要执行的操作。 例如，应用可能会执行客户端导航，或向浏览器指示浏览器将定位到新位置。

组件还可以通过使用以编程方式触发导航请求 <xref:Microsoft.AspNetCore.Components.NavigationManager> 。 在这种情况下，应用可能会执行客户端导航，或向浏览器指示浏览器将跳到新位置。

组件必须：

* 避免在导航调用参数中使用用户输入。
* 验证参数以确保应用允许目标。

否则，恶意用户可能会强制浏览器转向攻击者控制的站点。 在这种情况下，攻击者会在调用方法的过程中使用某些用户输入来对应用进行技巧 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 。

此建议也适用于在应用程序中呈现链接时的情况：

* 如果可能，请使用相对链接。
* 验证绝对链接目标是否有效，然后再将它们包含在页中。

有关详细信息，请参阅 <xref:security/preventing-open-redirects>。

## <a name="security-checklist"></a>安全清单

以下安全注意事项列表并不全面：

* 验证来自事件的参数。
* 通过 JS 互操作调用验证输入和结果。
* 避免对 .NET 到 JS 互操作调用使用（或预先验证）用户输入。
* 阻止客户端分配未绑定的内存量。
  * 组件中的数据。
  * `DotNetObject`返回到客户端的引用。
* 防范多个派单。
* 释放组件时，取消长时间运行的操作。
* 避免产生大量数据的事件。
* 避免使用用户输入作为对的调用的一部分 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> ，并先针对一组允许的来源验证 url 的用户输入。
* 不要基于 UI 的状态做出授权决策，只需从组件状态进行决策。
* 请考虑使用[内容安全策略（CSP）](https://developer.mozilla.org/docs/Web/HTTP/CSP)来防范 XSS 攻击。
* 请考虑使用 CSP 和[X 帧选项](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)来防范单击-点击劫持。
* 当启用 CORS 或显式禁用应用的 CORS 时，请确保 CORS 设置适用 Blazor 。
* 进行测试，以确保应用程序的服务器端限制 Blazor 提供可接受的用户体验，而无需接受的风险级别。
