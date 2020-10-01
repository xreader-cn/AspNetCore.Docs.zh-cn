---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 212d8a80bdc466479c34bc5fbd9c3261ca9d54c4
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393907"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="2c141-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="2c141-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="2c141-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="2c141-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="2c141-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="2c141-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2c141-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="2c141-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c141-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-107">Create a web API project.</span></span>
> * <span data-ttu-id="2c141-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="2c141-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2c141-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="2c141-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="2c141-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="2c141-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="2c141-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-111">Call the web API with Postman.</span></span>

<span data-ttu-id="2c141-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="2c141-113">概述</span><span class="sxs-lookup"><span data-stu-id="2c141-113">Overview</span></span>

<span data-ttu-id="2c141-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="2c141-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2c141-115">API</span><span class="sxs-lookup"><span data-stu-id="2c141-115">API</span></span> | <span data-ttu-id="2c141-116">描述</span><span class="sxs-lookup"><span data-stu-id="2c141-116">Description</span></span> | <span data-ttu-id="2c141-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="2c141-117">Request body</span></span> | <span data-ttu-id="2c141-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="2c141-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="2c141-119">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-119">Get all to-do items</span></span> | <span data-ttu-id="2c141-120">None</span><span class="sxs-lookup"><span data-stu-id="2c141-120">None</span></span> | <span data-ttu-id="2c141-121">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="2c141-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="2c141-122">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="2c141-122">Get an item by ID</span></span> | <span data-ttu-id="2c141-123">None</span><span class="sxs-lookup"><span data-stu-id="2c141-123">None</span></span> | <span data-ttu-id="2c141-124">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="2c141-125">添加新项</span><span class="sxs-lookup"><span data-stu-id="2c141-125">Add a new item</span></span> | <span data-ttu-id="2c141-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-126">To-do item</span></span> | <span data-ttu-id="2c141-127">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="2c141-128">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2c141-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-129">To-do item</span></span> | <span data-ttu-id="2c141-130">None</span><span class="sxs-lookup"><span data-stu-id="2c141-130">None</span></span> |
|<span data-ttu-id="2c141-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="2c141-132">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2c141-133">None</span><span class="sxs-lookup"><span data-stu-id="2c141-133">None</span></span> | <span data-ttu-id="2c141-134">None</span><span class="sxs-lookup"><span data-stu-id="2c141-134">None</span></span>|

<span data-ttu-id="2c141-135">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="2c141-135">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="2c141-141">先决条件</span><span class="sxs-lookup"><span data-stu-id="2c141-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="2c141-145">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="2c141-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-147">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="2c141-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2c141-148">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="2c141-149">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="2c141-150">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择 .NET Core 和 ASP.NET Core 5.0  。</span><span class="sxs-lookup"><span data-stu-id="2c141-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="2c141-151">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-151">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c141-154">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="2c141-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2c141-155">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2c141-156">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="2c141-157">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="2c141-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="2c141-158">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-158">The preceding commands:</span></span>

  * <span data-ttu-id="2c141-159">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="2c141-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="2c141-160">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2c141-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c141-162">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2c141-164">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="2c141-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="2c141-165">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="2c141-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="2c141-167">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 3.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="2c141-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="2c141-168">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="2c141-168">Select **Next**.</span></span>

* <span data-ttu-id="2c141-169">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="2c141-171">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="2c141-172">测试项目</span><span class="sxs-lookup"><span data-stu-id="2c141-172">Test the project</span></span>

<span data-ttu-id="2c141-173">项目模板创建了一个支持 [Swagger](xref:tutorials/web-api-help-pages-using-swagger) 的 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="2c141-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c141-175">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2c141-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2c141-176">Visual Studio 将启动：</span><span class="sxs-lookup"><span data-stu-id="2c141-176">Visual Studio launches:</span></span>

