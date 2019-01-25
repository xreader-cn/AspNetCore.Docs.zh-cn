---
uid: signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
title: 在 Azure Web 角色中使用 SignalR 性能计数器 |Microsoft Docs
author: guardrex
description: 了解如何安装并在 Azure Web 角色中使用 SignalR 性能计数器。
keywords: ASP.NET,signalr,performance 计数器，azure web 角色
ms.author: bradyg
ms.date: 10/03/2018
ms.assetid: 2a127d3b-21ed-4cc9-bec0-cdab4e742a25
msc.legacyurl: /signalr/overview/performance/using-signalr-performance-counters-in-an-azure-web-role
msc.type: authoredcontent
ms.openlocfilehash: 8e17e945bc144731dd149bd7ddfc9e29160eaf0b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836476"
---
# <a name="using-signalr-performance-counters-in-an-azure-web-role"></a><span data-ttu-id="ebb9f-104">在 Azure Web 角色中使用 SignalR 性能计数器</span><span class="sxs-lookup"><span data-stu-id="ebb9f-104">Using SignalR performance counters in an Azure Web Role</span></span>

<span data-ttu-id="ebb9f-105">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ebb9f-105">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="ebb9f-106">SignalR 性能计数器用于监视 Azure Web 角色中的应用程序的性能。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-106">SignalR performance counters are used to monitor your app's performance in an Azure Web Role.</span></span> <span data-ttu-id="ebb9f-107">Microsoft Azure 诊断捕获计数器。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-107">The counters are captured by Microsoft Azure Diagnostics.</span></span> <span data-ttu-id="ebb9f-108">与在 Azure 上安装 SignalR 性能计数器*signalr.exe*，用于独立或在本地应用相同的工具。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-108">You install SignalR performance counters on Azure with *signalr.exe*, the same tool used for standalone or on-premises apps.</span></span> <span data-ttu-id="ebb9f-109">Azure 角色是暂时的因为你将配置应用以安装并注册 SignalR 在启动时的性能计数器。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-109">Since Azure roles are transient, you configure an app to install and register SignalR performance counters upon startup.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ebb9f-110">系统必备</span><span class="sxs-lookup"><span data-stu-id="ebb9f-110">Prerequisites</span></span>

