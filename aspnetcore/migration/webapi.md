---
title: 从 ASP.NET Web API 迁移到 ASP.NET Core
author: ardalis
description: 了解如何将 web API 实现从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766584"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="584ef-103">从 ASP.NET Web API 迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="584ef-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="584ef-104">作者： [Scott Addie](https://twitter.com/scott_addie)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="584ef-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="584ef-105">ASP.NET 4.x Web API 是一种 HTTP 服务，它可达到各种客户端，包括浏览器和移动设备。</span><span class="sxs-lookup"><span data-stu-id="584ef-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="584ef-106">ASP.NET Core 将 ASP.NET 4.x 的 MVC 和 Web API 应用模型统一到称为 ASP.NET Core MVC 的更简单的编程模型中。</span><span class="sxs-lookup"><span data-stu-id="584ef-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="584ef-107">本文演示从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC 所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="584ef-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="584ef-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="584ef-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="584ef-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="584ef-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="584ef-110">查看 ASP.NET 4.x Web API 项目</span><span class="sxs-lookup"><span data-stu-id="584ef-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="584ef-111">作为起点，本文使用[ASP.NET Web API 2 入门](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)中创建的*ProductsApp*项目。</span><span class="sxs-lookup"><span data-stu-id="584ef-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="584ef-112">在该项目中，简单的 ASP.NET 4.x Web API 项目配置如下。</span><span class="sxs-lookup"><span data-stu-id="584ef-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="584ef-113">在*Global.asax.cs*中，对进行调用`WebApiConfig.Register`：</span><span class="sxs-lookup"><span data-stu-id="584ef-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="584ef-114">类位于*App_Start*文件夹中，并具有一个静态`Register`方法： `WebApiConfig`</span><span class="sxs-lookup"><span data-stu-id="584ef-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="584ef-115">此类配置[属性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，不过实际上它并不是在项目中使用。</span><span class="sxs-lookup"><span data-stu-id="584ef-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="584ef-116">它还配置 ASP.NET Web API 使用的路由表。</span><span class="sxs-lookup"><span data-stu-id="584ef-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="584ef-117">在这种情况下，ASP.NET 4.x Web API 需要 Url 来匹配格式`/api/{controller}/{id}`，这`{id}`是可选的。</span><span class="sxs-lookup"><span data-stu-id="584ef-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="584ef-118">*ProductsApp*项目包含一个控制器。</span><span class="sxs-lookup"><span data-stu-id="584ef-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="584ef-119">控制器继承自`ApiController` ，其中包含两个操作：</span><span class="sxs-lookup"><span data-stu-id="584ef-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="584ef-120">使用`Product`的模型`ProductsController`是一个简单的类：</span><span class="sxs-lookup"><span data-stu-id="584ef-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="584ef-121">以下部分演示了如何将 Web API 项目迁移到 ASP.NET Core MVC。</span><span class="sxs-lookup"><span data-stu-id="584ef-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="584ef-122">创建目标项目</span><span class="sxs-lookup"><span data-stu-id="584ef-122">Create destination project</span></span>

<span data-ttu-id="584ef-123">在 Visual Studio 中完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="584ef-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="584ef-124">中转到 "**文件** > " "**新建** > **项目** > " "**其他项目类型** > " "**Visual Studio 解决方案**"。</span><span class="sxs-lookup"><span data-stu-id="584ef-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="584ef-125">选择 "**空白解决方案**"，并将解决方案命名为 " *WebAPIMigration*"。</span><span class="sxs-lookup"><span data-stu-id="584ef-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="584ef-126">单击“确定”\*\*\*\* 按钮。</span><span class="sxs-lookup"><span data-stu-id="584ef-126">Click the **OK** button.</span></span>
* <span data-ttu-id="584ef-127">将现有的*ProductsApp*项目添加到解决方案。</span><span class="sxs-lookup"><span data-stu-id="584ef-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="584ef-128">向解决方案添加新的**ASP.NET Core Web 应用程序**项目。</span><span class="sxs-lookup"><span data-stu-id="584ef-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="584ef-129">从下拉选择 " **.Net Core**目标框架"，然后选择 " **API**项目" 模板。</span><span class="sxs-lookup"><span data-stu-id="584ef-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="584ef-130">将项目命名为 " *ProductsCore*"，然后单击 **"确定"** 按钮。</span><span class="sxs-lookup"><span data-stu-id="584ef-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="584ef-131">解决方案现在包含两个项目。</span><span class="sxs-lookup"><span data-stu-id="584ef-131">The solution now contains two projects.</span></span> <span data-ttu-id="584ef-132">以下各节介绍了如何将*ProductsApp*项目的内容迁移到*ProductsCore*项目。</span><span class="sxs-lookup"><span data-stu-id="584ef-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="584ef-133">迁移配置</span><span class="sxs-lookup"><span data-stu-id="584ef-133">Migrate configuration</span></span>

<span data-ttu-id="584ef-134">ASP.NET Core 不使用*App_Start*文件夹或*global.asax*文件，并且在发布时添加*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="584ef-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="584ef-135">*Startup.cs*是*global.asa*的替代，位于项目根目录中。</span><span class="sxs-lookup"><span data-stu-id="584ef-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="584ef-136">`Startup`类处理所有应用启动任务。</span><span class="sxs-lookup"><span data-stu-id="584ef-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="584ef-137">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="584ef-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="584ef-138">在 ASP.NET Core MVC 中，当在中<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure`调用时，默认情况下包含特性路由。</span><span class="sxs-lookup"><span data-stu-id="584ef-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="584ef-139">以下`UseMvc`调用将替换*ProductsApp*项目的*App_Start/webapiconfig.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="584ef-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="584ef-140">迁移模型和控制器</span><span class="sxs-lookup"><span data-stu-id="584ef-140">Migrate models and controllers</span></span>

<span data-ttu-id="584ef-141">复制*ProductApp*项目的控制器及其使用的模型。</span><span class="sxs-lookup"><span data-stu-id="584ef-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="584ef-142">执行以下步骤:</span><span class="sxs-lookup"><span data-stu-id="584ef-142">Follow these steps:</span></span>

1. <span data-ttu-id="584ef-143">将*控制器/ProductsController*从原始项目复制到新项目。</span><span class="sxs-lookup"><span data-stu-id="584ef-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="584ef-144">将整个*模型*文件夹从原始项目复制到新的项目。</span><span class="sxs-lookup"><span data-stu-id="584ef-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="584ef-145">更改复制的文件的命名空间，使其与新项目名称（*ProductsCore*）相匹配。</span><span class="sxs-lookup"><span data-stu-id="584ef-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="584ef-146">同时调整`using ProductsApp.Models;` *ProductsController.cs*中的语句。</span><span class="sxs-lookup"><span data-stu-id="584ef-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="584ef-147">此时，生成应用会导致大量编译错误。</span><span class="sxs-lookup"><span data-stu-id="584ef-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="584ef-148">之所以发生这些错误是因为 ASP.NET Core 中不存在下列组件：</span><span class="sxs-lookup"><span data-stu-id="584ef-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="584ef-149">`ApiController` 类</span><span class="sxs-lookup"><span data-stu-id="584ef-149">`ApiController` class</span></span>
* <span data-ttu-id="584ef-150">`System.Web.Http` 命名空间</span><span class="sxs-lookup"><span data-stu-id="584ef-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="584ef-151">`IHttpActionResult` 接口</span><span class="sxs-lookup"><span data-stu-id="584ef-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="584ef-152">修复错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="584ef-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="584ef-153">将 `ApiController` 更改为 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="584ef-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="584ef-154">添加`using Microsoft.AspNetCore.Mvc;`以解析`ControllerBase`引用。</span><span class="sxs-lookup"><span data-stu-id="584ef-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="584ef-155">删除 `using System.Web.Http;`。</span><span class="sxs-lookup"><span data-stu-id="584ef-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="584ef-156">将`GetProduct`操作的返回类型从`IHttpActionResult`更改为`ActionResult<Product>`。</span><span class="sxs-lookup"><span data-stu-id="584ef-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="584ef-157">简化`GetProduct`操作的`return`语句：</span><span class="sxs-lookup"><span data-stu-id="584ef-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="584ef-158">配置路由</span><span class="sxs-lookup"><span data-stu-id="584ef-158">Configure routing</span></span>

<span data-ttu-id="584ef-159">按如下所示配置路由：</span><span class="sxs-lookup"><span data-stu-id="584ef-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="584ef-160">用以下`ProductsController`特性标记类：</span><span class="sxs-lookup"><span data-stu-id="584ef-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="584ef-161">上述[`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性配置控制器的属性路由模式。</span><span class="sxs-lookup"><span data-stu-id="584ef-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="584ef-162">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)特性使特性路由成为此控制器中所有操作的要求。</span><span class="sxs-lookup"><span data-stu-id="584ef-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="584ef-163">特性路由支持标记，例如`[controller]`和。 `[action]`</span><span class="sxs-lookup"><span data-stu-id="584ef-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="584ef-164">在运行时，每个标记分别替换为应用了属性的控制器或操作的名称。</span><span class="sxs-lookup"><span data-stu-id="584ef-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="584ef-165">这些标记减少了项目中的幻字符串的数目。</span><span class="sxs-lookup"><span data-stu-id="584ef-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="584ef-166">标记还可确保在应用自动重命名重构时，路由与相应的控制器和操作保持同步。</span><span class="sxs-lookup"><span data-stu-id="584ef-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="584ef-167">将项目的兼容模式设置为 ASP.NET Core 2.2：</span><span class="sxs-lookup"><span data-stu-id="584ef-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="584ef-168">上述更改：</span><span class="sxs-lookup"><span data-stu-id="584ef-168">The preceding change:</span></span>

    * <span data-ttu-id="584ef-169">需要在控制器级别使用`[ApiController]`特性。</span><span class="sxs-lookup"><span data-stu-id="584ef-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="584ef-170">在 ASP.NET Core 2.2 中引入了可能中断的行为。</span><span class="sxs-lookup"><span data-stu-id="584ef-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="584ef-171">为`ProductController`操作启用 HTTP Get 请求：</span><span class="sxs-lookup"><span data-stu-id="584ef-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="584ef-172">将[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)特性应用于`GetAllProducts`操作。</span><span class="sxs-lookup"><span data-stu-id="584ef-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="584ef-173">将`[HttpGet("{id}")]`特性应用于`GetProduct`操作。</span><span class="sxs-lookup"><span data-stu-id="584ef-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="584ef-174">前面的更改和删除未使用`using`的语句后， *ProductsController.cs*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="584ef-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="584ef-175">运行迁移的项目，并浏览到`/api/products`。</span><span class="sxs-lookup"><span data-stu-id="584ef-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="584ef-176">此时会显示三个产品的完整列表。</span><span class="sxs-lookup"><span data-stu-id="584ef-176">A full list of three products appears.</span></span> <span data-ttu-id="584ef-177">浏览到 `/api/products/1` 。</span><span class="sxs-lookup"><span data-stu-id="584ef-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="584ef-178">第一个产品随即出现。</span><span class="sxs-lookup"><span data-stu-id="584ef-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="584ef-179">兼容性填充码</span><span class="sxs-lookup"><span data-stu-id="584ef-179">Compatibility shim</span></span>

<span data-ttu-id="584ef-180">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)库提供兼容性填充码，以将 ASP.NET 4.X Web API 项目移动到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="584ef-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="584ef-181">兼容性填充码扩展 ASP.NET Core，以支持 ASP.NET 4.x Web API 2 中的一些约定。</span><span class="sxs-lookup"><span data-stu-id="584ef-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="584ef-182">本文档前面的示例移植的基本操作足以确保兼容性填充程序是不必要的。</span><span class="sxs-lookup"><span data-stu-id="584ef-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="584ef-183">对于较大的项目，使用兼容性填充码对于临时桥接 ASP.NET Core 与 ASP.NET 4.x Web API 2 之间的 API 间隙非常有用。</span><span class="sxs-lookup"><span data-stu-id="584ef-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="584ef-184">Web API 兼容性填充码旨在用作一种临时度量，以支持将大型 ASP.NET 4.x Web API 项目迁移到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="584ef-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="584ef-185">随着时间的推移，应将项目更新为使用 ASP.NET Core 模式，而不是依赖于兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="584ef-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="584ef-186">中包含的`Microsoft.AspNetCore.Mvc.WebApiCompatShim`兼容性功能包括：</span><span class="sxs-lookup"><span data-stu-id="584ef-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="584ef-187">添加一个`ApiController`类型，以便不需要更新控制器的基类型。</span><span class="sxs-lookup"><span data-stu-id="584ef-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="584ef-188">启用 Web API 样式的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="584ef-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="584ef-189">默认情况下，ASP.NET Core MVC 模型绑定函数与 ASP.NET 4.x MVC 5 的绑定函数类似。</span><span class="sxs-lookup"><span data-stu-id="584ef-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="584ef-190">兼容性填充码更改模型绑定，更类似于 ASP.NET 4.x Web API 2 模型绑定约定。</span><span class="sxs-lookup"><span data-stu-id="584ef-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="584ef-191">例如，复杂类型会自动从请求正文进行绑定。</span><span class="sxs-lookup"><span data-stu-id="584ef-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="584ef-192">扩展模型绑定，以便控制器操作可以接受类型`HttpRequestMessage`的参数。</span><span class="sxs-lookup"><span data-stu-id="584ef-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="584ef-193">添加消息格式化程序，以允许操作返回类型`HttpResponseMessage`为的结果。</span><span class="sxs-lookup"><span data-stu-id="584ef-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="584ef-194">添加 Web API 2 操作可能用于提供响应的其他响应方法：</span><span class="sxs-lookup"><span data-stu-id="584ef-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="584ef-195">`HttpResponseMessage`生成器</span><span class="sxs-lookup"><span data-stu-id="584ef-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="584ef-196">操作结果方法：</span><span class="sxs-lookup"><span data-stu-id="584ef-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="584ef-197">将的`IContentNegotiator`实例添加到应用的依赖项注入（DI）容器，并使[WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)中与内容协商相关的类型可用。</span><span class="sxs-lookup"><span data-stu-id="584ef-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="584ef-198">此类类型的示例`DefaultContentNegotiator`包括`MediaTypeFormatter`和。</span><span class="sxs-lookup"><span data-stu-id="584ef-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="584ef-199">使用兼容性填充码：</span><span class="sxs-lookup"><span data-stu-id="584ef-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="584ef-200">安装[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="584ef-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="584ef-201">通过调用`services.AddMvc().AddWebApiConventions()`在中`Startup.ConfigureServices`，将兼容性填充程序的服务注册到应用的 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="584ef-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="584ef-202">在应用的`MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc`调用中，使用在上定义特定于 web API 的路由。</span><span class="sxs-lookup"><span data-stu-id="584ef-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="584ef-203">其他资源</span><span class="sxs-lookup"><span data-stu-id="584ef-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
