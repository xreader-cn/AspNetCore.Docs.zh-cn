---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 73e547b014d78dcbcbf1c887ebec16e0743d10b9
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294753"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="5392e-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="5392e-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="5392e-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="5392e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="5392e-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="5392e-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5392e-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="5392e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5392e-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-107">Create a web API project.</span></span>
> * <span data-ttu-id="5392e-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="5392e-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="5392e-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="5392e-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="5392e-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="5392e-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="5392e-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-111">Call the web API with Postman.</span></span>

<span data-ttu-id="5392e-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="5392e-113">概述</span><span class="sxs-lookup"><span data-stu-id="5392e-113">Overview</span></span>

<span data-ttu-id="5392e-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="5392e-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="5392e-115">API</span><span class="sxs-lookup"><span data-stu-id="5392e-115">API</span></span> | <span data-ttu-id="5392e-116">描述</span><span class="sxs-lookup"><span data-stu-id="5392e-116">Description</span></span> | <span data-ttu-id="5392e-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="5392e-117">Request body</span></span> | <span data-ttu-id="5392e-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="5392e-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="5392e-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5392e-119">GET /api/TodoItems</span></span> | <span data-ttu-id="5392e-120">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-120">Get all to-do items</span></span> | <span data-ttu-id="5392e-121">None</span><span class="sxs-lookup"><span data-stu-id="5392e-121">None</span></span> | <span data-ttu-id="5392e-122">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="5392e-122">Array of to-do items</span></span>|
|<span data-ttu-id="5392e-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5392e-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="5392e-124">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="5392e-124">Get an item by ID</span></span> | <span data-ttu-id="5392e-125">None</span><span class="sxs-lookup"><span data-stu-id="5392e-125">None</span></span> | <span data-ttu-id="5392e-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-126">To-do item</span></span>|
|<span data-ttu-id="5392e-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5392e-127">POST /api/TodoItems</span></span> | <span data-ttu-id="5392e-128">添加新项</span><span class="sxs-lookup"><span data-stu-id="5392e-128">Add a new item</span></span> | <span data-ttu-id="5392e-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-129">To-do item</span></span> | <span data-ttu-id="5392e-130">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-130">To-do item</span></span> |
|<span data-ttu-id="5392e-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5392e-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="5392e-132">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5392e-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="5392e-133">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-133">To-do item</span></span> | <span data-ttu-id="5392e-134">None</span><span class="sxs-lookup"><span data-stu-id="5392e-134">None</span></span> |
|<span data-ttu-id="5392e-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5392e-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="5392e-136">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="5392e-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="5392e-137">None</span><span class="sxs-lookup"><span data-stu-id="5392e-137">None</span></span> | <span data-ttu-id="5392e-138">None</span><span class="sxs-lookup"><span data-stu-id="5392e-138">None</span></span>|

<span data-ttu-id="5392e-139">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="5392e-139">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="5392e-145">先决条件</span><span class="sxs-lookup"><span data-stu-id="5392e-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="5392e-149">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="5392e-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-151">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="5392e-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5392e-152">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="5392e-153">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="5392e-154">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”    。</span><span class="sxs-lookup"><span data-stu-id="5392e-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="5392e-155">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-155">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5392e-158">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="5392e-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5392e-159">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="5392e-160">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5392e-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="5392e-161">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="5392e-162">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="5392e-162">The preceding commands:</span></span>

  * <span data-ttu-id="5392e-163">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="5392e-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="5392e-164">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="5392e-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5392e-166">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-166">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="5392e-168">选择“.NET Core”  >“应用”  >“API”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="5392e-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="5392e-170">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架选择为\*“.NET Core 3.1”    。</span><span class="sxs-lookup"><span data-stu-id="5392e-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="5392e-171">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="5392e-173">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5392e-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="5392e-174">测试 API</span><span class="sxs-lookup"><span data-stu-id="5392e-174">Test the API</span></span>