* <span data-ttu-id="2c141-177">IIS Express Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="2c141-177">The IIS Express web server.</span></span>
* <span data-ttu-id="2c141-178">默认浏览器，并导航到 `https://localhost:<port>/https://localhost:5001/swagger/index.html`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="2c141-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="2c141-180">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2c141-181">在浏览器中，转到以下 URL：[https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="2c141-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c141-183">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2c141-184">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="2c141-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2c141-185">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="2c141-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2c141-186">将 `/swagger` 追加到 URL（将 URL 更改为 `https://localhost:<port>/swagger`）。</span><span class="sxs-lookup"><span data-stu-id="2c141-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="2c141-187">随即显示 Swagger 页面 `/swagger/index.html`。</span><span class="sxs-lookup"><span data-stu-id="2c141-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="2c141-188">选择 GET  > “试用” > “执行”  。</span><span class="sxs-lookup"><span data-stu-id="2c141-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="2c141-189">页面将显示：</span><span class="sxs-lookup"><span data-stu-id="2c141-189">The page displays:</span></span>

* <span data-ttu-id="2c141-190">用于测试 WeatherForecast API 的 [Curl](https://curl.haxx.se/) 命令。</span><span class="sxs-lookup"><span data-stu-id="2c141-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="2c141-191">用于测试 WeatherForecast API 的 URL。</span><span class="sxs-lookup"><span data-stu-id="2c141-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="2c141-192">响应代码、正文和标头。</span><span class="sxs-lookup"><span data-stu-id="2c141-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="2c141-193">包含媒体类型、示例值和架构的下拉列表框。</span><span class="sxs-lookup"><span data-stu-id="2c141-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="2c141-194">Swagger 用于为 Web API 生成有用的文档和帮助页面。</span><span class="sxs-lookup"><span data-stu-id="2c141-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="2c141-195">本教程重点介绍如何创建 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="2c141-196">有关 Swagger 的详细信息，请参阅 <xref:tutorials/web-api-help-pages-using-swagger>。</span><span class="sxs-lookup"><span data-stu-id="2c141-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="2c141-197">将请求 URL 复制粘贴到浏览器中：`https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="2c141-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="2c141-198">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="2c141-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="2c141-199">更新 launchUrl</span><span class="sxs-lookup"><span data-stu-id="2c141-199">Update the launchUrl</span></span>

<span data-ttu-id="2c141-200">在 Properties\launchSettings.json 中，将 `launchUrl` 从 `"swagger"` 更新为 `"api/TodoItems"`：</span><span class="sxs-lookup"><span data-stu-id="2c141-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="2c141-201">由于已删除 Swagger，上述标记会将启动的 URL 更改为添加到以下部分的控制器的 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="2c141-202">添加模型类</span><span class="sxs-lookup"><span data-stu-id="2c141-202">Add a model class</span></span>

<span data-ttu-id="2c141-203">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="2c141-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2c141-204">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="2c141-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-206">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="2c141-207">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="2c141-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2c141-208">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="2c141-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="2c141-209">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2c141-210">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="2c141-211">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="2c141-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c141-213">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="2c141-214">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="2c141-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c141-216">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-216">Right-click the project.</span></span> <span data-ttu-id="2c141-217">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="2c141-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2c141-218">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="2c141-218">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2c141-220">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="2c141-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="2c141-221">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="2c141-222">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="2c141-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="2c141-223">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="2c141-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2c141-224">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="2c141-225">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="2c141-225">Add a database context</span></span>

<span data-ttu-id="2c141-226">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="2c141-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2c141-227">此类由 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="2c141-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="2c141-229">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="2c141-229">Add NuGet packages</span></span>

* <span data-ttu-id="2c141-230">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="2c141-231">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.。</span><span class="sxs-lookup"><span data-stu-id="2c141-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="2c141-232">EntityFrameworkCore.SqlServer。</span><span class="sxs-lookup"><span data-stu-id="2c141-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="2c141-233">选中“包括预发行版”复选框，以使 5.0 RC 版本可用。</span><span class="sxs-lookup"><span data-stu-id="2c141-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="2c141-234">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”。</span><span class="sxs-lookup"><span data-stu-id="2c141-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="2c141-235">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-235">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="2c141-236">使用前面的说明添加 Microsoft.EntityFrameworkCore.InMemory NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2c141-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="2c141-237">![NuGet 包管理器](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="2c141-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="2c141-238">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="2c141-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="2c141-239">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-239">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2c141-240">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-240">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2c141-241">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2c141-242">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-242">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="2c141-243">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="2c141-244">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="2c141-244">Register the database context</span></span>

<span data-ttu-id="2c141-245">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="2c141-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2c141-246">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="2c141-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="2c141-247">使用以下代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="2c141-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="2c141-248">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-248">The preceding code:</span></span>

* <span data-ttu-id="2c141-249">删除 Swagger 调用。</span><span class="sxs-lookup"><span data-stu-id="2c141-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="2c141-250">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="2c141-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2c141-251">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="2c141-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2c141-252">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="2c141-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="2c141-253">构建控制器</span><span class="sxs-lookup"><span data-stu-id="2c141-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-255">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2c141-256">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-256">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2c141-257">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-257">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="2c141-258">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="2c141-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="2c141-259">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-259">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="2c141-260">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-260">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="2c141-261">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-261">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2c141-262">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2c141-263">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="2c141-264">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-264">The preceding commands:</span></span>

* <span data-ttu-id="2c141-265">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2c141-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="2c141-266">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="2c141-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="2c141-267">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="2c141-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="2c141-268">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-268">The generated code:</span></span>

* <span data-ttu-id="2c141-269">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="2c141-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2c141-270">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2c141-271">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="2c141-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2c141-272">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="2c141-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2c141-273">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="2c141-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="2c141-274">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="2c141-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="2c141-275">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="2c141-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="2c141-276">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="2c141-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="2c141-277">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="2c141-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="2c141-278">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="2c141-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="2c141-279">更新 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="2c141-280">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="2c141-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="2c141-281">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="2c141-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2c141-282">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="2c141-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2c141-283">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="2c141-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2c141-284"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="2c141-285">如果成功，将返回 [HTTP 201 状态代码](https://developer.mozilla.org/docs/Web/HTTP/Status/201)。</span><span class="sxs-lookup"><span data-stu-id="2c141-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="2c141-286">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="2c141-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2c141-287">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="2c141-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="2c141-288">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="2c141-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="2c141-289">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="2c141-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2c141-290">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="2c141-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2c141-291">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="2c141-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="2c141-292">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="2c141-292">Install Postman</span></span>

<span data-ttu-id="2c141-293">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2c141-294">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="2c141-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="2c141-295">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-295">Start the web app.</span></span>
* <span data-ttu-id="2c141-296">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="2c141-296">Start Postman.</span></span>
* <span data-ttu-id="2c141-297">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="2c141-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="2c141-298">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="2c141-298">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="2c141-299">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="2c141-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="2c141-300">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="2c141-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="2c141-301">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-301">Create a new request.</span></span>
* <span data-ttu-id="2c141-302">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="2c141-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2c141-303">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2c141-304">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2c141-305">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="2c141-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="2c141-306">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="2c141-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2c141-307">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="2c141-307">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="2c141-308">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="2c141-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2c141-309">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-309">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="2c141-311">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="2c141-311">Test the location header URI</span></span>

<span data-ttu-id="2c141-312">你可以在浏览器中测试位置标头 URI。</span><span class="sxs-lookup"><span data-stu-id="2c141-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="2c141-313">将位置标头 URI 复制粘贴到浏览器中。</span><span class="sxs-lookup"><span data-stu-id="2c141-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="2c141-314">若要在 Postman 中测试，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2c141-314">To test in Postman:</span></span>

* <span data-ttu-id="2c141-315">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="2c141-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2c141-316">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="2c141-316">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="2c141-318">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="2c141-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="2c141-319">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="2c141-320">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="2c141-321">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-321">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="2c141-322">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-322">Examine the GET methods</span></span>

<span data-ttu-id="2c141-323">实现了两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="2c141-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="2c141-324">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="2c141-325">例如：</span><span class="sxs-lookup"><span data-stu-id="2c141-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="2c141-326">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="2c141-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="2c141-327">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="2c141-327">Test Get with Postman</span></span>

* <span data-ttu-id="2c141-328">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-328">Create a new request.</span></span>
* <span data-ttu-id="2c141-329">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="2c141-329">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="2c141-330">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2c141-331">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2c141-332">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="2c141-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2c141-333">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-333">Select **Send**.</span></span>

<span data-ttu-id="2c141-334">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="2c141-334">This app uses an in-memory database.</span></span> <span data-ttu-id="2c141-335">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="2c141-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="2c141-336">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="2c141-337">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="2c141-337">Routing and URL paths</span></span>

<span data-ttu-id="2c141-338">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2c141-339">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="2c141-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2c141-340">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="2c141-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="2c141-341">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="2c141-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2c141-342">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="2c141-342">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="2c141-343">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2c141-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2c141-344">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="2c141-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2c141-345">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="2c141-345">This sample doesn't use a template.</span></span> <span data-ttu-id="2c141-346">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="2c141-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2c141-347">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="2c141-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2c141-348">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="2c141-349">返回值</span><span class="sxs-lookup"><span data-stu-id="2c141-349">Return values</span></span>

<span data-ttu-id="2c141-350">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="2c141-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2c141-351">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="2c141-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2c141-352">此返回类型的响应代码为 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)（假设没有未处理的异常）。</span><span class="sxs-lookup"><span data-stu-id="2c141-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2c141-353">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="2c141-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2c141-354">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2c141-355">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="2c141-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2c141-356">如果没有任何项与请求的 ID 匹配，该方法将返回 [404 状态](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2c141-357">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="2c141-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2c141-358">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="2c141-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="2c141-359">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-359">The PutTodoItem method</span></span>

<span data-ttu-id="2c141-360">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="2c141-361">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="2c141-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2c141-362">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="2c141-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2c141-363">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="2c141-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2c141-364">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="2c141-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2c141-365">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="2c141-366">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="2c141-367">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="2c141-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2c141-368">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="2c141-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2c141-369">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="2c141-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2c141-370">更新 ID 为 1 的待办事项并将其名称设置为 `"feed fish"`：</span><span class="sxs-lookup"><span data-stu-id="2c141-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2c141-371">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="2c141-371">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="2c141-373">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="2c141-374">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="2c141-375">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="2c141-376">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="2c141-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2c141-377">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="2c141-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2c141-378">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="2c141-379">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-379">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="2c141-380">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="2c141-380">Prevent over-posting</span></span>

<span data-ttu-id="2c141-381">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="2c141-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="2c141-382">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="2c141-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="2c141-383">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="2c141-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="2c141-384">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="2c141-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="2c141-385">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="2c141-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="2c141-386">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="2c141-386">A DTO may be used to:</span></span>

* <span data-ttu-id="2c141-387">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="2c141-387">Prevent over-posting.</span></span>
* <span data-ttu-id="2c141-388">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="2c141-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="2c141-389">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="2c141-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="2c141-390">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="2c141-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="2c141-391">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="2c141-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="2c141-392">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="2c141-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="2c141-393">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="2c141-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="2c141-394">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="2c141-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="2c141-395">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="2c141-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="2c141-396">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="2c141-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="2c141-397">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="2c141-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="2c141-398">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="2c141-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="2c141-399">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="2c141-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2c141-400">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="2c141-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c141-401">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-401">Create a web API project.</span></span>
> * <span data-ttu-id="2c141-402">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="2c141-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2c141-403">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="2c141-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="2c141-404">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="2c141-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="2c141-405">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-405">Call the web API with Postman.</span></span>

<span data-ttu-id="2c141-406">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="2c141-407">概述</span><span class="sxs-lookup"><span data-stu-id="2c141-407">Overview</span></span>

<span data-ttu-id="2c141-408">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="2c141-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2c141-409">API</span><span class="sxs-lookup"><span data-stu-id="2c141-409">API</span></span> | <span data-ttu-id="2c141-410">描述</span><span class="sxs-lookup"><span data-stu-id="2c141-410">Description</span></span> | <span data-ttu-id="2c141-411">请求正文</span><span class="sxs-lookup"><span data-stu-id="2c141-411">Request body</span></span> | <span data-ttu-id="2c141-412">响应正文</span><span class="sxs-lookup"><span data-stu-id="2c141-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="2c141-413">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-413">Get all to-do items</span></span> | <span data-ttu-id="2c141-414">None</span><span class="sxs-lookup"><span data-stu-id="2c141-414">None</span></span> | <span data-ttu-id="2c141-415">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="2c141-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="2c141-416">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="2c141-416">Get an item by ID</span></span> | <span data-ttu-id="2c141-417">None</span><span class="sxs-lookup"><span data-stu-id="2c141-417">None</span></span> | <span data-ttu-id="2c141-418">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="2c141-419">添加新项</span><span class="sxs-lookup"><span data-stu-id="2c141-419">Add a new item</span></span> | <span data-ttu-id="2c141-420">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-420">To-do item</span></span> | <span data-ttu-id="2c141-421">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="2c141-422">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2c141-423">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-423">To-do item</span></span> | <span data-ttu-id="2c141-424">None</span><span class="sxs-lookup"><span data-stu-id="2c141-424">None</span></span> |
|<span data-ttu-id="2c141-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="2c141-426">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2c141-427">None</span><span class="sxs-lookup"><span data-stu-id="2c141-427">None</span></span> | <span data-ttu-id="2c141-428">None</span><span class="sxs-lookup"><span data-stu-id="2c141-428">None</span></span>|

<span data-ttu-id="2c141-429">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="2c141-429">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="2c141-435">先决条件</span><span class="sxs-lookup"><span data-stu-id="2c141-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="2c141-439">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="2c141-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-441">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="2c141-441">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2c141-442">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-442">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="2c141-443">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-443">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="2c141-444">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”  。</span><span class="sxs-lookup"><span data-stu-id="2c141-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="2c141-445">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-445">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c141-448">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="2c141-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2c141-449">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2c141-450">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="2c141-451">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="2c141-451">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="2c141-452">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-452">The preceding commands:</span></span>

  * <span data-ttu-id="2c141-453">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="2c141-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="2c141-454">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2c141-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-455">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c141-456">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-456">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2c141-458">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="2c141-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="2c141-459">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="2c141-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="2c141-461">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 3.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="2c141-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="2c141-462">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="2c141-462">Select **Next**.</span></span>

* <span data-ttu-id="2c141-463">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-463">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="2c141-465">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="2c141-466">测试 API</span><span class="sxs-lookup"><span data-stu-id="2c141-466">Test the API</span></span>

<span data-ttu-id="2c141-467">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="2c141-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="2c141-468">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c141-470">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2c141-471">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="2c141-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="2c141-472">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="2c141-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="2c141-473">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="2c141-473">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c141-475">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2c141-476">在浏览器中，转到以下 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="2c141-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-477">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c141-478">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2c141-479">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="2c141-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2c141-480">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="2c141-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2c141-481">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="2c141-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="2c141-482">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="2c141-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="2c141-483">添加模型类</span><span class="sxs-lookup"><span data-stu-id="2c141-483">Add a model class</span></span>

<span data-ttu-id="2c141-484">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="2c141-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2c141-485">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="2c141-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-487">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-487">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="2c141-488">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="2c141-488">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2c141-489">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="2c141-489">Name the folder *Models*.</span></span>

* <span data-ttu-id="2c141-490">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-490">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2c141-491">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-491">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="2c141-492">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c141-494">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-494">Add a folder named *Models*.</span></span>

* <span data-ttu-id="2c141-495">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="2c141-495">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-496">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c141-497">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-497">Right-click the project.</span></span> <span data-ttu-id="2c141-498">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="2c141-498">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2c141-499">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="2c141-499">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2c141-501">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="2c141-501">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="2c141-502">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-502">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="2c141-503">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="2c141-504">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="2c141-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2c141-505">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-505">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="2c141-506">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="2c141-506">Add a database context</span></span>

<span data-ttu-id="2c141-507">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="2c141-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2c141-508">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="2c141-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="2c141-510">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="2c141-510">Add NuGet packages</span></span>

* <span data-ttu-id="2c141-511">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="2c141-512">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer 。</span><span class="sxs-lookup"><span data-stu-id="2c141-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="2c141-513">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”。</span><span class="sxs-lookup"><span data-stu-id="2c141-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="2c141-514">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-514">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="2c141-515">使用前面的说明添加 Microsoft.EntityFrameworkCore.InMemory NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2c141-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="2c141-517">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="2c141-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="2c141-518">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-518">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2c141-519">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-519">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2c141-520">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2c141-521">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-521">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="2c141-522">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="2c141-523">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="2c141-523">Register the database context</span></span>

<span data-ttu-id="2c141-524">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="2c141-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2c141-525">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="2c141-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="2c141-526">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="2c141-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="2c141-527">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-527">The preceding code:</span></span>

* <span data-ttu-id="2c141-528">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="2c141-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2c141-529">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="2c141-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2c141-530">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="2c141-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="2c141-531">构建控制器</span><span class="sxs-lookup"><span data-stu-id="2c141-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-533">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2c141-534">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-534">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2c141-535">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-535">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="2c141-536">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="2c141-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="2c141-537">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-537">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="2c141-538">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-538">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="2c141-539">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-539">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2c141-540">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2c141-541">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="2c141-542">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-542">The preceding commands:</span></span>

* <span data-ttu-id="2c141-543">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2c141-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="2c141-544">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="2c141-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="2c141-545">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="2c141-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="2c141-546">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-546">The generated code:</span></span>

* <span data-ttu-id="2c141-547">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="2c141-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2c141-548">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2c141-549">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="2c141-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2c141-550">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="2c141-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2c141-551">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="2c141-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="2c141-552">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="2c141-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="2c141-553">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="2c141-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="2c141-554">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="2c141-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="2c141-555">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="2c141-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="2c141-556">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="2c141-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="2c141-557">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="2c141-558">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="2c141-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="2c141-559">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="2c141-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2c141-560">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="2c141-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2c141-561">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="2c141-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2c141-562"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="2c141-563">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="2c141-564">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="2c141-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2c141-565">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="2c141-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="2c141-566">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="2c141-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="2c141-567">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="2c141-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2c141-568">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="2c141-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2c141-569">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="2c141-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="2c141-570">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="2c141-570">Install Postman</span></span>

<span data-ttu-id="2c141-571">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2c141-572">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="2c141-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="2c141-573">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-573">Start the web app.</span></span>
* <span data-ttu-id="2c141-574">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="2c141-574">Start Postman.</span></span>
* <span data-ttu-id="2c141-575">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="2c141-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="2c141-576">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="2c141-576">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="2c141-577">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="2c141-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="2c141-578">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="2c141-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="2c141-579">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-579">Create a new request.</span></span>
* <span data-ttu-id="2c141-580">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="2c141-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2c141-581">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2c141-582">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2c141-583">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="2c141-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="2c141-584">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="2c141-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2c141-585">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="2c141-585">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="2c141-586">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="2c141-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2c141-587">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-587">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="2c141-589">使用 Postman 测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="2c141-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="2c141-590">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="2c141-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2c141-591">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="2c141-591">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="2c141-593">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="2c141-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="2c141-594">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="2c141-595">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="2c141-596">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-596">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="2c141-597">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-597">Examine the GET methods</span></span>

<span data-ttu-id="2c141-598">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="2c141-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="2c141-599">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="2c141-600">例如：</span><span class="sxs-lookup"><span data-stu-id="2c141-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="2c141-601">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="2c141-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="2c141-602">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="2c141-602">Test Get with Postman</span></span>

* <span data-ttu-id="2c141-603">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-603">Create a new request.</span></span>
* <span data-ttu-id="2c141-604">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="2c141-604">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="2c141-605">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2c141-606">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="2c141-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2c141-607">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="2c141-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2c141-608">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-608">Select **Send**.</span></span>

<span data-ttu-id="2c141-609">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="2c141-609">This app uses an in-memory database.</span></span> <span data-ttu-id="2c141-610">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="2c141-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="2c141-611">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="2c141-612">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="2c141-612">Routing and URL paths</span></span>

<span data-ttu-id="2c141-613">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2c141-614">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="2c141-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2c141-615">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="2c141-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="2c141-616">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="2c141-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2c141-617">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="2c141-617">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="2c141-618">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2c141-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2c141-619">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="2c141-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2c141-620">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="2c141-620">This sample doesn't use a template.</span></span> <span data-ttu-id="2c141-621">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="2c141-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2c141-622">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="2c141-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2c141-623">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="2c141-624">返回值</span><span class="sxs-lookup"><span data-stu-id="2c141-624">Return values</span></span> 

<span data-ttu-id="2c141-625">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="2c141-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2c141-626">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="2c141-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2c141-627">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="2c141-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2c141-628">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="2c141-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2c141-629">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2c141-630">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="2c141-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2c141-631">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2c141-632">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="2c141-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2c141-633">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="2c141-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="2c141-634">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-634">The PutTodoItem method</span></span>

<span data-ttu-id="2c141-635">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="2c141-636">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="2c141-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2c141-637">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="2c141-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2c141-638">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="2c141-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2c141-639">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="2c141-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2c141-640">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="2c141-641">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="2c141-642">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="2c141-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2c141-643">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="2c141-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2c141-644">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="2c141-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2c141-645">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="2c141-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2c141-646">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="2c141-646">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="2c141-648">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="2c141-649">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="2c141-650">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="2c141-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="2c141-651">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="2c141-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2c141-652">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="2c141-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2c141-653">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="2c141-654">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-654">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="2c141-655">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="2c141-655">Prevent over-posting</span></span>

<span data-ttu-id="2c141-656">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="2c141-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="2c141-657">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="2c141-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="2c141-658">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="2c141-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="2c141-659">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="2c141-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="2c141-660">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="2c141-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="2c141-661">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="2c141-661">A DTO may be used to:</span></span>

* <span data-ttu-id="2c141-662">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="2c141-662">Prevent over-posting.</span></span>
* <span data-ttu-id="2c141-663">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="2c141-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="2c141-664">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="2c141-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="2c141-665">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="2c141-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="2c141-666">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="2c141-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="2c141-667">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="2c141-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="2c141-668">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="2c141-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="2c141-669">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="2c141-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="2c141-670">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="2c141-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="2c141-671">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="2c141-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="2c141-672">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="2c141-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="2c141-673">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="2c141-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="2c141-674">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="2c141-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c141-675">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="2c141-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c141-676">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-676">Create a web API project.</span></span>
> * <span data-ttu-id="2c141-677">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="2c141-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2c141-678">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="2c141-678">Add a controller.</span></span>
> * <span data-ttu-id="2c141-679">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="2c141-680">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="2c141-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="2c141-681">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="2c141-681">Specify return values.</span></span>
> * <span data-ttu-id="2c141-682">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="2c141-683">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="2c141-684">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="2c141-685">概述 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-685">Overview 2.1</span></span>

<span data-ttu-id="2c141-686">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="2c141-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2c141-687">API</span><span class="sxs-lookup"><span data-stu-id="2c141-687">API</span></span> | <span data-ttu-id="2c141-688">描述</span><span class="sxs-lookup"><span data-stu-id="2c141-688">Description</span></span> | <span data-ttu-id="2c141-689">请求正文</span><span class="sxs-lookup"><span data-stu-id="2c141-689">Request body</span></span> | <span data-ttu-id="2c141-690">响应正文</span><span class="sxs-lookup"><span data-stu-id="2c141-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="2c141-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="2c141-691">GET /api/TodoItems</span></span> | <span data-ttu-id="2c141-692">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-692">Get all to-do items</span></span> | <span data-ttu-id="2c141-693">None</span><span class="sxs-lookup"><span data-stu-id="2c141-693">None</span></span> | <span data-ttu-id="2c141-694">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="2c141-694">Array of to-do items</span></span>|
|<span data-ttu-id="2c141-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="2c141-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="2c141-696">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="2c141-696">Get an item by ID</span></span> | <span data-ttu-id="2c141-697">None</span><span class="sxs-lookup"><span data-stu-id="2c141-697">None</span></span> | <span data-ttu-id="2c141-698">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-698">To-do item</span></span>|
|<span data-ttu-id="2c141-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="2c141-699">POST /api/TodoItems</span></span> | <span data-ttu-id="2c141-700">添加新项</span><span class="sxs-lookup"><span data-stu-id="2c141-700">Add a new item</span></span> | <span data-ttu-id="2c141-701">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-701">To-do item</span></span> | <span data-ttu-id="2c141-702">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-702">To-do item</span></span> |
|<span data-ttu-id="2c141-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="2c141-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="2c141-704">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2c141-705">待办事项</span><span class="sxs-lookup"><span data-stu-id="2c141-705">To-do item</span></span> | <span data-ttu-id="2c141-706">None</span><span class="sxs-lookup"><span data-stu-id="2c141-706">None</span></span> |
|<span data-ttu-id="2c141-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="2c141-708">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="2c141-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2c141-709">None</span><span class="sxs-lookup"><span data-stu-id="2c141-709">None</span></span> | <span data-ttu-id="2c141-710">None</span><span class="sxs-lookup"><span data-stu-id="2c141-710">None</span></span>|

<span data-ttu-id="2c141-711">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="2c141-711">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="2c141-717">先决条件 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-720">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="2c141-721">创建 Web 项目 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-723">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="2c141-723">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2c141-724">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-724">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="2c141-725">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-725">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="2c141-726">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”  。</span><span class="sxs-lookup"><span data-stu-id="2c141-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="2c141-727">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-727">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="2c141-728">请不要选择“启用 Docker 支持” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-728">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c141-731">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="2c141-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2c141-732">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2c141-733">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c141-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="2c141-734">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="2c141-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="2c141-735">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="2c141-735">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-736">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c141-737">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-737">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2c141-739">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="2c141-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="2c141-740">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="2c141-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="2c141-741">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 2.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="2c141-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="2c141-742">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="2c141-742">Select **Next**.</span></span>

* <span data-ttu-id="2c141-743">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-743">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="2c141-745">测试 API 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-745">Test the API 2.1</span></span>

<span data-ttu-id="2c141-746">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="2c141-746">The project template creates a `values` API.</span></span> <span data-ttu-id="2c141-747">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c141-749">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2c141-750">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="2c141-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="2c141-751">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="2c141-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="2c141-752">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="2c141-752">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c141-754">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2c141-755">在浏览器中，转到以下 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="2c141-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-756">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c141-757">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2c141-758">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="2c141-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2c141-759">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="2c141-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2c141-760">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="2c141-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="2c141-761">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="2c141-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="2c141-762">添加模型类 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-762">Add a model class 2.1</span></span>

<span data-ttu-id="2c141-763">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="2c141-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2c141-764">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="2c141-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-766">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-766">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="2c141-767">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="2c141-767">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2c141-768">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="2c141-768">Name the folder *Models*.</span></span>

* <span data-ttu-id="2c141-769">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-769">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2c141-770">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-770">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="2c141-771">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c141-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c141-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c141-773">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-773">Add a folder named *Models*.</span></span>

* <span data-ttu-id="2c141-774">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="2c141-774">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c141-775">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c141-776">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-776">Right-click the project.</span></span> <span data-ttu-id="2c141-777">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="2c141-777">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="2c141-778">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="2c141-778">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2c141-780">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="2c141-780">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="2c141-781">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="2c141-781">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="2c141-782">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="2c141-783">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="2c141-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2c141-784">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-784">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="2c141-785">添加数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-785">Add a database context 2.1</span></span>

<span data-ttu-id="2c141-786">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="2c141-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2c141-787">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="2c141-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-789">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="2c141-789">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="2c141-790">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-790">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2c141-791">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2c141-792">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-792">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="2c141-793">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="2c141-794">注册数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-794">Register the database context 2.1</span></span>

<span data-ttu-id="2c141-795">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="2c141-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2c141-796">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="2c141-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="2c141-797">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="2c141-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="2c141-798">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-798">The preceding code:</span></span>

* <span data-ttu-id="2c141-799">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="2c141-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2c141-800">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="2c141-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2c141-801">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="2c141-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="2c141-802">添加控制器 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-804">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2c141-805">选择“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="2c141-805">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="2c141-806">在“添加新项”对话框中，选择“API 控制器类”模板 。</span><span class="sxs-lookup"><span data-stu-id="2c141-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="2c141-807">将类命名为 TodoController，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="2c141-807">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2c141-809">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2c141-810">在“控制器”文件夹中，创建名为 `TodoController` 的类。</span><span class="sxs-lookup"><span data-stu-id="2c141-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="2c141-811">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="2c141-812">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="2c141-812">The preceding code:</span></span>

* <span data-ttu-id="2c141-813">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="2c141-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="2c141-814">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="2c141-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2c141-815">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2c141-816">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="2c141-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2c141-817">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="2c141-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2c141-818">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="2c141-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="2c141-819">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="2c141-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="2c141-820">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="2c141-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="2c141-821">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="2c141-822">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="2c141-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="2c141-823">添加 Get 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-823">Add Get methods 2.1</span></span>

<span data-ttu-id="2c141-824">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="2c141-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="2c141-825">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="2c141-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="2c141-826">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="2c141-826">Stop the app if it's still running.</span></span> <span data-ttu-id="2c141-827">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="2c141-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="2c141-828">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="2c141-829">例如：</span><span class="sxs-lookup"><span data-stu-id="2c141-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="2c141-830">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="2c141-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="2c141-831">路由和 URL 路径 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="2c141-832">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2c141-833">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="2c141-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2c141-834">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="2c141-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="2c141-835">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="2c141-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2c141-836">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”。</span><span class="sxs-lookup"><span data-stu-id="2c141-836">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="2c141-837">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2c141-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2c141-838">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="2c141-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2c141-839">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="2c141-839">This sample doesn't use a template.</span></span> <span data-ttu-id="2c141-840">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="2c141-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2c141-841">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="2c141-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2c141-842">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="2c141-843">返回值 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-843">Return values 2.1</span></span>

<span data-ttu-id="2c141-844">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="2c141-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2c141-845">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="2c141-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2c141-846">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="2c141-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2c141-847">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="2c141-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2c141-848">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2c141-849">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="2c141-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2c141-850">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2c141-851">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="2c141-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2c141-852">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="2c141-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="2c141-853">测试 GetTodoItems 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="2c141-854">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2c141-855">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="2c141-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="2c141-856">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-856">Start the web app.</span></span>
* <span data-ttu-id="2c141-857">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="2c141-857">Start Postman.</span></span>
* <span data-ttu-id="2c141-858">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="2c141-858">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c141-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c141-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c141-860">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="2c141-860">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2c141-861">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c141-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2c141-862">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”   。</span><span class="sxs-lookup"><span data-stu-id="2c141-862">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="2c141-863">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="2c141-863">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="2c141-864">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="2c141-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="2c141-865">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-865">Create a new request.</span></span>
  * <span data-ttu-id="2c141-866">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="2c141-866">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="2c141-867">将请求 URI 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="2c141-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="2c141-868">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="2c141-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="2c141-869">在 Postman 中设置**Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="2c141-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2c141-870">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-870">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="2c141-872">添加 Create 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-872">Add a Create method 2.1</span></span>

<span data-ttu-id="2c141-873">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="2c141-874">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="2c141-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2c141-875">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="2c141-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2c141-876">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="2c141-877">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="2c141-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="2c141-878">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="2c141-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2c141-879">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="2c141-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="2c141-880">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="2c141-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="2c141-881">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="2c141-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2c141-882">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="2c141-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2c141-883">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="2c141-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="2c141-884">测试 PostTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="2c141-885">生成项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-885">Build the project.</span></span>
* <span data-ttu-id="2c141-886">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="2c141-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2c141-887">将 URI 设置为 `https://localhost:<port>/api/TodoItem`。</span><span class="sxs-lookup"><span data-stu-id="2c141-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="2c141-888">例如 `https://localhost:5001/api/TodoItem`。</span><span class="sxs-lookup"><span data-stu-id="2c141-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="2c141-889">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="2c141-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="2c141-890">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="2c141-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2c141-891">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="2c141-891">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="2c141-892">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="2c141-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2c141-893">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-893">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="2c141-895">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="2c141-896">测试位置标头 URI 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="2c141-897">在**Headers** 窗格中选择**Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="2c141-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2c141-898">复制**Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="2c141-898">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="2c141-900">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="2c141-900">Set the method to GET.</span></span>
<span data-ttu-id="2c141-901">\*将 URI 设置为 `https://localhost:<port>/api/TodoItems/2`，</span><span class="sxs-lookup"><span data-stu-id="2c141-901">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="2c141-902"> 例如 `https://localhost:5001/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="2c141-902"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="2c141-903">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-903">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="2c141-904">添加 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="2c141-905">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="2c141-906">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="2c141-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2c141-907">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="2c141-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2c141-908">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="2c141-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2c141-909">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="2c141-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2c141-910">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="2c141-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="2c141-911">测试 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="2c141-912">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="2c141-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2c141-913">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="2c141-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2c141-914">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="2c141-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2c141-915">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="2c141-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2c141-916">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="2c141-916">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="2c141-918">添加 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="2c141-919">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="2c141-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="2c141-920">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="2c141-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="2c141-921">测试 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="2c141-922">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="2c141-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2c141-923">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="2c141-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2c141-924">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="2c141-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="2c141-925">选择**Send**。</span><span class="sxs-lookup"><span data-stu-id="2c141-925">Select **Send**.</span></span>

<span data-ttu-id="2c141-926">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="2c141-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="2c141-927">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="2c141-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="2c141-928">使用 JavaScript 调用 Web API 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="2c141-929">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="2c141-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="2c141-930">jQuery 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="2c141-930">jQuery initiates the request.</span></span> <span data-ttu-id="2c141-931">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="2c141-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="2c141-932">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)并[实现默认文件映射](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="2c141-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="2c141-933">在项目目录中创建 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c141-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="2c141-934">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="2c141-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="2c141-935">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="2c141-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="2c141-936">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="2c141-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="2c141-937">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="2c141-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="2c141-938">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="2c141-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="2c141-939">打开 Properties\launchSettings.json。</span><span class="sxs-lookup"><span data-stu-id="2c141-939">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="2c141-940">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用。</span><span class="sxs-lookup"><span data-stu-id="2c141-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="2c141-941">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="2c141-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="2c141-942">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="2c141-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="2c141-943">获取待办事项列表 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="2c141-944">jQuery 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="2c141-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="2c141-945">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="2c141-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="2c141-946">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="2c141-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="2c141-947">添加待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="2c141-948">jQuery 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="2c141-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="2c141-949">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="2c141-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="2c141-950">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="2c141-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="2c141-951">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="2c141-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="2c141-952">更新待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="2c141-953">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="2c141-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="2c141-954">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="2c141-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="2c141-955">删除待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="2c141-956">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="2c141-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="2c141-957">向 Web API 添加身份验证支持 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="2c141-958">其他资源 2.1</span><span class="sxs-lookup"><span data-stu-id="2c141-958">Additional resources 2.1</span></span>

<span data-ttu-id="2c141-959">[查看或下载本教程的示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="2c141-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="2c141-960">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="2c141-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="2c141-961">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="2c141-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="2c141-962">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="2c141-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
