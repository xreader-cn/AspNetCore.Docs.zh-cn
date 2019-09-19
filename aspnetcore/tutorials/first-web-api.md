---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 1cc4fffc50978a3a958a96e1eb250cb85a8d2879
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082069"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="499ee-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="499ee-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="499ee-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="499ee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="499ee-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="499ee-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="499ee-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="499ee-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="499ee-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-107">Create a web API project.</span></span>
> * <span data-ttu-id="499ee-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="499ee-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="499ee-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="499ee-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="499ee-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="499ee-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="499ee-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-111">Call the web API with Postman.</span></span>

<span data-ttu-id="499ee-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="499ee-113">概述</span><span class="sxs-lookup"><span data-stu-id="499ee-113">Overview</span></span>

<span data-ttu-id="499ee-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="499ee-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="499ee-115">API</span><span class="sxs-lookup"><span data-stu-id="499ee-115">API</span></span> | <span data-ttu-id="499ee-116">说明</span><span class="sxs-lookup"><span data-stu-id="499ee-116">Description</span></span> | <span data-ttu-id="499ee-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="499ee-117">Request body</span></span> | <span data-ttu-id="499ee-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="499ee-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="499ee-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="499ee-119">GET /api/TodoItems</span></span> | <span data-ttu-id="499ee-120">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-120">Get all to-do items</span></span> | <span data-ttu-id="499ee-121">无</span><span class="sxs-lookup"><span data-stu-id="499ee-121">None</span></span> | <span data-ttu-id="499ee-122">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="499ee-122">Array of to-do items</span></span>|
|<span data-ttu-id="499ee-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="499ee-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="499ee-124">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="499ee-124">Get an item by ID</span></span> | <span data-ttu-id="499ee-125">无</span><span class="sxs-lookup"><span data-stu-id="499ee-125">None</span></span> | <span data-ttu-id="499ee-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-126">To-do item</span></span>|
|<span data-ttu-id="499ee-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="499ee-127">POST /api/TodoItems</span></span> | <span data-ttu-id="499ee-128">添加新项</span><span class="sxs-lookup"><span data-stu-id="499ee-128">Add a new item</span></span> | <span data-ttu-id="499ee-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-129">To-do item</span></span> | <span data-ttu-id="499ee-130">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-130">To-do item</span></span> |
|<span data-ttu-id="499ee-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="499ee-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="499ee-132">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="499ee-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="499ee-133">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-133">To-do item</span></span> | <span data-ttu-id="499ee-134">无</span><span class="sxs-lookup"><span data-stu-id="499ee-134">None</span></span> |
|<span data-ttu-id="499ee-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="499ee-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="499ee-136">删除项&nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="499ee-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="499ee-137">无</span><span class="sxs-lookup"><span data-stu-id="499ee-137">None</span></span> | <span data-ttu-id="499ee-138">无</span><span class="sxs-lookup"><span data-stu-id="499ee-138">None</span></span>|

<span data-ttu-id="499ee-139">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="499ee-139">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="499ee-145">系统必备</span><span class="sxs-lookup"><span data-stu-id="499ee-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="499ee-149">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="499ee-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-151">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="499ee-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="499ee-152">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="499ee-153">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="499ee-154">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="499ee-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="499ee-155">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="499ee-156">请不要选择“启用 Docker 支持”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-156">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="499ee-159">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="499ee-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="499ee-160">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="499ee-161">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="499ee-161">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="499ee-162">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="499ee-163">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="499ee-163">The preceding commands:</span></span>

  * <span data-ttu-id="499ee-164">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="499ee-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="499ee-165">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="499ee-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="499ee-167">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="499ee-167">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="499ee-169">选择“.NET Core”>“应用”>“API”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="499ee-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="499ee-171">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架选择为\*“.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="499ee-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="499ee-172">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="499ee-174">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="499ee-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="499ee-175">测试 API</span><span class="sxs-lookup"><span data-stu-id="499ee-175">Test the API</span></span>