<span data-ttu-id="5392e-175">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="5392e-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="5392e-176">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5392e-178">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5392e-179">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="5392e-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5392e-180">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5392e-181">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5392e-183">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5392e-184">在浏览器中，转到以下 URL：[https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)。</span><span class="sxs-lookup"><span data-stu-id="5392e-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5392e-186">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="5392e-187">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="5392e-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5392e-188">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="5392e-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5392e-189">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="5392e-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="5392e-190">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="5392e-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="5392e-191">添加模型类</span><span class="sxs-lookup"><span data-stu-id="5392e-191">Add a model class</span></span>

<span data-ttu-id="5392e-192">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="5392e-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5392e-193">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="5392e-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-195">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5392e-196">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5392e-197">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="5392e-198">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5392e-199">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="5392e-200">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5392e-202">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="5392e-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="5392e-203">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="5392e-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5392e-205">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-205">Right-click the project.</span></span> <span data-ttu-id="5392e-206">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5392e-207">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-207">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="5392e-209">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  >“常规”  >“空类”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="5392e-210">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="5392e-211">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="5392e-212">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="5392e-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5392e-213">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5392e-214">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5392e-214">Add a database context</span></span>

<span data-ttu-id="5392e-215">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="5392e-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5392e-216">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="5392e-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="5392e-218">添加 Add Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="5392e-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="5392e-219">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="5392e-220">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer   。</span><span class="sxs-lookup"><span data-stu-id="5392e-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="5392e-221">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="5392e-222">选中右窗格中的“项目”复选框，然后选择“安装”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="5392e-223">使用上述说明添加 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="5392e-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="5392e-225">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5392e-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="5392e-226">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5392e-227">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5392e-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5392e-229">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5392e-230">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5392e-231">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5392e-231">Register the database context</span></span>

<span data-ttu-id="5392e-232">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="5392e-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5392e-233">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="5392e-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="5392e-234">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="5392e-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="5392e-235">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-235">The preceding code:</span></span>

* <span data-ttu-id="5392e-236">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="5392e-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5392e-237">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="5392e-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5392e-238">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="5392e-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="5392e-239">构建控制器</span><span class="sxs-lookup"><span data-stu-id="5392e-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-241">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="5392e-242">选择“添加”>“新建构建项”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5392e-243">选择“其操作使用实体框架的 API 控制器”，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="5392e-244">在“添加其操作使用实体框架的 API 控制器”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="5392e-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="5392e-245">在“模型类”中选择“TodoItem (TodoApi.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="5392e-246">在“数据上下文类”中选择“TodoContext (TodoAPI.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="5392e-247">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5392e-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5392e-249">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5392e-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="5392e-250">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="5392e-250">The preceding commands:</span></span>

* <span data-ttu-id="5392e-251">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="5392e-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="5392e-252">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="5392e-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="5392e-253">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="5392e-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="5392e-254">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-254">The generated code:</span></span>

