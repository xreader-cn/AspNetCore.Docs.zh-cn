---
title: ASP.NET Core SignalR 入门
author: bradygaster
description: 在本教程中，创建使用 ASP.NET Core SignalR 的聊天应用。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/30/2018
uid: tutorials/signalr
ms.openlocfilehash: ba36f75dfa40012df3140cdcbf8a7732fb0709fe
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264541"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="ec13d-103">教程：ASP.NET Core SignalR 入门</span><span class="sxs-lookup"><span data-stu-id="ec13d-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="ec13d-104">本教程介绍生成使用 SignalR 的实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="ec13d-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="ec13d-105">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="ec13d-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ec13d-106">创建 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="ec13d-106">Create a web project.</span></span>
> * <span data-ttu-id="ec13d-107">添加 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="ec13d-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="ec13d-108">创建 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="ec13d-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="ec13d-109">配置项目以使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="ec13d-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="ec13d-110">添加可将消息从任何客户端发送到所有连接客户端的代码。</span><span class="sxs-lookup"><span data-stu-id="ec13d-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="ec13d-111">最终将创建一个正常运行的聊天应用：</span><span class="sxs-lookup"><span data-stu-id="ec13d-111">At the end, you'll have a working chat app:</span></span>

![SignalR 示例应用](signalr/_static/signalr-get-started-finished.png)

<span data-ttu-id="ec13d-113">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="ec13d-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

