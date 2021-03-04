---
title: ASP.NET Core 的身份验证示例
author: rick-anderson
description: 提供指向 ASP.NET Core 存储库中的身份验证示例的链接。
ms.author: riande
ms.date: 02/21/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110113"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core 的身份验证示例

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[ASP.NET Core 存储库](https://github.com/dotnet/aspnetcore)包含以下 (分支) 的身份验证示例 `main` ：

* [声明转换](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* [Cookie 验证](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)
* [自定义授权失败响应](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [自定义策略提供程序-IAuthorizationPolicyProvider](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [动态身份验证方案和选项](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* [外部声明](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)
* [cookie基于请求选择和其他身份验证方案](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [限制对静态文件的访问](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a>获取并运行示例

本文中提供的示例链接提供了即将发布的 ASP.NET Core 的示例。 若要获取当前版本或以前版本的示例，请执行以下步骤：

* 选择 [ASP.NET Core 存储库](https://github.com/dotnet/aspnetcore)"的" 发布 "分支 (https://github.com/dotnet/aspnetcore) 。 例如， `release/5.0` 分支包含 ASP.NET Core 5.0 版本的示例。
* 克隆或下载 ASP.NET Core 存储库。
* 在本地系统上，验证是否安装与 ASP.NET Core 存储库的克隆相匹配的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。
* 导航到文件夹中的示例 `aspnetcore/src/Security/samples` ，并使用[ `dotnet run` 命令](/dotnet/core/tools/dotnet-run)运行示例。
