---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 08/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 855d05fa2b9c1a7572212c40adbe61bb396f4bac
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819834"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="4bc0f-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="4bc0f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="4bc0f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="4bc0f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="4bc0f-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bc0f-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4bc0f-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-107">Create a web API project.</span></span>
> * <span data-ttu-id="4bc0f-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4bc0f-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="4bc0f-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="4bc0f-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="4bc0f-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="4bc0f-113">概述</span><span class="sxs-lookup"><span data-stu-id="4bc0f-113">Overview</span></span>

<span data-ttu-id="4bc0f-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4bc0f-115">API</span><span class="sxs-lookup"><span data-stu-id="4bc0f-115">API</span></span> | <span data-ttu-id="4bc0f-116">说明</span><span class="sxs-lookup"><span data-stu-id="4bc0f-116">Description</span></span> | <span data-ttu-id="4bc0f-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-117">Request body</span></span> | <span data-ttu-id="4bc0f-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4bc0f-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4bc0f-119">GET /api/TodoItems</span></span> | <span data-ttu-id="4bc0f-120">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-120">Get all to-do items</span></span> | <span data-ttu-id="4bc0f-121">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-121">None</span></span> | <span data-ttu-id="4bc0f-122">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="4bc0f-122">Array of to-do items</span></span>|
|<span data-ttu-id="4bc0f-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4bc0f-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4bc0f-124">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-124">Get an item by ID</span></span> | <span data-ttu-id="4bc0f-125">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-125">None</span></span> | <span data-ttu-id="4bc0f-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-126">To-do item</span></span>|
|<span data-ttu-id="4bc0f-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4bc0f-127">POST /api/TodoItems</span></span> | <span data-ttu-id="4bc0f-128">添加新项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-128">Add a new item</span></span> | <span data-ttu-id="4bc0f-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-129">To-do item</span></span> | <span data-ttu-id="4bc0f-130">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-130">To-do item</span></span> |
|<span data-ttu-id="4bc0f-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4bc0f-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4bc0f-132">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4bc0f-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4bc0f-133">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-133">To-do item</span></span> | <span data-ttu-id="4bc0f-134">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-134">None</span></span> |
|<span data-ttu-id="4bc0f-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4bc0f-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4bc0f-136">删除项&nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4bc0f-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4bc0f-137">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-137">None</span></span> | <span data-ttu-id="4bc0f-138">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-138">None</span></span>|

<span data-ttu-id="4bc0f-139">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-139">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4bc0f-145">系统必备</span><span class="sxs-lookup"><span data-stu-id="4bc0f-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4bc0f-149">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="4bc0f-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-151">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4bc0f-152">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4bc0f-153">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4bc0f-154">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="4bc0f-155">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4bc0f-156">请不要选择“启用 Docker 支持”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-156">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4bc0f-159">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4bc0f-160">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4bc0f-161">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="4bc0f-162">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="4bc0f-163">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-163">The preceding commands:</span></span>

  * <span data-ttu-id="4bc0f-164">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="4bc0f-165">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4bc0f-167">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-167">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4bc0f-169">选择“.NET Core”>“应用”>“API”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4bc0f-171">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架选择为\*“.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="4bc0f-172">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4bc0f-174">测试 API</span><span class="sxs-lookup"><span data-stu-id="4bc0f-174">Test the API</span></span>

