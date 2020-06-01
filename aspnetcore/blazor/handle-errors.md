---
<span data-ttu-id="bf4fa-101">title:'处理 ASP.NET Core Blazor 应用中的错误' author: description:'了解 ASP.NET Core Blazor (Blazor) 如何管理未处理的异常，以及如何开发可检测和处理错误的应用。'</span><span class="sxs-lookup"><span data-stu-id="bf4fa-101">title: 'Handle errors in ASP.NET Core Blazor apps' author: description: 'Discover how ASP.NET Core Blazor how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.'</span></span>
<span data-ttu-id="bf4fa-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bf4fa-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bf4fa-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bf4fa-103">'Blazor'</span></span>
- <span data-ttu-id="bf4fa-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bf4fa-104">'Identity'</span></span>
- <span data-ttu-id="bf4fa-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bf4fa-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="bf4fa-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bf4fa-106">'Razor'</span></span>
- <span data-ttu-id="bf4fa-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bf4fa-107">'SignalR' uid:</span></span> 

---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="bf4fa-108">处理 ASP.NET Core Blazor 应用中的错误</span><span class="sxs-lookup"><span data-stu-id="bf4fa-108">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="bf4fa-109">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="bf4fa-109">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="bf4fa-110">本文介绍 Blazor 如何管理未经处理的异常以及如何开发用于检测和处理错误的应用。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-110">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="bf4fa-111">开发过程中的错误详细信息</span><span class="sxs-lookup"><span data-stu-id="bf4fa-111">Detailed errors during development</span></span>

<span data-ttu-id="bf4fa-112">当 Blazor 应用在开发过程中运行不正常时，从该应用接收详细的错误信息有助于故障排除和修复问题。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-112">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="bf4fa-113">出现错误时，Blazor 应用会在屏幕底部显示一个黄色条框：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-113">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="bf4fa-114">在开发过程中，黄色条框会将你定向到浏览器控制台，你可在其中查看异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-114">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="bf4fa-115">在生产过程中，黄色条框会通知用户发生了错误，并建议刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-115">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="bf4fa-116">此错误处理体验的 UI 属于 Blazor 项目模板。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-116">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="bf4fa-117">在 Blazor WebAssembly 应用的 wwwroot/index.html 文件中自定义体验：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-117">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="bf4fa-118">在 Blazor 服务器应用的 Pages/_Host.cshtml 文件中自定义体验：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-118">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="bf4fa-119">`blazor-error-ui` 元素被 Blazor 模板 (*wwwroot/css/site.css*) 附带的样式隐藏，并会在发生错误时显示：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-119">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="bf4fa-120">Blazor 服务器应用如何应对未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="bf4fa-120">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="bf4fa-121"> 服务器是一种有状态框架。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-121"> Server is a stateful framework.</span></span> <span data-ttu-id="bf4fa-122">用户与应用进行交互时，会与服务器保持名为“线路”的连接。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-122">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="bf4fa-123">线路包含活动组件实例，以及状态的许多其他方面，例如：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-123">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="bf4fa-124">最新呈现的组件输出。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-124">The most recent rendered output of components.</span></span>
* <span data-ttu-id="bf4fa-125">可由客户端事件触发的事件处理委托的当前集合。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-125">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="bf4fa-126">如果用户在多个浏览器标签页中打开应用，则具有多条独立线路。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-126">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="bf4fa-127"> 将大部分未经处理的异常视为发生该异常的线路的严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-127"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="bf4fa-128">如果线路由于未经处理的异常而终止，则用户只能重新加载页面来创建新线路，从而继续与应用进行交互。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-128">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="bf4fa-129">终止的线路以外的其他线路（即其他用户或其他浏览器标签页的线路）不会受到影响。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-129">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="bf4fa-130">此场景类似于出现故障的桌面应用。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-130">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="bf4fa-131">出现故障的应用必须重新启动，但其他应用不受影响。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-131">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="bf4fa-132">当发生未经处理的异常时，线路会终止，原因如下：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-132">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="bf4fa-133">未经处理的异常通常会将线路置于未定义状态。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-133">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="bf4fa-134">发生未经处理的异常后，应用可能无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-134">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="bf4fa-135">如果不终止线路，则可能导致应用中出现安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-135">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="bf4fa-136">在开发人员代码中管理未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="bf4fa-136">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="bf4fa-137">若要在出现错误后继续运行应用，该应用必须具备错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-137">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="bf4fa-138">本文后面的部分将介绍未经处理的异常出现的潜在原因。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-138">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="bf4fa-139">在生产环境中，不要在 UI 中呈现框架异常消息或堆栈跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-139">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="bf4fa-140">呈现异常消息或堆栈跟踪信息可能导致：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-140">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="bf4fa-141">向最终用户公开敏感信息。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-141">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="bf4fa-142">帮助恶意用户发现应用中可能会危及应用、服务器或网络安全的弱点。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-142">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="bf4fa-143">使用永久性提供程序记录错误信息</span><span class="sxs-lookup"><span data-stu-id="bf4fa-143">Log errors with a persistent provider</span></span>

