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
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767234"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="27091-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="27091-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="27091-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="27091-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="27091-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="27091-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="27091-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="27091-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="27091-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="27091-107">Create a web API project.</span></span>
> * <span data-ttu-id="27091-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="27091-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="27091-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="27091-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="27091-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="27091-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="27091-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-111">Call the web API with Postman.</span></span>

<span data-ttu-id="27091-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="27091-113">概述</span><span class="sxs-lookup"><span data-stu-id="27091-113">Overview</span></span>

<span data-ttu-id="27091-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="27091-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="27091-115">API</span><span class="sxs-lookup"><span data-stu-id="27091-115">API</span></span> | <span data-ttu-id="27091-116">描述</span><span class="sxs-lookup"><span data-stu-id="27091-116">Description</span></span> | <span data-ttu-id="27091-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="27091-117">Request body</span></span> | <span data-ttu-id="27091-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="27091-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="27091-119">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-119">Get all to-do items</span></span> | <span data-ttu-id="27091-120">None</span><span class="sxs-lookup"><span data-stu-id="27091-120">None</span></span> | <span data-ttu-id="27091-121">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="27091-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="27091-122">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="27091-122">Get an item by ID</span></span> | <span data-ttu-id="27091-123">None</span><span class="sxs-lookup"><span data-stu-id="27091-123">None</span></span> | <span data-ttu-id="27091-124">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="27091-125">添加新项</span><span class="sxs-lookup"><span data-stu-id="27091-125">Add a new item</span></span> | <span data-ttu-id="27091-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-126">To-do item</span></span> | <span data-ttu-id="27091-127">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="27091-128">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="27091-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="27091-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-129">To-do item</span></span> | <span data-ttu-id="27091-130">None</span><span class="sxs-lookup"><span data-stu-id="27091-130">None</span></span> |
|<span data-ttu-id="27091-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="27091-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="27091-132">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="27091-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="27091-133">None</span><span class="sxs-lookup"><span data-stu-id="27091-133">None</span></span> | <span data-ttu-id="27091-134">None</span><span class="sxs-lookup"><span data-stu-id="27091-134">None</span></span>|

<span data-ttu-id="27091-135">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="27091-135">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="27091-141">先决条件</span><span class="sxs-lookup"><span data-stu-id="27091-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="27091-145">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="27091-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-147">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="27091-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="27091-148">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="27091-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="27091-149">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="27091-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="27091-150">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”    。</span><span class="sxs-lookup"><span data-stu-id="27091-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="27091-151">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="27091-151">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27091-154">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="27091-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="27091-155">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="27091-156">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="27091-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="27091-157">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="27091-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="27091-158">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="27091-158">The preceding commands:</span></span>

  * <span data-ttu-id="27091-159">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="27091-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="27091-160">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="27091-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27091-162">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="27091-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="27091-164">选择“.NET Core”  >“应用”  >“API”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="27091-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="27091-166">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架选择为\*“.NET Core 3.1”    。</span><span class="sxs-lookup"><span data-stu-id="27091-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="27091-167">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="27091-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="27091-169">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="27091-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="27091-170">测试 API</span><span class="sxs-lookup"><span data-stu-id="27091-170">Test the API</span></span>

<span data-ttu-id="27091-171">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="27091-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="27091-172">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="27091-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="27091-174">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="27091-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="27091-175">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="27091-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="27091-176">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="27091-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="27091-177">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="27091-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="27091-179">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="27091-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="27091-180">在浏览器中，转到以下 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="27091-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-181">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="27091-182">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="27091-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="27091-183">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="27091-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="27091-184">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="27091-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="27091-185">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="27091-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="27091-186">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="27091-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="27091-187">添加模型类</span><span class="sxs-lookup"><span data-stu-id="27091-187">Add a model class</span></span>

