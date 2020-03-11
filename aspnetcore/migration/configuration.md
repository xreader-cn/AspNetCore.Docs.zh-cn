---
title: 将配置迁移到 ASP.NET Core
author: ardalis
description: 了解如何将配置从 ASP.NET MVC 项目迁移到 ASP.NET Core MVC 项目。
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: 2c50ea768a42aa38d14c55d8c403fea4176b3650
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651882"
---
# <a name="migrate-configuration-to-aspnet-core"></a>将配置迁移到 ASP.NET Core

作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)

在前面的文章中，我们开始将[ASP.NET mvc 项目迁移到 ASP.NET CORE mvc](xref:migration/mvc)。 本文将迁移配置。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="setup-configuration"></a>设置配置

ASP.NET Core 不再使用以前版本的 ASP.NET 使用的*global.asax* *和 web.config 文件。* 在早期版本的 ASP.NET 中，应用程序启动逻辑放置在*global.asax*内的 `Application_StartUp` 方法中。 稍后，在 ASP.NET MVC 中， *Startup.cs*文件包含在项目的根目录中;并在应用程序启动时调用。 ASP.NET Core 通过将所有启动逻辑放在*Startup.cs*文件中来完全采用这种方法。

Web.config*文件也*已替换为 ASP.NET Core。 配置本身现在可以配置为*Startup.cs*中所述的应用程序启动过程的一部分。 配置仍可利用 XML 文件，但通常 ASP.NET Core 项目会将配置值放入 JSON 格式的文件中，如*appsettings*。 ASP.NET Core 的配置系统还可以轻松地访问环境变量，从而为特定于环境的值提供[更安全、更可靠的位置](xref:security/app-secrets)。 对于不应签入源控件的机密（如连接字符串和 API 密钥），尤其如此。 若要详细了解 ASP.NET Core 中的配置，请参阅[配置](xref:fundamentals/configuration/index)。

对于本文，我们将从[上一篇文章](xref:migration/mvc)中的部分迁移 ASP.NET Core 项目开始。 若要设置配置，请将以下构造函数和属性添加到位于项目根目录中的*Startup.cs*文件：

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

请注意，此时， *Startup.cs*文件不会进行编译，因为我们仍需要添加以下 `using` 语句：

```csharp
using Microsoft.Extensions.Configuration;
```

使用适当的项模板，将*appsettings*文件添加到项目的根目录：

![添加 AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>从 web.config 迁移配置设置

在 `<connectionStrings>` 元素中，我们的 ASP.NET MVC 项目包含*web.config 中*所需的数据库连接字符串。 在 ASP.NET Core 项目中，我们要将此信息存储在*appsettings*文件中。 打开*appsettings*，注意它已经包含以下内容：

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

在上面所示的突出显示的行中，将数据库的名称从 **_CHANGE_ME**更改为数据库的名称。

## <a name="summary"></a>总结

ASP.NET Core 将置于单个文件，在其中的必要的服务和依赖项可以定义和配置应用程序的所有启动逻辑。 它*将 web.config 文件替换*为灵活的配置功能，该功能可利用各种文件格式（如 JSON）以及环境变量。