<span data-ttu-id="bf4fa-144">在发生未经处理的异常时，将异常记录到在服务容器中配置的 <xref:Microsoft.Extensions.Logging.ILogger> 实例。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-144">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="bf4fa-145">默认情况下，Blazor 应用使用控制台日志记录提供程序记录到控制台输出中。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-145">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="bf4fa-146">请考虑使用管理日志大小和日志轮换的提供程序将日志记录到更持久的位置。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-146">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="bf4fa-147">有关详细信息，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-147">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="bf4fa-148">在开发过程中，Blazor 通常会将异常的完整详细信息发送到浏览器的控制台，以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-148">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="bf4fa-149">在生产环境中，浏览器控制台中的错误详细信息默认禁用，也就是说错误信息不会发送到客户端，但异常的完整详细信息仍记录在服务器端。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-149">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="bf4fa-150">有关详细信息，请参阅 <xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-150">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="bf4fa-151">必须确定要记录的事件以及已记录的事件的严重性级别。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-151">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="bf4fa-152">恶意用户也许能刻意触发错误。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-152">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="bf4fa-153">例如，若显示产品详细信息的组件的 URL 中提供了未知的 `ProductId`，则请勿记录错误中的事件。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-153">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="bf4fa-154">不是所有的错误都应被视为高严重性事件进行记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-154">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="bf4fa-155">有关详细信息，请参阅 <xref:fundamentals/logging/index#create-logs-in-blazor>。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-155">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="bf4fa-156">可能发生错误的位置</span><span class="sxs-lookup"><span data-stu-id="bf4fa-156">Places where errors may occur</span></span>

<span data-ttu-id="bf4fa-157">框架和应用代码可能会在以下任一位置触发未经处理的异常：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-157">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="bf4fa-158">组件实例化</span><span class="sxs-lookup"><span data-stu-id="bf4fa-158">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="bf4fa-159">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="bf4fa-159">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="bf4fa-160">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="bf4fa-160">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="bf4fa-161">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="bf4fa-161">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="bf4fa-162">组件处置</span><span class="sxs-lookup"><span data-stu-id="bf4fa-162">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="bf4fa-163">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="bf4fa-163">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="bf4fa-164">[Blazor服务器重新呈现](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="bf4fa-164">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="bf4fa-165">本文的以下部分介绍了上述未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-165">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="bf4fa-166">组件实例化</span><span class="sxs-lookup"><span data-stu-id="bf4fa-166">Component instantiation</span></span>

