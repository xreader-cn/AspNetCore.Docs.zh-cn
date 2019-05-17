---
title: 教程：使用 ASP.NET Core MVC 创建 Web API
author: rick-anderson
description: 使用 ASP.NET Core MVC 构建 Web API
ms.author: riande
ms.custom: mvc
ms.date: 02/4/2019
uid: tutorials/first-web-api
ms.openlocfilehash: dce2926e3cb19d6ac8d2af0e9b96c31dee1d92fd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087527"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a><span data-ttu-id="d9c0c-103">教程：使用 ASP.NET Core MVC 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="d9c0c-103">Tutorial: Create a web API with ASP.NET Core MVC</span></span>

<span data-ttu-id="d9c0c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="d9c0c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="d9c0c-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="d9c0c-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d9c0c-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-107">Create a web API project.</span></span>
> * <span data-ttu-id="d9c0c-108">添加模型类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-108">Add a model class.</span></span>
> * <span data-ttu-id="d9c0c-109">创建数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-109">Create the database context.</span></span>
> * <span data-ttu-id="d9c0c-110">注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-110">Register the database context.</span></span>
> * <span data-ttu-id="d9c0c-111">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-111">Add a controller.</span></span>
> * <span data-ttu-id="d9c0c-112">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="d9c0c-113">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="d9c0c-114">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-114">Specify return values.</span></span>
> * <span data-ttu-id="d9c0c-115">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="d9c0c-116">使用 jQuery 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="d9c0c-117">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="d9c0c-118">概述</span><span class="sxs-lookup"><span data-stu-id="d9c0c-118">Overview</span></span>

<span data-ttu-id="d9c0c-119">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d9c0c-120">API</span><span class="sxs-lookup"><span data-stu-id="d9c0c-120">API</span></span> | <span data-ttu-id="d9c0c-121">说明</span><span class="sxs-lookup"><span data-stu-id="d9c0c-121">Description</span></span> | <span data-ttu-id="d9c0c-122">请求正文</span><span class="sxs-lookup"><span data-stu-id="d9c0c-122">Request body</span></span> | <span data-ttu-id="d9c0c-123">响应正文</span><span class="sxs-lookup"><span data-stu-id="d9c0c-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="d9c0c-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="d9c0c-124">GET /api/todo</span></span> | <span data-ttu-id="d9c0c-125">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-125">Get all to-do items</span></span> | <span data-ttu-id="d9c0c-126">None</span><span class="sxs-lookup"><span data-stu-id="d9c0c-126">None</span></span> | <span data-ttu-id="d9c0c-127">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="d9c0c-127">Array of to-do items</span></span>|
|<span data-ttu-id="d9c0c-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="d9c0c-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="d9c0c-129">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-129">Get an item by ID</span></span> | <span data-ttu-id="d9c0c-130">None</span><span class="sxs-lookup"><span data-stu-id="d9c0c-130">None</span></span> | <span data-ttu-id="d9c0c-131">待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-131">To-do item</span></span>|
|<span data-ttu-id="d9c0c-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="d9c0c-132">POST /api/todo</span></span> | <span data-ttu-id="d9c0c-133">添加新项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-133">Add a new item</span></span> | <span data-ttu-id="d9c0c-134">待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-134">To-do item</span></span> | <span data-ttu-id="d9c0c-135">待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-135">To-do item</span></span> |
|<span data-ttu-id="d9c0c-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="d9c0c-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="d9c0c-137">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d9c0c-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d9c0c-138">待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-138">To-do item</span></span> | <span data-ttu-id="d9c0c-139">None</span><span class="sxs-lookup"><span data-stu-id="d9c0c-139">None</span></span> |
|<span data-ttu-id="d9c0c-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d9c0c-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="d9c0c-141">删除项&nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d9c0c-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d9c0c-142">None</span><span class="sxs-lookup"><span data-stu-id="d9c0c-142">None</span></span> | <span data-ttu-id="d9c0c-143">None</span><span class="sxs-lookup"><span data-stu-id="d9c0c-143">None</span></span>|

<span data-ttu-id="d9c0c-144">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-144">The following diagram shows the design of the app.</span></span>

