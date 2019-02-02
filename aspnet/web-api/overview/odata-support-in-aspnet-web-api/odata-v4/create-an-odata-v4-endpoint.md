---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
title: 创建 OData v4 终结点使用 ASP.NET Web API 2.2 |Microsoft Docs
author: MikeWasson
description: 开放数据协议 (OData) 是一种用于 web 的数据访问协议。 OData 提供统一的方式来查询和操作通过 CRUD 操作的数据集...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 1e1927c0-ded1-4752-80fd-a146628d2f09
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
msc.type: authoredcontent
ms.openlocfilehash: c6a4aa4eb563fd77d5afd9248175d5f5b7984d19
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667565"
---
# <a name="create-an-odata-v4-endpoint-using-aspnet-web-api"></a><span data-ttu-id="8b660-104">创建 OData v4 终结点使用 ASP.NET Web API</span><span class="sxs-lookup"><span data-stu-id="8b660-104">Create an OData v4 Endpoint Using ASP.NET Web API</span></span> 

> <span data-ttu-id="8b660-105">开放数据协议 (OData) 是一种用于 web 的数据访问协议。</span><span class="sxs-lookup"><span data-stu-id="8b660-105">The Open Data Protocol (OData) is a data access protocol for the web.</span></span> <span data-ttu-id="8b660-106">OData 提供统一的方式来查询和操作数据集通过 CRUD 操作 （创建、 读取、 更新和删除）。</span><span class="sxs-lookup"><span data-stu-id="8b660-106">OData provides a uniform way to query and manipulate data sets through CRUD operations (create, read, update, and delete).</span></span>
>
> <span data-ttu-id="8b660-107">ASP.NET Web API 支持 v3 和 v4 协议。</span><span class="sxs-lookup"><span data-stu-id="8b660-107">ASP.NET Web API supports both v3 and v4 of the protocol.</span></span> <span data-ttu-id="8b660-108">你甚至可以让运行的并行的 v4 终结点与 v3 终结点。</span><span class="sxs-lookup"><span data-stu-id="8b660-108">You can even have a v4 endpoint that runs side-by-side with a v3 endpoint.</span></span>
>
> <span data-ttu-id="8b660-109">本教程演示如何创建支持 CRUD 操作的 OData v4 终结点。</span><span class="sxs-lookup"><span data-stu-id="8b660-109">This tutorial shows how to create an OData v4 endpoint that supports CRUD operations.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="8b660-110">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="8b660-110">Software versions used in the tutorial</span></span>
>
> - <span data-ttu-id="8b660-111">Web API 5.2</span><span class="sxs-lookup"><span data-stu-id="8b660-111">Web API 5.2</span></span>
> - <span data-ttu-id="8b660-112">OData v4</span><span class="sxs-lookup"><span data-stu-id="8b660-112">OData v4</span></span>
> - <span data-ttu-id="8b660-113">Visual Studio 2017 (下载 Visual Studio 2017[此处](https://visualstudio.microsoft.com/downloads/))</span><span class="sxs-lookup"><span data-stu-id="8b660-113">Visual Studio 2017 (download Visual Studio 2017 [here](https://visualstudio.microsoft.com/downloads/))</span></span>
> - <span data-ttu-id="8b660-114">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="8b660-114">Entity Framework 6</span></span>
> - <span data-ttu-id="8b660-115">.NET 4.7.2</span><span class="sxs-lookup"><span data-stu-id="8b660-115">.NET 4.7.2</span></span>
>
> ## <a name="tutorial-versions"></a><span data-ttu-id="8b660-116">教程版本</span><span class="sxs-lookup"><span data-stu-id="8b660-116">Tutorial versions</span></span>
>
> <span data-ttu-id="8b660-117">OData 版本 3，请参阅[创建 OData v3 终结点](../odata-v3/creating-an-odata-endpoint.md)。</span><span class="sxs-lookup"><span data-stu-id="8b660-117">For the OData Version 3, see [Creating an OData v3 Endpoint](../odata-v3/creating-an-odata-endpoint.md).</span></span>

## <a name="create-the-visual-studio-project"></a><span data-ttu-id="8b660-118">创建 Visual Studio 项目</span><span class="sxs-lookup"><span data-stu-id="8b660-118">Create the Visual Studio Project</span></span>

<span data-ttu-id="8b660-119">在 Visual Studio 中，从**文件**菜单中，选择**新建** &gt; **项目**。</span><span class="sxs-lookup"><span data-stu-id="8b660-119">In Visual Studio, from the **File** menu, select **New** &gt; **Project**.</span></span>

<span data-ttu-id="8b660-120">展开**已安装** &gt; **Visual C#**  &gt; **Web**，然后选择**ASP.NET Web 应用程序 (.NET Framework)** 模板。</span><span class="sxs-lookup"><span data-stu-id="8b660-120">Expand **Installed** &gt; **Visual C#** &gt; **Web**, and select the **ASP.NET Web Application (.NET Framework)** template.</span></span> <span data-ttu-id="8b660-121">将项目命名&quot;ProductService&quot;。</span><span class="sxs-lookup"><span data-stu-id="8b660-121">Name the project &quot;ProductService&quot;.</span></span>

[![](create-an-odata-v4-endpoint/_static/image7.png)](create-an-odata-v4-endpoint/_static/image7.png)

<span data-ttu-id="8b660-122">选择 **确定**。</span><span class="sxs-lookup"><span data-stu-id="8b660-122">Select **OK**.</span></span>



[![](create-an-odata-v4-endpoint/_static/image8.png)](create-an-odata-v4-endpoint/_static/image8.png)

<span data-ttu-id="8b660-123">选择**空**模板。</span><span class="sxs-lookup"><span data-stu-id="8b660-123">Select the **Empty** template.</span></span> <span data-ttu-id="8b660-124">下**添加文件夹和核心引用：**，选择**Web API**。</span><span class="sxs-lookup"><span data-stu-id="8b660-124">Under **Add folders and core references for:**, select **Web API**.</span></span> <span data-ttu-id="8b660-125">选择 **确定**。</span><span class="sxs-lookup"><span data-stu-id="8b660-125">Select **OK**.</span></span>

## <a name="install-the-odata-packages"></a><span data-ttu-id="8b660-126">OData 包安装</span><span class="sxs-lookup"><span data-stu-id="8b660-126">Install the OData packages</span></span>

<span data-ttu-id="8b660-127">从“工具”菜单中，选择“NuGet 程序包管理器”&gt;“包管理器控制台”。</span><span class="sxs-lookup"><span data-stu-id="8b660-127">From the **Tools** menu, select **NuGet Package Manager** &gt; **Package Manager Console**.</span></span> <span data-ttu-id="8b660-128">在包管理器控制台窗口中，键入：</span><span class="sxs-lookup"><span data-stu-id="8b660-128">In the Package Manager Console window, type:</span></span>

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample1.cmd)]

