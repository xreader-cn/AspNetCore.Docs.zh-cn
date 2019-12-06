---
title: 将从 ASP.NET Web API 迁移到 ASP.NET Core
author: ardalis
description: 了解如何将 web API 实现从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: c68cf83f427f53b110075168c6d5e4d021808782
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881148"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="9ef24-103">将从 ASP.NET Web API 迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ef24-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="9ef24-104">作者： [Scott Addie](https://twitter.com/scott_addie)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9ef24-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9ef24-105">ASP.NET 4.x Web API 是一种 HTTP 服务，它可达到各种客户端，包括浏览器和移动设备。</span><span class="sxs-lookup"><span data-stu-id="9ef24-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="9ef24-106">ASP.NET Core 将 ASP.NET 4.x 的 MVC 和 Web API 应用模型统一到称为 ASP.NET Core MVC 的更简单的编程模型中。</span><span class="sxs-lookup"><span data-stu-id="9ef24-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="9ef24-107">本文演示从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC 所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="9ef24-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="9ef24-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9ef24-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ef24-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="9ef24-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="9ef24-110">查看 ASP.NET 4.x Web API 项目</span><span class="sxs-lookup"><span data-stu-id="9ef24-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="9ef24-111">作为起点，本文使用[ASP.NET Web API 2 入门](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)中创建的*ProductsApp*项目。</span><span class="sxs-lookup"><span data-stu-id="9ef24-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="9ef24-112">在该项目中，简单的 ASP.NET 4.x Web API 项目配置如下。</span><span class="sxs-lookup"><span data-stu-id="9ef24-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="9ef24-113">在*Global.asax.cs*中，调用 `WebApiConfig.Register`：</span><span class="sxs-lookup"><span data-stu-id="9ef24-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="9ef24-114">`WebApiConfig` 类在*App_Start*文件夹中找到，并且具有静态 `Register` 方法：</span><span class="sxs-lookup"><span data-stu-id="9ef24-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="9ef24-115">此类配置[属性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，不过实际上它并不是在项目中使用。</span><span class="sxs-lookup"><span data-stu-id="9ef24-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="9ef24-116">它还配置 ASP.NET Web API 使用的路由表。</span><span class="sxs-lookup"><span data-stu-id="9ef24-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="9ef24-117">在这种情况下，ASP.NET 4.x Web API 需要 Url 与格式 `/api/{controller}/{id}`，`{id}` 是可选的。</span><span class="sxs-lookup"><span data-stu-id="9ef24-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="9ef24-118">*ProductsApp*项目包含一个控制器。</span><span class="sxs-lookup"><span data-stu-id="9ef24-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="9ef24-119">控制器继承自 `ApiController`，其中包含两个操作：</span><span class="sxs-lookup"><span data-stu-id="9ef24-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="9ef24-120">`ProductsController` 使用的 `Product` 模型是一个简单的类：</span><span class="sxs-lookup"><span data-stu-id="9ef24-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="9ef24-121">以下部分演示了如何将 Web API 项目迁移到 ASP.NET Core MVC。</span><span class="sxs-lookup"><span data-stu-id="9ef24-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="9ef24-122">创建目标项目</span><span class="sxs-lookup"><span data-stu-id="9ef24-122">Create destination project</span></span>

<span data-ttu-id="9ef24-123">在 Visual Studio 中完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="9ef24-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="9ef24-124">请参阅**文件** > **新**的 > **项目** >  > **Visual Studio 解决方案**中的**其他项目类型**。</span><span class="sxs-lookup"><span data-stu-id="9ef24-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="9ef24-125">选择 "**空白解决方案**"，并将解决方案命名为 " *WebAPIMigration*"。</span><span class="sxs-lookup"><span data-stu-id="9ef24-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="9ef24-126">单击 **“确定”** 按钮。</span><span class="sxs-lookup"><span data-stu-id="9ef24-126">Click the **OK** button.</span></span>
* <span data-ttu-id="9ef24-127">将现有的*ProductsApp*项目添加到解决方案。</span><span class="sxs-lookup"><span data-stu-id="9ef24-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="9ef24-128">向解决方案添加新的**ASP.NET Core Web 应用程序**项目。</span><span class="sxs-lookup"><span data-stu-id="9ef24-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="9ef24-129">从下拉选择 " **.Net Core**目标框架"，然后选择 " **API**项目" 模板。</span><span class="sxs-lookup"><span data-stu-id="9ef24-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="9ef24-130">将项目命名为 " *ProductsCore*"，然后单击 **"确定"** 按钮。</span><span class="sxs-lookup"><span data-stu-id="9ef24-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="9ef24-131">解决方案现在包含两个项目。</span><span class="sxs-lookup"><span data-stu-id="9ef24-131">The solution now contains two projects.</span></span> <span data-ttu-id="9ef24-132">以下各节介绍了如何将*ProductsApp*项目的内容迁移到*ProductsCore*项目。</span><span class="sxs-lookup"><span data-stu-id="9ef24-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="9ef24-133">迁移配置</span><span class="sxs-lookup"><span data-stu-id="9ef24-133">Migrate configuration</span></span>

<span data-ttu-id="9ef24-134">ASP.NET Core 不使用*App_Start*文件夹或*global.asax*文件，并且在发布时添加*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="9ef24-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="9ef24-135">*Startup.cs*是*global.asa*的替代，位于项目根目录中。</span><span class="sxs-lookup"><span data-stu-id="9ef24-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="9ef24-136">`Startup` 类处理所有应用启动任务。</span><span class="sxs-lookup"><span data-stu-id="9ef24-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="9ef24-137">有关更多信息，请参见<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="9ef24-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="9ef24-138">在 ASP.NET Core MVC 中，当在 `Startup.Configure`中调用 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 时，默认情况下将包含特性路由。</span><span class="sxs-lookup"><span data-stu-id="9ef24-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="9ef24-139">以下 `UseMvc` 调用替换*ProductsApp*项目的*App_Start/webapiconfig.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="9ef24-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="9ef24-140">迁移模型和控制器</span><span class="sxs-lookup"><span data-stu-id="9ef24-140">Migrate models and controllers</span></span>

<span data-ttu-id="9ef24-141">复制*ProductApp*项目的控制器及其使用的模型。</span><span class="sxs-lookup"><span data-stu-id="9ef24-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="9ef24-142">按照以下步骤进行操作：</span><span class="sxs-lookup"><span data-stu-id="9ef24-142">Follow these steps:</span></span>

1. <span data-ttu-id="9ef24-143">将*控制器/ProductsController*从原始项目复制到新项目。</span><span class="sxs-lookup"><span data-stu-id="9ef24-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="9ef24-144">将整个*模型*文件夹从原始项目复制到新的项目。</span><span class="sxs-lookup"><span data-stu-id="9ef24-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="9ef24-145">更改复制的文件的命名空间，使其与新项目名称（*ProductsCore*）相匹配。</span><span class="sxs-lookup"><span data-stu-id="9ef24-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="9ef24-146">同时调整*ProductsController.cs*中的 `using ProductsApp.Models;` 语句。</span><span class="sxs-lookup"><span data-stu-id="9ef24-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="9ef24-147">此时，生成应用会导致大量编译错误。</span><span class="sxs-lookup"><span data-stu-id="9ef24-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="9ef24-148">之所以发生这些错误是因为 ASP.NET Core 中不存在下列组件：</span><span class="sxs-lookup"><span data-stu-id="9ef24-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="9ef24-149">`ApiController` 类</span><span class="sxs-lookup"><span data-stu-id="9ef24-149">`ApiController` class</span></span>
* <span data-ttu-id="9ef24-150">`System.Web.Http` 命名空间</span><span class="sxs-lookup"><span data-stu-id="9ef24-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="9ef24-151">`IHttpActionResult` 接口</span><span class="sxs-lookup"><span data-stu-id="9ef24-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="9ef24-152">修复错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ef24-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="9ef24-153">更改`ApiController`到<xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="9ef24-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="9ef24-154">添加 `using Microsoft.AspNetCore.Mvc;` 以解析 `ControllerBase` 引用。</span><span class="sxs-lookup"><span data-stu-id="9ef24-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="9ef24-155">删除 `using System.Web.Http;`。</span><span class="sxs-lookup"><span data-stu-id="9ef24-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="9ef24-156">将 `GetProduct` 操作的返回类型从 `IHttpActionResult` 更改为 `ActionResult<Product>`。</span><span class="sxs-lookup"><span data-stu-id="9ef24-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="9ef24-157">简化 `GetProduct` 操作的 `return` 语句，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ef24-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="9ef24-158">配置路由</span><span class="sxs-lookup"><span data-stu-id="9ef24-158">Configure routing</span></span>

<span data-ttu-id="9ef24-159">按如下所示配置路由：</span><span class="sxs-lookup"><span data-stu-id="9ef24-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="9ef24-160">用以下特性标记 `ProductsController` 类：</span><span class="sxs-lookup"><span data-stu-id="9ef24-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="9ef24-161">前面的[`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性配置控制器的属性路由模式。</span><span class="sxs-lookup"><span data-stu-id="9ef24-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="9ef24-162">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)特性使特性路由成为此控制器中所有操作的要求。</span><span class="sxs-lookup"><span data-stu-id="9ef24-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="9ef24-163">特性路由支持令牌，如 `[controller]` 和 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="9ef24-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="9ef24-164">在运行时，每个标记分别替换为应用了属性的控制器或操作的名称。</span><span class="sxs-lookup"><span data-stu-id="9ef24-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="9ef24-165">这些标记减少了项目中的幻字符串的数目。</span><span class="sxs-lookup"><span data-stu-id="9ef24-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="9ef24-166">标记还可确保在应用自动重命名重构时，路由与相应的控制器和操作保持同步。</span><span class="sxs-lookup"><span data-stu-id="9ef24-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="9ef24-167">将项目的兼容模式设置为 ASP.NET Core 2.2：</span><span class="sxs-lookup"><span data-stu-id="9ef24-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="9ef24-168">上述更改：</span><span class="sxs-lookup"><span data-stu-id="9ef24-168">The preceding change:</span></span>

    * <span data-ttu-id="9ef24-169">需要在控制器级别使用 `[ApiController]` 特性。</span><span class="sxs-lookup"><span data-stu-id="9ef24-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="9ef24-170">在 ASP.NET Core 2.2 中引入了可能中断的行为。</span><span class="sxs-lookup"><span data-stu-id="9ef24-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="9ef24-171">启用 `ProductController` 操作的 HTTP Get 请求：</span><span class="sxs-lookup"><span data-stu-id="9ef24-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="9ef24-172">向 `GetAllProducts` 操作应用[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)特性。</span><span class="sxs-lookup"><span data-stu-id="9ef24-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="9ef24-173">向 `GetProduct` 操作应用 `[HttpGet("{id}")]` 特性。</span><span class="sxs-lookup"><span data-stu-id="9ef24-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="9ef24-174">前面的更改和删除未使用的 `using` 语句后， *ProductsController.cs*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ef24-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="9ef24-175">运行迁移的项目，并浏览到 `/api/products`。</span><span class="sxs-lookup"><span data-stu-id="9ef24-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="9ef24-176">此时会显示三个产品的完整列表。</span><span class="sxs-lookup"><span data-stu-id="9ef24-176">A full list of three products appears.</span></span> <span data-ttu-id="9ef24-177">浏览到 `/api/products/1`。</span><span class="sxs-lookup"><span data-stu-id="9ef24-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="9ef24-178">第一个产品随即出现。</span><span class="sxs-lookup"><span data-stu-id="9ef24-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="9ef24-179">兼容性填充码</span><span class="sxs-lookup"><span data-stu-id="9ef24-179">Compatibility shim</span></span>

<span data-ttu-id="9ef24-180">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)库提供兼容性填充码，以将 ASP.NET 4.X Web API 项目移动到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="9ef24-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="9ef24-181">兼容性填充码扩展 ASP.NET Core，以支持 ASP.NET 4.x Web API 2 中的一些约定。</span><span class="sxs-lookup"><span data-stu-id="9ef24-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="9ef24-182">本文档前面的示例移植的基本操作足以确保兼容性填充程序是不必要的。</span><span class="sxs-lookup"><span data-stu-id="9ef24-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="9ef24-183">对于较大的项目，使用兼容性填充码对于临时桥接 ASP.NET Core 与 ASP.NET 4.x Web API 2 之间的 API 间隙非常有用。</span><span class="sxs-lookup"><span data-stu-id="9ef24-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="9ef24-184">Web API 兼容性填充码旨在用作一种临时度量，以支持将大型 ASP.NET 4.x Web API 项目迁移到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="9ef24-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="9ef24-185">随着时间推移，应更新项目以使用 ASP.NET Core 模式，而不是依靠兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="9ef24-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="9ef24-186">`Microsoft.AspNetCore.Mvc.WebApiCompatShim` 中包含的兼容性功能包括：</span><span class="sxs-lookup"><span data-stu-id="9ef24-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="9ef24-187">添加 `ApiController` 类型，以便不需要更新控制器的基类型。</span><span class="sxs-lookup"><span data-stu-id="9ef24-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="9ef24-188">启用 Web API 样式的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="9ef24-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="9ef24-189">默认情况下，ASP.NET Core MVC 模型绑定函数与 ASP.NET 4.x MVC 5 的绑定函数类似。</span><span class="sxs-lookup"><span data-stu-id="9ef24-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="9ef24-190">兼容性填充码更改模型绑定，更类似于 ASP.NET 4.x Web API 2 模型绑定约定。</span><span class="sxs-lookup"><span data-stu-id="9ef24-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="9ef24-191">例如，复杂类型会自动从请求正文进行绑定。</span><span class="sxs-lookup"><span data-stu-id="9ef24-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="9ef24-192">扩展模型绑定，以便控制器操作可以采用 `HttpRequestMessage`类型的参数。</span><span class="sxs-lookup"><span data-stu-id="9ef24-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="9ef24-193">添加消息格式化程序，以允许操作返回 `HttpResponseMessage`类型的结果。</span><span class="sxs-lookup"><span data-stu-id="9ef24-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="9ef24-194">添加 Web API 2 操作可能用于提供响应的其他响应方法：</span><span class="sxs-lookup"><span data-stu-id="9ef24-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="9ef24-195">`HttpResponseMessage` 生成器：</span><span class="sxs-lookup"><span data-stu-id="9ef24-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="9ef24-196">操作结果方法：</span><span class="sxs-lookup"><span data-stu-id="9ef24-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="9ef24-197">将 `IContentNegotiator` 的实例添加到应用的依赖项注入（DI）容器，并使[WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)中与内容协商相关的类型可用。</span><span class="sxs-lookup"><span data-stu-id="9ef24-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="9ef24-198">此类类型的示例包括 `DefaultContentNegotiator` 和 `MediaTypeFormatter`。</span><span class="sxs-lookup"><span data-stu-id="9ef24-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="9ef24-199">使用兼容性填充码：</span><span class="sxs-lookup"><span data-stu-id="9ef24-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="9ef24-200">安装[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9ef24-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="9ef24-201">通过在 `Startup.ConfigureServices`中调用 `services.AddMvc().AddWebApiConventions()`，将兼容性填充程序的服务注册到应用的 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="9ef24-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="9ef24-202">使用应用的 `IApplicationBuilder.UseMvc` 调用中 `IRouteBuilder` 上的 `MapWebApiRoute` 定义特定于 web API 的路由。</span><span class="sxs-lookup"><span data-stu-id="9ef24-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ef24-203">其他资源</span><span class="sxs-lookup"><span data-stu-id="9ef24-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
