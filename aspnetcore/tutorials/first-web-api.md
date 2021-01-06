---
title: 教程：使用 ASP.NET Core 创建 Web API
author: rick-anderson
description: 了解如何使用 ASP.NET Core 生成 Web API。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "96175047"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="c6caa-103">教程：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="c6caa-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="c6caa-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5) 和 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="c6caa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="c6caa-105">本教程介绍使用 ASP.NET Core 构建 Web API 的基础知识。</span><span class="sxs-lookup"><span data-stu-id="c6caa-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c6caa-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="c6caa-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c6caa-107">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-107">Create a web API project.</span></span>
> * <span data-ttu-id="c6caa-108">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="c6caa-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c6caa-109">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="c6caa-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c6caa-110">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="c6caa-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c6caa-111">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-111">Call the web API with Postman.</span></span>

<span data-ttu-id="c6caa-112">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c6caa-113">概述</span><span class="sxs-lookup"><span data-stu-id="c6caa-113">Overview</span></span>

<span data-ttu-id="c6caa-114">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="c6caa-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c6caa-115">API</span><span class="sxs-lookup"><span data-stu-id="c6caa-115">API</span></span> | <span data-ttu-id="c6caa-116">描述</span><span class="sxs-lookup"><span data-stu-id="c6caa-116">Description</span></span> | <span data-ttu-id="c6caa-117">请求正文</span><span class="sxs-lookup"><span data-stu-id="c6caa-117">Request body</span></span> | <span data-ttu-id="c6caa-118">响应正文</span><span class="sxs-lookup"><span data-stu-id="c6caa-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c6caa-119">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-119">Get all to-do items</span></span> | <span data-ttu-id="c6caa-120">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-120">None</span></span> | <span data-ttu-id="c6caa-121">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="c6caa-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c6caa-122">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="c6caa-122">Get an item by ID</span></span> | <span data-ttu-id="c6caa-123">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-123">None</span></span> | <span data-ttu-id="c6caa-124">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c6caa-125">添加新项</span><span class="sxs-lookup"><span data-stu-id="c6caa-125">Add a new item</span></span> | <span data-ttu-id="c6caa-126">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-126">To-do item</span></span> | <span data-ttu-id="c6caa-127">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c6caa-128">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c6caa-129">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-129">To-do item</span></span> | <span data-ttu-id="c6caa-130">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-130">None</span></span> |
|<span data-ttu-id="c6caa-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c6caa-132">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c6caa-133">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-133">None</span></span> | <span data-ttu-id="c6caa-134">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-134">None</span></span>|

<span data-ttu-id="c6caa-135">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="c6caa-135">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c6caa-141">先决条件</span><span class="sxs-lookup"><span data-stu-id="c6caa-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c6caa-145">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="c6caa-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-147">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c6caa-148">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c6caa-149">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c6caa-150">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择 .NET Core 和 ASP.NET Core 5.0  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="c6caa-151">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-151">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c6caa-154">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c6caa-155">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c6caa-156">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c6caa-157">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="c6caa-158">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-158">The preceding commands:</span></span>

  * <span data-ttu-id="c6caa-159">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="c6caa-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c6caa-160">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c6caa-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c6caa-162">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-162">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c6caa-164">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="c6caa-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c6caa-165">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c6caa-167">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 5.x 目标框架。</span><span class="sxs-lookup"><span data-stu-id="c6caa-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="c6caa-168">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-168">Select **Next**.</span></span>

* <span data-ttu-id="c6caa-169">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c6caa-171">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="c6caa-172">测试项目</span><span class="sxs-lookup"><span data-stu-id="c6caa-172">Test the project</span></span>

<span data-ttu-id="c6caa-173">项目模板创建了一个支持 [Swagger](xref:tutorials/web-api-help-pages-using-swagger) 的 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c6caa-175">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="c6caa-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="c6caa-176">Visual Studio 将启动：</span><span class="sxs-lookup"><span data-stu-id="c6caa-176">Visual Studio launches:</span></span>

