---
title: 'ASP.NET Core :::no-loc(Blazor)::: 日志记录'
author: guardrex
description: '了解 :::no-loc(Blazor)::: 应用中的日志记录，包括日志级别配置以及如何从 :::no-loc(Razor)::: 组件写入日志消息。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055979"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="84e3b-103">ASP.NET Core :::no-loc(Blazor)::: 日志记录</span><span class="sxs-lookup"><span data-stu-id="84e3b-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="84e3b-104">使用 `Program.Main` 中的 `WebAssemblyHostBuilder.Logging` 属性在 :::no-loc(Blazor WebAssembly)::: 应用中配置日志：</span><span class="sxs-lookup"><span data-stu-id="84e3b-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="84e3b-105">`Logging` 属性的类型为 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>，因此可在 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> 上使用的所有扩展方法也可在 `Logging` 上使用。</span><span class="sxs-lookup"><span data-stu-id="84e3b-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="84e3b-106">可以从应用设置文件中加载日志记录配置。</span><span class="sxs-lookup"><span data-stu-id="84e3b-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="84e3b-107">有关详细信息，请参阅 <xref:blazor/fundamentals/configuration#logging-configuration>。</span><span class="sxs-lookup"><span data-stu-id="84e3b-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="84e3b-108">若要获取常规 ASP.NET Core 日志记录指南，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="84e3b-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="84e3b-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="84e3b-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="84e3b-110">注入 <xref:Microsoft.Extensions.Logging.ILoggerProvider>，将 `WebAssemblyConsoleLogger` 添加到传递给 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> 的日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="84e3b-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="84e3b-111">与传统的 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> 不同，`WebAssemblyConsoleLogger` 是特定于浏览器的日志记录 API（例如，`console.log`）的包装器。</span><span class="sxs-lookup"><span data-stu-id="84e3b-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="84e3b-112">使用 `WebAssemblyConsoleLogger` 可以在浏览器上下文内的 Mono 中进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="84e3b-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="84e3b-113">登录 :::no-loc(Razor)::: 组件</span><span class="sxs-lookup"><span data-stu-id="84e3b-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="84e3b-114">记录器会采用应用启动配置。</span><span class="sxs-lookup"><span data-stu-id="84e3b-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="84e3b-115">要支持对 API 使用 IntelliSense 完成项（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>），需具备 <xref:Microsoft.Extensions.Logging> 的 `using` 指令。</span><span class="sxs-lookup"><span data-stu-id="84e3b-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="84e3b-116">下面的示例演示了如何使用 :::no-loc(Razor)::: 组件中的 <xref:Microsoft.Extensions.Logging.ILogger> 进行日志记录：</span><span class="sxs-lookup"><span data-stu-id="84e3b-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="84e3b-117">下面的示例演示了如何使用 :::no-loc(Razor)::: 组件中的 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 进行日志记录：</span><span class="sxs-lookup"><span data-stu-id="84e3b-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="84e3b-118">其他资源</span><span class="sxs-lookup"><span data-stu-id="84e3b-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