<span data-ttu-id="4bc0f-175">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="4bc0f-176">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bc0f-178">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4bc0f-179">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4bc0f-180">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4bc0f-181">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4bc0f-183">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4bc0f-184">在浏览器中，转到以下 URL：[https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4bc0f-186">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4bc0f-187">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4bc0f-188">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4bc0f-189">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="4bc0f-190">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4bc0f-191">添加模型类</span><span class="sxs-lookup"><span data-stu-id="4bc0f-191">Add a model class</span></span>

<span data-ttu-id="4bc0f-192">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4bc0f-193">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-195">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4bc0f-196">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4bc0f-197">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="4bc0f-198">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4bc0f-199">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4bc0f-200">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4bc0f-202">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4bc0f-203">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4bc0f-205">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-205">Right-click the project.</span></span> <span data-ttu-id="4bc0f-206">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4bc0f-207">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-207">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4bc0f-209">右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4bc0f-210">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4bc0f-211">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4bc0f-212">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4bc0f-213">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4bc0f-214">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-214">Add a database context</span></span>

<span data-ttu-id="4bc0f-215">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4bc0f-216">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="4bc0f-218">添加 Add Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="4bc0f-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="4bc0f-219">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="4bc0f-220">选中“包括预发行版”复选框  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="4bc0f-221">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="4bc0f-222">在左窗口中选择“Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="4bc0f-223">选中右窗格中的“项目”复选框，然后选择“安装”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="4bc0f-224">使用上述说明添加 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="4bc0f-226">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="4bc0f-227">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4bc0f-228">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4bc0f-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4bc0f-230">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4bc0f-231">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4bc0f-232">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-232">Register the database context</span></span>

<span data-ttu-id="4bc0f-233">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4bc0f-234">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="4bc0f-235">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="4bc0f-236">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-236">The preceding code:</span></span>

* <span data-ttu-id="4bc0f-237">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4bc0f-238">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4bc0f-239">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="4bc0f-240">构建控制器</span><span class="sxs-lookup"><span data-stu-id="4bc0f-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-242">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4bc0f-243">选择“添加”>“新建构建项”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4bc0f-244">选择“其操作使用实体框架的 API 控制器”，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="4bc0f-245">在“添加其操作使用实体框架的 API 控制器”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="4bc0f-246">在“模型类”中选择“TodoItem (TodoAPI.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="4bc0f-247">在“数据上下文类”中选择“TodoContext (TodoAPI.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="4bc0f-248">选择“添加” </span><span class="sxs-lookup"><span data-stu-id="4bc0f-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4bc0f-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4bc0f-250">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="4bc0f-251">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-251">The preceding commands:</span></span>

* <span data-ttu-id="4bc0f-252">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="4bc0f-253">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="4bc0f-254">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="4bc0f-255">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-255">The generated code:</span></span>

* <span data-ttu-id="4bc0f-256">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4bc0f-257">使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4bc0f-258">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4bc0f-259">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4bc0f-260">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4bc0f-261">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="4bc0f-262">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="4bc0f-263">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="4bc0f-264">正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4bc0f-265">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4bc0f-266"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="4bc0f-267">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="4bc0f-268">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4bc0f-269">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="4bc0f-270">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="4bc0f-271">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4bc0f-272">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4bc0f-273">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="4bc0f-274">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="4bc0f-274">Install Postman</span></span>

<span data-ttu-id="4bc0f-275">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4bc0f-276">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="4bc0f-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4bc0f-277">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-277">Start the web app.</span></span>
* <span data-ttu-id="4bc0f-278">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-278">Start Postman.</span></span>
* <span data-ttu-id="4bc0f-279">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="4bc0f-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="4bc0f-280">在“文件”>“设置”  （“常规”\*  选项卡）中，禁用“SSL 证书验证”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="4bc0f-281">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="4bc0f-282">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="4bc0f-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="4bc0f-283">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-283">Create a new request.</span></span>
* <span data-ttu-id="4bc0f-284">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4bc0f-285">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="4bc0f-286">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4bc0f-287">将类型设置为 JSON (application/json) </span><span class="sxs-lookup"><span data-stu-id="4bc0f-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4bc0f-288">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4bc0f-289">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-289">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4bc0f-291">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="4bc0f-291">Test the location header URI</span></span>

* <span data-ttu-id="4bc0f-292">在“响应”  窗格中选择“标头”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4bc0f-293">复制“位置”  标头值：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-293">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="4bc0f-295">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-295">Set the method to GET.</span></span>
* <span data-ttu-id="4bc0f-296">粘贴 URI（例如，`https://localhost:5001/api/TodoItems/1`）</span><span class="sxs-lookup"><span data-stu-id="4bc0f-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="4bc0f-297">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="4bc0f-298">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-298">Examine the GET methods</span></span>

<span data-ttu-id="4bc0f-299">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="4bc0f-300">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="4bc0f-301">例如:</span><span class="sxs-lookup"><span data-stu-id="4bc0f-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="4bc0f-302">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="4bc0f-303">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="4bc0f-303">Test Get with Postman</span></span>

* <span data-ttu-id="4bc0f-304">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-304">Create a new request.</span></span>
* <span data-ttu-id="4bc0f-305">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="4bc0f-306">将请求 URL 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="4bc0f-307">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="4bc0f-308">在 Postman 中设置“两窗格视图”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4bc0f-309">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-309">Select **Send**.</span></span>

<span data-ttu-id="4bc0f-310">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-310">This app uses an in-memory database.</span></span> <span data-ttu-id="4bc0f-311">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="4bc0f-312">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="4bc0f-313">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="4bc0f-313">Routing and URL paths</span></span>

<span data-ttu-id="4bc0f-314">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4bc0f-315">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4bc0f-316">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="4bc0f-317">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4bc0f-318">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="4bc0f-319">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4bc0f-320">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4bc0f-321">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-321">This sample doesn't use a template.</span></span> <span data-ttu-id="4bc0f-322">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4bc0f-323">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4bc0f-324">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4bc0f-325">返回值</span><span class="sxs-lookup"><span data-stu-id="4bc0f-325">Return values</span></span>

<span data-ttu-id="4bc0f-326">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4bc0f-327">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4bc0f-328">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4bc0f-329">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4bc0f-330">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4bc0f-331">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4bc0f-332">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4bc0f-333">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4bc0f-334">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="4bc0f-335">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-335">The PutTodoItem method</span></span>

<span data-ttu-id="4bc0f-336">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="4bc0f-337">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4bc0f-338">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4bc0f-339">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4bc0f-340">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4bc0f-341">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4bc0f-342">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="4bc0f-343">本示例使用内存数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4bc0f-344">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4bc0f-345">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4bc0f-346">更新 ID = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4bc0f-347">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-347">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="4bc0f-349">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="4bc0f-350">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="4bc0f-351">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4bc0f-352">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4bc0f-353">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4bc0f-354">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4bc0f-355">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="4bc0f-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="4bc0f-356">选择“Send” </span><span class="sxs-lookup"><span data-stu-id="4bc0f-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="4bc0f-357">从 jQuery 调用 API</span><span class="sxs-lookup"><span data-stu-id="4bc0f-357">Call the API from jQuery</span></span>

<span data-ttu-id="4bc0f-358">有关分步说明，请参阅[教程：使用 jQuery 调用 ASP.NET Core web API](xref:tutorials/web-api-jquery)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4bc0f-359">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4bc0f-360">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-360">Create a web API project.</span></span>
> * <span data-ttu-id="4bc0f-361">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="4bc0f-362">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-362">Add a controller.</span></span>
> * <span data-ttu-id="4bc0f-363">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="4bc0f-364">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="4bc0f-365">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-365">Specify return values.</span></span>
> * <span data-ttu-id="4bc0f-366">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="4bc0f-367">使用 jQuery 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="4bc0f-368">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="4bc0f-369">概述</span><span class="sxs-lookup"><span data-stu-id="4bc0f-369">Overview</span></span>

<span data-ttu-id="4bc0f-370">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="4bc0f-371">API</span><span class="sxs-lookup"><span data-stu-id="4bc0f-371">API</span></span> | <span data-ttu-id="4bc0f-372">说明</span><span class="sxs-lookup"><span data-stu-id="4bc0f-372">Description</span></span> | <span data-ttu-id="4bc0f-373">请求正文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-373">Request body</span></span> | <span data-ttu-id="4bc0f-374">响应正文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="4bc0f-375">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4bc0f-375">GET /api/TodoItems</span></span> | <span data-ttu-id="4bc0f-376">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-376">Get all to-do items</span></span> | <span data-ttu-id="4bc0f-377">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-377">None</span></span> | <span data-ttu-id="4bc0f-378">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="4bc0f-378">Array of to-do items</span></span>|
|<span data-ttu-id="4bc0f-379">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4bc0f-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="4bc0f-380">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-380">Get an item by ID</span></span> | <span data-ttu-id="4bc0f-381">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-381">None</span></span> | <span data-ttu-id="4bc0f-382">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-382">To-do item</span></span>|
|<span data-ttu-id="4bc0f-383">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="4bc0f-383">POST /api/TodoItems</span></span> | <span data-ttu-id="4bc0f-384">添加新项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-384">Add a new item</span></span> | <span data-ttu-id="4bc0f-385">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-385">To-do item</span></span> | <span data-ttu-id="4bc0f-386">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-386">To-do item</span></span> |
|<span data-ttu-id="4bc0f-387">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="4bc0f-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="4bc0f-388">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4bc0f-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="4bc0f-389">待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-389">To-do item</span></span> | <span data-ttu-id="4bc0f-390">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-390">None</span></span> |
|<span data-ttu-id="4bc0f-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4bc0f-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="4bc0f-392">删除项&nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="4bc0f-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="4bc0f-393">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-393">None</span></span> | <span data-ttu-id="4bc0f-394">无</span><span class="sxs-lookup"><span data-stu-id="4bc0f-394">None</span></span>|

<span data-ttu-id="4bc0f-395">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-395">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="4bc0f-401">系统必备</span><span class="sxs-lookup"><span data-stu-id="4bc0f-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-404">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="4bc0f-405">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="4bc0f-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-407">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4bc0f-408">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="4bc0f-409">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="4bc0f-410">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”    。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="4bc0f-411">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="4bc0f-412">请不要选择“启用 Docker 支持”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-412">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4bc0f-415">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="4bc0f-416">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="4bc0f-417">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="4bc0f-418">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="4bc0f-419">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-420">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4bc0f-421">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-421">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="4bc0f-423">选择“.NET Core”>“应用”>“API”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="4bc0f-425">在“配置新的 ASP.NET Core Web API”  对话框中，接受默认的 \*.NET Core 2.2  “目标框架”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="4bc0f-426">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="4bc0f-428">测试 API</span><span class="sxs-lookup"><span data-stu-id="4bc0f-428">Test the API</span></span>

<span data-ttu-id="4bc0f-429">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-429">The project template creates a `values` API.</span></span> <span data-ttu-id="4bc0f-430">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bc0f-432">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4bc0f-433">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="4bc0f-434">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="4bc0f-435">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4bc0f-437">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="4bc0f-438">在浏览器中，转到以下 URL：[https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-439">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4bc0f-440">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="4bc0f-441">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="4bc0f-442">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="4bc0f-443">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="4bc0f-444">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="4bc0f-445">添加模型类</span><span class="sxs-lookup"><span data-stu-id="4bc0f-445">Add a model class</span></span>

<span data-ttu-id="4bc0f-446">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="4bc0f-447">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-449">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="4bc0f-450">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4bc0f-451">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="4bc0f-452">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4bc0f-453">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="4bc0f-454">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4bc0f-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4bc0f-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4bc0f-456">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="4bc0f-457">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4bc0f-458">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4bc0f-459">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-459">Right-click the project.</span></span> <span data-ttu-id="4bc0f-460">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="4bc0f-461">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-461">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="4bc0f-463">右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="4bc0f-464">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="4bc0f-465">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="4bc0f-466">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="4bc0f-467">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="4bc0f-468">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-468">Add a database context</span></span>

<span data-ttu-id="4bc0f-469">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="4bc0f-470">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-472">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="4bc0f-473">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4bc0f-474">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4bc0f-475">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="4bc0f-476">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="4bc0f-477">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="4bc0f-477">Register the database context</span></span>

<span data-ttu-id="4bc0f-478">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4bc0f-479">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="4bc0f-480">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="4bc0f-481">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-481">The preceding code:</span></span>

* <span data-ttu-id="4bc0f-482">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="4bc0f-483">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="4bc0f-484">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="4bc0f-485">添加控制器</span><span class="sxs-lookup"><span data-stu-id="4bc0f-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-487">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="4bc0f-488">选择“添加”>“新项”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="4bc0f-489">在“添加新项”对话框中，选择“API 控制器类”模板   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="4bc0f-490">将类命名为 TodoController，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4bc0f-492">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4bc0f-493">在“控制器”文件夹中，创建名为 `TodoController` 的类  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="4bc0f-494">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="4bc0f-495">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-495">The preceding code:</span></span>

* <span data-ttu-id="4bc0f-496">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="4bc0f-497">使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="4bc0f-498">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="4bc0f-499">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="4bc0f-500">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="4bc0f-501">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="4bc0f-502">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="4bc0f-503">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="4bc0f-504">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="4bc0f-505">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="4bc0f-506">添加 Get 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-506">Add Get methods</span></span>

<span data-ttu-id="4bc0f-507">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="4bc0f-508">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="4bc0f-509">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-509">Stop the app if it's still running.</span></span> <span data-ttu-id="4bc0f-510">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="4bc0f-511">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="4bc0f-512">例如:</span><span class="sxs-lookup"><span data-stu-id="4bc0f-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="4bc0f-513">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="4bc0f-514">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="4bc0f-514">Routing and URL paths</span></span>

<span data-ttu-id="4bc0f-515">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="4bc0f-516">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="4bc0f-517">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="4bc0f-518">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="4bc0f-519">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="4bc0f-520">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="4bc0f-521">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="4bc0f-522">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-522">This sample doesn't use a template.</span></span> <span data-ttu-id="4bc0f-523">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="4bc0f-524">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="4bc0f-525">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="4bc0f-526">返回值</span><span class="sxs-lookup"><span data-stu-id="4bc0f-526">Return values</span></span>

<span data-ttu-id="4bc0f-527">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="4bc0f-528">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="4bc0f-529">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="4bc0f-530">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="4bc0f-531">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="4bc0f-532">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="4bc0f-533">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="4bc0f-534">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="4bc0f-535">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-535">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="4bc0f-536">测试 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="4bc0f-537">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="4bc0f-538">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="4bc0f-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="4bc0f-539">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-539">Start the web app.</span></span>
* <span data-ttu-id="4bc0f-540">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-540">Start Postman.</span></span>
* <span data-ttu-id="4bc0f-541">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="4bc0f-541">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bc0f-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bc0f-542">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bc0f-543">在“文件”>“设置”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-543">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4bc0f-544">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4bc0f-544">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="4bc0f-545">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-545">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="4bc0f-546">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-546">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="4bc0f-547">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-547">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="4bc0f-548">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-548">Create a new request.</span></span>
  * <span data-ttu-id="4bc0f-549">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-549">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="4bc0f-550">将请求 URL 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-550">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="4bc0f-551">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-551">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="4bc0f-552">在 Postman 中设置“两窗格视图”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-552">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="4bc0f-553">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-553">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="4bc0f-555">添加创建方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-555">Add a Create method</span></span>

<span data-ttu-id="4bc0f-556">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-556">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="4bc0f-557">正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-557">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="4bc0f-558">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="4bc0f-559">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-559">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="4bc0f-560">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-560">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="4bc0f-561">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-561">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="4bc0f-562">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-562">Adds a `Location` header to the response.</span></span> <span data-ttu-id="4bc0f-563">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-563">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="4bc0f-564">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-564">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="4bc0f-565">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-565">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="4bc0f-566">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-566">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="4bc0f-567">测试 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-567">Test the PostTodoItem method</span></span>

* <span data-ttu-id="4bc0f-568">生成项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-568">Build the project.</span></span>
* <span data-ttu-id="4bc0f-569">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-569">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="4bc0f-570">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-570">Select the **Body** tab.</span></span>
* <span data-ttu-id="4bc0f-571">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-571">Select the **raw** radio button.</span></span>
* <span data-ttu-id="4bc0f-572">将类型设置为 JSON (application/json) </span><span class="sxs-lookup"><span data-stu-id="4bc0f-572">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="4bc0f-573">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-573">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="4bc0f-574">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-574">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="4bc0f-576">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-576">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="4bc0f-577">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="4bc0f-577">Test the location header URI</span></span>

* <span data-ttu-id="4bc0f-578">在“响应”  窗格中选择“标头”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-578">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="4bc0f-579">复制“位置”  标头值：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-579">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="4bc0f-581">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-581">Set the method to GET.</span></span>
* <span data-ttu-id="4bc0f-582">粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）</span><span class="sxs-lookup"><span data-stu-id="4bc0f-582">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="4bc0f-583">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-583">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="4bc0f-584">添加 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-584">Add a PutTodoItem method</span></span>

<span data-ttu-id="4bc0f-585">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-585">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="4bc0f-586">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-586">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="4bc0f-587">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-587">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="4bc0f-588">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-588">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="4bc0f-589">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-589">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="4bc0f-590">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-590">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="4bc0f-591">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-591">Test the PutTodoItem method</span></span>

<span data-ttu-id="4bc0f-592">本示例使用内存数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-592">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="4bc0f-593">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-593">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="4bc0f-594">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-594">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="4bc0f-595">更新 id = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-595">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="4bc0f-596">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-596">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="4bc0f-598">添加 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-598">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="4bc0f-599">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-599">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="4bc0f-600">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-600">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="4bc0f-601">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="4bc0f-601">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="4bc0f-602">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-602">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="4bc0f-603">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-603">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="4bc0f-604">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="4bc0f-604">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="4bc0f-605">选择“Send” </span><span class="sxs-lookup"><span data-stu-id="4bc0f-605">Select **Send**</span></span>

<span data-ttu-id="4bc0f-606">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-606">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="4bc0f-607">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-607">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="4bc0f-608">使用 jQuery 调用 API</span><span class="sxs-lookup"><span data-stu-id="4bc0f-608">Call the API with jQuery</span></span>

<span data-ttu-id="4bc0f-609">在本部分中，添加了使用 jQuery 调用 Web API 的 HTML 页面。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-609">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="4bc0f-610">jQuery 启动请求，并用 API 响应中的详细信息更新页面。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-610">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="4bc0f-611">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="4bc0f-612">在项目目录中创建 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="4bc0f-613">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="4bc0f-614">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="4bc0f-615">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="4bc0f-616">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="4bc0f-617">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="4bc0f-618">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="4bc0f-619">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="4bc0f-620">有多种方式可以获取 jQuery。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-620">There are several ways to get jQuery.</span></span> <span data-ttu-id="4bc0f-621">在前面的代码片段中，库是从 CDN 中加载的。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-621">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="4bc0f-622">此示例调用 API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-622">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="4bc0f-623">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="4bc0f-624">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="4bc0f-624">Get a list of to-do items</span></span>

<span data-ttu-id="4bc0f-625">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 函数将 `GET` 请求发送至 API，这将返回表示待办事项数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-625">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="4bc0f-626">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="4bc0f-627">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="4bc0f-628">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-628">Add a to-do item</span></span>

<span data-ttu-id="4bc0f-629">[Ajax](https://api.jquery.com/jquery.ajax/) 函数发送 `POST`，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-629">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="4bc0f-630">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="4bc0f-631">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="4bc0f-632">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="4bc0f-633">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-633">Update a to-do item</span></span>

<span data-ttu-id="4bc0f-634">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="4bc0f-635">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="4bc0f-636">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="4bc0f-636">Delete a to-do item</span></span>

<span data-ttu-id="4bc0f-637">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4bc0f-638">其他资源</span><span class="sxs-lookup"><span data-stu-id="4bc0f-638">Additional resources</span></span>

<span data-ttu-id="4bc0f-639">[查看或下载本教程的示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="4bc0f-640">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="4bc0f-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="4bc0f-641">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="4bc0f-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="4bc0f-642">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="4bc0f-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