<span data-ttu-id="bf4fa-167">当 Blazor 创建某组件的实例时：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-167">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="bf4fa-168">会调用该组件的构造函数。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-168">The component's constructor is invoked.</span></span>
* <span data-ttu-id="bf4fa-169">会调用通过 [`@inject`](xref:mvc/views/razor#inject) 指令或 [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) 特性提供给组件构造函数的非单一 DI 设备的构造函数。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-169">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="bf4fa-170">如果任何已执行的构造函数或任何 `[Inject]` 属性的资源库引发了未经处理的异常，则 Blazor 服务器线路会失败。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-170">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="bf4fa-171">这是严重异常，因为框架无法实例化组件。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-171">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="bf4fa-172">如果构造函数逻辑可能引发异常，应用应使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-172">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="bf4fa-173">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="bf4fa-173">Lifecycle methods</span></span>

<span data-ttu-id="bf4fa-174">在组件的生命周期内，Blazor 会调用以下[生命周期方法](xref:blazor/lifecycle)：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-174">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="bf4fa-175">如果任何生命周期方法以同步或异步方式引发异常，则该异常对于 Blazor 服务器线路而言是严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-175">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="bf4fa-176">若要使组件处理生命周期方法中的错误，请添加错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-176">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="bf4fa-177">在下面的示例中，<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 会调用方法来获取产品：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-177">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="bf4fa-178">在 `ProductRepository.GetProductByIdAsync` 方法中抛出的异常由 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句进行处理。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-178">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="bf4fa-179">在执行 `catch` 块时：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-179">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="bf4fa-180">`loadFailed` 设置为 `true`，用于向用户显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-180">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="bf4fa-181">错误会被记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-181">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="bf4fa-182">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="bf4fa-182">Rendering logic</span></span>

<span data-ttu-id="bf4fa-183">`.razor` 组件文件中的声明性标记被编译到名为 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 的 C# 方法中。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-183">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="bf4fa-184">当组件呈现时，<xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 会执行并构建一个数据结构，该结构描述所呈现组件的元素、文本和子组件。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-184">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="bf4fa-185">呈现逻辑可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-185">Rendering logic can throw an exception.</span></span> <span data-ttu-id="bf4fa-186">例如评估了 `@someObject.PropertyName`，但 `@someObject` 为 `null` 时，就会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-186">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="bf4fa-187">呈现逻辑引发的未经处理的异常对于 Blazor 服务器线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-187">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="bf4fa-188">为防止呈现逻辑中出现空引用异常，请在访问其成员之前检查 `null` 对象。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-188">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="bf4fa-189">在以下示例中，如果 `person.Address` 为 `null`，则不访问 `person.Address` 属性：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-189">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="bf4fa-190">上述代码假定 `person` 不是 `null`。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-190">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="bf4fa-191">通常，代码的结构保证了呈现组件时存在对象。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-191">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="bf4fa-192">在这些情况下，不需要检查呈现逻辑中是否存在 `null`。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-192">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="bf4fa-193">在前面的示例中，由于在实例化组件时创建了 `person`，因此可保证存在 `person`。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-193">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="bf4fa-194">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="bf4fa-194">Event handlers</span></span>

<span data-ttu-id="bf4fa-195">使用以下内容创建事件处理程序时，客户端代码将触发 C# 代码调用：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-195">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="bf4fa-196">其他 `@on...` 特性</span><span class="sxs-lookup"><span data-stu-id="bf4fa-196">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="bf4fa-197">在这些情况下，事件处理程序代码可能会引发未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-197">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="bf4fa-198">如果事件处理程序引发未经处理的异常（例如数据库查询失败），则该异常对于 Blazor 服务器线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-198">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="bf4fa-199">如果应用调用可能因外部原因而失败的代码，请使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-199">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="bf4fa-200">如果用户代码不会捕获和处理异常，则框架将记录异常并终止线路。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-200">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="bf4fa-201">组件处置</span><span class="sxs-lookup"><span data-stu-id="bf4fa-201">Component disposal</span></span>

<span data-ttu-id="bf4fa-202">例如，可从 UI 中删除组件，因为用户已导航到其他页面。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-202">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="bf4fa-203">当从 UI 中删除实现 <xref:System.IDisposable?displayProperty=fullName> 的组件时，框架将调用该组件的 <xref:System.IDisposable.Dispose%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-203">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="bf4fa-204">如果组件的 `Dispose` 方法引发未经处理的异常，则该异常对于 Blazor 服务器线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-204">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="bf4fa-205">如果处置逻辑可能引发异常，应用应使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-205">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="bf4fa-206">要详细了解组件处置，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-206">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="bf4fa-207">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="bf4fa-207">JavaScript interop</span></span>

<span data-ttu-id="bf4fa-208"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允许 .NET 代码在用户浏览器中对 JavaScript 运行时进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-208"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="bf4fa-209">以下条件适用于带有 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的错误处理：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-209">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="bf4fa-210">如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行同步调用，则会发生 .NET 异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-210">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="bf4fa-211">例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，因为不能序列化提供的自变量。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-211">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="bf4fa-212">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-212">Developer code must catch the exception.</span></span> <span data-ttu-id="bf4fa-213">如果事件处理程序或组件生命周期方法中的应用代码未处理异常，则该异常对于 Blazor 服务器线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-213">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="bf4fa-214">如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行异步调用，则 .NET <xref:System.Threading.Tasks.Task> 会失败。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-214">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="bf4fa-215">例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，这是因为 JavaScript 端代码会引发异常或返回完成状态为 `rejected` 的 `Promise`。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-215">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="bf4fa-216">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-216">Developer code must catch the exception.</span></span> <span data-ttu-id="bf4fa-217">如果使用 [await](/dotnet/csharp/language-reference/keywords/await) 运算符，请考虑使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句包装方法调用，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-217">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="bf4fa-218">否则，失败的代码会导致未经处理的异常，这对于 Blazor 服务器线路来说是严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-218">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="bf4fa-219">默认情况下，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用必须在特定时间段内完成，否则调用会超时。默认超时期限为一分钟。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-219">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="bf4fa-220">超时会保护代码免受网络连接丢失的影响，或者保护永远不会发回完成消息的 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-220">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="bf4fa-221">如果调用超时，则生成的 <xref:System.Threading.Tasks> 将失败，并出现 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-221">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="bf4fa-222">捕获异常，并进行异常处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-222">Trap and process the exception with logging.</span></span>

<span data-ttu-id="bf4fa-223">同样，JavaScript 代码可以对 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)](xref:blazor/call-dotnet-from-javascript) 特性指明的 .NET 方法发起调用。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-223">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="bf4fa-224">如果这些 .NET 方法引发未经处理的异常：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-224">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="bf4fa-225">此异常不会被视为 Blazor 服务器线路的严重异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-225">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="bf4fa-226">JavaScript 端 `Promise` 会被拒绝。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-226">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="bf4fa-227">可选择在方法调用的 .NET 端或 JavaScript 端使用错误处理代码。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-227">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="bf4fa-228">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-228">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Blazor<span data-ttu-id="bf4fa-229"> 服务器预呈现</span><span class="sxs-lookup"><span data-stu-id="bf4fa-229"> Server prerendering</span></span>

