---
title: 在 ASP.NET Core 中的策略方案
author: rick-anderson
description: 身份验证策略方案，更便于具有单一逻辑身份验证方案
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: 1a2d92e6fa54189b8154fc501b31c8a99d1f9081
ms.sourcegitcommit: 357a7120632b20465801c093e4e5bd4a315496a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649178"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="e0461-103">在 ASP.NET Core 中的策略方案</span><span class="sxs-lookup"><span data-stu-id="e0461-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="e0461-104">身份验证策略方案更加轻松地有可能使用多个方法的单一逻辑身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e0461-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="e0461-105">例如，策略方案可能使用的挑战，Google 身份验证和 cookie 身份验证的其他所有内容。</span><span class="sxs-lookup"><span data-stu-id="e0461-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="e0461-106">身份验证策略方案使其：</span><span class="sxs-lookup"><span data-stu-id="e0461-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="e0461-107">轻松地将转发到另一个方案的任何身份验证操作。</span><span class="sxs-lookup"><span data-stu-id="e0461-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="e0461-108">正向动态根据该请求。</span><span class="sxs-lookup"><span data-stu-id="e0461-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="e0461-109">使用派生的所有身份验证方案<xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions>和关联[ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="e0461-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [`AuthenticationHandler<TOptions>`](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="e0461-110">会自动在 ASP.NET Core 2.1 及更高版本的策略方案。</span><span class="sxs-lookup"><span data-stu-id="e0461-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="e0461-111">可以通过配置方案的选项来启用。</span><span class="sxs-lookup"><span data-stu-id="e0461-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="e0461-112">示例</span><span class="sxs-lookup"><span data-stu-id="e0461-112">Examples</span></span>

<span data-ttu-id="e0461-113">下面的示例演示结合了较低级别方案的更高级别方案。</span><span class="sxs-lookup"><span data-stu-id="e0461-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="e0461-114">Google 身份验证用于挑战，并为其他所有使用 cookie 身份验证：</span><span class="sxs-lookup"><span data-stu-id="e0461-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="e0461-115">下面的示例可动态选择基于每个请求的方案。</span><span class="sxs-lookup"><span data-stu-id="e0461-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="e0461-116">它是如何混合使用 cookie 和 API 身份验证：</span><span class="sxs-lookup"><span data-stu-id="e0461-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
