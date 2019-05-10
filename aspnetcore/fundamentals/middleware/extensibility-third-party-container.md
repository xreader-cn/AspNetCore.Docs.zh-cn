---
title: 使用 ASP.NET Core 中的第三方容器激活中间件
author: guardrex
description: 了解如何在基于工厂的激活和 ASP.NET Core 中的第三方容器中使用强类型中间件。
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2018
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9e4d4c6c0232ebc51ad08923e10164262b652280
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888072"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>使用 ASP.NET Core 中的第三方容器激活中间件

作者：[Luke Latham](https://github.com/guardrex)

本文演示如何使用 [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) 和 [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) 作为使用第三方容器激活[中间件](xref:fundamentals/middleware/index)的可扩展点。 有关 `IMiddlewareFactory` 和 `IMiddleware` 的介绍性信息，请参阅[基于工厂的中间件激活](xref:fundamentals/middleware/extensibility)主题。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample)（[如何下载](xref:index#how-to-download-a-sample)）

示例应用演示了使用 `IMiddlewareFactory`、`SimpleInjectorMiddlewareFactory` 实现激活的中间件。 此示例使用 [Simple Injector](https://simpleinjector.org) 依赖项注入 (DI) 容器。

此示例的中间件实现记录了查询字符串参数 (`key`) 提供的值。 中间件使用插入的数据库上下文（有作用域的服务）将查询字符串值记录在内存中数据库。

> [!NOTE]
> 此示例应用仅出于演示目的使用 [Simple Injector](https://github.com/simpleinjector/SimpleInjector)。 不认可使用 Simple Injector。 Simple Injector 文档中描述的中间件激活方法和 Simple Injector 维护人员推荐的 GitHub 问题。 有关详细信息，请参阅 [Simple Injector 文档](https://simpleinjector.readthedocs.io/en/latest/index.html)和 [Simple Injector GitHub 存储库](https://github.com/simpleinjector/SimpleInjector)。

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) 提供中间件的创建方法。

在示例应用中，实现了中间件工厂以创建 `SimpleInjectorActivatedMiddleware` 实例。 中间件工厂使用 Simple Injector 容器来解析中间件：

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) 定义应用的请求管道的中间件。

由 `IMiddlewareFactory` 实现 (Middleware/SimpleInjectorActivatedMiddleware.cs) 激活的中间件：

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

为中间件创建扩展 (Middleware/MiddlewareExtensions.cs)：

[!code-csharp[](extensibility-third-party-container/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices` 必须执行多项任务：

* 设置 Simple Injector 容器。
* 注册工厂和中间件。
* 为 Razor 页面的 Simple Injector 容器创建应用的数据库上下文。

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet1)]

中间件在 `Startup.Configure` 的请求处理管道中注册：

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a>其他资源

* [中间件](xref:fundamentals/middleware/index)
* [基于工厂的中间件激活](xref:fundamentals/middleware/extensibility)
* [Simple Injector GitHub 存储库](https://github.com/simpleinjector/SimpleInjector)
* [Simple Injector 文档](https://simpleinjector.readthedocs.io/en/latest/index.html)