Blazor<span data-ttu-id="bf4fa-230"> 组件可使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)进行预呈现，以便在用户的初始 HTTP 请求过程中返回其呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-230"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="bf4fa-231">实现方式如下：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-231">This works by:</span></span>

* <span data-ttu-id="bf4fa-232">为属于同一页面的所有预呈现组件创建新的线路。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-232">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="bf4fa-233">生成初始 HTML。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-233">Generating the initial HTML.</span></span>
* <span data-ttu-id="bf4fa-234">将线路视为 `disconnected`，直到用户浏览器与同一服务器重新建立起 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-234">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="bf4fa-235">建立该连接后，将恢复线路的交互性，并更新组件的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-235">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="bf4fa-236">如果任何组件在预呈现期间引发未经处理的异常，例如在生命周期方法或呈现逻辑中：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-236">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="bf4fa-237">则该异常对线路是严重的。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-237">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="bf4fa-238">此异常将从 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序中的调用堆栈引发。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-238">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="bf4fa-239">这将导致整个 HTTP 请求失败，除非开发人员代码显式捕获该异常。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-239">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="bf4fa-240">在正常情况下，如果预呈现失败，则继续生成和呈现组件都将没有作用，因为无法呈现工作组件。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-240">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="bf4fa-241">若要容许在预呈现期间可能发生的错误，必须将错误处理逻辑置于可能引发异常的组件中。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-241">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="bf4fa-242">请使用 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句，并进行错误处理和日志记录。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-242">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="bf4fa-243">请勿将 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序包装在 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句中，而是将错误处理逻辑置于由 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序呈现的组件中。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-243">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="bf4fa-244">高级方案</span><span class="sxs-lookup"><span data-stu-id="bf4fa-244">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="bf4fa-245">递归呈现</span><span class="sxs-lookup"><span data-stu-id="bf4fa-245">Recursive rendering</span></span>