![客户端提交请求并从应用程序接收响应，客户端由左侧的框表示，应用程序则由右侧的框表示。](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="d9c0c-149">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="d9c0c-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d9c0c-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9c0c-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9c0c-151">从“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d9c0c-152">选择“ASP.NET Core Web 应用程序”模板。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-152">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="d9c0c-153">将项目命名为 TodoApi，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-153">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="d9c0c-154">在“新建 ASP.NET Core Web 应用程序 - TodoApi”对话框中，选择 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-154">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="d9c0c-155">选择“API”模板，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-155">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="d9c0c-156">请不要选择“启用 Docker 支持”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-156">Do **not** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d9c0c-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9c0c-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d9c0c-159">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d9c0c-160">将目录更改为 (`cd`) 包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-160">Change directories (`cd`) to the folder which will contain the project folder.</span></span>
* <span data-ttu-id="d9c0c-161">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="d9c0c-162">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-162">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="d9c0c-163">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-163">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d9c0c-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9c0c-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d9c0c-165">选择“文件” > “新建解决方案”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-165">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d9c0c-167">选择“.NET Core App” > “ASP.NET Core Web API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-167">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="d9c0c-169">在“配置新的 ASP.NET Core Web API”对话框中，接受默认的 \*.NET Core 2.2“目标框架”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-169">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="d9c0c-170">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="d9c0c-172">测试 API</span><span class="sxs-lookup"><span data-stu-id="d9c0c-172">Test the API</span></span>

<span data-ttu-id="d9c0c-173">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-173">The project template creates a `values` API.</span></span> <span data-ttu-id="d9c0c-174">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d9c0c-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9c0c-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d9c0c-176">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d9c0c-177">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d9c0c-178">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d9c0c-179">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d9c0c-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9c0c-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d9c0c-181">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d9c0c-182">在浏览器中，转到以下 URL：[https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-182">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d9c0c-183">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9c0c-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d9c0c-184">选择“运行” > “开始执行(调试)”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-184">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="d9c0c-185">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d9c0c-186">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d9c0c-187">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-187">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="d9c0c-188">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-188">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="d9c0c-189">添加模型类</span><span class="sxs-lookup"><span data-stu-id="d9c0c-189">Add a model class</span></span>

<span data-ttu-id="d9c0c-190">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d9c0c-191">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d9c0c-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9c0c-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9c0c-193">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d9c0c-194">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d9c0c-195">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="d9c0c-196">右键单击“Models”文件夹，然后选择“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d9c0c-197">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d9c0c-198">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d9c0c-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9c0c-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d9c0c-200">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d9c0c-201">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d9c0c-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9c0c-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d9c0c-203">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-203">Right-click the project.</span></span> <span data-ttu-id="d9c0c-204">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d9c0c-205">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-205">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d9c0c-207">右键单击“Models”文件夹，然后选择“添加” > “新建文件” > “常规” > “空类”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d9c0c-208">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d9c0c-209">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d9c0c-210">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d9c0c-211">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d9c0c-212">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="d9c0c-212">Add a database context</span></span>

<span data-ttu-id="d9c0c-213">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d9c0c-214">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d9c0c-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9c0c-215">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9c0c-216">右键单击“Models”文件夹，然后选择“添加” > “类”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-216">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d9c0c-217">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-217">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d9c0c-218">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9c0c-218">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d9c0c-219">将 `TodoContext` 类添加到“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-219">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d9c0c-220">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-220">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d9c0c-221">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="d9c0c-221">Register the database context</span></span>

<span data-ttu-id="d9c0c-222">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-222">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d9c0c-223">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-223">The container provides the service to controllers.</span></span>

<span data-ttu-id="d9c0c-224">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-224">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="d9c0c-225">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-225">The preceding code:</span></span>

* <span data-ttu-id="d9c0c-226">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-226">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d9c0c-227">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-227">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d9c0c-228">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-228">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d9c0c-229">添加控制器</span><span class="sxs-lookup"><span data-stu-id="d9c0c-229">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d9c0c-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9c0c-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9c0c-231">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-231">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d9c0c-232">选择 **添加** > **新建项**。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-232">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="d9c0c-233">在“添加新项”对话框中，选择“API 控制器类”模板。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-233">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="d9c0c-234">将类命名为 TodoController，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-234">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d9c0c-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9c0c-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d9c0c-237">在“控制器”文件夹中，创建名为 `TodoController` 的类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-237">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="d9c0c-238">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-238">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="d9c0c-239">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-239">The preceding code:</span></span>

* <span data-ttu-id="d9c0c-240">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-240">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="d9c0c-241">使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-241">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="d9c0c-242">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-242">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d9c0c-243">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-243">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d9c0c-244">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-244">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d9c0c-245">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-245">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="d9c0c-246">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-246">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="d9c0c-247">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-247">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="d9c0c-248">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-248">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="d9c0c-249">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-249">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="d9c0c-250">添加 Get 方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-250">Add Get methods</span></span>

<span data-ttu-id="d9c0c-251">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-251">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="d9c0c-252">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-252">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="d9c0c-253">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-253">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="d9c0c-254">例如:</span><span class="sxs-lookup"><span data-stu-id="d9c0c-254">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="d9c0c-255">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-255">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="d9c0c-256">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="d9c0c-256">Routing and URL paths</span></span>

<span data-ttu-id="d9c0c-257">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-257">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d9c0c-258">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-258">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d9c0c-259">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-259">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="d9c0c-260">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-260">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d9c0c-261">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-261">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="d9c0c-262">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-262">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d9c0c-263">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-263">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d9c0c-264">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-264">This sample doesn't use a template.</span></span> <span data-ttu-id="d9c0c-265">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-265">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d9c0c-266">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-266">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d9c0c-267">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-267">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d9c0c-268">返回值</span><span class="sxs-lookup"><span data-stu-id="d9c0c-268">Return values</span></span>

<span data-ttu-id="d9c0c-269">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-269">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d9c0c-270">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-270">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d9c0c-271">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-271">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d9c0c-272">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-272">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d9c0c-273">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-273">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d9c0c-274">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-274">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d9c0c-275">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-275">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="d9c0c-276">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-276">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d9c0c-277">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-277">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="d9c0c-278">测试 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-278">Test the GetTodoItems method</span></span>

<span data-ttu-id="d9c0c-279">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-279">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d9c0c-280">安装 [Postman](https://www.getpostman.com/apps)</span><span class="sxs-lookup"><span data-stu-id="d9c0c-280">Install [Postman](https://www.getpostman.com/apps)</span></span>
* <span data-ttu-id="d9c0c-281">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-281">Start the web app.</span></span>
* <span data-ttu-id="d9c0c-282">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-282">Start Postman.</span></span>
* <span data-ttu-id="d9c0c-283">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="d9c0c-283">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="d9c0c-284">在“文件”>“设置”（“常规”\*选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-284">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="d9c0c-285">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-285">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="d9c0c-286">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-286">Create a new request.</span></span>
  * <span data-ttu-id="d9c0c-287">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-287">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="d9c0c-288">将请求 URL 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-288">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="d9c0c-289">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-289">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="d9c0c-290">在 Postman 中设置“两窗格视图”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-290">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d9c0c-291">选择“发送”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-291">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="d9c0c-293">添加创建方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-293">Add a Create method</span></span>

<span data-ttu-id="d9c0c-294">添加以下 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-294">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="d9c0c-295">正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-295">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="d9c0c-296">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-296">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d9c0c-297">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-297">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="d9c0c-298">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-298">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="d9c0c-299">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-299">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d9c0c-300">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-300">Adds a `Location` header to the response.</span></span> <span data-ttu-id="d9c0c-301">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-301">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="d9c0c-302">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-302">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d9c0c-303">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-303">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d9c0c-304">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-304">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="d9c0c-305">测试 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-305">Test the PostTodoItem method</span></span>

* <span data-ttu-id="d9c0c-306">生成项目。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-306">Build the project.</span></span>
* <span data-ttu-id="d9c0c-307">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-307">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d9c0c-308">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-308">Select the **Body** tab.</span></span>
* <span data-ttu-id="d9c0c-309">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-309">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d9c0c-310">将类型设置为 JSON (application/json)</span><span class="sxs-lookup"><span data-stu-id="d9c0c-310">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d9c0c-311">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-311">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d9c0c-312">选择“发送”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-312">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="d9c0c-314">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-314">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d9c0c-315">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="d9c0c-315">Test the location header URI</span></span>

* <span data-ttu-id="d9c0c-316">在“响应”窗格中选择“标头”选项卡。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-316">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d9c0c-317">复制“位置”标头值：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-317">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="d9c0c-319">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-319">Set the method to GET.</span></span>
* <span data-ttu-id="d9c0c-320">粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）</span><span class="sxs-lookup"><span data-stu-id="d9c0c-320">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="d9c0c-321">选择“发送”。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-321">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="d9c0c-322">添加 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-322">Add a PutTodoItem method</span></span>

<span data-ttu-id="d9c0c-323">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-323">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="d9c0c-324">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-324">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d9c0c-325">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-325">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d9c0c-326">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-326">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d9c0c-327">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-327">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d9c0c-328">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-328">If you get an error calling `PutTodoItem`, call `GET` to ensure there is an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d9c0c-329">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-329">Test the PutTodoItem method</span></span>

<span data-ttu-id="d9c0c-330">本示例使用内存数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-330">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="d9c0c-331">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-331">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d9c0c-332">调用 GET 以确保在进行 PUT 调用之前数据库中有一个项。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-332">Call GET to insure there is an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d9c0c-333">更新 id = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-333">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d9c0c-334">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-334">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="d9c0c-336">添加 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-336">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="d9c0c-337">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-337">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="d9c0c-338">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-338">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d9c0c-339">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="d9c0c-339">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d9c0c-340">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-340">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d9c0c-341">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-341">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d9c0c-342">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="d9c0c-342">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="d9c0c-343">选择“Send”</span><span class="sxs-lookup"><span data-stu-id="d9c0c-343">Select **Send**</span></span>

<span data-ttu-id="d9c0c-344">示例应用允许删除所有项，但是在删除最后一项后，模型类构造函数会在下次调用 API 时创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-344">The sample app allows you to delete all the items, but when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="d9c0c-345">使用 jQuery 调用 API</span><span class="sxs-lookup"><span data-stu-id="d9c0c-345">Call the API with jQuery</span></span>

<span data-ttu-id="d9c0c-346">在本部分中，添加了使用 jQuery 调用 Web API 的 HTML 页面。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-346">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="d9c0c-347">jQuery 启动请求，并用 API 响应中的详细信息更新页面。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-347">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="d9c0c-348">配置应用[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[启用默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-348">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="d9c0c-349">在项目目录中创建 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-349">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="d9c0c-350">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-350">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="d9c0c-351">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-351">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="d9c0c-352">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-352">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="d9c0c-353">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-353">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="d9c0c-354">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-354">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="d9c0c-355">打开 Properties\launchSettings.json。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-355">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="d9c0c-356">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-356">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="d9c0c-357">有多种方式可以获取 jQuery。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-357">There are several ways to get jQuery.</span></span> <span data-ttu-id="d9c0c-358">在前面的代码片段中，库是从 CDN 中加载的。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-358">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="d9c0c-359">此示例调用 API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-359">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="d9c0c-360">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-360">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="d9c0c-361">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="d9c0c-361">Get a list of to-do items</span></span>

<span data-ttu-id="d9c0c-362">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 函数将 `GET` 请求发送至 API，这将返回表示待办事项数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-362">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="d9c0c-363">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-363">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="d9c0c-364">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-364">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="d9c0c-365">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-365">Add a to-do item</span></span>

<span data-ttu-id="d9c0c-366">[Ajax](https://api.jquery.com/jquery.ajax/) 函数发送 `POST`，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-366">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="d9c0c-367">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-367">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="d9c0c-368">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-368">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="d9c0c-369">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-369">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="d9c0c-370">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-370">Update a to-do item</span></span>

<span data-ttu-id="d9c0c-371">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-371">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="d9c0c-372">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-372">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="d9c0c-373">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="d9c0c-373">Delete a to-do item</span></span>

<span data-ttu-id="d9c0c-374">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-374">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="d9c0c-375">其他资源</span><span class="sxs-lookup"><span data-stu-id="d9c0c-375">Additional resources</span></span>

<span data-ttu-id="d9c0c-376">[查看或下载本教程的示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-376">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="d9c0c-377">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-377">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="d9c0c-378">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-378">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="d9c0c-379">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="d9c0c-379">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a><span data-ttu-id="d9c0c-380">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d9c0c-380">Next steps</span></span>

<span data-ttu-id="d9c0c-381">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-381">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d9c0c-382">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-382">Create a web api project.</span></span>
> * <span data-ttu-id="d9c0c-383">添加模型类。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-383">Add a model class.</span></span>
> * <span data-ttu-id="d9c0c-384">创建数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-384">Create the database context.</span></span>
> * <span data-ttu-id="d9c0c-385">注册数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-385">Register the database context.</span></span>
> * <span data-ttu-id="d9c0c-386">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-386">Add a controller.</span></span>
> * <span data-ttu-id="d9c0c-387">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-387">Add CRUD methods.</span></span>
> * <span data-ttu-id="d9c0c-388">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-388">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="d9c0c-389">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-389">Specify return values.</span></span>
> * <span data-ttu-id="d9c0c-390">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-390">Call the web API with Postman.</span></span>
> * <span data-ttu-id="d9c0c-391">使用 jQuery 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="d9c0c-391">Call the web api with jQuery.</span></span>

<span data-ttu-id="d9c0c-392">前进到下一个教程，了解如何生成 API 帮助页：</span><span class="sxs-lookup"><span data-stu-id="d9c0c-392">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
