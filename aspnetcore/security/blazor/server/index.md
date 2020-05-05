---
title: 为 ASP.NET Core Blazor 服务器应用提供保护
author: guardrex
description: 了解如何将 Blazor 服务器应用作为 ASP.NET Core 应用程序来提供保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: bbd8b6fcd357b8929bf097450854d98fbea2570e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772630"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>为 ASP.NET Core Blazor 服务器应用提供保护

作者：[Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Blazor 服务器项目模板

创建项目后，可以配置 Blazor 服务器项目模板以进行身份验证。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

按照 <xref:blazor/get-started> 一文中的 Visual Studio 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。

在“创建新的 ASP.NET Core Web 应用程序”  对话框中选择“Blazor 服务器应用”  模板后，在“身份验证”  下选择“更改”  。

此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：

* **无身份验证**
* **个人用户帐户** &ndash; 可以通过以下方式存储用户帐户：
  * 使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统在应用程序中存储。
  * 使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 存储。
* **工作或学校帐户**
* **Windows 身份验证**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

按照 <xref:blazor/get-started> 一文中的 Visual Studio Code 指南操作，创建具有身份验证机制的新 Blazor 服务器项目。

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。

| 身份验证机制 | 描述 |
| ------------------------ | ----------- |
| `None`（默认值）         | 无身份验证 |
| `Individual`             | 使用 ASP.NET Core 标识将用户存储在应用程序中 |
| `IndividualB2C`          | 用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中 |
| `SingleOrg`              | 对一个租户进行组织身份验证 |
| `MultiOrg`               | 对多个租户进行组织身份验证 |
| `Windows`                | Windows 身份验证 |

使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：

* 为项目创建文件夹。
* 为该项目命名。

有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 请按照 <xref:blazor/get-started> 一文中的 Visual Studio for Mac 指南进行操作。

1. 在“配置新的 Blazor 服务器应用”  步骤中，从“身份验证”  下拉列表中选择“个人身份验证(应用内)”  。

1. 此应用是使用 ASP.NET Core 标识为存储在应用中的个人用户创建的。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

请按照 <xref:blazor/get-started> 一文中的 .NET Core CLI 指南操作，创建具有身份验证机制的新 Blazor 服务器项目：

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。

| 身份验证机制 | 描述 |
| ------------------------ | ----------- |
| `None`（默认值）         | 无身份验证 |
| `Individual`             | 使用 ASP.NET Core 标识将用户存储在应用程序中 |
| `IndividualB2C`          | 用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中 |
| `SingleOrg`              | 对一个租户进行组织身份验证 |
| `MultiOrg`               | 对多个租户进行组织身份验证 |
| `Windows`                | Windows 身份验证 |

使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：

* 为项目创建文件夹。
* 为该项目命名。

有关详细信息，请参阅 .NET Core 指南中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。

---

## <a name="secure-an-existing-app"></a>保护现有应用

Blazor 服务器应用采用与 ASP.NET Core 应用相同方式的安全配置。 有关详细信息，请参阅 <xref:security/index> 下的文章。
