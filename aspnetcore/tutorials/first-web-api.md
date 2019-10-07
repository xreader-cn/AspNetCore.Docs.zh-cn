---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 7bb98fe5befa8eea80885d246da31ad87d5cfc2d
ms.sourcegitcommit: fe88748b762525cb490f7e39089a4760f6a73a24
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71691212"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="0eb34-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="0eb34-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="0eb34-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="0eb34-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="0eb34-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="0eb34-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0eb34-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="0eb34-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0eb34-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-107">Create a web API project.</span></span>
> * <span data-ttu-id="0eb34-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="0eb34-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0eb34-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="0eb34-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="0eb34-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="0eb34-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="0eb34-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-111">Call the web API with Postman.</span></span>

<span data-ttu-id="0eb34-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="0eb34-113">概述</span><span class="sxs-lookup"><span data-stu-id="0eb34-113">Overview</span></span>

<span data-ttu-id="0eb34-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="0eb34-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0eb34-115">API</span><span class="sxs-lookup"><span data-stu-id="0eb34-115">API</span></span> | <span data-ttu-id="0eb34-116">说明</span><span class="sxs-lookup"><span data-stu-id="0eb34-116">Description</span></span> | <span data-ttu-id="0eb34-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="0eb34-117">Request body</span></span> | <span data-ttu-id="0eb34-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="0eb34-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="0eb34-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0eb34-119">GET /api/TodoItems</span></span> | <span data-ttu-id="0eb34-120">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-120">Get all to-do items</span></span> | <span data-ttu-id="0eb34-121">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-121">None</span></span> | <span data-ttu-id="0eb34-122">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="0eb34-122">Array of to-do items</span></span>|
|<span data-ttu-id="0eb34-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0eb34-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="0eb34-124">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="0eb34-124">Get an item by ID</span></span> | <span data-ttu-id="0eb34-125">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-125">None</span></span> | <span data-ttu-id="0eb34-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-126">To-do item</span></span>|
|<span data-ttu-id="0eb34-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0eb34-127">POST /api/TodoItems</span></span> | <span data-ttu-id="0eb34-128">添加新项</span><span class="sxs-lookup"><span data-stu-id="0eb34-128">Add a new item</span></span> | <span data-ttu-id="0eb34-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-129">To-do item</span></span> | <span data-ttu-id="0eb34-130">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-130">To-do item</span></span> |
|<span data-ttu-id="0eb34-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0eb34-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="0eb34-132">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0eb34-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0eb34-133">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-133">To-do item</span></span> | <span data-ttu-id="0eb34-134">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-134">None</span></span> |
|<span data-ttu-id="0eb34-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0eb34-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="0eb34-136">删除项&nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0eb34-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0eb34-137">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-137">None</span></span> | <span data-ttu-id="0eb34-138">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-138">None</span></span>|

<span data-ttu-id="0eb34-139">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="0eb34-139">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0eb34-145">系统必备</span><span class="sxs-lookup"><span data-stu-id="0eb34-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0eb34-149">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="0eb34-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-151">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="0eb34-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0eb34-152">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0eb34-153">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0eb34-154">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="0eb34-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="0eb34-155">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-155">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eb34-158">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0eb34-159">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0eb34-160">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eb34-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="0eb34-161">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="0eb34-162">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="0eb34-162">The preceding commands:</span></span>

  * <span data-ttu-id="0eb34-163">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="0eb34-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="0eb34-164">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="0eb34-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0eb34-166">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-166">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0eb34-168">选择“.NET Core”>“应用”>“API”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="0eb34-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="0eb34-170">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架选择为\*“.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="0eb34-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="0eb34-171">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="0eb34-173">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eb34-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="0eb34-174">测试 API</span><span class="sxs-lookup"><span data-stu-id="0eb34-174">Test the API</span></span>

<span data-ttu-id="0eb34-175">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="0eb34-176">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eb34-178">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0eb34-179">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="0eb34-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0eb34-180">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0eb34-181">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eb34-183">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0eb34-184">在浏览器中，转到以下 URL：[https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0eb34-186">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0eb34-187">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="0eb34-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0eb34-188">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="0eb34-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0eb34-189">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="0eb34-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="0eb34-190">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="0eb34-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="0eb34-191">添加模型类</span><span class="sxs-lookup"><span data-stu-id="0eb34-191">Add a model class</span></span>

