---
title: 基于 ASP.NET Core 项目使用单个用户帐户创建项目
author: rick-anderson
description: 发现基于 ASP.NET Core 项目使用单个用户帐户创建的文章。
ms.author: riande
ms.date: 12/11/2019
uid: security/authentication/individual
ms.openlocfilehash: 7ef0d5eabded61d04fb9fe7be384a663ad7ea5f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828706"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>基于 ASP.NET Core 项目使用单个用户帐户创建项目

ASP.NET Core 标识包含在 Visual Studio 中使用"单个用户帐户"选项的项目模板中。

中使用的.NET Core CLI 中可用的身份验证模板`-au Individual`:

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

对于 web API 身份验证，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5833)。

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

## <a name="dotnet-new-webapp-authentication-options"></a>dotnet new webapp authentication 选项

下表显示了可用于新 web 应用的身份验证选项：

| 选项 | 身份验证类型 | 有关详细信息的链接 |
 | ----------------- | ------------ | ---------- |
| 无            |  不进行身份验证 | | 
| 个人      |  单个身份验证 | <xref:security/authentication/identity>
| IndividualB2C   |  Azure AD B2C 的云托管的单个身份验证 | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  单个租户的组织身份验证 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  多个租户的组织身份验证 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 身份验证 | [Windows 身份验证](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Visual Studio new webapp authentication 选项

下表显示了使用 Visual Studio 创建新的 web 应用时可用的身份验证选项：

| 选项 | 身份验证类型 | 有关详细信息的链接 |
 | ----------------- | ------------ | ---------- |
| 无            |  不进行身份验证 | | 
| 应用中的单个用户帐户/存储用户帐户 |  单个身份验证 | <xref:security/authentication/identity> |
| 单个用户帐户/连接到云中的现有用户存储 |  Azure AD B2C 的云托管的单个身份验证 | [Azure AD B2C](/azure/active-directory-b2c/) |
| 工作或学校云/单个组织  |  单个租户的组织身份验证 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| 工作或学校云/多个组织 |  多个租户的组织身份验证 | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows 身份验证 | [Windows 身份验证](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>其他资源

以下文章演示了如何使用 ASP.NET Core 使用单个用户帐户的模板中生成的代码：

* [ASP.NET Core 中的帐户确认和密码恢复](xref:security/authentication/accconfirm)
* [使用受授权的用户数据创建 ASP.NET Core 应用](xref:security/authorization/secure-data)