* <span data-ttu-id="ebb9f-111">Visual Studio 2015 或[2017年](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)</span><span class="sxs-lookup"><span data-stu-id="ebb9f-111">Visual Studio 2015 or [2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)</span></span>
* <span data-ttu-id="ebb9f-112">[用于 Visual Studio 的 Microsoft Azure SDK](https://azure.microsoft.com/downloads/) **注意：安装 SDK 之后重新启动计算机。**</span><span class="sxs-lookup"><span data-stu-id="ebb9f-112">[Microsoft Azure SDK for Visual Studio](https://azure.microsoft.com/downloads/) **Note: Restart your machine after installing the SDK.**</span></span>
* <span data-ttu-id="ebb9f-113">Microsoft Azure 订阅：若要注册一个免费 Azure 试用帐户，请参阅[Azure 免费试用](https://azure.microsoft.com/free/)。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-113">Microsoft Azure subscription: To sign up for a free Azure trial account, see [Azure Free Trial](https://azure.microsoft.com/free/).</span></span>

## <a name="creating-an-azure-web-role-application-that-exposes-signalr-performance-counters"></a><span data-ttu-id="ebb9f-114">创建 Azure Web 角色应用程序公开 SignalR 性能计数器</span><span class="sxs-lookup"><span data-stu-id="ebb9f-114">Creating an Azure Web Role application that exposes SignalR performance counters</span></span>

1. <span data-ttu-id="ebb9f-115">打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-115">Open Visual Studio.</span></span>

2. <span data-ttu-id="ebb9f-116">在 Visual Studio 中，选择“文件” > “新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-116">In Visual Studio, select **File** > **New** > **Project**.</span></span>

3. <span data-ttu-id="ebb9f-117">在**新的项目**对话框中，选择**Visual C#** > **云**左边的类别，然后选择**Azure 云服务**模板。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-117">In the **New Project** dialog box, select the **Visual C#** > **Cloud** category on the left, and then select the **Azure Cloud Service** template.</span></span> <span data-ttu-id="ebb9f-118">命名应用程序**SignalRPerfCounters** ，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-118">Name the app **SignalRPerfCounters** and select **OK**.</span></span>

   ![新的云应用程序](using-signalr-performance-counters-in-an-azure-web-role/_static/image1.png)

   > [!NOTE]
   > <span data-ttu-id="ebb9f-120">如果没有看到**云**模板类别或**Azure 云服务**模板中，你需要安装**Azure 开发**Visual Studio 2017 工作负载。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-120">If you don't see the **Cloud** template category or the **Azure Cloud Service** template, you need to install the **Azure development** workload for Visual Studio 2017.</span></span> <span data-ttu-id="ebb9f-121">选择**打开 Visual Studio 安装程序**左下方的链接**新项目**对话框打开 Visual Studio 安装程序。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-121">Choose the **Open Visual Studio Installer** link on the bottom-left side of the **New Project** dialog to open Visual Studio Installer.</span></span> <span data-ttu-id="ebb9f-122">选择**Azure 开发**工作负荷中，然后选择**修改**以启动安装工作负载。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-122">Select the **Azure development** workload, and then choose **Modify** to start installing the workload.</span></span>
   >
   > ![在 Visual Studio 安装程序中的 azure 开发工作负载](using-signalr-performance-counters-in-an-azure-web-role/_static/azure-development-workload.png)

4. <span data-ttu-id="ebb9f-124">在中**新的 Microsoft Azure 云服务**对话框中，选择**ASP.NET Web 角色**和选择 > 按钮以将角色添加到项目。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-124">In the **New Microsoft Azure Cloud Service** dialog, select **ASP.NET Web Role** and select the > button to add the role to the project.</span></span> <span data-ttu-id="ebb9f-125">选择 **确定**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-125">Select **OK**.</span></span>

   ![添加 ASP.NET Web 角色](using-signalr-performance-counters-in-an-azure-web-role/_static/image2.png)

5. <span data-ttu-id="ebb9f-127">在中**新 ASP.NET Web 应用程序-WebRole1**对话框中，选择**MVC**模板，，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-127">In the **New ASP.NET Web Application - WebRole1** dialog, select the **MVC** template, and then select **OK**.</span></span>

   ![添加 MVC 和 Web API](using-signalr-performance-counters-in-an-azure-web-role/_static/image3.png)

6. <span data-ttu-id="ebb9f-129">在中**解决方案资源管理器**，打开*diagnostics.wadcfgx*文件下**WebRole1**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-129">In **Solution Explorer**, open the *diagnostics.wadcfgx* file under **WebRole1**.</span></span>

   ![解决方案资源管理器 diagnostics.wadcfgx](using-signalr-performance-counters-in-an-azure-web-role/_static/image4.png)

7. <span data-ttu-id="ebb9f-131">文件的内容替换为下面的配置并保存文件：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-131">Replace the contents of the file with the following configuration and save the file:</span></span>

   [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample1.xml)]

8. <span data-ttu-id="ebb9f-132">打开**程序包管理器控制台**从**工具** > **NuGet 包管理器**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-132">Open the **Package Manager Console** from **Tools** > **NuGet Package Manager**.</span></span> <span data-ttu-id="ebb9f-133">输入以下命令以安装最新版本的 SignalR 和 SignalR 实用程序包：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-133">Enter the following commands to install the latest version of SignalR and the SignalR utilities package:</span></span>

   [!code-powershell[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample2.ps1)]

9. <span data-ttu-id="ebb9f-134">配置应用到角色实例安装的 SignalR 性能计数器，它启动或回收时。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-134">Configure the app to install the SignalR performance counters into the role instance when it starts up or recycles.</span></span> <span data-ttu-id="ebb9f-135">在中**解决方案资源管理器**，右键单击**WebRole1**项目，然后选择**添加** > **新文件夹**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-135">In **Solution Explorer**, right-click on the **WebRole1** project and select **Add** > **New Folder**.</span></span> <span data-ttu-id="ebb9f-136">将新文件夹命名*启动*。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-136">Name the new folder *Startup*.</span></span>

   ![添加启动文件夹](using-signalr-performance-counters-in-an-azure-web-role/_static/image5.png)

10. <span data-ttu-id="ebb9f-138">复制*signalr.exe*文件 (与添加**Microsoft.AspNet.SignalR.Utils**包) 从\<项目文件夹 > / SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils。\<版本 > / 工具，以*启动*上一步中创建的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-138">Copy the *signalr.exe* file (added with the **Microsoft.AspNet.SignalR.Utils** package) from \<project folder>/SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils.\<version>/tools to the *Startup* folder you created in the previous step.</span></span>

11. <span data-ttu-id="ebb9f-139">在中**解决方案资源管理器**，右键单击*启动*文件夹，然后选择**添加** > **现有项**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-139">In **Solution Explorer**, right-click the *Startup* folder and select **Add** > **Existing Item**.</span></span> <span data-ttu-id="ebb9f-140">在出现的对话框，选择*signalr.exe* ，然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-140">In the dialog that appears, select *signalr.exe* and select **Add**.</span></span>

    ![向项目添加 signalr.exe](using-signalr-performance-counters-in-an-azure-web-role/_static/image6.png)

12. <span data-ttu-id="ebb9f-142">右键单击*启动*创建的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-142">Right-click on the *Startup* folder you created.</span></span> <span data-ttu-id="ebb9f-143">选择 **添加** > **新建项**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-143">Select **Add** > **New Item**.</span></span> <span data-ttu-id="ebb9f-144">选择**常规**节点中，选择**文本文件**，并命名新项*SignalRPerfCounterInstall.cmd*。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-144">Select the **General** node, select **Text File**, and name the new item *SignalRPerfCounterInstall.cmd*.</span></span> <span data-ttu-id="ebb9f-145">此命令文件将安装到 web 角色的 SignalR 性能计数器。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-145">This command file will install the SignalR performance counters into the web role.</span></span>

    ![创建 SignalR 性能计数器安装批处理文件](using-signalr-performance-counters-in-an-azure-web-role/_static/image7.png)

13. <span data-ttu-id="ebb9f-147">当 Visual Studio 将创建*SignalRPerfCounterInstall.cmd*文件，它将自动打开主窗口中。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-147">When Visual Studio creates the *SignalRPerfCounterInstall.cmd* file, it will automatically open in the main window.</span></span> <span data-ttu-id="ebb9f-148">该文件的内容替换为以下脚本，然后保存并关闭该文件。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-148">Replace the contents of the file with the following script, then save and close the file.</span></span> <span data-ttu-id="ebb9f-149">此脚本执行*signalr.exe*，从而将 SignalR 性能计数器添加到角色实例。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-149">This script executes *signalr.exe*, which adds the SignalR performance counters to the role instance.</span></span>

    [!code-console[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample3.cmd)]

14. <span data-ttu-id="ebb9f-150">选择*signalr.exe*中的文件**解决方案资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-150">Select the *signalr.exe* file in **Solution Explorer**.</span></span> <span data-ttu-id="ebb9f-151">在文件中**属性**，请设置**复制到输出目录**到**始终复制**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-151">In the file's **Properties**, set **Copy to Output Directory** to **Copy Always**.</span></span>

    ![设置复制到输出目录将始终复制](using-signalr-performance-counters-in-an-azure-web-role/_static/image8.png)

15. <span data-ttu-id="ebb9f-153">重复上一步*SignalRPerfCounterInstall.cmd*文件。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-153">Repeat the previous step for the *SignalRPerfCounterInstall.cmd* file.</span></span>


16. <span data-ttu-id="ebb9f-154">右键单击*SignalRPerfCounterInstall.cmd*文件，然后选择**打开**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-154">Right-click on the *SignalRPerfCounterInstall.cmd* file and select **Open With**.</span></span> <span data-ttu-id="ebb9f-155">在出现的对话框，选择**二进制编辑器**，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-155">In the dialog that appears, select **Binary Editor** and select **OK**.</span></span>

    ![使用二进制编辑器中打开](using-signalr-performance-counters-in-an-azure-web-role/_static/image9.png)

17. <span data-ttu-id="ebb9f-157">在二进制编辑器中，在文件中选择任何前导字节，并将其删除。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-157">In the binary editor, select any leading bytes in the file and delete them.</span></span> <span data-ttu-id="ebb9f-158">保存并关闭文件。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-158">Save and close the file.</span></span>

    ![删除前导字节](using-signalr-performance-counters-in-an-azure-web-role/_static/image10.png)

18. <span data-ttu-id="ebb9f-160">打开*ServiceDefinition.csdef*并添加的启动任务的执行*SignalrPerfCounterInstall.cmd*文件时，服务启动：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-160">Open *ServiceDefinition.csdef* and add a startup task that executes the *SignalrPerfCounterInstall.cmd* file when the service starts up:</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample4.xml?highlight=4-7)]

19. <span data-ttu-id="ebb9f-161">打开`Views/Shared/_Layout.cshtml`和移除文件末尾的 jQuery 捆绑脚本。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-161">Open `Views/Shared/_Layout.cshtml` and remove the jQuery bundle script from the end of the file.</span></span>

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample5.cshtml)]

