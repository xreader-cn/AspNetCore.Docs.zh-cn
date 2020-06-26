---
title: ASP.NET Core 中的要求处理程序中的依赖项注入
author: rick-anderson
description: 了解如何使用依赖关系注入将授权要求处理程序注入到 ASP.NET Core 应用。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: d12253ad1c1442c0db5cd497393daabe280fae8d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406350"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="63520-103">ASP.NET Core 中的要求处理程序中的依赖项注入</span><span class="sxs-lookup"><span data-stu-id="63520-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="63520-104">在配置期间，必须在服务集合中[注册授权处理程序](xref:security/authorization/policies#handler-registration)（使用[依赖关系注入](xref:fundamentals/dependency-injection)）。</span><span class="sxs-lookup"><span data-stu-id="63520-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="63520-105">假设你有一个规则存储库，你希望在授权处理程序中评估该存储库，并在服务集合中注册该存储库。</span><span class="sxs-lookup"><span data-stu-id="63520-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="63520-106">授权将解析并注入构造函数。</span><span class="sxs-lookup"><span data-stu-id="63520-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="63520-107">例如，如果你想要使用 ASP。要注入处理程序的网络日志记录基础结构 `ILoggerFactory` 。</span><span class="sxs-lookup"><span data-stu-id="63520-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="63520-108">此类处理程序可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="63520-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="63520-109">将处理程序注册到 `services.AddSingleton()` ：</span><span class="sxs-lookup"><span data-stu-id="63520-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="63520-110">当应用程序启动时，将创建处理程序的实例，DI 会将注册的注入 `ILoggerFactory` 到构造函数中。</span><span class="sxs-lookup"><span data-stu-id="63520-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="63520-111">使用实体框架的处理程序不应注册为单一实例。</span><span class="sxs-lookup"><span data-stu-id="63520-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
