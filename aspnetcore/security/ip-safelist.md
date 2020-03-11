---
title: ASP.NET Core 的客户端 IP 安全安全
author: damienbod
description: 了解如何编写中间件或操作筛选器，以根据已批准的 IP 地址列表来验证远程 IP 地址。
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: d25c375f7e659168ab8cc9d8e11753cb7dfde831
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652050"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core 的客户端 IP 安全安全

作者： [Damien Bowden](https://twitter.com/damien_bod)和[Tom Dykstra](https://github.com/tdykstra)
 
本文介绍三种在 ASP.NET Core 应用程序中实现 IP 安全列表（也称为白名单）的方法。 可用工具如下：

* 用于检查每个请求的远程 IP 地址的中间件。
* 操作筛选器来检查针对特定控制器或操作方法的请求的远程 IP 地址。
* Razor Pages 筛选器来检查 Razor 页面的请求的远程 IP 地址。

在每种情况下，包含批准的客户端 IP 地址的字符串存储在应用设置中。 中间件或筛选器会将字符串分析为一个列表，并检查远程 IP 是否在列表中。 如果不是，则返回 HTTP 403 禁止的状态代码。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="the-safelist"></a>安全安全

在*appsettings*文件中配置该列表。 它是以分号分隔的列表，并且可以包含 IPv4 和 IPv6 地址。

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>中间件

`Configure` 方法将添加中间件，并在构造函数参数中将安全项的字符串传递给它。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

中间件将字符串分析为数组，并在数组中查找远程 IP 地址。 如果找不到远程 IP 地址，中间件将返回 HTTP 401 禁止访问。 对于 HTTP Get 请求，将跳过此验证过程。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>操作筛选器

如果只希望特定控制器或操作方法使用 "安全"，请使用操作筛选器。 下面是一个示例： 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckFilter.cs)]

操作筛选器将添加到服务容器中。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

然后，可以在控制器或操作方法上使用该筛选器。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

在示例应用中，筛选器将应用于 `Get` 方法。 因此，当你通过发送 `Get` API 请求来测试应用时，属性将验证客户端 IP 地址。 当你通过使用任何其他 HTTP 方法调用 API 进行测试时，中间件将验证客户端 IP。

## <a name="razor-pages-filter"></a>Razor Pages 筛选器 

如果需要 Razor Pages 应用的安全安全，请使用 Razor Pages 筛选器。 下面是一个示例： 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIpCheckPageFilter.cs)]

此筛选器通过将其添加到 MVC 筛选器集合来启用。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

当你运行应用并请求 Razor 页面时，Razor Pages 筛选器将验证客户端 IP。

## <a name="next-steps"></a>后续步骤

[了解有关 ASP.NET Core 中间件的详细信息](xref:fundamentals/middleware/index)。