* <span data-ttu-id="5392e-255">使用 [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="5392e-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="5392e-256">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="5392e-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5392e-257">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="5392e-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="5392e-258">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="5392e-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5392e-259">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="5392e-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="5392e-260">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-260">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="5392e-261">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="5392e-261">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="5392e-262">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="5392e-262">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="5392e-263">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="5392e-263">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5392e-264"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="5392e-264">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="5392e-265">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="5392e-265">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="5392e-266">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="5392e-266">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5392e-267">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="5392e-267">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="5392e-268">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="5392e-268">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="5392e-269">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="5392e-269">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5392e-270">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="5392e-270">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5392e-271">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="5392e-271">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="5392e-272">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="5392e-272">Install Postman</span></span>

<span data-ttu-id="5392e-273">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-273">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="5392e-274">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="5392e-274">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="5392e-275">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-275">Start the web app.</span></span>
* <span data-ttu-id="5392e-276">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="5392e-276">Start Postman.</span></span>
* <span data-ttu-id="5392e-277">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="5392e-277">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="5392e-278">在“文件”  >“设置”（“常规”   选项卡）中，禁用“SSL 证书验证”。 </span><span class="sxs-lookup"><span data-stu-id="5392e-278">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="5392e-279">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="5392e-279">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="5392e-280">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="5392e-280">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="5392e-281">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="5392e-281">Create a new request.</span></span>
* <span data-ttu-id="5392e-282">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="5392e-282">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="5392e-283">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="5392e-283">Select the **Body** tab.</span></span>
* <span data-ttu-id="5392e-284">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="5392e-284">Select the **raw** radio button.</span></span>
* <span data-ttu-id="5392e-285">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="5392e-285">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="5392e-286">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="5392e-286">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="5392e-287">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-287">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5392e-289">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="5392e-289">Test the location header URI</span></span>

* <span data-ttu-id="5392e-290">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="5392e-290">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="5392e-291">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="5392e-291">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="5392e-293">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="5392e-293">Set the method to GET.</span></span>
* <span data-ttu-id="5392e-294">粘贴 URI（例如，`https://localhost:5001/api/TodoItems/1`）。</span><span class="sxs-lookup"><span data-stu-id="5392e-294">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="5392e-295">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-295">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="5392e-296">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-296">Examine the GET methods</span></span>

<span data-ttu-id="5392e-297">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="5392e-297">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="5392e-298">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-298">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="5392e-299">例如：</span><span class="sxs-lookup"><span data-stu-id="5392e-299">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="5392e-300">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="5392e-300">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="5392e-301">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="5392e-301">Test Get with Postman</span></span>

* <span data-ttu-id="5392e-302">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="5392e-302">Create a new request.</span></span>
* <span data-ttu-id="5392e-303">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-303">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="5392e-304">将请求 URL 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="5392e-304">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="5392e-305">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="5392e-305">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="5392e-306">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="5392e-306">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="5392e-307">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-307">Select **Send**.</span></span>

<span data-ttu-id="5392e-308">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="5392e-308">This app uses an in-memory database.</span></span> <span data-ttu-id="5392e-309">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="5392e-309">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="5392e-310">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-310">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="5392e-311">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="5392e-311">Routing and URL paths</span></span>

<span data-ttu-id="5392e-312">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="5392e-312">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5392e-313">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="5392e-313">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5392e-314">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="5392e-314">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="5392e-315">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="5392e-315">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5392e-316">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-316">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="5392e-317">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="5392e-317">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5392e-318">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="5392e-318">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5392e-319">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="5392e-319">This sample doesn't use a template.</span></span> <span data-ttu-id="5392e-320">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="5392e-320">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5392e-321">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="5392e-321">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5392e-322">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="5392e-322">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5392e-323">返回值</span><span class="sxs-lookup"><span data-stu-id="5392e-323">Return values</span></span>

<span data-ttu-id="5392e-324">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="5392e-324">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5392e-325">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="5392e-325">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5392e-326">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="5392e-326">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5392e-327">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="5392e-327">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5392e-328">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="5392e-328">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5392e-329">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="5392e-329">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5392e-330">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="5392e-330">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="5392e-331">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="5392e-331">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5392e-332">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="5392e-332">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="5392e-333">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-333">The PutTodoItem method</span></span>

<span data-ttu-id="5392e-334">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="5392e-334">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="5392e-335">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="5392e-335">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5392e-336">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="5392e-336">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5392e-337">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="5392e-337">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5392e-338">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="5392e-338">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5392e-339">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-339">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5392e-340">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-340">Test the PutTodoItem method</span></span>

<span data-ttu-id="5392e-341">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="5392e-341">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="5392e-342">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="5392e-342">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5392e-343">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-343">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5392e-344">更新 ID = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="5392e-344">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="5392e-345">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="5392e-345">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="5392e-347">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-347">The DeleteTodoItem method</span></span>

<span data-ttu-id="5392e-348">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="5392e-348">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5392e-349">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-349">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5392e-350">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="5392e-350">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="5392e-351">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="5392e-351">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="5392e-352">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="5392e-352">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="5392e-353">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-353">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="5392e-354">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="5392e-354">Call the web API with JavaScript</span></span>

<span data-ttu-id="5392e-355">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="5392e-355">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5392e-356">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="5392e-356">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5392e-357">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-357">Create a web API project.</span></span>
> * <span data-ttu-id="5392e-358">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="5392e-358">Add a model class and a database context.</span></span>
> * <span data-ttu-id="5392e-359">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="5392e-359">Add a controller.</span></span>
> * <span data-ttu-id="5392e-360">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="5392e-360">Add CRUD methods.</span></span>
> * <span data-ttu-id="5392e-361">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="5392e-361">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="5392e-362">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="5392e-362">Specify return values.</span></span>
> * <span data-ttu-id="5392e-363">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-363">Call the web API with Postman.</span></span>
> * <span data-ttu-id="5392e-364">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-364">Call the web API with JavaScript.</span></span>

<span data-ttu-id="5392e-365">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-365">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="5392e-366">概述</span><span class="sxs-lookup"><span data-stu-id="5392e-366">Overview</span></span>

<span data-ttu-id="5392e-367">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="5392e-367">This tutorial creates the following API:</span></span>

|<span data-ttu-id="5392e-368">API</span><span class="sxs-lookup"><span data-stu-id="5392e-368">API</span></span> | <span data-ttu-id="5392e-369">描述</span><span class="sxs-lookup"><span data-stu-id="5392e-369">Description</span></span> | <span data-ttu-id="5392e-370">请求正文</span><span class="sxs-lookup"><span data-stu-id="5392e-370">Request body</span></span> | <span data-ttu-id="5392e-371">响应正文</span><span class="sxs-lookup"><span data-stu-id="5392e-371">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="5392e-372">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5392e-372">GET /api/TodoItems</span></span> | <span data-ttu-id="5392e-373">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-373">Get all to-do items</span></span> | <span data-ttu-id="5392e-374">None</span><span class="sxs-lookup"><span data-stu-id="5392e-374">None</span></span> | <span data-ttu-id="5392e-375">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="5392e-375">Array of to-do items</span></span>|
|<span data-ttu-id="5392e-376">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5392e-376">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="5392e-377">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="5392e-377">Get an item by ID</span></span> | <span data-ttu-id="5392e-378">None</span><span class="sxs-lookup"><span data-stu-id="5392e-378">None</span></span> | <span data-ttu-id="5392e-379">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-379">To-do item</span></span>|
|<span data-ttu-id="5392e-380">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5392e-380">POST /api/TodoItems</span></span> | <span data-ttu-id="5392e-381">添加新项</span><span class="sxs-lookup"><span data-stu-id="5392e-381">Add a new item</span></span> | <span data-ttu-id="5392e-382">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-382">To-do item</span></span> | <span data-ttu-id="5392e-383">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-383">To-do item</span></span> |
|<span data-ttu-id="5392e-384">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5392e-384">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="5392e-385">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5392e-385">Update an existing item &nbsp;</span></span> | <span data-ttu-id="5392e-386">待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-386">To-do item</span></span> | <span data-ttu-id="5392e-387">None</span><span class="sxs-lookup"><span data-stu-id="5392e-387">None</span></span> |
|<span data-ttu-id="5392e-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="5392e-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="5392e-389">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="5392e-389">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="5392e-390">None</span><span class="sxs-lookup"><span data-stu-id="5392e-390">None</span></span> | <span data-ttu-id="5392e-391">None</span><span class="sxs-lookup"><span data-stu-id="5392e-391">None</span></span>|

<span data-ttu-id="5392e-392">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="5392e-392">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="5392e-398">先决条件</span><span class="sxs-lookup"><span data-stu-id="5392e-398">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-399">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-400">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-400">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-401">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-401">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="5392e-402">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="5392e-402">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-403">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-404">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="5392e-404">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="5392e-405">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-405">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="5392e-406">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-406">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="5392e-407">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”    。</span><span class="sxs-lookup"><span data-stu-id="5392e-407">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="5392e-408">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-408">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="5392e-409">请不要选择“启用 Docker 支持”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-409">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5392e-412">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="5392e-412">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="5392e-413">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-413">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="5392e-414">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5392e-414">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="5392e-415">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="5392e-415">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="5392e-416">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-416">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-417">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-417">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5392e-418">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-418">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="5392e-420">选择“.NET Core”  >“应用”  >“API”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="5392e-420">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="5392e-422">在“配置新的 ASP.NET Core Web API”  对话框中，接受默认的 \*.NET Core 2.2  “目标框架”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-422">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="5392e-423">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-423">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="5392e-425">测试 API</span><span class="sxs-lookup"><span data-stu-id="5392e-425">Test the API</span></span>

<span data-ttu-id="5392e-426">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="5392e-426">The project template creates a `values` API.</span></span> <span data-ttu-id="5392e-427">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-427">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-428">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5392e-429">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-429">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5392e-430">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="5392e-430">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5392e-431">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-431">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5392e-432">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-432">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5392e-434">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-434">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="5392e-435">在浏览器中，转到以下 URL：[https://localhost:5001/api/values](https://localhost:5001/api/values)。</span><span class="sxs-lookup"><span data-stu-id="5392e-435">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5392e-437">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-437">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="5392e-438">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="5392e-438">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5392e-439">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="5392e-439">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5392e-440">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="5392e-440">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="5392e-441">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="5392e-441">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="5392e-442">添加模型类</span><span class="sxs-lookup"><span data-stu-id="5392e-442">Add a model class</span></span>

<span data-ttu-id="5392e-443">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="5392e-443">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5392e-444">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="5392e-444">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-445">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-445">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-446">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-446">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5392e-447">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-447">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5392e-448">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-448">Name the folder *Models*.</span></span>

* <span data-ttu-id="5392e-449">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-449">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5392e-450">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-450">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="5392e-451">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-451">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5392e-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5392e-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5392e-453">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="5392e-453">Add a folder named *Models*.</span></span>

* <span data-ttu-id="5392e-454">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="5392e-454">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5392e-455">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5392e-456">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-456">Right-click the project.</span></span> <span data-ttu-id="5392e-457">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-457">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5392e-458">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-458">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="5392e-460">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  >“常规”  >“空类”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-460">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="5392e-461">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-461">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="5392e-462">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-462">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="5392e-463">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="5392e-463">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5392e-464">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-464">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5392e-465">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5392e-465">Add a database context</span></span>

<span data-ttu-id="5392e-466">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="5392e-466">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5392e-467">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="5392e-467">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-469">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-469">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5392e-470">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-470">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5392e-471">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-471">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5392e-472">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-472">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5392e-473">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-473">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5392e-474">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="5392e-474">Register the database context</span></span>

<span data-ttu-id="5392e-475">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="5392e-475">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5392e-476">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="5392e-476">The container provides the service to controllers.</span></span>

<span data-ttu-id="5392e-477">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="5392e-477">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="5392e-478">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-478">The preceding code:</span></span>

* <span data-ttu-id="5392e-479">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="5392e-479">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5392e-480">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="5392e-480">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5392e-481">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="5392e-481">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="5392e-482">添加控制器</span><span class="sxs-lookup"><span data-stu-id="5392e-482">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-484">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-484">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="5392e-485">选择“添加”>“新项”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-485">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="5392e-486">在“添加新项”对话框中，选择“API 控制器类”模板   。</span><span class="sxs-lookup"><span data-stu-id="5392e-486">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="5392e-487">将类命名为 TodoController，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="5392e-487">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5392e-489">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-489">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5392e-490">在“控制器”文件夹中，创建名为 `TodoController` 的类  。</span><span class="sxs-lookup"><span data-stu-id="5392e-490">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="5392e-491">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-491">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="5392e-492">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="5392e-492">The preceding code:</span></span>

* <span data-ttu-id="5392e-493">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="5392e-493">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="5392e-494">使用 [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="5392e-494">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="5392e-495">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="5392e-495">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5392e-496">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="5392e-496">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="5392e-497">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="5392e-497">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5392e-498">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="5392e-498">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="5392e-499">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="5392e-499">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="5392e-500">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="5392e-500">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="5392e-501">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="5392e-501">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="5392e-502">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="5392e-502">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="5392e-503">添加 Get 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-503">Add Get methods</span></span>

<span data-ttu-id="5392e-504">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="5392e-504">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="5392e-505">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="5392e-505">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="5392e-506">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="5392e-506">Stop the app if it's still running.</span></span> <span data-ttu-id="5392e-507">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="5392e-507">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="5392e-508">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-508">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="5392e-509">例如：</span><span class="sxs-lookup"><span data-stu-id="5392e-509">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="5392e-510">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="5392e-510">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="5392e-511">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="5392e-511">Routing and URL paths</span></span>

<span data-ttu-id="5392e-512">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="5392e-512">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5392e-513">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="5392e-513">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5392e-514">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="5392e-514">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="5392e-515">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="5392e-515">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5392e-516">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-516">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="5392e-517">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="5392e-517">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5392e-518">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="5392e-518">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5392e-519">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="5392e-519">This sample doesn't use a template.</span></span> <span data-ttu-id="5392e-520">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="5392e-520">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5392e-521">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="5392e-521">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5392e-522">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="5392e-522">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5392e-523">返回值</span><span class="sxs-lookup"><span data-stu-id="5392e-523">Return values</span></span>

<span data-ttu-id="5392e-524">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="5392e-524">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5392e-525">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="5392e-525">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5392e-526">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="5392e-526">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5392e-527">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="5392e-527">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5392e-528">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="5392e-528">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5392e-529">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="5392e-529">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5392e-530">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="5392e-530">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="5392e-531">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="5392e-531">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5392e-532">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="5392e-532">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="5392e-533">测试 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-533">Test the GetTodoItems method</span></span>

<span data-ttu-id="5392e-534">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-534">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="5392e-535">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="5392e-535">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="5392e-536">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="5392e-536">Start the web app.</span></span>
* <span data-ttu-id="5392e-537">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="5392e-537">Start Postman.</span></span>
* <span data-ttu-id="5392e-538">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="5392e-538">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5392e-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5392e-539">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5392e-540">在“文件”  >“设置”（“常规”   选项卡）中，禁用“SSL 证书验证”。 </span><span class="sxs-lookup"><span data-stu-id="5392e-540">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5392e-541">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5392e-541">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5392e-542">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="5392e-542">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="5392e-543">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-543">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="5392e-544">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="5392e-544">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="5392e-545">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="5392e-545">Create a new request.</span></span>
  * <span data-ttu-id="5392e-546">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="5392e-546">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="5392e-547">将请求 URL 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="5392e-547">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="5392e-548">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="5392e-548">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="5392e-549">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="5392e-549">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="5392e-550">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-550">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="5392e-552">添加创建方法</span><span class="sxs-lookup"><span data-stu-id="5392e-552">Add a Create method</span></span>

<span data-ttu-id="5392e-553">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="5392e-553">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="5392e-554">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="5392e-554">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="5392e-555">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="5392e-555">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5392e-556">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="5392e-556">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="5392e-557">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="5392e-557">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="5392e-558">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="5392e-558">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5392e-559">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="5392e-559">Adds a `Location` header to the response.</span></span> <span data-ttu-id="5392e-560">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="5392e-560">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="5392e-561">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="5392e-561">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5392e-562">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="5392e-562">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5392e-563">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="5392e-563">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="5392e-564">测试 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-564">Test the PostTodoItem method</span></span>

* <span data-ttu-id="5392e-565">生成项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-565">Build the project.</span></span>
* <span data-ttu-id="5392e-566">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="5392e-566">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="5392e-567">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="5392e-567">Select the **Body** tab.</span></span>
* <span data-ttu-id="5392e-568">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="5392e-568">Select the **raw** radio button.</span></span>
* <span data-ttu-id="5392e-569">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="5392e-569">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="5392e-570">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="5392e-570">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="5392e-571">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-571">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="5392e-573">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-573">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5392e-574">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="5392e-574">Test the location header URI</span></span>

* <span data-ttu-id="5392e-575">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="5392e-575">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="5392e-576">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="5392e-576">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="5392e-578">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="5392e-578">Set the method to GET.</span></span>
* <span data-ttu-id="5392e-579">粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）。</span><span class="sxs-lookup"><span data-stu-id="5392e-579">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="5392e-580">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-580">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="5392e-581">添加 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-581">Add a PutTodoItem method</span></span>

<span data-ttu-id="5392e-582">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="5392e-582">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="5392e-583">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="5392e-583">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5392e-584">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="5392e-584">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5392e-585">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="5392e-585">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5392e-586">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="5392e-586">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5392e-587">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-587">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5392e-588">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-588">Test the PutTodoItem method</span></span>

<span data-ttu-id="5392e-589">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="5392e-589">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="5392e-590">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="5392e-590">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5392e-591">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="5392e-591">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5392e-592">更新 id = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="5392e-592">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="5392e-593">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="5392e-593">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="5392e-595">添加 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-595">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="5392e-596">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="5392e-596">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="5392e-597">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="5392e-597">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5392e-598">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="5392e-598">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5392e-599">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="5392e-599">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="5392e-600">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="5392e-600">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="5392e-601">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="5392e-601">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="5392e-602">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="5392e-602">Select **Send**.</span></span>

<span data-ttu-id="5392e-603">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="5392e-603">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="5392e-604">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="5392e-604">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="5392e-605">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="5392e-605">Call the web API with JavaScript</span></span>

<span data-ttu-id="5392e-606">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="5392e-606">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="5392e-607">jQuery 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="5392e-607">jQuery initiates the request.</span></span> <span data-ttu-id="5392e-608">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="5392e-608">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="5392e-609">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：</span><span class="sxs-lookup"><span data-stu-id="5392e-609">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="5392e-610">在项目目录中创建 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="5392e-610">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="5392e-611">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="5392e-611">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="5392e-612">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="5392e-612">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="5392e-613">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="5392e-613">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="5392e-614">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="5392e-614">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="5392e-615">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="5392e-615">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="5392e-616">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="5392e-616">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="5392e-617">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="5392e-617">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="5392e-618">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="5392e-618">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="5392e-619">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="5392e-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="5392e-620">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="5392e-620">Get a list of to-do items</span></span>

<span data-ttu-id="5392e-621">jQuery 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="5392e-621">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="5392e-622">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="5392e-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="5392e-623">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="5392e-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="5392e-624">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-624">Add a to-do item</span></span>

<span data-ttu-id="5392e-625">jQuery 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="5392e-625">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="5392e-626">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="5392e-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="5392e-627">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="5392e-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="5392e-628">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="5392e-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="5392e-629">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-629">Update a to-do item</span></span>

<span data-ttu-id="5392e-630">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="5392e-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="5392e-631">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="5392e-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="5392e-632">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="5392e-632">Delete a to-do item</span></span>

<span data-ttu-id="5392e-633">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="5392e-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="5392e-634">向 Web API 添加身份验证支持</span><span class="sxs-lookup"><span data-stu-id="5392e-634">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="5392e-635">其他资源</span><span class="sxs-lookup"><span data-stu-id="5392e-635">Additional resources</span></span>

<span data-ttu-id="5392e-636">[查看或下载本教程的示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="5392e-636">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="5392e-637">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="5392e-637">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="5392e-638">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="5392e-638">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="5392e-639">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="5392e-639">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