20. <span data-ttu-id="ebb9f-162">添加连续调用的 JavaScript 客户端`increment`在服务器上的方法。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-162">Add a JavaScript client that continuously calls the `increment` method on the server.</span></span> <span data-ttu-id="ebb9f-163">打开`Views/Home/Index.cshtml`和内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-163">Open `Views/Home/Index.cshtml` and replace the contents with the following code:</span></span>

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample6.cshtml)]

21. <span data-ttu-id="ebb9f-164">创建新的文件夹中**WebRole1**名为项目*中心*。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-164">Create a new folder in the **WebRole1** project named *Hubs*.</span></span> <span data-ttu-id="ebb9f-165">右键单击*中心*中的文件夹**解决方案资源管理器**，然后选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-165">Right-click the *Hubs* folder in **Solution Explorer** and select **Add** > **New Item**.</span></span> <span data-ttu-id="ebb9f-166">在中**添加新项**对话框中，选择**Web** > **SignalR**类别，并选择**SignalR Hub 类 (v2)** 项模板。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-166">In the **Add New Item** dialog box, select the **Web** > **SignalR** category, and then select the **SignalR Hub Class (v2)** item template.</span></span> <span data-ttu-id="ebb9f-167">命名新集线器*MyHub.cs* ，然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-167">Name the new hub *MyHub.cs* and select **Add**.</span></span>

    ![将 SignalR Hub 类添加到添加新项对话框中的中心文件夹](using-signalr-performance-counters-in-an-azure-web-role/_static/image13.png)

