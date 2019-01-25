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
# <a name="using-signalr-performance-counters-in-an-azure-web-role"></a>在 Azure Web 角色中使用 SignalR 性能计数器

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

SignalR 性能计数器用于监视 Azure Web 角色中的应用程序的性能。 Microsoft Azure 诊断捕获计数器。 与在 Azure 上安装 SignalR 性能计数器*signalr.exe*，用于独立或在本地应用相同的工具。 Azure 角色是暂时的因为你将配置应用以安装并注册 SignalR 在启动时的性能计数器。

## <a name="prerequisites"></a>系统必备

* Visual Studio 2015 或[2017年](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
* [用于 Visual Studio 的 Microsoft Azure SDK](https://azure.microsoft.com/downloads/) **注意：安装 SDK 之后重新启动计算机。**
* Microsoft Azure 订阅：若要注册一个免费 Azure 试用帐户，请参阅[Azure 免费试用](https://azure.microsoft.com/free/)。

## <a name="creating-an-azure-web-role-application-that-exposes-signalr-performance-counters"></a>创建 Azure Web 角色应用程序公开 SignalR 性能计数器

1. 打开 Visual Studio。

2. 在 Visual Studio 中，选择“文件” > “新建” > “项目”。

3. 在**新的项目**对话框中，选择**Visual C#** > **云**左边的类别，然后选择**Azure 云服务**模板。 命名应用程序**SignalRPerfCounters** ，然后选择**确定**。

   ![新的云应用程序](using-signalr-performance-counters-in-an-azure-web-role/_static/image1.png)

   > [!NOTE]
   > 如果没有看到**云**模板类别或**Azure 云服务**模板中，你需要安装**Azure 开发**Visual Studio 2017 工作负载。 选择**打开 Visual Studio 安装程序**左下方的链接**新项目**对话框打开 Visual Studio 安装程序。 选择**Azure 开发**工作负荷中，然后选择**修改**以启动安装工作负载。
   >
   > ![在 Visual Studio 安装程序中的 azure 开发工作负载](using-signalr-performance-counters-in-an-azure-web-role/_static/azure-development-workload.png)

4. 在中**新的 Microsoft Azure 云服务**对话框中，选择**ASP.NET Web 角色**和选择 > 按钮以将角色添加到项目。 选择 **确定**。

   ![添加 ASP.NET Web 角色](using-signalr-performance-counters-in-an-azure-web-role/_static/image2.png)

5. 在中**新 ASP.NET Web 应用程序-WebRole1**对话框中，选择**MVC**模板，，然后选择**确定**。

   ![添加 MVC 和 Web API](using-signalr-performance-counters-in-an-azure-web-role/_static/image3.png)

6. 在中**解决方案资源管理器**，打开*diagnostics.wadcfgx*文件下**WebRole1**。

   ![解决方案资源管理器 diagnostics.wadcfgx](using-signalr-performance-counters-in-an-azure-web-role/_static/image4.png)

7. 文件的内容替换为下面的配置并保存文件：

   [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample1.xml)]

8. 打开**程序包管理器控制台**从**工具** > **NuGet 包管理器**。 输入以下命令以安装最新版本的 SignalR 和 SignalR 实用程序包：

   [!code-powershell[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample2.ps1)]

9. 配置应用到角色实例安装的 SignalR 性能计数器，它启动或回收时。 在中**解决方案资源管理器**，右键单击**WebRole1**项目，然后选择**添加** > **新文件夹**。 将新文件夹命名*启动*。

   ![添加启动文件夹](using-signalr-performance-counters-in-an-azure-web-role/_static/image5.png)

10. 复制*signalr.exe*文件 (与添加**Microsoft.AspNet.SignalR.Utils**包) 从\<项目文件夹 > / SignalRPerfCounters/packages/Microsoft.AspNet.SignalR.Utils。\<版本 > / 工具，以*启动*上一步中创建的文件夹。

11. 在中**解决方案资源管理器**，右键单击*启动*文件夹，然后选择**添加** > **现有项**。 在出现的对话框，选择*signalr.exe* ，然后选择**添加**。

    ![向项目添加 signalr.exe](using-signalr-performance-counters-in-an-azure-web-role/_static/image6.png)

12. 右键单击*启动*创建的文件夹。 选择 **添加** > **新建项**。 选择**常规**节点中，选择**文本文件**，并命名新项*SignalRPerfCounterInstall.cmd*。 此命令文件将安装到 web 角色的 SignalR 性能计数器。

    ![创建 SignalR 性能计数器安装批处理文件](using-signalr-performance-counters-in-an-azure-web-role/_static/image7.png)

13. 当 Visual Studio 将创建*SignalRPerfCounterInstall.cmd*文件，它将自动打开主窗口中。 该文件的内容替换为以下脚本，然后保存并关闭该文件。 此脚本执行*signalr.exe*，从而将 SignalR 性能计数器添加到角色实例。

    [!code-console[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample3.cmd)]

14. 选择*signalr.exe*中的文件**解决方案资源管理器**。 在文件中**属性**，请设置**复制到输出目录**到**始终复制**。

    ![设置复制到输出目录将始终复制](using-signalr-performance-counters-in-an-azure-web-role/_static/image8.png)

15. 重复上一步*SignalRPerfCounterInstall.cmd*文件。


16. 右键单击*SignalRPerfCounterInstall.cmd*文件，然后选择**打开**。 在出现的对话框，选择**二进制编辑器**，然后选择**确定**。

    ![使用二进制编辑器中打开](using-signalr-performance-counters-in-an-azure-web-role/_static/image9.png)

17. 在二进制编辑器中，在文件中选择任何前导字节，并将其删除。 保存并关闭文件。

    ![删除前导字节](using-signalr-performance-counters-in-an-azure-web-role/_static/image10.png)

18. 打开*ServiceDefinition.csdef*并添加的启动任务的执行*SignalrPerfCounterInstall.cmd*文件时，服务启动：

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample4.xml?highlight=4-7)]

