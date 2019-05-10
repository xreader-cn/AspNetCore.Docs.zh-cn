---
title: 将配置迁移到 ASP.NET Core
author: ardalis
description: 了解如何将配置从 ASP.NET MVC 项目迁移到 ASP.NET Core MVC 项目。
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894924"
---
# <a name="migrate-configuration-to-aspnet-core"></a>将配置迁移到 ASP.NET Core

作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)

在以前的文章中，我们就已着手[将 ASP.NET MVC 项目迁移到 ASP.NET Core MVC](xref:migration/mvc)。 在本文中，我们将迁移配置。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="setup-configuration"></a>安装程序配置

ASP.NET Core 不再使用*Global.asax*和*web.config* ASP.NET 的早期版本使用的文件。 在早期版本的 ASP.NET，应用程序启动逻辑已放置在`Application_StartUp`方法内的*Global.asax*。 更高版本，在 ASP.NET MVC 中， *Startup.cs*文件包含在项目; 的根目录，并当应用程序启动时调用。 ASP.NET Core 已完全采用这种方法，通过将中的所有启动逻辑*Startup.cs*文件。

*Web.config*还在 ASP.NET Core 替换文件。 配置本身现在可以配置，应用程序启动过程中所述*Startup.cs*。 配置仍然可以利用 XML 文件，但通常 ASP.NET Core 项目将置于配置值的 JSON 格式文件，如*appsettings.json*。 ASP.NET Core 配置系统可以方便地访问环境变量，可以提供[更安全、 极其可靠的位置](xref:security/app-secrets)特定于环境的值。 这是不应签入源代码管理的 API 密钥连接字符串等机密的尤其如此。 请参阅[配置](xref:fundamentals/configuration/index)若要了解有关 ASP.NET Core 中配置的详细信息。

有关本文中，我们开始使用从部分已迁移的 ASP.NET Core 项目[上一篇文章](xref:migration/mvc)。 若要设置配置，请添加以下构造函数和属性设置为*Startup.cs*文件位于项目根目录中：

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

请注意，此时， *Startup.cs*文件将无法编译，因为我们仍需要添加以下`using`语句：

```csharp
using Microsoft.Extensions.Configuration;
```

添加*appsettings.json*到使用合适的项目模板的项目的根目录的文件：

![添加 AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>从 web.config 中迁移的配置设置

我们的 ASP.NET MVC 项目包含中的所需的数据库连接字符串*web.config*，请在`<connectionStrings>`元素。 在我们 ASP.NET Core 项目中，我们将存储此信息在*appsettings.json*文件。 打开*appsettings.json*，并记下它已包含以下：

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

突出显示的行上面所示，在将更改从数据库的名称 **_CHANGE_ME**到数据库的名称。

## <a name="summary"></a>总结

ASP.NET Core 将置于单个文件，在其中的必要的服务和依赖项可以定义和配置应用程序的所有启动逻辑。 它将替换*web.config*具有一种灵活的配置功能，可以利用各种文件格式，如 JSON，以及环境变量文件。