[!INCLUDE [|Prerequisites](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="ec13d-114">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="ec13d-114">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ec13d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec13d-115">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ec13d-116">从菜单中选择“文件”>“新建项目”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-116">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="ec13d-117">在“新建项目”对话框中，选择“已安装”>“Visual C#”>“Web”>“ASP.NET Core Web 应用”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-117">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="ec13d-118">将项目命名为“SignalRChat”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-118">Name the project *SignalRChat*.</span></span>

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/signalr-new-project-dialog.png)

* <span data-ttu-id="ec13d-120">选择“Web 应用”，以创建使用 Razor Pages 的项目。</span><span class="sxs-lookup"><span data-stu-id="ec13d-120">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="ec13d-121">选择“.NET Core”目标框架，选择“ASP.NET Core 2.2”，然后单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-121">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ec13d-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec13d-123">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ec13d-124">在将要在其中创建新项目文件夹的文件夹中打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="ec13d-124">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="ec13d-125">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ec13d-125">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ec13d-126">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec13d-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ec13d-127">从菜单中选择“文件”>“新建解决方案”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-127">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="ec13d-128">选择“.NET Core”>“应用”>“ASP.NET Core Web 应用”（请勿选择 ASP.NET Core Web 应用 (MVC)）。</span><span class="sxs-lookup"><span data-stu-id="ec13d-128">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="ec13d-129">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-129">Select **Next**.</span></span>

* <span data-ttu-id="ec13d-130">将项目命名为“SignalRChat”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-130">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="ec13d-131">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="ec13d-131">Add the SignalR client library</span></span>

<span data-ttu-id="ec13d-132">`Microsoft.AspNetCore.App` 元包中包括 SignalR 服务器库。</span><span class="sxs-lookup"><span data-stu-id="ec13d-132">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="ec13d-133">JavaScript 客户端库不会自动包含在项目中。</span><span class="sxs-lookup"><span data-stu-id="ec13d-133">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="ec13d-134">对于此教程，使用库管理器 (LibMan) 从 unpkg 获取客户端库。</span><span class="sxs-lookup"><span data-stu-id="ec13d-134">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="ec13d-135">unpkg 是一个内容分发网络 (CDN)，可以分发在 npm（即 Node.js 包管理器）中找到的任何内容。</span><span class="sxs-lookup"><span data-stu-id="ec13d-135">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ec13d-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec13d-136">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="ec13d-137">在“解决方案资源管理器”中，右键单击项目，然后选择“添加” > “客户端库”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-137">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="ec13d-138">在“添加客户端库”对话框中，对于“提供程序”，选择“unpkg”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-138">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="ec13d-139">对于“库”，输入 `@aspnet/signalr@1`，然后选择不是预览版的最新版本。</span><span class="sxs-lookup"><span data-stu-id="ec13d-139">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![“添加客户端库”对话框 - 选择库](signalr/_static/libman1.png)

* <span data-ttu-id="ec13d-141">选择“选择特定文件”，展开“dist/browser”文件夹，然后选择“signalr.js”和“signalr.min.js”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-141">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="ec13d-142">将“目标位置”设置为 wwwroot/lib/signalr/，然后选择“安装”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-142">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![“添加客户端库”对话框 - 选择文件和目标](signalr/_static/libman2.png)

  <span data-ttu-id="ec13d-144">LibMan 创建 wwwroot/lib/signalr 文件夹并将所选文件复制到该文件夹。</span><span class="sxs-lookup"><span data-stu-id="ec13d-144">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ec13d-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec13d-145">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="ec13d-146">在集成终端中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="ec13d-146">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ec13d-147">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="ec13d-147">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="ec13d-148">可能需要等待几秒钟的时间才能看到输出。</span><span class="sxs-lookup"><span data-stu-id="ec13d-148">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ec13d-149">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="ec13d-149">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ec13d-150">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="ec13d-150">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ec13d-151">将文件复制到 wwwroot/lib/signalr 目标。</span><span class="sxs-lookup"><span data-stu-id="ec13d-151">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="ec13d-152">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="ec13d-152">Copy only the specified files.</span></span>

  <span data-ttu-id="ec13d-153">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="ec13d-153">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ec13d-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec13d-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ec13d-155">在“终端”中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="ec13d-155">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="ec13d-156">导航到项目文件夹（包含 SignalRChat.csproj 文件的文件夹）。</span><span class="sxs-lookup"><span data-stu-id="ec13d-156">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="ec13d-157">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="ec13d-157">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="ec13d-158">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="ec13d-158">The parameters specify the following options:</span></span>
  * <span data-ttu-id="ec13d-159">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="ec13d-159">Use the unpkg provider.</span></span>
  * <span data-ttu-id="ec13d-160">将文件复制到 wwwroot/lib/signalr 目标。</span><span class="sxs-lookup"><span data-stu-id="ec13d-160">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="ec13d-161">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="ec13d-161">Copy only the specified files.</span></span>

  <span data-ttu-id="ec13d-162">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="ec13d-162">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="ec13d-163">创建 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="ec13d-163">Create a SignalR hub</span></span>

<span data-ttu-id="ec13d-164">*中心*是一个类，用作处理客户端 - 服务器通信的高级管道。</span><span class="sxs-lookup"><span data-stu-id="ec13d-164">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="ec13d-165">在 SignalRChat 项目文件夹中，创建 Hubs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ec13d-165">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="ec13d-166">在 Hubs 文件夹中，使用以下代码创建 ChatHub.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="ec13d-166">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample/Hubs/ChatHub.cs)]

  <span data-ttu-id="ec13d-167">`ChatHub` 类继承自 SignalR `Hub` 类。</span><span class="sxs-lookup"><span data-stu-id="ec13d-167">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="ec13d-168">`Hub` 类管理连接、组和消息。</span><span class="sxs-lookup"><span data-stu-id="ec13d-168">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="ec13d-169">可通过已连接客户端调用 `SendMessage`，以向所有客户端发送消息。</span><span class="sxs-lookup"><span data-stu-id="ec13d-169">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="ec13d-170">本教程后面部分将显示调用该方法的 JavaScript 客户端代码。</span><span class="sxs-lookup"><span data-stu-id="ec13d-170">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="ec13d-171">SignalR 代码是异步模式，可提供最大的可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="ec13d-171">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="ec13d-172">配置 SignalR</span><span class="sxs-lookup"><span data-stu-id="ec13d-172">Configure SignalR</span></span>

<span data-ttu-id="ec13d-173">必须配置 SignalR 服务器，以将 SignalR 请求传递到 SignalR。</span><span class="sxs-lookup"><span data-stu-id="ec13d-173">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="ec13d-174">将以下突出显示的代码添加到 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="ec13d-174">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="ec13d-175">这些更改将 SignalR 添加到 ASP.NET Core 依赖关系注入系统和中间件管道。</span><span class="sxs-lookup"><span data-stu-id="ec13d-175">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="ec13d-176">添加 SignalR 客户端代码</span><span class="sxs-lookup"><span data-stu-id="ec13d-176">Add SignalR client code</span></span>

* <span data-ttu-id="ec13d-177">使用以下代码替换 Pages\Index.cshtml 中的内容：</span><span class="sxs-lookup"><span data-stu-id="ec13d-177">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample/Pages/Index.cshtml)]

  <span data-ttu-id="ec13d-178">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="ec13d-178">The preceding code:</span></span>

  * <span data-ttu-id="ec13d-179">创建名称以及消息文本的文本框和“提交”按钮。</span><span class="sxs-lookup"><span data-stu-id="ec13d-179">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="ec13d-180">使用 `id="messagesList"` 创建一个列表，用于显示从 SignalR 中心接收的消息。</span><span class="sxs-lookup"><span data-stu-id="ec13d-180">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="ec13d-181">包含对 SignalR 的脚本引用以及在下一步中创建的 chat.js 应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="ec13d-181">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="ec13d-182">在 wwwroot/js 文件夹中，使用以下代码创建 chat.js 文件：</span><span class="sxs-lookup"><span data-stu-id="ec13d-182">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample/wwwroot/js/chat.js)]

  <span data-ttu-id="ec13d-183">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="ec13d-183">The preceding code:</span></span>

  * <span data-ttu-id="ec13d-184">创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="ec13d-184">Creates and starts a connection.</span></span>
  * <span data-ttu-id="ec13d-185">向“提交”按钮添加一个用于向中心发送消息的处理程序。</span><span class="sxs-lookup"><span data-stu-id="ec13d-185">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="ec13d-186">向连接对象添加一个用于从中心接收消息并将其添加到列表的处理程序。</span><span class="sxs-lookup"><span data-stu-id="ec13d-186">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ec13d-187">运行应用</span><span class="sxs-lookup"><span data-stu-id="ec13d-187">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ec13d-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec13d-188">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec13d-189">按 Ctrl+F5 可运行应用而不进行调试。</span><span class="sxs-lookup"><span data-stu-id="ec13d-189">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="ec13d-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec13d-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec13d-191">在集成终端中，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ec13d-191">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="ec13d-192">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec13d-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ec13d-193">从菜单中选择“运行”>“开始执行(不调试)”。</span><span class="sxs-lookup"><span data-stu-id="ec13d-193">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="ec13d-194">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="ec13d-194">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="ec13d-195">选择任一浏览器，输入名称和消息，然后选择“发送消息”按钮。</span><span class="sxs-lookup"><span data-stu-id="ec13d-195">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="ec13d-196">两个页面上立即显示名称和消息。</span><span class="sxs-lookup"><span data-stu-id="ec13d-196">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR 示例应用](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="ec13d-198">如果应用不起作用，请打开浏览器开发人员工具 (F12) 并转到控制台。</span><span class="sxs-lookup"><span data-stu-id="ec13d-198">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="ec13d-199">可能会看到与 HTML 和 JavaScript 代码相关的错误。</span><span class="sxs-lookup"><span data-stu-id="ec13d-199">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="ec13d-200">例如，假设将 signalr.js 放在不同于系统指示的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="ec13d-200">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="ec13d-201">在这种情况下，对该文件的引用将不起作用，并且你将在控制台中看到 404 错误。</span><span class="sxs-lookup"><span data-stu-id="ec13d-201">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="ec13d-202">![未找到 signalr.js 错误](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="ec13d-202">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="ec13d-203">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ec13d-203">Next steps</span></span>

<span data-ttu-id="ec13d-204">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="ec13d-204">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ec13d-205">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="ec13d-205">Create a web app project.</span></span>
> * <span data-ttu-id="ec13d-206">添加 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="ec13d-206">Add the SignalR client library.</span></span>
> * <span data-ttu-id="ec13d-207">创建 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="ec13d-207">Create a SignalR hub.</span></span>
> * <span data-ttu-id="ec13d-208">配置项目以使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="ec13d-208">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="ec13d-209">添加代码，以便使用此中心将消息从任何客户端发送到所有连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="ec13d-209">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="ec13d-210">若要详细了解 SignalR，请参阅简介：</span><span class="sxs-lookup"><span data-stu-id="ec13d-210">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ec13d-211">ASP.NET Core SignalR 简介</span><span class="sxs-lookup"><span data-stu-id="ec13d-211">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
