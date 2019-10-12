---
title: 处理 ASP.NET Core Blazor 应用中的错误
author: guardrex
description: 了解 ASP.NET Core 如何 Blazor Blazor 如何管理未经处理的异常以及如何开发检测并处理错误的应用程序。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/handle-errors
ms.openlocfilehash: fb4c7cacfe8be2417d6009cfc722595d0d91d530
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288833"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="0636f-103">处理 ASP.NET Core Blazor 应用中的错误</span><span class="sxs-lookup"><span data-stu-id="0636f-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="0636f-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="0636f-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="0636f-105">本文介绍 Blazor 如何管理未经处理的异常以及如何开发检测和处理错误的应用。</span><span class="sxs-lookup"><span data-stu-id="0636f-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a><span data-ttu-id="0636f-106">Blazor 框架如何响应未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="0636f-106">How the Blazor framework reacts to unhandled exceptions</span></span>

<span data-ttu-id="0636f-107">Blazor 服务器是有状态框架。</span><span class="sxs-lookup"><span data-stu-id="0636f-107">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="0636f-108">当用户与应用交互时，它们会保持与服务器（称为*线路*）的连接。</span><span class="sxs-lookup"><span data-stu-id="0636f-108">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="0636f-109">线路包含活动组件实例，以及状态的许多其他方面，例如：</span><span class="sxs-lookup"><span data-stu-id="0636f-109">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="0636f-110">最新呈现的组件输出。</span><span class="sxs-lookup"><span data-stu-id="0636f-110">The most recent rendered output of components.</span></span>
* <span data-ttu-id="0636f-111">客户端事件可触发的事件处理委托的当前集合。</span><span class="sxs-lookup"><span data-stu-id="0636f-111">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="0636f-112">如果用户在多个浏览器选项卡中打开应用程序，则它们具有多个独立的线路。</span><span class="sxs-lookup"><span data-stu-id="0636f-112">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="0636f-113">Blazor 将大多数未经处理的异常视为致命的异常，以使其发生。</span><span class="sxs-lookup"><span data-stu-id="0636f-113">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="0636f-114">如果线路由于未经处理的异常而终止，则用户只可以通过重新加载页面来创建新线路，从而继续与应用进行交互。</span><span class="sxs-lookup"><span data-stu-id="0636f-114">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="0636f-115">已终止的线路（其他用户或其他浏览器选项卡的线路）不会受到影响。</span><span class="sxs-lookup"><span data-stu-id="0636f-115">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="0636f-116">此方案类似于桌面应用程序，该应用程序必须重新启动崩溃 @ no__t-0the 崩溃的应用程序，但其他应用不会受到影响。</span><span class="sxs-lookup"><span data-stu-id="0636f-116">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="0636f-117">当发生未处理的异常时，线路会终止，原因如下：</span><span class="sxs-lookup"><span data-stu-id="0636f-117">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="0636f-118">未经处理的异常通常使线路处于未定义状态。</span><span class="sxs-lookup"><span data-stu-id="0636f-118">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="0636f-119">无法在未处理的异常后确保应用的正常操作。</span><span class="sxs-lookup"><span data-stu-id="0636f-119">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="0636f-120">如果线路继续存在，则可能会在应用程序中出现安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="0636f-120">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="0636f-121">在开发人员代码中管理未经处理的异常</span><span class="sxs-lookup"><span data-stu-id="0636f-121">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="0636f-122">若要使应用在出现错误后继续操作，应用必须具有错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="0636f-122">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="0636f-123">本文后面的部分将介绍未经处理的异常的潜在来源。</span><span class="sxs-lookup"><span data-stu-id="0636f-123">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="0636f-124">在生产环境中，不要在 UI 中呈现框架异常消息或堆栈跟踪。</span><span class="sxs-lookup"><span data-stu-id="0636f-124">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="0636f-125">呈现异常消息或堆栈跟踪可以：</span><span class="sxs-lookup"><span data-stu-id="0636f-125">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="0636f-126">向最终用户公开敏感信息。</span><span class="sxs-lookup"><span data-stu-id="0636f-126">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="0636f-127">帮助恶意用户发现应用程序中可能会危及应用程序、服务器或网络安全的弱点。</span><span class="sxs-lookup"><span data-stu-id="0636f-127">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="0636f-128">使用永久性提供程序记录错误</span><span class="sxs-lookup"><span data-stu-id="0636f-128">Log errors with a persistent provider</span></span>

