---
uid: web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
title: 开始使用 ASP.NET Web API 2 (C#)
author: MikeWasson
description: HTTP 不只是为了提供网页。 它也是用于生成 Api，用于公开服务和数据的强大平台。 HTTP 是简单、 灵活和 ubiq...
ms.author: riande
ms.date: 11/28/2017
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
msc.type: authoredcontent
ms.openlocfilehash: 7bec95af4532535f0d620bfe6862958907466874
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444254"
---
<a name="get-started-with-aspnet-web-api-2-c"></a><span data-ttu-id="83195-105">开始使用 ASP.NET Web API 2 (C#)</span><span class="sxs-lookup"><span data-stu-id="83195-105">Get Started with ASP.NET Web API 2 (C#)</span></span>
====================
<span data-ttu-id="83195-106">通过[Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="83195-106">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="83195-107">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="83195-107">Download Completed Project</span></span>](https://code.msdn.microsoft.com/Sample-code-of-Getting-c56ccb28)

<span data-ttu-id="83195-108">HTTP 不只是为了提供网页。</span><span class="sxs-lookup"><span data-stu-id="83195-108">HTTP is not just for serving up web pages.</span></span> <span data-ttu-id="83195-109">HTTP 也是一个强大的平台，用于构建公开服务和数据的 Api。</span><span class="sxs-lookup"><span data-stu-id="83195-109">HTTP is also a powerful platform for building APIs that expose services and data.</span></span> <span data-ttu-id="83195-110">HTTP 是简单、 灵活且无所不在。</span><span class="sxs-lookup"><span data-stu-id="83195-110">HTTP is simple, flexible, and ubiquitous.</span></span> <span data-ttu-id="83195-111">您可以将几乎任何平台都有有 HTTP 库，因此 HTTP 服务可满足范围广泛的客户端，包括浏览器、 移动设备和传统桌面应用程序。</span><span class="sxs-lookup"><span data-stu-id="83195-111">Almost any platform that you can think of has an HTTP library, so HTTP services can reach a broad range of clients, including browsers, mobile devices, and traditional desktop applications.</span></span>

<span data-ttu-id="83195-112">ASP.NET Web API 是一个框架，用于.NET Framework 之上构建 web Api。</span><span class="sxs-lookup"><span data-stu-id="83195-112">ASP.NET Web API is a framework for building web APIs on top of the .NET Framework.</span></span> <span data-ttu-id="83195-113">在本教程中，您将使用 ASP.NET Web API 创建的 web API 返回的产品列表。</span><span class="sxs-lookup"><span data-stu-id="83195-113">In this tutorial, you will use ASP.NET Web API to create a web API that returns a list of products.</span></span>

## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="83195-114">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="83195-114">Software versions used in the tutorial</span></span>

- [<span data-ttu-id="83195-115">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="83195-115">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
- <span data-ttu-id="83195-116">Web API 2</span><span class="sxs-lookup"><span data-stu-id="83195-116">Web API 2</span></span>

<span data-ttu-id="83195-117">请参阅[使用 ASP.NET Core 和 Visual Studio 为 Windows 创建 web API](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api)为本教程中的较新版本。</span><span class="sxs-lookup"><span data-stu-id="83195-117">See [Create a web API with ASP.NET Core and Visual Studio for Windows](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api) for a newer version of this tutorial.</span></span>

## <a name="create-a-web-api-project"></a><span data-ttu-id="83195-118">创建 Web API 项目</span><span class="sxs-lookup"><span data-stu-id="83195-118">Create a Web API Project</span></span>

<span data-ttu-id="83195-119">在本教程中，您将使用 ASP.NET Web API 创建的 web API 返回的产品列表。</span><span class="sxs-lookup"><span data-stu-id="83195-119">In this tutorial, you will use ASP.NET Web API to create a web API that returns a list of products.</span></span> <span data-ttu-id="83195-120">前端的 web 页面使用 jQuery 来显示结果。</span><span class="sxs-lookup"><span data-stu-id="83195-120">The front-end web page uses jQuery to display the results.</span></span>

![](tutorial-your-first-web-api/_static/image1.png)

<span data-ttu-id="83195-121">启动 Visual Studio 并选择**新的项目**从**启动**页。</span><span class="sxs-lookup"><span data-stu-id="83195-121">Start Visual Studio and select **New Project** from the **Start** page.</span></span> <span data-ttu-id="83195-122">或者，从**文件**菜单中，选择**新建**，然后**项目**。</span><span class="sxs-lookup"><span data-stu-id="83195-122">Or, from the **File** menu, select **New** and then **Project**.</span></span>

<span data-ttu-id="83195-123">在中**模板**窗格中，选择**已安装的模板**展开**Visual C#** 节点。</span><span class="sxs-lookup"><span data-stu-id="83195-123">In the **Templates** pane, select **Installed Templates** and expand the **Visual C#** node.</span></span> <span data-ttu-id="83195-124">下**Visual C#**，选择**Web**。</span><span class="sxs-lookup"><span data-stu-id="83195-124">Under **Visual C#**, select **Web**.</span></span> <span data-ttu-id="83195-125">在项目模板列表中选择**ASP.NET Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="83195-125">In the list of project templates, select **ASP.NET Web Application**.</span></span> <span data-ttu-id="83195-126">命名项目"ProductsApp"，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="83195-126">Name the project "ProductsApp" and click **OK**.</span></span>

![](tutorial-your-first-web-api/_static/image2.png)

<span data-ttu-id="83195-127">在中**新建 ASP.NET 项目**对话框中，选择**空**模板。</span><span class="sxs-lookup"><span data-stu-id="83195-127">In the **New ASP.NET Project** dialog, select the **Empty** template.</span></span> <span data-ttu-id="83195-128">下&quot;添加文件夹和核心引用&quot;，检查**Web API**。</span><span class="sxs-lookup"><span data-stu-id="83195-128">Under &quot;Add folders and core references for&quot;, check **Web API**.</span></span> <span data-ttu-id="83195-129">单击 **“确定”**。</span><span class="sxs-lookup"><span data-stu-id="83195-129">Click **OK**.</span></span>

![](tutorial-your-first-web-api/_static/image3.png)

> [!NOTE]
> <span data-ttu-id="83195-130">此外可以创建 Web API 项目中使用&quot;Web API&quot;模板。</span><span class="sxs-lookup"><span data-stu-id="83195-130">You can also create a Web API project using the &quot;Web API&quot; template.</span></span> <span data-ttu-id="83195-131">Web API 模板使用 ASP.NET MVC 提供 API 帮助页。</span><span class="sxs-lookup"><span data-stu-id="83195-131">The Web API template uses ASP.NET MVC to provide API help pages.</span></span> <span data-ttu-id="83195-132">因为我想要显示而无需 MVC Web API，我正在本教程中使用空模板。</span><span class="sxs-lookup"><span data-stu-id="83195-132">I'm using the Empty template for this tutorial because I want to show Web API without MVC.</span></span> <span data-ttu-id="83195-133">一般情况下，不需要知道要使用 Web API 的 ASP.NET MVC。</span><span class="sxs-lookup"><span data-stu-id="83195-133">In general, you don't need to know ASP.NET MVC to use Web API.</span></span>


## <a name="adding-a-model"></a><span data-ttu-id="83195-134">添加模型</span><span class="sxs-lookup"><span data-stu-id="83195-134">Adding a Model</span></span>

<span data-ttu-id="83195-135">模型是表示应用程序中的数据的对象。</span><span class="sxs-lookup"><span data-stu-id="83195-135">A *model* is an object that represents the data in your application.</span></span> <span data-ttu-id="83195-136">ASP.NET Web API 可以自动序列化到 JSON、 XML 或其他某种格式，您的模型，然后将序列化的数据写入到 HTTP 响应消息的正文。</span><span class="sxs-lookup"><span data-stu-id="83195-136">ASP.NET Web API can automatically serialize your model to JSON, XML, or some other format, and then write the serialized data into the body of the HTTP response message.</span></span> <span data-ttu-id="83195-137">只要客户端可以读取序列化格式，它可以反序列化对象。</span><span class="sxs-lookup"><span data-stu-id="83195-137">As long as a client can read the serialization format, it can deserialize the object.</span></span> <span data-ttu-id="83195-138">大多数客户端可以分析 XML 或 JSON。</span><span class="sxs-lookup"><span data-stu-id="83195-138">Most clients can parse either XML or JSON.</span></span> <span data-ttu-id="83195-139">此外，客户端可以指示它希望通过 HTTP 请求消息中设置 Accept 标头的格式。</span><span class="sxs-lookup"><span data-stu-id="83195-139">Moreover, the client can indicate which format it wants by setting the Accept header in the HTTP request message.</span></span>

<span data-ttu-id="83195-140">让我们首先创建一个表示产品的简单模型。</span><span class="sxs-lookup"><span data-stu-id="83195-140">Let's start by creating a simple model that represents a product.</span></span>

<span data-ttu-id="83195-141">如果解决方案资源管理器尚不可见，请单击**视图**菜单，然后选择**解决方案资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="83195-141">If Solution Explorer is not already visible, click the **View** menu and select **Solution Explorer**.</span></span> <span data-ttu-id="83195-142">在解决方案资源管理器，右键单击模型文件夹。</span><span class="sxs-lookup"><span data-stu-id="83195-142">In Solution Explorer, right-click the Models folder.</span></span> <span data-ttu-id="83195-143">从上下文菜单中，选择**外**然后选择**类**。</span><span class="sxs-lookup"><span data-stu-id="83195-143">From the context menu, select **Add** then select **Class**.</span></span>

![](tutorial-your-first-web-api/_static/image4.png)

<span data-ttu-id="83195-144">将类命名&quot;产品&quot;。</span><span class="sxs-lookup"><span data-stu-id="83195-144">Name the class &quot;Product&quot;.</span></span> <span data-ttu-id="83195-145">添加以下属性来`Product`类。</span><span class="sxs-lookup"><span data-stu-id="83195-145">Add the following properties to the `Product` class.</span></span>

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample1.cs)]

## <a name="adding-a-controller"></a><span data-ttu-id="83195-146">添加控制器</span><span class="sxs-lookup"><span data-stu-id="83195-146">Adding a Controller</span></span>

<span data-ttu-id="83195-147">在 Web API 中，*控制器*是处理 HTTP 请求的对象。</span><span class="sxs-lookup"><span data-stu-id="83195-147">In Web API, a *controller* is an object that handles HTTP requests.</span></span> <span data-ttu-id="83195-148">我们将添加一个可以返回的产品列表或一个产品按 ID 指定的控制器</span><span class="sxs-lookup"><span data-stu-id="83195-148">We'll add a controller that can return either a list of products or a single product specified by ID.</span></span>

> [!NOTE]
> <span data-ttu-id="83195-149">如果您使用过 ASP.NET MVC，您已熟悉控制器。</span><span class="sxs-lookup"><span data-stu-id="83195-149">If you have used ASP.NET MVC, you are already familiar with controllers.</span></span> <span data-ttu-id="83195-150">Web API 控制器类似于 MVC 控制器，但继承**ApiController**类而不是**控制器**类。</span><span class="sxs-lookup"><span data-stu-id="83195-150">Web API controllers are similar to MVC controllers, but inherit the **ApiController** class instead of the **Controller** class.</span></span>

<span data-ttu-id="83195-151">在中**解决方案资源管理器**，右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="83195-151">In **Solution Explorer**, right-click the Controllers folder.</span></span> <span data-ttu-id="83195-152">选择**外**，然后选择**控制器**。</span><span class="sxs-lookup"><span data-stu-id="83195-152">Select **Add** and then select **Controller**.</span></span>

![](tutorial-your-first-web-api/_static/image5.png)

<span data-ttu-id="83195-153">在中**添加基架**对话框中，选择**Web API 控制器-空**。</span><span class="sxs-lookup"><span data-stu-id="83195-153">In the **Add Scaffold** dialog, select **Web API Controller - Empty**.</span></span> <span data-ttu-id="83195-154">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="83195-154">Click **Add**.</span></span>

![](tutorial-your-first-web-api/_static/image6.png)

<span data-ttu-id="83195-155">在中**添加控制器**对话框中，将控制器命名&quot;ProductsController&quot;。</span><span class="sxs-lookup"><span data-stu-id="83195-155">In the **Add Controller** dialog, name the controller &quot;ProductsController&quot;.</span></span> <span data-ttu-id="83195-156">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="83195-156">Click **Add**.</span></span>

![](tutorial-your-first-web-api/_static/image7.png)

<span data-ttu-id="83195-157">基架将创建名为 ProductsController.cs 控制器文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="83195-157">The scaffolding creates a file named ProductsController.cs in the Controllers folder.</span></span>

![](tutorial-your-first-web-api/_static/image8.png)

> [!NOTE]
> <span data-ttu-id="83195-158">无需将你的控制器放入名为控制器的文件夹。</span><span class="sxs-lookup"><span data-stu-id="83195-158">You don't need to put your controllers into a folder named Controllers.</span></span> <span data-ttu-id="83195-159">文件夹名称为只是组织的源文件的简便方法。</span><span class="sxs-lookup"><span data-stu-id="83195-159">The folder name is just a convenient way to organize your source files.</span></span>


<span data-ttu-id="83195-160">如果没有打开此文件，则双击文件将其打开。</span><span class="sxs-lookup"><span data-stu-id="83195-160">If this file is not open already, double-click the file to open it.</span></span> <span data-ttu-id="83195-161">此文件中的代码替换为以下：</span><span class="sxs-lookup"><span data-stu-id="83195-161">Replace the code in this file with the following:</span></span>

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample2.cs)]

