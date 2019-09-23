---
title: ASP.NET Core 的身份验证示例
author: rick-anderson
description: 提供指向 ASP.NET Core 存储库中的身份验证示例的链接。
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: d49aef198e926d88f1a6727f84b06f0861c8812d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187296"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="f6fff-103">ASP.NET Core 的身份验证示例</span><span class="sxs-lookup"><span data-stu-id="f6fff-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="f6fff-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f6fff-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6fff-105">[ASP.NET Core 存储库](https://github.com/aspnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="f6fff-105">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f6fff-106">声明转换</span><span class="sxs-lookup"><span data-stu-id="f6fff-106">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="f6fff-107">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="f6fff-107">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Cookies)
* [<span data-ttu-id="f6fff-108">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f6fff-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f6fff-109">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="f6fff-109">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="f6fff-110">外部声明</span><span class="sxs-lookup"><span data-stu-id="f6fff-110">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="f6fff-111">选择 cookie 和其他基于请求的身份验证方案</span><span class="sxs-lookup"><span data-stu-id="f6fff-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f6fff-112">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="f6fff-112">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f6fff-113">运行示例</span><span class="sxs-lookup"><span data-stu-id="f6fff-113">Run the samples</span></span>

* <span data-ttu-id="f6fff-114">选择[分支](https://github.com/aspnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="f6fff-114">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="f6fff-115">例如，`Tag:v3.0.0`</span><span class="sxs-lookup"><span data-stu-id="f6fff-115">For example, `Tag:v3.0.0`</span></span>
* <span data-ttu-id="f6fff-116">克隆或下载[ASP.NET Core 存储库](https://github.com/aspnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="f6fff-116">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="f6fff-117">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://www.microsoft.com/net/download/all)版本。</span><span class="sxs-lookup"><span data-stu-id="f6fff-117">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f6fff-118">导航到*AspNetCore/src/Security/samples*中的示例，并使用`dotnet run`运行该示例。</span><span class="sxs-lookup"><span data-stu-id="f6fff-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6fff-119">[ASP.NET Core 存储库](https://github.com/aspnet/AspNetCore)包含*AspNetCore/src/Security/samples*文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="f6fff-119">The [ASP.NET Core repository](https://github.com/aspnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f6fff-120">声明转换</span><span class="sxs-lookup"><span data-stu-id="f6fff-120">Claims transformation</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="f6fff-121">Cookie 身份验证</span><span class="sxs-lookup"><span data-stu-id="f6fff-121">Cookie authentication</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="f6fff-122">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f6fff-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f6fff-123">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="f6fff-123">Dynamic authentication schemes and options</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="f6fff-124">外部声明</span><span class="sxs-lookup"><span data-stu-id="f6fff-124">External claims</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="f6fff-125">选择 cookie 和其他基于请求的身份验证方案</span><span class="sxs-lookup"><span data-stu-id="f6fff-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f6fff-126">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="f6fff-126">Restricts access to static files</span></span>](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f6fff-127">运行示例</span><span class="sxs-lookup"><span data-stu-id="f6fff-127">Run the samples</span></span>

* <span data-ttu-id="f6fff-128">选择[分支](https://github.com/aspnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="f6fff-128">Select a [branch](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="f6fff-129">例如，`release/2.2`</span><span class="sxs-lookup"><span data-stu-id="f6fff-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="f6fff-130">克隆或下载[ASP.NET Core 存储库](https://github.com/aspnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="f6fff-130">Clone or download the [ASP.NET Core repository](https://github.com/aspnet/AspNetCore).</span></span>
* <span data-ttu-id="f6fff-131">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的[.NET Core SDK](https://www.microsoft.com/net/download/all)版本。</span><span class="sxs-lookup"><span data-stu-id="f6fff-131">Verify you have installed the [.NET Core SDK](https://www.microsoft.com/net/download/all) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f6fff-132">导航到*AspNetCore/src/Security/samples*中的示例，并使用`dotnet run`运行该示例。</span><span class="sxs-lookup"><span data-stu-id="f6fff-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