<span data-ttu-id="0636f-129">如果发生未处理的异常，则会将异常记录到在服务容器中配置 @no__t 0 实例。</span><span class="sxs-lookup"><span data-stu-id="0636f-129">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="0636f-130">默认情况下，Blazor apps 使用控制台日志记录提供程序登录到控制台输出。</span><span class="sxs-lookup"><span data-stu-id="0636f-130">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="0636f-131">请考虑使用管理日志大小和日志轮换的提供程序，将日志记录到更永久性的位置。</span><span class="sxs-lookup"><span data-stu-id="0636f-131">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="0636f-132">有关详细信息，请参阅 <xref:fundamentals/logging/index> 。</span><span class="sxs-lookup"><span data-stu-id="0636f-132">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="0636f-133">在开发过程中，Blazor 通常会将异常的完整详细信息发送到浏览器的控制台，以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="0636f-133">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="0636f-134">在生产环境中，默认情况下禁用浏览器控制台中的详细错误，这意味着不会将错误发送到客户端，但异常的完整详细信息仍记录在服务器端。</span><span class="sxs-lookup"><span data-stu-id="0636f-134">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="0636f-135">有关详细信息，请参阅 <xref:fundamentals/error-handling> 。</span><span class="sxs-lookup"><span data-stu-id="0636f-135">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="0636f-136">您必须确定要记录的事件以及记录事件的严重性级别。</span><span class="sxs-lookup"><span data-stu-id="0636f-136">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="0636f-137">恶意用户可能会特意触发错误。</span><span class="sxs-lookup"><span data-stu-id="0636f-137">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="0636f-138">例如，不要记录某个错误中的事件，其中显示产品详细信息的组件的 URL 中提供了未知的 `ProductId`。</span><span class="sxs-lookup"><span data-stu-id="0636f-138">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="0636f-139">不是所有的错误都应视为日志记录的高严重性事件。</span><span class="sxs-lookup"><span data-stu-id="0636f-139">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="0636f-140">可能发生错误的位置</span><span class="sxs-lookup"><span data-stu-id="0636f-140">Places where errors may occur</span></span>

