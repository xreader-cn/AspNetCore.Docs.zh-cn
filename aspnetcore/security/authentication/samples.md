---
title: ASP.NET Core 的身份验证示例
author: rick-anderson
description: 提供指向 ASP.NET Core 存储库中的身份验证示例的链接。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 3d7e28f6e501bd8bd3908ca4b314a63cee52ebe3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651654"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core 的身份验证示例

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：

* [声明转换](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [Cookie 身份验证](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [自定义策略提供程序-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [动态身份验证方案和选项](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [外部声明](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [选择 cookie 和其他基于请求的身份验证方案](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [限制对静态文件的访问](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>运行示例

* 选择[分支](https://github.com/dotnet/AspNetCore)。 例如： `Tag:v3.0.0`
* 克隆或下载[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。
* 验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://www.microsoft.com/net/download/all)版本。
* 导航到*AspNetCore/src/Security/samples*中的示例，并使用 `dotnet run`运行该示例。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：

* [声明转换](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Cookie 身份验证](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [自定义策略提供程序-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [动态身份验证方案和选项](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [外部声明](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [选择 cookie 和其他基于请求的身份验证方案](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [限制对静态文件的访问](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>运行示例

* 选择[分支](https://github.com/dotnet/AspNetCore)。 例如： `release/2.2`
* 克隆或下载[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。
* 验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://www.microsoft.com/net/download/all)版本。
* 导航到*AspNetCore/src/Security/samples*中的示例，并使用 `dotnet run`运行该示例。

::: moniker-end
