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
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="ab69e-103">ASP.NET核心的身份验证示例</span><span class="sxs-lookup"><span data-stu-id="ab69e-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="ab69e-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ab69e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab69e-105">[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)在*AspNetCore/src/安全/示例*文件夹中包含以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="ab69e-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="ab69e-106">声明转换</span><span class="sxs-lookup"><span data-stu-id="ab69e-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="ab69e-107">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="ab69e-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="ab69e-108">自定义策略提供程序 - 授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="ab69e-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="ab69e-109">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="ab69e-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="ab69e-110">外部索赔</span><span class="sxs-lookup"><span data-stu-id="ab69e-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="ab69e-111">根据请求在 Cookie 和另一个身份验证方案之间进行选择</span><span class="sxs-lookup"><span data-stu-id="ab69e-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="ab69e-112">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="ab69e-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="ab69e-113">运行示例</span><span class="sxs-lookup"><span data-stu-id="ab69e-113">Run the samples</span></span>

* <span data-ttu-id="ab69e-114">选择[分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="ab69e-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="ab69e-115">例如： `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="ab69e-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="ab69e-116">克隆或下载[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="ab69e-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="ab69e-117">验证已安装与 ASP.NET 核心存储库的克隆相匹配的[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)版本。</span><span class="sxs-lookup"><span data-stu-id="ab69e-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="ab69e-118">导航到*AspNetCore/src/Security/示例*中的示例，然后使用`dotnet run`运行示例。</span><span class="sxs-lookup"><span data-stu-id="ab69e-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ab69e-119">[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)在*AspNetCore/src/安全/示例*文件夹中包含以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="ab69e-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="ab69e-120">声明转换</span><span class="sxs-lookup"><span data-stu-id="ab69e-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="ab69e-121">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="ab69e-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="ab69e-122">自定义策略提供程序 - 授权策略提供程序</span><span class="sxs-lookup"><span data-stu-id="ab69e-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="ab69e-123">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="ab69e-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="ab69e-124">外部索赔</span><span class="sxs-lookup"><span data-stu-id="ab69e-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="ab69e-125">根据请求在 Cookie 和另一个身份验证方案之间进行选择</span><span class="sxs-lookup"><span data-stu-id="ab69e-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="ab69e-126">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="ab69e-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="ab69e-127">运行示例</span><span class="sxs-lookup"><span data-stu-id="ab69e-127">Run the samples</span></span>

* <span data-ttu-id="ab69e-128">选择[分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="ab69e-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="ab69e-129">例如： `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="ab69e-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="ab69e-130">克隆或下载[ASP.NET核心存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="ab69e-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="ab69e-131">验证已安装与 ASP.NET 核心存储库的克隆相匹配的[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)版本。</span><span class="sxs-lookup"><span data-stu-id="ab69e-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="ab69e-132">导航到*AspNetCore/src/Security/示例*中的示例，然后使用`dotnet run`运行示例。</span><span class="sxs-lookup"><span data-stu-id="ab69e-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