* <span data-ttu-id="c6caa-177">IIS Express Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="c6caa-177">The IIS Express web server.</span></span>
* <span data-ttu-id="c6caa-178">默认浏览器，并导航到 `https://localhost:<port>/swagger/index.html`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="c6caa-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="c6caa-180">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c6caa-181">在浏览器中，转到以下 URL：[https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="c6caa-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c6caa-183">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c6caa-184">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="c6caa-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c6caa-185">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="c6caa-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c6caa-186">将 `/swagger` 追加到 URL（将 URL 更改为 `https://localhost:<port>/swagger`）。</span><span class="sxs-lookup"><span data-stu-id="c6caa-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="c6caa-187">随即显示 Swagger 页面 `/swagger/index.html`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="c6caa-188">选择 GET  > “试用” > “执行”  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="c6caa-189">页面将显示：</span><span class="sxs-lookup"><span data-stu-id="c6caa-189">The page displays:</span></span>

* <span data-ttu-id="c6caa-190">用于测试 WeatherForecast API 的 [Curl](https://curl.haxx.se/) 命令。</span><span class="sxs-lookup"><span data-stu-id="c6caa-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c6caa-191">用于测试 WeatherForecast API 的 URL。</span><span class="sxs-lookup"><span data-stu-id="c6caa-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="c6caa-192">响应代码、正文和标头。</span><span class="sxs-lookup"><span data-stu-id="c6caa-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="c6caa-193">包含媒体类型、示例值和架构的下拉列表框。</span><span class="sxs-lookup"><span data-stu-id="c6caa-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="c6caa-194">Swagger 用于为 Web API 生成有用的文档和帮助页面。</span><span class="sxs-lookup"><span data-stu-id="c6caa-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="c6caa-195">本教程重点介绍如何创建 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="c6caa-196">有关 Swagger 的详细信息，请参阅 <xref:tutorials/web-api-help-pages-using-swagger>。</span><span class="sxs-lookup"><span data-stu-id="c6caa-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="c6caa-197">将请求 URL 复制粘贴到浏览器中：`https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="c6caa-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="c6caa-198">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="c6caa-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="c6caa-199">更新 launchUrl</span><span class="sxs-lookup"><span data-stu-id="c6caa-199">Update the launchUrl</span></span>

<span data-ttu-id="c6caa-200">在 Properties\launchSettings.json 中，将 `launchUrl` 从 `"swagger"` 更新为 `"api/TodoItems"`：</span><span class="sxs-lookup"><span data-stu-id="c6caa-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="c6caa-201">由于已删除 Swagger，上述标记会将启动的 URL 更改为添加到以下部分的控制器的 GET 方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="c6caa-202">添加模型类</span><span class="sxs-lookup"><span data-stu-id="c6caa-202">Add a model class</span></span>

<span data-ttu-id="c6caa-203">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c6caa-204">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-206">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c6caa-207">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c6caa-208">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="c6caa-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="c6caa-209">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c6caa-210">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c6caa-211">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="c6caa-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c6caa-213">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c6caa-214">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="c6caa-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c6caa-216">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-216">Right-click the project.</span></span> <span data-ttu-id="c6caa-217">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c6caa-218">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="c6caa-218">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c6caa-220">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="c6caa-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c6caa-221">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c6caa-222">将模板代码替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="c6caa-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="c6caa-223">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="c6caa-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c6caa-224">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c6caa-225">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c6caa-225">Add a database context</span></span>

<span data-ttu-id="c6caa-226">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c6caa-227">此类由 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="c6caa-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c6caa-229">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="c6caa-229">Add NuGet packages</span></span>

* <span data-ttu-id="c6caa-230">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="c6caa-231">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="c6caa-232">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c6caa-233">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="c6caa-234">使用前面的说明添加 Microsoft.EntityFrameworkCore.InMemory NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c6caa-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="c6caa-235">![NuGet 包管理器](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="c6caa-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c6caa-236">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c6caa-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="c6caa-237">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c6caa-238">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c6caa-240">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c6caa-241">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c6caa-242">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c6caa-242">Register the database context</span></span>

<span data-ttu-id="c6caa-243">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="c6caa-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c6caa-244">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="c6caa-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="c6caa-245">使用以下代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="c6caa-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c6caa-246">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-246">The preceding code:</span></span>

* <span data-ttu-id="c6caa-247">删除 Swagger 调用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="c6caa-248">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="c6caa-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c6caa-249">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="c6caa-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c6caa-250">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="c6caa-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c6caa-251">构建控制器</span><span class="sxs-lookup"><span data-stu-id="c6caa-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-253">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c6caa-254">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c6caa-255">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="c6caa-256">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="c6caa-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c6caa-257">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="c6caa-258">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="c6caa-259">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-260">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c6caa-261">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c6caa-262">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-262">The preceding commands:</span></span>

* <span data-ttu-id="c6caa-263">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c6caa-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c6caa-264">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c6caa-265">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c6caa-266">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-266">The generated code:</span></span>

* <span data-ttu-id="c6caa-267">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c6caa-268">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c6caa-269">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="c6caa-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c6caa-270">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="c6caa-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c6caa-271">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c6caa-272">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="c6caa-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c6caa-273">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c6caa-274">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c6caa-275">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="c6caa-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c6caa-276">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="c6caa-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="c6caa-277">更新 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="c6caa-278">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="c6caa-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c6caa-279">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="c6caa-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c6caa-280">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="c6caa-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c6caa-281">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c6caa-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c6caa-283">如果成功，将返回 [HTTP 201 状态代码](https://developer.mozilla.org/docs/Web/HTTP/Status/201)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="c6caa-284">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="c6caa-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c6caa-285">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="c6caa-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c6caa-286">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c6caa-287">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c6caa-288">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="c6caa-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c6caa-289">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="c6caa-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c6caa-290">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="c6caa-290">Install Postman</span></span>

<span data-ttu-id="c6caa-291">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c6caa-292">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="c6caa-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c6caa-293">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-293">Start the web app.</span></span>
* <span data-ttu-id="c6caa-294">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="c6caa-294">Start Postman.</span></span>
* <span data-ttu-id="c6caa-295">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="c6caa-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c6caa-296">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="c6caa-297">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="c6caa-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c6caa-298">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c6caa-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c6caa-299">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-299">Create a new request.</span></span>
* <span data-ttu-id="c6caa-300">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c6caa-301">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c6caa-302">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c6caa-303">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="c6caa-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="c6caa-304">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="c6caa-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c6caa-305">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="c6caa-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c6caa-306">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="c6caa-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c6caa-307">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-307">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="c6caa-309">测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="c6caa-309">Test the location header URI</span></span>

<span data-ttu-id="c6caa-310">你可以在浏览器中测试位置标头 URI。</span><span class="sxs-lookup"><span data-stu-id="c6caa-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="c6caa-311">将位置标头 URI 复制粘贴到浏览器中。</span><span class="sxs-lookup"><span data-stu-id="c6caa-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="c6caa-312">若要在 Postman 中测试，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c6caa-312">To test in Postman:</span></span>

* <span data-ttu-id="c6caa-313">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="c6caa-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c6caa-314">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="c6caa-314">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="c6caa-316">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c6caa-317">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c6caa-318">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c6caa-319">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c6caa-320">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-320">Examine the GET methods</span></span>

<span data-ttu-id="c6caa-321">实现了两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="c6caa-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c6caa-322">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c6caa-323">例如：</span><span class="sxs-lookup"><span data-stu-id="c6caa-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c6caa-324">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="c6caa-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c6caa-325">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="c6caa-325">Test Get with Postman</span></span>

* <span data-ttu-id="c6caa-326">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-326">Create a new request.</span></span>
* <span data-ttu-id="c6caa-327">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="c6caa-328">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c6caa-329">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c6caa-330">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c6caa-331">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-331">Select **Send**.</span></span>

<span data-ttu-id="c6caa-332">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="c6caa-332">This app uses an in-memory database.</span></span> <span data-ttu-id="c6caa-333">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="c6caa-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c6caa-334">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c6caa-335">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="c6caa-335">Routing and URL paths</span></span>

<span data-ttu-id="c6caa-336">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c6caa-337">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="c6caa-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c6caa-338">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="c6caa-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c6caa-339">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="c6caa-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c6caa-340">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c6caa-341">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c6caa-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c6caa-342">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="c6caa-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c6caa-343">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="c6caa-343">This sample doesn't use a template.</span></span> <span data-ttu-id="c6caa-344">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c6caa-345">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="c6caa-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c6caa-346">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c6caa-347">返回值</span><span class="sxs-lookup"><span data-stu-id="c6caa-347">Return values</span></span>

<span data-ttu-id="c6caa-348">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c6caa-349">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="c6caa-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c6caa-350">此返回类型的响应代码为 [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200)（假设没有未处理的异常）。</span><span class="sxs-lookup"><span data-stu-id="c6caa-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c6caa-351">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="c6caa-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c6caa-352">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c6caa-353">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="c6caa-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c6caa-354">如果没有任何项与请求的 ID 匹配，该方法将返回 [404 状态](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c6caa-355">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="c6caa-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c6caa-356">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="c6caa-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c6caa-357">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-357">The PutTodoItem method</span></span>

<span data-ttu-id="c6caa-358">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c6caa-359">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="c6caa-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c6caa-360">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c6caa-361">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="c6caa-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c6caa-362">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c6caa-363">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c6caa-364">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="c6caa-365">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="c6caa-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c6caa-366">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c6caa-367">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c6caa-368">更新 ID 为 1 的待办事项并将其名称设置为 `"feed fish"`：</span><span class="sxs-lookup"><span data-stu-id="c6caa-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c6caa-369">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="c6caa-369">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c6caa-371">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="c6caa-372">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c6caa-373">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c6caa-374">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="c6caa-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c6caa-375">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c6caa-376">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c6caa-377">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c6caa-378">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="c6caa-378">Prevent over-posting</span></span>

<span data-ttu-id="c6caa-379">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="c6caa-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c6caa-380">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="c6caa-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c6caa-381">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="c6caa-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c6caa-382">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="c6caa-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c6caa-383">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="c6caa-384">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="c6caa-384">A DTO may be used to:</span></span>

* <span data-ttu-id="c6caa-385">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="c6caa-385">Prevent over-posting.</span></span>
* <span data-ttu-id="c6caa-386">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="c6caa-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c6caa-387">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="c6caa-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c6caa-388">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="c6caa-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c6caa-389">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="c6caa-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c6caa-390">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="c6caa-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="c6caa-391">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="c6caa-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c6caa-392">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="c6caa-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c6caa-393">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="c6caa-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c6caa-394">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="c6caa-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c6caa-395">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="c6caa-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c6caa-396">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="c6caa-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="c6caa-397">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c6caa-398">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="c6caa-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c6caa-399">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-399">Create a web API project.</span></span>
> * <span data-ttu-id="c6caa-400">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="c6caa-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c6caa-401">使用 CRUD 方法构建控制器。</span><span class="sxs-lookup"><span data-stu-id="c6caa-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="c6caa-402">配置路由、URL 路径和返回值。</span><span class="sxs-lookup"><span data-stu-id="c6caa-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="c6caa-403">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-403">Call the web API with Postman.</span></span>

<span data-ttu-id="c6caa-404">在结束时，你会获得可以管理存储在数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="c6caa-405">概述</span><span class="sxs-lookup"><span data-stu-id="c6caa-405">Overview</span></span>

<span data-ttu-id="c6caa-406">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="c6caa-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c6caa-407">API</span><span class="sxs-lookup"><span data-stu-id="c6caa-407">API</span></span> | <span data-ttu-id="c6caa-408">描述</span><span class="sxs-lookup"><span data-stu-id="c6caa-408">Description</span></span> | <span data-ttu-id="c6caa-409">请求正文</span><span class="sxs-lookup"><span data-stu-id="c6caa-409">Request body</span></span> | <span data-ttu-id="c6caa-410">响应正文</span><span class="sxs-lookup"><span data-stu-id="c6caa-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="c6caa-411">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-411">Get all to-do items</span></span> | <span data-ttu-id="c6caa-412">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-412">None</span></span> | <span data-ttu-id="c6caa-413">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="c6caa-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="c6caa-414">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="c6caa-414">Get an item by ID</span></span> | <span data-ttu-id="c6caa-415">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-415">None</span></span> | <span data-ttu-id="c6caa-416">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="c6caa-417">添加新项</span><span class="sxs-lookup"><span data-stu-id="c6caa-417">Add a new item</span></span> | <span data-ttu-id="c6caa-418">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-418">To-do item</span></span> | <span data-ttu-id="c6caa-419">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="c6caa-420">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c6caa-421">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-421">To-do item</span></span> | <span data-ttu-id="c6caa-422">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-422">None</span></span> |
|<span data-ttu-id="c6caa-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="c6caa-424">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c6caa-425">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-425">None</span></span> | <span data-ttu-id="c6caa-426">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-426">None</span></span>|

<span data-ttu-id="c6caa-427">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="c6caa-427">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="c6caa-433">先决条件</span><span class="sxs-lookup"><span data-stu-id="c6caa-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="c6caa-437">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="c6caa-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-439">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c6caa-440">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c6caa-441">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c6caa-442">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="c6caa-443">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-443">Select the **API** template and click **Create**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c6caa-446">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c6caa-447">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c6caa-448">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="c6caa-449">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="c6caa-450">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-450">The preceding commands:</span></span>

  * <span data-ttu-id="c6caa-451">创建新的 web API 项目，并在 Visual Studio Code 中打开它。</span><span class="sxs-lookup"><span data-stu-id="c6caa-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="c6caa-452">添加下一部分中所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c6caa-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-453">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c6caa-454">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-454">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c6caa-456">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="c6caa-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c6caa-457">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS API 模板选择](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="c6caa-459">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 3.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="c6caa-460">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-460">Select **Next**.</span></span>

* <span data-ttu-id="c6caa-461">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="c6caa-463">在项目文件夹中打开命令终端并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="c6caa-464">测试 API</span><span class="sxs-lookup"><span data-stu-id="c6caa-464">Test the API</span></span>

<span data-ttu-id="c6caa-465">项目模板会创建 `WeatherForecast` API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="c6caa-466">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c6caa-468">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c6caa-469">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/WeatherForecast`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="c6caa-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c6caa-470">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c6caa-471">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c6caa-473">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c6caa-474">在浏览器中，转到以下 URL：`https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-475">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c6caa-476">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c6caa-477">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="c6caa-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c6caa-478">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="c6caa-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c6caa-479">将 `/WeatherForecast` 追加到 URL（将 URL 更改为 `https://localhost:<port>/WeatherForecast`）。</span><span class="sxs-lookup"><span data-stu-id="c6caa-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="c6caa-480">返回类似于以下项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="c6caa-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="c6caa-481">添加模型类</span><span class="sxs-lookup"><span data-stu-id="c6caa-481">Add a model class</span></span>

<span data-ttu-id="c6caa-482">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c6caa-483">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-485">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c6caa-486">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c6caa-487">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="c6caa-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="c6caa-488">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c6caa-489">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c6caa-490">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c6caa-492">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c6caa-493">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="c6caa-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-494">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c6caa-495">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-495">Right-click the project.</span></span> <span data-ttu-id="c6caa-496">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c6caa-497">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="c6caa-497">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c6caa-499">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="c6caa-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c6caa-500">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c6caa-501">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="c6caa-502">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="c6caa-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c6caa-503">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="c6caa-504">添加数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c6caa-504">Add a database context</span></span>

<span data-ttu-id="c6caa-505">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c6caa-506">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="c6caa-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="c6caa-508">添加 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="c6caa-508">Add NuGet packages</span></span>

* <span data-ttu-id="c6caa-509">在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="c6caa-510">选择“浏览”选项卡，然后在搜索框中输入 Microsoft.EntityFrameworkCore.SqlServer 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="c6caa-511">在左窗格中选择“Microsoft.EntityFrameworkCore.SqlServer”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="c6caa-512">选中右窗格中的“项目”复选框，然后选择“安装” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="c6caa-513">使用前面的说明添加 Microsoft.EntityFrameworkCore.InMemory NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c6caa-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet 程序包管理器](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="c6caa-515">添加 TodoContext 数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c6caa-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="c6caa-516">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c6caa-517">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-518">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c6caa-519">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c6caa-520">输入以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="c6caa-521">注册数据库上下文</span><span class="sxs-lookup"><span data-stu-id="c6caa-521">Register the database context</span></span>

<span data-ttu-id="c6caa-522">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="c6caa-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c6caa-523">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="c6caa-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="c6caa-524">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="c6caa-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="c6caa-525">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-525">The preceding code:</span></span>

* <span data-ttu-id="c6caa-526">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="c6caa-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c6caa-527">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="c6caa-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c6caa-528">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="c6caa-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="c6caa-529">构建控制器</span><span class="sxs-lookup"><span data-stu-id="c6caa-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-531">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c6caa-532">选择“添加”>“新建构建项” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c6caa-533">选择“其操作使用实体框架的 API 控制器”，然后选择“添加” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="c6caa-534">在“添加其操作使用实体框架的 API 控制器”对话框中：</span><span class="sxs-lookup"><span data-stu-id="c6caa-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="c6caa-535">在“模型类”中选择“TodoItem (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="c6caa-536">在“数据上下文类”中选择“TodoContext (TodoApi.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="c6caa-537">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-538">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c6caa-539">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="c6caa-540">前面的命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-540">The preceding commands:</span></span>

* <span data-ttu-id="c6caa-541">添加构建所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c6caa-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="c6caa-542">安装构建引擎 (`dotnet-aspnet-codegenerator`)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="c6caa-543">构建 `TodoItemsController`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="c6caa-544">生成的代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-544">The generated code:</span></span>

* <span data-ttu-id="c6caa-545">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c6caa-546">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c6caa-547">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="c6caa-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c6caa-548">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="c6caa-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c6caa-549">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="c6caa-550">ASP.NET Core 模板：</span><span class="sxs-lookup"><span data-stu-id="c6caa-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="c6caa-551">具有视图的控制器在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="c6caa-552">API 控制器不在路由模板中包含 `[action]`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="c6caa-553">如果 `[action]` 标记不在路由模板中，则从路由中排除[操作](xref:mvc/controllers/routing#action)名称。</span><span class="sxs-lookup"><span data-stu-id="c6caa-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="c6caa-554">也就是说，不会在匹配的路由中使用操作的关联方法名称。</span><span class="sxs-lookup"><span data-stu-id="c6caa-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="c6caa-555">检查 PostTodoItem create 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="c6caa-556">替换 `PostTodoItem` 中的返回语句，以使用 [nameof](/dotnet/csharp/language-reference/operators/nameof) 运算符：</span><span class="sxs-lookup"><span data-stu-id="c6caa-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="c6caa-557">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="c6caa-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c6caa-558">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="c6caa-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c6caa-559">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c6caa-560"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="c6caa-561">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="c6caa-562">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="c6caa-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c6caa-563">向响应添加[位置](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location)标头。</span><span class="sxs-lookup"><span data-stu-id="c6caa-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="c6caa-564">`Location` 标头指定新建的待办事项的 [URI](https://developer.mozilla.org/docs/Glossary/URI)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="c6caa-565">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c6caa-566">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="c6caa-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c6caa-567">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="c6caa-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="c6caa-568">安装 Postman</span><span class="sxs-lookup"><span data-stu-id="c6caa-568">Install Postman</span></span>

<span data-ttu-id="c6caa-569">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c6caa-570">安装 [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="c6caa-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="c6caa-571">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-571">Start the web app.</span></span>
* <span data-ttu-id="c6caa-572">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="c6caa-572">Start Postman.</span></span>
* <span data-ttu-id="c6caa-573">禁用 **SSL 证书验证**</span><span class="sxs-lookup"><span data-stu-id="c6caa-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="c6caa-574">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="c6caa-575">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="c6caa-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="c6caa-576">通过 Postman 测试 PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="c6caa-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="c6caa-577">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-577">Create a new request.</span></span>
* <span data-ttu-id="c6caa-578">将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c6caa-579">将 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c6caa-580">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c6caa-581">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="c6caa-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="c6caa-582">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="c6caa-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c6caa-583">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="c6caa-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c6caa-584">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="c6caa-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c6caa-585">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-585">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="c6caa-587">使用 Postman 测试位置标头 URI</span><span class="sxs-lookup"><span data-stu-id="c6caa-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="c6caa-588">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="c6caa-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c6caa-589">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="c6caa-589">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/3/create.png)

* <span data-ttu-id="c6caa-591">将 HTTP 方法设置为 `GET`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="c6caa-592">将 URI 设置为 `https://localhost:<port>/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="c6caa-593">例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="c6caa-594">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="c6caa-595">检查 GET 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-595">Examine the GET methods</span></span>

<span data-ttu-id="c6caa-596">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="c6caa-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="c6caa-597">通过从浏览器或 Postman 调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="c6caa-598">例如：</span><span class="sxs-lookup"><span data-stu-id="c6caa-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="c6caa-599">对 `GetTodoItems` 的调用生成类似于以下项的响应：</span><span class="sxs-lookup"><span data-stu-id="c6caa-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="c6caa-600">使用 Postman 测试 Get</span><span class="sxs-lookup"><span data-stu-id="c6caa-600">Test Get with Postman</span></span>

* <span data-ttu-id="c6caa-601">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-601">Create a new request.</span></span>
* <span data-ttu-id="c6caa-602">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="c6caa-603">将请求 URI 设置为 `https://localhost:<port>/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="c6caa-604">例如 `https://localhost:5001/api/TodoItems`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="c6caa-605">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c6caa-606">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-606">Select **Send**.</span></span>

<span data-ttu-id="c6caa-607">此应用使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="c6caa-607">This app uses an in-memory database.</span></span> <span data-ttu-id="c6caa-608">如果应用已停止并启动，则前面的 GET 请求将不会返回任何数据。</span><span class="sxs-lookup"><span data-stu-id="c6caa-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="c6caa-609">如果未返回任何数据，将数据 [POST](#post) 到应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="c6caa-610">路由和 URL 路径</span><span class="sxs-lookup"><span data-stu-id="c6caa-610">Routing and URL paths</span></span>

<span data-ttu-id="c6caa-611">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c6caa-612">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="c6caa-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c6caa-613">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="c6caa-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="c6caa-614">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="c6caa-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c6caa-615">对于此示例，控制器类名称为“TodoItems”控制器，因此控制器名称为“TodoItems”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="c6caa-616">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c6caa-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c6caa-617">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="c6caa-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c6caa-618">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="c6caa-618">This sample doesn't use a template.</span></span> <span data-ttu-id="c6caa-619">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c6caa-620">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="c6caa-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c6caa-621">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="c6caa-622">返回值</span><span class="sxs-lookup"><span data-stu-id="c6caa-622">Return values</span></span> 

<span data-ttu-id="c6caa-623">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c6caa-624">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="c6caa-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c6caa-625">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="c6caa-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c6caa-626">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="c6caa-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c6caa-627">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c6caa-628">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="c6caa-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c6caa-629">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c6caa-630">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="c6caa-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c6caa-631">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="c6caa-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="c6caa-632">PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-632">The PutTodoItem method</span></span>

<span data-ttu-id="c6caa-633">检查 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="c6caa-634">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="c6caa-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c6caa-635">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c6caa-636">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="c6caa-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c6caa-637">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c6caa-638">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="c6caa-639">测试 PutTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="c6caa-640">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="c6caa-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c6caa-641">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c6caa-642">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c6caa-643">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="c6caa-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c6caa-644">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="c6caa-644">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="c6caa-646">DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="c6caa-647">检查 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="c6caa-648">测试 DeleteTodoItem 方法</span><span class="sxs-lookup"><span data-stu-id="c6caa-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="c6caa-649">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="c6caa-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c6caa-650">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c6caa-651">设置要删除的对象的 URI，例如 `https://localhost:5001/api/TodoItems/1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="c6caa-652">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="c6caa-653">防止过度发布</span><span class="sxs-lookup"><span data-stu-id="c6caa-653">Prevent over-posting</span></span>

<span data-ttu-id="c6caa-654">目前，示例应用公开了整个 `TodoItem` 对象。</span><span class="sxs-lookup"><span data-stu-id="c6caa-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="c6caa-655">生产应用通常使用模型的子集来限制输入和返回的数据。</span><span class="sxs-lookup"><span data-stu-id="c6caa-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="c6caa-656">这背后有多种原因，但安全性是主要原因。</span><span class="sxs-lookup"><span data-stu-id="c6caa-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="c6caa-657">模型的子集通常称为数据传输对象 (DTO)、输入模型或视图模型。</span><span class="sxs-lookup"><span data-stu-id="c6caa-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="c6caa-658">本文使用的是 **DTO**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="c6caa-659">DTO 可用于：</span><span class="sxs-lookup"><span data-stu-id="c6caa-659">A DTO may be used to:</span></span>

* <span data-ttu-id="c6caa-660">防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="c6caa-660">Prevent over-posting.</span></span>
* <span data-ttu-id="c6caa-661">隐藏客户端不应查看的属性。</span><span class="sxs-lookup"><span data-stu-id="c6caa-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="c6caa-662">省略一些属性以缩减有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="c6caa-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="c6caa-663">平展包含嵌套对象的对象图。</span><span class="sxs-lookup"><span data-stu-id="c6caa-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="c6caa-664">对客户端而言，平展的对象图可能更方便。</span><span class="sxs-lookup"><span data-stu-id="c6caa-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="c6caa-665">若要演示 DTO 方法，请更新 `TodoItem` 类，使其包含机密字段：</span><span class="sxs-lookup"><span data-stu-id="c6caa-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="c6caa-666">此应用需要隐藏机密字段，但管理应用可以选择公开它。</span><span class="sxs-lookup"><span data-stu-id="c6caa-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="c6caa-667">确保可以发布和获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="c6caa-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="c6caa-668">创建 DTO 模型：</span><span class="sxs-lookup"><span data-stu-id="c6caa-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="c6caa-669">更新 `TodoItemsController` 以使用 `TodoItemDTO`：</span><span class="sxs-lookup"><span data-stu-id="c6caa-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="c6caa-670">确保无法发布或获取机密字段。</span><span class="sxs-lookup"><span data-stu-id="c6caa-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="c6caa-671">使用 JavaScript 调用 Web API</span><span class="sxs-lookup"><span data-stu-id="c6caa-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="c6caa-672">请参阅[教程：使用 JavaScript 调用 ASP.NET Core Web API](xref:tutorials/web-api-javascript)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6caa-673">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="c6caa-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c6caa-674">创建 Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-674">Create a web API project.</span></span>
> * <span data-ttu-id="c6caa-675">添加模型类和数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="c6caa-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="c6caa-676">添加控制器。</span><span class="sxs-lookup"><span data-stu-id="c6caa-676">Add a controller.</span></span>
> * <span data-ttu-id="c6caa-677">添加 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="c6caa-678">配置路由和 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="c6caa-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="c6caa-679">指定返回值。</span><span class="sxs-lookup"><span data-stu-id="c6caa-679">Specify return values.</span></span>
> * <span data-ttu-id="c6caa-680">使用 Postman 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="c6caa-681">使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="c6caa-682">在结束时，你会获得可以管理存储在关系数据库中的“待办事项”的 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="c6caa-683">概述 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-683">Overview 2.1</span></span>

<span data-ttu-id="c6caa-684">本教程将创建以下 API：</span><span class="sxs-lookup"><span data-stu-id="c6caa-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="c6caa-685">API</span><span class="sxs-lookup"><span data-stu-id="c6caa-685">API</span></span> | <span data-ttu-id="c6caa-686">描述</span><span class="sxs-lookup"><span data-stu-id="c6caa-686">Description</span></span> | <span data-ttu-id="c6caa-687">请求正文</span><span class="sxs-lookup"><span data-stu-id="c6caa-687">Request body</span></span> | <span data-ttu-id="c6caa-688">响应正文</span><span class="sxs-lookup"><span data-stu-id="c6caa-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="c6caa-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c6caa-689">GET /api/TodoItems</span></span> | <span data-ttu-id="c6caa-690">获取所有待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-690">Get all to-do items</span></span> | <span data-ttu-id="c6caa-691">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-691">None</span></span> | <span data-ttu-id="c6caa-692">待办事项的数组</span><span class="sxs-lookup"><span data-stu-id="c6caa-692">Array of to-do items</span></span>|
|<span data-ttu-id="c6caa-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c6caa-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="c6caa-694">按 ID 获取项</span><span class="sxs-lookup"><span data-stu-id="c6caa-694">Get an item by ID</span></span> | <span data-ttu-id="c6caa-695">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-695">None</span></span> | <span data-ttu-id="c6caa-696">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-696">To-do item</span></span>|
|<span data-ttu-id="c6caa-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="c6caa-697">POST /api/TodoItems</span></span> | <span data-ttu-id="c6caa-698">添加新项</span><span class="sxs-lookup"><span data-stu-id="c6caa-698">Add a new item</span></span> | <span data-ttu-id="c6caa-699">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-699">To-do item</span></span> | <span data-ttu-id="c6caa-700">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-700">To-do item</span></span> |
|<span data-ttu-id="c6caa-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="c6caa-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="c6caa-702">更新现有项 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="c6caa-703">待办事项</span><span class="sxs-lookup"><span data-stu-id="c6caa-703">To-do item</span></span> | <span data-ttu-id="c6caa-704">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-704">None</span></span> |
|<span data-ttu-id="c6caa-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="c6caa-706">删除项 &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="c6caa-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="c6caa-707">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-707">None</span></span> | <span data-ttu-id="c6caa-708">None</span><span class="sxs-lookup"><span data-stu-id="c6caa-708">None</span></span>|

<span data-ttu-id="c6caa-709">下图显示了应用的设计。</span><span class="sxs-lookup"><span data-stu-id="c6caa-709">The following diagram shows the design of the app.</span></span>

![右侧的框表示客户端。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="c6caa-715">先决条件 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-718">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="c6caa-719">创建 Web 项目 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-721">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c6caa-722">选择“ASP.NET Core Web 应用程序”模板，再单击“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="c6caa-723">将项目命名为 TodoApi，然后单击“创建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="c6caa-724">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 2.2”  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="c6caa-725">选择“API”模板，然后单击“创建” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="c6caa-726">请不要选择“启用 Docker 支持” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-726">**Don't** select **Enable Docker Support**.</span></span>

![VS“新建项目”对话框](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c6caa-729">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c6caa-730">将目录 (`cd`) 更改为包含项目文件夹的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="c6caa-731">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c6caa-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="c6caa-732">这些命令会创建新 Web API 项目并在新项目文件夹中打开 Visual Studio Code 的新实例。</span><span class="sxs-lookup"><span data-stu-id="c6caa-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="c6caa-733">当对话框询问是否要将所需资产添加到项目时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-734">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c6caa-735">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-735">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="c6caa-737">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “API” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="c6caa-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="c6caa-738">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “API” > “下一步”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="c6caa-739">在“配置新的 ASP.NET Core Web API”对话框中，选择最新的 .NET Core 2.x 目标框架 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="c6caa-740">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-740">Select **Next**.</span></span>

* <span data-ttu-id="c6caa-741">输入“TodoApi”作为“项目名称”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![配置对话框](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="c6caa-743">测试 API 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-743">Test the API 2.1</span></span>

<span data-ttu-id="c6caa-744">项目模板会创建 `values` API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-744">The project template creates a `values` API.</span></span> <span data-ttu-id="c6caa-745">从浏览器调用 `Get` 方法以测试应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c6caa-747">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c6caa-748">Visual Studio 启动浏览器并导航到 `https://localhost:<port>/api/values`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="c6caa-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="c6caa-749">如果出现询问是否应信任 IIS Express 证书的对话框，则选择“是”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="c6caa-750">在接下来出现的“安全警告”对话框中，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c6caa-752">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="c6caa-753">在浏览器中，转到以下 URL：`https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-754">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c6caa-755">选择“运行” > “开始调试”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="c6caa-756">Visual Studio for Mac 会启动浏览器并导航到 `https://localhost:<port>`，其中 `<port>` 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="c6caa-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="c6caa-757">将返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="c6caa-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="c6caa-758">将 `/api/values` 追加到 URL（将 URL 更改为 `https://localhost:<port>/api/values`）。</span><span class="sxs-lookup"><span data-stu-id="c6caa-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="c6caa-759">会返回以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="c6caa-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="c6caa-760">添加模型类 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-760">Add a model class 2.1</span></span>

<span data-ttu-id="c6caa-761">模型是一组表示应用管理的数据的类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="c6caa-762">此应用的模型是单个 `TodoItem` 类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-764">在“解决方案资源管理器”中，右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="c6caa-765">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c6caa-766">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="c6caa-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="c6caa-767">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c6caa-768">将类命名为 TodoItem，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="c6caa-769">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c6caa-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c6caa-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c6caa-771">添加名为 Models 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="c6caa-772">使用以下代码将 `TodoItem` 类添加到 Models 文件夹：</span><span class="sxs-lookup"><span data-stu-id="c6caa-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-773">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c6caa-774">右键单击项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-774">Right-click the project.</span></span> <span data-ttu-id="c6caa-775">选择“添加” > “新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="c6caa-776">将文件夹命名为 Models。</span><span class="sxs-lookup"><span data-stu-id="c6caa-776">Name the folder *Models*.</span></span>

  ![新建文件夹](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="c6caa-778">右键单击 Models 文件夹，然后选择“添加”>“新建文件”>“常规”>“空类”   。</span><span class="sxs-lookup"><span data-stu-id="c6caa-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="c6caa-779">将类命名为“TodoItem”，然后单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="c6caa-780">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="c6caa-781">`Id` 属性用作关系数据库中的唯一键。</span><span class="sxs-lookup"><span data-stu-id="c6caa-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="c6caa-782">模型类可位于项目的任意位置，但按照惯例会使用 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="c6caa-783">添加数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-783">Add a database context 2.1</span></span>

<span data-ttu-id="c6caa-784">数据库上下文是为数据模型协调 Entity Framework 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="c6caa-785">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="c6caa-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-787">右键单击 Models 文件夹，然后选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="c6caa-788">将类命名为 TodoContext，然后单击“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-789">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c6caa-790">将 `TodoContext` 类添加到 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="c6caa-791">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="c6caa-792">注册数据库上下文 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-792">Register the database context 2.1</span></span>

<span data-ttu-id="c6caa-793">在 ASP.NET Core 中，服务（如数据库上下文）必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器进行注册。</span><span class="sxs-lookup"><span data-stu-id="c6caa-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c6caa-794">该容器向控制器提供服务。</span><span class="sxs-lookup"><span data-stu-id="c6caa-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="c6caa-795">使用以下突出显示的代码更新 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="c6caa-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="c6caa-796">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-796">The preceding code:</span></span>

* <span data-ttu-id="c6caa-797">删除未使用的 `using` 声明。</span><span class="sxs-lookup"><span data-stu-id="c6caa-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="c6caa-798">将数据库上下文添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="c6caa-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="c6caa-799">指定数据库上下文将使用内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="c6caa-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="c6caa-800">添加控制器 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-802">右键单击 Controllers 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="c6caa-803">选择“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="c6caa-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="c6caa-804">在“添加新项”对话框中，选择“API 控制器类”模板 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="c6caa-805">将类命名为 TodoController，然后选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![“添加新项”对话框，“控制器”显示在搜索框中，并且“Web API 控制器”已选中](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-807">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c6caa-808">在“控制器”文件夹中，创建名为 `TodoController` 的类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="c6caa-809">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="c6caa-810">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="c6caa-810">The preceding code:</span></span>

* <span data-ttu-id="c6caa-811">定义了没有方法的 API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="c6caa-812">使用 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性标记类。</span><span class="sxs-lookup"><span data-stu-id="c6caa-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="c6caa-813">此属性指示控制器响应 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="c6caa-814">有关该属性启用的特定行为的信息，请参阅 <xref:web-api/index>。</span><span class="sxs-lookup"><span data-stu-id="c6caa-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="c6caa-815">使用 DI 将数据库上下文 (`TodoContext`) 注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="c6caa-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="c6caa-816">数据库上下文将在控制器中的每个 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 方法中使用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="c6caa-817">如果数据库为空，则将名为 `Item1` 的项添加到数据库。</span><span class="sxs-lookup"><span data-stu-id="c6caa-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="c6caa-818">此代码位于构造函数中，因此在每次出现新 HTTP 请求时运行。</span><span class="sxs-lookup"><span data-stu-id="c6caa-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="c6caa-819">如果删除所有项，则构造函数会在下次调用 API 方法时再次创建 `Item1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="c6caa-820">因此删除可能看上去不起作用，不过实际上确实有效。</span><span class="sxs-lookup"><span data-stu-id="c6caa-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="c6caa-821">添加 Get 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-821">Add Get methods 2.1</span></span>

<span data-ttu-id="c6caa-822">若要提供检索待办事项的 API，请将以下方法添加到 `TodoController` 类中：</span><span class="sxs-lookup"><span data-stu-id="c6caa-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="c6caa-823">这些方法实现两个 GET 终结点：</span><span class="sxs-lookup"><span data-stu-id="c6caa-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="c6caa-824">如果应用仍在运行，请停止它。</span><span class="sxs-lookup"><span data-stu-id="c6caa-824">Stop the app if it's still running.</span></span> <span data-ttu-id="c6caa-825">然后再次运行它以包括最新更改。</span><span class="sxs-lookup"><span data-stu-id="c6caa-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="c6caa-826">通过从浏览器调用两个终结点来测试应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="c6caa-827">例如：</span><span class="sxs-lookup"><span data-stu-id="c6caa-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="c6caa-828">以下 HTTP 响应通过调用 `GetTodoItems` 来生成：</span><span class="sxs-lookup"><span data-stu-id="c6caa-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="c6caa-829">路由和 URL 路径 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="c6caa-830">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性表示响应 HTTP GET 请求的方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="c6caa-831">每个方法的 URL 路径构造如下所示：</span><span class="sxs-lookup"><span data-stu-id="c6caa-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="c6caa-832">在控制器的 `Route` 属性中以模板字符串开头：</span><span class="sxs-lookup"><span data-stu-id="c6caa-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="c6caa-833">将 `[controller]` 替换为控制器的名称，按照惯例，在控制器类名称中去掉“Controller”后缀。</span><span class="sxs-lookup"><span data-stu-id="c6caa-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="c6caa-834">对于此示例，控制器类名称为“Todo”控制器，因此控制器名称为“todo”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="c6caa-835">ASP.NET Core [路由](xref:mvc/controllers/routing)不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c6caa-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="c6caa-836">如果 `[HttpGet]` 属性具有路由模板（例如 `[HttpGet("products")]`），则将它追加到路径。</span><span class="sxs-lookup"><span data-stu-id="c6caa-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="c6caa-837">此示例不使用模板。</span><span class="sxs-lookup"><span data-stu-id="c6caa-837">This sample doesn't use a template.</span></span> <span data-ttu-id="c6caa-838">有关详细信息，请参阅[使用 Http [Verb] 特性的特性路由](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="c6caa-839">在下面的 `GetTodoItem` 方法中，`"{id}"` 是待办事项的唯一标识符的占位符变量。</span><span class="sxs-lookup"><span data-stu-id="c6caa-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="c6caa-840">调用 `GetTodoItem` 时，URL 中 `"{id}"` 的值会在 `id` 参数中提供给方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="c6caa-841">返回值 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-841">Return values 2.1</span></span>

<span data-ttu-id="c6caa-842">`GetTodoItems` 和 `GetTodoItem` 方法的返回类型是 [ActionResult\<T> 类型](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="c6caa-843">ASP.NET Core 自动将对象序列化为 [JSON](https://www.json.org/)，并将 JSON 写入响应消息的正文中。</span><span class="sxs-lookup"><span data-stu-id="c6caa-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="c6caa-844">在假设没有未经处理的异常的情况下，此返回类型的响应代码为 200。</span><span class="sxs-lookup"><span data-stu-id="c6caa-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="c6caa-845">未经处理的异常将转换为 5xx 错误。</span><span class="sxs-lookup"><span data-stu-id="c6caa-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="c6caa-846">`ActionResult` 返回类型可以表示大范围的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="c6caa-847">例如，`GetTodoItem` 可以返回两个不同的状态值：</span><span class="sxs-lookup"><span data-stu-id="c6caa-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="c6caa-848">如果没有任何项与请求的 ID 匹配，则该方法将返回 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> 错误代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="c6caa-849">否则，此方法将返回具有 JSON 响应正文的 200。</span><span class="sxs-lookup"><span data-stu-id="c6caa-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="c6caa-850">返回 `item` 则产生 HTTP 200 响应。</span><span class="sxs-lookup"><span data-stu-id="c6caa-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="c6caa-851">测试 GetTodoItems 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="c6caa-852">本教程使用 Postman 测试 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="c6caa-853">安装 [Postman](https://www.getpostman.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="c6caa-854">启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-854">Start the web app.</span></span>
* <span data-ttu-id="c6caa-855">启动 Postman。</span><span class="sxs-lookup"><span data-stu-id="c6caa-855">Start Postman.</span></span>
* <span data-ttu-id="c6caa-856">禁用 **SSL 证书验证**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c6caa-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c6caa-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c6caa-858">在“文件”>“设置”（“常规” 选项卡）中，禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c6caa-859">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c6caa-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c6caa-860">在“Postman” > “首选项”（“常规”选项卡）中，禁用“SSL 证书验证”   。</span><span class="sxs-lookup"><span data-stu-id="c6caa-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="c6caa-861">或者，选择扳手图标并选择“设置”，然后禁用“SSL 证书验证”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="c6caa-862">在测试控制器之后重新启用 SSL 证书验证。</span><span class="sxs-lookup"><span data-stu-id="c6caa-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="c6caa-863">创建新请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-863">Create a new request.</span></span>
  * <span data-ttu-id="c6caa-864">将 HTTP 方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="c6caa-865">将请求 URI 设置为 `https://localhost:<port>/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="c6caa-866">例如 `https://localhost:5001/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="c6caa-867">在 Postman 中设置 **Two pane view** 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="c6caa-868">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-868">Select **Send**.</span></span>

![使用 Get 请求的 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="c6caa-870">添加 Create 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-870">Add a Create method 2.1</span></span>

<span data-ttu-id="c6caa-871">在 Controllers / TodoController.cs 中添加以下 `PostTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="c6caa-872">前面的代码是 HTTP POST 方法，如 [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性所指示。</span><span class="sxs-lookup"><span data-stu-id="c6caa-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="c6caa-873">该方法从 HTTP 请求正文获取待办事项的值。</span><span class="sxs-lookup"><span data-stu-id="c6caa-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="c6caa-874">`CreatedAtAction` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="c6caa-875">如果成功，则返回 HTTP 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="c6caa-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="c6caa-876">HTTP 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="c6caa-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="c6caa-877">将 `Location` 标头添加到响应。</span><span class="sxs-lookup"><span data-stu-id="c6caa-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="c6caa-878">`Location` 标头指定新建的待办事项的 URI。</span><span class="sxs-lookup"><span data-stu-id="c6caa-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="c6caa-879">有关详细信息，请参阅[创建的 10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="c6caa-880">引用 `GetTodoItem` 操作以创建 `Location` 标头的 URI。</span><span class="sxs-lookup"><span data-stu-id="c6caa-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="c6caa-881">C# `nameof` 关键字用于避免在 `CreatedAtAction` 调用中硬编码操作名称。</span><span class="sxs-lookup"><span data-stu-id="c6caa-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="c6caa-882">测试 PostTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="c6caa-883">生成项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-883">Build the project.</span></span>
* <span data-ttu-id="c6caa-884">在 Postman 中，将 HTTP 方法设置为 `POST`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="c6caa-885">将 URI 设置为 `https://localhost:<port>/api/Todo`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="c6caa-886">例如 `https://localhost:5001/api/Todo`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="c6caa-887">选择“正文”选项卡。</span><span class="sxs-lookup"><span data-stu-id="c6caa-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="c6caa-888">选择“原始”单选按钮。</span><span class="sxs-lookup"><span data-stu-id="c6caa-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="c6caa-889">将类型设置为 **JSON (application/json)**</span><span class="sxs-lookup"><span data-stu-id="c6caa-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="c6caa-890">在请求正文中，输入待办事项的 JSON：</span><span class="sxs-lookup"><span data-stu-id="c6caa-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="c6caa-891">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-891">Select **Send**.</span></span>

  ![使用创建请求的 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="c6caa-893">如果收到 405 不允许的方法错误，则可能是由于未在添加 `PostTodoItem` 方法之后编译项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="c6caa-894">测试位置标头 URI 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="c6caa-895">在 **Headers** 窗格中选择 **Response** 选项卡。</span><span class="sxs-lookup"><span data-stu-id="c6caa-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="c6caa-896">复制 **Location** 标头值：</span><span class="sxs-lookup"><span data-stu-id="c6caa-896">Copy the **Location** header value:</span></span>

  ![Postman 控制台的“标头”选项卡](first-web-api/_static/pmc2.png)

* <span data-ttu-id="c6caa-898">将方法设置为“GET”。</span><span class="sxs-lookup"><span data-stu-id="c6caa-898">Set the method to GET.</span></span>
* <span data-ttu-id="c6caa-899">将 URI 设置为 `https://localhost:<port>/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="c6caa-900">例如 `https://localhost:5001/api/TodoItems/2`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="c6caa-901">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="c6caa-902">添加 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="c6caa-903">添加以下 `PutTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="c6caa-904">`PutTodoItem` 与 `PostTodoItem` 类似，但是使用的是 HTTP PUT。</span><span class="sxs-lookup"><span data-stu-id="c6caa-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="c6caa-905">响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="c6caa-906">根据 HTTP 规范，PUT 请求需要客户端发送整个更新的实体，而不仅仅是更改。</span><span class="sxs-lookup"><span data-stu-id="c6caa-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="c6caa-907">若要支持部分更新，请使用 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="c6caa-908">如果在调用 `PutTodoItem` 时出错，请调用 `GET` 以确保数据库中有项目。</span><span class="sxs-lookup"><span data-stu-id="c6caa-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="c6caa-909">测试 PutTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="c6caa-910">本示例使用内存内、数据库，每次启动应用时都必须对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="c6caa-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="c6caa-911">在进行 PUT 调用之前，数据库中必须有一个项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="c6caa-912">调用 GET，以确保在调用 PUT 之前数据库中存在项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="c6caa-913">更新 ID 为 1 的待办事项并将其名称设置为“feed fish”：</span><span class="sxs-lookup"><span data-stu-id="c6caa-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="c6caa-914">下图显示 Postman 更新：</span><span class="sxs-lookup"><span data-stu-id="c6caa-914">The following image shows the Postman update:</span></span>

![显示 204（无内容）响应的 Postman 控制台](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="c6caa-916">添加 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c6caa-917">添加以下 `DeleteTodoItem` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6caa-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="c6caa-918">`DeleteTodoItem` 响应是 [204（无内容）](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="c6caa-919">测试 DeleteTodoItem 方法 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="c6caa-920">使用 Postman 删除待办事项：</span><span class="sxs-lookup"><span data-stu-id="c6caa-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="c6caa-921">将方法设置为 `DELETE`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="c6caa-922">设置要删除的对象的 URI，例如 `https://localhost:5001/api/todo/1`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="c6caa-923">选择 **Send**。</span><span class="sxs-lookup"><span data-stu-id="c6caa-923">Select **Send**.</span></span>

<span data-ttu-id="c6caa-924">可通过示例应用删除所有项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="c6caa-925">但如果删除最后一项，则在下次调用 API 时，模型类构造函数会创建一个新项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="c6caa-926">使用 JavaScript 调用 Web API 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="c6caa-927">在本部分中，将添加一个 HTML 页面，该页面使用 JavaScript 调用 Web API。</span><span class="sxs-lookup"><span data-stu-id="c6caa-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="c6caa-928">jQuery 可启动该请求。</span><span class="sxs-lookup"><span data-stu-id="c6caa-928">jQuery initiates the request.</span></span> <span data-ttu-id="c6caa-929">JavaScript 会使用 Web API 响应的详细信息来更新页面。</span><span class="sxs-lookup"><span data-stu-id="c6caa-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="c6caa-930">通过下面突出显示的代码更新 Startup.cs，配置应用来[提供静态文件](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)并[实现默认文件映射](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)：</span><span class="sxs-lookup"><span data-stu-id="c6caa-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="c6caa-931">在项目目录中创建 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c6caa-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="c6caa-932">将一个名为 index.html 的 HTML 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="c6caa-933">用以下标记替代其内容：</span><span class="sxs-lookup"><span data-stu-id="c6caa-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="c6caa-934">将名为 site.js 的 JavaScript 文件添加到 wwwroot 目录 。</span><span class="sxs-lookup"><span data-stu-id="c6caa-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="c6caa-935">用以下代码替代其内容：</span><span class="sxs-lookup"><span data-stu-id="c6caa-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="c6caa-936">可能需要更改 ASP.NET Core 项目的启动设置，以便对 HTML 页面进行本地测试：</span><span class="sxs-lookup"><span data-stu-id="c6caa-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="c6caa-937">打开 Properties\launchSettings.json。</span><span class="sxs-lookup"><span data-stu-id="c6caa-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="c6caa-938">删除 `launchUrl` 以便在项目的默认文件 index.html 强制打开应用。</span><span class="sxs-lookup"><span data-stu-id="c6caa-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="c6caa-939">此示例调用 Web API 的所有 CRUD 方法。</span><span class="sxs-lookup"><span data-stu-id="c6caa-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="c6caa-940">以下是 API 调用的说明。</span><span class="sxs-lookup"><span data-stu-id="c6caa-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="c6caa-941">获取待办事项列表 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="c6caa-942">jQuery 将向 Web API 发送 HTTP GET 请求，该 API 返回表示待办事项的数组的 JSON。</span><span class="sxs-lookup"><span data-stu-id="c6caa-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="c6caa-943">如果请求成功，则调用 `success` 回调函数。</span><span class="sxs-lookup"><span data-stu-id="c6caa-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="c6caa-944">在该回调中使用待办事项信息更新 DOM。</span><span class="sxs-lookup"><span data-stu-id="c6caa-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="c6caa-945">添加待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="c6caa-946">jQuery 发送 HTTP POST 请求，请求正文中包含待办事项。</span><span class="sxs-lookup"><span data-stu-id="c6caa-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="c6caa-947">将 `accepts` 和 `contentType` 选项设置为 `application/json`，以便指定接收和发送的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="c6caa-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="c6caa-948">待办事项使用 [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 转换为 JSON。</span><span class="sxs-lookup"><span data-stu-id="c6caa-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="c6caa-949">当 API 返回成功状态的代码时，将调用 `getData` 函数来更新 HTML 表。</span><span class="sxs-lookup"><span data-stu-id="c6caa-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="c6caa-950">更新待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="c6caa-951">更新待办事项与添加类似。</span><span class="sxs-lookup"><span data-stu-id="c6caa-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="c6caa-952">`url` 更改为添加项的唯一标识符，并且 `type` 为 `PUT`。</span><span class="sxs-lookup"><span data-stu-id="c6caa-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="c6caa-953">删除待办事项 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="c6caa-954">若要删除待办事项，请将 AJAX 调用上的 `type` 设为 `DELETE` 并指定该项在 URL 中的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="c6caa-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="c6caa-955">向 Web API 添加身份验证支持 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="c6caa-956">其他资源 2.1</span><span class="sxs-lookup"><span data-stu-id="c6caa-956">Additional resources 2.1</span></span>

<span data-ttu-id="c6caa-957">[查看或下载本教程的示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="c6caa-958">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="c6caa-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="c6caa-959">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="c6caa-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="c6caa-960">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="c6caa-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
