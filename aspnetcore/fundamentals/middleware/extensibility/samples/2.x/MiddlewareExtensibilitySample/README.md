---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705518"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Core 中间件扩展性示例

此示例演示了 [ASP.NET Core 中基于工厂的中间件激活](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility)中所述的方案。

示例应用演示了使用以下两种方式激活的中间件：

* 约定。 有关使用约定激活中间件的详细信息，请参阅[中间件](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/)主题。
* [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) 实现。 默认的 [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) 类可激活中间件。

这两种中间件实现的功能相同，并能记录由查询字符串参数 (`key`) 提供的值。 中间件使用插入的数据库上下文（作用域服务）将查询字符串值记录在内存中数据库。
