---
title: 使用 Web API 分析器
author: pranavkm
description: 了解 Microsoft.AspNetCore.Mvc.Api.Analyzers 中的 Web API 分析器。
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/14/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: 2aaef738ab2a64f85cb85708f63d2375c04cacb5
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538569"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="1dc39-103">使用 Web API 分析器</span><span class="sxs-lookup"><span data-stu-id="1dc39-103">Use web API analyzers</span></span>

<span data-ttu-id="1dc39-104">ASP.NET Core 2.2 和更高版本包含 [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet 包，其中包含适用于 Web API 的分析器。</span><span class="sxs-lookup"><span data-stu-id="1dc39-104">ASP.NET Core 2.2 and later includes the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package containing analyzers for web APIs.</span></span> <span data-ttu-id="1dc39-105">分析器使用带有 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 批注的控制器，同时构建 [API 约定](xref:web-api/advanced/conventions)。</span><span class="sxs-lookup"><span data-stu-id="1dc39-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [API conventions](xref:web-api/advanced/conventions).</span></span>

## <a name="package-installation"></a><span data-ttu-id="1dc39-106">包安装</span><span class="sxs-lookup"><span data-stu-id="1dc39-106">Package installation</span></span>

<span data-ttu-id="1dc39-107">可以使用以下方法之一添加 `Microsoft.AspNetCore.Mvc.Api.Analyzers`：</span><span class="sxs-lookup"><span data-stu-id="1dc39-107">`Microsoft.AspNetCore.Mvc.Api.Analyzers` can be added with one of the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1dc39-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1dc39-108">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1dc39-109">从“程序包管理器控制台”  窗口：</span><span class="sxs-lookup"><span data-stu-id="1dc39-109">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="1dc39-110">转到“视图” > “其他窗口” > “包管理器控制台”    。</span><span class="sxs-lookup"><span data-stu-id="1dc39-110">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="1dc39-111">导航到 ApiConventions.csproj 文件所在的目录  。</span><span class="sxs-lookup"><span data-stu-id="1dc39-111">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="1dc39-112">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1dc39-112">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* <span data-ttu-id="1dc39-113">从“管理 NuGet 程序包”  对话框中：</span><span class="sxs-lookup"><span data-stu-id="1dc39-113">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="1dc39-114">右键单击“解决方案资源管理器” > “管理 NuGet 包”中的项目   。</span><span class="sxs-lookup"><span data-stu-id="1dc39-114">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
  * <span data-ttu-id="1dc39-115">将“包源”设置为“nuget.org”  。</span><span class="sxs-lookup"><span data-stu-id="1dc39-115">Set the **Package source** to "nuget.org".</span></span>
  * <span data-ttu-id="1dc39-116">在搜索框中输入“Microsoft.AspNetCore.Mvc.Api.Analyzers”。</span><span class="sxs-lookup"><span data-stu-id="1dc39-116">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
  * <span data-ttu-id="1dc39-117">从“浏览”选项卡中选择“Microsoft.AspNetCore.Mvc.Api.Analyzers”包，然后单击“安装”   。</span><span class="sxs-lookup"><span data-stu-id="1dc39-117">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the **Browse** tab and click **Install**.</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1dc39-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1dc39-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1dc39-119">右键单击“Solution Pad” > “添加包...”中的“包”文件夹    。</span><span class="sxs-lookup"><span data-stu-id="1dc39-119">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="1dc39-120">将“添加包”窗口的“源”下拉列表设置为“nuget.org”   。</span><span class="sxs-lookup"><span data-stu-id="1dc39-120">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="1dc39-121">在搜索框中输入“Microsoft.AspNetCore.Mvc.Api.Analyzers”。</span><span class="sxs-lookup"><span data-stu-id="1dc39-121">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="1dc39-122">从结果窗格中选择“Microsoft.AspNetCore.Mvc.Api.Analyzers”包，然后单击“添加包”  。</span><span class="sxs-lookup"><span data-stu-id="1dc39-122">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1dc39-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1dc39-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1dc39-124">从“集成终端”  中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1dc39-124">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1dc39-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1dc39-125">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1dc39-126">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="1dc39-126">Run the following command:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a><span data-ttu-id="1dc39-127">API 约定的分析器</span><span class="sxs-lookup"><span data-stu-id="1dc39-127">Analyzers for API conventions</span></span>

<span data-ttu-id="1dc39-128">OpenAPI 文档包含操作可能返回的状态代码和响应类型。</span><span class="sxs-lookup"><span data-stu-id="1dc39-128">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="1dc39-129">在 ASP.NET Core MVC 中，<xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> 和 <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> 等属性用于记录操作。</span><span class="sxs-lookup"><span data-stu-id="1dc39-129">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="1dc39-130"><xref:tutorials/web-api-help-pages-using-swagger> 进一步介绍有关记录 API 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="1dc39-130"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your API.</span></span>

<span data-ttu-id="1dc39-131">包中的其中一个分析器检查使用 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 进行批注的控制器，并标识不完全记录其响应的操作。</span><span class="sxs-lookup"><span data-stu-id="1dc39-131">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="1dc39-132">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="1dc39-132">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

<span data-ttu-id="1dc39-133">上述操作记录了 HTTP 200 成功返回类型，但未记录 HTTP 404 失败状态代码。</span><span class="sxs-lookup"><span data-stu-id="1dc39-133">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="1dc39-134">分析器将 HTTP 404 状态代码的缺失文档报告为警告。</span><span class="sxs-lookup"><span data-stu-id="1dc39-134">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="1dc39-135">提供了修复此问题的选项。</span><span class="sxs-lookup"><span data-stu-id="1dc39-135">An option to fix the problem is provided.</span></span>

![报告警告的分析器](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="1dc39-137">其他资源</span><span class="sxs-lookup"><span data-stu-id="1dc39-137">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