<span data-ttu-id="83195-162">若要使示例尽量简单，产品存储在控制器类中的固定数组。</span><span class="sxs-lookup"><span data-stu-id="83195-162">To keep the example simple, products are stored in a fixed array inside the controller class.</span></span> <span data-ttu-id="83195-163">当然，在实际的应用程序，将查询数据库或使用某些其他外部数据源。</span><span class="sxs-lookup"><span data-stu-id="83195-163">Of course, in a real application, you would query a database or use some other external data source.</span></span>

<span data-ttu-id="83195-164">控制器定义返回产品的两个方法：</span><span class="sxs-lookup"><span data-stu-id="83195-164">The controller defines two methods that return products:</span></span>

- <span data-ttu-id="83195-165">`GetAllProducts`方法返回作为产品的整个列表**IEnumerable&lt;产品&gt;** 类型。</span><span class="sxs-lookup"><span data-stu-id="83195-165">The `GetAllProducts` method returns the entire list of products as an **IEnumerable&lt;Product&gt;** type.</span></span>
- <span data-ttu-id="83195-166">`GetProduct`方法查找单个产品通过其 id。</span><span class="sxs-lookup"><span data-stu-id="83195-166">The `GetProduct` method looks up a single product by its ID.</span></span>

<span data-ttu-id="83195-167">就这么简单！</span><span class="sxs-lookup"><span data-stu-id="83195-167">That's it!</span></span> <span data-ttu-id="83195-168">具有工作 web API。</span><span class="sxs-lookup"><span data-stu-id="83195-168">You have a working web API.</span></span> <span data-ttu-id="83195-169">在控制器上的每个方法对应于一个或多个 Uri:</span><span class="sxs-lookup"><span data-stu-id="83195-169">Each method on the controller corresponds to one or more URIs:</span></span>