22. <span data-ttu-id="ebb9f-169">*MyHub.cs*将自动在主窗口中打开。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-169">*MyHub.cs* will automatically open in the main window.</span></span> <span data-ttu-id="ebb9f-170">内容替换为以下代码，然后保存并关闭文件：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-170">Replace the contents with the following code, then save and close the file:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample7.cs)]

23. <span data-ttu-id="ebb9f-171">*[Crank.exe](signalr-connection-density-testing-with-crank.md)* 是连接密度测试工具提供与 SignalR 基本代码。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-171">*[Crank.exe](signalr-connection-density-testing-with-crank.md)* is a connection density testing tool provided with the SignalR codebase.</span></span> <span data-ttu-id="ebb9f-172">由于曲柄需要持续性连接，则添加一个到你的站点使用的测试时。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-172">Since Crank requires a persistent connection, you add one to your site for use when testing.</span></span> <span data-ttu-id="ebb9f-173">添加一个新文件夹来**WebRole1**名为项目*PersistentConnections*。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-173">Add a new folder to the **WebRole1** project called *PersistentConnections*.</span></span> <span data-ttu-id="ebb9f-174">右键单击此文件夹并选择**外** > **类**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-174">Right-click this folder and select **Add** > **Class**.</span></span> <span data-ttu-id="ebb9f-175">将新类文件命名*MyPersistentConnections.cs* ，然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-175">Name the new class file *MyPersistentConnections.cs* and select **Add**.</span></span>

24. <span data-ttu-id="ebb9f-176">Visual Studio 将打开*MyPersistentConnections.cs*主窗口中的文件。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-176">Visual Studio will open the *MyPersistentConnections.cs* file in the main window.</span></span> <span data-ttu-id="ebb9f-177">内容替换为以下代码，然后保存并关闭文件：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-177">Replace the contents with the following code, then save and close the file:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample8.cs)]

25. <span data-ttu-id="ebb9f-178">使用`Startup`类，SignalR 对象启动 OWIN 启动时。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-178">Using the `Startup` class, the SignalR objects start when OWIN starts up.</span></span> <span data-ttu-id="ebb9f-179">打开或创建*Startup.cs*和内容替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-179">Open or create *Startup.cs* and replace the contents with the following code:</span></span>

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample9.cs)]

    <span data-ttu-id="ebb9f-180">在上面的代码，`OwinStartup`特性标记此类，以启动 OWIN。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-180">In the code above, the `OwinStartup` attribute marks this class to start OWIN.</span></span> <span data-ttu-id="ebb9f-181">`Configuration`方法启动 SignalR。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-181">The `Configuration` method starts SignalR.</span></span>

