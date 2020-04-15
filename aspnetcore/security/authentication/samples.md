---
title: ASP.NET核心的身份验证示例
author: rick-anderson
description: 提供指向ASP.NET核心存储库中的身份验证示例的链接。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308210"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET核心的身份验证示例

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)在*AspNetCore/src/安全/示例*文件夹中包含以下身份验证示例：

* [声明转换](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookie 身份验证](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [自定义策略提供程序 - 授权策略提供程序](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [动态身份验证方案和选项](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [外部索赔](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [根据请求在 Cookie 和另一个身份验证方案之间进行选择](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [限制对静态文件的访问](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>运行示例

* 选择[分支](https://github.com/dotnet/AspNetCore)。 例如： `release/3.1`
* 克隆或下载[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)。
* 验证已安装与 ASP.NET 核心存储库的克隆相匹配的[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)版本。
* 导航到*AspNetCore/src/Security/示例*中的示例，然后使用`dotnet run`运行示例。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)在*AspNetCore/src/安全/示例*文件夹中包含以下身份验证示例：

* [声明转换](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Cookie 身份验证](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [自定义策略提供程序 - 授权策略提供程序](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [动态身份验证方案和选项](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [外部索赔](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [根据请求在 Cookie 和另一个身份验证方案之间进行选择](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [限制对静态文件的访问](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>运行示例

* 选择[分支](https://github.com/dotnet/AspNetCore)。 例如： `release/2.2`
* 克隆或下载[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)。
* 验证已安装与 ASP.NET 核心存储库的克隆相匹配的[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)版本。
* 导航到*AspNetCore/src/Security/示例*中的示例，然后使用`dotnet run`运行示例。

::: moniker-end