<span data-ttu-id="0eb34-192">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0eb34-193">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-195">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0eb34-196">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0eb34-197">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="0eb34-198">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0eb34-199">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0eb34-200">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eb34-202">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0eb34-203">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="0eb34-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0eb34-205">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-205">Right-click the project.</span></span> <span data-ttu-id="0eb34-206">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0eb34-207">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-207">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0eb34-209">右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。</span><span class="sxs-lookup"><span data-stu-id="0eb34-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0eb34-210">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0eb34-211">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="0eb34-212">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="0eb34-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0eb34-213">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0eb34-214">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="0eb34-214">Add a database context</span></span>

<span data-ttu-id="0eb34-215">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0eb34-216">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="0eb34-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="0eb34-218">添加 Add Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="0eb34-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="0eb34-219">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="0eb34-220">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="0eb34-221">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="0eb34-222">选中右窗格中的“项目”复选框，然后选择“安装”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="0eb34-223">使用上述说明添加 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="0eb34-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="0eb34-225">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="0eb34-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="0eb34-226">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0eb34-227">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0eb34-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0eb34-229">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0eb34-230">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0eb34-231">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="0eb34-231">Register the database context</span></span>

<span data-ttu-id="0eb34-232">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="0eb34-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0eb34-233">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="0eb34-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="0eb34-234">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="0eb34-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="0eb34-235">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-235">The preceding code:</span></span>

