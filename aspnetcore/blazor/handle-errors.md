---
title: 处理 ASP.NET Core Blazor 应用中的错误
author: guardrex
description: 了解 ASP.NET Core 如何 Blazor 如何 Blazor 管理未经处理的异常以及如何开发检测并处理错误的应用程序。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: b987513e5410e95ab632b9935d858b648838d94f
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928273"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="0b7ff-103">处理 ASP.NET Core Blazor 应用中的错误</span><span class="sxs-lookup"><span data-stu-id="0b7ff-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="0b7ff-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="0b7ff-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="0b7ff-105">本文介绍 Blazor 如何管理未经处理的异常以及如何开发检测和处理错误的应用。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="0b7ff-106">开发过程中的详细错误</span><span class="sxs-lookup"><span data-stu-id="0b7ff-106">Detailed errors during development</span></span>

<span data-ttu-id="0b7ff-107">当 Blazor 应用在开发过程中运行不正常时，从该应用接收详细的错误信息有助于故障排除和修复问题。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="0b7ff-108">出现错误时，Blazor 应用会在屏幕底部显示一个黄色条框：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="0b7ff-109">在开发过程中，黄色条框会将你定向到浏览器控制台，你可在其中查看异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="0b7ff-110">在生产过程中，黄色条框会通知用户发生了错误，并建议刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="0b7ff-111">此错误处理体验的 UI 是 Blazor 项目模板的一部分。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="0b7ff-112">在 Blazor WebAssembly 应用程序中，自定义*wwwroot/index.html*文件中的体验：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="0b7ff-113">在 Blazor Server 应用程序中，自定义*Pages/_Host*文件中的体验：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="0b7ff-114">`blazor-error-ui` 元素被 Blazor 模板附带的样式隐藏，然后在发生错误时显示。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-114">The `blazor-error-ui` element is hidden by the styles included with the Blazor templates and then shown when an error occurs.</span></span>

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="0b7ff-115">Blazor Server 应用如何响应未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="0b7ff-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="0b7ff-116"> Server 是有状态框架。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-116"> Server is a stateful framework.</span></span> <span data-ttu-id="0b7ff-117">当用户与应用交互时，它们会保持与服务器（称为*线路*）的连接。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="0b7ff-118">线路包含活动组件实例，以及状态的许多其他方面，例如：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="0b7ff-119">最新呈现的组件输出。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="0b7ff-120">客户端事件可触发的事件处理委托的当前集合。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="0b7ff-121">如果用户在多个浏览器选项卡中打开应用程序，则它们具有多个独立的线路。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="0b7ff-122"> 将最未经处理的异常视为致命的异常，并将其出现在线路上。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="0b7ff-123">如果线路由于未经处理的异常而终止，则用户只可以通过重新加载页面来创建新线路，从而继续与应用进行交互。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="0b7ff-124">已终止的线路（其他用户或其他浏览器选项卡的线路）不会受到影响。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="0b7ff-125">这种情况类似于桌面应用程序崩溃&mdash;崩溃的应用程序必须重新启动，但其他应用不受影响。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="0b7ff-126">当发生未处理的异常时，线路会终止，原因如下：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="0b7ff-127">未经处理的异常通常使线路处于未定义状态。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="0b7ff-128">无法在未处理的异常后确保应用的正常操作。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="0b7ff-129">如果线路继续存在，则可能会在应用程序中出现安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="0b7ff-130">在开发人员代码中管理未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="0b7ff-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="0b7ff-131">若要使应用在出现错误后继续操作，应用必须具有错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="0b7ff-132">本文后面的部分将介绍未经处理的异常的潜在来源。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="0b7ff-133">在生产环境中，不要在 UI 中呈现框架异常消息或堆栈跟踪。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="0b7ff-134">呈现异常消息或堆栈跟踪可以：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="0b7ff-135">向最终用户公开敏感信息。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="0b7ff-136">帮助恶意用户发现应用程序中可能会危及应用程序、服务器或网络安全的弱点。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="0b7ff-137">使用永久性提供程序记录错误</span><span class="sxs-lookup"><span data-stu-id="0b7ff-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="0b7ff-138">如果发生未处理的异常，则会将异常记录到在服务容器中配置 <xref:Microsoft.Extensions.Logging.ILogger> 实例。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="0b7ff-139">默认情况下，使用控制台日志记录提供程序 Blazor 应用日志输出到控制台输出。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="0b7ff-140">请考虑使用管理日志大小和日志轮换的提供程序，将日志记录到更永久性的位置。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="0b7ff-141">有关更多信息，请参见<xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="0b7ff-142">在开发过程中，Blazor 通常会将异常的完整详细信息发送到浏览器的控制台，以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="0b7ff-143">在生产环境中，默认情况下禁用浏览器控制台中的详细错误，这意味着不会将错误发送到客户端，但异常的完整详细信息仍记录在服务器端。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="0b7ff-144">有关更多信息，请参见<xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="0b7ff-145">您必须确定要记录的事件以及记录事件的严重性级别。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="0b7ff-146">恶意用户可能会特意触发错误。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="0b7ff-147">例如，请勿记录一个错误，其中显示产品详细信息的组件 URL 中提供了未知 `ProductId`。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="0b7ff-148">不是所有的错误都应视为日志记录的高严重性事件。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="0b7ff-149">可能发生错误的位置</span><span class="sxs-lookup"><span data-stu-id="0b7ff-149">Places where errors may occur</span></span>

