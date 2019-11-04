---
title: 基于通过单独用户帐户创建的 ASP.NET Core 项目的项目
author: rick-anderson
description: 基于通过单独用户帐户创建的 ASP.NET Core 项目发现文章。
ms.author: riande
ms.date: 11/30/2017
uid: security/authentication/individual
ms.openlocfilehash: 91c5665dc50124b3ba09bdcfbf3ba501f684c604
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463031"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>基于通过单独用户帐户创建的 ASP.NET Core 项目的项目

ASP.NET Core 标识包含在 Visual Studio 中具有 "单独用户帐户" 选项的项目模板中。

使用 `-au Individual`的 .NET Core CLI 提供身份验证模板：

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

对于 web API 身份验证，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/5833)。

<a name="no"></a>

## <a name="no-authentication"></a>无身份验证

身份验证在 .NET Core CLI 中通过 `-au` 选项指定。 在 Visual Studio 中，"**更改身份验证**" 对话框可用于新的 web 应用程序。 Visual Studio 中新 web 应用的默认值为 "**无身份验证**"。

用无身份验证创建的项目：

* 不要包含用于登录和注销的网页和 UI。
* 不包含身份验证代码。

<a name="win"></a>

## <a name="windows-authentication"></a>Windows 身份验证

Windows 身份验证是通过 `-au Windows` 选项在 .NET Core CLI 中为新 web 应用指定的。 在 Visual Studio 中，"**更改身份验证**" 对话框提供**Windows 身份验证**选项。

如果选择了 "Windows 身份验证"，则会将应用程序配置为使用[Windows 身份验证 IIS 模块](xref:host-and-deploy/iis/modules)。 Windows 身份验证适用于 Intranet 网站。

## <a name="additional-resources"></a>其他资源

以下文章演示了如何使用 ASP.NET Core 使用单个用户帐户的模板中生成的代码：

* [ASP.NET Core 中的帐户确认和密码恢复](xref:security/authentication/accconfirm)
* [使用授权保护的用户数据创建 ASP.NET Core 应用](xref:security/authorization/secure-data)