19. 打开`Views/Shared/_Layout.cshtml`和移除文件末尾的 jQuery 捆绑脚本。

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample5.cshtml)]

20. 添加连续调用的 JavaScript 客户端`increment`在服务器上的方法。 打开`Views/Home/Index.cshtml`和内容替换为以下代码：

    [!code-cshtml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample6.cshtml)]

21. 创建新的文件夹中**WebRole1**名为项目*中心*。 右键单击*中心*中的文件夹**解决方案资源管理器**，然后选择**添加** > **新项**。 在中**添加新项**对话框中，选择**Web** > **SignalR**类别，并选择**SignalR Hub 类 (v2)** 项模板。 命名新集线器*MyHub.cs* ，然后选择**添加**。

    ![将 SignalR Hub 类添加到添加新项对话框中的中心文件夹](using-signalr-performance-counters-in-an-azure-web-role/_static/image13.png)

22. *MyHub.cs*将自动在主窗口中打开。 内容替换为以下代码，然后保存并关闭文件：

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample7.cs)]

23. *[Crank.exe](signalr-connection-density-testing-with-crank.md)* 是连接密度测试工具提供与 SignalR 基本代码。 由于曲柄需要持续性连接，则添加一个到你的站点使用的测试时。 添加一个新文件夹来**WebRole1**名为项目*PersistentConnections*。 右键单击此文件夹并选择**外** > **类**。 将新类文件命名*MyPersistentConnections.cs* ，然后选择**添加**。

24. Visual Studio 将打开*MyPersistentConnections.cs*主窗口中的文件。 内容替换为以下代码，然后保存并关闭文件：

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample8.cs)]

25. 使用`Startup`类，SignalR 对象启动 OWIN 启动时。 打开或创建*Startup.cs*和内容替换为以下代码：

    [!code-csharp[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample9.cs)]

    在上面的代码，`OwinStartup`特性标记此类，以启动 OWIN。 `Configuration`方法启动 SignalR。

26. 在 Microsoft Azure 模拟器中测试应用程序，通过按**F5**。

    > [!NOTE]
    > 如果遇到**FileLoadException**处**MapSignalR**，更改中的绑定重定向*web.config*所示：

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample12.xml?highlight=3,7)]

27. 等待大约一分钟。 在 Visual Studio 中打开云资源管理器工具窗口 (**视图** > **云资源管理器**) 和展开的路径`(Local)/Storage Accounts/(Development)/Tables`。 双击**WADPerformanceCountersTable**。 应会看到表数据中的 SignalR 计数器。 如果看不到表，您可能需要重新输入你的 Azure 存储凭据。 可能需要选择**刷新**按钮，请参阅中的表**云资源管理器**，或选择**刷新**打开表窗口以查看表中的数据中的按钮。

    ![在 Visual Studio 云资源管理器中选择 WAD 性能计数器表](using-signalr-performance-counters-in-an-azure-web-role/_static/image11.png)

    ![在 WAD 性能计数器表中显示计数器收集](using-signalr-performance-counters-in-an-azure-web-role/_static/image12.png)

28. 若要在云中测试应用程序，更新**ServiceConfiguration.Cloud.cscfg**文件，并设置`Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`为有效的 Azure 存储帐户连接字符串。

    [!code-xml[Main](using-signalr-performance-counters-in-an-azure-web-role/samples/sample10.xml)]

29. 应用程序部署到你的 Azure 订阅。 有关如何部署到 Azure 的应用程序的详细信息，请参阅[如何创建和部署云服务](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy)。

30. 等待几分钟。 在中**云资源管理器**，找到上面配置的存储帐户，并查找`WADPerformanceCountersTable`中其表。 应会看到表数据中的 SignalR 计数器。 如果看不到表，您可能需要重新输入你的 Azure 存储凭据。 可能需要选择**刷新**按钮，请参阅中的表**云资源管理器**，或选择**刷新**打开表窗口以查看表中的数据中的按钮。

特别感谢[Martin Richard](https://social.msdn.microsoft.com/profile/Martin+Richard)本教程中使用的原始内容。
