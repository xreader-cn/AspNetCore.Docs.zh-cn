---
title: 从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0
author: pakrym
description: 了解如何迁移使用 non-ASP.NET 的核心应用程序。日志记录2.1 到2.2 或3.0。
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777054"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="bf54b-103">从 Microsoft 进行迁移。日志记录2.1 到2.2 或3。0</span><span class="sxs-lookup"><span data-stu-id="bf54b-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="bf54b-104">本文概述了将使用`Microsoft.Extensions.Logging` 2.1 的 non-ASP.NET 核心应用程序迁移到2.2 或3.0 的常见步骤。</span><span class="sxs-lookup"><span data-stu-id="bf54b-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="bf54b-105">2.1 到 2.2</span><span class="sxs-lookup"><span data-stu-id="bf54b-105">2.1 to 2.2</span></span>

<span data-ttu-id="bf54b-106">手动创建`ServiceCollection`并调用`AddLogging`。</span><span class="sxs-lookup"><span data-stu-id="bf54b-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="bf54b-107">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="bf54b-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="bf54b-108">2.2 示例：</span><span class="sxs-lookup"><span data-stu-id="bf54b-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="bf54b-109">2.1 至3。0</span><span class="sxs-lookup"><span data-stu-id="bf54b-109">2.1 to 3.0</span></span>

<span data-ttu-id="bf54b-110">在3.0 中， `LoggingFactory.Create`使用。</span><span class="sxs-lookup"><span data-stu-id="bf54b-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="bf54b-111">2.1 示例：</span><span class="sxs-lookup"><span data-stu-id="bf54b-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="bf54b-112">3.0 示例：</span><span class="sxs-lookup"><span data-stu-id="bf54b-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="bf54b-113">其他资源</span><span class="sxs-lookup"><span data-stu-id="bf54b-113">Additional resources</span></span>

<xref:fundamentals/logging/index>