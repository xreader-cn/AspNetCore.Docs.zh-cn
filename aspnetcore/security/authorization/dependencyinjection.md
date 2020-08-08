---
title: ASP.NET Core 中的要求处理程序中的依赖项注入
author: rick-anderson
description: 了解如何使用依赖关系注入将授权要求处理程序注入到 ASP.NET Core 应用。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: e58498cc7a28b598b919c5cab128249448bde32a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020998"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>ASP.NET Core 中的要求处理程序中的依赖项注入

<a name="security-authorization-di"></a>

在配置 (使用[依赖关系注入](xref:fundamentals/dependency-injection)) ，必须在服务集合中[注册授权处理程序](xref:security/authorization/policies#handler-registration)。

假设你有一个规则存储库，你希望在授权处理程序中评估该存储库，并在服务集合中注册该存储库。 授权将解析并注入构造函数。

例如，如果你想要使用 ASP。要注入处理程序的网络日志记录基础结构 `ILoggerFactory` 。 此类处理程序可能如下所示：

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

将处理程序注册到 `services.AddSingleton()` ：

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

当应用程序启动时，将创建处理程序的实例，DI 会将注册的注入 `ILoggerFactory` 到构造函数中。

> [!NOTE]
> 使用实体框架的处理程序不应注册为单一实例。
