---
title: ASP.NET Core 中的策略方案
author: rick-anderson
description: 使用身份验证策略方案，可以更轻松地创建一个逻辑身份验证方案
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
uid: security/authentication/policyschemes
ms.openlocfilehash: 60ac9914ef811a705c61ab3b2bec61643acc6ec0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634977"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="7edfe-103">ASP.NET Core 中的策略方案</span><span class="sxs-lookup"><span data-stu-id="7edfe-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="7edfe-104">使用身份验证策略方案，可以更方便地使用多种方法。</span><span class="sxs-lookup"><span data-stu-id="7edfe-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="7edfe-105">例如，策略方案可能会使用 Google 身份验证，并对 cookie 其他所有内容进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="7edfe-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="7edfe-106">身份验证策略方案：</span><span class="sxs-lookup"><span data-stu-id="7edfe-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="7edfe-107">可以轻松地将任何身份验证操作转发到另一个方案。</span><span class="sxs-lookup"><span data-stu-id="7edfe-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="7edfe-108">根据请求动态转发。</span><span class="sxs-lookup"><span data-stu-id="7edfe-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="7edfe-109">使用派生的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> 和关联的[AuthenticationHandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)的所有身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="7edfe-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="7edfe-110">是 ASP.NET Core 2.1 及更高版本中自动的策略方案。</span><span class="sxs-lookup"><span data-stu-id="7edfe-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="7edfe-111">可以通过配置方案的选项来启用。</span><span class="sxs-lookup"><span data-stu-id="7edfe-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="7edfe-112">示例</span><span class="sxs-lookup"><span data-stu-id="7edfe-112">Examples</span></span>

<span data-ttu-id="7edfe-113">下面的示例演示了结合较低级别方案的更高级别的方案。</span><span class="sxs-lookup"><span data-stu-id="7edfe-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="7edfe-114">Google 身份验证用于质询，而 cookie 身份验证用于所有其他操作：</span><span class="sxs-lookup"><span data-stu-id="7edfe-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="7edfe-115">下面的示例基于每个请求启用动态选择方案。</span><span class="sxs-lookup"><span data-stu-id="7edfe-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="7edfe-116">也就是说，如何混合使用 cookie 和 API 身份验证：</span><span class="sxs-lookup"><span data-stu-id="7edfe-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
