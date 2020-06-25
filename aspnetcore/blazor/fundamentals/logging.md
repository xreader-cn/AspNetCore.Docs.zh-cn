---
title: ASP.NET Core Blazor 日志记录
author: guardrex
description: 了解 Blazor 应用中的日志记录，包括日志级别配置以及如何从 Razor 组件写入日志消息。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: 841c4021d9217312b2601b0e775542c6455cca82
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240874"
---
# <a name="aspnet-core-blazor-logging"></a>ASP.NET Core Blazor 日志记录

## <a name="blazor-webassembly"></a>Blazor WebAssembly

使用 `Program.Main` 中的 `WebAssemblyHostBuilder.Logging` 属性在 Blazor WebAssembly 应用中配置日志：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging` 属性的类型为 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>，因此可在 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> 上使用的所有扩展方法也可在 `Logging` 上使用。

可以从应用设置文件中加载日志记录配置。 有关详细信息，请参阅 <xref:blazor/fundamentals/configuration#logging-configuration>。

## <a name="blazor-server"></a>Blazor 服务器

若要获取常规 ASP.NET Core 日志记录指南，请参阅 <xref:fundamentals/logging/index>。

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor WebAssembly SignalR .NET 客户端日志记录

注入 <xref:Microsoft.Extensions.Logging.ILoggerProvider>，将 `WebAssemblyConsoleLogger` 添加到传递给 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 的日志记录提供程序。 与传统的 <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> 不同，`WebAssemblyConsoleLogger` 是特定于浏览器的日志记录 API（例如，`console.log`）的包装器。 使用 `WebAssemblyConsoleLogger` 可以在浏览器上下文内的 Mono 中进行日志记录。

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a>登录 Razor 组件

记录器会采用应用启动配置。

要支持对 API 使用 IntelliSense 完成项（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>），需具备 <xref:Microsoft.Extensions.Logging> 的 `using` 指令。

下面的示例演示了如何使用 Razor 组件中的 <xref:Microsoft.Extensions.Logging.ILogger> 进行日志记录：

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

下面的示例演示了如何使用 Razor 组件中的 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 进行日志记录：

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

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/logging/index>