<span data-ttu-id="8b660-129">此命令将安装最新的 OData NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="8b660-129">This command installs the latest OData NuGet packages.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="8b660-130">添加模型类</span><span class="sxs-lookup"><span data-stu-id="8b660-130">Add a model class</span></span>

<span data-ttu-id="8b660-131">一个*模型*是一个对象，表示在应用程序中的数据实体。</span><span class="sxs-lookup"><span data-stu-id="8b660-131">A *model* is an object that represents a data entity in your application.</span></span>

<span data-ttu-id="8b660-132">在解决方案资源管理器，右键单击模型文件夹。</span><span class="sxs-lookup"><span data-stu-id="8b660-132">In Solution Explorer, right-click the Models folder.</span></span> <span data-ttu-id="8b660-133">从上下文菜单中，选择**外** &gt; **类**。</span><span class="sxs-lookup"><span data-stu-id="8b660-133">From the context menu, select **Add** &gt; **Class**.</span></span>

[![](create-an-odata-v4-endpoint/_static/image6.png)](create-an-odata-v4-endpoint/_static/image5.png)

> [!NOTE]
> <span data-ttu-id="8b660-134">按照约定，模型类都位于 Models 文件夹中，但无需遵循此约定在您自己的项目中。</span><span class="sxs-lookup"><span data-stu-id="8b660-134">By convention, model classes are placed in the Models folder, but you don't have to follow this convention in your own projects.</span></span>