<span data-ttu-id="bf4fa-246">组件能以递归方式嵌套。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-246">Components can be nested recursively.</span></span> <span data-ttu-id="bf4fa-247">这适用于表示递归数据结构。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-247">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="bf4fa-248">例如，`TreeNode` 组件可以为节点的每个子级呈现更多 `TreeNode` 组件。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-248">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="bf4fa-249">以递归方式呈现时，请避免采用会导致无限递归的编码模式：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-249">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="bf4fa-250">请勿以递归方式呈现包含循环的数据结构。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-250">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="bf4fa-251">例如，请勿呈现其子级包含其自身的树节点。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-251">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="bf4fa-252">请勿创建包含循环的布局链。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-252">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="bf4fa-253">例如，请勿创建布局为其本身的布局。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-253">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="bf4fa-254">请勿允许最终用户通过恶意数据输入或 JavaScript 互操作调用违反递归固定协定（规则）。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-254">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="bf4fa-255">呈现过程中的无限循环：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-255">Infinite loops during rendering:</span></span>

* <span data-ttu-id="bf4fa-256">会导致呈现过程永久地继续下去。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-256">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="bf4fa-257">相当于创建不终止的循环。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-257">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="bf4fa-258">在这些情况下，受影响的 Blazor 服务器线路会失败，并且该线程通常会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-258">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="bf4fa-259">在操作系统允许范围内无限期地消耗 CPU 时间。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-259">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="bf4fa-260">消耗不限量的服务器内存。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-260">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="bf4fa-261">消耗不限量的内存相当于不终止的循环在每次迭代时向集合添加条目的情况。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-261">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="bf4fa-262">若要避免无限递归模式，请确保递归呈现代码包含合适的停止条件。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-262">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="bf4fa-263">自定义呈现器树逻辑</span><span class="sxs-lookup"><span data-stu-id="bf4fa-263">Custom render tree logic</span></span>

<span data-ttu-id="bf4fa-264">大多数 Blazor 组件都实现为 .razor 文件，并经过编译以生成在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 上运行的逻辑，目的是呈现其输出。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-264">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="bf4fa-265">开发人员可使用程序 C# 代码手动实现 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-265">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="bf4fa-266">有关详细信息，请参阅 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-266">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="bf4fa-267">手动呈现树生成器逻辑被视为一种高级且不安全的方案，不建议开发人员在常规组件开发工作中采用。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-267">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="bf4fa-268">如果编写 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 代码，开发人员必须保证代码的正确性。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-268">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="bf4fa-269">例如，开发人员必须确保：</span><span class="sxs-lookup"><span data-stu-id="bf4fa-269">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="bf4fa-270">对 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的调用已正确均衡。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-270">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="bf4fa-271">仅将特性添加到正确的位置。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-271">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="bf4fa-272">若手动呈现树生成器逻辑不正确，则可能出现任意未定义的行为（包括崩溃和服务器挂起）以及安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-272">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="bf4fa-273">请知悉：手动呈现树生成器逻辑的复杂程度和危险程度与手动编写程序集代码或 MSIL 指令是一样的。</span><span class="sxs-lookup"><span data-stu-id="bf4fa-273">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
