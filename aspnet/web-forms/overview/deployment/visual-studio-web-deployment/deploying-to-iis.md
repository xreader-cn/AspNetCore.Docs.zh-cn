---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
title: 使用 Visual Studio 的 ASP.NET Web 部署：部署到测试 |Microsoft Docs
author: tdykstra
description: 本系列教程演示如何部署 （发布） ASP.NET web 应用程序到 Azure 应用服务 Web 应用或第三方托管提供商，通过使用...
ms.author: riande
ms.date: 01/16/2019
ms.assetid: 8bf2c4fb-4ee5-4841-bfc2-03462c1f7a7a
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
msc.type: authoredcontent
ms.openlocfilehash: d49dfad368ca4b81bb865103a99ec223a1cc66df
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396332"
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-to-test"></a><span data-ttu-id="7f9c2-103">使用 Visual Studio 的 ASP.NET Web 部署：部署到测试</span><span class="sxs-lookup"><span data-stu-id="7f9c2-103">ASP.NET Web Deployment using Visual Studio: Deploying to Test</span></span>
====================
<span data-ttu-id="7f9c2-104">通过[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7f9c2-104">by [Tom Dykstra](https://github.com/tdykstra)</span></span>

> <span data-ttu-id="7f9c2-105">本系列教程演示如何部署 （发布） ASP.NET web 应用程序到 Azure 应用服务 Web 应用或第三方托管提供商使用 Visual Studio 2017。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-105">This tutorial series shows you how to deploy (publish) an ASP.NET web application to Azure App Service Web Apps or to a third-party hosting provider using Visual Studio 2017.</span></span> <span data-ttu-id="7f9c2-106">有关序列的信息，请参阅[系列中的第一个教程](introduction.md)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-106">For information about the series, see [the first tutorial in the series](introduction.md).</span></span>

## <a name="overview"></a><span data-ttu-id="7f9c2-107">概述</span><span class="sxs-lookup"><span data-stu-id="7f9c2-107">Overview</span></span>

<span data-ttu-id="7f9c2-108">在本教程中，你将在本地计算机上部署 ASP.NET web 应用程序到 Internet 信息服务器 (IIS)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-108">In this tutorial, you'll deploy an ASP.NET web application to Internet Information Server (IIS) on your local computer.</span></span>

<span data-ttu-id="7f9c2-109">通常开发的应用程序，您将运行它并在 Visual Studio 中进行测试。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-109">Generally when you develop an application, you run it and test it in Visual Studio.</span></span> <span data-ttu-id="7f9c2-110">默认情况下，在 Visual Studio 2017 中的 web 应用程序项目使用 IIS Express 用作开发 web 服务器。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-110">By default, web application projects in Visual Studio 2017 use IIS Express as the development web server.</span></span> <span data-ttu-id="7f9c2-111">IIS Express 的行为更类似于比 Visual Studio 开发服务器 (也称为 Cassini)，Visual Studio 2017 默认使用的完整 IIS。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-111">IIS Express behaves more like full IIS than the Visual Studio Development Server (also known as Cassini), which Visual Studio 2017 uses by default.</span></span> <span data-ttu-id="7f9c2-112">但既不开发 web 服务器的工作方式完全相同 IIS。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-112">But neither development web server works exactly like IIS.</span></span> <span data-ttu-id="7f9c2-113">因此，应用无法运行和测试在 Visual Studio 中的正确但部署到 IIS 时失败。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-113">Consequently, an app could run and test correctly in Visual Studio but fail when it's deployed to IIS.</span></span>

<span data-ttu-id="7f9c2-114">两种方式，可以可靠地测试你的应用程序：</span><span class="sxs-lookup"><span data-stu-id="7f9c2-114">You can reliably test your application in two ways:</span></span>

1. <span data-ttu-id="7f9c2-115">应用程序部署到 IIS 在开发计算机上使用相同的过程的更高版本将使用它来将其部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-115">Deploy your application to IIS on your development computer using the same process that you'll use later to deploy it to your production environment.</span></span>

   <span data-ttu-id="7f9c2-116">可以配置 Visual Studio 以使用 IIS 时运行 web 项目中，但不会测试您的部署过程。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-116">You can configure Visual Studio to use IIS when you run a web project, but that wouldn't test your deployment process.</span></span> <span data-ttu-id="7f9c2-117">此方法验证您的部署过程，并在 IIS 下正确运行你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-117">This method validates your deployment process and that your application runs correctly under IIS.</span></span>

2. <span data-ttu-id="7f9c2-118">应用程序部署到测试环境类似于生产环境。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-118">Deploy your application to a test environment similar to your production environment.</span></span> 
 
   <span data-ttu-id="7f9c2-119">这些教程中的生产环境是 Azure 应用服务中的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-119">The production environment for these tutorials is Web Apps in Azure App Service.</span></span> <span data-ttu-id="7f9c2-120">理想的测试环境是在 Azure 服务中创建的其他 web 应用。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-120">The ideal test environment is an additional web app created in the Azure Service.</span></span> <span data-ttu-id="7f9c2-121">尽管它会设置为生产 web 应用的相同方式，您将仅使用它进行测试。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-121">Though it would be set up the same way as a production web app, you would only use it for testing.</span></span>

<span data-ttu-id="7f9c2-122">选项 2 是测试的最可靠方法。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-122">Option 2 is the most reliable way to test.</span></span> <span data-ttu-id="7f9c2-123">如果使用选项 2，不一定需要使用选项 1。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-123">If you use option 2, you don't necessarily need to use option 1.</span></span> <span data-ttu-id="7f9c2-124">但是如果要部署到第三方承载提供程序，选项 2 不一定能够使用或可能开销很大，因此本系列教程演示这两种方法。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-124">However if you're deploying to a third-party hosting provider, option 2 might not be feasible or might be expensive, so this tutorial series shows both methods.</span></span> <span data-ttu-id="7f9c2-125">在提供了选项 2 的指导[部署到生产环境](deploying-to-production.md)教程。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-125">Guidance for option 2 is provided in the [Deploying to the Production Environment](deploying-to-production.md) tutorial.</span></span>

<span data-ttu-id="7f9c2-126">有关在 Visual Studio 中使用 web 服务器的详细信息，请参阅[对于 ASP.NET Web 项目的 Visual Studio 中的 Web 服务器](https://msdn.microsoft.com/library/58wxa9w5.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-126">For more information about using web servers in Visual Studio, see [Web Servers in Visual Studio for ASP.NET Web Projects](https://msdn.microsoft.com/library/58wxa9w5.aspx).</span></span>

<span data-ttu-id="7f9c2-127">提醒：如果你收到一条错误消息或某些操作无法按完成以下教程，请务必检查[故障排除页](troubleshooting.md)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-127">Reminder: If you receive an error message or something doesn't work as you go through the tutorial, be sure to check the [troubleshooting page](troubleshooting.md).</span></span>

## <a name="download-the-contoso-university-starter-project"></a><span data-ttu-id="7f9c2-128">下载 Contoso University 初学者项目</span><span class="sxs-lookup"><span data-stu-id="7f9c2-128">Download the Contoso University starter project</span></span>

<span data-ttu-id="7f9c2-129">下载并安装 Contoso 大学 Visual Studio 初学者解决方案和项目。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-129">Download and install the Contoso University Visual Studio starter solution and project.</span></span> <span data-ttu-id="7f9c2-130">此解决方案包含已完成本教程。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-130">This solution contains the completed tutorial.</span></span> 

[<span data-ttu-id="7f9c2-131">下载初学者项目</span><span class="sxs-lookup"><span data-stu-id="7f9c2-131">Download Starter Project</span></span>](http://go.microsoft.com/fwlink/p/?LinkId=282627)

## <a name="install-iis"></a><span data-ttu-id="7f9c2-132">安装 IIS</span><span class="sxs-lookup"><span data-stu-id="7f9c2-132">Install IIS</span></span>

<span data-ttu-id="7f9c2-133">若要将部署到 IIS 在开发计算机上，确认安装了 IIS 和 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-133">To deploy to IIS on your development computer, confirm that IIS and Web Deploy are installed.</span></span> <span data-ttu-id="7f9c2-134">默认情况下，Visual Studio 会安装 Web 部署，但 IIS 不包括在默认 Windows 10、 Windows 8 或 Windows 7 配置。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-134">By default, Visual Studio installs Web Deploy, but IIS isn't included in the default Windows 10, Windows 8, or Windows 7 configuration.</span></span> <span data-ttu-id="7f9c2-135">如果已安装 IIS 和默认应用程序池已设置为.NET 4，请跳到[下一步部分](#sqlexpress)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-135">If you've already installed IIS and the default application pool is already set to .NET 4, skip to [the next section](#sqlexpress).</span></span>

1. <span data-ttu-id="7f9c2-136">建议你使用[Web 平台安装程序 (WPI)](https://www.microsoft.com/web/downloads/platform.aspx)安装 IIS 和 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-136">It's recommended you use the [Web Platform Installer (WPI)](https://www.microsoft.com/web/downloads/platform.aspx) to install IIS and Web Deploy.</span></span> <span data-ttu-id="7f9c2-137">WPI 安装建议的 IIS 配置，包括 IIS 和 Web 部署的先决条件，如有必要。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-137">WPI installs a recommended IIS configuration that includes IIS and Web Deploy prerequisites if necessary.</span></span>

   <span data-ttu-id="7f9c2-138">如果你已安装 IIS、 Web 部署，或任何其所需的组件，则会安装 WPI 只缺少的内容。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-138">If you've already installed IIS, Web Deploy, or any of their required components, the WPI installs only what is missing.</span></span>

   * <span data-ttu-id="7f9c2-139">使用 Web 平台安装程序安装 IIS 和 Web 部署：</span><span class="sxs-lookup"><span data-stu-id="7f9c2-139">Use the Web Platform Installer to install IIS and Web Deploy:</span></span>
    
     ![使用 WPI 安装 IIS](deploying-to-iis/_static/image30.png)

     ![安装 Web 部署使用 WPI](deploying-to-iis/_static/image31.png)

     <span data-ttu-id="7f9c2-142">可以看到，该值指示将安装 IIS 7 的消息。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-142">You'll see messages indicating that IIS 7 will be installed.</span></span> <span data-ttu-id="7f9c2-143">适用于 Windows 8; 中的 IIS 8 可使用该链接但 Windows 8 和更高版本，通过以下步骤以确保已安装 ASP.NET 4.7:</span><span class="sxs-lookup"><span data-stu-id="7f9c2-143">The link works for IIS 8 in Windows 8; but for Windows 8 and later, go through the following steps to make sure that ASP.NET 4.7 is installed:</span></span>

   * <span data-ttu-id="7f9c2-144">打开**Control Panel** > **程序** > **程序和功能** > **打开或关闭打开的 Windows 功能**.</span><span class="sxs-lookup"><span data-stu-id="7f9c2-144">Open **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off**.</span></span>

   * <span data-ttu-id="7f9c2-145">展开**Internet 信息服务**，**万维网服务**，并**应用程序开发功能**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-145">Expand **Internet Information Services**, **World Wide Web Services**, and **Application Development Features**.</span></span>
   
   * <span data-ttu-id="7f9c2-146">确认**ASP.NET 4.7**处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-146">Confirm that **ASP.NET 4.7** is selected.</span></span>

     ![选择 ASP.NET 4.7](deploying-to-iis/_static/image1a.png)

   * <span data-ttu-id="7f9c2-148">确认**World Wide Web 服务**并**IIS 管理控制台**处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-148">Confirm that **World Wide Web Services** and **IIS Management Console** is selected.</span></span> <span data-ttu-id="7f9c2-149">这会安装 IIS 和 IIS 管理器。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-149">This installs IIS and IIS Manager.</span></span>
    
     ![选择万维网服务](deploying-to-iis/_static/image24.png)    
  
   * <span data-ttu-id="7f9c2-151">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-151">Select **OK**.</span></span> <span data-ttu-id="7f9c2-152">对话框消息，指示安装正在进行显示。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-152">Dialog box messages indicating installation is taking place appear.</span></span>

<span data-ttu-id="7f9c2-153">安装 IIS 后，运行**IIS 管理器**以确保.NET Framework 版本 4 分配给默认应用程序池。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-153">After installing IIS, run **IIS Manager** to make sure that the .NET Framework version 4 is assigned to the default application pool.</span></span>

1. <span data-ttu-id="7f9c2-154">按 WINDOWS + R 打开**运行**对话框。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-154">Press WINDOWS+R to open the **Run** dialog box.</span></span>

   <span data-ttu-id="7f9c2-155">(在 Windows 8 或更高版本中，输入"运行"的**启动**页。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-155">(On Windows 8 or later, enter "run" on the **Start** page.</span></span> <span data-ttu-id="7f9c2-156">在 Windows 7 中，选择**运行**从**启动**菜单。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-156">In Windows 7, select **Run** from the **Start** menu.</span></span> <span data-ttu-id="7f9c2-157">如果**运行**不在**启动**菜单中，右键单击任务栏中，选择**属性**，选择 **「 开始 」 菜单**选项卡上，选择**自定义**，然后选择**运行命令**。)</span><span class="sxs-lookup"><span data-stu-id="7f9c2-157">If **Run** isn't in the **Start** menu, right-click the taskbar, select **Properties**, select the **Start Menu** tab, select **Customize**, and select **Run command**.)</span></span>

2. <span data-ttu-id="7f9c2-158">输入"inetmgr"，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-158">Enter "inetmgr" and select **OK**.</span></span>

3. <span data-ttu-id="7f9c2-159">在中**连接**窗格中，展开服务器节点，然后选择**应用程序池**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-159">In the **Connections** pane, expand the server node and select **Application Pools**.</span></span> <span data-ttu-id="7f9c2-160">在中**应用程序池**窗格如果**DefaultAppPool**是分配给.NET framework 版本 4 所示，跳到下一节。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-160">In the **Application Pools** pane if **DefaultAppPool** is assigned to the .NET framework version 4 as in the following illustration, skip to the next section.</span></span>

   ![Inetmgr_showing_4.0_app_pools](deploying-to-iis/_static/image5a.png)

4. <span data-ttu-id="7f9c2-162">如果您看到只有两个应用程序池，并且两者都设置为.NET Framework 2.0，请在 IIS 中安装 ASP.NET 4。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-162">If you see only two application pools and both are set to .NET Framework 2.0, install ASP.NET 4 in IIS.</span></span>

   <span data-ttu-id="7f9c2-163">Windows 8 或更高版本，请参阅上一部分的说明，确保该 ASP.NET 4.7 安装或请参阅[如何在 Windows 8 和 Windows Server 2012 上安装 ASP.NET 4.5](https://support.microsoft.com/kb/2736284)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-163">For Windows 8 or later, see the previous section's instructions for making sure that ASP.NET 4.7 is installed or see [How to install ASP.NET 4.5 on Windows 8 and Windows Server 2012](https://support.microsoft.com/kb/2736284).</span></span> <span data-ttu-id="7f9c2-164">对于 Windows 7 中，打开命令提示符窗口，请右键单击**命令提示符**在 Windows 中**启动**菜单并选择**以管理员身份运行**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-164">For Windows 7, open a command prompt window by right-clicking **Command Prompt** in the Windows **Start** menu and selecting **Run as Administrator**.</span></span> <span data-ttu-id="7f9c2-165">运行[aspnet\_regiis.exe](https://msdn.microsoft.com/library/k6h9cz8h.aspx)中 IIS，使用以下命令安装 ASP.NET 4。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-165">Run [aspnet\_regiis.exe](https://msdn.microsoft.com/library/k6h9cz8h.aspx) to install ASP.NET 4 in IIS using the following commands.</span></span> <span data-ttu-id="7f9c2-166">（在 32 位系统中，将为"Framework64"与"框架"。）</span><span class="sxs-lookup"><span data-stu-id="7f9c2-166">(In 32-bit systems, replace "Framework64" with "Framework".)</span></span>

   [!code-console[Main](deploying-to-iis/samples/sample1.cmd)]

   <span data-ttu-id="7f9c2-167">此命令创建新的应用程序池的.NET Framework 4 中，但默认应用程序池将保持设置为 2.0。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-167">This command creates new application pools for the .NET Framework 4, but the default application pool will remain set to 2.0.</span></span> <span data-ttu-id="7f9c2-168">要部署应用程序以.NET 4 为目标为该应用程序池，因此为.NET 4 更改应用程序池。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-168">You're deploying an application that targets .NET 4 to that application pool, so change the application pool to .NET 4.</span></span>

5. <span data-ttu-id="7f9c2-169">如果您关闭**IIS 管理器**，再次运行中，展开服务器节点，并选择**应用程序池**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-169">If you closed **IIS Manager**, run it again, expand the server node, and select **Application Pools**.</span></span>

6. <span data-ttu-id="7f9c2-170">在中**应用程序池**窗格中，选择**DefaultAppPool**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-170">In the **Application Pools** pane, select **DefaultAppPool**.</span></span> <span data-ttu-id="7f9c2-171">在中**操作**窗格中，选择**基本设置**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-171">In the **Actions** pane, select **Basic Settings**.</span></span>

   ![Inetmgr_selecting_Basic_Settings_for_app_pool](deploying-to-iis/_static/image25.png)

7. <span data-ttu-id="7f9c2-173">在中**编辑应用程序池**对话框中，更改 **.NET CLR 版本**到 **.NET CLR v4.0.30319**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-173">In the **Edit Application Pool** dialog box, change the **.NET CLR version** to **.NET CLR v4.0.30319**.</span></span> <span data-ttu-id="7f9c2-174">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-174">Select **OK**.</span></span>

   ![Selecting_.NET_4_for_DefaultAppPool](deploying-to-iis/_static/image6a.png)

<span data-ttu-id="7f9c2-176">现在你准备好发布到 IIS 的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-176">You're now ready to publish a web application to IIS.</span></span> <span data-ttu-id="7f9c2-177">首先，但是，创建用于测试的数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-177">First, however, create databases for testing.</span></span>

<a id="sqlexpress"></a>

## <a name="install-sql-server-express"></a><span data-ttu-id="7f9c2-178">安装 SQL Server Express</span><span class="sxs-lookup"><span data-stu-id="7f9c2-178">Install SQL Server Express</span></span>

<span data-ttu-id="7f9c2-179">LocalDB 不被设计用于处理在 IIS 中，因此你的测试环境必须有 SQL Server Express 安装。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-179">LocalDB isn't designed to work in IIS, so your test environment has to have SQL Server Express installed.</span></span> <span data-ttu-id="7f9c2-180">如果使用 Visual Studio 2010 SQL Server Express，它是已安装默认情况下。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-180">If you're using Visual Studio 2010 SQL Server Express, it's already installed by default.</span></span> <span data-ttu-id="7f9c2-181">如果你使用 Visual Studio 2012 或更高版本，安装 SQL Server Express。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-181">If you're using Visual Studio 2012 or later, install SQL Server Express.</span></span>

<span data-ttu-id="7f9c2-182">若要安装 SQL Server Express，下载并安装它从[下载中心：Microsoft SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-182">To install SQL Server Express, download and install it from [Download Center: Microsoft SQL Server 2017 Express edition](https://www.microsoft.com/sql-server/sql-server-editions-express).</span></span> 

<span data-ttu-id="7f9c2-183">SQL Server 安装中心的第一页，选择**新的 SQL Server 独立安装或向现有安装添加功能**并按照说明接受默认选项。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-183">On the first page of the SQL Server Installation Center, select **New SQL Server stand-alone installation or add features to an existing installation** and follow the instructions accepting the default choices.</span></span> <span data-ttu-id="7f9c2-184">在安装向导中，接受默认设置。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-184">In the installation wizard, accept the default settings.</span></span> <span data-ttu-id="7f9c2-185">有关安装选项的详细信息，请参阅[从安装向导 （安装程序） 安装 SQL Server](https://msdn.microsoft.com/library/ms143219.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-185">For more information about installation options, see [Install SQL Server from the Installation Wizard (Setup)](https://msdn.microsoft.com/library/ms143219.aspx).</span></span>

## <a name="create-sql-server-express-databases-for-the-test-environment"></a><span data-ttu-id="7f9c2-186">创建 SQL Server Express 数据库的测试环境</span><span class="sxs-lookup"><span data-stu-id="7f9c2-186">Create SQL Server Express databases for the test environment</span></span>

<span data-ttu-id="7f9c2-187">Contoso 大学应用程序有两个数据库：</span><span class="sxs-lookup"><span data-stu-id="7f9c2-187">The Contoso University application has two databases:</span></span> 

1. <span data-ttu-id="7f9c2-188">成员资格数据库</span><span class="sxs-lookup"><span data-stu-id="7f9c2-188">Membership database</span></span> 
2. <span data-ttu-id="7f9c2-189">应用程序数据库</span><span class="sxs-lookup"><span data-stu-id="7f9c2-189">Application database</span></span> 

<span data-ttu-id="7f9c2-190">可以将这些数据库部署到两个独立数据库或单个数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-190">You can deploy these databases to two separate databases or to a single database.</span></span> <span data-ttu-id="7f9c2-191">将它们合并，使它们更容易的数据库连接。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-191">Combining them makes database joins between them easier.</span></span> 

<span data-ttu-id="7f9c2-192">如果你正在部署到第三方托管提供商，托管计划可能还为您提供原因要将它们合并。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-192">If you're deploying to a third-party hosting provider, your hosting plan might also give you a reason to combine them.</span></span> <span data-ttu-id="7f9c2-193">例如，提供程序可能会收取多个数据库的详细信息，或可能甚至不允许多个数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-193">For example, the provider might charge more for multiple databases or might not even allow more than one database.</span></span>

<span data-ttu-id="7f9c2-194">在本教程中，你将部署到测试环境中的两个数据库和过渡和生产环境中的一个数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-194">In this tutorial, you'll deploy to two databases in the test environment and to one database in the staging and production environments.</span></span>

<span data-ttu-id="7f9c2-195">从**视图**在 Visual Studio 中，选择菜单**服务器资源管理器**(**数据库资源管理器**Visual Web Developer 中)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-195">From the **View** menu in Visual Studio, select **Server Explorer** (**Database Explorer** in Visual Web Developer).</span></span> <span data-ttu-id="7f9c2-196">右键单击**数据连接**，然后选择**创建新的 SQL Server 数据库**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-196">Right-click **Data Connections** and select **Create New SQL Server Database**.</span></span>

![Selecting_Create_New_SQL_Server_Database](deploying-to-iis/_static/image8.png)

<span data-ttu-id="7f9c2-198">在中**创建新的 SQL Server 数据库**对话框框中，输入"。 \SQLExpress"在**服务器名称**框和"aspnet-ContosoUniversity"中**新的数据库名称**框。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-198">In the **Create New SQL Server Database** dialog box, enter ".\SQLExpress" in the **Server name** box and "aspnet-ContosoUniversity" in the **New database name** box.</span></span> <span data-ttu-id="7f9c2-199">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-199">Select **OK**.</span></span>

![创建 aspnet ContosoUniversity](deploying-to-iis/_static/image9.png)

<span data-ttu-id="7f9c2-201">请按照相同的过程来创建一个名为的新 SQL Server Express School 数据库`ContosoUniversity`。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-201">Follow the same procedure to create a new SQL Server Express School database named `ContosoUniversity`.</span></span>

<span data-ttu-id="7f9c2-202">**服务器资源管理器**显示了两个新数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-202">**Server Explorer** shows the two new databases.</span></span>

![在服务器资源管理器中的新数据库](deploying-to-iis/_static/image10.png)

## <a name="create-a-grant-script-for-the-new-databases"></a><span data-ttu-id="7f9c2-204">创建新的数据库授予脚本</span><span class="sxs-lookup"><span data-stu-id="7f9c2-204">Create a grant script for the new databases</span></span>

<span data-ttu-id="7f9c2-205">在开发计算机上在 IIS 中运行的应用程序，该应用程序使用默认应用程序池的凭据来访问数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-205">When the application runs in IIS on your development computer, the application uses the default application pool's credentials to access the database.</span></span> <span data-ttu-id="7f9c2-206">但是，默认情况下，应用程序池没有打开数据库的权限。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-206">However, by default, the application pool doesn't have permission to open the databases.</span></span> <span data-ttu-id="7f9c2-207">这意味着您需要运行脚本，以授予该权限。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-207">This means you need to run a script to grant that permission.</span></span> <span data-ttu-id="7f9c2-208">在本部分中，将创建该脚本并运行更高版本以确保在 IIS 中运行时，该应用程序可以打开数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-208">In this section, you'll create that script and run it later to make sure that the application can open the databases when it runs in IIS.</span></span>

<span data-ttu-id="7f9c2-209">在文本编辑器中，将以下 SQL 命令复制到新文件并将其保存为*Grant.sql*。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-209">In a text editor, copy the following SQL commands into a new file and save it as *Grant.sql*.</span></span> 

[!code-sql[Main](deploying-to-iis/samples/sample2.sql)]

<span data-ttu-id="7f9c2-210">在 Visual Studio 中，打开 Contoso University 解决方案。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-210">In Visual Studio, open the Contoso University solution.</span></span> <span data-ttu-id="7f9c2-211">右键单击该解决方案 （而不是项目的），然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-211">Right-click the solution (not one of the projects), and select **Add**.</span></span> <span data-ttu-id="7f9c2-212">选择**现有项**，浏览到*Grant.sql*，并将其打开。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-212">Select **Existing Item**, browse to *Grant.sql*, and open it.</span></span>

> [!NOTE]
> <span data-ttu-id="7f9c2-213">此脚本在本教程中指定了被设计适用于 SQL Server Express 2012 或更高版本和 Windows 10、 Windows 8 或 Windows 7 中的 IIS 设置。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-213">This script is designed to work with SQL Server Express 2012 or later and with the IIS settings in Windows 10, Windows 8, or Windows 7 as they are specified in this tutorial.</span></span> <span data-ttu-id="7f9c2-214">如果在使用不同版本的 SQL Server 或 Windows，或者如果设置了 IIS 计算机上以不同的方式，可能需要对此脚本的更改。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-214">If you're using a different version of SQL Server or Windows, or if you set up IIS on your computer differently, changes to this script might be required.</span></span> <span data-ttu-id="7f9c2-215">有关 SQL Server 脚本详细信息，请参阅[SQL Server 联机丛书](https://go.microsoft.com/fwlink/?LinkId=132511)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-215">For more information about SQL Server scripts, see [SQL Server Books Online](https://go.microsoft.com/fwlink/?LinkId=132511).</span></span>

> [!NOTE] 
> <span data-ttu-id="7f9c2-216">**安全说明**此脚本将向提供`db_owner`在运行时，这是您在生产环境中必须访问数据库的用户的权限。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-216">**Security Note** This script gives `db_owner` permissions to the user that accesses the database at run time, which is what you'll have in the production environment.</span></span> <span data-ttu-id="7f9c2-217">在某些情况下，你可能想要指定具有完整的数据库架构更新部署权限和指定的运行时具有的权限仅读取和写入数据的不同用户的用户。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-217">In some scenarios, you might want to specify a user that has full database schema update permissions only for deployment and specify for run time a different user that has permissions only to read and write data.</span></span> <span data-ttu-id="7f9c2-218">有关详细信息，请参阅[评审 Code First 迁移自动 Web.config 更改](#reviewingmigrations)在本教程中更高版本。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-218">For more information, see [Reviewing the Automatic Web.config Changes for Code First Migrations](#reviewingmigrations) later in this tutorial.</span></span>

<a id="publish"></a>

## <a name="run-the-grant-script-in-the-application-database"></a><span data-ttu-id="7f9c2-219">运行应用程序数据库中授予脚本</span><span class="sxs-lookup"><span data-stu-id="7f9c2-219">Run the grant script in the application database</span></span>

<span data-ttu-id="7f9c2-220">可以配置要运行授予脚本成员资格数据库中，在部署期间因为该数据库的部署使用的发布配置文件[dbDacFx](https://docs.microsoft.com/iis/publish/using-web-deploy/dbdacfx-provider-for-incremental-database-publishing)提供程序。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-220">You can configure the publish profile to run the grant script in the membership database during deployment because that database deployment uses the [dbDacFx](https://docs.microsoft.com/iis/publish/using-web-deploy/dbdacfx-provider-for-incremental-database-publishing) provider.</span></span> <span data-ttu-id="7f9c2-221">在 Code First 迁移部署，这是你要如何部署应用程序数据库期间，无法运行脚本。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-221">You can't run scripts during Code First Migrations deployment, which is how you're deploying the application database.</span></span> <span data-ttu-id="7f9c2-222">这意味着您必须手动运行应用程序数据库中的部署前的脚本。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-222">This means you have to manually run the script before deployment in the application database.</span></span>

1. <span data-ttu-id="7f9c2-223">在 Visual Studio 中打开*Grant.sql*前面创建的文件。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-223">In Visual Studio, open the *Grant.sql* file that you created earlier.</span></span>

2. <span data-ttu-id="7f9c2-224">选择**连接**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-224">Select **Connect**.</span></span> 

    ![连接按钮](deploying-to-iis/_static/image11.png)

3. <span data-ttu-id="7f9c2-226">在中**连接到服务器**对话框框中，输入 *。 \SQLExpress*作为**服务器名称**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-226">In the **Connect to Server** dialog box, enter *.\SQLExpress* as the **Server Name**.</span></span> <span data-ttu-id="7f9c2-227">选择**连接**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-227">Select **Connect**.</span></span>

4. <span data-ttu-id="7f9c2-228">在数据库下拉列表中选择**ContosoUniversity**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-228">In the database drop-down list select **ContosoUniversity**.</span></span> <span data-ttu-id="7f9c2-229">选择**执行**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-229">Select **Execute**.</span></span> 

   ![](deploying-to-iis/_static/image12.png)

<span data-ttu-id="7f9c2-230">现在，默认应用程序池标识代码优先迁移来创建数据库表，该应用程序运行时在应用程序数据库中具有足够的权限。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-230">The default application pool identity now has sufficient permissions in the application database for Code First Migrations to create the database tables when the application runs.</span></span>

## <a name="publish-to-iis"></a><span data-ttu-id="7f9c2-231">发布到 IIS</span><span class="sxs-lookup"><span data-stu-id="7f9c2-231">Publish to IIS</span></span>

<span data-ttu-id="7f9c2-232">有几种方法可以将它们部署到 IIS，使用 Visual Studio 和 Web 部署：</span><span class="sxs-lookup"><span data-stu-id="7f9c2-232">There are several ways you can deploy to IIS using Visual Studio and Web Deploy:</span></span>

* <span data-ttu-id="7f9c2-233">使用一键式发布的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-233">Use Visual Studio one-click publish.</span></span>
* <span data-ttu-id="7f9c2-234">从命令行发布。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-234">Publish from the command line.</span></span>
* <span data-ttu-id="7f9c2-235">创建部署包并将其使用 IIS 管理器安装。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-235">Create a deployment package and install it using IIS Manager.</span></span> <span data-ttu-id="7f9c2-236">包具有的所有文件和在 IIS 中安装站点所需的元数据的.zip 文件。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-236">The package has a .zip file with all the files and metadata required to install a site in IIS.</span></span>
* <span data-ttu-id="7f9c2-237">创建部署包并将其使用命令行安装。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-237">Create a deployment package and install it using the command line.</span></span>

<span data-ttu-id="7f9c2-238">已在前面的教程，要将 Visual Studio 设置为自动执行部署任务适用于所有这些方法的过程。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-238">The process you went through in the previous tutorials to set up Visual Studio to automate deployment tasks applies to all of these methods.</span></span> <span data-ttu-id="7f9c2-239">在这些教程中，将使用前两个方法。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-239">In these tutorials, you'll use the first two methods.</span></span> <span data-ttu-id="7f9c2-240">有关使用部署包的信息，请参阅[web 应用程序部署通过创建和安装 web 部署包](https://go.microsoft.com/fwlink/p/?LinkId=282413#package)for Visual Studio 和 ASP.NET Web 部署内容映射中。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-240">For information about using deployment packages, see [Deploying a web application by creating and installing a web deployment package](https://go.microsoft.com/fwlink/p/?LinkId=282413#package) in the Web Deployment Content Map for Visual Studio and ASP.NET.</span></span>

<span data-ttu-id="7f9c2-241">发布前，请确保您要在管理员模式下运行 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-241">Before publishing, make sure that you're running Visual Studio in administrator mode.</span></span> <span data-ttu-id="7f9c2-242">如果没有看到 **（管理员）** 在标题栏中，关闭 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-242">If you don't see **(Administrator)** in the title bar, close Visual Studio.</span></span> <span data-ttu-id="7f9c2-243">在 Windows 8 （或更高版本）**启动**页上或 Windows 7**启动**菜单中，右键单击 Visual Studio 图标，然后选择**以管理员身份运行**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-243">In the Windows 8 (or later) **Start** page or the Windows 7 **Start** menu, right-click the Visual Studio icon and select **Run as Administrator**.</span></span> <span data-ttu-id="7f9c2-244">管理员模式下才发布你的本地计算机上发布到 IIS 时所需。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-244">Administrator mode is only required for publishing when you're publishing to IIS on the local computer.</span></span>

### <a name="create-the-publish-profile"></a><span data-ttu-id="7f9c2-245">创建发布配置文件</span><span class="sxs-lookup"><span data-stu-id="7f9c2-245">Create the publish profile</span></span>

1. <span data-ttu-id="7f9c2-246">在中**解决方案资源管理器**，右键单击**ContosoUniversity**项目 (不**ContosoUniversity.DAL**项目)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-246">In **Solution Explorer**, right-click the **ContosoUniversity** project (not the **ContosoUniversity.DAL** project).</span></span> <span data-ttu-id="7f9c2-247">选择“发布”。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-247">Select **Publish**.</span></span> <span data-ttu-id="7f9c2-248">**发布**页将出现。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-248">The **Publish** page appears.</span></span>

2. <span data-ttu-id="7f9c2-249">选择**新的配置文件**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-249">Select **New Profile**.</span></span> <span data-ttu-id="7f9c2-250">**选取发布目标**对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-250">The **Pick a publish target** dialog box appears.</span></span>

3. <span data-ttu-id="7f9c2-251">选择**IIS、 FTP 等**。选择“创建配置文件”。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-251">Select **IIS, FTP, etc**. Select **Create Profile**.</span></span> <span data-ttu-id="7f9c2-252">**发布**向导显示。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-252">The **Publish** wizard appears.</span></span>

   ![发布 Web 向导配置文件选项卡](deploying-to-iis/_static/image26.png)

4. <span data-ttu-id="7f9c2-254">从**发布方法**下拉列表菜单中，选择**Web 部署**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-254">From the **Publish method** drop-down menu, select **Web Deploy**.</span></span>

5. <span data-ttu-id="7f9c2-255">有关**服务器**，输入*localhost*。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-255">For **Server**, enter *localhost*.</span></span>

6. <span data-ttu-id="7f9c2-256">有关**站点名称**，输入*默认 Web 站点/ContosoUniversity*。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-256">For **Site name**, enter *Default Web Site/ContosoUniversity*.</span></span>

7. <span data-ttu-id="7f9c2-257">有关**目标 URL**，输入*http://localhost/ContosoUniversity*。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-257">For **Destination URL**, enter *http://localhost/ContosoUniversity*.</span></span>

   <span data-ttu-id="7f9c2-258">**目标 URL**设置不需要。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-258">The **Destination URL** setting isn't required.</span></span> <span data-ttu-id="7f9c2-259">完成 Visual Studio 部署应用程序后，它会自动打开默认浏览器访问此 URL。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-259">When Visual Studio finishes deploying the application, it automatically opens your default browser to this URL.</span></span> <span data-ttu-id="7f9c2-260">如果不想要在部署后自动打开浏览器，将此框留空。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-260">If you don't want the browser to open automatically after deployment, leave this box blank.</span></span>

8. <span data-ttu-id="7f9c2-261">选择**验证连接**以验证是否设置正确无误，并可以在本地计算机上连接到 IIS。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-261">Select **Validate Connection** to verify that the settings are correct and you can connect to IIS on the local computer.</span></span>

   <span data-ttu-id="7f9c2-262">绿色复选标记验证连接成功。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-262">A green check mark verifies that the connection is successful.</span></span>

   ![发布 Web 向导连接选项卡](deploying-to-iis/_static/image27.png)

9. <span data-ttu-id="7f9c2-264">选择**下一步**转到**设置**选项卡。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-264">Select **Next** to advance to the **Settings** tab.</span></span>

10. <span data-ttu-id="7f9c2-265">**配置**下拉列表框指定要部署的生成配置。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-265">The **Configuration** drop-down box specifies the build configuration to deploy.</span></span> <span data-ttu-id="7f9c2-266">将保留该设置的默认值为**版本**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-266">Leave it set to the default value of **Release**.</span></span> <span data-ttu-id="7f9c2-267">不会将在本教程中部署了调试版本。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-267">You won't be deploying Debug builds in this tutorial.</span></span>

11. <span data-ttu-id="7f9c2-268">展开**文件发布选项**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-268">Expand **File Publish Options**.</span></span> <span data-ttu-id="7f9c2-269">选择**将应用程序中排除文件\_数据文件夹**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-269">Select **Exclude files from the App\_Data folder**.</span></span>

    <span data-ttu-id="7f9c2-270">在测试环境中，该应用程序访问本地 SQL Server Express 实例，不在的.mdf 文件中创建的数据库*应用程序\_数据*文件夹。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-270">In the test environment, the application accesses the databases that you created in the local SQL Server Express instance, not the .mdf files in the *App\_Data* folder.</span></span>

12. <span data-ttu-id="7f9c2-271">将保留**在发布期间预编译**并**删除目标处的其他文件**清除复选框。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-271">Leave the **Precompile during publishing** and **Remove additional files at destination** check boxes cleared.</span></span>

    ![设置选项卡中的文件发布选项](deploying-to-iis/_static/image15a.png)

    <span data-ttu-id="7f9c2-273">预编译是主要用于大型站点的选项。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-273">Precompiling is an option that is useful mainly for large sites.</span></span> <span data-ttu-id="7f9c2-274">它可以减少启动时间后将站点发布请求页的第一个时间。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-274">It can reduce startup time the first time a page is requested after the site is published.</span></span>

    <span data-ttu-id="7f9c2-275">不需要由于这是你第一次部署，也不会有任何文件的目标文件夹中尚未删除的其他文件。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-275">You don't need to remove additional files since this is your first deployment and there won't be any files in the destination folder yet.</span></span>

    > [!NOTE] 
    > <span data-ttu-id="7f9c2-276">如果选择**删除目标处的其他文件**对于到相同站点的后续部署，请确保你使用的预览功能，以便你提前看到在部署之前，将删除哪些文件。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-276">If you select **Remove additional files at destination** for a subsequent deployment to the same site, make sure that you use the preview feature so that you see in advance which files will be deleted before you deploy.</span></span> <span data-ttu-id="7f9c2-277">预期的行为是，Web 部署会删除已在项目中删除目标服务器上的文件。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-277">The expected behavior is that Web Deploy will delete files on the destination server that you have deleted in your project.</span></span> <span data-ttu-id="7f9c2-278">但是，在源和目标文件夹下的整个文件夹结构进行比较;并在某些情况下，Web 部署可能会删除不想要删除的文件。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-278">However, the entire folder structure under the source and destination folders is compared; and in some scenarios, Web Deploy might delete files you don't want to delete.</span></span>
    > 
    > <span data-ttu-id="7f9c2-279">例如在服务器上的子文件夹中没有的 web 应用程序项目部署到的根文件夹时，如果将删除子文件夹。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-279">For example if you have a web application in a subfolder on the server when you deploy a project to the root folder, the subfolder will be deleted.</span></span> <span data-ttu-id="7f9c2-280">你可能在 contoso.com 的主站点的一个项目，并在 contoso.com/blog 博客的另一个项目。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-280">You might have one project for the main site at contoso.com and another project for a blog at contoso.com/blog.</span></span> <span data-ttu-id="7f9c2-281">博客应用程序是一个子文件夹中。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-281">The blog application is in a subfolder.</span></span> <span data-ttu-id="7f9c2-282">如果选择**删除目标处的其他文件**博客应用程序时部署主站点时，将被删除。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-282">If you select **Remove additional files at destination** when you deploy the main site, the blog application will be deleted.</span></span>
    > 
    > <span data-ttu-id="7f9c2-283">有关其他示例，您的应用程序\_数据文件夹可能会意外删除。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-283">For another example, your App\_Data folder might get deleted unexpectedly.</span></span> <span data-ttu-id="7f9c2-284">某些数据库，例如 SQL Server Compact 数据库文件存储在应用\_数据文件夹。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-284">Certain databases such as SQL Server Compact store database files in the App\_Data folder.</span></span> <span data-ttu-id="7f9c2-285">在初始部署之后不想要保留复制的数据库文件在后续部署中，因此选择**排除应用\_数据**打包/发布 Web 选项卡上。在执行操作，如果之后有**删除目标处的其他文件**选择，将数据库文件和应用\_的下次发布时都将删除数据文件夹本身。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-285">After the initial deployment, you don't want to keep copying the database files in subsequent deployments, so you select  **Exclude App\_Data** on the Package/Publish Web tab. After you do that if you have **Remove additional files at destination** selected, your database files and the App\_Data folder itself will be deleted the next time you publish.</span></span>

### <a name="configure-deployment-for-the-membership-database"></a><span data-ttu-id="7f9c2-286">配置成员资格数据库部署</span><span class="sxs-lookup"><span data-stu-id="7f9c2-286">Configure deployment for the membership database</span></span>

<span data-ttu-id="7f9c2-287">以下步骤适用于**DefaultConnection**对话框框中的数据库**数据库**部分。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-287">The following steps apply to the **DefaultConnection** database in the dialog box's **Databases** section.</span></span>

1. <span data-ttu-id="7f9c2-288">在中**远程连接字符串**框中，输入以下指向新的 SQL Server Express 成员资格数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-288">In the **Remote connection string** box, enter the following connection string that points to the new SQL Server Express membership database.</span></span>

   [!code-console[Main](deploying-to-iis/samples/sample3.cmd)]

   <span data-ttu-id="7f9c2-289">部署过程： 将此连接字符串放入部署的 Web.config 文件，因为**在运行时使用此连接字符串**处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-289">The deployment process puts this connection string in the deployed Web.config file because **Use this connection string at runtime** is selected.</span></span>

    <span data-ttu-id="7f9c2-290">此外可以获取的连接字符串**服务器资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-290">You can also get the connection string from **Server Explorer**.</span></span> <span data-ttu-id="7f9c2-291">在中**服务器资源管理器**，展开**数据连接**，然后选择 **&lt;machinename&gt;\sqlexpress.aspnet-ContosoUniversity**数据库，然后从**属性**窗口中复制**连接字符串**值。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-291">In **Server Explorer**, expand **Data Connections** and select the **&lt;machinename&gt;\sqlexpress.aspnet-ContosoUniversity** database, then from the **Properties** window copy the **Connection String** value.</span></span> <span data-ttu-id="7f9c2-292">连接字符串将具有一个可以删除的其他设置： `Pooling=False`。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-292">That connection string will have one additional setting that you can delete: `Pooling=False`.</span></span>

2. <span data-ttu-id="7f9c2-293">选择**更新数据库**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-293">Select **Update database**.</span></span>

   <span data-ttu-id="7f9c2-294">这会导致要在部署过程中创建目标数据库中的数据库架构。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-294">This causes the database schema to be created in the destination database during deployment.</span></span> <span data-ttu-id="7f9c2-295">在下一步骤中，指定您需要运行其他脚本： 一个用于授予对默认应用程序池，一个用于部署数据的数据库访问权限。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-295">In next steps, you specify the additional scripts that you need to run: one to grant database access to the default application pool and one to deploy data.</span></span>

3. <span data-ttu-id="7f9c2-296">选择**配置数据库更新**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-296">Select **Configure database updates**.</span></span>
 
4. <span data-ttu-id="7f9c2-297">在中**配置数据库更新**对话框中，选择**添加 SQL 脚本**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-297">In the **Configure Database Updates** dialog box, select **Add SQL Script**.</span></span> <span data-ttu-id="7f9c2-298">导航到*Grant.sql*之前保存在解决方案文件夹中的脚本。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-298">Navigate to the *Grant.sql* script that you saved earlier in the solution folder.</span></span>

5. <span data-ttu-id="7f9c2-299">重复此过程以添加*aspnet 数据 dev.sql*脚本。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-299">Repeat the process to add the *aspnet-data-dev.sql* script.</span></span>

   ![为成员资格数据库配置数据库更新](deploying-to-iis/_static/image16.png)

6. <span data-ttu-id="7f9c2-301">选择**关闭**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-301">Select **Close**.</span></span>

### <a name="configure-deployment-for-the-application-database"></a><span data-ttu-id="7f9c2-302">配置应用程序数据库的部署</span><span class="sxs-lookup"><span data-stu-id="7f9c2-302">Configure deployment for the application database</span></span>

<span data-ttu-id="7f9c2-303">当 Visual Studio 检测到实体框架`DbContext`类，它创建中的条目**数据库**节，其中包含**执行 Code First 迁移**复选框，而不是**更新数据库**复选框。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-303">When Visual Studio detects an Entity Framework `DbContext` class, it creates an entry in the **Databases** section that has an **Execute Code First Migrations** check box instead of an **Update Database** check box.</span></span> <span data-ttu-id="7f9c2-304">对于本教程，将使用该复选框来指定 Code First 迁移部署。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-304">For this tutorial, you'll use that check box to specify Code First Migrations deployment.</span></span>

<span data-ttu-id="7f9c2-305">在某些情况下，你可能使用`DbContext`数据库，但想要使用 dbDacFx 提供程序而不是迁移部署数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-305">In some scenarios, you might be using a `DbContext` database but you want to use the dbDacFx provider instead of Migrations to deploy the database.</span></span> <span data-ttu-id="7f9c2-306">在这种情况下，请参阅[如何将 Code First 数据库而无需迁移的部署？](https://msdn.microsoft.com/library/ee942158.aspx#deploy_code_first_without_migrations) MSDN 上的 ASP.NET Web 部署常见问题解答中。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-306">In that case, see [How do I deploy a Code First database without Migrations?](https://msdn.microsoft.com/library/ee942158.aspx#deploy_code_first_without_migrations) in the ASP.NET Web Deployment FAQ on MSDN.</span></span>

<span data-ttu-id="7f9c2-307">以下步骤适用于**SchoolContext**对话框框中的数据库**数据库**部分。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-307">The following steps apply to the **SchoolContext** database in the dialog box's **Databases** section.</span></span>

1. <span data-ttu-id="7f9c2-308">在中**远程连接字符串**框中，输入以下指向新的 SQL Server Express 应用程序数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-308">In the **Remote connection string** box, enter the following connection string that points to the new SQL Server Express application database.</span></span>

   [!code-console[Main](deploying-to-iis/samples/sample4.cmd)]

   <span data-ttu-id="7f9c2-309">部署过程： 将此连接字符串放入部署的 Web.config 文件，因为**在运行时使用此连接字符串**处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-309">The deployment process puts this connection string in the deployed Web.config file because **Use this connection string at runtime** is selected.</span></span>

   <span data-ttu-id="7f9c2-310">此外可以获取的应用程序数据库连接字符串**服务器资源管理器**相同的方式获取成员资格数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-310">You can also get the application database connection string from **Server Explorer** in the same way you got the membership database connection string.</span></span>

2. <span data-ttu-id="7f9c2-311">选择**执行 Code First 迁移 （应用程序启动时运行）**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-311">Select **Execute Code First Migrations (runs on application start)**.</span></span>

   <span data-ttu-id="7f9c2-312">此选项将导致部署过程，配置已部署的 Web.config 文件，以指定`MigrateDatabaseToLatestVersion`初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-312">This option causes the deployment process to configure the deployed Web.config file to specify the `MigrateDatabaseToLatestVersion` initializer.</span></span> <span data-ttu-id="7f9c2-313">此初始值设定项自动更新数据库的最新版本时该应用程序部署后首次访问数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-313">This initializer automatically updates the database to the latest version when the application accesses the database for the first time after deployment.</span></span>

### <a name="configure-publish-profile-transforms"></a><span data-ttu-id="7f9c2-314">配置发布配置文件转换</span><span class="sxs-lookup"><span data-stu-id="7f9c2-314">Configure publish profile transforms</span></span>

1. <span data-ttu-id="7f9c2-315">选择**关闭**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-315">Select **Close**.</span></span> <span data-ttu-id="7f9c2-316">选择**是**当系统询问你是否要保存更改。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-316">Select **Yes** when you are asked if you want to save changes.</span></span>

2. <span data-ttu-id="7f9c2-317">在中**解决方案资源管理器**，展开**属性**，展开**PublishProfiles**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-317">In **Solution Explorer**, expand **Properties**, expand **PublishProfiles**.</span></span>

3. <span data-ttu-id="7f9c2-318">右键单击*CustomProfile.pubxml*并将其重命名*Test.pubxml*。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-318">Right-click *CustomProfile.pubxml* and rename it *Test.pubxml*.</span></span>

4. <span data-ttu-id="7f9c2-319">右键单击*Test.pubxml*。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-319">Right-click *Test.pubxml*.</span></span> <span data-ttu-id="7f9c2-320">选择**添加配置转换**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-320">Select **Add Config Transform**.</span></span>

   ![添加配置转换菜单](deploying-to-iis/_static/image17.png)

   <span data-ttu-id="7f9c2-322">Visual Studio 将创建*Web.Test.config*转换文件并将其打开。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-322">Visual Studio creates the *Web.Test.config* transform file and opens it.</span></span>

5. <span data-ttu-id="7f9c2-323">在中*Web.Test.config*转换文件中，打开后立即插入以下代码配置标记。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-323">In the *Web.Test.config* transform file, insert the following code immediately after the opening configuration tag.</span></span>

   [!code-xml[Main](deploying-to-iis/samples/sample5.xml)]

    <span data-ttu-id="7f9c2-324">当使用测试发布配置文件时，此转换设置为"Test"的环境指示器。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-324">When you use the Test publish profile, this transform sets the environment indicator to "Test".</span></span> <span data-ttu-id="7f9c2-325">在已部署的站点中，将"Contoso University"H1 标题后看到"（测试）"。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-325">In the deployed site, you'll see "(Test)" after the "Contoso University" H1 heading.</span></span>

6. <span data-ttu-id="7f9c2-326">保存并关闭文件。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-326">Save and close the file.</span></span>

7. <span data-ttu-id="7f9c2-327">右键单击*Web.Test.config*文件，然后选择**预览版转换**以确保您编码的转换生成预期的更改。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-327">Right-click the *Web.Test.config* file and select **Preview Transform** to make sure that the transform you coded produces the expected changes.</span></span>

    <span data-ttu-id="7f9c2-328">**Web.config 预览版**窗口中显示结果应用同时*Web.Release.config*转换并*Web.Test.config*转换。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-328">The **Web.config Preview** window shows the result of applying both the *Web.Release.config* transforms and the *Web.Test.config* transforms.</span></span>

### <a name="preview-the-deployment-updates"></a><span data-ttu-id="7f9c2-329">预览的部署更新</span><span class="sxs-lookup"><span data-stu-id="7f9c2-329">Preview the deployment updates</span></span>

1. <span data-ttu-id="7f9c2-330">打开**发布 Web**再次向导 (右键单击 ContosoUniversity 项目，选择**发布**，然后**预览**)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-330">Open the **Publish Web** wizard again (right-click the ContosoUniversity project, select **Publish**, then **Preview**).</span></span>

2. <span data-ttu-id="7f9c2-331">在中**预览版**对话框中，选择**开始预览**以查看将复制的文件的列表。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-331">In the **Preview** dialog box, select **Start Preview** to see a list of the files that will be copied.</span></span> 

   ![发布预览](deploying-to-iis/_static/image29.png)

   <span data-ttu-id="7f9c2-333">您还可以选择**预览版数据库**链接可查看将在成员资格数据库中运行的脚本。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-333">You can also select the **Preview database** link to see the scripts that will run in the membership database.</span></span> <span data-ttu-id="7f9c2-334">（没有运行脚本对于 Code First 迁移部署，因此没有要预览的应用程序数据库。）</span><span class="sxs-lookup"><span data-stu-id="7f9c2-334">(No scripts are run for Code First Migrations deployment, so there's nothing to preview for the application database.)</span></span>

3. <span data-ttu-id="7f9c2-335">选择“发布”。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-335">Select **Publish**.</span></span>

   <span data-ttu-id="7f9c2-336">如果 Visual Studio 不是在管理员模式下，可能会收到权限错误消息。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-336">If Visual Studio is not in administrator mode, you might get a permissions error message.</span></span> <span data-ttu-id="7f9c2-337">在这种情况下，关闭 Visual Studio，在管理员模式下打开并尝试再次发布。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-337">In that case, close Visual Studio, open it in administrator mode, and try to publish again.</span></span>

   <span data-ttu-id="7f9c2-338">在管理员模式下，Visual Studio 是否**输出**窗口报告成功生成和发布。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-338">If Visual Studio is in administrator mode, the **Output** window reports successful build and publish.</span></span>

   ![Output_window_publish_Test](deploying-to-iis/_static/image20.png)

   <span data-ttu-id="7f9c2-340">如果输入中的 URL**目标 URL**上的发布配置文件框**连接**选项卡上，浏览器会自动打开指向在 IIS 中运行您的计算机上的 Contoso University 主页上。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-340">If you entered the URL in the **Destination URL** box on the publish profile **Connection** tab, the browser automatically opens to the Contoso University Home page running in IIS on your computer.</span></span>

## <a name="test-in-the-test-environment"></a><span data-ttu-id="7f9c2-341">在测试环境中测试</span><span class="sxs-lookup"><span data-stu-id="7f9c2-341">Test in the test environment</span></span>

<span data-ttu-id="7f9c2-342">请注意，环境指示器，显示"（测试）"而不是"（开发）、"显示的*Web.config*环境指示器的转换是否成功。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-342">Notice that the environment indicator shows "(Test)" instead of "(Dev)," which shows that the *Web.config* transformation for the environment indicator was successful.</span></span>

<span data-ttu-id="7f9c2-343">运行**讲师**页以验证 Code First 种子讲师数据的数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-343">Run the **Instructors** page to verify that Code First seeded the database with instructor data.</span></span> <span data-ttu-id="7f9c2-344">当选中此页时，可能需要几分钟才能加载，因为 Code First 创建数据库并运行`Seed`方法。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-344">When you select this page, it may take a few minutes to load because Code First creates the database and then runs the `Seed` method.</span></span> <span data-ttu-id="7f9c2-345">（它没有这样做时，因为该应用程序不尝试尚未访问的数据库都在主页上。）</span><span class="sxs-lookup"><span data-stu-id="7f9c2-345">(It didn't do that when you were on the home page because the application didn't try to access the database yet.)</span></span>

<span data-ttu-id="7f9c2-346">选择**学生**选项卡以验证是否已部署的数据库具有任何学生。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-346">Select the **Students** tab to verify that the deployed database has no students.</span></span>

<span data-ttu-id="7f9c2-347">选择**添加学生**从**学生**菜单。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-347">Select **Add Students** from the **Students** menu.</span></span> <span data-ttu-id="7f9c2-348">添加一名学生，，然后查看中的新学生**学生**页。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-348">Add a student, and then view the new student in the **Students** page.</span></span> <span data-ttu-id="7f9c2-349">这将验证可以成功写入到数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-349">This verifies that you can successfully write to the database.</span></span>

<span data-ttu-id="7f9c2-350">从**课程**菜单中，选择**更新信用额度**。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-350">From the **Courses** menu, select **Update Credits**.</span></span> <span data-ttu-id="7f9c2-351">**更新信用额度**页面需要管理员权限，因此**Log In**显示页。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-351">The **Update Credits** page requires administrator permissions, so the **Log In** page is displayed.</span></span> <span data-ttu-id="7f9c2-352">输入创建早期 （"admin"和"devpwd"） 的管理员帐户凭据。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-352">Enter the administrator account credentials that you created earlier ("admin" and "devpwd").</span></span> <span data-ttu-id="7f9c2-353">**更新信用额度**显示页。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-353">The **Update Credits** page is displayed.</span></span> <span data-ttu-id="7f9c2-354">这将验证在上一教程中创建的管理员帐户已正确部署到测试环境。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-354">This verifies that the administrator account that you created in the previous tutorial was correctly deployed to the test environment.</span></span>

<span data-ttu-id="7f9c2-355">确认*ELMAH*文件夹中存在*c:\inetpub\wwwroot\ContosoUniversity*的占位符文件将在其中的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-355">Verify that an *ELMAH* folder exists in the *c:\inetpub\wwwroot\ContosoUniversity* folder with only the placeholder file in it.</span></span>

<a id="reviewingmigrations"></a>

## <a name="review-the-automatic-webconfig-changes-for-code-first-migrations"></a><span data-ttu-id="7f9c2-356">查看自动 Web.config 更改的 Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="7f9c2-356">Review the automatic Web.config changes for Code First Migrations</span></span>

<span data-ttu-id="7f9c2-357">打开*Web.config*文件中的已部署应用程序*C:\inetpub\wwwroot\ContosoUniversity* ，可以看到部署过程会自动配置 Code First 迁移到其中更新到最新版本的数据库。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-357">Open the *Web.config* file in the deployed application at *C:\inetpub\wwwroot\ContosoUniversity* and you can see where the deployment process configured Code First Migrations to automatically update the database to the latest version.</span></span>

![](deploying-to-iis/_static/image21.png)

<span data-ttu-id="7f9c2-358">部署过程还创建新的连接字符串的代码优先迁移来更新数据库架构的以独占方式使用：</span><span class="sxs-lookup"><span data-stu-id="7f9c2-358">The deployment process also created a new connection string for Code First Migrations to use exclusively for updating the database schema:</span></span>

![Database_Publish 连接字符串](deploying-to-iis/_static/image22.png)

<span data-ttu-id="7f9c2-360">此附加的连接字符串，可指定一个用户帐户对数据库架构更新和应用程序数据访问的其他用户帐户。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-360">This additional connection string enables you to specify one user account for database schema updates and a different user account for application data access.</span></span> <span data-ttu-id="7f9c2-361">例如，可将分配**db\_所有者**到 Code First 迁移角色和**db\_datareader**与**db\_datawriter**向应用程序角色。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-361">For example, you could assign the **db\_owner** role to Code First Migrations and **db\_datareader** with **db\_datawriter** roles to the application.</span></span> <span data-ttu-id="7f9c2-362">这是一种常用的深度防御模式阻止潜在的恶意代码应用程序更改数据库架构。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-362">This is a common defense-in-depth pattern that prevents potentially malicious code in the application from changing the database schema.</span></span> <span data-ttu-id="7f9c2-363">（例如，可能发生此错误在成功的 SQL 注入式攻击。）这些教程不使用此模式。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-363">(For example, this might happen in a successful SQL injection attack.) These tutorials don't use this pattern.</span></span> <span data-ttu-id="7f9c2-364">若要实现此模式在你的方案中，执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="7f9c2-364">To implement this pattern in your scenario, take these steps:</span></span>

1. <span data-ttu-id="7f9c2-365">在中**发布 Web**向导下**设置**选项卡上，输入完整的数据库架构更新权限与指定的用户的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-365">In the **Publish Web** wizard under the **Settings** tab, enter the connection string that specifies a user with full database schema update permissions.</span></span> <span data-ttu-id="7f9c2-366">清除**在运行时使用此连接字符串**复选框。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-366">Clear the **Use this connection string at runtime** check box.</span></span> <span data-ttu-id="7f9c2-367">在已部署的 Web.config 文件中，这将成为`DatabasePublish`连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-367">In the deployed Web.config file, this becomes the `DatabasePublish` connection string.</span></span>

2. <span data-ttu-id="7f9c2-368">创建 Web.config 文件转换为你想要在运行时使用的应用程序的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-368">Create a Web.config file transformation for the connection string that you want the application to use at run time.</span></span>

## <a name="summary"></a><span data-ttu-id="7f9c2-369">总结</span><span class="sxs-lookup"><span data-stu-id="7f9c2-369">Summary</span></span>

<span data-ttu-id="7f9c2-370">现在已在开发计算机上部署到 IIS 应用程序，并对其进行存在测试。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-370">You've now deployed your application to IIS on your development computer and tested it there.</span></span>

![在测试中的主页](deploying-to-iis/_static/image23.png)

<span data-ttu-id="7f9c2-372">这将验证部署过程将应用程序的内容复制到正确的位置 （不包括不想部署的文件） 和也该 Web 部署 IIS 正确配置在部署过程。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-372">This verifies that the deployment process copied the application's content to the right location (excluding the files that you did not want to deploy) and also that Web Deploy configured IIS correctly during deployment.</span></span> <span data-ttu-id="7f9c2-373">在下一步的教程中，你将运行一个测试，以发现尚未完成的部署任务： 上设置文件夹权限*Elm ah*文件夹。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-373">In the next tutorial, you'll run one more test that finds a deployment task that has not yet been done: setting folder permissions on the *Elm ah* folder.</span></span>

## <a name="more-information"></a><span data-ttu-id="7f9c2-374">详细信息</span><span class="sxs-lookup"><span data-stu-id="7f9c2-374">More information</span></span>

<span data-ttu-id="7f9c2-375">在 Visual Studio 中运行 IIS 或 IIS Express 的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="7f9c2-375">For information about running IIS or IIS Express in Visual Studio, see the following resources:</span></span>

- <span data-ttu-id="7f9c2-376">[IIS Express 概述](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview)IIS.net 站点上。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-376">[IIS Express Overview](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) on the IIS.net site.</span></span>
- <span data-ttu-id="7f9c2-377">[引入了 IIS Express](https://weblogs.asp.net/scottgu/archive/2010/06/28/introducing-iis-express.aspx) Scott Guthrie 的博客上。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-377">[Introducing IIS Express](https://weblogs.asp.net/scottgu/archive/2010/06/28/introducing-iis-express.aspx) on Scott Guthrie's blog.</span></span>
- <span data-ttu-id="7f9c2-378">[Web 服务器在 Visual Studio 中的，对 ASP.NET Web 项目](https://msdn.microsoft.com/library/58wxa9w5.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-378">[Web Servers in Visual Studio for ASP.NET Web Projects](https://msdn.microsoft.com/library/58wxa9w5.aspx).</span></span>
- <span data-ttu-id="7f9c2-379">[核心区别之间 IIS 和 ASP.NET Development Server](../../older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md) ASP.NET 站点上。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-379">[Core Differences Between IIS and the ASP.NET Development Server](../../older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md) on the ASP.NET site.</span></span>

<span data-ttu-id="7f9c2-380">在中等信任环境中运行应用程序时，可能出现什么问题的信息，请参阅[承载 ASP.NET 应用程序在中等信任](http://www.4guysfromrolla.com/articles/100307-1.aspx)上 Rolla 站点中的四个专家。</span><span class="sxs-lookup"><span data-stu-id="7f9c2-380">For information about what issues might arise when your application runs in medium trust, see [Hosting ASP.NET Applications in Medium Trust](http://www.4guysfromrolla.com/articles/100307-1.aspx) on the four Guys from Rolla site.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7f9c2-381">[上一页](project-properties.md)
> [下一页](setting-folder-permissions.md)</span><span class="sxs-lookup"><span data-stu-id="7f9c2-381">[Previous](project-properties.md)
[Next](setting-folder-permissions.md)</span></span>
