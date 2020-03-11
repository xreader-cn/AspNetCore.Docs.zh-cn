---
title: 在 ASP.NET Core要求处理程序中的依赖关系注入
author: rick-anderson
description: 了解如何插入到 ASP.NET Core 应用使用依赖关系注入的授权要求处理程序。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 71d563e11d308a95c08e6d012d3a071f4697d2de
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654360"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>在 ASP.NET Core要求处理程序中的依赖关系注入

<a name="security-authorization-di"></a>

在配置期间，必须在服务集合中[注册授权处理程序](xref:security/authorization/policies#handler-registration)（使用[依赖关系注入](xref:fundamentals/dependency-injection)）。

假设你有一个规则存储库，你希望在授权处理程序中评估该存储库，并在服务集合中注册该存储库。 授权将解析并注入构造函数。

例如，如果你想要使用 ASP。网络的日志记录基础结构，你需要将 `ILoggerFactory` 插入处理程序中。 此类处理程序可能如下所示：

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

将处理程序注册到 `services.AddSingleton()`：

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

当应用程序启动时，将创建处理程序的实例，DI 会将已注册的 `ILoggerFactory` 注入构造函数中。

> [!NOTE]
> 使用实体框架的处理程序不应注册为单一实例。
