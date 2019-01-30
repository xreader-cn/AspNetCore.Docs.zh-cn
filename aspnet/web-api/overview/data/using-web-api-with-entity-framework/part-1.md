---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: 通过 Entity Framework 6 使用 Web API 2 |Microsoft Docs
author: MikeWasson
description: 本教程将讲述使用 ASP.NET Web API 创建 web 应用程序的基础知识后端。 本教程使用 Entity Framework 6 的数据布局...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236518"
---
<a name="using-web-api-2-with-entity-framework-6"></a><span data-ttu-id="23ec7-104">通过 Entity Framework 6 使用 Web API 2</span><span class="sxs-lookup"><span data-stu-id="23ec7-104">Using Web API 2 with Entity Framework 6</span></span>
====================

[<span data-ttu-id="23ec7-105">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="23ec7-105">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

> <span data-ttu-id="23ec7-106">本教程介绍使用 ASP.NET Web API 创建 web 应用程序的基础知识后端。</span><span class="sxs-lookup"><span data-stu-id="23ec7-106">This tutorial teaches you the basics of creating a web application with an ASP.NET Web API back end.</span></span> <span data-ttu-id="23ec7-107">本教程使用 Entity Framework 6 进行的数据层和 Knockout.js 进行客户端的 JavaScript 应用程序。</span><span class="sxs-lookup"><span data-stu-id="23ec7-107">The tutorial uses Entity Framework 6 for the data layer, and Knockout.js for the client-side JavaScript application.</span></span> <span data-ttu-id="23ec7-108">本教程还演示如何将应用部署到 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="23ec7-108">The tutorial also shows how to deploy the app to Azure App Service Web Apps.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="23ec7-109">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="23ec7-109">Software versions used in the tutorial</span></span>
>
> - <span data-ttu-id="23ec7-110">Web API 2.1</span><span class="sxs-lookup"><span data-stu-id="23ec7-110">Web API 2.1</span></span>
> - <span data-ttu-id="23ec7-111">Visual Studio 2017 (下载 Visual Studio 2017[此处](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span><span class="sxs-lookup"><span data-stu-id="23ec7-111">Visual Studio 2017 (download Visual Studio 2017 [here](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span></span>
> - <span data-ttu-id="23ec7-112">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="23ec7-112">Entity Framework 6</span></span>
> - <span data-ttu-id="23ec7-113">.NET 4.7</span><span class="sxs-lookup"><span data-stu-id="23ec7-113">.NET 4.7</span></span>
> - <span data-ttu-id="23ec7-114">[Knockout.js](http://knockoutjs.com/) 3.1</span><span class="sxs-lookup"><span data-stu-id="23ec7-114">[Knockout.js](http://knockoutjs.com/) 3.1</span></span>

<span data-ttu-id="23ec7-115">本教程中使用 Entity Framework 6 与 ASP.NET Web API 2 创建操作后端数据库的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="23ec7-115">This tutorial uses ASP.NET Web API 2 with Entity Framework 6 to create a web application that manipulates a back-end database.</span></span> <span data-ttu-id="23ec7-116">下面是您将创建的应用程序的屏幕截图。</span><span class="sxs-lookup"><span data-stu-id="23ec7-116">Here is a screen shot of the application that you will create.</span></span>

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

<span data-ttu-id="23ec7-117">该应用使用单页面应用程序 (SPA) 设计。</span><span class="sxs-lookup"><span data-stu-id="23ec7-117">The app uses a single-page application (SPA) design.</span></span> <span data-ttu-id="23ec7-118">"单页面应用程序"是加载单个 HTML 页面，然后页面动态更新，而不是加载新页面的 web 应用程序的常规术语。</span><span class="sxs-lookup"><span data-stu-id="23ec7-118">"Single-page application" is the general term for a web application that loads a single HTML page and then updates the page dynamically, instead of loading new pages.</span></span> <span data-ttu-id="23ec7-119">初始页面加载后该应用通过 AJAX 请求与服务器通信。</span><span class="sxs-lookup"><span data-stu-id="23ec7-119">After the initial page load, the app talks with the server through AJAX requests.</span></span> <span data-ttu-id="23ec7-120">AJAX 请求返回 JSON 数据时，应用程序用它来更新 UI。</span><span class="sxs-lookup"><span data-stu-id="23ec7-120">The AJAX requests return JSON data, which the app uses to update the UI.</span></span>

<span data-ttu-id="23ec7-121">AJAX 并不新鲜，但如今有更加轻松地构建和维护大型复杂的 SPA 应用程序的 JavaScript 框架。</span><span class="sxs-lookup"><span data-stu-id="23ec7-121">AJAX isn't new, but today there are JavaScript frameworks that make it easier to build and maintain a large sophisticated SPA application.</span></span> <span data-ttu-id="23ec7-122">本教程使用[Knockout.js](http://knockoutjs.com/)，但您可以使用任何 JavaScript 客户端框架。</span><span class="sxs-lookup"><span data-stu-id="23ec7-122">This tutorial uses [Knockout.js](http://knockoutjs.com/), but you can use any JavaScript client framework.</span></span>

<span data-ttu-id="23ec7-123">下面是此应用的主要构建基块：</span><span class="sxs-lookup"><span data-stu-id="23ec7-123">Here are the main building blocks for this app:</span></span>

- <span data-ttu-id="23ec7-124">ASP.NET MVC 创建 HTML 页面。</span><span class="sxs-lookup"><span data-stu-id="23ec7-124">ASP.NET MVC creates the HTML page.</span></span>
- <span data-ttu-id="23ec7-125">ASP.NET Web API 处理 AJAX 请求，并返回 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="23ec7-125">ASP.NET Web API handles the AJAX requests and returns JSON data.</span></span>
- <span data-ttu-id="23ec7-126">Knockout.js 数据绑定 HTML 元素的 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="23ec7-126">Knockout.js data-binds the HTML elements to the JSON data.</span></span>
- <span data-ttu-id="23ec7-127">实体框架与数据库通信。</span><span class="sxs-lookup"><span data-stu-id="23ec7-127">Entity Framework talks to the database.</span></span>

## <a name="see-this-app-running-on-azure"></a><span data-ttu-id="23ec7-128">请参阅在 Azure 上运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="23ec7-128">See this app running on Azure</span></span>

<span data-ttu-id="23ec7-129">若要查看已完成的站点作为实时 web 应用运行吗？</span><span class="sxs-lookup"><span data-stu-id="23ec7-129">Would you like to see the finished site running as a live web app?</span></span> <span data-ttu-id="23ec7-130">通过选择下面的按钮，可以将完整版本的应用部署到 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="23ec7-130">You can deploy a complete version of the app to your Azure account by selecting the following button.</span></span>

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

<span data-ttu-id="23ec7-131">需要一个 Azure 帐户才能将此解决方案部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="23ec7-131">You need an Azure account to deploy this solution to Azure.</span></span> <span data-ttu-id="23ec7-132">如果你还没有帐户，你具有以下选项：</span><span class="sxs-lookup"><span data-stu-id="23ec7-132">If you do not already have an account, you have the following options:</span></span>

- <span data-ttu-id="23ec7-133">[免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604)-获取信用额度可用于试用付费版 Azure 服务，甚至在用之后最多可以保留帐户并使用免费的 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="23ec7-133">[Open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="23ec7-134">[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604)-MSDN 订阅提供信用额度可以用于付费版 Azure 服务的每个月。</span><span class="sxs-lookup"><span data-stu-id="23ec7-134">[Activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="23ec7-135">创建项目</span><span class="sxs-lookup"><span data-stu-id="23ec7-135">Create the project</span></span>

<span data-ttu-id="23ec7-136">打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="23ec7-136">Open Visual Studio.</span></span> <span data-ttu-id="23ec7-137">从**文件**菜单中，选择**新建**，然后选择**项目**。</span><span class="sxs-lookup"><span data-stu-id="23ec7-137">From the **File** menu, select **New**, then select **Project**.</span></span> <span data-ttu-id="23ec7-138">(或选择**新的项目**起始页上。)</span><span class="sxs-lookup"><span data-stu-id="23ec7-138">(Or select **New Project** on the Start page.)</span></span>

<span data-ttu-id="23ec7-139">在**新的项目**对话框中，选择**Web**左窗格中并**ASP.NET Web 应用程序 (.NET Framework)** 在中间窗格中。</span><span class="sxs-lookup"><span data-stu-id="23ec7-139">In the **New Project** dialog, select **Web** in the left pane and **ASP.NET Web Application (.NET Framework)** in the middle pane.</span></span> <span data-ttu-id="23ec7-140">将项目命名**BookService** ，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="23ec7-140">Name the project **BookService** and select **OK**.</span></span>

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

<span data-ttu-id="23ec7-141">在中**新建 ASP.NET 项目**对话框中，选择**Web API**模板。</span><span class="sxs-lookup"><span data-stu-id="23ec7-141">In the **New ASP.NET Project** dialog, select the **Web API** template.</span></span>

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


<span data-ttu-id="23ec7-142">选择“确定”创建项目。</span><span class="sxs-lookup"><span data-stu-id="23ec7-142">Select **OK** to create the project.</span></span>

## <a name="configure-azure-settings-optional"></a><span data-ttu-id="23ec7-143">配置 Azure 设置 （可选）</span><span class="sxs-lookup"><span data-stu-id="23ec7-143">Configure Azure settings (optional)</span></span>

<span data-ttu-id="23ec7-144">创建项目后，你可以选择在任何时候将部署到 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="23ec7-144">After you create the project, you can choose to deploy to Azure App Service Web Apps at any time.</span></span> 

1. <span data-ttu-id="23ec7-145">在解决方案资源管理器中右键单击项目，然后选择**发布**。</span><span class="sxs-lookup"><span data-stu-id="23ec7-145">In Solution Explorer, right-click on your project and select **Publish**.</span></span>

2. <span data-ttu-id="23ec7-146">在出现的窗口，选择**启动**。</span><span class="sxs-lookup"><span data-stu-id="23ec7-146">In the window that appears, select **Start**.</span></span> <span data-ttu-id="23ec7-147">**选取发布目标**对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="23ec7-147">The **Pick a publish target** dialog box appears.</span></span>

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. <span data-ttu-id="23ec7-148">选择“创建配置文件”。</span><span class="sxs-lookup"><span data-stu-id="23ec7-148">Select **Create Profile**.</span></span> <span data-ttu-id="23ec7-149">“创建应用服务”对话框随即显示。</span><span class="sxs-lookup"><span data-stu-id="23ec7-149">The **Create App Service** dialog box appears.</span></span>

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   <span data-ttu-id="23ec7-150">接受默认值，或输入应用程序名称、 资源组，托管计划、 Azure 订阅和地理区域的不同值。</span><span class="sxs-lookup"><span data-stu-id="23ec7-150">Accept the defaults, or enter different values for the application name, resource group, hosting plan, Azure subscription, and geographical region.</span></span> 

4. <span data-ttu-id="23ec7-151">选择**创建 SQL 数据库**。</span><span class="sxs-lookup"><span data-stu-id="23ec7-151">Select **Create a SQL database**.</span></span> <span data-ttu-id="23ec7-152">**配置 SQL Server**对话框随即出现。</span><span class="sxs-lookup"><span data-stu-id="23ec7-152">The **Configure SQL Server** dialog box appears.</span></span> 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   <span data-ttu-id="23ec7-153">接受默认值或输入不同的值。</span><span class="sxs-lookup"><span data-stu-id="23ec7-153">Accept the defaults or enter different values.</span></span> <span data-ttu-id="23ec7-154">输入**管理员用户名**并**管理员密码**为新的数据库。</span><span class="sxs-lookup"><span data-stu-id="23ec7-154">Enter an **Adminstrator Username** and **Administrator Password** for your new database.</span></span> <span data-ttu-id="23ec7-155">选择**确定**完成后。</span><span class="sxs-lookup"><span data-stu-id="23ec7-155">Select **OK** when you're done.</span></span> <span data-ttu-id="23ec7-156">**创建应用服务**页上再次出现。</span><span class="sxs-lookup"><span data-stu-id="23ec7-156">The **Create App Service** page reappears.</span></span>

5. <span data-ttu-id="23ec7-157">选择**创建**创建你的配置文件。</span><span class="sxs-lookup"><span data-stu-id="23ec7-157">Select **Create** to create your profile.</span></span> <span data-ttu-id="23ec7-158">指示部署正在进行的右下角出现一条消息。</span><span class="sxs-lookup"><span data-stu-id="23ec7-158">A message appears in the lower-right corner indicating that deployment is in progress.</span></span> <span data-ttu-id="23ec7-159">之后不久，**发布**窗口重新出现。</span><span class="sxs-lookup"><span data-stu-id="23ec7-159">After a short while, the **Publish** window reappears.</span></span>

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    <span data-ttu-id="23ec7-160">创建要部署的应用的配置文件现已推出。</span><span class="sxs-lookup"><span data-stu-id="23ec7-160">The profile you created to deploy the app is now available.</span></span> 


> [!div class="step-by-step"]
> [<span data-ttu-id="23ec7-161">下一页</span><span class="sxs-lookup"><span data-stu-id="23ec7-161">Next</span></span>](part-2.md)
