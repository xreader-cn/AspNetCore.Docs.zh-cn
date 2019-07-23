---
title: ASP.NET Core SignalR 入门
author: bradygaster
description: 在本教程中，创建使用 ASP.NET Core SignalR 的聊天应用。
ms.author: bradyg
ms.custom: mvc
ms.date: 07/08/2019
uid: tutorials/signalr
ms.openlocfilehash: 53d3763a93cc72b6bcf85b64a706500299b3597f
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67893799"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="19c40-103">教程：ASP.NET Core SignalR 入门</span><span class="sxs-lookup"><span data-stu-id="19c40-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="19c40-104">本教程介绍生成使用 SignalR 的实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="19c40-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="19c40-105">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="19c40-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19c40-106">创建 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="19c40-106">Create a web project.</span></span>
> * <span data-ttu-id="19c40-107">添加 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="19c40-108">创建 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="19c40-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="19c40-109">配置项目以使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="19c40-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="19c40-110">添加可将消息从任何客户端发送到所有连接客户端的代码。</span><span class="sxs-lookup"><span data-stu-id="19c40-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="19c40-111">最终将创建一个正常运行的聊天应用：</span><span class="sxs-lookup"><span data-stu-id="19c40-111">At the end, you'll have a working chat app:</span></span>

![SignalR 示例应用](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="19c40-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="19c40-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="19c40-117">创建 Web 应用项目</span><span class="sxs-lookup"><span data-stu-id="19c40-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="19c40-119">从菜单中选择“文件”>“新建项目”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="19c40-120">在“创建新项目”对话框中，选择“ASP.NET Core Web 应用程序”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="19c40-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="19c40-121">在“配置新项目”对话框中，为项目 SignalRChat 命名，然后选择“创建”    。</span><span class="sxs-lookup"><span data-stu-id="19c40-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="19c40-122">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择“.NET Core”和“ASP.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="19c40-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="19c40-123">选择“Web 应用程序”以创建使用 Razor Pages 的项目，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="19c40-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="19c40-126">在将要在其中创建新项目文件夹的文件夹中打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="19c40-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="19c40-127">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="19c40-127">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19c40-129">从菜单中选择“文件”>“新建解决方案”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="19c40-130">选择“.NET Core”>“应用”>“Web 应用程序”（不要选择“Web 应用程序(Model-View-Controller)”），然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="19c40-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="19c40-131">确保“目标框架”设置为“.NET Core 3.0”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="19c40-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="19c40-132">将项目命名为“SignalRChat”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="19c40-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="19c40-133">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="19c40-133">Add the SignalR client library</span></span>

<span data-ttu-id="19c40-134">SignalR 服务器库包含在 ASP.NET Core 3.0 共享框架中。</span><span class="sxs-lookup"><span data-stu-id="19c40-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="19c40-135">JavaScript 客户端库不会自动包含在项目中。</span><span class="sxs-lookup"><span data-stu-id="19c40-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="19c40-136">对于此教程，使用库管理器 (LibMan) 从 unpkg  获取客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="19c40-137">unpkg 是一个内容分发网络 (CDN)，可以分发在 npm（即 Node.js 包管理器）中找到的任何内容。</span><span class="sxs-lookup"><span data-stu-id="19c40-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="19c40-139">在“解决方案资源管理器”  中，右键单击项目，然后选择“添加”  >“客户端库”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="19c40-140">在“添加客户端库”  对话框中，对于“提供程序”  ，选择“unpkg”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="19c40-141">对于“库”  ，输入 `@aspnet/signalr@next`。</span><span class="sxs-lookup"><span data-stu-id="19c40-141">For **Library**, enter `@aspnet/signalr@next`.</span></span>
<!-- when 3.0 is released, change @next to @latest -->

* <span data-ttu-id="19c40-142">选择“选择特定文件”  ，展开“dist/browser”  文件夹，然后选择“signalr.js”  和“signalr.min.js”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="19c40-143">将“目标位置”  设置为 wwwroot/lib/signalr/  ，然后选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-143">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![“添加客户端库”对话框 - 选择库](signalr/_static/3.x/libman1.png)

  <span data-ttu-id="19c40-145">LibMan 创建 wwwroot/lib/signalr  文件夹并将所选文件复制到该文件夹。</span><span class="sxs-lookup"><span data-stu-id="19c40-145">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="19c40-147">在集成终端中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="19c40-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="19c40-148">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="19c40-149">可能需要等待几秒钟的时间才能看到输出。</span><span class="sxs-lookup"><span data-stu-id="19c40-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="19c40-150">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="19c40-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="19c40-151">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="19c40-152">将文件复制到 wwwroot/lib/signalr  目标。</span><span class="sxs-lookup"><span data-stu-id="19c40-152">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="19c40-153">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="19c40-153">Copy only the specified files.</span></span>

  <span data-ttu-id="19c40-154">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="19c40-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19c40-156">在“终端”  中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="19c40-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="19c40-157">导航到项目文件夹（包含 SignalRChat.csproj  文件的文件夹）。</span><span class="sxs-lookup"><span data-stu-id="19c40-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="19c40-158">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="19c40-159">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="19c40-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="19c40-160">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="19c40-161">将文件复制到 wwwroot/lib/signalr  目标。</span><span class="sxs-lookup"><span data-stu-id="19c40-161">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="19c40-162">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="19c40-162">Copy only the specified files.</span></span>

  <span data-ttu-id="19c40-163">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="19c40-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="19c40-164">创建 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="19c40-164">Create a SignalR hub</span></span>

<span data-ttu-id="19c40-165">*中心*是一个类，用作处理客户端 - 服务器通信的高级管道。</span><span class="sxs-lookup"><span data-stu-id="19c40-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="19c40-166">在 SignalRChat 项目文件夹中，创建 Hubs 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="19c40-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="19c40-167">在 Hubs 文件夹中，使用以下代码创建 ChatHub.cs 文件   ：</span><span class="sxs-lookup"><span data-stu-id="19c40-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="19c40-168">`ChatHub` 类继承自 SignalR `Hub` 类。</span><span class="sxs-lookup"><span data-stu-id="19c40-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="19c40-169">`Hub` 类管理连接、组和消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="19c40-170">可通过已连接客户端调用 `SendMessage`，以向所有客户端发送消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="19c40-171">本教程后面部分将显示调用该方法的 JavaScript 客户端代码。</span><span class="sxs-lookup"><span data-stu-id="19c40-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="19c40-172">SignalR 代码是异步模式，可提供最大的可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="19c40-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="19c40-173">配置 SignalR</span><span class="sxs-lookup"><span data-stu-id="19c40-173">Configure SignalR</span></span>

<span data-ttu-id="19c40-174">必须配置 SignalR 服务器，以将 SignalR 请求传递到 SignalR。</span><span class="sxs-lookup"><span data-stu-id="19c40-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="19c40-175">将以下突出显示的代码添加到 Startup.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="19c40-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="19c40-176">这些更改将 SignalR 添加到 ASP.NET Core 依赖关系注入和路由系统。</span><span class="sxs-lookup"><span data-stu-id="19c40-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="19c40-177">添加 SignalR 客户端代码</span><span class="sxs-lookup"><span data-stu-id="19c40-177">Add SignalR client code</span></span>

* <span data-ttu-id="19c40-178">使用以下代码替换 Pages\Index.cshtml 中的内容  ：</span><span class="sxs-lookup"><span data-stu-id="19c40-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="19c40-179">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="19c40-179">The preceding code:</span></span>

  * <span data-ttu-id="19c40-180">创建名称以及消息文本的文本框和“提交”按钮。</span><span class="sxs-lookup"><span data-stu-id="19c40-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="19c40-181">使用 `id="messagesList"` 创建一个列表，用于显示从 SignalR 中心接收的消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="19c40-182">包含对 SignalR 的脚本引用以及在下一步中创建的 chat.js 应用程序代码  。</span><span class="sxs-lookup"><span data-stu-id="19c40-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="19c40-183">在 wwwroot/js 文件夹中，使用以下代码创建 chat.js 文件   ：</span><span class="sxs-lookup"><span data-stu-id="19c40-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="19c40-184">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="19c40-184">The preceding code:</span></span>

  * <span data-ttu-id="19c40-185">创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="19c40-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="19c40-186">向“提交”按钮添加一个用于向中心发送消息的处理程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="19c40-187">向连接对象添加一个用于从中心接收消息并将其添加到列表的处理程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="19c40-188">运行应用</span><span class="sxs-lookup"><span data-stu-id="19c40-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19c40-190">按 Ctrl+F5 可运行应用而不进行调试  。</span><span class="sxs-lookup"><span data-stu-id="19c40-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="19c40-192">在集成终端中，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="19c40-192">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19c40-194">从菜单中选择“运行”>“开始执行(不调试)”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="19c40-195">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="19c40-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="19c40-196">选择任一浏览器，输入名称和消息，然后选择“发送消息”按钮  。</span><span class="sxs-lookup"><span data-stu-id="19c40-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="19c40-197">两个页面上立即显示名称和消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR 示例应用](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="19c40-199">如果应用不起作用，请打开浏览器开发人员工具 (F12) 并转到控制台。</span><span class="sxs-lookup"><span data-stu-id="19c40-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="19c40-200">可能会看到与 HTML 和 JavaScript 代码相关的错误。</span><span class="sxs-lookup"><span data-stu-id="19c40-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="19c40-201">例如，假设将 signalr.js 放在不同于系统指示的文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="19c40-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="19c40-202">在这种情况下，对该文件的引用将不起作用，并且你将在控制台中看到 404 错误。</span><span class="sxs-lookup"><span data-stu-id="19c40-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="19c40-203">![未找到 signalr.js 错误](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="19c40-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="19c40-204">如果 Chrome 中出现 ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY 错误或 Firefox 中出现 NS_ERROR_NET_INADEQUATE_SECURITY 错误，请运行这些命令以更新开发证书：</span><span class="sxs-lookup"><span data-stu-id="19c40-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome or NS_ERROR_NET_INADEQUATE_SECURITY in Firefox, run these commands to update your development certificate:</span></span>
>   ```
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="19c40-205">后续步骤</span><span class="sxs-lookup"><span data-stu-id="19c40-205">Next steps</span></span>

<span data-ttu-id="19c40-206">若要详细了解 SignalR，请参阅简介：</span><span class="sxs-lookup"><span data-stu-id="19c40-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="19c40-207">ASP.NET Core SignalR 简介</span><span class="sxs-lookup"><span data-stu-id="19c40-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="19c40-208">本教程介绍生成使用 SignalR 的实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="19c40-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="19c40-209">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="19c40-209">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19c40-210">创建 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="19c40-210">Create a web project.</span></span>
> * <span data-ttu-id="19c40-211">添加 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-211">Add the SignalR client library.</span></span>
> * <span data-ttu-id="19c40-212">创建 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="19c40-212">Create a SignalR hub.</span></span>
> * <span data-ttu-id="19c40-213">配置项目以使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="19c40-213">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="19c40-214">添加可将消息从任何客户端发送到所有连接客户端的代码。</span><span class="sxs-lookup"><span data-stu-id="19c40-214">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="19c40-215">最终将创建一个正常运行的聊天应用：</span><span class="sxs-lookup"><span data-stu-id="19c40-215">At the end, you'll have a working chat app:</span></span>

![SignalR 示例应用](signalr/_static/2.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="19c40-217">系统必备</span><span class="sxs-lookup"><span data-stu-id="19c40-217">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-218">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-220">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="19c40-221">创建 Web 项目</span><span class="sxs-lookup"><span data-stu-id="19c40-221">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-222">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="19c40-223">从菜单中选择“文件”>“新建项目”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-223">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="19c40-224">在“新建项目”对话框中，选择“已安装”>“Visual C#”>“Web”>“ASP.NET Core Web 应用”   。</span><span class="sxs-lookup"><span data-stu-id="19c40-224">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="19c40-225">将项目命名为“SignalRChat”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-225">Name the project *SignalRChat*.</span></span>

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/2.x/signalr-new-project-dialog.png)

* <span data-ttu-id="19c40-227">选择“Web 应用”，以创建使用 Razor Pages 的项目  。</span><span class="sxs-lookup"><span data-stu-id="19c40-227">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="19c40-228">选择“.NET Core”目标框架，选择“ASP.NET Core 2.2”，然后单击“确定”    。</span><span class="sxs-lookup"><span data-stu-id="19c40-228">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/2.x/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-230">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="19c40-231">在将要在其中创建新项目文件夹的文件夹中打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="19c40-231">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="19c40-232">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="19c40-232">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-233">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-233">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19c40-234">从菜单中选择“文件”>“新建解决方案”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-234">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="19c40-235">选择“.NET Core”>“应用”>“ASP.NET Core Web 应用”（请勿选择 ASP.NET Core Web 应用 (MVC)）   。</span><span class="sxs-lookup"><span data-stu-id="19c40-235">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="19c40-236">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-236">Select **Next**.</span></span>

* <span data-ttu-id="19c40-237">将项目命名为“SignalRChat”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="19c40-237">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="19c40-238">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="19c40-238">Add the SignalR client library</span></span>

<span data-ttu-id="19c40-239">`Microsoft.AspNetCore.App` 元包中包括 SignalR 服务器库。</span><span class="sxs-lookup"><span data-stu-id="19c40-239">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="19c40-240">JavaScript 客户端库不会自动包含在项目中。</span><span class="sxs-lookup"><span data-stu-id="19c40-240">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="19c40-241">对于此教程，使用库管理器 (LibMan) 从 unpkg  获取客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-241">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="19c40-242">unpkg 是一个内容分发网络 (CDN)，可以分发在 npm（即 Node.js 包管理器）中找到的任何内容。</span><span class="sxs-lookup"><span data-stu-id="19c40-242">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-243">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="19c40-244">在“解决方案资源管理器”  中，右键单击项目，然后选择“添加”  >“客户端库”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-244">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="19c40-245">在“添加客户端库”  对话框中，对于“提供程序”  ，选择“unpkg”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-245">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="19c40-246">对于“库”  ，输入 `@aspnet/signalr@1`，然后选择不是预览版的最新版本。</span><span class="sxs-lookup"><span data-stu-id="19c40-246">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![“添加客户端库”对话框 - 选择库](signalr/_static/2.x/libman1.png)

* <span data-ttu-id="19c40-248">选择“选择特定文件”  ，展开“dist/browser”  文件夹，然后选择“signalr.js”  和“signalr.min.js”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-248">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="19c40-249">将“目标位置”  设置为 wwwroot/lib/signalr/  ，然后选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-249">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![“添加客户端库”对话框 - 选择文件和目标](signalr/_static/2.x/libman2.png)

  <span data-ttu-id="19c40-251">LibMan 创建 wwwroot/lib/signalr  文件夹并将所选文件复制到该文件夹。</span><span class="sxs-lookup"><span data-stu-id="19c40-251">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-252">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="19c40-253">在集成终端中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="19c40-253">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="19c40-254">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-254">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="19c40-255">可能需要等待几秒钟的时间才能看到输出。</span><span class="sxs-lookup"><span data-stu-id="19c40-255">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="19c40-256">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="19c40-256">The parameters specify the following options:</span></span>
  * <span data-ttu-id="19c40-257">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-257">Use the unpkg provider.</span></span>
  * <span data-ttu-id="19c40-258">将文件复制到 wwwroot/lib/signalr  目标。</span><span class="sxs-lookup"><span data-stu-id="19c40-258">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="19c40-259">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="19c40-259">Copy only the specified files.</span></span>

  <span data-ttu-id="19c40-260">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="19c40-260">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-261">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19c40-262">在“终端”  中，运行以下命令以安装 LibMan。</span><span class="sxs-lookup"><span data-stu-id="19c40-262">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="19c40-263">导航到项目文件夹（包含 SignalRChat.csproj  文件的文件夹）。</span><span class="sxs-lookup"><span data-stu-id="19c40-263">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="19c40-264">使用 LibMan 运行以下命令，以获取 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-264">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="19c40-265">参数指定以下选项：</span><span class="sxs-lookup"><span data-stu-id="19c40-265">The parameters specify the following options:</span></span>
  * <span data-ttu-id="19c40-266">使用 unpkg 提供程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-266">Use the unpkg provider.</span></span>
  * <span data-ttu-id="19c40-267">将文件复制到 wwwroot/lib/signalr  目标。</span><span class="sxs-lookup"><span data-stu-id="19c40-267">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="19c40-268">仅复制指定的文件。</span><span class="sxs-lookup"><span data-stu-id="19c40-268">Copy only the specified files.</span></span>

  <span data-ttu-id="19c40-269">输出如下所示：</span><span class="sxs-lookup"><span data-stu-id="19c40-269">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="19c40-270">创建 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="19c40-270">Create a SignalR hub</span></span>

<span data-ttu-id="19c40-271">*中心*是一个类，用作处理客户端 - 服务器通信的高级管道。</span><span class="sxs-lookup"><span data-stu-id="19c40-271">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="19c40-272">在 SignalRChat 项目文件夹中，创建 Hubs 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="19c40-272">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="19c40-273">在 Hubs 文件夹中，使用以下代码创建 ChatHub.cs 文件   ：</span><span class="sxs-lookup"><span data-stu-id="19c40-273">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]

  <span data-ttu-id="19c40-274">`ChatHub` 类继承自 SignalR `Hub` 类。</span><span class="sxs-lookup"><span data-stu-id="19c40-274">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="19c40-275">`Hub` 类管理连接、组和消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-275">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="19c40-276">可通过已连接客户端调用 `SendMessage`，以向所有客户端发送消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-276">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="19c40-277">本教程后面部分将显示调用该方法的 JavaScript 客户端代码。</span><span class="sxs-lookup"><span data-stu-id="19c40-277">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="19c40-278">SignalR 代码是异步模式，可提供最大的可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="19c40-278">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="19c40-279">配置 SignalR</span><span class="sxs-lookup"><span data-stu-id="19c40-279">Configure SignalR</span></span>

<span data-ttu-id="19c40-280">必须配置 SignalR 服务器，以将 SignalR 请求传递到 SignalR。</span><span class="sxs-lookup"><span data-stu-id="19c40-280">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="19c40-281">将以下突出显示的代码添加到 Startup.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="19c40-281">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="19c40-282">这些更改将 SignalR 添加到 ASP.NET Core 依赖关系注入系统和中间件管道。</span><span class="sxs-lookup"><span data-stu-id="19c40-282">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="19c40-283">添加 SignalR 客户端代码</span><span class="sxs-lookup"><span data-stu-id="19c40-283">Add SignalR client code</span></span>

* <span data-ttu-id="19c40-284">使用以下代码替换 Pages\Index.cshtml 中的内容  ：</span><span class="sxs-lookup"><span data-stu-id="19c40-284">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]

  <span data-ttu-id="19c40-285">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="19c40-285">The preceding code:</span></span>

  * <span data-ttu-id="19c40-286">创建名称以及消息文本的文本框和“提交”按钮。</span><span class="sxs-lookup"><span data-stu-id="19c40-286">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="19c40-287">使用 `id="messagesList"` 创建一个列表，用于显示从 SignalR 中心接收的消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-287">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="19c40-288">包含对 SignalR 的脚本引用以及在下一步中创建的 chat.js 应用程序代码  。</span><span class="sxs-lookup"><span data-stu-id="19c40-288">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="19c40-289">在 wwwroot/js 文件夹中，使用以下代码创建 chat.js 文件   ：</span><span class="sxs-lookup"><span data-stu-id="19c40-289">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]

  <span data-ttu-id="19c40-290">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="19c40-290">The preceding code:</span></span>

  * <span data-ttu-id="19c40-291">创建并启动连接。</span><span class="sxs-lookup"><span data-stu-id="19c40-291">Creates and starts a connection.</span></span>
  * <span data-ttu-id="19c40-292">向“提交”按钮添加一个用于向中心发送消息的处理程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-292">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="19c40-293">向连接对象添加一个用于从中心接收消息并将其添加到列表的处理程序。</span><span class="sxs-lookup"><span data-stu-id="19c40-293">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="19c40-294">运行应用</span><span class="sxs-lookup"><span data-stu-id="19c40-294">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="19c40-295">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="19c40-295">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="19c40-296">按 Ctrl+F5 可运行应用而不进行调试  。</span><span class="sxs-lookup"><span data-stu-id="19c40-296">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="19c40-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="19c40-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="19c40-298">在集成终端中，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="19c40-298">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="19c40-299">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="19c40-299">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="19c40-300">从菜单中选择“运行”>“开始执行(不调试)”  。</span><span class="sxs-lookup"><span data-stu-id="19c40-300">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="19c40-301">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="19c40-301">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="19c40-302">选择任一浏览器，输入名称和消息，然后选择“发送消息”按钮  。</span><span class="sxs-lookup"><span data-stu-id="19c40-302">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="19c40-303">两个页面上立即显示名称和消息。</span><span class="sxs-lookup"><span data-stu-id="19c40-303">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR 示例应用](signalr/_static/2.x/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="19c40-305">如果应用不起作用，请打开浏览器开发人员工具 (F12) 并转到控制台。</span><span class="sxs-lookup"><span data-stu-id="19c40-305">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="19c40-306">可能会看到与 HTML 和 JavaScript 代码相关的错误。</span><span class="sxs-lookup"><span data-stu-id="19c40-306">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="19c40-307">例如，假设将 signalr.js 放在不同于系统指示的文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="19c40-307">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="19c40-308">在这种情况下，对该文件的引用将不起作用，并且你将在控制台中看到 404 错误。</span><span class="sxs-lookup"><span data-stu-id="19c40-308">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="19c40-309">![未找到 signalr.js 错误](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="19c40-309">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>

## <a name="next-steps"></a><span data-ttu-id="19c40-310">后续步骤</span><span class="sxs-lookup"><span data-stu-id="19c40-310">Next steps</span></span>

<span data-ttu-id="19c40-311">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="19c40-311">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="19c40-312">创建 Web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="19c40-312">Create a web app project.</span></span>
> * <span data-ttu-id="19c40-313">添加 SignalR 客户端库。</span><span class="sxs-lookup"><span data-stu-id="19c40-313">Add the SignalR client library.</span></span>
> * <span data-ttu-id="19c40-314">创建 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="19c40-314">Create a SignalR hub.</span></span>
> * <span data-ttu-id="19c40-315">配置项目以使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="19c40-315">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="19c40-316">添加代码，以便使用此中心将消息从任何客户端发送到所有连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="19c40-316">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="19c40-317">若要详细了解 SignalR，请参阅简介：</span><span class="sxs-lookup"><span data-stu-id="19c40-317">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="19c40-318">ASP.NET Core SignalR 简介</span><span class="sxs-lookup"><span data-stu-id="19c40-318">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end