<span data-ttu-id="8b660-135">将此类命名为 `Product`。</span><span class="sxs-lookup"><span data-stu-id="8b660-135">Name the class `Product`.</span></span> <span data-ttu-id="8b660-136">在 Product.cs 文件中，将替换以下为样板代码：</span><span class="sxs-lookup"><span data-stu-id="8b660-136">In the Product.cs file, replace the boilerplate code with the following:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample2.cs)]

<span data-ttu-id="8b660-137">`Id`属性是实体键。</span><span class="sxs-lookup"><span data-stu-id="8b660-137">The `Id` property is the entity key.</span></span> <span data-ttu-id="8b660-138">客户端可以通过键查询实体。</span><span class="sxs-lookup"><span data-stu-id="8b660-138">Clients can query entities by key.</span></span> <span data-ttu-id="8b660-139">例如，若要获取 id 为 5 的产品，URI 是`/Products(5)`。</span><span class="sxs-lookup"><span data-stu-id="8b660-139">For example, to get the product with ID of 5, the URI is `/Products(5)`.</span></span> <span data-ttu-id="8b660-140">`Id`属性也是后端数据库中的主键。</span><span class="sxs-lookup"><span data-stu-id="8b660-140">The `Id` property will also be the primary key in the back-end database.</span></span>

## <a name="enable-entity-framework"></a><span data-ttu-id="8b660-141">启用实体框架</span><span class="sxs-lookup"><span data-stu-id="8b660-141">Enable Entity Framework</span></span>

<span data-ttu-id="8b660-142">对于本教程，我们将使用 Entity Framework (EF) Code First 创建后端数据库。</span><span class="sxs-lookup"><span data-stu-id="8b660-142">For this tutorial, we'll use Entity Framework (EF) Code First to create the back-end database.</span></span>

> [!NOTE]
> <span data-ttu-id="8b660-143">Web API OData 不需要 EF。</span><span class="sxs-lookup"><span data-stu-id="8b660-143">Web API OData does not require EF.</span></span> <span data-ttu-id="8b660-144">使用可以转换为模型的数据库实体的任何数据访问层。</span><span class="sxs-lookup"><span data-stu-id="8b660-144">Use any data-access layer that can translate database entities into models.</span></span>


<span data-ttu-id="8b660-145">首先，安装 EF 的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="8b660-145">First, install the NuGet package for EF.</span></span> <span data-ttu-id="8b660-146">从“工具”菜单中，选择“NuGet 程序包管理器”&gt;“包管理器控制台”。</span><span class="sxs-lookup"><span data-stu-id="8b660-146">From the **Tools** menu, select **NuGet Package Manager** &gt; **Package Manager Console**.</span></span> <span data-ttu-id="8b660-147">在包管理器控制台窗口中，键入：</span><span class="sxs-lookup"><span data-stu-id="8b660-147">In the Package Manager Console window, type:</span></span>

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample3.cmd)]

<span data-ttu-id="8b660-148">打开 Web.config 文件中，并添加以下节内的**配置**元素后面**configSections**元素。</span><span class="sxs-lookup"><span data-stu-id="8b660-148">Open the Web.config file, and add the following section inside the **configuration** element, after the **configSections** element.</span></span>

[!code-xml[Main](create-an-odata-v4-endpoint/samples/sample4.xml?highlight=6)]

<span data-ttu-id="8b660-149">此设置将添加 LocalDB 数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8b660-149">This setting adds a connection string for a LocalDB database.</span></span> <span data-ttu-id="8b660-150">在本地运行应用时，将使用此数据库。</span><span class="sxs-lookup"><span data-stu-id="8b660-150">This database will be used when you run the app locally.</span></span>

