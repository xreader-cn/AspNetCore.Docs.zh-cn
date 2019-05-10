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
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>在 ASP.NET Core要求处理程序中的依赖关系注入

<a name="security-authorization-di"></a>

[授权处理程序必须进行注册](xref:security/authorization/policies#handler-registration)在配置期间服务集合中 (使用[依赖关系注入](xref:fundamentals/dependency-injection))。

假设你有想要评估的授权处理程序内的规则的存储库和服务集合中已注册该存储库。 授权将解决，然后将它注入到您的构造函数。

例如，如果你想要使用 ASP。NET 的日志记录基础结构要注入`ILoggerFactory`到您的处理程序。 此类处理程序可能如下所示：

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

将注册处理程序替换`services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

该处理程序将你的应用程序启动时创建的实例并注入的已注册的 DI 将`ILoggerFactory`到您的构造函数。

> [!NOTE]
> 使用实体框架的处理程序不应注册为单一实例。
