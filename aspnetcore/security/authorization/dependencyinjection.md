---
title: 在 ASP.NET Core要求处理程序中的依赖关系注入
author: rick-anderson
description: 了解如何插入到 ASP.NET Core 应用使用依赖关系注入的授权要求处理程序。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 71d563e11d308a95c08e6d012d3a071f4697d2de
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896364"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="1f710-103">在 ASP.NET Core要求处理程序中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="1f710-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="1f710-104">[授权处理程序必须进行注册](xref:security/authorization/policies#handler-registration)在配置期间服务集合中 (使用[依赖关系注入](xref:fundamentals/dependency-injection))。</span><span class="sxs-lookup"><span data-stu-id="1f710-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="1f710-105">假设你有想要评估的授权处理程序内的规则的存储库和服务集合中已注册该存储库。</span><span class="sxs-lookup"><span data-stu-id="1f710-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="1f710-106">授权将解决，然后将它注入到您的构造函数。</span><span class="sxs-lookup"><span data-stu-id="1f710-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="1f710-107">例如，如果你想要使用 ASP。NET 的日志记录基础结构要注入`ILoggerFactory`到您的处理程序。</span><span class="sxs-lookup"><span data-stu-id="1f710-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="1f710-108">此类处理程序可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="1f710-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="1f710-109">将注册处理程序替换`services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="1f710-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="1f710-110">该处理程序将你的应用程序启动时创建的实例并注入的已注册的 DI 将`ILoggerFactory`到您的构造函数。</span><span class="sxs-lookup"><span data-stu-id="1f710-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="1f710-111">使用实体框架的处理程序不应注册为单一实例。</span><span class="sxs-lookup"><span data-stu-id="1f710-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