<span data-ttu-id="8b660-151">接下来，添加一个名为类`ProductsContext`到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="8b660-151">Next, add a class named `ProductsContext` to the Models folder:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample5.cs)]

<span data-ttu-id="8b660-152">在构造函数`"name=ProductsContext"`提供连接字符串的名称。</span><span class="sxs-lookup"><span data-stu-id="8b660-152">In the constructor, `"name=ProductsContext"` gives the name of the connection string.</span></span>

## <a name="configure-the-odata-endpoint"></a><span data-ttu-id="8b660-153">配置 OData 终结点</span><span class="sxs-lookup"><span data-stu-id="8b660-153">Configure the OData endpoint</span></span>

<span data-ttu-id="8b660-154">打开文件应用\_Start/WebApiConfig.cs。</span><span class="sxs-lookup"><span data-stu-id="8b660-154">Open the file App\_Start/WebApiConfig.cs.</span></span> <span data-ttu-id="8b660-155">添加以下**使用**语句：</span><span class="sxs-lookup"><span data-stu-id="8b660-155">Add the following **using** statements:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample6.cs)]

<span data-ttu-id="8b660-156">然后将以下代码添加到**注册**方法：</span><span class="sxs-lookup"><span data-stu-id="8b660-156">Then add the following code to the **Register** method:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample7.cs)]

<span data-ttu-id="8b660-157">此代码执行两项操作：</span><span class="sxs-lookup"><span data-stu-id="8b660-157">This code does two things:</span></span>

- <span data-ttu-id="8b660-158">创建实体数据模型 (EDM)。</span><span class="sxs-lookup"><span data-stu-id="8b660-158">Creates an Entity Data Model (EDM).</span></span>
- <span data-ttu-id="8b660-159">添加一个路由。</span><span class="sxs-lookup"><span data-stu-id="8b660-159">Adds a route.</span></span>

<span data-ttu-id="8b660-160">EDM 是抽象的数据模型。</span><span class="sxs-lookup"><span data-stu-id="8b660-160">An EDM is an abstract model of the data.</span></span> <span data-ttu-id="8b660-161">EDM 用于创建服务元数据文档。</span><span class="sxs-lookup"><span data-stu-id="8b660-161">The EDM is used to create the service metadata document.</span></span> <span data-ttu-id="8b660-162">**ODataConventionModelBuilder**类使用默认命名约定创建 EDM。</span><span class="sxs-lookup"><span data-stu-id="8b660-162">The **ODataConventionModelBuilder** class creates an EDM by using default naming conventions.</span></span> <span data-ttu-id="8b660-163">此方法要求最少的代码。</span><span class="sxs-lookup"><span data-stu-id="8b660-163">This approach requires the least code.</span></span> <span data-ttu-id="8b660-164">如果你想更好地控制 EDM，则可以使用**ODataModelBuilder**类，以通过添加属性、 键和导航属性显式创建 EDM。</span><span class="sxs-lookup"><span data-stu-id="8b660-164">If you want more control over the EDM, you can use the **ODataModelBuilder** class to create the EDM by adding properties, keys, and navigation properties explicitly.</span></span>

<span data-ttu-id="8b660-165">一个*路由*告知如何将 HTTP 请求路由到终结点的 Web API。</span><span class="sxs-lookup"><span data-stu-id="8b660-165">A *route* tells Web API how to route HTTP requests to the endpoint.</span></span> <span data-ttu-id="8b660-166">若要创建 OData v4 路由，请调用**MapODataServiceRoute**扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b660-166">To create an OData v4 route, call the **MapODataServiceRoute** extension method.</span></span>

<span data-ttu-id="8b660-167">如果你的应用程序具有多个 OData 终结点，每个创建一个单独的路由。</span><span class="sxs-lookup"><span data-stu-id="8b660-167">If your application has multiple OData endpoints, create a separate route for each.</span></span> <span data-ttu-id="8b660-168">为每个路由指定唯一的路由名称和前缀。</span><span class="sxs-lookup"><span data-stu-id="8b660-168">Give each route a unique route name and prefix.</span></span>