26. <span data-ttu-id="ebb9f-182">在 Microsoft Azure 模拟器中测试应用程序，通过按**F5**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-182">Test your application in the Microsoft Azure Emulator by pressing **F5**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ebb9f-183">如果遇到**FileLoadException**处**MapSignalR**，更改中的绑定重定向*web.config*所示：</span><span class="sxs-lookup"><span data-stu-id="ebb9f-183">If you encounter a **FileLoadException** at **MapSignalR**, change the binding redirects in *web.config* to the following:</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample12.xml?highlight=3,7)]

27. <span data-ttu-id="ebb9f-184">等待大约一分钟。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-184">Wait about one minute.</span></span> <span data-ttu-id="ebb9f-185">在 Visual Studio 中打开云资源管理器工具窗口 (**视图** > **云资源管理器**) 和展开的路径`(Local)/Storage Accounts/(Development)/Tables`。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-185">Open the Cloud Explorer tool window in Visual Studio (**View** > **Cloud Explorer**) and expand the path `(Local)/Storage Accounts/(Development)/Tables`.</span></span> <span data-ttu-id="ebb9f-186">双击**WADPerformanceCountersTable**。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-186">Double-click **WADPerformanceCountersTable**.</span></span> <span data-ttu-id="ebb9f-187">应会看到表数据中的 SignalR 计数器。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-187">You should see SignalR counters in the table data.</span></span> <span data-ttu-id="ebb9f-188">如果看不到表，您可能需要重新输入你的 Azure 存储凭据。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-188">If you don't see the table, you may need to re-enter your Azure Storage credentials.</span></span> <span data-ttu-id="ebb9f-189">可能需要选择**刷新**按钮，请参阅中的表**云资源管理器**，或选择**刷新**打开表窗口以查看表中的数据中的按钮。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-189">You may need to select the **Refresh** button to see the table in **Cloud Explorer** or select the **Refresh** button in the open table window to see data in the table.</span></span>

    ![在 Visual Studio 云资源管理器中选择 WAD 性能计数器表](using-signalr-performance-counters-in-an-azure-web-role/_static/image11.png)

    ![在 WAD 性能计数器表中显示计数器收集](using-signalr-performance-counters-in-an-azure-web-role/_static/image12.png)

28. <span data-ttu-id="ebb9f-192">若要在云中测试应用程序，更新**ServiceConfiguration.Cloud.cscfg**文件，并设置`Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`为有效的 Azure 存储帐户连接字符串。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-192">To test your application in the cloud, update the **ServiceConfiguration.Cloud.cscfg** file and set the `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` to a valid Azure Storage account connection string.</span></span>

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample10.xml)]

29. <span data-ttu-id="ebb9f-193">应用程序部署到你的 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-193">Deploy the application to your Azure subscription.</span></span> <span data-ttu-id="ebb9f-194">有关如何部署到 Azure 的应用程序的详细信息，请参阅[如何创建和部署云服务](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy)。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-194">For details on how to deploy an application to Azure, see [How to Create and Deploy a Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy).</span></span>

30. <span data-ttu-id="ebb9f-195">等待几分钟。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-195">Wait a few minutes.</span></span> <span data-ttu-id="ebb9f-196">在中**云资源管理器**，找到上面配置的存储帐户，并查找`WADPerformanceCountersTable`中其表。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-196">In **Cloud Explorer**, locate the storage account you configured above and find the `WADPerformanceCountersTable` table in it.</span></span> <span data-ttu-id="ebb9f-197">应会看到表数据中的 SignalR 计数器。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-197">You should see SignalR counters in the table data.</span></span> <span data-ttu-id="ebb9f-198">如果看不到表，您可能需要重新输入你的 Azure 存储凭据。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-198">If you don't see the table, you may need to re-enter your Azure Storage credentials.</span></span> <span data-ttu-id="ebb9f-199">可能需要选择**刷新**按钮，请参阅中的表**云资源管理器**，或选择**刷新**打开表窗口以查看表中的数据中的按钮。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-199">You may need to select the **Refresh** button to see the table in **Cloud Explorer** or select the **Refresh** button in the open table window to see data in the table.</span></span>

<span data-ttu-id="ebb9f-200">特别感谢[Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard)本教程中使用的原始内容。</span><span class="sxs-lookup"><span data-stu-id="ebb9f-200">Special thanks to [Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard) for the original content used in this tutorial.</span></span>
