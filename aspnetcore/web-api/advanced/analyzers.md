---
title: 使用 Web API 分析器
author: pranavkm
description: 了解有关 ASP.NET Core MVC Web API 分析器包的信息。
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/analyzers
ms.openlocfilehash: 571046052dbe131e9cdcf981aaee0921ed8c2ea1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021843"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="bd3d7-103">使用 Web API 分析器</span><span class="sxs-lookup"><span data-stu-id="bd3d7-103">Use web API analyzers</span></span>

<span data-ttu-id="bd3d7-104">ASP.NET Core 2.2 和更高版本提供了用于 Web API 项目的 MVC 分析器包。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="bd3d7-105">分析器使用带有 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 批注的控制器，同时构建 [Web API 约定](xref:web-api/advanced/conventions)。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="bd3d7-106">分析器包会通知你执行以下操作的任何控制器操作：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="bd3d7-107">返回未声明的状态代码。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="bd3d7-108">返回未声明的成功结果。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="bd3d7-109">记录不返回的状态代码。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="bd3d7-110">包含显式模型验证检查。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="bd3d7-111">引用分析器包</span><span class="sxs-lookup"><span data-stu-id="bd3d7-111">Reference the analyzer package</span></span>

<span data-ttu-id="bd3d7-112">在 ASP.NET Core 3.0 或更高版本中，分析器随附在 .NET Core SDK 中。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="bd3d7-113">若要在项目中启用分析器，请在项目文件中包含 `IncludeOpenAPIAnalyzers` 属性：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="bd3d7-114">包安装</span><span class="sxs-lookup"><span data-stu-id="bd3d7-114">Package installation</span></span>

<span data-ttu-id="bd3d7-115">通过以下方法之一安装 [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="bd3d7-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd3d7-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd3d7-117">从“程序包管理器控制台”窗口：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="bd3d7-118">请参阅**查看** > **其他 Windows** > **程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="bd3d7-119">导航到 ApiConventions.csproj 文件所在的目录\*\*。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="bd3d7-120">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd3d7-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bd3d7-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bd3d7-122">右键单击*Packages* **Solution Pad** > **添加包 ...**"中的" 包 "文件夹。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="bd3d7-123">将 "**添加包**" 窗口的 "**源**" 下拉箭头设置为 "nuget.org"。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="bd3d7-124">在搜索框中输入“Microsoft.AspNetCore.Mvc.Api.Analyzers”。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="bd3d7-125">从结果窗格中选择“Microsoft.AspNetCore.Mvc.Api.Analyzers”包，然后单击“添加包”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="bd3d7-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd3d7-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bd3d7-127">从“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-127">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[<span data-ttu-id="bd3d7-128">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd3d7-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bd3d7-129">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-129">Run the following command:</span></span>

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="bd3d7-130">Web API 约定的分析器</span><span class="sxs-lookup"><span data-stu-id="bd3d7-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="bd3d7-131">OpenAPI 文档包含操作可能返回的状态代码和响应类型。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="bd3d7-132">在 ASP.NET Core MVC 中，<xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> 和 <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> 等属性用于记录操作。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="bd3d7-133"><xref:tutorials/web-api-help-pages-using-swagger> 进一步介绍有关记录 Web API 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="bd3d7-134">包中的其中一个分析器检查使用 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> 进行批注的控制器，并标识不完全记录其响应的操作。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="bd3d7-135">请考虑以下示例：</span><span class="sxs-lookup"><span data-stu-id="bd3d7-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="bd3d7-136">上述操作记录了 HTTP 200 成功返回类型，但未记录 HTTP 404 失败状态代码。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="bd3d7-137">分析器将 HTTP 404 状态代码的缺失文档报告为警告。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="bd3d7-138">提供了修复此问题的选项。</span><span class="sxs-lookup"><span data-stu-id="bd3d7-138">An option to fix the problem is provided.</span></span>

![报告警告的分析器](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="bd3d7-140">其他资源</span><span class="sxs-lookup"><span data-stu-id="bd3d7-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
