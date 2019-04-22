---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705518"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a><span data-ttu-id="b6e75-101">ASP.NET Core 中间件扩展性示例</span><span class="sxs-lookup"><span data-stu-id="b6e75-101">ASP.NET Core Middleware Extensibility Sample</span></span>

<span data-ttu-id="b6e75-102">此示例演示了 [ASP.NET Core 中基于工厂的中间件激活](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility)中所述的方案。</span><span class="sxs-lookup"><span data-stu-id="b6e75-102">This sample demonstrates the scenarios described in [Factory-based middleware activation in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span></span>

<span data-ttu-id="b6e75-103">示例应用演示了使用以下两种方式激活的中间件：</span><span class="sxs-lookup"><span data-stu-id="b6e75-103">The sample app demonstrates middleware activated by:</span></span>

* <span data-ttu-id="b6e75-104">约定。</span><span class="sxs-lookup"><span data-stu-id="b6e75-104">Convention.</span></span> <span data-ttu-id="b6e75-105">有关使用约定激活中间件的详细信息，请参阅[中间件](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/)主题。</span><span class="sxs-lookup"><span data-stu-id="b6e75-105">For more information on conventional middleware activation, see the [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) topic.</span></span>
* <span data-ttu-id="b6e75-106">[IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) 实现。</span><span class="sxs-lookup"><span data-stu-id="b6e75-106">An [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementation.</span></span> <span data-ttu-id="b6e75-107">默认的 [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) 类可激活中间件。</span><span class="sxs-lookup"><span data-stu-id="b6e75-107">The default [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) class activates the middleware.</span></span>

<span data-ttu-id="b6e75-108">这两种中间件实现的功能相同，并能记录由查询字符串参数 (`key`) 提供的值。</span><span class="sxs-lookup"><span data-stu-id="b6e75-108">The middleware implementations function identically and record the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="b6e75-109">中间件使用插入的数据库上下文（作用域服务）将查询字符串值记录在内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="b6e75-109">The middlewares use an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>