<span data-ttu-id="27091-188">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="27091-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="27091-189">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="27091-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-191">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="27091-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="27091-192">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="27091-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="27091-193">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="27091-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="27091-194">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="27091-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="27091-195">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="27091-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="27091-196">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="27091-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27091-198">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="27091-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="27091-199">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="27091-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27091-201">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="27091-201">Right-click the project.</span></span> <span data-ttu-id="27091-202">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="27091-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="27091-203">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="27091-203">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="27091-205">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  >“常规”  >“空类”  。</span><span class="sxs-lookup"><span data-stu-id="27091-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="27091-206">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="27091-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="27091-207">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="27091-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="27091-208">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="27091-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="27091-209">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="27091-210">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="27091-210">Add a database context</span></span>

<span data-ttu-id="27091-211">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="27091-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="27091-212">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="27091-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="27091-214">添加 Add Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="27091-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="27091-215">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”   。</span><span class="sxs-lookup"><span data-stu-id="27091-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="27091-216">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer   。</span><span class="sxs-lookup"><span data-stu-id="27091-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="27091-217">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”  。</span><span class="sxs-lookup"><span data-stu-id="27091-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="27091-218">选中右窗格中的“项目”复选框，然后选择“安装”   。</span><span class="sxs-lookup"><span data-stu-id="27091-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="27091-219">使用上述说明添加 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="27091-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="27091-221">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="27091-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="27091-222">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="27091-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="27091-223">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="27091-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="27091-224">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="27091-225">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="27091-226">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="27091-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="27091-227">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="27091-227">Register the database context</span></span>

<span data-ttu-id="27091-228">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="27091-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="27091-229">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="27091-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="27091-230">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="27091-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="27091-231">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="27091-231">The preceding code:</span></span>

