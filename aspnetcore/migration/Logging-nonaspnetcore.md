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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="6f05b-103">从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0</span><span class="sxs-lookup"><span data-stu-id="6f05b-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="6f05b-104">本文概述了将使用2.1 的 non-ASP.NET 核心应用程序迁移 `Microsoft.Extensions.Logging` 到2.2 或3.0 的常见步骤。</span><span class="sxs-lookup"><span data-stu-id="6f05b-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="6f05b-105">2.1 到 2.2</span><span class="sxs-lookup"><span data-stu-id="6f05b-105">2.1 to 2.2</span></span>

<span data-ttu-id="6f05b-106">手动创建 `ServiceCollection` 并调用 `AddLogging` 。</span><span class="sxs-lookup"><span data-stu-id="6f05b-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="6f05b-107">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="6f05b-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="6f05b-108">2.2 示例：</span><span class="sxs-lookup"><span data-stu-id="6f05b-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="6f05b-109">2.1 至3。0</span><span class="sxs-lookup"><span data-stu-id="6f05b-109">2.1 to 3.0</span></span>

<span data-ttu-id="6f05b-110">在3.0 中，使用 `LoggingFactory.Create` 。</span><span class="sxs-lookup"><span data-stu-id="6f05b-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="6f05b-111">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="6f05b-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="6f05b-112">3.0 示例：</span><span class="sxs-lookup"><span data-stu-id="6f05b-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="6f05b-113">其他资源</span><span class="sxs-lookup"><span data-stu-id="6f05b-113">Additional resources</span></span>

* <span data-ttu-id="6f05b-114">- [Console NuGet 包](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。</span><span class="sxs-lookup"><span data-stu-id="6f05b-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
