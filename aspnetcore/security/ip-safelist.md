---
title: ASP.NET核心客户端 IP 安全列表
author: damienbod
description: 了解如何编写中间件或操作筛选器，以便根据已批准的 IP 地址列表验证远程 IP 地址。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471794"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET核心客户端 IP 安全列表

由[达米安·鲍登](https://twitter.com/damien_bod)和[汤姆·戴克斯特拉](https://github.com/tdykstra)
 
本文介绍了在ASP.NET核心应用中实现 IP 地址安全列表（也称为允许列表）的三种方法。 随附的示例应用演示了所有三种方法。 可用工具如下：

* 用于检查每个请求的远程 IP 地址的中间件。
* MVC 操作筛选器，用于检查特定控制器或操作方法的请求的远程 IP 地址。
* Razor 页面筛选器以检查 Razor 页面请求的远程 IP 地址。

在每种情况下，包含已批准的客户端 IP 地址的字符串都存储在应用设置中。 中间件或过滤器：

* 将字符串解析为数组。 
* 检查阵列中是否存在远程 IP 地址。

如果数组包含 IP 地址，则允许访问。 否则，将返回 HTTP 403 禁止状态代码。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="ip-address-safelist"></a>IP 地址安全列表

在示例应用中，IP 地址安全列表为：

* 由`AdminSafeList`*appsettings.json*文件中的属性定义。
* 一个分号分隔字符串，可能包含 Internet[协议版本 4 （IPv4）](https://wikipedia.org/wiki/IPv4)和[Internet 协议版本 6 （IPv6）](https://wikipedia.org/wiki/IPv6)地址。

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

在前面的示例中，`127.0.0.1`允许 和`192.168.1.5`的 IPv4 地址和`::1`IPv6 回回地址（压缩格式`0:0:0:0:0:0:0:1`）。

## <a name="middleware"></a>中间件

该方法`Startup.Configure`将自定义`AdminSafeListMiddleware`中间件类型添加到应用的请求管道中。 使用 .NET Core 配置提供程序检索安全列表，并作为构造函数参数传递。

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

中间件将字符串解析为数组并搜索数组中的远程 IP 地址。 如果未找到远程 IP 地址，中间件将返回 HTTP 403 禁止。 此验证过程是绕过 HTTP GET 请求的。

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>操作筛选器

如果需要针对特定 MVC 控制器或操作方法的安全列表驱动访问控制，请使用操作筛选器。 例如：

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

在`Startup.ConfigureServices`中，将操作筛选器添加到 MVC 筛选器集合。 在下面的示例中，将添加`ClientIpCheckActionFilter`操作筛选器。 安全列表和控制台记录器实例作为构造函数参数传递。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

然后，操作筛选器可以应用于具有[[服务筛选器]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)属性的控制器或操作方法：

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

在示例应用中，操作筛选器应用于控制器的操作`Get`方法。 通过发送测试应用时：

* HTTP GET 请求，`[ServiceFilter]`属性验证客户端 IP 地址。 如果允许访问`Get`操作方法，操作筛选器和操作方法将生成以下控制台输出的变体：

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* 除了 GET 之外，`AdminSafeListMiddleware`中间件的 HTTP 请求谓词将验证客户端 IP 地址。

## <a name="razor-pages-filter"></a>剃刀页面过滤器

如果需要 Razor Pages 应用的安全列表驱动访问控制，请使用 Razor 页面筛选器。 例如：

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

在`Startup.ConfigureServices`中，通过将 Razor 页面添加到 MVC 筛选器集合，使其启用该筛选器。 在下面的示例中，将添加`ClientIpCheckPageFilter`Razor 页面筛选器。 安全列表和控制台记录器实例作为构造函数参数传递。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

当请求示例应用的*索引*Razor 页面时，"Razor 页面"筛选器将验证客户端 IP 地址。 筛选器生成以下控制台输出的变体：

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/middleware/index>
* [操作筛选器](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
