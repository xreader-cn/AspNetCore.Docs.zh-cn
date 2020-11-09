---
title: 从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0
author: pakrym
description: 了解如何迁移使用 non-ASP.NET 的核心应用程序。日志记录2.1 到2.2 或3.0。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 1082ae494aae7a31b195b32dfedae81c60fe3eed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060373"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="ff7e5-103">从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0</span><span class="sxs-lookup"><span data-stu-id="ff7e5-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="ff7e5-104">本文概述了将使用2.1 的 non-ASP.NET 核心应用程序迁移 `Microsoft.Extensions.Logging` 到2.2 或3.0 的常见步骤。</span><span class="sxs-lookup"><span data-stu-id="ff7e5-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="ff7e5-105">2.1 到 2.2</span><span class="sxs-lookup"><span data-stu-id="ff7e5-105">2.1 to 2.2</span></span>

<span data-ttu-id="ff7e5-106">手动创建 `ServiceCollection` 并调用 `AddLogging` 。</span><span class="sxs-lookup"><span data-stu-id="ff7e5-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="ff7e5-107">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="ff7e5-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="ff7e5-108">2.2 示例：</span><span class="sxs-lookup"><span data-stu-id="ff7e5-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="ff7e5-109">2.1 至3。0</span><span class="sxs-lookup"><span data-stu-id="ff7e5-109">2.1 to 3.0</span></span>

<span data-ttu-id="ff7e5-110">在3.0 中，使用 `LoggingFactory.Create` 。</span><span class="sxs-lookup"><span data-stu-id="ff7e5-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="ff7e5-111">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="ff7e5-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="ff7e5-112">3.0 示例：</span><span class="sxs-lookup"><span data-stu-id="ff7e5-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="ff7e5-113">其他资源</span><span class="sxs-lookup"><span data-stu-id="ff7e5-113">Additional resources</span></span>

* <span data-ttu-id="ff7e5-114">- [Console NuGet 包](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)。</span><span class="sxs-lookup"><span data-stu-id="ff7e5-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
