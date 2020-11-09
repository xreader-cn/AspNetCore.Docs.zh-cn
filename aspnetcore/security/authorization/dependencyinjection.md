---
title: ASP.NET Core 中的要求处理程序中的依赖项注入
author: rick-anderson
description: 了解如何使用依赖关系注入将授权要求处理程序注入到 ASP.NET Core 应用。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060256"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="08516-103">ASP.NET Core 中的要求处理程序中的依赖项注入</span><span class="sxs-lookup"><span data-stu-id="08516-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="08516-104">在使用[依赖关系注入](xref:fundamentals/dependency-injection)的配置过程中，必须在服务集合中[注册授权处理程序](xref:security/authorization/policies#handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="08516-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="08516-105">假设你有一个规则存储库，你希望在授权处理程序中评估该存储库，并在服务集合中注册该存储库。</span><span class="sxs-lookup"><span data-stu-id="08516-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="08516-106">授权将解析并注入到构造函数中。</span><span class="sxs-lookup"><span data-stu-id="08516-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="08516-107">例如，使用 ASP。网络日志记录基础结构，注入 `ILoggerFactory` 处理程序。</span><span class="sxs-lookup"><span data-stu-id="08516-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="08516-108">此类处理程序可能类似于以下代码：</span><span class="sxs-lookup"><span data-stu-id="08516-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="08516-109">前面的处理程序可注册到任何 [服务生存期](/dotnet/core/extensions/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="08516-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="08516-110">下面的代码使用 `AddSingleton` 注册前面的处理程序：</span><span class="sxs-lookup"><span data-stu-id="08516-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="08516-111">处理程序的实例是在应用程序启动时创建的，DI 会将注册的注入 `ILoggerFactory` 构造函数中。</span><span class="sxs-lookup"><span data-stu-id="08516-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="08516-112">使用实体框架的处理程序不应注册为单一实例。</span><span class="sxs-lookup"><span data-stu-id="08516-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