<span data-ttu-id="499ee-176">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="499ee-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="499ee-177">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="499ee-179">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="499ee-180">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="499ee-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="499ee-181">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="499ee-182">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="499ee-184">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="499ee-185">在浏览器中，转到以下 URL：[https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="499ee-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-186">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="499ee-187">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="499ee-188">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="499ee-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="499ee-189">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="499ee-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="499ee-190">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="499ee-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="499ee-191">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="499ee-191">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="499ee-192">添加模型类</span><span class="sxs-lookup"><span data-stu-id="499ee-192">Add a model class</span></span>

<span data-ttu-id="499ee-193">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="499ee-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="499ee-194">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="499ee-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-196">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="499ee-197">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="499ee-198">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="499ee-199">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="499ee-200">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="499ee-201">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="499ee-203">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="499ee-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="499ee-204">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="499ee-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-205">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="499ee-206">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-206">Right-click the project.</span></span> <span data-ttu-id="499ee-207">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="499ee-208">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-208">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="499ee-210">右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。</span><span class="sxs-lookup"><span data-stu-id="499ee-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="499ee-211">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="499ee-212">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="499ee-213">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="499ee-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="499ee-214">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="499ee-215">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="499ee-215">Add a database context</span></span>

<span data-ttu-id="499ee-216">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="499ee-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="499ee-217">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="499ee-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="499ee-219">添加 Add Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="499ee-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="499ee-220">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="499ee-221">选中“包括预发行版”复选框  。</span><span class="sxs-lookup"><span data-stu-id="499ee-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="499ee-222">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer   。</span><span class="sxs-lookup"><span data-stu-id="499ee-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="499ee-223">在左窗口中选择“Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="499ee-224">选中右窗格中的“项目”复选框，然后选择“安装”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="499ee-225">使用上述说明添加 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="499ee-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="499ee-227">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="499ee-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="499ee-228">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="499ee-229">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="499ee-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="499ee-231">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="499ee-232">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="499ee-233">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="499ee-233">Register the database context</span></span>

<span data-ttu-id="499ee-234">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="499ee-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="499ee-235">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="499ee-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="499ee-236">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="499ee-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="499ee-237">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-237">The preceding code:</span></span>

* <span data-ttu-id="499ee-238">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="499ee-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="499ee-239">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="499ee-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="499ee-240">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="499ee-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="499ee-241">构建控制器</span><span class="sxs-lookup"><span data-stu-id="499ee-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-243">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="499ee-244">选择“添加”>“新建构建项”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="499ee-245">选择“其操作使用实体框架的 API 控制器”，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="499ee-246">在“添加其操作使用实体框架的 API 控制器”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="499ee-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="499ee-247">在“模型类”中选择“TodoItem (TodoAPI.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="499ee-248">在“数据上下文类”中选择“TodoContext (TodoAPI.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="499ee-249">选择“添加” </span><span class="sxs-lookup"><span data-stu-id="499ee-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="499ee-250">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="499ee-251">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="499ee-251">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="499ee-252">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="499ee-252">The preceding commands:</span></span>

* <span data-ttu-id="499ee-253">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="499ee-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="499ee-254">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="499ee-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="499ee-255">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="499ee-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="499ee-256">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-256">The generated code:</span></span>

* <span data-ttu-id="499ee-257">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="499ee-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="499ee-258">使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。</span><span class="sxs-lookup"><span data-stu-id="499ee-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="499ee-259">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="499ee-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="499ee-260">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="499ee-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="499ee-261">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="499ee-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="499ee-262">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="499ee-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="499ee-263">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="499ee-264">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="499ee-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="499ee-265">正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="499ee-266">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="499ee-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="499ee-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="499ee-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="499ee-268">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="499ee-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="499ee-269">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="499ee-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="499ee-270">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="499ee-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="499ee-271">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="499ee-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="499ee-272">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="499ee-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="499ee-273">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="499ee-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="499ee-274">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="499ee-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="499ee-275">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="499ee-275">Install Postman</span></span>

<span data-ttu-id="499ee-276">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="499ee-277">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="499ee-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="499ee-278">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-278">Start the web app.</span></span>
* <span data-ttu-id="499ee-279">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="499ee-279">Start Postman.</span></span>
* <span data-ttu-id="499ee-280">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="499ee-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="499ee-281">在“文件”>“设置”  （“常规”\*  选项卡）中，禁用“SSL 证书验证”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="499ee-282">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="499ee-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="499ee-283">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="499ee-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="499ee-284">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="499ee-284">Create a new request.</span></span>
* <span data-ttu-id="499ee-285">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="499ee-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="499ee-286">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="499ee-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="499ee-287">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="499ee-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="499ee-288">将类型设置为 JSON (application/json) </span><span class="sxs-lookup"><span data-stu-id="499ee-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="499ee-289">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="499ee-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="499ee-290">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-290">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="499ee-292">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="499ee-292">Test the location header URI</span></span>

* <span data-ttu-id="499ee-293">在“响应”  窗格中选择“标头”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="499ee-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="499ee-294">复制“位置”  标头值：</span><span class="sxs-lookup"><span data-stu-id="499ee-294">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="499ee-296">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="499ee-296">Set the method to GET.</span></span>
* <span data-ttu-id="499ee-297">粘贴 URI（例如，`https://localhost:5001/api/TodoItems/1`）</span><span class="sxs-lookup"><span data-stu-id="499ee-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="499ee-298">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="499ee-299">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-299">Examine the GET methods</span></span>

<span data-ttu-id="499ee-300">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="499ee-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="499ee-301">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="499ee-302">例如:</span><span class="sxs-lookup"><span data-stu-id="499ee-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="499ee-303">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="499ee-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="499ee-304">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="499ee-304">Test Get with Postman</span></span>

* <span data-ttu-id="499ee-305">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="499ee-305">Create a new request.</span></span>
* <span data-ttu-id="499ee-306">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="499ee-307">将请求 URL 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="499ee-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="499ee-308">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="499ee-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="499ee-309">在 Postman 中设置“两窗格视图”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="499ee-310">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-310">Select **Send**.</span></span>

<span data-ttu-id="499ee-311">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="499ee-311">This app uses an in-memory database.</span></span> <span data-ttu-id="499ee-312">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="499ee-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="499ee-313">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="499ee-314">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="499ee-314">Routing and URL paths</span></span>

<span data-ttu-id="499ee-315">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="499ee-316">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="499ee-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="499ee-317">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="499ee-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="499ee-318">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="499ee-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="499ee-319">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="499ee-320">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="499ee-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="499ee-321">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="499ee-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="499ee-322">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="499ee-322">This sample doesn't use a template.</span></span> <span data-ttu-id="499ee-323">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="499ee-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="499ee-324">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="499ee-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="499ee-325">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="499ee-326">返回值</span><span class="sxs-lookup"><span data-stu-id="499ee-326">Return values</span></span>

<span data-ttu-id="499ee-327">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="499ee-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="499ee-328">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="499ee-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="499ee-329">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="499ee-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="499ee-330">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="499ee-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="499ee-331">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="499ee-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="499ee-332">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="499ee-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="499ee-333">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="499ee-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="499ee-334">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="499ee-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="499ee-335">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="499ee-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="499ee-336">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-336">The PutTodoItem method</span></span>

<span data-ttu-id="499ee-337">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="499ee-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="499ee-338">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="499ee-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="499ee-339">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="499ee-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="499ee-340">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="499ee-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="499ee-341">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="499ee-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="499ee-342">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="499ee-343">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="499ee-344">本示例使用内存数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="499ee-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="499ee-345">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="499ee-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="499ee-346">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="499ee-347">更新 ID = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="499ee-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="499ee-348">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="499ee-348">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="499ee-350">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="499ee-351">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="499ee-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="499ee-352">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="499ee-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="499ee-353">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="499ee-354">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="499ee-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="499ee-355">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="499ee-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="499ee-356">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="499ee-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="499ee-357">选择“Send” </span><span class="sxs-lookup"><span data-stu-id="499ee-357">Select **Send**</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="499ee-358">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="499ee-358">Call the web API with JavaScript</span></span>

<span data-ttu-id="499ee-359">有关分步说明，请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="499ee-359">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="499ee-360">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="499ee-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="499ee-361">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-361">Create a web API project.</span></span>
> * <span data-ttu-id="499ee-362">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="499ee-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="499ee-363">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="499ee-363">Add a controller.</span></span>
> * <span data-ttu-id="499ee-364">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="499ee-365">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="499ee-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="499ee-366">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="499ee-366">Specify return values.</span></span>
> * <span data-ttu-id="499ee-367">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="499ee-368">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-368">Call the web API with JavaScript.</span></span>

<span data-ttu-id="499ee-369">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="499ee-370">概述</span><span class="sxs-lookup"><span data-stu-id="499ee-370">Overview</span></span>

<span data-ttu-id="499ee-371">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="499ee-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="499ee-372">API</span><span class="sxs-lookup"><span data-stu-id="499ee-372">API</span></span> | <span data-ttu-id="499ee-373">说明</span><span class="sxs-lookup"><span data-stu-id="499ee-373">Description</span></span> | <span data-ttu-id="499ee-374">请求正文</span><span class="sxs-lookup"><span data-stu-id="499ee-374">Request body</span></span> | <span data-ttu-id="499ee-375">响应正文</span><span class="sxs-lookup"><span data-stu-id="499ee-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="499ee-376">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="499ee-376">GET /api/TodoItems</span></span> | <span data-ttu-id="499ee-377">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-377">Get all to-do items</span></span> | <span data-ttu-id="499ee-378">无</span><span class="sxs-lookup"><span data-stu-id="499ee-378">None</span></span> | <span data-ttu-id="499ee-379">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="499ee-379">Array of to-do items</span></span>|
|<span data-ttu-id="499ee-380">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="499ee-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="499ee-381">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="499ee-381">Get an item by ID</span></span> | <span data-ttu-id="499ee-382">无</span><span class="sxs-lookup"><span data-stu-id="499ee-382">None</span></span> | <span data-ttu-id="499ee-383">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-383">To-do item</span></span>|
|<span data-ttu-id="499ee-384">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="499ee-384">POST /api/TodoItems</span></span> | <span data-ttu-id="499ee-385">添加新项</span><span class="sxs-lookup"><span data-stu-id="499ee-385">Add a new item</span></span> | <span data-ttu-id="499ee-386">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-386">To-do item</span></span> | <span data-ttu-id="499ee-387">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-387">To-do item</span></span> |
|<span data-ttu-id="499ee-388">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="499ee-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="499ee-389">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="499ee-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="499ee-390">待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-390">To-do item</span></span> | <span data-ttu-id="499ee-391">无</span><span class="sxs-lookup"><span data-stu-id="499ee-391">None</span></span> |
|<span data-ttu-id="499ee-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="499ee-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="499ee-393">删除项&nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="499ee-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="499ee-394">无</span><span class="sxs-lookup"><span data-stu-id="499ee-394">None</span></span> | <span data-ttu-id="499ee-395">无</span><span class="sxs-lookup"><span data-stu-id="499ee-395">None</span></span>|

<span data-ttu-id="499ee-396">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="499ee-396">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="499ee-402">系统必备</span><span class="sxs-lookup"><span data-stu-id="499ee-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-405">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="499ee-406">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="499ee-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-408">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="499ee-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="499ee-409">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="499ee-410">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="499ee-411">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”    。</span><span class="sxs-lookup"><span data-stu-id="499ee-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="499ee-412">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="499ee-413">请不要选择“启用 Docker 支持”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-413">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="499ee-416">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="499ee-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="499ee-417">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="499ee-418">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="499ee-418">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="499ee-419">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="499ee-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="499ee-420">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-421">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="499ee-422">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="499ee-422">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="499ee-424">选择“.NET Core”>“应用”>“API”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="499ee-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="499ee-426">在“配置新的 ASP.NET Core Web API”  对话框中，接受默认的 \*.NET Core 2.2  “目标框架”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="499ee-427">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="499ee-429">测试 API</span><span class="sxs-lookup"><span data-stu-id="499ee-429">Test the API</span></span>

<span data-ttu-id="499ee-430">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="499ee-430">The project template creates a `values` API.</span></span> <span data-ttu-id="499ee-431">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="499ee-433">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="499ee-434">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="499ee-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="499ee-435">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="499ee-436">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="499ee-438">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="499ee-439">在浏览器中，转到以下 URL：[https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="499ee-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="499ee-441">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="499ee-442">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="499ee-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="499ee-443">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="499ee-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="499ee-444">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="499ee-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="499ee-445">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="499ee-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="499ee-446">添加模型类</span><span class="sxs-lookup"><span data-stu-id="499ee-446">Add a model class</span></span>

<span data-ttu-id="499ee-447">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="499ee-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="499ee-448">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="499ee-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-450">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="499ee-451">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="499ee-452">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="499ee-453">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="499ee-454">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="499ee-455">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="499ee-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="499ee-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="499ee-457">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="499ee-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="499ee-458">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="499ee-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="499ee-459">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="499ee-460">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-460">Right-click the project.</span></span> <span data-ttu-id="499ee-461">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="499ee-462">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-462">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="499ee-464">右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。</span><span class="sxs-lookup"><span data-stu-id="499ee-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="499ee-465">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="499ee-466">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="499ee-467">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="499ee-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="499ee-468">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="499ee-469">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="499ee-469">Add a database context</span></span>

<span data-ttu-id="499ee-470">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="499ee-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="499ee-471">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="499ee-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-473">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="499ee-474">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="499ee-475">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="499ee-476">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="499ee-477">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="499ee-478">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="499ee-478">Register the database context</span></span>

<span data-ttu-id="499ee-479">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="499ee-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="499ee-480">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="499ee-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="499ee-481">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="499ee-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="499ee-482">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-482">The preceding code:</span></span>

* <span data-ttu-id="499ee-483">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="499ee-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="499ee-484">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="499ee-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="499ee-485">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="499ee-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="499ee-486">添加控制器</span><span class="sxs-lookup"><span data-stu-id="499ee-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-488">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="499ee-489">选择“添加”>“新项”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="499ee-490">在“添加新项”对话框中，选择“API 控制器类”模板   。</span><span class="sxs-lookup"><span data-stu-id="499ee-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="499ee-491">将类命名为 TodoController，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="499ee-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="499ee-493">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="499ee-494">在“控制器”文件夹中，创建名为 `TodoController` 的类  。</span><span class="sxs-lookup"><span data-stu-id="499ee-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="499ee-495">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="499ee-496">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="499ee-496">The preceding code:</span></span>

* <span data-ttu-id="499ee-497">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="499ee-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="499ee-498">使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。</span><span class="sxs-lookup"><span data-stu-id="499ee-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="499ee-499">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="499ee-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="499ee-500">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="499ee-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="499ee-501">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="499ee-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="499ee-502">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="499ee-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="499ee-503">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="499ee-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="499ee-504">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="499ee-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="499ee-505">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="499ee-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="499ee-506">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="499ee-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="499ee-507">添加 Get 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-507">Add Get methods</span></span>

<span data-ttu-id="499ee-508">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="499ee-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="499ee-509">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="499ee-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="499ee-510">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="499ee-510">Stop the app if it's still running.</span></span> <span data-ttu-id="499ee-511">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="499ee-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="499ee-512">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="499ee-513">例如:</span><span class="sxs-lookup"><span data-stu-id="499ee-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="499ee-514">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="499ee-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="499ee-515">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="499ee-515">Routing and URL paths</span></span>

<span data-ttu-id="499ee-516">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="499ee-517">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="499ee-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="499ee-518">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="499ee-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="499ee-519">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="499ee-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="499ee-520">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="499ee-521">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="499ee-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="499ee-522">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="499ee-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="499ee-523">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="499ee-523">This sample doesn't use a template.</span></span> <span data-ttu-id="499ee-524">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="499ee-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="499ee-525">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="499ee-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="499ee-526">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="499ee-527">返回值</span><span class="sxs-lookup"><span data-stu-id="499ee-527">Return values</span></span>

<span data-ttu-id="499ee-528">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="499ee-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="499ee-529">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="499ee-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="499ee-530">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="499ee-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="499ee-531">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="499ee-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="499ee-532">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="499ee-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="499ee-533">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="499ee-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="499ee-534">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="499ee-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="499ee-535">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="499ee-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="499ee-536">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="499ee-536">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="499ee-537">测试 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="499ee-538">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="499ee-539">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="499ee-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="499ee-540">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="499ee-540">Start the web app.</span></span>
* <span data-ttu-id="499ee-541">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="499ee-541">Start Postman.</span></span>
* <span data-ttu-id="499ee-542">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="499ee-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="499ee-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="499ee-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="499ee-544">在“文件”>“设置”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="499ee-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="499ee-545">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="499ee-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="499ee-546">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="499ee-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="499ee-547">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="499ee-548">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="499ee-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="499ee-549">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="499ee-549">Create a new request.</span></span>
  * <span data-ttu-id="499ee-550">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="499ee-551">将请求 URL 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="499ee-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="499ee-552">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="499ee-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="499ee-553">在 Postman 中设置“两窗格视图”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="499ee-554">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-554">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="499ee-556">添加创建方法</span><span class="sxs-lookup"><span data-stu-id="499ee-556">Add a Create method</span></span>

<span data-ttu-id="499ee-557">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="499ee-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="499ee-558">正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="499ee-559">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="499ee-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="499ee-560">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="499ee-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="499ee-561">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="499ee-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="499ee-562">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="499ee-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="499ee-563">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="499ee-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="499ee-564">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="499ee-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="499ee-565">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="499ee-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="499ee-566">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="499ee-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="499ee-567">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="499ee-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="499ee-568">测试 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="499ee-569">生成项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-569">Build the project.</span></span>
* <span data-ttu-id="499ee-570">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="499ee-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="499ee-571">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="499ee-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="499ee-572">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="499ee-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="499ee-573">将类型设置为 JSON (application/json) </span><span class="sxs-lookup"><span data-stu-id="499ee-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="499ee-574">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="499ee-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="499ee-575">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-575">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="499ee-577">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="499ee-578">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="499ee-578">Test the location header URI</span></span>

* <span data-ttu-id="499ee-579">在“响应”  窗格中选择“标头”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="499ee-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="499ee-580">复制“位置”  标头值：</span><span class="sxs-lookup"><span data-stu-id="499ee-580">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="499ee-582">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="499ee-582">Set the method to GET.</span></span>
* <span data-ttu-id="499ee-583">粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）</span><span class="sxs-lookup"><span data-stu-id="499ee-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="499ee-584">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="499ee-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="499ee-585">添加 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="499ee-586">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="499ee-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="499ee-587">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="499ee-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="499ee-588">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="499ee-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="499ee-589">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="499ee-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="499ee-590">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="499ee-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="499ee-591">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="499ee-592">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="499ee-593">本示例使用内存数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="499ee-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="499ee-594">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="499ee-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="499ee-595">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="499ee-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="499ee-596">更新 id = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="499ee-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="499ee-597">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="499ee-597">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="499ee-599">添加 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="499ee-600">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="499ee-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="499ee-601">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="499ee-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="499ee-602">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="499ee-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="499ee-603">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="499ee-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="499ee-604">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="499ee-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="499ee-605">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="499ee-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="499ee-606">选择“Send” </span><span class="sxs-lookup"><span data-stu-id="499ee-606">Select **Send**</span></span>

<span data-ttu-id="499ee-607">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="499ee-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="499ee-608">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="499ee-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="499ee-609">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="499ee-609">Call the web API with JavaScript</span></span>

<span data-ttu-id="499ee-610">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="499ee-610">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="499ee-611">Fetch API 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="499ee-611">The Fetch API initiates the request.</span></span> <span data-ttu-id="499ee-612">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="499ee-612">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="499ee-613">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：</span><span class="sxs-lookup"><span data-stu-id="499ee-613">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="499ee-614">在项目目录中创建 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="499ee-614">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="499ee-615">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="499ee-615">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="499ee-616">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="499ee-616">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="499ee-617">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="499ee-617">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="499ee-618">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="499ee-618">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="499ee-619">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="499ee-619">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="499ee-620">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="499ee-620">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="499ee-621">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="499ee-621">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="499ee-622">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="499ee-622">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="499ee-623">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="499ee-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="499ee-624">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="499ee-624">Get a list of to-do items</span></span>

<span data-ttu-id="499ee-625">Fetch 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="499ee-625">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="499ee-626">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="499ee-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="499ee-627">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="499ee-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="499ee-628">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-628">Add a to-do item</span></span>

<span data-ttu-id="499ee-629">Fetch 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="499ee-629">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="499ee-630">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="499ee-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="499ee-631">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="499ee-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="499ee-632">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="499ee-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="499ee-633">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-633">Update a to-do item</span></span>

<span data-ttu-id="499ee-634">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="499ee-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="499ee-635">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="499ee-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="499ee-636">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="499ee-636">Delete a to-do item</span></span>

<span data-ttu-id="499ee-637">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="499ee-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="499ee-638">其他资源</span><span class="sxs-lookup"><span data-stu-id="499ee-638">Additional resources</span></span>

<span data-ttu-id="499ee-639">[查看或下载本教程的示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="499ee-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="499ee-640">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="499ee-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="499ee-641">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="499ee-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="499ee-642">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="499ee-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