## <a name="add-the-odata-controller"></a><span data-ttu-id="8b660-169">添加 OData 控制器</span><span class="sxs-lookup"><span data-stu-id="8b660-169">Add the OData controller</span></span>

<span data-ttu-id="8b660-170">一个*控制器*是处理 HTTP 请求的类。</span><span class="sxs-lookup"><span data-stu-id="8b660-170">A *controller* is a class that handles HTTP requests.</span></span> <span data-ttu-id="8b660-171">创建 OData 服务中设置每个实体的单独控制器。</span><span class="sxs-lookup"><span data-stu-id="8b660-171">You create a separate controller for each entity set in your OData service.</span></span> <span data-ttu-id="8b660-172">在本教程中，您将创建一个控制器为`Product`实体。</span><span class="sxs-lookup"><span data-stu-id="8b660-172">In this tutorial, you will create one controller, for the `Product` entity.</span></span>

<span data-ttu-id="8b660-173">在解决方案资源管理器，右键单击 Controllers 文件夹并选择**外** &gt; **类**。</span><span class="sxs-lookup"><span data-stu-id="8b660-173">In Solution Explorer, right-click the Controllers folder and select **Add** &gt; **Class**.</span></span> <span data-ttu-id="8b660-174">将此类命名为 `ProductsController`。</span><span class="sxs-lookup"><span data-stu-id="8b660-174">Name the class `ProductsController`.</span></span>

> [!NOTE]
> <span data-ttu-id="8b660-175">本教程，了解 OData v3 使用新版**添加控制器**基架。</span><span class="sxs-lookup"><span data-stu-id="8b660-175">The version of this tutorial for OData v3 uses the **Add Controller** scaffolding.</span></span> <span data-ttu-id="8b660-176">目前，OData v4 没有基架。</span><span class="sxs-lookup"><span data-stu-id="8b660-176">Currently, there is no scaffolding for OData v4.</span></span>


<span data-ttu-id="8b660-177">ProductsController.cs 中的样板代码替换为以下。</span><span class="sxs-lookup"><span data-stu-id="8b660-177">Replace the boilerplate code in ProductsController.cs with the following.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample8.cs)]

<span data-ttu-id="8b660-178">控制器使用`ProductsContext`类，以使用 EF 访问数据库。</span><span class="sxs-lookup"><span data-stu-id="8b660-178">The controller uses the `ProductsContext` class to access the database using EF.</span></span> <span data-ttu-id="8b660-179">请注意，在控制器重写**Dispose**方法来释放**ProductsContext**。</span><span class="sxs-lookup"><span data-stu-id="8b660-179">Notice that the controller overrides the **Dispose** method to dispose of the **ProductsContext**.</span></span>

<span data-ttu-id="8b660-180">这是在控制器的起始点。</span><span class="sxs-lookup"><span data-stu-id="8b660-180">This is the starting point for the controller.</span></span> <span data-ttu-id="8b660-181">接下来，我们将添加所有 CRUD 操作的方法。</span><span class="sxs-lookup"><span data-stu-id="8b660-181">Next, we'll add methods for all of the CRUD operations.</span></span>

## <a name="query-the-entity-set"></a><span data-ttu-id="8b660-182">查询实体集</span><span class="sxs-lookup"><span data-stu-id="8b660-182">Query the entity set</span></span>

<span data-ttu-id="8b660-183">添加以下方法向`ProductsController`。</span><span class="sxs-lookup"><span data-stu-id="8b660-183">Add the following methods to `ProductsController`.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample9.cs)]

<span data-ttu-id="8b660-184">无参数版本的`Get`方法将返回整个产品集合。</span><span class="sxs-lookup"><span data-stu-id="8b660-184">The parameterless version of the `Get` method returns the entire Products collection.</span></span> <span data-ttu-id="8b660-185">`Get`方法替换*密钥*根据键查找产品参数 (在这种情况下，`Id`属性)。</span><span class="sxs-lookup"><span data-stu-id="8b660-185">The `Get` method with a *key* parameter looks up a product by its key (in this case, the `Id` property).</span></span>

