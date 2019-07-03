---
title: 将从 ASP.NET Web API 迁移到 ASP.NET Core
author: ardalis
description: 了解如何将 web API 实现从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: 74c7730a667ebc979241489733cdace149cacdf2
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555746"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="9fde7-103">将从 ASP.NET Web API 迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9fde7-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="9fde7-104">通过[Scott Addie](https://twitter.com/scott_addie)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9fde7-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9fde7-105">ASP.NET 4.x Web API 是到达范围广泛的客户端，包括浏览器和移动设备的 HTTP 服务。</span><span class="sxs-lookup"><span data-stu-id="9fde7-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="9fde7-106">ASP.NET Core 统一了 ASP.NET 4.x 的 MVC 和 Web API 应用模型到名为 ASP.NET Core MVC 更简单的编程模型。</span><span class="sxs-lookup"><span data-stu-id="9fde7-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="9fde7-107">本文演示如何将 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC 所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="9fde7-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="9fde7-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9fde7-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9fde7-109">系统必备</span><span class="sxs-lookup"><span data-stu-id="9fde7-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="9fde7-110">检查 ASP.NET 4.x Web API 项目</span><span class="sxs-lookup"><span data-stu-id="9fde7-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="9fde7-111">作为起点，本文使用*ProductsApp*中创建项目[Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="9fde7-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="9fde7-112">该项目中，一个简单的 ASP.NET 4.x Web API 项目配置，如下所示。</span><span class="sxs-lookup"><span data-stu-id="9fde7-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="9fde7-113">在中*Global.asax.cs*，调用`WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="9fde7-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="9fde7-114">`WebApiConfig`类位于*App_Start*文件夹并且具有静态`Register`方法：</span><span class="sxs-lookup"><span data-stu-id="9fde7-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="9fde7-115">此类配置[的属性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，但它实际上未使用项目中。</span><span class="sxs-lookup"><span data-stu-id="9fde7-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="9fde7-116">它还可以配置 ASP.NET Web API 使用的路由表。</span><span class="sxs-lookup"><span data-stu-id="9fde7-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="9fde7-117">在这种情况下，ASP.NET 4.x Web API 需要 Url 以匹配格式`/api/{controller}/{id}`，使用`{id}`可选的。</span><span class="sxs-lookup"><span data-stu-id="9fde7-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="9fde7-118">*ProductsApp*项目包括一个控制器。</span><span class="sxs-lookup"><span data-stu-id="9fde7-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="9fde7-119">在控制器继承`ApiController`和包含两个操作：</span><span class="sxs-lookup"><span data-stu-id="9fde7-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="9fde7-120">`Product`使用模型`ProductsController`是一个简单的类：</span><span class="sxs-lookup"><span data-stu-id="9fde7-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="9fde7-121">以下部分演示了迁移到 ASP.NET Core MVC Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="9fde7-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="9fde7-122">创建目标项目</span><span class="sxs-lookup"><span data-stu-id="9fde7-122">Create destination project</span></span>

<span data-ttu-id="9fde7-123">完成 Visual Studio 中的以下步骤：</span><span class="sxs-lookup"><span data-stu-id="9fde7-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="9fde7-124">转到**文件** > **新** > **项目** > **其他项目类型** > **Visual Studio 解决方案**。</span><span class="sxs-lookup"><span data-stu-id="9fde7-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="9fde7-125">选择**空白解决方案**，并将解决方案命名*WebAPIMigration*。</span><span class="sxs-lookup"><span data-stu-id="9fde7-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="9fde7-126">单击**确定**按钮。</span><span class="sxs-lookup"><span data-stu-id="9fde7-126">Click the **OK** button.</span></span>
* <span data-ttu-id="9fde7-127">添加现有*ProductsApp*到解决方案。</span><span class="sxs-lookup"><span data-stu-id="9fde7-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="9fde7-128">添加一个新**ASP.NET Core Web 应用程序**到解决方案。</span><span class="sxs-lookup"><span data-stu-id="9fde7-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="9fde7-129">选择 **.NET Core**从下拉列表中的框架为目标，然后选择**API**项目模板。</span><span class="sxs-lookup"><span data-stu-id="9fde7-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="9fde7-130">将项目命名*ProductsCore*，然后单击**确定**按钮。</span><span class="sxs-lookup"><span data-stu-id="9fde7-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="9fde7-131">该解决方案现在包含两个项目。</span><span class="sxs-lookup"><span data-stu-id="9fde7-131">The solution now contains two projects.</span></span> <span data-ttu-id="9fde7-132">以下各节介绍了迁移*ProductsApp*项目的内容与*ProductsCore*项目。</span><span class="sxs-lookup"><span data-stu-id="9fde7-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="9fde7-133">迁移配置</span><span class="sxs-lookup"><span data-stu-id="9fde7-133">Migrate configuration</span></span>