* <span data-ttu-id="0eb34-236">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="0eb34-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0eb34-237">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="0eb34-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0eb34-238">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="0eb34-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="0eb34-239">构建控制器</span><span class="sxs-lookup"><span data-stu-id="0eb34-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-241">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0eb34-242">选择“添加”>“新建构建项”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0eb34-243">选择“其操作使用实体框架的 API 控制器”，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="0eb34-244">在“添加其操作使用实体框架的 API 控制器”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="0eb34-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="0eb34-245">在“模型类”中选择“TodoItem (TodoApi.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="0eb34-246">在“数据上下文类”中选择“TodoContext (TodoAPI.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="0eb34-247">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0eb34-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0eb34-249">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eb34-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="0eb34-250">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="0eb34-250">The preceding commands:</span></span>

* <span data-ttu-id="0eb34-251">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="0eb34-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="0eb34-252">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="0eb34-253">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="0eb34-254">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-254">The generated code:</span></span>

* <span data-ttu-id="0eb34-255">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="0eb34-256">使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="0eb34-257">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="0eb34-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0eb34-258">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="0eb34-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0eb34-259">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="0eb34-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0eb34-260">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="0eb34-261">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="0eb34-262">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="0eb34-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="0eb34-263">正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="0eb34-264">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="0eb34-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0eb34-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb34-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="0eb34-266">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="0eb34-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="0eb34-267">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="0eb34-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0eb34-268">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="0eb34-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="0eb34-269">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="0eb34-270">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0eb34-271">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="0eb34-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0eb34-272">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="0eb34-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="0eb34-273">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="0eb34-273">Install Postman</span></span>

<span data-ttu-id="0eb34-274">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0eb34-275">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="0eb34-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="0eb34-276">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-276">Start the web app.</span></span>
* <span data-ttu-id="0eb34-277">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="0eb34-277">Start Postman.</span></span>
* <span data-ttu-id="0eb34-278">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="0eb34-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="0eb34-279">在“文件”>“设置”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="0eb34-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="0eb34-280">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="0eb34-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="0eb34-281">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="0eb34-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="0eb34-282">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="0eb34-282">Create a new request.</span></span>
* <span data-ttu-id="0eb34-283">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0eb34-284">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="0eb34-285">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0eb34-286">将类型设置为 JSON (application/json) </span><span class="sxs-lookup"><span data-stu-id="0eb34-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0eb34-287">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="0eb34-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0eb34-288">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-288">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0eb34-290">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="0eb34-290">Test the location header URI</span></span>

* <span data-ttu-id="0eb34-291">在“响应”  窗格中选择“标头”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="0eb34-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0eb34-292">复制“位置”  标头值：</span><span class="sxs-lookup"><span data-stu-id="0eb34-292">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="0eb34-294">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="0eb34-294">Set the method to GET.</span></span>
* <span data-ttu-id="0eb34-295">粘贴 URI（例如，`https://localhost:5001/api/TodoItems/1`）。</span><span class="sxs-lookup"><span data-stu-id="0eb34-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0eb34-296">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="0eb34-297">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-297">Examine the GET methods</span></span>

<span data-ttu-id="0eb34-298">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="0eb34-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="0eb34-299">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="0eb34-300">例如:</span><span class="sxs-lookup"><span data-stu-id="0eb34-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="0eb34-301">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="0eb34-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="0eb34-302">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="0eb34-302">Test Get with Postman</span></span>

* <span data-ttu-id="0eb34-303">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="0eb34-303">Create a new request.</span></span>
* <span data-ttu-id="0eb34-304">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="0eb34-305">将请求 URL 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="0eb34-306">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="0eb34-307">在 Postman 中设置“两窗格视图”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0eb34-308">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-308">Select **Send**.</span></span>

<span data-ttu-id="0eb34-309">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="0eb34-309">This app uses an in-memory database.</span></span> <span data-ttu-id="0eb34-310">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="0eb34-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="0eb34-311">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="0eb34-312">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="0eb34-312">Routing and URL paths</span></span>

<span data-ttu-id="0eb34-313">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0eb34-314">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="0eb34-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0eb34-315">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="0eb34-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="0eb34-316">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="0eb34-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0eb34-317">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="0eb34-318">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="0eb34-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0eb34-319">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="0eb34-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0eb34-320">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="0eb34-320">This sample doesn't use a template.</span></span> <span data-ttu-id="0eb34-321">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0eb34-322">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="0eb34-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0eb34-323">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0eb34-324">返回值</span><span class="sxs-lookup"><span data-stu-id="0eb34-324">Return values</span></span>

<span data-ttu-id="0eb34-325">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0eb34-326">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="0eb34-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0eb34-327">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="0eb34-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0eb34-328">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="0eb34-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0eb34-329">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="0eb34-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0eb34-330">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="0eb34-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0eb34-331">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="0eb34-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="0eb34-332">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="0eb34-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0eb34-333">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="0eb34-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="0eb34-334">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-334">The PutTodoItem method</span></span>

<span data-ttu-id="0eb34-335">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb34-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="0eb34-336">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="0eb34-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0eb34-337">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0eb34-338">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="0eb34-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0eb34-339">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0eb34-340">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0eb34-341">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="0eb34-342">本示例使用内存数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="0eb34-342">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="0eb34-343">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="0eb34-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0eb34-344">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0eb34-345">更新 ID = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="0eb34-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0eb34-346">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="0eb34-346">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="0eb34-348">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="0eb34-349">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb34-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="0eb34-350">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0eb34-351">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0eb34-352">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="0eb34-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0eb34-353">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0eb34-354">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-354">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="0eb34-355">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-355">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0eb34-356">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="0eb34-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="0eb34-357">有关分步说明，请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0eb34-358">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="0eb34-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0eb34-359">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-359">Create a web API project.</span></span>
> * <span data-ttu-id="0eb34-360">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="0eb34-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="0eb34-361">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="0eb34-361">Add a controller.</span></span>
> * <span data-ttu-id="0eb34-362">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="0eb34-363">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="0eb34-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="0eb34-364">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="0eb34-364">Specify return values.</span></span>
> * <span data-ttu-id="0eb34-365">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="0eb34-366">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="0eb34-367">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="0eb34-368">概述</span><span class="sxs-lookup"><span data-stu-id="0eb34-368">Overview</span></span>

<span data-ttu-id="0eb34-369">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="0eb34-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="0eb34-370">API</span><span class="sxs-lookup"><span data-stu-id="0eb34-370">API</span></span> | <span data-ttu-id="0eb34-371">说明</span><span class="sxs-lookup"><span data-stu-id="0eb34-371">Description</span></span> | <span data-ttu-id="0eb34-372">请求正文</span><span class="sxs-lookup"><span data-stu-id="0eb34-372">Request body</span></span> | <span data-ttu-id="0eb34-373">响应正文</span><span class="sxs-lookup"><span data-stu-id="0eb34-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="0eb34-374">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0eb34-374">GET /api/TodoItems</span></span> | <span data-ttu-id="0eb34-375">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-375">Get all to-do items</span></span> | <span data-ttu-id="0eb34-376">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-376">None</span></span> | <span data-ttu-id="0eb34-377">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="0eb34-377">Array of to-do items</span></span>|
|<span data-ttu-id="0eb34-378">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0eb34-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="0eb34-379">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="0eb34-379">Get an item by ID</span></span> | <span data-ttu-id="0eb34-380">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-380">None</span></span> | <span data-ttu-id="0eb34-381">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-381">To-do item</span></span>|
|<span data-ttu-id="0eb34-382">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="0eb34-382">POST /api/TodoItems</span></span> | <span data-ttu-id="0eb34-383">添加新项</span><span class="sxs-lookup"><span data-stu-id="0eb34-383">Add a new item</span></span> | <span data-ttu-id="0eb34-384">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-384">To-do item</span></span> | <span data-ttu-id="0eb34-385">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-385">To-do item</span></span> |
|<span data-ttu-id="0eb34-386">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="0eb34-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="0eb34-387">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0eb34-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="0eb34-388">待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-388">To-do item</span></span> | <span data-ttu-id="0eb34-389">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-389">None</span></span> |
|<span data-ttu-id="0eb34-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0eb34-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="0eb34-391">删除项&nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="0eb34-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="0eb34-392">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-392">None</span></span> | <span data-ttu-id="0eb34-393">无</span><span class="sxs-lookup"><span data-stu-id="0eb34-393">None</span></span>|

<span data-ttu-id="0eb34-394">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="0eb34-394">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="0eb34-400">系统必备</span><span class="sxs-lookup"><span data-stu-id="0eb34-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-403">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="0eb34-404">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="0eb34-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-406">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="0eb34-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0eb34-407">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="0eb34-408">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="0eb34-409">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”    。</span><span class="sxs-lookup"><span data-stu-id="0eb34-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="0eb34-410">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="0eb34-411">请不要选择“启用 Docker 支持”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-411">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eb34-414">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="0eb34-415">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="0eb34-416">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0eb34-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="0eb34-417">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="0eb34-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="0eb34-418">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-419">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0eb34-420">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-420">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="0eb34-422">选择“.NET Core”>“应用”>“API”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="0eb34-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="0eb34-424">在“配置新的 ASP.NET Core Web API”  对话框中，接受默认的 \*.NET Core 2.2  “目标框架”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="0eb34-425">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="0eb34-427">测试 API</span><span class="sxs-lookup"><span data-stu-id="0eb34-427">Test the API</span></span>

<span data-ttu-id="0eb34-428">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-428">The project template creates a `values` API.</span></span> <span data-ttu-id="0eb34-429">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0eb34-431">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0eb34-432">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="0eb34-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="0eb34-433">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="0eb34-434">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0eb34-436">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="0eb34-437">在浏览器中，转到以下 URL：[https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0eb34-439">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="0eb34-440">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="0eb34-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="0eb34-441">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="0eb34-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="0eb34-442">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="0eb34-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="0eb34-443">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="0eb34-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="0eb34-444">添加模型类</span><span class="sxs-lookup"><span data-stu-id="0eb34-444">Add a model class</span></span>

<span data-ttu-id="0eb34-445">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="0eb34-446">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-448">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="0eb34-449">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0eb34-450">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="0eb34-451">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0eb34-452">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="0eb34-453">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0eb34-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0eb34-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0eb34-455">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="0eb34-456">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="0eb34-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0eb34-457">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0eb34-458">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-458">Right-click the project.</span></span> <span data-ttu-id="0eb34-459">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="0eb34-460">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-460">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="0eb34-462">右键单击“Models”文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”      。</span><span class="sxs-lookup"><span data-stu-id="0eb34-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="0eb34-463">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="0eb34-464">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="0eb34-465">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="0eb34-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="0eb34-466">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="0eb34-467">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="0eb34-467">Add a database context</span></span>

<span data-ttu-id="0eb34-468">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="0eb34-469">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="0eb34-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-471">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="0eb34-472">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0eb34-473">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0eb34-474">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="0eb34-475">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="0eb34-476">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="0eb34-476">Register the database context</span></span>

<span data-ttu-id="0eb34-477">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="0eb34-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="0eb34-478">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="0eb34-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="0eb34-479">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="0eb34-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="0eb34-480">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-480">The preceding code:</span></span>

* <span data-ttu-id="0eb34-481">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="0eb34-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="0eb34-482">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="0eb34-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="0eb34-483">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="0eb34-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="0eb34-484">添加控制器</span><span class="sxs-lookup"><span data-stu-id="0eb34-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-486">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="0eb34-487">选择“添加”>“新项”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="0eb34-488">在“添加新项”对话框中，选择“API 控制器类”模板   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="0eb34-489">将类命名为 TodoController，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0eb34-491">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0eb34-492">在“控制器”文件夹中，创建名为 `TodoController` 的类  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="0eb34-493">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="0eb34-494">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="0eb34-494">The preceding code:</span></span>

* <span data-ttu-id="0eb34-495">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="0eb34-496">使用 [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性修饰类。</span><span class="sxs-lookup"><span data-stu-id="0eb34-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="0eb34-497">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="0eb34-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="0eb34-498">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="0eb34-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="0eb34-499">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="0eb34-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="0eb34-500">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="0eb34-501">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="0eb34-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="0eb34-502">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="0eb34-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="0eb34-503">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="0eb34-504">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="0eb34-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="0eb34-505">添加 Get 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-505">Add Get methods</span></span>

<span data-ttu-id="0eb34-506">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="0eb34-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="0eb34-507">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="0eb34-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="0eb34-508">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="0eb34-508">Stop the app if it's still running.</span></span> <span data-ttu-id="0eb34-509">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="0eb34-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="0eb34-510">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="0eb34-511">例如:</span><span class="sxs-lookup"><span data-stu-id="0eb34-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="0eb34-512">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="0eb34-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="0eb34-513">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="0eb34-513">Routing and URL paths</span></span>

<span data-ttu-id="0eb34-514">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="0eb34-515">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="0eb34-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="0eb34-516">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="0eb34-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="0eb34-517">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="0eb34-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="0eb34-518">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="0eb34-519">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="0eb34-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="0eb34-520">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="0eb34-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="0eb34-521">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="0eb34-521">This sample doesn't use a template.</span></span> <span data-ttu-id="0eb34-522">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="0eb34-523">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="0eb34-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="0eb34-524">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="0eb34-525">返回值</span><span class="sxs-lookup"><span data-stu-id="0eb34-525">Return values</span></span>

<span data-ttu-id="0eb34-526">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="0eb34-527">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="0eb34-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="0eb34-528">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="0eb34-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="0eb34-529">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="0eb34-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="0eb34-530">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="0eb34-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="0eb34-531">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="0eb34-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="0eb34-532">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="0eb34-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="0eb34-533">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="0eb34-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="0eb34-534">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="0eb34-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="0eb34-535">测试 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="0eb34-536">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="0eb34-537">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-537">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="0eb34-538">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="0eb34-538">Start the web app.</span></span>
* <span data-ttu-id="0eb34-539">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="0eb34-539">Start Postman.</span></span>
* <span data-ttu-id="0eb34-540">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="0eb34-540">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0eb34-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0eb34-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0eb34-542">在“文件”>“设置”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="0eb34-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0eb34-543">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0eb34-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="0eb34-544">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="0eb34-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="0eb34-545">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="0eb34-546">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="0eb34-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="0eb34-547">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="0eb34-547">Create a new request.</span></span>
  * <span data-ttu-id="0eb34-548">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="0eb34-549">将请求 URL 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="0eb34-550">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="0eb34-551">在 Postman 中设置“两窗格视图”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="0eb34-552">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-552">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="0eb34-554">添加创建方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-554">Add a Create method</span></span>

<span data-ttu-id="0eb34-555">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="0eb34-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="0eb34-556">正如 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示，前面的代码是 HTTP POST 方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="0eb34-557">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="0eb34-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="0eb34-558">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb34-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="0eb34-559">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="0eb34-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="0eb34-560">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="0eb34-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="0eb34-561">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="0eb34-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="0eb34-562">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="0eb34-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="0eb34-563">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="0eb34-564">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="0eb34-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="0eb34-565">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="0eb34-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="0eb34-566">测试 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="0eb34-567">生成项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-567">Build the project.</span></span>
* <span data-ttu-id="0eb34-568">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="0eb34-569">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="0eb34-570">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="0eb34-571">将类型设置为 JSON (application/json) </span><span class="sxs-lookup"><span data-stu-id="0eb34-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="0eb34-572">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="0eb34-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="0eb34-573">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-573">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="0eb34-575">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="0eb34-576">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="0eb34-576">Test the location header URI</span></span>

* <span data-ttu-id="0eb34-577">在“响应”  窗格中选择“标头”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="0eb34-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="0eb34-578">复制“位置”  标头值：</span><span class="sxs-lookup"><span data-stu-id="0eb34-578">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="0eb34-580">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="0eb34-580">Set the method to GET.</span></span>
* <span data-ttu-id="0eb34-581">粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）。</span><span class="sxs-lookup"><span data-stu-id="0eb34-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="0eb34-582">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="0eb34-583">添加 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="0eb34-584">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb34-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="0eb34-585">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="0eb34-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="0eb34-586">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="0eb34-587">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="0eb34-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="0eb34-588">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="0eb34-589">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="0eb34-590">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="0eb34-591">本示例使用内存数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="0eb34-591">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="0eb34-592">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="0eb34-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="0eb34-593">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="0eb34-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="0eb34-594">更新 id = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="0eb34-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="0eb34-595">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="0eb34-595">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="0eb34-597">添加 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="0eb34-598">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb34-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="0eb34-599">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="0eb34-600">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="0eb34-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="0eb34-601">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="0eb34-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="0eb34-602">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="0eb34-603">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-603">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="0eb34-604">选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-604">Select **Send**.</span></span>

<span data-ttu-id="0eb34-605">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="0eb34-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="0eb34-606">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="0eb34-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="0eb34-607">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="0eb34-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="0eb34-608">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="0eb34-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="0eb34-609">Fetch API 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="0eb34-609">The Fetch API initiates the request.</span></span> <span data-ttu-id="0eb34-610">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="0eb34-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="0eb34-611">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：</span><span class="sxs-lookup"><span data-stu-id="0eb34-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="0eb34-612">在项目目录中创建 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0eb34-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="0eb34-613">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="0eb34-614">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="0eb34-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="0eb34-615">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="0eb34-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="0eb34-616">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="0eb34-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="0eb34-617">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="0eb34-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="0eb34-618">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="0eb34-619">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="0eb34-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="0eb34-620">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="0eb34-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="0eb34-621">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="0eb34-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="0eb34-622">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="0eb34-622">Get a list of to-do items</span></span>

<span data-ttu-id="0eb34-623">Fetch 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="0eb34-623">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="0eb34-624">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="0eb34-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="0eb34-625">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="0eb34-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="0eb34-626">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-626">Add a to-do item</span></span>

<span data-ttu-id="0eb34-627">Fetch 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="0eb34-627">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="0eb34-628">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="0eb34-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="0eb34-629">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="0eb34-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="0eb34-630">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="0eb34-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="0eb34-631">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-631">Update a to-do item</span></span>

<span data-ttu-id="0eb34-632">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="0eb34-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="0eb34-633">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="0eb34-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="0eb34-634">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="0eb34-634">Delete a to-do item</span></span>

<span data-ttu-id="0eb34-635">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="0eb34-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="0eb34-636">向 Web API 添加身份验证支持</span><span class="sxs-lookup"><span data-stu-id="0eb34-636">Add authentication support to a web API</span></span>

<span data-ttu-id="0eb34-637">请参阅 [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) 教程。</span><span class="sxs-lookup"><span data-stu-id="0eb34-637">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0eb34-638">其他资源</span><span class="sxs-lookup"><span data-stu-id="0eb34-638">Additional resources</span></span>

<span data-ttu-id="0eb34-639">[查看或下载本教程的示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="0eb34-640">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="0eb34-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="0eb34-641">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="0eb34-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="0eb34-642">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="0eb34-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
