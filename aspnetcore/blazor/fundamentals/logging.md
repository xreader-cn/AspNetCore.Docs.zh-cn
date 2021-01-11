---
title: ASP.NET Core Blazor 日志记录
author: guardrex
description: 了解 Blazor 应用中的日志记录，包括日志级别配置以及如何从 Razor 组件写入日志消息。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97666828"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="bb9cc-103">ASP.NET Core Blazor 日志记录</span><span class="sxs-lookup"><span data-stu-id="bb9cc-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="bb9cc-104">使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> 属性在 Blazor WebAssembly 应用配置自定义日志。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="bb9cc-105">向 `Program.cs` 添加 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> 的命名空间：</span><span class="sxs-lookup"><span data-stu-id="bb9cc-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="bb9cc-106">在 `Program.cs` 的 `Program.Main` 中，使用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> 设置最小日志记录级别，并添加自定义日志记录提供程序：</span><span class="sxs-lookup"><span data-stu-id="bb9cc-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="bb9cc-107">`Logging` 属性的类型为 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>，因此可在 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> 上使用的所有扩展方法也可在 `Logging` 上使用。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="bb9cc-108">可以从应用设置文件中加载日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="bb9cc-109">有关详细信息，请参阅 <xref:blazor/fundamentals/configuration#logging-configuration>。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="bb9cc-110">SignalR .NET 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="bb9cc-110">SignalR .NET client logging</span></span>

<span data-ttu-id="bb9cc-111">注入 <xref:Microsoft.Extensions.Logging.ILoggerProvider>，将 `WebAssemblyConsoleLogger` 添加到传递给 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 的日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="bb9cc-112">与传统的 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> 不同，`WebAssemblyConsoleLogger` 是特定于浏览器的日志记录 API（例如，`console.log`）的包装器。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="bb9cc-113">使用 `WebAssemblyConsoleLogger` 可以在浏览器上下文内的 Mono 中进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

> [!NOTE]
> <span data-ttu-id="bb9cc-114">`WebAssemblyConsoleLogger` 是[内部](/dotnet/csharp/language-reference/keywords/internal)包装器，不能在开发人员代码中直接使用。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-114">`WebAssemblyConsoleLogger` is [internal](/dotnet/csharp/language-reference/keywords/internal) and not available for direct use in developer code.</span></span>

<span data-ttu-id="bb9cc-115">为 <xref:Microsoft.Extensions.Logging?displayProperty=fullName> 添加命名空间，并将 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 注入到组件中：</span><span class="sxs-lookup"><span data-stu-id="bb9cc-115">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="bb9cc-116">在组件的 [`OnInitializedAsync` 方法](xref:blazor/components/lifecycle#component-initialization-methods)中，使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="bb9cc-116">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="bb9cc-117">若要获取 Blazor Server的常规 ASP.NET Core 日志记录指南，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-117">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="bb9cc-118">登录 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="bb9cc-118">Log in Razor components</span></span>

<span data-ttu-id="bb9cc-119">记录器会采用应用启动配置。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-119">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="bb9cc-120">要支持对 API 使用 [IntelliSense](/visualstudio/ide/using-intellisense) 补全功能（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>），需具备 <xref:Microsoft.Extensions.Logging> 的 `using` 指令。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-120">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="bb9cc-121">下面的示例演示了如何使用组件中的 <xref:Microsoft.Extensions.Logging.ILogger> 进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-121">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="bb9cc-122">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb9cc-122">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="bb9cc-123">下面的示例演示了如何使用组件中的 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="bb9cc-123">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="bb9cc-124">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb9cc-124">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="bb9cc-125">其他资源</span><span class="sxs-lookup"><span data-stu-id="bb9cc-125">Additional resources</span></span>

* <xref:fundamentals/logging/index>
