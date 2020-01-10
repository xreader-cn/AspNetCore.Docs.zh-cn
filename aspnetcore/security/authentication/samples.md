---
title: ASP.NET Core 的身份验证示例
author: rick-anderson
description: 提供指向 ASP.NET Core 存储库中的身份验证示例的链接。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: 3d7e28f6e501bd8bd3908ca4b314a63cee52ebe3
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828667"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="cf877-103">ASP.NET Core 的身份验证示例</span><span class="sxs-lookup"><span data-stu-id="cf877-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="cf877-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cf877-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf877-105">[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="cf877-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="cf877-106">声明转换</span><span class="sxs-lookup"><span data-stu-id="cf877-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="cf877-107">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="cf877-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="cf877-108">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="cf877-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="cf877-109">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="cf877-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="cf877-110">外部声明</span><span class="sxs-lookup"><span data-stu-id="cf877-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="cf877-111">选择 cookie 和其他基于请求的身份验证方案</span><span class="sxs-lookup"><span data-stu-id="cf877-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="cf877-112">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="cf877-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="cf877-113">运行示例</span><span class="sxs-lookup"><span data-stu-id="cf877-113">Run the samples</span></span>

* <span data-ttu-id="cf877-114">选择[分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="cf877-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="cf877-115">例如，`Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="cf877-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="cf877-116">克隆或下载[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="cf877-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="cf877-117">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://www.microsoft.com/net/download/all)版本。</span><span class="sxs-lookup"><span data-stu-id="cf877-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="cf877-118">导航到*AspNetCore/src/Security/samples*中的示例，并使用 `dotnet run`运行该示例。</span><span class="sxs-lookup"><span data-stu-id="cf877-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf877-119">[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="cf877-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="cf877-120">声明转换</span><span class="sxs-lookup"><span data-stu-id="cf877-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="cf877-121">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="cf877-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="cf877-122">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="cf877-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="cf877-123">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="cf877-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="cf877-124">外部声明</span><span class="sxs-lookup"><span data-stu-id="cf877-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="cf877-125">选择 cookie 和其他基于请求的身份验证方案</span><span class="sxs-lookup"><span data-stu-id="cf877-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="cf877-126">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="cf877-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="cf877-127">运行示例</span><span class="sxs-lookup"><span data-stu-id="cf877-127">Run the samples</span></span>

* <span data-ttu-id="cf877-128">选择[分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="cf877-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="cf877-129">例如，`release/2.2`</span><span class="sxs-lookup"><span data-stu-id="cf877-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="cf877-130">克隆或下载[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="cf877-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="cf877-131">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://www.microsoft.com/net/download/all)版本。</span><span class="sxs-lookup"><span data-stu-id="cf877-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="cf877-132">导航到*AspNetCore/src/Security/samples*中的示例，并使用 `dotnet run`运行该示例。</span><span class="sxs-lookup"><span data-stu-id="cf877-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
