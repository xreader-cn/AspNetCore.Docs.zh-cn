---
title: ASP.NET Core 中的策略方案
author: rick-anderson
description: 使用身份验证策略方案，可以更轻松地创建一个逻辑身份验证方案
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/policyschemes
ms.openlocfilehash: 63d931c926c9660f5d68d5a2ce292bf57efdb49c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053223"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="7d974-103">ASP.NET Core 中的策略方案</span><span class="sxs-lookup"><span data-stu-id="7d974-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="7d974-104">使用身份验证策略方案，可以更方便地使用多种方法。</span><span class="sxs-lookup"><span data-stu-id="7d974-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="7d974-105">例如，策略方案可能会使用 Google 身份验证，并对 :::no-loc(cookie)::: 其他所有内容进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="7d974-105">For example, a policy scheme might use Google authentication for challenges, and :::no-loc(cookie)::: authentication for everything else.</span></span> <span data-ttu-id="7d974-106">身份验证策略方案：</span><span class="sxs-lookup"><span data-stu-id="7d974-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="7d974-107">可以轻松地将任何身份验证操作转发到另一个方案。</span><span class="sxs-lookup"><span data-stu-id="7d974-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="7d974-108">根据请求动态转发。</span><span class="sxs-lookup"><span data-stu-id="7d974-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="7d974-109">使用派生的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> 和关联的[AuthenticationHandler \<TOptions> ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)的所有身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="7d974-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="7d974-110">是 ASP.NET Core 2.1 及更高版本中自动的策略方案。</span><span class="sxs-lookup"><span data-stu-id="7d974-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="7d974-111">可以通过配置方案的选项来启用。</span><span class="sxs-lookup"><span data-stu-id="7d974-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="7d974-112">示例</span><span class="sxs-lookup"><span data-stu-id="7d974-112">Examples</span></span>

<span data-ttu-id="7d974-113">下面的示例演示了结合较低级别方案的更高级别的方案。</span><span class="sxs-lookup"><span data-stu-id="7d974-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="7d974-114">Google 身份验证用于质询，而 :::no-loc(cookie)::: 身份验证用于所有其他操作：</span><span class="sxs-lookup"><span data-stu-id="7d974-114">Google authentication is used for challenges, and :::no-loc(cookie)::: authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="7d974-115">下面的示例基于每个请求启用动态选择方案。</span><span class="sxs-lookup"><span data-stu-id="7d974-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="7d974-116">也就是说，如何混合使用 :::no-loc(cookie)::: 和 API 身份验证：</span><span class="sxs-lookup"><span data-stu-id="7d974-116">That is, how to mix :::no-loc(cookie):::s and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
