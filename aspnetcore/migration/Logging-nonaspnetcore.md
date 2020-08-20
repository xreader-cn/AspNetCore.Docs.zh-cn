---
title: 从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0
author: pakrym
description: 了解如何迁移使用 non-ASP.NET 的核心应用程序。日志记录2.1 到2.2 或3.0。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 8ef07118e3a885e41221402bdfc2d7e942c6dd73
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634093"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0

本文概述了将使用2.1 的 non-ASP.NET 核心应用程序迁移 `Microsoft.Extensions.Logging` 到2.2 或3.0 的常见步骤。

## <a name="21-to-22"></a>2.1 到 2.2

手动创建 `ServiceCollection` 并调用 `AddLogging` 。

2.1 示例：

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2.2 示例：

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1 至3。0

在3.0 中，使用 `LoggingFactory.Create` 。

2.1 示例：

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3.0 示例：

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>其他资源

* - [Console NuGet 包](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。
* <xref:fundamentals/logging/index>
