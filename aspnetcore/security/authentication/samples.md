---
title: ASP.NET Core 的身份验证示例
author: rick-anderson
description: 提供指向 ASP.NET Core 存储库中的身份验证示例的链接。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b013d02a65e752bbb61a87a0bf502785125378a2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511608"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="732bc-103">ASP.NET Core 的身份验证示例</span><span class="sxs-lookup"><span data-stu-id="732bc-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="732bc-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="732bc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="732bc-105">[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="732bc-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="732bc-106">声明转换</span><span class="sxs-lookup"><span data-stu-id="732bc-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="732bc-107">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="732bc-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="732bc-108">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="732bc-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="732bc-109">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="732bc-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="732bc-110">外部声明</span><span class="sxs-lookup"><span data-stu-id="732bc-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="732bc-111">选择 cookie 和其他基于请求的身份验证方案</span><span class="sxs-lookup"><span data-stu-id="732bc-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="732bc-112">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="732bc-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="732bc-113">运行示例</span><span class="sxs-lookup"><span data-stu-id="732bc-113">Run the samples</span></span>

* <span data-ttu-id="732bc-114">选择[分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="732bc-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="732bc-115">例如： `Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="732bc-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="732bc-116">克隆或下载[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="732bc-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="732bc-117">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)版本。</span><span class="sxs-lookup"><span data-stu-id="732bc-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="732bc-118">导航到*AspNetCore/src/Security/samples*中的示例，并使用 `dotnet run`运行该示例。</span><span class="sxs-lookup"><span data-stu-id="732bc-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="732bc-119">[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="732bc-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="732bc-120">声明转换</span><span class="sxs-lookup"><span data-stu-id="732bc-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="732bc-121">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="732bc-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="732bc-122">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="732bc-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="732bc-123">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="732bc-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="732bc-124">外部声明</span><span class="sxs-lookup"><span data-stu-id="732bc-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="732bc-125">选择 cookie 和其他基于请求的身份验证方案</span><span class="sxs-lookup"><span data-stu-id="732bc-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="732bc-126">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="732bc-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="732bc-127">运行示例</span><span class="sxs-lookup"><span data-stu-id="732bc-127">Run the samples</span></span>

* <span data-ttu-id="732bc-128">选择[分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="732bc-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="732bc-129">例如： `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="732bc-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="732bc-130">克隆或下载[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="732bc-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="732bc-131">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)版本。</span><span class="sxs-lookup"><span data-stu-id="732bc-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="732bc-132">导航到*AspNetCore/src/Security/samples*中的示例，并使用 `dotnet run`运行该示例。</span><span class="sxs-lookup"><span data-stu-id="732bc-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