<span data-ttu-id="0b7ff-150">框架和应用代码可能会在以下任何位置触发未经处理的异常：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="0b7ff-151">组件实例化</span><span class="sxs-lookup"><span data-stu-id="0b7ff-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="0b7ff-152">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="0b7ff-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="0b7ff-153">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="0b7ff-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="0b7ff-154">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="0b7ff-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="0b7ff-155">组件处置</span><span class="sxs-lookup"><span data-stu-id="0b7ff-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="0b7ff-156">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="0b7ff-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="0b7ff-157">[Blazor Server 线路处理程序](#blazor-server-circuit-handlers)</span><span class="sxs-lookup"><span data-stu-id="0b7ff-157">[Blazor Server circuit handlers](#blazor-server-circuit-handlers)</span></span>
* <span data-ttu-id="0b7ff-158">[服务器线路处置 Blazor](#blazor-server-circuit-disposal)</span><span class="sxs-lookup"><span data-stu-id="0b7ff-158">[Blazor Server circuit disposal](#blazor-server-circuit-disposal)</span></span>
* <span data-ttu-id="0b7ff-159">[Blazor Server rerendering](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="0b7ff-159">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="0b7ff-160">本文的以下部分介绍了前面未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="0b7ff-161">组件实例化</span><span class="sxs-lookup"><span data-stu-id="0b7ff-161">Component instantiation</span></span>

<span data-ttu-id="0b7ff-162">当 Blazor 创建组件的实例时：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="0b7ff-163">调用组件的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="0b7ff-164">将调用通过[`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component)指令或[`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component)属性提供给组件的构造函数的任何非单一服务器 DI 服务的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="0b7ff-165">如果任何已执行的构造函数或任何 `[Inject]` 属性的 setter 引发了未处理的异常，则 Blazor 服务器线路会失败。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="0b7ff-166">异常是致命的，因为框架无法实例化组件。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="0b7ff-167">如果构造函数逻辑可能引发异常，应用应使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="0b7ff-168">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="0b7ff-168">Lifecycle methods</span></span>

<span data-ttu-id="0b7ff-169">在组件的生存期内，Blazor 调用以下[生命周期方法](xref:blazor/lifecycle)：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="0b7ff-170">如果任何生命周期方法以同步或异步方式引发异常，则该异常对于 Blazor 服务器线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="0b7ff-171">若要使组件处理生命周期方法中的错误，请添加错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="0b7ff-172">在下面的示例中，`OnParametersSetAsync` 调用方法来获取产品：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="0b7ff-173">`ProductRepository.GetProductByIdAsync` 方法中引发的异常由 `try-catch` 语句处理。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="0b7ff-174">执行 `catch` 块时：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="0b7ff-175">`loadFailed` 设置为 `true`，用于向用户显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="0b7ff-176">记录错误。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="0b7ff-177">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="0b7ff-177">Rendering logic</span></span>

<span data-ttu-id="0b7ff-178">`.razor` 组件文件中的声明性标记被编译到称为C# `BuildRenderTree`的方法中。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="0b7ff-179">当组件呈现时，`BuildRenderTree` 执行并生成一个数据结构，该结构描述所呈现组件的元素、文本和子组件。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="0b7ff-180">呈现逻辑可能引发异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="0b7ff-181">在计算 `@someObject.PropertyName` 但 `@someObject` `null`时，会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="0b7ff-182">呈现逻辑引发的未经处理的异常对于 Blazor 服务器线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="0b7ff-183">若要防止呈现逻辑中出现空引用异常，请在访问其成员之前检查 `null` 对象。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="0b7ff-184">在以下示例中，如果 `person.Address` `null`，则不会访问 `person.Address` 属性：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="0b7ff-185">前面的代码假定 `person` 不 `null`。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="0b7ff-186">通常，代码的结构保证在呈现组件时存在对象。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="0b7ff-187">在这些情况下，不需要检查呈现逻辑中的 `null`。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="0b7ff-188">在前面的示例中，可以保证存在 `person` 因为在实例化组件时创建 `person`。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="0b7ff-189">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="0b7ff-189">Event handlers</span></span>

<span data-ttu-id="0b7ff-190">使用以下代码创建事件处理程序C#时，客户端代码将触发代码调用：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="0b7ff-191">其他 `@on...` 特性</span><span class="sxs-lookup"><span data-stu-id="0b7ff-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="0b7ff-192">在这些情况下，事件处理程序代码可能会引发未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="0b7ff-193">如果事件处理程序引发未经处理的异常（例如，数据库查询失败），则异常对于 Blazor 服务器线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="0b7ff-194">如果应用调用可能因外部原因而失败的代码，则使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="0b7ff-195">如果用户代码不会捕获并处理异常，则框架将记录异常并终止线路。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="0b7ff-196">组件处置</span><span class="sxs-lookup"><span data-stu-id="0b7ff-196">Component disposal</span></span>

<span data-ttu-id="0b7ff-197">例如，可以从 UI 中删除组件，因为用户已导航到另一个页面。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="0b7ff-198">当从 UI 中删除实现 <xref:System.IDisposable?displayProperty=fullName> 的组件时，框架将调用该组件的 <xref:System.IDisposable.Dispose*> 方法。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="0b7ff-199">如果组件的 `Dispose` 方法引发未处理的异常，则该异常对于 Blazor 服务器线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="0b7ff-200">如果处理逻辑可能引发异常，应用应使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="0b7ff-201">有关组件处置的详细信息，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="0b7ff-202">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="0b7ff-202">JavaScript interop</span></span>

<span data-ttu-id="0b7ff-203">`IJSRuntime.InvokeAsync<T>` 允许 .NET 代码对用户浏览器中的 JavaScript 运行时进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="0b7ff-204">以下条件适用于带有 `InvokeAsync<T>`的错误处理：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="0b7ff-205">如果对 `InvokeAsync<T>` 的调用同步失败，则会发生 .NET 异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="0b7ff-206">例如，对 `InvokeAsync<T>` 的调用可能会失败，因为不能序列化提供的自变量。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="0b7ff-207">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-207">Developer code must catch the exception.</span></span> <span data-ttu-id="0b7ff-208">如果事件处理程序或组件生命周期方法中的应用代码未处理异常，则生成的异常对于 Blazor 服务器线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="0b7ff-209">如果对 `InvokeAsync<T>` 的调用异步失败，则 .NET <xref:System.Threading.Tasks.Task> 会失败。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="0b7ff-210">例如，对 `InvokeAsync<T>` 的调用可能会失败，这是因为 JavaScript 端代码引发异常或返回以 `rejected`完成的 `Promise`。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="0b7ff-211">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-211">Developer code must catch the exception.</span></span> <span data-ttu-id="0b7ff-212">如果使用[await](/dotnet/csharp/language-reference/keywords/await)运算符，请考虑在包含错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中包装方法调用。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="0b7ff-213">否则，失败的代码会导致未处理的异常，这是 Blazor 服务器线路的严重错误。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="0b7ff-214">默认情况下，对 `InvokeAsync<T>` 的调用必须在特定时间段内完成，否则调用会超时。默认超时期限为一分钟。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="0b7ff-215">超时可防止代码丢失网络连接或从不发送回完成消息的 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="0b7ff-216">如果调用超时，则生成的 `Task` 将失败，并出现 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="0b7ff-217">捕获并处理日志记录的异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="0b7ff-218">同样，JavaScript 代码可能会启动对[`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)属性所指示的 .net 方法的调用。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) attribute.</span></span> <span data-ttu-id="0b7ff-219">如果这些 .NET 方法引发未经处理的异常：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="0b7ff-220">此异常不会被视为 Blazor 服务器线路的严重。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="0b7ff-221">JavaScript 端 `Promise` 被拒绝。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="0b7ff-222">您可以选择在 .NET 端或方法调用的 JavaScript 端使用错误处理代码。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="0b7ff-223">有关更多信息，请参见<xref:blazor/javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-223">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="opno-locblazor-server-circuit-handlers"></a>Blazor<span data-ttu-id="0b7ff-224"> Server 线路处理程序</span><span class="sxs-lookup"><span data-stu-id="0b7ff-224"> Server circuit handlers</span></span>

Blazor<span data-ttu-id="0b7ff-225"> Server 允许代码定义*线路处理程序，该处理程序*允许在用户线路的状态发生更改时运行代码。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-225"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="0b7ff-226">线路处理程序是通过从 `CircuitHandler` 派生并在应用程序的服务容器中注册该类来实现的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-226">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="0b7ff-227">以下线路处理程序示例将跟踪打开的 SignalR 连接：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-227">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="0b7ff-228">使用 DI 注册线路处理程序。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-228">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="0b7ff-229">为每个线路实例创建范围内的实例。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-229">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="0b7ff-230">使用前面示例中的 `TrackingCircuitHandler`，将创建一个单一实例服务，因为必须跟踪所有线路的状态：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-230">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="0b7ff-231">如果自定义线路处理程序的方法引发未经处理的异常，则该异常对于 Blazor 服务器线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-231">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="0b7ff-232">若要容忍处理程序代码或调用方法中的异常，请使用错误处理和日志记录将代码包装在一个或多个[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-232">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="opno-locblazor-server-circuit-disposal"></a><span data-ttu-id="0b7ff-233">服务器线路处置 Blazor</span><span class="sxs-lookup"><span data-stu-id="0b7ff-233">Blazor Server circuit disposal</span></span>

<span data-ttu-id="0b7ff-234">当某个线路由于用户已断开连接并且该框架正在清理线路状态而结束时，框架会释放该线路的 DI 范围。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-234">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="0b7ff-235">释放作用域将释放任何实现 <xref:System.IDisposable?displayProperty=fullName>的线路范围的 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-235">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="0b7ff-236">如果任何 DI 服务在处理过程中引发未经处理的异常，则框架将记录该异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-236">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="0b7ff-237"> Server 预呈现</span><span class="sxs-lookup"><span data-stu-id="0b7ff-237"> Server prerendering</span></span>

<span data-ttu-id="0b7ff-238">使用 `Component` 标记帮助器可以预呈现 Blazor 组件，以便在用户初始 HTTP 请求过程中返回其呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-238">Blazor components can be prerendered using the `Component` Tag Helper so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="0b7ff-239">此功能的工作方式如下：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-239">This works by:</span></span>

* <span data-ttu-id="0b7ff-240">为属于同一页面的所有预呈现组件创建新线路。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-240">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="0b7ff-241">正在生成初始 HTML。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-241">Generating the initial HTML.</span></span>
* <span data-ttu-id="0b7ff-242">将线路视为 `disconnected`，直到用户的浏览器将 SignalR 连接回同一服务器。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-242">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="0b7ff-243">建立连接后，将恢复对线路的交互，并更新组件的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-243">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="0b7ff-244">如果任何组件在预呈现期间引发未经处理的异常，例如，在生命周期方法或呈现逻辑中：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-244">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="0b7ff-245">此异常是线路致命的。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-245">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="0b7ff-246">此异常将从 `Component` 标记帮助程序中的调用堆栈引发。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-246">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="0b7ff-247">因此，整个 HTTP 请求将失败，除非开发人员代码显式捕获了异常。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-247">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="0b7ff-248">在正常情况下，如果预呈现失败，则继续生成并呈现组件没有意义，因为无法呈现工作组件。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-248">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="0b7ff-249">若要容忍在预呈现期间可能发生的错误，必须将错误处理逻辑放置在可能引发异常的组件中。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-249">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="0b7ff-250">使用带有错误处理和日志记录的[try catch](/dotnet/csharp/language-reference/keywords/try-catch)语句。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-250">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="0b7ff-251">不要将 `Component` 标记帮助程序包装在 `try-catch` 语句中，而是将错误处理逻辑放在由 `Component` 标记帮助器呈现的组件中。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-251">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="0b7ff-252">高级方案</span><span class="sxs-lookup"><span data-stu-id="0b7ff-252">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="0b7ff-253">递归呈现</span><span class="sxs-lookup"><span data-stu-id="0b7ff-253">Recursive rendering</span></span>

<span data-ttu-id="0b7ff-254">组件可以递归嵌套。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-254">Components can be nested recursively.</span></span> <span data-ttu-id="0b7ff-255">这适用于表示递归数据结构。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-255">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="0b7ff-256">例如，`TreeNode` 组件可以为节点的每个子元素呈现更多 `TreeNode` 组件。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-256">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="0b7ff-257">以递归方式呈现时，请避免将导致无限递归的编码模式：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-257">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="0b7ff-258">不以递归方式呈现包含循环的数据结构。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-258">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="0b7ff-259">例如，不呈现其子级包含其自身的树节点。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-259">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="0b7ff-260">不要创建包含循环的布局链。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-260">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="0b7ff-261">例如，请勿创建布局本身为的布局。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-261">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="0b7ff-262">不允许最终用户通过恶意数据输入或 JavaScript 互操作调用来违反递归固定条件（规则）。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-262">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="0b7ff-263">呈现过程中的无限循环：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-263">Infinite loops during rendering:</span></span>

* <span data-ttu-id="0b7ff-264">导致呈现过程永久继续。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-264">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="0b7ff-265">等效于创建未终止的循环。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-265">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="0b7ff-266">在这些情况下，受影响的 Blazor 服务器线路会失败，并且该线程通常会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-266">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="0b7ff-267">无限期地消耗操作系统允许的 CPU 时间。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-267">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="0b7ff-268">消耗不限数量的服务器内存。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-268">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="0b7ff-269">使用无限制内存等效于未终止的循环在每次迭代时向集合添加项的情况。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-269">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="0b7ff-270">若要避免无限递归模式，请确保递归呈现代码包含合适的停止条件。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-270">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="0b7ff-271">自定义呈现器树根逻辑</span><span class="sxs-lookup"><span data-stu-id="0b7ff-271">Custom render tree logic</span></span>

<span data-ttu-id="0b7ff-272">大多数 Blazor 组件都作为*razor*文件实现，并且编译为生成可对 `RenderTreeBuilder` 进行操作以呈现其输出的逻辑。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-272">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="0b7ff-273">开发人员可以使用过程C#代码手动实现 `RenderTreeBuilder` 逻辑。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-273">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="0b7ff-274">有关更多信息，请参见<xref:blazor/components#manual-rendertreebuilder-logic>。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-274">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="0b7ff-275">使用手动渲染树生成器逻辑被视为一种高级不安全的方案，不建议用于常规组件开发。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-275">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="0b7ff-276">如果编写 `RenderTreeBuilder` 代码，开发人员必须保证代码的正确性。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-276">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="0b7ff-277">例如，开发人员必须确保：</span><span class="sxs-lookup"><span data-stu-id="0b7ff-277">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="0b7ff-278">对 `OpenElement` 和 `CloseElement` 的调用已正确平衡。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-278">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="0b7ff-279">特性仅添加到正确的位置。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-279">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="0b7ff-280">手动呈现树生成器逻辑不正确可能会导致任意未定义的行为，包括崩溃、服务器挂起和安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-280">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="0b7ff-281">请考虑在相同程度的复杂性上手动呈现树生成器逻辑，并使用与手动编写程序集代码或 MSIL 指令相同的*危险*级别。</span><span class="sxs-lookup"><span data-stu-id="0b7ff-281">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
