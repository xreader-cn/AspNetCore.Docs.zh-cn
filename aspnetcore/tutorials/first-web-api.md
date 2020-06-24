---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: 71ab0fc0a679acf540fa53fa2a9c8ba893b34edf
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724349"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="580af-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="580af-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="580af-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="580af-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="580af-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="580af-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="580af-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="580af-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="580af-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="580af-107">Create a web API project.</span></span>
> * <span data-ttu-id="580af-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="580af-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="580af-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="580af-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="580af-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="580af-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="580af-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-111">Call the web API with Postman.</span></span>

<span data-ttu-id="580af-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="580af-113">概述</span><span class="sxs-lookup"><span data-stu-id="580af-113">Overview</span></span>

<span data-ttu-id="580af-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="580af-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="580af-115">API</span><span class="sxs-lookup"><span data-stu-id="580af-115">API</span></span> | <span data-ttu-id="580af-116">描述</span><span class="sxs-lookup"><span data-stu-id="580af-116">Description</span></span> | <span data-ttu-id="580af-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="580af-117">Request body</span></span> | <span data-ttu-id="580af-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="580af-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="580af-119">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-119">Get all to-do items</span></span> | <span data-ttu-id="580af-120">None</span><span class="sxs-lookup"><span data-stu-id="580af-120">None</span></span> | <span data-ttu-id="580af-121">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="580af-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="580af-122">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="580af-122">Get an item by ID</span></span> | <span data-ttu-id="580af-123">None</span><span class="sxs-lookup"><span data-stu-id="580af-123">None</span></span> | <span data-ttu-id="580af-124">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="580af-125">添加新项</span><span class="sxs-lookup"><span data-stu-id="580af-125">Add a new item</span></span> | <span data-ttu-id="580af-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-126">To-do item</span></span> | <span data-ttu-id="580af-127">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="580af-128">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="580af-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="580af-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-129">To-do item</span></span> | <span data-ttu-id="580af-130">None</span><span class="sxs-lookup"><span data-stu-id="580af-130">None</span></span> |
|<span data-ttu-id="580af-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="580af-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="580af-132">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="580af-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="580af-133">None</span><span class="sxs-lookup"><span data-stu-id="580af-133">None</span></span> | <span data-ttu-id="580af-134">None</span><span class="sxs-lookup"><span data-stu-id="580af-134">None</span></span>|

<span data-ttu-id="580af-135">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="580af-135">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="580af-141">先决条件</span><span class="sxs-lookup"><span data-stu-id="580af-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="580af-145">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="580af-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-147">从“文件”菜单中选择“新建”>“项目”\*\*\*\* \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="580af-148">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="580af-149">将项目命名为 TodoApi，然后单击“创建”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="580af-150">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”\*\*\*\* \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="580af-151">选择“API”模板，然后单击“创建”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-151">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="580af-154">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="580af-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="580af-155">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="580af-156">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="580af-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="580af-157">当对话框询问是否要将所需资产添加到项目时，选择“是”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="580af-158">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="580af-158">The preceding commands:</span></span>

  * <span data-ttu-id="580af-159">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="580af-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="580af-160">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="580af-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="580af-162">选择“文件”>“新建解决方案”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="580af-164">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”\*\*\*\* \*\*\*\* \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="580af-165">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。\*\*\*\* \*\*\*\* \*\*\*\* \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="580af-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="580af-167">确认“目标框架”设置为“.NET Core 3.1”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-167">Confirm the **Target Framework** is set to **.NET Core 3.1**.</span></span> <span data-ttu-id="580af-168">选择“下一步”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-168">Select **Next**.</span></span>

  ![macOS .NET Core 3.1 选择](first-web-api-mac/_static/api_31_config.png)

* <span data-ttu-id="580af-170">输入“TodoApi”\*\* 作为“项目名称”\*\*\*\*，然后选择“创建”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="580af-172">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="580af-172">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="580af-173">测试 API</span><span class="sxs-lookup"><span data-stu-id="580af-173">Test the API</span></span>