| <span data-ttu-id="83195-170">控制器方法</span><span class="sxs-lookup"><span data-stu-id="83195-170">Controller Method</span></span> | <span data-ttu-id="83195-171">URI</span><span class="sxs-lookup"><span data-stu-id="83195-171">URI</span></span> |
| --- | --- |
| <span data-ttu-id="83195-172">GetAllProducts</span><span class="sxs-lookup"><span data-stu-id="83195-172">GetAllProducts</span></span> | <span data-ttu-id="83195-173">/api/products</span><span class="sxs-lookup"><span data-stu-id="83195-173">/api/products</span></span> |
| <span data-ttu-id="83195-174">GetProduct</span><span class="sxs-lookup"><span data-stu-id="83195-174">GetProduct</span></span> | <span data-ttu-id="83195-175">/api/products/*id*</span><span class="sxs-lookup"><span data-stu-id="83195-175">/api/products/*id*</span></span> |

<span data-ttu-id="83195-176">有关`GetProduct`方法， *id*在 URI 中是一个占位符。</span><span class="sxs-lookup"><span data-stu-id="83195-176">For the `GetProduct` method, the *id* in the URI is a placeholder.</span></span> <span data-ttu-id="83195-177">例如，若要获取 id 为 5 的产品，URI 是`api/products/5`。</span><span class="sxs-lookup"><span data-stu-id="83195-177">For example, to get the product with ID of 5, the URI is `api/products/5`.</span></span>

<span data-ttu-id="83195-178">有关 Web API 如何将 HTTP 请求路由到控制器方法的详细信息，请参阅[ASP.NET Web API 中的路由](../web-api-routing-and-actions/routing-in-aspnet-web-api.md)。</span><span class="sxs-lookup"><span data-stu-id="83195-178">For more information about how Web API routes HTTP requests to controller methods, see [Routing in ASP.NET Web API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md).</span></span>

## <a name="calling-the-web-api-with-javascript-and-jquery"></a><span data-ttu-id="83195-179">调用 Web API 使用 Javascript 和 jQuery</span><span class="sxs-lookup"><span data-stu-id="83195-179">Calling the Web API with Javascript and jQuery</span></span>

<span data-ttu-id="83195-180">在本部分中，我们将添加使用 AJAX 来调用 web API 的 HTML 页。</span><span class="sxs-lookup"><span data-stu-id="83195-180">In this section, we'll add an HTML page that uses AJAX to call the web API.</span></span> <span data-ttu-id="83195-181">我们将使用 jQuery 进行 AJAX 调用以及使用结果更新页面。</span><span class="sxs-lookup"><span data-stu-id="83195-181">We'll use jQuery to make the AJAX calls and also to update the page with the results.</span></span>

<span data-ttu-id="83195-182">在解决方案资源管理器，右键单击该项目并选择**外**，然后选择**新项**。</span><span class="sxs-lookup"><span data-stu-id="83195-182">In Solution Explorer, right-click the project and select **Add**, then select **New Item**.</span></span>

![](tutorial-your-first-web-api/_static/image9.png)

<span data-ttu-id="83195-183">在中**添加新项**对话框中，选择**Web**节点下的**Visual C#**，然后选择**HTML 页**项。</span><span class="sxs-lookup"><span data-stu-id="83195-183">In the **Add New Item** dialog, select the **Web** node under **Visual C#**, and then select the **HTML Page** item.</span></span> <span data-ttu-id="83195-184">将该页命名为&quot;index.html&quot;。</span><span class="sxs-lookup"><span data-stu-id="83195-184">Name the page &quot;index.html&quot;.</span></span>

![](tutorial-your-first-web-api/_static/image10.png)

<span data-ttu-id="83195-185">使用以下标记替换此文件中的所有内容：</span><span class="sxs-lookup"><span data-stu-id="83195-185">Replace everything in this file with the following:</span></span>

[!code-html[Main](tutorial-your-first-web-api/samples/sample3.html)]

<span data-ttu-id="83195-186">有多种方式可以获取 jQuery。</span><span class="sxs-lookup"><span data-stu-id="83195-186">There are several ways to get jQuery.</span></span> <span data-ttu-id="83195-187">在此示例中，我使用了[Microsoft Ajax CDN](../../../ajax/cdn/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="83195-187">In this example, I used the [Microsoft Ajax CDN](../../../ajax/cdn/overview.md).</span></span> <span data-ttu-id="83195-188">你也可以从下载[ http://jquery.com/ ](http://jquery.com/)，和 ASP.NET"Web API"项目模板包括 jQuery 也。</span><span class="sxs-lookup"><span data-stu-id="83195-188">You can also download it from [http://jquery.com/](http://jquery.com/), and the ASP.NET "Web API" project template includes jQuery as well.</span></span>

### <a name="getting-a-list-of-products"></a><span data-ttu-id="83195-189">获取产品列表</span><span class="sxs-lookup"><span data-stu-id="83195-189">Getting a List of Products</span></span>

<span data-ttu-id="83195-190">若要获取的产品的列表，请将发送到 HTTP GET 请求&quot;/api/产品&quot;。</span><span class="sxs-lookup"><span data-stu-id="83195-190">To get a list of products, send an HTTP GET request to &quot;/api/products&quot;.</span></span>

<span data-ttu-id="83195-191">JQuery[先前所述 getJSON](http://api.jquery.com/jQuery.getJSON/)函数发送 AJAX 请求。</span><span class="sxs-lookup"><span data-stu-id="83195-191">The jQuery [getJSON](http://api.jquery.com/jQuery.getJSON/) function sends an AJAX request.</span></span> <span data-ttu-id="83195-192">响应包含 JSON 对象的数组。</span><span class="sxs-lookup"><span data-stu-id="83195-192">For response contains array of JSON objects.</span></span> <span data-ttu-id="83195-193">`done`函数指定如果请求成功，则调用的回调。</span><span class="sxs-lookup"><span data-stu-id="83195-193">The `done` function specifies a callback that is called if the request succeeds.</span></span> <span data-ttu-id="83195-194">在回调中，我们使用的产品信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="83195-194">In the callback, we update the DOM with the product information.</span></span>

[!code-html[Main](tutorial-your-first-web-api/samples/sample4.html)]

### <a name="getting-a-product-by-id"></a><span data-ttu-id="83195-195">获取按 ID 的产品</span><span class="sxs-lookup"><span data-stu-id="83195-195">Getting a Product By ID</span></span>

<span data-ttu-id="83195-196">若要获取产品的 ID，将发送到 HTTP GET 请求&quot;/api/产品/*id*&quot;，其中*id*是产品 id。</span><span class="sxs-lookup"><span data-stu-id="83195-196">To get a product by ID, send an HTTP GET request to &quot;/api/products/*id*&quot;, where *id* is the product ID.</span></span>

[!code-javascript[Main](tutorial-your-first-web-api/samples/sample5.js)]

<span data-ttu-id="83195-197">我们仍调用`getJSON`为了发送 AJAX 请求，但这次我们将该 ID 在请求 URI 中。</span><span class="sxs-lookup"><span data-stu-id="83195-197">We still call `getJSON` to send the AJAX request, but this time we put the ID in the request URI.</span></span> <span data-ttu-id="83195-198">此请求的响应是一种产品的 JSON 表示形式。</span><span class="sxs-lookup"><span data-stu-id="83195-198">The response from this request is a JSON representation of a single product.</span></span>

## <a name="running-the-application"></a><span data-ttu-id="83195-199">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="83195-199">Running the Application</span></span>

<span data-ttu-id="83195-200">按 F5 开始调试应用程序。</span><span class="sxs-lookup"><span data-stu-id="83195-200">Press F5 to start debugging the application.</span></span> <span data-ttu-id="83195-201">网页应如下所示：</span><span class="sxs-lookup"><span data-stu-id="83195-201">The web page should look like the following:</span></span>

![](tutorial-your-first-web-api/_static/image11.png)

<span data-ttu-id="83195-202">若要获取按 ID 的产品，请输入的 ID，并单击搜索:</span><span class="sxs-lookup"><span data-stu-id="83195-202">To get a product by ID, enter the ID and click Search:</span></span>

![](tutorial-your-first-web-api/_static/image12.png)

<span data-ttu-id="83195-203">如果输入了无效的 ID，则服务器将返回 HTTP 错误：</span><span class="sxs-lookup"><span data-stu-id="83195-203">If you enter an invalid ID, the server returns an HTTP error:</span></span>

![](tutorial-your-first-web-api/_static/image13.png)

## <a name="using-f12-to-view-the-http-request-and-response"></a><span data-ttu-id="83195-204">使用 f12 键查看 HTTP 请求和响应</span><span class="sxs-lookup"><span data-stu-id="83195-204">Using F12 to View the HTTP Request and Response</span></span>

<span data-ttu-id="83195-205">当您正在使用的 HTTP 服务时，它可以是非常有用，可以查看 HTTP 请求和请求消息。</span><span class="sxs-lookup"><span data-stu-id="83195-205">When you are working with an HTTP service, it can be very useful to see the HTTP request and request messages.</span></span> <span data-ttu-id="83195-206">可以在 Internet Explorer 9 中使用 F12 开发人员工具来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="83195-206">You can do this by using the F12 developer tools in Internet Explorer 9.</span></span> <span data-ttu-id="83195-207">在 Internet Explorer 9 中，按**F12**打开工具。</span><span class="sxs-lookup"><span data-stu-id="83195-207">From Internet Explorer 9, press **F12** to open the tools.</span></span> <span data-ttu-id="83195-208">单击**网络**选项卡并按**启动捕获**。</span><span class="sxs-lookup"><span data-stu-id="83195-208">Click the **Network** tab and press **Start Capturing**.</span></span> <span data-ttu-id="83195-209">现在，转回 web 页并按**F5**来重新加载 web 页。</span><span class="sxs-lookup"><span data-stu-id="83195-209">Now go back to the web page and press **F5** to reload the web page.</span></span> <span data-ttu-id="83195-210">Internet Explorer 将捕获在浏览器和 web 服务器之间的 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="83195-210">Internet Explorer will capture the HTTP traffic between the browser and the web server.</span></span> <span data-ttu-id="83195-211">摘要视图显示了一个页面的所有网络流量：</span><span class="sxs-lookup"><span data-stu-id="83195-211">The summary view shows all the network traffic for a page:</span></span>

![](tutorial-your-first-web-api/_static/image14.png)

<span data-ttu-id="83195-212">找到的相对 URI 的条目"api/产品 /"。</span><span class="sxs-lookup"><span data-stu-id="83195-212">Locate the entry for the relative URI "api/products/".</span></span> <span data-ttu-id="83195-213">选择此项，然后单击**转到详细视图**。</span><span class="sxs-lookup"><span data-stu-id="83195-213">Select this entry and click **Go to detailed view**.</span></span> <span data-ttu-id="83195-214">在详细信息视图中，有选项卡以查看请求和响应标头和正文。</span><span class="sxs-lookup"><span data-stu-id="83195-214">In the detail view, there are tabs to view the request and response headers and bodies.</span></span> <span data-ttu-id="83195-215">例如，如果您单击**请求标头**选项卡上，可以看到客户端请求&quot;应用程序 /json&quot; Accept 标头中。</span><span class="sxs-lookup"><span data-stu-id="83195-215">For example, if you click the **Request headers** tab, you can see that the client requested &quot;application/json&quot; in the Accept header.</span></span>

![](tutorial-your-first-web-api/_static/image15.png)

<span data-ttu-id="83195-216">如果单击响应正文选项卡，可以看到如何将产品列表已序列化为 JSON。</span><span class="sxs-lookup"><span data-stu-id="83195-216">If you click the Response body tab, you can see how the product list was serialized to JSON.</span></span> <span data-ttu-id="83195-217">其他浏览器具有类似的功能。</span><span class="sxs-lookup"><span data-stu-id="83195-217">Other browsers have similar functionality.</span></span> <span data-ttu-id="83195-218">另一个有用工具是[Fiddler](http://www.fiddler2.com/fiddler2/)、 一个 web 调试代理。</span><span class="sxs-lookup"><span data-stu-id="83195-218">Another useful tool is [Fiddler](http://www.fiddler2.com/fiddler2/), a web debugging proxy.</span></span> <span data-ttu-id="83195-219">您可以使用 Fiddler，若要查看你的 HTTP 流量，以及编写 HTTP 请求，这将使您可以完全控制 HTTP 标头在请求中。</span><span class="sxs-lookup"><span data-stu-id="83195-219">You can use Fiddler to view your HTTP traffic, and also to compose HTTP requests, which gives you full control over the HTTP headers in the request.</span></span>

## <a name="see-this-app-running-on-azure"></a><span data-ttu-id="83195-220">请参阅在 Azure 上运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="83195-220">See this App Running on Azure</span></span>

<span data-ttu-id="83195-221">若要查看已完成的站点作为实时 web 应用运行吗？</span><span class="sxs-lookup"><span data-stu-id="83195-221">Would you like to see the finished site running as a live web app?</span></span> <span data-ttu-id="83195-222">只需单击下面的按钮，可以将完整版本的应用部署到 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="83195-222">You can deploy a complete version of the app to your Azure account by simply clicking the following button.</span></span>

[![](https://azuredeploy.net/deploybutton.png)](https://deploy.azure.com/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/WebAPI-ProductsApp#/form/setup)

<span data-ttu-id="83195-223">需要一个 Azure 帐户才能将此解决方案部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="83195-223">You need an Azure account to deploy this solution to Azure.</span></span> <span data-ttu-id="83195-224">如果你还没有帐户，你具有以下选项：</span><span class="sxs-lookup"><span data-stu-id="83195-224">If you do not already have an account, you have the following options:</span></span>

- <span data-ttu-id="83195-225">[免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604)-获取信用额度可用于试用付费版 Azure 服务，甚至在用之后最多可以保留帐户并使用免费的 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="83195-225">[Open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="83195-226">[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604)-MSDN 订阅提供信用额度可以用于付费版 Azure 服务的每个月。</span><span class="sxs-lookup"><span data-stu-id="83195-226">[Activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

## <a name="next-steps"></a><span data-ttu-id="83195-227">后续步骤</span><span class="sxs-lookup"><span data-stu-id="83195-227">Next Steps</span></span>

- <span data-ttu-id="83195-228">支持 POST、 PUT 和 DELETE 操作，并将写入到数据库的 HTTP 服务的更完整示例，请参阅[Entity Framework 6 Web API 2](../data/using-web-api-with-entity-framework/part-1.md)。</span><span class="sxs-lookup"><span data-stu-id="83195-228">For a more complete example of an HTTP service that supports POST, PUT, and DELETE actions and writes to a database, see [Using Web API 2 with Entity Framework 6](../data/using-web-api-with-entity-framework/part-1.md).</span></span>
- <span data-ttu-id="83195-229">有关创建 HTTP 服务之上的流畅且响应迅速的 web 应用程序的详细信息，请参阅[ASP.NET 单页面应用程序](../../../single-page-application/index.md)。</span><span class="sxs-lookup"><span data-stu-id="83195-229">For more about creating fluid and responsive web applications on top of an HTTP service, see [ASP.NET Single Page Application](../../../single-page-application/index.md).</span></span>
- <span data-ttu-id="83195-230">有关如何将 Visual Studio web 项目部署到 Azure 应用服务的信息，请参阅[在 Azure 应用服务中创建 ASP.NET web 应用](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/)。</span><span class="sxs-lookup"><span data-stu-id="83195-230">For information about how to deploy a Visual Studio web project to Azure App Service, see [Create an ASP.NET web app in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).</span></span>
