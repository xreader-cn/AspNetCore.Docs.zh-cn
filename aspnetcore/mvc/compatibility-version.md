---
title: ASP.NET Core MVC 的兼容性版本
author: rick-anderson
description: 了解 ASP.NET Core 中的 Startup 类如何配置服务和应用的请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/15/2019
uid: mvc/compatibility-version
ms.openlocfilehash: 7c4189db435088e0803b35add82fa0eb9372e664
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410140"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>ASP.NET Core MVC 的兼容性版本

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法允许应用选择加入或退出 ASP.NET Core MVC 2.1 或更高版本中引入的潜在中断行为变更。 这些潜在的中断行为变更通常取决于 MVC 子系统的行为方式以及运行时调用“代码”的方式。 通过选择加入，你将获取最新的行为以及 ASP.NET Core 的长期行为。

以下代码将兼容模式设置为 ASP.NET Core 2.2：

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

建议使用最新版本 (`CompatibilityVersion.Version_2_2`) 来测试应用。 我们预计大多数应用不会使用最新版本进行中断行为变更。

调用 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` 的应用会被阻止进行 ASP.NET Core 2.1 MVC 和更高的 2.x 版本中引入的潜在中断行为变更。 该阻止操作：

* 不适用于所有 2.1 和更高版本的更改，它的目标是潜在地中断 MVC 子系统中的 ASP.NET Core 运行时行为变更。
* 不会扩展到下一个主要版本。

未调用 `SetCompatibilityVersion` 的 ASP.NET Core 2.1 和更高 2.x 版本的应用的默认兼容性是 2.0 兼容性。 即，未调用 `SetCompatibilityVersion` 与调用 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` 相同。

以下代码将兼容模式设置为 ASP.NET Core 2.2（以下行为除外）：

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

对于遇到中断行为变更的应用，请使用适当的兼容性开关：

* 允许使用最新版本并选择退出特定的中断行为变更。
* 请用些时间更新应用，以便其适用于最新更改。

[MvcOptions](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Mvc/Mvc.Core/src/MvcOptions.cs) 类源注释充分地阐述了更改的内容以及为什么更改对大多数用户来说是一种改进。

将来会推出 [ASP.NET Core 3.0 版本](https://github.com/aspnet/Home/wiki/Roadmap)。 在 3.0 版本中，将删除兼容性开关支持的旧行为。 我们认为这些积极的变化几乎使所有用户受益。 现在通过引入这些更改，大多数应用可以立即受益，其他人员将有时间更新其应用。
