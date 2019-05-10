---
title: ASP.NET Core MVC 的兼容性版本
author: rick-anderson
description: 了解 ASP.NET Core 中的 Startup 类如何配置服务和应用的请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/15/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b360da105799a1dccb1902e167e50e78864b76a9
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085890"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="aa68b-103">ASP.NET Core MVC 的兼容性版本</span><span class="sxs-lookup"><span data-stu-id="aa68b-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="aa68b-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aa68b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aa68b-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法允许应用选择加入或退出 ASP.NET Core MVC 2.1 或更高版本中引入的潜在中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="aa68b-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span> <span data-ttu-id="aa68b-106">这些潜在的中断行为变更通常取决于 MVC 子系统的行为方式以及运行时调用“代码”的方式。</span><span class="sxs-lookup"><span data-stu-id="aa68b-106">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="aa68b-107">通过选择加入，你将获取最新的行为以及 ASP.NET Core 的长期行为。</span><span class="sxs-lookup"><span data-stu-id="aa68b-107">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="aa68b-108">以下代码将兼容模式设置为 ASP.NET Core 2.2：</span><span class="sxs-lookup"><span data-stu-id="aa68b-108">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="aa68b-109">建议使用最新版本 (`CompatibilityVersion.Version_2_2`) 来测试应用。</span><span class="sxs-lookup"><span data-stu-id="aa68b-109">We recommend you test your app using the latest version (`CompatibilityVersion.Version_2_2`).</span></span> <span data-ttu-id="aa68b-110">我们预计大多数应用不会使用最新版本进行中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="aa68b-110">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="aa68b-111">调用 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` 的应用会被阻止进行 ASP.NET Core 2.1 MVC 和更高的 2.x 版本中引入的潜在中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="aa68b-111">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1 MVC and later 2.x versions.</span></span> <span data-ttu-id="aa68b-112">该阻止操作：</span><span class="sxs-lookup"><span data-stu-id="aa68b-112">This protection:</span></span>

* <span data-ttu-id="aa68b-113">不适用于所有 2.1 和更高版本的更改，它的目标是潜在地中断 MVC 子系统中的 ASP.NET Core 运行时行为变更。</span><span class="sxs-lookup"><span data-stu-id="aa68b-113">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="aa68b-114">不会扩展到下一个主要版本。</span><span class="sxs-lookup"><span data-stu-id="aa68b-114">Does not extend to the next major version.</span></span>

<span data-ttu-id="aa68b-115">未调用 `SetCompatibilityVersion` 的 ASP.NET Core 2.1 和更高 2.x 版本的应用的默认兼容性是 2.0 兼容性。</span><span class="sxs-lookup"><span data-stu-id="aa68b-115">The default compatibility for ASP.NET Core 2.1 and later 2.x apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="aa68b-116">即，未调用 `SetCompatibilityVersion` 与调用 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` 相同。</span><span class="sxs-lookup"><span data-stu-id="aa68b-116">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="aa68b-117">以下代码将兼容模式设置为 ASP.NET Core 2.2（以下行为除外）：</span><span class="sxs-lookup"><span data-stu-id="aa68b-117">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="aa68b-118">对于遇到中断行为变更的应用，请使用适当的兼容性开关：</span><span class="sxs-lookup"><span data-stu-id="aa68b-118">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="aa68b-119">允许使用最新版本并选择退出特定的中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="aa68b-119">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="aa68b-120">请用些时间更新应用，以便其适用于最新更改。</span><span class="sxs-lookup"><span data-stu-id="aa68b-120">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="aa68b-121"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> 文件很好地解释了更改的内容以及为什么更改对大多数用户来说是一种改进。</span><span class="sxs-lookup"><span data-stu-id="aa68b-121">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="aa68b-122">将来会推出 [ASP.NET Core 3.0 版本](https://github.com/aspnet/Home/wiki/Roadmap)。</span><span class="sxs-lookup"><span data-stu-id="aa68b-122">At some future date, there will be an [ASP.NET Core 3.0 version](https://github.com/aspnet/Home/wiki/Roadmap).</span></span> <span data-ttu-id="aa68b-123">在 3.0 版本中，将删除兼容性开关支持的旧行为。</span><span class="sxs-lookup"><span data-stu-id="aa68b-123">Old behaviors supported by compatibility switches will be removed in the 3.0 version.</span></span> <span data-ttu-id="aa68b-124">我们认为这些积极的变化几乎使所有用户受益。</span><span class="sxs-lookup"><span data-stu-id="aa68b-124">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="aa68b-125">现在通过引入这些更改，大多数应用可以立即受益，其他人员将有时间更新其应用。</span><span class="sxs-lookup"><span data-stu-id="aa68b-125">By introducing these changes now, most apps can benefit now, and the others will have time to update their apps.</span></span>