<span data-ttu-id="9fde7-134">不使用 ASP.NET Core *App_Start*文件夹或*Global.asax*文件，并*web.config*文件添加在发布时间。</span><span class="sxs-lookup"><span data-stu-id="9fde7-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="9fde7-135">*Startup.cs*将取代*Global.asax*和位于项目根目录中。</span><span class="sxs-lookup"><span data-stu-id="9fde7-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="9fde7-136">`Startup`类负责处理所有应用程序启动任务。</span><span class="sxs-lookup"><span data-stu-id="9fde7-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="9fde7-137">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="9fde7-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="9fde7-138">在 ASP.NET Core MVC 中，在默认情况下包含的属性路由时<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>名为`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="9fde7-139">以下`UseMvc`调用替换*ProductsApp*项目的*app_start/webapiconfig.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="9fde7-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="9fde7-140">迁移模型和控制器</span><span class="sxs-lookup"><span data-stu-id="9fde7-140">Migrate models and controllers</span></span>

<span data-ttu-id="9fde7-141">通过复制*ProductApp*项目的控制器和它所使用的模型。</span><span class="sxs-lookup"><span data-stu-id="9fde7-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="9fde7-142">请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="9fde7-142">Follow these steps:</span></span>

1. <span data-ttu-id="9fde7-143">复制*Controllers/ProductsController.cs*从原始到新项目。</span><span class="sxs-lookup"><span data-stu-id="9fde7-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="9fde7-144">复制整个*模型*从原始项目到新的文件夹。</span><span class="sxs-lookup"><span data-stu-id="9fde7-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="9fde7-145">更改复制的文件的命名空间以匹配新的项目名称 (*ProductsCore*)。</span><span class="sxs-lookup"><span data-stu-id="9fde7-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="9fde7-146">调整`using ProductsApp.Models;`中的语句*ProductsController.cs*过。</span><span class="sxs-lookup"><span data-stu-id="9fde7-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="9fde7-147">此时，生成中的多个编译错误的应用程序结果。</span><span class="sxs-lookup"><span data-stu-id="9fde7-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="9fde7-148">发生错误的原因在 ASP.NET Core 中不存在以下组件：</span><span class="sxs-lookup"><span data-stu-id="9fde7-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="9fde7-149">`ApiController` 类</span><span class="sxs-lookup"><span data-stu-id="9fde7-149">`ApiController` class</span></span>
* <span data-ttu-id="9fde7-150">`System.Web.Http` 命名空间</span><span class="sxs-lookup"><span data-stu-id="9fde7-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="9fde7-151">`IHttpActionResult` 接口</span><span class="sxs-lookup"><span data-stu-id="9fde7-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="9fde7-152">修复错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fde7-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="9fde7-153">更改`ApiController`到<xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="9fde7-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="9fde7-154">添加`using Microsoft.AspNetCore.Mvc;`若要解决`ControllerBase`引用。</span><span class="sxs-lookup"><span data-stu-id="9fde7-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="9fde7-155">删除 `using System.Web.Http;`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="9fde7-156">更改`GetProduct`操作的返回类型从`IHttpActionResult`到`ActionResult<Product>`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="9fde7-157">简化`GetProduct`操作的`return`与以下语句：</span><span class="sxs-lookup"><span data-stu-id="9fde7-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="9fde7-158">配置路由</span><span class="sxs-lookup"><span data-stu-id="9fde7-158">Configure routing</span></span>

<span data-ttu-id="9fde7-159">配置路由，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fde7-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="9fde7-160">修饰`ProductsController`类具有以下属性：</span><span class="sxs-lookup"><span data-stu-id="9fde7-160">Decorate the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="9fde7-161">在前面[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性所配置控制器的属性路由模式。</span><span class="sxs-lookup"><span data-stu-id="9fde7-161">The preceding [[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="9fde7-162">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)特性使此控制器中的路由所必需的所有操作的属性。</span><span class="sxs-lookup"><span data-stu-id="9fde7-162">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="9fde7-163">属性路由支持令牌，如`[controller]`和`[action]`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="9fde7-164">在运行时，每个令牌是分别替换为控制器或操作的名称，已向其应用了该属性。</span><span class="sxs-lookup"><span data-stu-id="9fde7-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="9fde7-165">令牌减少的项目中的魔幻字符串。</span><span class="sxs-lookup"><span data-stu-id="9fde7-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="9fde7-166">路由保持与相应的控制器同步，并将应用时自动重命名重构操作，还请确保令牌。</span><span class="sxs-lookup"><span data-stu-id="9fde7-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="9fde7-167">将项目的兼容性模式设置为 ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="9fde7-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="9fde7-168">上述更改：</span><span class="sxs-lookup"><span data-stu-id="9fde7-168">The preceding change:</span></span>

    * <span data-ttu-id="9fde7-169">使用所需`[ApiController]`在控制器级别属性。</span><span class="sxs-lookup"><span data-stu-id="9fde7-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="9fde7-170">选择启用可能出现的中断 ASP.NET Core 2.2 中引入的行为。</span><span class="sxs-lookup"><span data-stu-id="9fde7-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="9fde7-171">启用 HTTP Get 请求到`ProductController`操作：</span><span class="sxs-lookup"><span data-stu-id="9fde7-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="9fde7-172">将应用[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)属性为`GetAllProducts`操作。</span><span class="sxs-lookup"><span data-stu-id="9fde7-172">Apply the [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="9fde7-173">将应用`[HttpGet("{id}")]`属性为`GetProduct`操作。</span><span class="sxs-lookup"><span data-stu-id="9fde7-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="9fde7-174">上述更改和删除未使用之后`using`语句， *ProductsController.cs*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fde7-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="9fde7-175">运行已迁移的项目，并浏览到`/api/products`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="9fde7-176">将显示三种产品的完整列表。</span><span class="sxs-lookup"><span data-stu-id="9fde7-176">A full list of three products appears.</span></span> <span data-ttu-id="9fde7-177">浏览到 `/api/products/1`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="9fde7-178">第一个产品将出现。</span><span class="sxs-lookup"><span data-stu-id="9fde7-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="9fde7-179">兼容性修补程序</span><span class="sxs-lookup"><span data-stu-id="9fde7-179">Compatibility shim</span></span>

<span data-ttu-id="9fde7-180">[Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)库提供了兼容性填充程序移动到 ASP.NET Core 的 ASP.NET 4.x Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="9fde7-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="9fde7-181">兼容性填充程序扩展了 ASP.NET Core 支持数量从 ASP.NET 4.x Web API 2 的约定。</span><span class="sxs-lookup"><span data-stu-id="9fde7-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="9fde7-182">移植之前在本文档中的示例是基本兼容性填充程序是不需要的。</span><span class="sxs-lookup"><span data-stu-id="9fde7-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="9fde7-183">对于大型项目，使用兼容性修补程序也可用于临时之间的隔阂 API ASP.NET Core 和 ASP.NET 4.x Web API 2。</span><span class="sxs-lookup"><span data-stu-id="9fde7-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="9fde7-184">旨在用作临时度量值以支持大型 ASP.NET 4.x Web API 项目迁移到 ASP.NET Core Web API 兼容性修补程序。</span><span class="sxs-lookup"><span data-stu-id="9fde7-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="9fde7-185">随着时间推移，应更新项目以使用 ASP.NET Core 模式，而不是依靠兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="9fde7-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="9fde7-186">中包含的兼容性功能`Microsoft.AspNetCore.Mvc.WebApiCompatShim`包括：</span><span class="sxs-lookup"><span data-stu-id="9fde7-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="9fde7-187">添加`ApiController`类型，以便控制器的基类型不需要更新。</span><span class="sxs-lookup"><span data-stu-id="9fde7-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="9fde7-188">启用 Web API 样式模型绑定。</span><span class="sxs-lookup"><span data-stu-id="9fde7-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="9fde7-189">ASP.NET Core MVC 模型绑定函数类似于的 ASP.NET 4.x MVC 5，默认情况下。</span><span class="sxs-lookup"><span data-stu-id="9fde7-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="9fde7-190">兼容性填充程序更改模型绑定是更类似于 ASP.NET 4.x Web API 2 模型绑定约定。</span><span class="sxs-lookup"><span data-stu-id="9fde7-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="9fde7-191">例如，复杂类型自动绑定从请求正文。</span><span class="sxs-lookup"><span data-stu-id="9fde7-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="9fde7-192">扩展模型绑定，以便控制器操作可以采取的类型参数`HttpRequestMessage`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="9fde7-193">添加消息格式化程序，允许的操作以返回类型的结果`HttpResponseMessage`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="9fde7-194">添加 Web API 2 操作可能具有用于为响应提供服务的其他响应方法：</span><span class="sxs-lookup"><span data-stu-id="9fde7-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="9fde7-195">`HttpResponseMessage` 生成器：</span><span class="sxs-lookup"><span data-stu-id="9fde7-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="9fde7-196">结果的操作方法：</span><span class="sxs-lookup"><span data-stu-id="9fde7-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="9fde7-197">添加的一个实例`IContentNegotiator`向应用程序的依赖关系注入 (DI) 容器，并使可从内容协商相关类型[Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)。</span><span class="sxs-lookup"><span data-stu-id="9fde7-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="9fde7-198">此类类型的示例包括`DefaultContentNegotiator`和`MediaTypeFormatter`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="9fde7-199">若要使用兼容性修补程序：</span><span class="sxs-lookup"><span data-stu-id="9fde7-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="9fde7-200">安装[Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9fde7-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="9fde7-201">兼容性修补程序的服务注册应用程序的 DI 容器通过调用`services.AddMvc().AddWebApiConventions()`在`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="9fde7-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="9fde7-202">定义特定于 API 的路由使用的 web`MapWebApiRoute`上`IRouteBuilder`在应用的`IApplicationBuilder.UseMvc`调用。</span><span class="sxs-lookup"><span data-stu-id="9fde7-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9fde7-203">其他资源</span><span class="sxs-lookup"><span data-stu-id="9fde7-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
