---
title: 适用于 ASP.NET Core 的客户端 IP 安全列表
author: damienbod
description: 了解如何编写中间件或操作筛选器来验证针对一组已批准的 IP 地址的远程 IP 地址。
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: ca05989efabea3a71c6912e98055a6746e0f5966
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223934"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>适用于 ASP.NET Core 的客户端 IP 安全列表

通过[Damien Bowden](https://twitter.com/damien_bod)和[Tom Dykstra](https://github.com/tdykstra)
 
本文介绍三种方法可以在 ASP.NET Core 应用中实施 IP 安全列表 （也称为允许列表）。 您可以使用：

* 若要检查的每个请求的远程 IP 地址的中间件。
* 若要检查为特定控制器或操作方法的请求的远程 IP 地址的操作筛选器。
* Razor 页面筛选器，以检查 Razor 页的请求的远程 IP 地址。

在每种情况下，包含已批准的客户端 IP 地址的字符串存储在一个应用程序设置。 中间件或筛选器将字符串分析为列表，并检查远程 IP 是否在列表中。 如果没有，则返回 HTTP 403 禁止访问状态代码。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="the-safelist"></a>安全列表

在配置列表*appsettings.json*文件。 它是以分号分隔的列表，并且可以包含 IPv4 和 IPv6 地址。

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a>中间件

`Configure`方法添加中间件，并向其构造函数参数中传递的安全列表的字符串。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

中间件将字符串分析为一个数组，并查找数组中的远程 IP 地址。 如果找不到的远程 IP 地址，中间件返回 HTTP 401 禁止访问。 对于 HTTP Get 请求跳过此验证过程。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>操作筛选器

如果您希望仅为特定控制器或操作方法的安全列表，请使用操作筛选器。 以下是一个示例： 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

操作筛选器添加到服务容器。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

然后可以在控制器或操作方法上使用筛选器。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

在示例应用中，筛选器应用于`Get`方法。 因此，当您测试应用程序通过发送`Get`API 请求，该属性就验证客户端 IP 地址。 当测试通过使用任何其他 HTTP 方法调用的 API 时，中间件就验证客户端 IP。

## <a name="razor-pages-filter"></a>Razor 页面筛选器 

如果你想为 Razor 页面应用的安全列表，使用 Razor 页面筛选器。 以下是一个示例： 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

通过将其添加到 MVC 筛选器集合启用该筛选器。

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

当您运行该应用程序，并请求 Razor 页面时，Razor 页面筛选器就验证客户端 IP。

## <a name="next-steps"></a>后续步骤

[了解有关 ASP.NET Core 中间件的详细信息](xref:fundamentals/middleware/index)。