<span data-ttu-id="8b660-186">**[EnableQuery]** 属性使客户端能够通过使用查询选项，例如 $filter、 $sort 和 $page 修改查询。</span><span class="sxs-lookup"><span data-stu-id="8b660-186">The **[EnableQuery]** attribute enables clients to modify the query, by using query options such as $filter, $sort, and $page.</span></span> <span data-ttu-id="8b660-187">有关详细信息，请参阅[支持 OData 查询选项](../supporting-odata-query-options.md)。</span><span class="sxs-lookup"><span data-stu-id="8b660-187">For more information, see [Supporting OData Query Options](../supporting-odata-query-options.md).</span></span>

## <a name="add-an-entity-to-the-entity-set"></a><span data-ttu-id="8b660-188">将实体添加到实体集</span><span class="sxs-lookup"><span data-stu-id="8b660-188">Add an entity to the entity set</span></span>

<span data-ttu-id="8b660-189">若要允许客户端向数据库添加一个新的产品，添加以下方法`ProductsController`。</span><span class="sxs-lookup"><span data-stu-id="8b660-189">To enable clients to add a new product to the database, add the following method to `ProductsController`.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample10.cs)]

## <a name="update-an-entity"></a><span data-ttu-id="8b660-190">更新实体</span><span class="sxs-lookup"><span data-stu-id="8b660-190">Update an entity</span></span>

<span data-ttu-id="8b660-191">OData 支持用于更新实体，PATCH 和 PUT 的两个不同的语义。</span><span class="sxs-lookup"><span data-stu-id="8b660-191">OData supports two different semantics for updating an entity, PATCH and PUT.</span></span>

- <span data-ttu-id="8b660-192">修补程序执行部分更新。</span><span class="sxs-lookup"><span data-stu-id="8b660-192">PATCH performs a partial update.</span></span> <span data-ttu-id="8b660-193">客户端指定只是要更新的属性。</span><span class="sxs-lookup"><span data-stu-id="8b660-193">The client specifies just the properties to update.</span></span>
- <span data-ttu-id="8b660-194">PUT 会替换整个实体。</span><span class="sxs-lookup"><span data-stu-id="8b660-194">PUT replaces the entire entity.</span></span>

<span data-ttu-id="8b660-195">PUT 的缺点是客户端必须在实体中，其中包括未更改的值发送的所有属性的值。</span><span class="sxs-lookup"><span data-stu-id="8b660-195">The disadvantage of PUT is that the client must send values for all of the properties in the entity, including values that are not changing.</span></span> <span data-ttu-id="8b660-196">[OData 规范](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719)状态修补程序是首选。</span><span class="sxs-lookup"><span data-stu-id="8b660-196">The [OData spec](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719) states that PATCH is preferred.</span></span>

<span data-ttu-id="8b660-197">在任何情况下，下面是修补程序和 PUT 方法的代码：</span><span class="sxs-lookup"><span data-stu-id="8b660-197">In any case, here is the code for both PATCH and PUT methods:</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample11.cs)]

<span data-ttu-id="8b660-198">控制器使用修补程序，对于**增量&lt;T&gt;** 类型以跟踪所做的更改。</span><span class="sxs-lookup"><span data-stu-id="8b660-198">In the case of PATCH, the controller uses the **Delta&lt;T&gt;** type to track the changes.</span></span>

## <a name="delete-an-entity"></a><span data-ttu-id="8b660-199">删除实体</span><span class="sxs-lookup"><span data-stu-id="8b660-199">Delete an entity</span></span>

<span data-ttu-id="8b660-200">若要使客户端能够从数据库中删除某个产品，添加以下方法`ProductsController`。</span><span class="sxs-lookup"><span data-stu-id="8b660-200">To enable clients to delete a product from the database, add the following method to `ProductsController`.</span></span>

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample12.cs)]