<span data-ttu-id="580af-174">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="580af-174">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="580af-175">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="580af-175">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="580af-177">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="580af-177">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="580af-178">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="580af-178">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="580af-179">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-179">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="580af-180">在接下来出现的“安全警告”\*\*\*\* 对话框中，选择“是”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-180">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="580af-182">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="580af-182">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="580af-183">在浏览器中，转到以下 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="580af-183">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-184">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-184">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="580af-185">选择“运行”\*\*\*\* > “开始调试”\*\*\*\* 以启动应用。</span><span class="sxs-lookup"><span data-stu-id="580af-185">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="580af-186">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="580af-186">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="580af-187">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="580af-187">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="580af-188">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="580af-188">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="580af-189">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="580af-189">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="580af-190">添加模型类</span><span class="sxs-lookup"><span data-stu-id="580af-190">Add a model class</span></span>

<span data-ttu-id="580af-191">模型\*\* 是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="580af-191">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="580af-192">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="580af-192">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-194">在“解决方案资源管理器”\*\*\*\* 中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="580af-194">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="580af-195">选择“添加”\*\*\*\* > “新建文件夹”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-195">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="580af-196">将文件夹命名为“Models”\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-196">Name the folder *Models*.</span></span>

* <span data-ttu-id="580af-197">右键单击“Models”\*\* 文件夹，然后选择“添加”\*\*\*\* > “类”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-197">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="580af-198">将类命名为 TodoItem，然后选择“添加”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-198">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="580af-199">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="580af-199">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="580af-201">添加名为“Models”的文件夹\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-201">Add a folder named *Models*.</span></span>

* <span data-ttu-id="580af-202">使用以下代码将 `TodoItem` 类添加到 Models\*\* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="580af-202">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-203">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="580af-204">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="580af-204">Right-click the project.</span></span> <span data-ttu-id="580af-205">选择“添加”\*\*\*\* > “新建文件夹”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-205">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="580af-206">将文件夹命名为“Models”\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-206">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="580af-208">右键单击“Models”\*\* 文件夹，然后选择“添加”\*\*\*\*>“新建文件”\*\*\*\*>“常规”\*\*\*\*>“空类”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-208">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="580af-209">将类命名为“TodoItem”，然后单击“新建”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-209">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="580af-210">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="580af-210">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="580af-211">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="580af-211">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="580af-212">模型类可位于项目的任意位置，但按照惯例会使用 Models\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-212">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="580af-213">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="580af-213">Add a database context</span></span>

<span data-ttu-id="580af-214">数据库上下文是为数据模型协调 Entity Framework 功能的主类\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-214">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="580af-215">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="580af-215">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-216">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="580af-217">添加 Add Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="580af-217">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="580af-218">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-218">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="580af-219">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-219">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="580af-220">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-220">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="580af-221">选中右窗格中的“项目”复选框，然后选择“安装”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-221">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="580af-222">使用上述说明添加 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="580af-222">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="580af-224">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="580af-224">Add the TodoContext database context</span></span>

* <span data-ttu-id="580af-225">右键单击“Models”\*\* 文件夹，然后选择“添加”\*\*\*\* > “类”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-225">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="580af-226">将类命名为 TodoContext，然后单击“添加”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-226">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="580af-227">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-227">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="580af-228">将 `TodoContext` 类添加到“Models”\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-228">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="580af-229">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="580af-229">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="580af-230">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="580af-230">Register the database context</span></span>

<span data-ttu-id="580af-231">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="580af-231">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="580af-232">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="580af-232">The container provides the service to controllers.</span></span>

<span data-ttu-id="580af-233">使用以下突出显示的代码更新 Startup.cs\*\*：</span><span class="sxs-lookup"><span data-stu-id="580af-233">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="580af-234">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="580af-234">The preceding code:</span></span>

* <span data-ttu-id="580af-235">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="580af-235">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="580af-236">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="580af-236">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="580af-237">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="580af-237">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="580af-238">构建控制器</span><span class="sxs-lookup"><span data-stu-id="580af-238">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-239">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-240">右键单击 Controllers\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-240">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="580af-241">选择“添加”>“新建构建项”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-241">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="580af-242">选择“其操作使用实体框架的 API 控制器”，然后选择“添加”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-242">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="580af-243">在“添加其操作使用实体框架的 API 控制器”对话框中\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="580af-243">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="580af-244">在“模型类”中选择“TodoItem (TodoApi.Models)”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-244">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="580af-245">在“数据上下文类”中选择“TodoContext (TodoAPI.Models)”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-245">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="580af-246">选择“添加”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-246">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="580af-247">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="580af-248">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="580af-248">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="580af-249">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="580af-249">The preceding commands:</span></span>

