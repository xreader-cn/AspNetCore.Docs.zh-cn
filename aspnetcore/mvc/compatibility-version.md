---
title: ASP.NET Core MVC 的兼容性版本
author: rick-anderson
description: 了解 ASP.NET Core 中的 Startup 类如何配置服务和应用的请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b29e2ee49aaf0f557f1acd0cf03e9e82d5ea0105
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654804"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="f956b-103">ASP.NET Core MVC 的兼容性版本</span><span class="sxs-lookup"><span data-stu-id="f956b-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="f956b-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f956b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f956b-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法为 ASP.NET Core 3.0 应用的无操作方法。</span><span class="sxs-lookup"><span data-stu-id="f956b-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="f956b-106">也就是说，使用 `SetCompatibilityVersion` 的任何值调用 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> 都不会影响应用程序。</span><span class="sxs-lookup"><span data-stu-id="f956b-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="f956b-107">ASP.NET Core 的下一个次版本可能提供新的 `CompatibilityVersion` 值。</span><span class="sxs-lookup"><span data-stu-id="f956b-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="f956b-108">`CompatibilityVersion` 值 `Version_2_0` 到 `Version_2_2` 被标记为 `[Obsolete(...)]`。</span><span class="sxs-lookup"><span data-stu-id="f956b-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="f956b-109">请参阅[防伪、CORS、诊断、Mvc 和路由中的重大 API 变更](https://github.com/aspnet/Announcements/issues/387)。</span><span class="sxs-lookup"><span data-stu-id="f956b-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="f956b-110">此列表包含兼容性开关的重大变更。</span><span class="sxs-lookup"><span data-stu-id="f956b-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="f956b-111">若要查看 `SetCompatibilityVersion` 如何与 ASP.NET Core 2.x 应用协同工作，请选择[这篇文章的 ASP.NET Core 2.2 版本](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="f956b-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f956b-112"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法允许 ASP.NET Core 2.x 应用选择加入或退出 ASP.NET Core MVC 2.1 或 2.2 中引入的潜在重大行为变更。</span><span class="sxs-lookup"><span data-stu-id="f956b-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="f956b-113">这些潜在的中断行为变更通常取决于 MVC 子系统的行为方式以及运行时调用“代码”的方式。</span><span class="sxs-lookup"><span data-stu-id="f956b-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="f956b-114">通过选择加入，你将获取最新的行为以及 ASP.NET Core 的长期行为。</span><span class="sxs-lookup"><span data-stu-id="f956b-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="f956b-115">以下代码将兼容模式设置为 ASP.NET Core 2.2：</span><span class="sxs-lookup"><span data-stu-id="f956b-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f956b-116">建议使用最新版本 (`CompatibilityVersion.Latest`) 来测试应用。</span><span class="sxs-lookup"><span data-stu-id="f956b-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="f956b-117">我们预计大多数应用不会使用最新版本进行中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="f956b-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="f956b-118">调用 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` 的应用会被阻止进行 ASP.NET Core 2.1/2.2 MVC 版本中引入的潜在重大行为变更。</span><span class="sxs-lookup"><span data-stu-id="f956b-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="f956b-119">该阻止操作：</span><span class="sxs-lookup"><span data-stu-id="f956b-119">This protection:</span></span>

* <span data-ttu-id="f956b-120">不适用于所有 2.1 和更高版本的更改，它的目标是潜在地中断 MVC 子系统中的 ASP.NET Core 运行时行为变更。</span><span class="sxs-lookup"><span data-stu-id="f956b-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="f956b-121">不扩展到 ASP.NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="f956b-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="f956b-122">未调用 `SetCompatibilityVersion` 的 ASP.NET Core 2.1 和 2.2 版本的应用的默认兼容性是 2.0 兼容性。</span><span class="sxs-lookup"><span data-stu-id="f956b-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="f956b-123">即，未调用 `SetCompatibilityVersion` 与调用 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` 相同。</span><span class="sxs-lookup"><span data-stu-id="f956b-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="f956b-124">以下代码将兼容模式设置为 ASP.NET Core 2.2（以下行为除外）：</span><span class="sxs-lookup"><span data-stu-id="f956b-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="f956b-125">对于遇到中断行为变更的应用，请使用适当的兼容性开关：</span><span class="sxs-lookup"><span data-stu-id="f956b-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="f956b-126">允许使用最新版本并选择退出特定的中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="f956b-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="f956b-127">请用些时间更新应用，以便其适用于最新更改。</span><span class="sxs-lookup"><span data-stu-id="f956b-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="f956b-128"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> 文件很好地解释了更改的内容以及为什么更改对大多数用户来说是一种改进。</span><span class="sxs-lookup"><span data-stu-id="f956b-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="f956b-129">对于 ASP.NET Core 3.0，已删除兼容性开关支持的旧行为。</span><span class="sxs-lookup"><span data-stu-id="f956b-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="f956b-130">我们认为这些积极的变化几乎使所有用户受益。</span><span class="sxs-lookup"><span data-stu-id="f956b-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="f956b-131">通过在 2.1 和 2.2 中引入这些更改，大多数应用都可以受益，而其他应用则有时间更新。</span><span class="sxs-lookup"><span data-stu-id="f956b-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end