* <span data-ttu-id="27091-232">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="27091-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="27091-233">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="27091-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="27091-234">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="27091-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="27091-235">构建控制器</span><span class="sxs-lookup"><span data-stu-id="27091-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-237">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="27091-238">选择“添加”>“新建构建项”   。</span><span class="sxs-lookup"><span data-stu-id="27091-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="27091-239">选择“其操作使用实体框架的 API 控制器”，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="27091-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="27091-240">在“添加其操作使用实体框架的 API 控制器”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="27091-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="27091-241">在“模型类”中选择“TodoItem (TodoApi.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="27091-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="27091-242">在“数据上下文类”中选择“TodoContext (TodoAPI.Models)”   。</span><span class="sxs-lookup"><span data-stu-id="27091-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="27091-243">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="27091-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="27091-244">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="27091-245">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="27091-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="27091-246">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="27091-246">The preceding commands:</span></span>

* <span data-ttu-id="27091-247">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="27091-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="27091-248">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="27091-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="27091-249">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="27091-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="27091-250">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="27091-250">The generated code:</span></span>

* <span data-ttu-id="27091-251">使用 [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="27091-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="27091-252">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="27091-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="27091-253">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="27091-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="27091-254">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="27091-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="27091-255">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="27091-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="27091-256">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="27091-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="27091-257">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="27091-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="27091-258">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="27091-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="27091-259">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="27091-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="27091-260">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="27091-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="27091-261">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="27091-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="27091-262">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="27091-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="27091-263">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="27091-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="27091-264">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="27091-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="27091-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="27091-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="27091-266">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="27091-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="27091-267">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="27091-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="27091-268">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="27091-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="27091-269">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="27091-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="27091-270">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="27091-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="27091-271">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="27091-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="27091-272">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="27091-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="27091-273">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="27091-273">Install Postman</span></span>

<span data-ttu-id="27091-274">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="27091-275">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="27091-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="27091-276">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="27091-276">Start the web app.</span></span>
* <span data-ttu-id="27091-277">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="27091-277">Start Postman.</span></span>
* <span data-ttu-id="27091-278">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="27091-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="27091-279">在“文件”  >“设置”（“常规”   选项卡）中，禁用“SSL 证书验证”。 </span><span class="sxs-lookup"><span data-stu-id="27091-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="27091-280">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="27091-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="27091-281">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="27091-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="27091-282">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="27091-282">Create a new request.</span></span>
* <span data-ttu-id="27091-283">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="27091-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="27091-284">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="27091-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="27091-285">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="27091-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="27091-286">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="27091-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="27091-287">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="27091-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="27091-288">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-288">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="27091-290">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="27091-290">Test the location header URI</span></span>

* <span data-ttu-id="27091-291">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="27091-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="27091-292">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="27091-292">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="27091-294">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="27091-294">Set the method to GET.</span></span>
* <span data-ttu-id="27091-295">粘贴 URI（例如，`https://localhost:5001/api/TodoItems/1`）。</span><span class="sxs-lookup"><span data-stu-id="27091-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="27091-296">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="27091-297">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="27091-297">Examine the GET methods</span></span>

<span data-ttu-id="27091-298">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="27091-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="27091-299">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="27091-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="27091-300">例如：</span><span class="sxs-lookup"><span data-stu-id="27091-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="27091-301">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="27091-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="27091-302">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="27091-302">Test Get with Postman</span></span>

* <span data-ttu-id="27091-303">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="27091-303">Create a new request.</span></span>
* <span data-ttu-id="27091-304">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="27091-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="27091-305">将请求 URL 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="27091-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="27091-306">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="27091-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="27091-307">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="27091-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="27091-308">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-308">Select **Send**.</span></span>

<span data-ttu-id="27091-309">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="27091-309">This app uses an in-memory database.</span></span> <span data-ttu-id="27091-310">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="27091-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="27091-311">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="27091-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="27091-312">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="27091-312">Routing and URL paths</span></span>

<span data-ttu-id="27091-313">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="27091-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="27091-314">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="27091-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="27091-315">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="27091-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="27091-316">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="27091-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="27091-317">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”  。</span><span class="sxs-lookup"><span data-stu-id="27091-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="27091-318">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="27091-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="27091-319">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="27091-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="27091-320">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="27091-320">This sample doesn't use a template.</span></span> <span data-ttu-id="27091-321">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="27091-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="27091-322">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="27091-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="27091-323">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="27091-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="27091-324">返回值</span><span class="sxs-lookup"><span data-stu-id="27091-324">Return values</span></span>

<span data-ttu-id="27091-325">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="27091-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="27091-326">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="27091-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="27091-327">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="27091-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="27091-328">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="27091-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="27091-329">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="27091-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="27091-330">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="27091-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="27091-331">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="27091-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="27091-332">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="27091-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="27091-333">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="27091-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="27091-334">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-334">The PutTodoItem method</span></span>

<span data-ttu-id="27091-335">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="27091-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="27091-336">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="27091-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="27091-337">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="27091-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="27091-338">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="27091-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="27091-339">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="27091-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="27091-340">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="27091-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="27091-341">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="27091-342">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="27091-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="27091-343">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="27091-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="27091-344">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="27091-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="27091-345">更新 ID = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="27091-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="27091-346">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="27091-346">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="27091-348">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="27091-349">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="27091-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="27091-350">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="27091-351">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="27091-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="27091-352">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="27091-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="27091-353">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="27091-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="27091-354">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="27091-355">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="27091-355">Prevent over-posting</span></span>

<span data-ttu-id="27091-356">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="27091-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="27091-357">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="27091-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="27091-358">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="27091-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="27091-359">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="27091-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="27091-360">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="27091-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="27091-361">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="27091-361">A DTO may be used to:</span></span>

* <span data-ttu-id="27091-362">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="27091-362">Prevent over-posting.</span></span>
* <span data-ttu-id="27091-363">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="27091-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="27091-364">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="27091-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="27091-365">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="27091-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="27091-366">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="27091-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="27091-367">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="27091-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="27091-368">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="27091-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="27091-369">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="27091-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="27091-370">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="27091-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="27091-371">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="27091-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="27091-372">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="27091-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="27091-373">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="27091-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="27091-374">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="27091-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="27091-375">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="27091-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="27091-376">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="27091-376">Create a web API project.</span></span>
> * <span data-ttu-id="27091-377">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="27091-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="27091-378">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="27091-378">Add a controller.</span></span>
> * <span data-ttu-id="27091-379">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="27091-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="27091-380">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="27091-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="27091-381">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="27091-381">Specify return values.</span></span>
> * <span data-ttu-id="27091-382">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="27091-383">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="27091-384">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="27091-385">概述</span><span class="sxs-lookup"><span data-stu-id="27091-385">Overview</span></span>

<span data-ttu-id="27091-386">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="27091-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="27091-387">API</span><span class="sxs-lookup"><span data-stu-id="27091-387">API</span></span> | <span data-ttu-id="27091-388">描述</span><span class="sxs-lookup"><span data-stu-id="27091-388">Description</span></span> | <span data-ttu-id="27091-389">请求正文</span><span class="sxs-lookup"><span data-stu-id="27091-389">Request body</span></span> | <span data-ttu-id="27091-390">响应正文</span><span class="sxs-lookup"><span data-stu-id="27091-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="27091-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="27091-391">GET /api/TodoItems</span></span> | <span data-ttu-id="27091-392">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-392">Get all to-do items</span></span> | <span data-ttu-id="27091-393">None</span><span class="sxs-lookup"><span data-stu-id="27091-393">None</span></span> | <span data-ttu-id="27091-394">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="27091-394">Array of to-do items</span></span>|
|<span data-ttu-id="27091-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="27091-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="27091-396">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="27091-396">Get an item by ID</span></span> | <span data-ttu-id="27091-397">None</span><span class="sxs-lookup"><span data-stu-id="27091-397">None</span></span> | <span data-ttu-id="27091-398">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-398">To-do item</span></span>|
|<span data-ttu-id="27091-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="27091-399">POST /api/TodoItems</span></span> | <span data-ttu-id="27091-400">添加新项</span><span class="sxs-lookup"><span data-stu-id="27091-400">Add a new item</span></span> | <span data-ttu-id="27091-401">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-401">To-do item</span></span> | <span data-ttu-id="27091-402">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-402">To-do item</span></span> |
|<span data-ttu-id="27091-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="27091-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="27091-404">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="27091-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="27091-405">待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-405">To-do item</span></span> | <span data-ttu-id="27091-406">None</span><span class="sxs-lookup"><span data-stu-id="27091-406">None</span></span> |
|<span data-ttu-id="27091-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="27091-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="27091-408">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="27091-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="27091-409">None</span><span class="sxs-lookup"><span data-stu-id="27091-409">None</span></span> | <span data-ttu-id="27091-410">None</span><span class="sxs-lookup"><span data-stu-id="27091-410">None</span></span>|

<span data-ttu-id="27091-411">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="27091-411">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="27091-417">先决条件</span><span class="sxs-lookup"><span data-stu-id="27091-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-420">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="27091-421">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="27091-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-423">从“文件”菜单中选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="27091-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="27091-424">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="27091-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="27091-425">将项目命名为 TodoApi，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="27091-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="27091-426">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”    。</span><span class="sxs-lookup"><span data-stu-id="27091-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="27091-427">选择“API”模板，然后单击“创建”   。</span><span class="sxs-lookup"><span data-stu-id="27091-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="27091-428">请不要选择“启用 Docker 支持”   。</span><span class="sxs-lookup"><span data-stu-id="27091-428">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27091-431">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="27091-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="27091-432">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="27091-433">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="27091-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="27091-434">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="27091-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="27091-435">当对话框询问是否要将所需资产添加到项目时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="27091-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27091-437">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="27091-437">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="27091-439">选择“.NET Core”  >“应用”  >“API”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="27091-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS“新建项目”对话框](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="27091-441">在“配置新的 ASP.NET Core Web API”  对话框中，接受默认的 \*.NET Core 2.2  “目标框架”  。</span><span class="sxs-lookup"><span data-stu-id="27091-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="27091-442">输入“TodoApi”  作为“项目名称”  ，然后选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="27091-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="27091-444">测试 API</span><span class="sxs-lookup"><span data-stu-id="27091-444">Test the API</span></span>

<span data-ttu-id="27091-445">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="27091-445">The project template creates a `values` API.</span></span> <span data-ttu-id="27091-446">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="27091-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="27091-448">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="27091-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="27091-449">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="27091-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="27091-450">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="27091-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="27091-451">在接下来出现的“安全警告”  对话框中，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="27091-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="27091-453">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="27091-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="27091-454">在浏览器中，转到以下 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="27091-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-455">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="27091-456">选择“运行”   > “开始调试”  以启动应用。</span><span class="sxs-lookup"><span data-stu-id="27091-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="27091-457">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="27091-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="27091-458">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="27091-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="27091-459">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="27091-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="27091-460">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="27091-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="27091-461">添加模型类</span><span class="sxs-lookup"><span data-stu-id="27091-461">Add a model class</span></span>

<span data-ttu-id="27091-462">模型  是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="27091-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="27091-463">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="27091-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-465">在“解决方案资源管理器”  中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="27091-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="27091-466">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="27091-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="27091-467">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="27091-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="27091-468">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="27091-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="27091-469">将类命名为 TodoItem，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="27091-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="27091-470">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="27091-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="27091-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27091-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27091-472">添加名为“Models”的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="27091-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="27091-473">使用以下代码将 `TodoItem` 类添加到 Models  文件夹：</span><span class="sxs-lookup"><span data-stu-id="27091-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="27091-474">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27091-475">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="27091-475">Right-click the project.</span></span> <span data-ttu-id="27091-476">选择“添加”   > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="27091-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="27091-477">将文件夹命名为“Models”  。</span><span class="sxs-lookup"><span data-stu-id="27091-477">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="27091-479">右键单击“Models”  文件夹，然后选择“添加”  >“新建文件”  >“常规”  >“空类”  。</span><span class="sxs-lookup"><span data-stu-id="27091-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="27091-480">将类命名为“TodoItem”，然后单击“新建”   。</span><span class="sxs-lookup"><span data-stu-id="27091-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="27091-481">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="27091-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="27091-482">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="27091-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="27091-483">模型类可位于项目的任意位置，但按照惯例会使用 Models  文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="27091-484">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="27091-484">Add a database context</span></span>

<span data-ttu-id="27091-485">数据库上下文是为数据模型协调 Entity Framework 功能的主类  。</span><span class="sxs-lookup"><span data-stu-id="27091-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="27091-486">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="27091-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-488">右键单击“Models”  文件夹，然后选择“添加”   > “类”  。</span><span class="sxs-lookup"><span data-stu-id="27091-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="27091-489">将类命名为 TodoContext，然后单击“添加”   。</span><span class="sxs-lookup"><span data-stu-id="27091-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="27091-490">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="27091-491">将 `TodoContext` 类添加到“Models”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="27091-492">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="27091-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="27091-493">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="27091-493">Register the database context</span></span>

<span data-ttu-id="27091-494">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="27091-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="27091-495">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="27091-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="27091-496">使用以下突出显示的代码更新 Startup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="27091-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="27091-497">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="27091-497">The preceding code:</span></span>

* <span data-ttu-id="27091-498">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="27091-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="27091-499">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="27091-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="27091-500">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="27091-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="27091-501">添加控制器</span><span class="sxs-lookup"><span data-stu-id="27091-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-503">右键单击 Controllers  文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="27091-504">选择“添加”>“新项”   。</span><span class="sxs-lookup"><span data-stu-id="27091-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="27091-505">在“添加新项”对话框中，选择“API 控制器类”模板   。</span><span class="sxs-lookup"><span data-stu-id="27091-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="27091-506">将类命名为 TodoController，然后选择“添加”   。</span><span class="sxs-lookup"><span data-stu-id="27091-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="27091-508">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="27091-509">在“控制器”文件夹中，创建名为 `TodoController` 的类  。</span><span class="sxs-lookup"><span data-stu-id="27091-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="27091-510">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="27091-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="27091-511">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="27091-511">The preceding code:</span></span>

* <span data-ttu-id="27091-512">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="27091-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="27091-513">使用 [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="27091-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="27091-514">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="27091-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="27091-515">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="27091-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="27091-516">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="27091-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="27091-517">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="27091-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="27091-518">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="27091-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="27091-519">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="27091-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="27091-520">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="27091-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="27091-521">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="27091-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="27091-522">添加 Get 方法</span><span class="sxs-lookup"><span data-stu-id="27091-522">Add Get methods</span></span>

<span data-ttu-id="27091-523">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="27091-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="27091-524">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="27091-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="27091-525">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="27091-525">Stop the app if it's still running.</span></span> <span data-ttu-id="27091-526">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="27091-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="27091-527">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="27091-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="27091-528">例如：</span><span class="sxs-lookup"><span data-stu-id="27091-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="27091-529">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="27091-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="27091-530">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="27091-530">Routing and URL paths</span></span>

<span data-ttu-id="27091-531">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="27091-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="27091-532">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="27091-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="27091-533">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="27091-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="27091-534">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="27091-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="27091-535">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”  。</span><span class="sxs-lookup"><span data-stu-id="27091-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="27091-536">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="27091-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="27091-537">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="27091-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="27091-538">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="27091-538">This sample doesn't use a template.</span></span> <span data-ttu-id="27091-539">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="27091-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="27091-540">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="27091-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="27091-541">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="27091-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="27091-542">返回值</span><span class="sxs-lookup"><span data-stu-id="27091-542">Return values</span></span>

<span data-ttu-id="27091-543">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="27091-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="27091-544">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="27091-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="27091-545">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="27091-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="27091-546">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="27091-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="27091-547">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="27091-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="27091-548">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="27091-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="27091-549">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 错误代码。</span><span class="sxs-lookup"><span data-stu-id="27091-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="27091-550">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="27091-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="27091-551">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="27091-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="27091-552">测试 GetTodoItems 方法</span><span class="sxs-lookup"><span data-stu-id="27091-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="27091-553">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="27091-554">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="27091-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="27091-555">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="27091-555">Start the web app.</span></span>
* <span data-ttu-id="27091-556">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="27091-556">Start Postman.</span></span>
* <span data-ttu-id="27091-557">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="27091-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="27091-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27091-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27091-559">在“文件”  >“设置”（“常规”   选项卡）中，禁用“SSL 证书验证”。 </span><span class="sxs-lookup"><span data-stu-id="27091-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="27091-560">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="27091-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="27091-561">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”     。</span><span class="sxs-lookup"><span data-stu-id="27091-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="27091-562">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”  。</span><span class="sxs-lookup"><span data-stu-id="27091-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="27091-563">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="27091-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="27091-564">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="27091-564">Create a new request.</span></span>
  * <span data-ttu-id="27091-565">将 HTTP 方法设置为“GET”  。</span><span class="sxs-lookup"><span data-stu-id="27091-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="27091-566">将请求 URL 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="27091-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="27091-567">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="27091-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="27091-568">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="27091-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="27091-569">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-569">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="27091-571">添加创建方法</span><span class="sxs-lookup"><span data-stu-id="27091-571">Add a Create method</span></span>

<span data-ttu-id="27091-572">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="27091-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="27091-573">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="27091-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="27091-574">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="27091-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="27091-575">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="27091-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="27091-576">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="27091-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="27091-577">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="27091-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="27091-578">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="27091-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="27091-579">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="27091-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="27091-580">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="27091-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="27091-581">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="27091-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="27091-582">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="27091-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="27091-583">测试 PostTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="27091-584">生成项目。</span><span class="sxs-lookup"><span data-stu-id="27091-584">Build the project.</span></span>
* <span data-ttu-id="27091-585">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="27091-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="27091-586">选择“正文”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="27091-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="27091-587">选择“原始”单选按钮  。</span><span class="sxs-lookup"><span data-stu-id="27091-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="27091-588">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="27091-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="27091-589">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="27091-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="27091-590">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-590">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="27091-592">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="27091-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="27091-593">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="27091-593">Test the location header URI</span></span>

* <span data-ttu-id="27091-594">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="27091-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="27091-595">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="27091-595">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="27091-597">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="27091-597">Set the method to GET.</span></span>
* <span data-ttu-id="27091-598">粘贴 URI（例如，`https://localhost:5001/api/Todo/2`）。</span><span class="sxs-lookup"><span data-stu-id="27091-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="27091-599">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="27091-600">添加 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="27091-601">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="27091-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="27091-602">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="27091-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="27091-603">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="27091-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="27091-604">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="27091-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="27091-605">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="27091-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="27091-606">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="27091-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="27091-607">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="27091-608">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="27091-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="27091-609">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="27091-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="27091-610">调用 GET，以确保在调用 PUT 之前数据库中存在项目。</span><span class="sxs-lookup"><span data-stu-id="27091-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="27091-611">更新 id = 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="27091-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="27091-612">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="27091-612">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="27091-614">添加 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="27091-615">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="27091-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="27091-616">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="27091-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="27091-617">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="27091-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="27091-618">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="27091-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="27091-619">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="27091-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="27091-620">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="27091-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="27091-621">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="27091-621">Select **Send**.</span></span>

<span data-ttu-id="27091-622">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="27091-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="27091-623">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="27091-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="27091-624">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="27091-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="27091-625">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="27091-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="27091-626">jQuery 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="27091-626">jQuery initiates the request.</span></span> <span data-ttu-id="27091-627">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="27091-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="27091-628">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)并[实现默认文件映射](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)  ：</span><span class="sxs-lookup"><span data-stu-id="27091-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="27091-629">在项目目录中创建 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="27091-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="27091-630">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="27091-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="27091-631">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="27091-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="27091-632">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录   。</span><span class="sxs-lookup"><span data-stu-id="27091-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="27091-633">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="27091-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="27091-634">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="27091-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="27091-635">打开 Properties\launchSettings.json  。</span><span class="sxs-lookup"><span data-stu-id="27091-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="27091-636">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用  。</span><span class="sxs-lookup"><span data-stu-id="27091-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="27091-637">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="27091-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="27091-638">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="27091-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="27091-639">获取待办事项的列表</span><span class="sxs-lookup"><span data-stu-id="27091-639">Get a list of to-do items</span></span>

<span data-ttu-id="27091-640">jQuery 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="27091-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="27091-641">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="27091-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="27091-642">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="27091-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="27091-643">添加待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-643">Add a to-do item</span></span>

<span data-ttu-id="27091-644">jQuery 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="27091-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="27091-645">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="27091-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="27091-646">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="27091-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="27091-647">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="27091-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="27091-648">更新待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-648">Update a to-do item</span></span>

<span data-ttu-id="27091-649">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="27091-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="27091-650">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="27091-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="27091-651">删除待办事项</span><span class="sxs-lookup"><span data-stu-id="27091-651">Delete a to-do item</span></span>

<span data-ttu-id="27091-652">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="27091-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="27091-653">向 Web API 添加身份验证支持</span><span class="sxs-lookup"><span data-stu-id="27091-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="27091-654">其他资源</span><span class="sxs-lookup"><span data-stu-id="27091-654">Additional resources</span></span>

<span data-ttu-id="27091-655">[查看或下载本教程的示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="27091-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="27091-656">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="27091-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="27091-657">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="27091-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="27091-658">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="27091-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