<span data-ttu-id="0636f-141">框架和应用代码可能会在以下任何位置触发未经处理的异常：</span><span class="sxs-lookup"><span data-stu-id="0636f-141">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="0636f-142">组件实例化</span><span class="sxs-lookup"><span data-stu-id="0636f-142">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="0636f-143">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="0636f-143">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="0636f-144">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="0636f-144">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="0636f-145">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="0636f-145">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="0636f-146">组件处置</span><span class="sxs-lookup"><span data-stu-id="0636f-146">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="0636f-147">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="0636f-147">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="0636f-148">线路处理程序</span><span class="sxs-lookup"><span data-stu-id="0636f-148">Circuit handlers</span></span>](#circuit-handlers)
* [<span data-ttu-id="0636f-149">线路处置</span><span class="sxs-lookup"><span data-stu-id="0636f-149">Circuit disposal</span></span>](#circuit-disposal)
* [<span data-ttu-id="0636f-150">呈现</span><span class="sxs-lookup"><span data-stu-id="0636f-150">Prerendering</span></span>](#prerendering)

<span data-ttu-id="0636f-151">本文的以下部分介绍了前面未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-151">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="0636f-152">组件实例化</span><span class="sxs-lookup"><span data-stu-id="0636f-152">Component instantiation</span></span>

<span data-ttu-id="0636f-153">当 Blazor 创建组件的实例时：</span><span class="sxs-lookup"><span data-stu-id="0636f-153">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="0636f-154">调用组件的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0636f-154">The component's constructor is invoked.</span></span>
* <span data-ttu-id="0636f-155">将调用通过[@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)指令或[[注入]](xref:blazor/dependency-injection#request-a-service-in-a-component)特性提供给组件的构造函数的任何非单一服务器 DI 服务的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0636f-155">The constructors of any non-singleton DI services supplied to the component's constructor via the [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [[Inject]](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span> 

<span data-ttu-id="0636f-156">任何已执行的构造函数或任何 `[Inject]` 属性的 setter 都将引发未经处理的异常时，线路会失败。</span><span class="sxs-lookup"><span data-stu-id="0636f-156">A circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="0636f-157">异常是致命的，因为框架无法实例化组件。</span><span class="sxs-lookup"><span data-stu-id="0636f-157">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="0636f-158">如果构造函数逻辑可能引发异常，应用应使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-158">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="0636f-159">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="0636f-159">Lifecycle methods</span></span>

<span data-ttu-id="0636f-160">在组件的生存期内，Blazor 调用生命周期方法：</span><span class="sxs-lookup"><span data-stu-id="0636f-160">During the lifetime of a component, Blazor invokes lifecycle methods:</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="0636f-161">如果任何生命周期方法以同步或异步方式引发异常，则该异常对于线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0636f-161">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to the circuit.</span></span> <span data-ttu-id="0636f-162">若要使组件处理生命周期方法中的错误，请添加错误处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="0636f-162">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="0636f-163">在下面的示例中，`OnParametersSetAsync` 调用方法来获取产品：</span><span class="sxs-lookup"><span data-stu-id="0636f-163">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="0636f-164">@No__t-0 方法中引发的异常由 @no__t 的语句处理。</span><span class="sxs-lookup"><span data-stu-id="0636f-164">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="0636f-165">当执行 `catch` 块时：</span><span class="sxs-lookup"><span data-stu-id="0636f-165">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="0636f-166">@no__t 设置为 `true`，用于向用户显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="0636f-166">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="0636f-167">记录错误。</span><span class="sxs-lookup"><span data-stu-id="0636f-167">The error is logged.</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="0636f-168">呈现逻辑</span><span class="sxs-lookup"><span data-stu-id="0636f-168">Rendering logic</span></span>

<span data-ttu-id="0636f-169">@No__t-0 组件文件中的声明性标记被编译到称为C# @no__t 的方法中。</span><span class="sxs-lookup"><span data-stu-id="0636f-169">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="0636f-170">呈现组件时，@no__t 执行并生成一个数据结构，该结构描述所呈现组件的元素、文本和子组件。</span><span class="sxs-lookup"><span data-stu-id="0636f-170">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="0636f-171">呈现逻辑可能引发异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-171">Rendering logic can throw an exception.</span></span> <span data-ttu-id="0636f-172">这种情况的一个示例是在计算 `@someObject.PropertyName` 但 `@someObject` @no__t 为-2 时出现。</span><span class="sxs-lookup"><span data-stu-id="0636f-172">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="0636f-173">呈现逻辑引发的未经处理的异常对于线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0636f-173">An unhandled exception thrown by rendering logic is fatal to the circuit.</span></span>

<span data-ttu-id="0636f-174">若要防止呈现逻辑中出现空引用异常，请在访问其成员之前检查 `null` 对象。</span><span class="sxs-lookup"><span data-stu-id="0636f-174">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="0636f-175">在以下示例中，如果 @no__t @no__t 为-2，则不会访问 `person.Address` 属性：</span><span class="sxs-lookup"><span data-stu-id="0636f-175">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="0636f-176">前面的代码假定 `person` 不 @no__t 为-1。</span><span class="sxs-lookup"><span data-stu-id="0636f-176">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="0636f-177">通常，代码的结构保证在呈现组件时存在对象。</span><span class="sxs-lookup"><span data-stu-id="0636f-177">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="0636f-178">在这些情况下，不需要检查呈现逻辑中的 @no__t 0。</span><span class="sxs-lookup"><span data-stu-id="0636f-178">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="0636f-179">在前面的示例中，可以保证存在 `person`，因为在实例化组件时创建 @no__t。</span><span class="sxs-lookup"><span data-stu-id="0636f-179">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="0636f-180">事件处理程序</span><span class="sxs-lookup"><span data-stu-id="0636f-180">Event handlers</span></span>

<span data-ttu-id="0636f-181">使用以下代码创建事件处理程序C#时，客户端代码将触发代码调用：</span><span class="sxs-lookup"><span data-stu-id="0636f-181">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="0636f-182">其他 `@on...` 属性</span><span class="sxs-lookup"><span data-stu-id="0636f-182">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="0636f-183">在这些情况下，事件处理程序代码可能会引发未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-183">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="0636f-184">如果事件处理程序引发未经处理的异常（例如，数据库查询失败），则异常对于线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0636f-184">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to the circuit.</span></span> <span data-ttu-id="0636f-185">如果应用调用可能因外部原因而失败的代码，则使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-185">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="0636f-186">如果用户代码不会捕获并处理异常，则框架将记录异常并终止线路。</span><span class="sxs-lookup"><span data-stu-id="0636f-186">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="0636f-187">组件处置</span><span class="sxs-lookup"><span data-stu-id="0636f-187">Component disposal</span></span>

<span data-ttu-id="0636f-188">例如，可以从 UI 中删除组件，因为用户已导航到另一个页面。</span><span class="sxs-lookup"><span data-stu-id="0636f-188">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="0636f-189">当从 UI 中删除实现 <xref:System.IDisposable?displayProperty=fullName> 的组件时，框架将调用该组件的 @no__t 方法。</span><span class="sxs-lookup"><span data-stu-id="0636f-189">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span> 

<span data-ttu-id="0636f-190">如果组件的 `Dispose` 方法引发未经处理的异常，则该异常对于线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0636f-190">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="0636f-191">如果处理逻辑可能引发异常，应用应使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-191">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="0636f-192">有关组件处置的详细信息，请参阅 <xref:blazor/components#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="0636f-192">For more information on component disposal, see <xref:blazor/components#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="0636f-193">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="0636f-193">JavaScript interop</span></span>

<span data-ttu-id="0636f-194">`IJSRuntime.InvokeAsync<T>` 使 .NET 代码可以在用户浏览器中异步调用 JavaScript 运行时。</span><span class="sxs-lookup"><span data-stu-id="0636f-194">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="0636f-195">以下条件适用于与 `InvokeAsync<T>` 的错误处理：</span><span class="sxs-lookup"><span data-stu-id="0636f-195">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="0636f-196">如果对 @no__t 的调用同步失败，则会发生 .NET 异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-196">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="0636f-197">例如，对 @no__t 的调用可能会失败，因为无法对提供的参数进行序列化。</span><span class="sxs-lookup"><span data-stu-id="0636f-197">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="0636f-198">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-198">Developer code must catch the exception.</span></span> <span data-ttu-id="0636f-199">如果事件处理程序或组件生命周期方法中的应用代码未处理异常，则生成的异常对于线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0636f-199">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to the circuit.</span></span>
* <span data-ttu-id="0636f-200">如果对 @no__t 的调用异步失败，则 .NET <xref:System.Threading.Tasks.Task> 会失败。</span><span class="sxs-lookup"><span data-stu-id="0636f-200">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="0636f-201">例如，对 @no__t 的调用可能会失败，这是因为 JavaScript 端代码引发异常或返回一个以 `rejected` 完成的 `Promise`。</span><span class="sxs-lookup"><span data-stu-id="0636f-201">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="0636f-202">开发人员代码必须捕获异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-202">Developer code must catch the exception.</span></span> <span data-ttu-id="0636f-203">如果使用[await](/dotnet/csharp/language-reference/keywords/await)运算符，请考虑在包含错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中包装方法调用。</span><span class="sxs-lookup"><span data-stu-id="0636f-203">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="0636f-204">否则，失败的代码会导致未处理的异常，这是对线路至关重要的。</span><span class="sxs-lookup"><span data-stu-id="0636f-204">Otherwise, the failing code results in an unhandled exception that's fatal to the circuit.</span></span>
* <span data-ttu-id="0636f-205">默认情况下，对 @no__t 的调用必须在特定时间段内完成，否则调用会超时。默认超时期限为一分钟。</span><span class="sxs-lookup"><span data-stu-id="0636f-205">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="0636f-206">超时可防止代码丢失网络连接或从不发送回完成消息的 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="0636f-206">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="0636f-207">如果调用超时，则生成的 `Task` 会失败，并出现 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="0636f-207">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="0636f-208">捕获并处理日志记录的异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-208">Trap and process the exception with logging.</span></span>

<span data-ttu-id="0636f-209">同样，JavaScript 代码可以启动对[[JSInvokable] 属性](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)指示的 .net 方法的调用。</span><span class="sxs-lookup"><span data-stu-id="0636f-209">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [[JSInvokable] attribute](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions).</span></span> <span data-ttu-id="0636f-210">如果这些 .NET 方法引发未经处理的异常：</span><span class="sxs-lookup"><span data-stu-id="0636f-210">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="0636f-211">此异常不会被视为对线路的严重。</span><span class="sxs-lookup"><span data-stu-id="0636f-211">The exception isn't treated as fatal to the circuit.</span></span>
* <span data-ttu-id="0636f-212">JavaScript 端 `Promise` 已被拒绝。</span><span class="sxs-lookup"><span data-stu-id="0636f-212">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="0636f-213">您可以选择在 .NET 端或方法调用的 JavaScript 端使用错误处理代码。</span><span class="sxs-lookup"><span data-stu-id="0636f-213">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="0636f-214">有关详细信息，请参阅 <xref:blazor/javascript-interop> 。</span><span class="sxs-lookup"><span data-stu-id="0636f-214">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="circuit-handlers"></a><span data-ttu-id="0636f-215">线路处理程序</span><span class="sxs-lookup"><span data-stu-id="0636f-215">Circuit handlers</span></span>

<span data-ttu-id="0636f-216">Blazor 允许代码定义一个*线路处理程序，该处理程序*在用户线路的状态发生变化时接收通知。</span><span class="sxs-lookup"><span data-stu-id="0636f-216">Blazor allows code to define a *circuit handler*, which receives notifications when the state of a user's circuit changes.</span></span> <span data-ttu-id="0636f-217">使用以下状态：</span><span class="sxs-lookup"><span data-stu-id="0636f-217">The following states are used:</span></span>

* `initialized`
* `connected`
* `disconnected`
* `disposed`

<span data-ttu-id="0636f-218">通过注册从 `CircuitHandler` 抽象基类继承的 DI 服务来管理通知。</span><span class="sxs-lookup"><span data-stu-id="0636f-218">Notifications are managed by registering a DI service that inherits from the `CircuitHandler` abstract base class.</span></span>

<span data-ttu-id="0636f-219">如果自定义线路处理程序的方法引发未经处理的异常，则该异常对于线路是致命的。</span><span class="sxs-lookup"><span data-stu-id="0636f-219">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="0636f-220">若要容忍处理程序代码或调用方法中的异常，请使用错误处理和日志记录将代码包装在一个或多个[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中。</span><span class="sxs-lookup"><span data-stu-id="0636f-220">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="circuit-disposal"></a><span data-ttu-id="0636f-221">线路处置</span><span class="sxs-lookup"><span data-stu-id="0636f-221">Circuit disposal</span></span>

<span data-ttu-id="0636f-222">当某个线路由于用户已断开连接并且该框架正在清理线路状态而结束时，框架会释放该线路的 DI 范围。</span><span class="sxs-lookup"><span data-stu-id="0636f-222">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="0636f-223">释放作用域将释放任何实现 <xref:System.IDisposable?displayProperty=fullName> 的线路范围的 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="0636f-223">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="0636f-224">如果任何 DI 服务在处理过程中引发未经处理的异常，则框架将记录该异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-224">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="prerendering"></a><span data-ttu-id="0636f-225">呈现</span><span class="sxs-lookup"><span data-stu-id="0636f-225">Prerendering</span></span>

<span data-ttu-id="0636f-226">Blazor 组件可以使用 @no__t 来预呈现，以便在用户初始 HTTP 请求过程中返回其呈现的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="0636f-226">Blazor components can be prerendered using `Html.RenderComponentAsync` so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="0636f-227">此功能的工作方式如下：</span><span class="sxs-lookup"><span data-stu-id="0636f-227">This works by:</span></span>

* <span data-ttu-id="0636f-228">创建一个新线路，其中包含属于同一页面的所有预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="0636f-228">Creating a new circuit containing all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="0636f-229">正在生成初始 HTML。</span><span class="sxs-lookup"><span data-stu-id="0636f-229">Generating the initial HTML.</span></span>
* <span data-ttu-id="0636f-230">将线路视为 `disconnected`，直到用户的浏览器将 SignalR 连接到同一台服务器，以恢复线路上的交互性。</span><span class="sxs-lookup"><span data-stu-id="0636f-230">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server to resume interactivity on the circuit.</span></span>

<span data-ttu-id="0636f-231">如果任何组件在预呈现期间引发未经处理的异常，例如，在生命周期方法或呈现逻辑中：</span><span class="sxs-lookup"><span data-stu-id="0636f-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="0636f-232">此异常是线路致命的。</span><span class="sxs-lookup"><span data-stu-id="0636f-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="0636f-233">从 `Html.RenderComponentAsync` 调用引发了调用堆栈后引发的异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-233">The exception is thrown up the call stack from the `Html.RenderComponentAsync` call.</span></span> <span data-ttu-id="0636f-234">因此，整个 HTTP 请求将失败，除非开发人员代码显式捕获了异常。</span><span class="sxs-lookup"><span data-stu-id="0636f-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="0636f-235">在正常情况下，如果预呈现失败，则继续生成并呈现组件没有意义，因为无法呈现工作组件。</span><span class="sxs-lookup"><span data-stu-id="0636f-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="0636f-236">若要容忍在预呈现期间可能发生的错误，必须将错误处理逻辑放置在可能引发异常的组件中。</span><span class="sxs-lookup"><span data-stu-id="0636f-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="0636f-237">使用带有错误处理和日志记录的[try catch](/dotnet/csharp/language-reference/keywords/try-catch)语句。</span><span class="sxs-lookup"><span data-stu-id="0636f-237">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="0636f-238">不是在 @no__t 1 语句中包装对 @no__t 的调用，而是将错误处理逻辑放在由 `RenderComponentAsync` 呈现的组件中。</span><span class="sxs-lookup"><span data-stu-id="0636f-238">Instead of wrapping the call to `RenderComponentAsync` in a `try-catch` statement, place error handling logic in the component rendered by `RenderComponentAsync`.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="0636f-239">高级方案</span><span class="sxs-lookup"><span data-stu-id="0636f-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="0636f-240">递归呈现</span><span class="sxs-lookup"><span data-stu-id="0636f-240">Recursive rendering</span></span>

<span data-ttu-id="0636f-241">组件可以递归嵌套。</span><span class="sxs-lookup"><span data-stu-id="0636f-241">Components can be nested recursively.</span></span> <span data-ttu-id="0636f-242">这适用于表示递归数据结构。</span><span class="sxs-lookup"><span data-stu-id="0636f-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="0636f-243">例如，@no__t 的组件可以为每个节点的子节点呈现更多 @no__t 的组件。</span><span class="sxs-lookup"><span data-stu-id="0636f-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="0636f-244">以递归方式呈现时，请避免将导致无限递归的编码模式：</span><span class="sxs-lookup"><span data-stu-id="0636f-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="0636f-245">不以递归方式呈现包含循环的数据结构。</span><span class="sxs-lookup"><span data-stu-id="0636f-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="0636f-246">例如，不呈现其子级包含其自身的树节点。</span><span class="sxs-lookup"><span data-stu-id="0636f-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="0636f-247">不要创建包含循环的布局链。</span><span class="sxs-lookup"><span data-stu-id="0636f-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="0636f-248">例如，请勿创建布局本身为的布局。</span><span class="sxs-lookup"><span data-stu-id="0636f-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="0636f-249">不允许最终用户通过恶意数据输入或 JavaScript 互操作调用来违反递归固定条件（规则）。</span><span class="sxs-lookup"><span data-stu-id="0636f-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="0636f-250">呈现过程中的无限循环：</span><span class="sxs-lookup"><span data-stu-id="0636f-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="0636f-251">导致呈现过程永久继续。</span><span class="sxs-lookup"><span data-stu-id="0636f-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="0636f-252">等效于创建未终止的循环。</span><span class="sxs-lookup"><span data-stu-id="0636f-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="0636f-253">在这些情况下，受影响的线路会挂起，并且该线程通常会尝试执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0636f-253">In these scenarios, the affected circuit hangs, and the thread usually attempts to:</span></span>

* <span data-ttu-id="0636f-254">无限期地消耗操作系统允许的 CPU 时间。</span><span class="sxs-lookup"><span data-stu-id="0636f-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="0636f-255">消耗不限数量的服务器内存。</span><span class="sxs-lookup"><span data-stu-id="0636f-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="0636f-256">使用无限制内存等效于未终止的循环在每次迭代时向集合添加项的情况。</span><span class="sxs-lookup"><span data-stu-id="0636f-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="0636f-257">若要避免无限递归模式，请确保递归呈现代码包含合适的停止条件。</span><span class="sxs-lookup"><span data-stu-id="0636f-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="0636f-258">自定义呈现器树根逻辑</span><span class="sxs-lookup"><span data-stu-id="0636f-258">Custom render tree logic</span></span>

<span data-ttu-id="0636f-259">大多数 Blazor 组件都作为*razor*文件实现并进行编译，以生成在 `RenderTreeBuilder` 上进行操作以呈现其输出的逻辑。</span><span class="sxs-lookup"><span data-stu-id="0636f-259">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="0636f-260">开发人员可以使用程序C#代码手动实现 `RenderTreeBuilder` 逻辑。</span><span class="sxs-lookup"><span data-stu-id="0636f-260">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="0636f-261">有关详细信息，请参阅 <xref:blazor/components#manual-rendertreebuilder-logic> 。</span><span class="sxs-lookup"><span data-stu-id="0636f-261">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="0636f-262">使用手动渲染树生成器逻辑被视为一种高级不安全的方案，不建议用于常规组件开发。</span><span class="sxs-lookup"><span data-stu-id="0636f-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="0636f-263">如果写入 @no__t 0 代码，开发人员必须保证代码的正确性。</span><span class="sxs-lookup"><span data-stu-id="0636f-263">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="0636f-264">例如，开发人员必须确保：</span><span class="sxs-lookup"><span data-stu-id="0636f-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="0636f-265">对 @no__t 和 `CloseElement` 的调用已正确平衡。</span><span class="sxs-lookup"><span data-stu-id="0636f-265">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="0636f-266">特性仅添加到正确的位置。</span><span class="sxs-lookup"><span data-stu-id="0636f-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="0636f-267">手动呈现树生成器逻辑不正确可能会导致任意未定义的行为，包括崩溃、服务器挂起和安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="0636f-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="0636f-268">请考虑在相同程度的复杂性上手动呈现树生成器逻辑，并使用与手动编写程序集代码或 MSIL 指令相同的*危险*级别。</span><span class="sxs-lookup"><span data-stu-id="0636f-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
