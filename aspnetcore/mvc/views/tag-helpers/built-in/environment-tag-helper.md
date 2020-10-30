---
title: ASP.NET Core 中的环境标记帮助程序
author: pkellner
description: 定义的 ASP.NET Core 环境标记帮助程序（包括所有属性）
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 4f8330521bb9114f6639c1889cc15ebd18adc0ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061127"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="365e8-103">ASP.NET Core 中的环境标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="365e8-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="365e8-104">作者：[Peter Kellner](https://peterkellner.net) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="365e8-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="365e8-105">环境标记帮助程序根据当前 [宿主环境](xref:fundamentals/environments)有条件地呈现其包含的内容。</span><span class="sxs-lookup"><span data-stu-id="365e8-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="365e8-106">环境标记帮助程序的单个属性 `names` 是以逗号分隔的环境名称列表。</span><span class="sxs-lookup"><span data-stu-id="365e8-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="365e8-107">任何提供的环境名称与当前环境匹配时，都会呈现包含的内容。</span><span class="sxs-lookup"><span data-stu-id="365e8-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="365e8-108">有关标记帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="365e8-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="365e8-109">环境标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="365e8-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="365e8-110">姓名</span><span class="sxs-lookup"><span data-stu-id="365e8-110">names</span></span>

<span data-ttu-id="365e8-111">`names` 采用单个宿主环境名称或以逗号分隔的宿主环境名称列表，用于触发已包含内容的呈现。</span><span class="sxs-lookup"><span data-stu-id="365e8-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="365e8-112">将环境值与 [ IHostingEnvironment.EnvironmentName ](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*) 返回的当前值进行比较。</span><span class="sxs-lookup"><span data-stu-id="365e8-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="365e8-113">比较不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="365e8-113">The comparison ignores case.</span></span>

<span data-ttu-id="365e8-114">下面的示例使用图像标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="365e8-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="365e8-115">如果宿主环境是暂存或生产，则呈现内容：</span><span class="sxs-lookup"><span data-stu-id="365e8-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="365e8-116">include 和 exclude 属性</span><span class="sxs-lookup"><span data-stu-id="365e8-116">include and exclude attributes</span></span>

<span data-ttu-id="365e8-117">`include`& `exclude` 属性控件基于包含或排除的宿主环境名称呈现包含的内容。</span><span class="sxs-lookup"><span data-stu-id="365e8-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="365e8-118">include</span><span class="sxs-lookup"><span data-stu-id="365e8-118">include</span></span>

<span data-ttu-id="365e8-119">`include` 属性表现出与 `names` 属性相似的行为。</span><span class="sxs-lookup"><span data-stu-id="365e8-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="365e8-120">`include` 属性值中列出的环境必须与应用程序的托管环境 ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) 匹配才能呈现 `<environment>` 标记的内容。</span><span class="sxs-lookup"><span data-stu-id="365e8-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="365e8-121">排除</span><span class="sxs-lookup"><span data-stu-id="365e8-121">exclude</span></span>

<span data-ttu-id="365e8-122">与 `include` 属性相反，当托管环境与 `exclude` 属性值中列出的环境不匹配时，将呈现 `<environment>` 标记的内容。</span><span class="sxs-lookup"><span data-stu-id="365e8-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="365e8-123">其他资源</span><span class="sxs-lookup"><span data-stu-id="365e8-123">Additional resources</span></span>

* <xref:fundamentals/environments>
