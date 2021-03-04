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
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="4149a-103">ASP.NET Core 的身份验证示例</span><span class="sxs-lookup"><span data-stu-id="4149a-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="4149a-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4149a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4149a-105">[ASP.NET Core 存储库](https://github.com/dotnet/aspnetcore)包含以下 (分支) 的身份验证示例 `main` ：</span><span class="sxs-lookup"><span data-stu-id="4149a-105">The [ASP.NET Core repository](https://github.com/dotnet/aspnetcore) contains the following authentication samples (`main` branch):</span></span>

* [<span data-ttu-id="4149a-106">声明转换</span><span class="sxs-lookup"><span data-stu-id="4149a-106">Claims transformation</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="4149a-107">[Cookie 验证](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="4149a-107">[Cookie authentication](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="4149a-108">自定义授权失败响应</span><span class="sxs-lookup"><span data-stu-id="4149a-108">Custom authorization failure response</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [<span data-ttu-id="4149a-109">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="4149a-109">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="4149a-110">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="4149a-110">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="4149a-111">[外部声明](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="4149a-111">[External claims](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="4149a-112">cookie基于请求选择和其他身份验证方案</span><span class="sxs-lookup"><span data-stu-id="4149a-112">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="4149a-113">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="4149a-113">Restricts access to static files</span></span>](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a><span data-ttu-id="4149a-114">获取并运行示例</span><span class="sxs-lookup"><span data-stu-id="4149a-114">Obtain and run the samples</span></span>

<span data-ttu-id="4149a-115">本文中提供的示例链接提供了即将发布的 ASP.NET Core 的示例。</span><span class="sxs-lookup"><span data-stu-id="4149a-115">The sample links provided in this article provide samples for the upcoming release of ASP.NET Core.</span></span> <span data-ttu-id="4149a-116">若要获取当前版本或以前版本的示例，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="4149a-116">To obtain a sample for the current release or a prior release, perform the following steps:</span></span>

* <span data-ttu-id="4149a-117">选择 [ASP.NET Core 存储库](https://github.com/dotnet/aspnetcore)"的" 发布 "分支 (https://github.com/dotnet/aspnetcore) 。</span><span class="sxs-lookup"><span data-stu-id="4149a-117">Select the release branch of the [ASP.NET Core repository](https://github.com/dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore).</span></span> <span data-ttu-id="4149a-118">例如， `release/5.0` 分支包含 ASP.NET Core 5.0 版本的示例。</span><span class="sxs-lookup"><span data-stu-id="4149a-118">For example, the `release/5.0` branch contains the samples for the ASP.NET Core 5.0 release.</span></span>
* <span data-ttu-id="4149a-119">克隆或下载 ASP.NET Core 存储库。</span><span class="sxs-lookup"><span data-stu-id="4149a-119">Clone or download the ASP.NET Core repository.</span></span>
* <span data-ttu-id="4149a-120">在本地系统上，验证是否安装与 ASP.NET Core 存储库的克隆相匹配的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。</span><span class="sxs-lookup"><span data-stu-id="4149a-120">On your local system, verify installation of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="4149a-121">导航到文件夹中的示例 `aspnetcore/src/Security/samples` ，并使用[ `dotnet run` 命令](/dotnet/core/tools/dotnet-run)运行示例。</span><span class="sxs-lookup"><span data-stu-id="4149a-121">Navigate to a sample in `aspnetcore/src/Security/samples` folder and run the sample with the [`dotnet run` command](/dotnet/core/tools/dotnet-run).</span></span>