* <span data-ttu-id="580af-250">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="580af-250">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="580af-251">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="580af-251">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="580af-252">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="580af-252">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="580af-253">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="580af-253">The generated code:</span></span>

* <span data-ttu-id="580af-254">使用 [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="580af-254">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="580af-255">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="580af-255">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="580af-256">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="580af-256">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="580af-257">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="580af-257">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="580af-258">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="580af-258">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="580af-259">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="580af-259">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="580af-260">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="580af-260">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="580af-261">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="580af-261">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="580af-262">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="580af-262">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="580af-263">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="580af-263">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="580af-264">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="580af-264">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="580af-265">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="580af-265">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="580af-266">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="580af-266">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="580af-267">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="580af-267">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="580af-268"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="580af-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="580af-269">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="580af-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="580af-270">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="580af-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="580af-271">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="580af-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="580af-272">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="580af-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="580af-273">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="580af-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="580af-274">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="580af-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="580af-275">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="580af-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="580af-276">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="580af-276">Install Postman</span></span>

<span data-ttu-id="580af-277">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="580af-278">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="580af-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="580af-279">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="580af-279">Start the web app.</span></span>
* <span data-ttu-id="580af-280">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="580af-280">Start Postman.</span></span>
* <span data-ttu-id="580af-281">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="580af-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="580af-282">在“文件”\*\*\*\*>“设置”（“常规”\*\*\*\* \*\*\*\* 选项卡）中，禁用“SSL 证书验证”。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="580af-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="580af-283">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="580af-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="580af-284">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="580af-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="580af-285">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="580af-285">Create a new request.</span></span>
* <span data-ttu-id="580af-286">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="580af-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="580af-287">选择“正文”选项卡\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-287">Select the **Body** tab.</span></span>
* <span data-ttu-id="580af-288">选择“原始”单选按钮\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-288">Select the **raw** radio button.</span></span>
* <span data-ttu-id="580af-289">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="580af-289">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="580af-290">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="580af-290">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="580af-291">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-291">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="580af-293">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="580af-293">Test the location header URI</span></span>

* <span data-ttu-id="580af-294">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="580af-294">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="580af-295">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="580af-295">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="580af-297">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="580af-297">Set the method to GET.</span></span>
* <span data-ttu-id="580af-298">粘贴 URI（例如，`https://localhost:5001/api/TodoItems/1`）。</span><span class="sxs-lookup"><span data-stu-id="580af-298">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="580af-299">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-299">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="580af-300">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="580af-300">Examine the GET methods</span></span>

<span data-ttu-id="580af-301">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="580af-301">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="580af-302">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="580af-302">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="580af-303">例如：</span><span class="sxs-lookup"><span data-stu-id="580af-303">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="580af-304">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="580af-304">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="580af-305">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="580af-305">Test Get with Postman</span></span>

* <span data-ttu-id="580af-306">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="580af-306">Create a new request.</span></span>
* <span data-ttu-id="580af-307">将 HTTP 方法设置为“GET”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-307">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="580af-308">将请求 URL 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="580af-308">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="580af-309">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="580af-309">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="580af-310">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="580af-310">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="580af-311">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-311">Select **Send**.</span></span>

<span data-ttu-id="580af-312">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="580af-312">This app uses an in-memory database.</span></span> <span data-ttu-id="580af-313">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="580af-313">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="580af-314">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="580af-314">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="580af-315">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="580af-315">Routing and URL paths</span></span>

<span data-ttu-id="580af-316">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="580af-316">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="580af-317">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="580af-317">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="580af-318">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="580af-318">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="580af-319">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="580af-319">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="580af-320">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-320">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="580af-321">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="580af-321">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="580af-322">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="580af-322">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="580af-323">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="580af-323">This sample doesn't use a template.</span></span> <span data-ttu-id="580af-324">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="580af-324">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="580af-325">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="580af-325">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="580af-326">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="580af-326">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="580af-327">返回值</span><span class="sxs-lookup"><span data-stu-id="580af-327">Return values</span></span>

<span data-ttu-id="580af-328">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="580af-328">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="580af-329">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="580af-329">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="580af-330">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="580af-330">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="580af-331">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="580af-331">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="580af-332">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="580af-332">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="580af-333">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="580af-333">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="580af-334">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="580af-334">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="580af-335">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="580af-335">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="580af-336">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="580af-336">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="580af-337">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-337">The PutTodoItem method</span></span>

<span data-ttu-id="580af-338">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="580af-338">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="580af-339">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="580af-339">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="580af-340">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="580af-340">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="580af-341">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="580af-341">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="580af-342">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="580af-342">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="580af-343">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="580af-343">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="580af-344">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-344">Test the PutTodoItem method</span></span>

<span data-ttu-id="580af-345">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="580af-345">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="580af-346">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="580af-346">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="580af-347">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="580af-347">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="580af-348">更新 ID = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="580af-348">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="580af-349">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="580af-349">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="580af-351">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-351">The DeleteTodoItem method</span></span>

<span data-ttu-id="580af-352">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="580af-352">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="580af-353">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="580af-354">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="580af-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="580af-355">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="580af-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="580af-356">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="580af-356">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="580af-357">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-357">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="580af-358">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="580af-358">Prevent over-posting</span></span>

<span data-ttu-id="580af-359">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="580af-359">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="580af-360">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="580af-360">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="580af-361">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="580af-361">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="580af-362">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="580af-362">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="580af-363">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="580af-363">**DTO** is used in this article.</span></span>

<span data-ttu-id="580af-364">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="580af-364">A DTO may be used to:</span></span>

* <span data-ttu-id="580af-365">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="580af-365">Prevent over-posting.</span></span>
* <span data-ttu-id="580af-366">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="580af-366">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="580af-367">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="580af-367">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="580af-368">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="580af-368">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="580af-369">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="580af-369">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="580af-370">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="580af-370">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="580af-371">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="580af-371">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="580af-372">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="580af-372">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="580af-373">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="580af-373">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="580af-374">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="580af-374">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="580af-375">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="580af-375">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="580af-376">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="580af-376">Call the web API with JavaScript</span></span>

<span data-ttu-id="580af-377">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="580af-377">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="580af-378">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="580af-378">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="580af-379">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="580af-379">Create a web API project.</span></span>
> * <span data-ttu-id="580af-380">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="580af-380">Add a model class and a database context.</span></span>
> * <span data-ttu-id="580af-381">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="580af-381">Add a controller.</span></span>
> * <span data-ttu-id="580af-382">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="580af-382">Add CRUD methods.</span></span>
> * <span data-ttu-id="580af-383">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="580af-383">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="580af-384">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="580af-384">Specify return values.</span></span>
> * <span data-ttu-id="580af-385">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-385">Call the web API with Postman.</span></span>
> * <span data-ttu-id="580af-386">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-386">Call the web API with JavaScript.</span></span>

<span data-ttu-id="580af-387">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-387">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="580af-388">概述</span><span class="sxs-lookup"><span data-stu-id="580af-388">Overview</span></span>

<span data-ttu-id="580af-389">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="580af-389">This tutorial creates the following API:</span></span>

|<span data-ttu-id="580af-390">API</span><span class="sxs-lookup"><span data-stu-id="580af-390">API</span></span> | <span data-ttu-id="580af-391">描述</span><span class="sxs-lookup"><span data-stu-id="580af-391">Description</span></span> | <span data-ttu-id="580af-392">请求正文</span><span class="sxs-lookup"><span data-stu-id="580af-392">Request body</span></span> | <span data-ttu-id="580af-393">响应正文</span><span class="sxs-lookup"><span data-stu-id="580af-393">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="580af-394">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="580af-394">GET /api/TodoItems</span></span> | <span data-ttu-id="580af-395">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-395">Get all to-do items</span></span> | <span data-ttu-id="580af-396">None</span><span class="sxs-lookup"><span data-stu-id="580af-396">None</span></span> | <span data-ttu-id="580af-397">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="580af-397">Array of to-do items</span></span>|
|<span data-ttu-id="580af-398">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="580af-398">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="580af-399">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="580af-399">Get an item by ID</span></span> | <span data-ttu-id="580af-400">None</span><span class="sxs-lookup"><span data-stu-id="580af-400">None</span></span> | <span data-ttu-id="580af-401">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-401">To-do item</span></span>|
|<span data-ttu-id="580af-402">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="580af-402">POST /api/TodoItems</span></span> | <span data-ttu-id="580af-403">添加新项</span><span class="sxs-lookup"><span data-stu-id="580af-403">Add a new item</span></span> | <span data-ttu-id="580af-404">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-404">To-do item</span></span> | <span data-ttu-id="580af-405">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-405">To-do item</span></span> |
|<span data-ttu-id="580af-406">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="580af-406">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="580af-407">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="580af-407">Update an existing item &nbsp;</span></span> | <span data-ttu-id="580af-408">待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-408">To-do item</span></span> | <span data-ttu-id="580af-409">None</span><span class="sxs-lookup"><span data-stu-id="580af-409">None</span></span> |
|<span data-ttu-id="580af-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="580af-410">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="580af-411">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="580af-411">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="580af-412">None</span><span class="sxs-lookup"><span data-stu-id="580af-412">None</span></span> | <span data-ttu-id="580af-413">None</span><span class="sxs-lookup"><span data-stu-id="580af-413">None</span></span>|

<span data-ttu-id="580af-414">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="580af-414">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="580af-420">先决条件</span><span class="sxs-lookup"><span data-stu-id="580af-420">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-421">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-423">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-423">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="580af-424">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="580af-424">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-425">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-425">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-426">从“文件”菜单中选择“新建”>“项目”\*\*\*\* \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-426">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="580af-427">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-427">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="580af-428">将项目命名为 TodoApi，然后单击“创建”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-428">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="580af-429">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”\*\*\*\* \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-429">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="580af-430">选择“API”模板，然后单击“创建”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-430">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="580af-431">请不要选择“启用 Docker 支持”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-431">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="580af-434">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="580af-434">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="580af-435">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-435">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="580af-436">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="580af-436">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="580af-437">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="580af-437">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="580af-438">当对话框询问是否要将所需资产添加到项目时，选择“是”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-438">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-439">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="580af-440">选择“文件”>“新建解决方案”\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-440">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="580af-442">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”\*\*\*\* \*\*\*\* \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-442">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="580af-443">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。\*\*\*\* \*\*\*\* \*\*\*\* \*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="580af-443">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="580af-444">在“配置新的 ASP.NET Core Web API”\*\*\*\* 对话框中，接受默认的 \*.NET Core 2.2\*\*“目标框架”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-444">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="580af-445">输入“TodoApi”\*\* 作为“项目名称”\*\*\*\*，然后选择“创建”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="580af-447">测试 API</span><span class="sxs-lookup"><span data-stu-id="580af-447">Test the API</span></span>

<span data-ttu-id="580af-448">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="580af-448">The project template creates a `values` API.</span></span> <span data-ttu-id="580af-449">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="580af-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="580af-451">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="580af-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="580af-452">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="580af-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="580af-453">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="580af-454">在接下来出现的“安全警告”\*\*\*\* 对话框中，选择“是”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="580af-456">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="580af-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="580af-457">在浏览器中，转到以下 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="580af-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-458">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="580af-459">选择“运行”\*\*\*\* > “开始调试”\*\*\*\* 以启动应用。</span><span class="sxs-lookup"><span data-stu-id="580af-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="580af-460">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="580af-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="580af-461">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="580af-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="580af-462">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="580af-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="580af-463">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="580af-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="580af-464">添加模型类</span><span class="sxs-lookup"><span data-stu-id="580af-464">Add a model class</span></span>

<span data-ttu-id="580af-465">模型\*\* 是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="580af-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="580af-466">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="580af-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-468">在“解决方案资源管理器”\*\*\*\* 中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="580af-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="580af-469">选择“添加”\*\*\*\* > “新建文件夹”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="580af-470">将文件夹命名为“Models”\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="580af-471">右键单击“Models”\*\* 文件夹，然后选择“添加”\*\*\*\* > “类”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="580af-472">将类命名为 TodoItem，然后选择“添加”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="580af-473">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="580af-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="580af-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="580af-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="580af-475">添加名为“Models”的文件夹\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="580af-476">使用以下代码将 `TodoItem` 类添加到 Models\*\* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="580af-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="580af-477">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="580af-478">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="580af-478">Right-click the project.</span></span> <span data-ttu-id="580af-479">选择“添加”\*\*\*\* > “新建文件夹”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="580af-480">将文件夹命名为“Models”\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-480">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="580af-482">右键单击“Models”\*\* 文件夹，然后选择“添加”\*\*\*\*>“新建文件”\*\*\*\*>“常规”\*\*\*\*>“空类”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="580af-483">将类命名为“TodoItem”，然后单击“新建”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="580af-484">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="580af-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="580af-485">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="580af-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="580af-486">模型类可位于项目的任意位置，但按照惯例会使用 Models\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="580af-487">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="580af-487">Add a database context</span></span>

<span data-ttu-id="580af-488">数据库上下文是为数据模型协调 Entity Framework 功能的主类\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="580af-489">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="580af-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-491">右键单击“Models”\*\* 文件夹，然后选择“添加”\*\*\*\* > “类”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="580af-492">将类命名为 TodoContext，然后单击“添加”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="580af-493">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="580af-494">将 `TodoContext` 类添加到“Models”\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="580af-495">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="580af-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="580af-496">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="580af-496">Register the database context</span></span>

<span data-ttu-id="580af-497">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="580af-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="580af-498">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="580af-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="580af-499">使用以下突出显示的代码更新 Startup.cs\*\*：</span><span class="sxs-lookup"><span data-stu-id="580af-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="580af-500">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="580af-500">The preceding code:</span></span>

* <span data-ttu-id="580af-501">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="580af-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="580af-502">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="580af-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="580af-503">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="580af-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="580af-504">添加控制器</span><span class="sxs-lookup"><span data-stu-id="580af-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-506">右键单击 Controllers\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="580af-507">选择“添加”>“新项” \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="580af-508">在“添加新项”对话框中，选择“API 控制器类”模板\*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="580af-509">将类命名为 TodoController，然后选择“添加”\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="580af-511">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="580af-512">在“控制器”文件夹中，创建名为 `TodoController` 的类\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="580af-513">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="580af-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="580af-514">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="580af-514">The preceding code:</span></span>

* <span data-ttu-id="580af-515">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="580af-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="580af-516">使用 [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="580af-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="580af-517">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="580af-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="580af-518">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="580af-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="580af-519">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="580af-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="580af-520">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="580af-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="580af-521">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="580af-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="580af-522">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="580af-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="580af-523">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="580af-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="580af-524">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="580af-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="580af-525">添加 Get 方法</span><span class="sxs-lookup"><span data-stu-id="580af-525">Add Get methods</span></span>

<span data-ttu-id="580af-526">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="580af-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="580af-527">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="580af-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="580af-528">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="580af-528">Stop the app if it's still running.</span></span> <span data-ttu-id="580af-529">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="580af-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="580af-530">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="580af-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="580af-531">例如：</span><span class="sxs-lookup"><span data-stu-id="580af-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="580af-532">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="580af-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="580af-533">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="580af-533">Routing and URL paths</span></span>

<span data-ttu-id="580af-534">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="580af-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="580af-535">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="580af-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="580af-536">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="580af-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="580af-537">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="580af-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="580af-538">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="580af-539">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="580af-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="580af-540">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="580af-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="580af-541">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="580af-541">This sample doesn't use a template.</span></span> <span data-ttu-id="580af-542">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="580af-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="580af-543">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="580af-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="580af-544">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="580af-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="580af-545">返回值</span><span class="sxs-lookup"><span data-stu-id="580af-545">Return values</span></span>

<span data-ttu-id="580af-546">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="580af-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="580af-547">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="580af-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="580af-548">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="580af-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="580af-549">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="580af-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="580af-550">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="580af-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="580af-551">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="580af-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="580af-552">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="580af-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="580af-553">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="580af-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="580af-554">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="580af-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="580af-555">测试 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="580af-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="580af-556">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="580af-557">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="580af-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="580af-558">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="580af-558">Start the web app.</span></span>
* <span data-ttu-id="580af-559">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="580af-559">Start Postman.</span></span>
* <span data-ttu-id="580af-560">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="580af-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="580af-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="580af-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="580af-562">在“文件”\*\*\*\*>“设置”（“常规”\*\*\*\* \*\*\*\* 选项卡）中，禁用“SSL 证书验证”。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="580af-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="580af-563">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="580af-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="580af-564">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”\*\*\*\* \*\*\*\* \*\*\*\* \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="580af-565">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="580af-566">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="580af-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="580af-567">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="580af-567">Create a new request.</span></span>
  * <span data-ttu-id="580af-568">将 HTTP 方法设置为“GET”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="580af-569">将请求 URL 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="580af-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="580af-570">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="580af-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="580af-571">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="580af-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="580af-572">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-572">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="580af-574">添加创建方法</span><span class="sxs-lookup"><span data-stu-id="580af-574">Add a Create method</span></span>

<span data-ttu-id="580af-575">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法\*\*：</span><span class="sxs-lookup"><span data-stu-id="580af-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="580af-576">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="580af-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="580af-577">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="580af-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="580af-578">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="580af-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="580af-579">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="580af-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="580af-580">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="580af-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="580af-581">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="580af-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="580af-582">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="580af-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="580af-583">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="580af-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="580af-584">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="580af-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="580af-585">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="580af-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="580af-586">测试 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="580af-587">生成项目。</span><span class="sxs-lookup"><span data-stu-id="580af-587">Build the project.</span></span>
* <span data-ttu-id="580af-588">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="580af-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="580af-589">选择“正文”选项卡\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="580af-590">选择“原始”单选按钮\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="580af-591">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="580af-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="580af-592">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="580af-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="580af-593">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-593">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="580af-595">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="580af-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="580af-596">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="580af-596">Test the location header URI</span></span>

* <span data-ttu-id="580af-597">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="580af-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="580af-598">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="580af-598">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="580af-600">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="580af-600">Set the method to GET.</span></span>
* <span data-ttu-id="580af-601">粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）。</span><span class="sxs-lookup"><span data-stu-id="580af-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="580af-602">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="580af-603">添加 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="580af-604">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="580af-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="580af-605">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="580af-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="580af-606">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="580af-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="580af-607">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="580af-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="580af-608">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="580af-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="580af-609">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="580af-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="580af-610">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="580af-611">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="580af-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="580af-612">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="580af-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="580af-613">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="580af-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="580af-614">更新 id = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="580af-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="580af-615">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="580af-615">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="580af-617">添加 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="580af-618">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="580af-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="580af-619">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="580af-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="580af-620">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="580af-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="580af-621">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="580af-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="580af-622">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="580af-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="580af-623">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="580af-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="580af-624">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="580af-624">Select **Send**.</span></span>

<span data-ttu-id="580af-625">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="580af-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="580af-626">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="580af-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="580af-627">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="580af-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="580af-628">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="580af-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="580af-629">jQuery 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="580af-629">jQuery initiates the request.</span></span> <span data-ttu-id="580af-630">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="580af-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="580af-631">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)\*\*：</span><span class="sxs-lookup"><span data-stu-id="580af-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="580af-632">在项目目录中创建 wwwroot\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="580af-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="580af-633">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录\*\* \*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="580af-634">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="580af-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="580af-635">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录\*\* \*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="580af-636">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="580af-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="580af-637">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="580af-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="580af-638">打开 Properties\launchSettings.json\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="580af-639">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用\*\*。</span><span class="sxs-lookup"><span data-stu-id="580af-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="580af-640">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="580af-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="580af-641">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="580af-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="580af-642">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="580af-642">Get a list of to-do items</span></span>

<span data-ttu-id="580af-643">jQuery 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="580af-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="580af-644">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="580af-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="580af-645">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="580af-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="580af-646">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-646">Add a to-do item</span></span>

<span data-ttu-id="580af-647">jQuery 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="580af-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="580af-648">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="580af-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="580af-649">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="580af-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="580af-650">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="580af-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="580af-651">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-651">Update a to-do item</span></span>

<span data-ttu-id="580af-652">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="580af-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="580af-653">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="580af-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="580af-654">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="580af-654">Delete a to-do item</span></span>

<span data-ttu-id="580af-655">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="580af-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="580af-656">向 Web API 添加身份验证支持</span><span class="sxs-lookup"><span data-stu-id="580af-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="580af-657">其他资源</span><span class="sxs-lookup"><span data-stu-id="580af-657">Additional resources</span></span>

<span data-ttu-id="580af-658">[查看或下载本教程的示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="580af-658">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="580af-659">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="580af-659">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="580af-660">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="580af-660">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="580af-661">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="580af-661">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
