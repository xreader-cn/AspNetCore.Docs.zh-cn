---
title: 将 ASP.NET Core 应用发布到 IIS
author: rick-anderson
description: 了解如何在 IIS 服务器上托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/publish-to-iis
ms.openlocfilehash: 47f78ba78741a8e0175ce801c0c0e51f091273a8
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511387"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="b89c3-103">将 ASP.NET Core 应用发布到 IIS</span><span class="sxs-lookup"><span data-stu-id="b89c3-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="b89c3-104">本教程介绍如何在 IIS 服务器上托管 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b89c3-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="b89c3-105">本教程涵盖以下主题：</span><span class="sxs-lookup"><span data-stu-id="b89c3-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b89c3-106">在 Windows Server 上安装.NET Core Hosting Bundle。</span><span class="sxs-lookup"><span data-stu-id="b89c3-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="b89c3-107">在 IIS 管理器中创建 IIS 站点。</span><span class="sxs-lookup"><span data-stu-id="b89c3-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="b89c3-108">部署 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b89c3-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b89c3-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="b89c3-109">Prerequisites</span></span>

* <span data-ttu-id="b89c3-110">[.NET Core SDK](/dotnet/core/sdk) 安装在开发计算机上。</span><span class="sxs-lookup"><span data-stu-id="b89c3-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="b89c3-111">Windows Server 配置了“Web 服务器 (IIS)”服务器角色  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="b89c3-112">如果服务器未配置为托管具有 IIS 的网站，请按照 <xref:host-and-deploy/iis/index#iis-configuration> 文章中“IIS 配置”部分的指南操作，然后返回本教程  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="b89c3-113">IIS 配置和网站安全涉及到本教程未介绍的概念。 </span><span class="sxs-lookup"><span data-stu-id="b89c3-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="b89c3-114">在 IIS 上托管生产应用之前，请先参阅 [Microsoft IIS 文档](https://www.iis.net/)中的 IIS 指南和[有关使用 IIS 进行托管的 ASP.NET Core 文章](xref:host-and-deploy/iis/index)。</span><span class="sxs-lookup"><span data-stu-id="b89c3-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="b89c3-115">本教程未介绍的 IIS 托管的重要方案包括：</span><span class="sxs-lookup"><span data-stu-id="b89c3-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="b89c3-116">为 ASP.NET Core 数据保护创建注册表配置单元</span><span class="sxs-lookup"><span data-stu-id="b89c3-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="b89c3-117">配置应用池的访问控制列表 (ACL)</span><span class="sxs-lookup"><span data-stu-id="b89c3-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="b89c3-118">为了重点介绍 IIS 部署概念，本教程部署了一个没有在 IIS 中配置 HTTPS 安全性的应用。</span><span class="sxs-lookup"><span data-stu-id="b89c3-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="b89c3-119">有关托管为 HTTPS 协议启用的应用的详细信息，请参阅本文[其他资源](#additional-resources)部分中的安全主题。</span><span class="sxs-lookup"><span data-stu-id="b89c3-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="b89c3-120">有关托管 ASP.NET 核心应用的更多指南，请参阅 <xref:host-and-deploy/iis/index> 文章。</span><span class="sxs-lookup"><span data-stu-id="b89c3-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="b89c3-121">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="b89c3-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="b89c3-122">在 IIS 服务器上安装 .NET Core 托管捆绑包  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="b89c3-123">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="b89c3-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="b89c3-124">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="b89c3-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="b89c3-125">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="b89c3-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="b89c3-126">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="b89c3-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="b89c3-127">在 IIS 服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="b89c3-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="b89c3-128">重启服务器或在命令行界面中执行 net stop was /y，后跟 net start w3svc   。</span><span class="sxs-lookup"><span data-stu-id="b89c3-128">Restart the server or execute **net stop was /y** followed by **net start w3svc** in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="b89c3-129">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="b89c3-129">Create the IIS site</span></span>

1. <span data-ttu-id="b89c3-130">在 IIS 服务器上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="b89c3-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="b89c3-131">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="b89c3-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span>

1. <span data-ttu-id="b89c3-132">在 IIS 管理器中，打开“连接”  面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="b89c3-132">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="b89c3-133">右键单击“站点”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="b89c3-133">Right-click the **Sites** folder.</span></span> <span data-ttu-id="b89c3-134">选择上下文菜单中的“添加网站”  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-134">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="b89c3-135">提供网站名称，并将“物理路径”设置为所创建应用的部署文件夹   。</span><span class="sxs-lookup"><span data-stu-id="b89c3-135">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="b89c3-136">提供“绑定”配置，并通过选择“确定”创建网站   。</span><span class="sxs-lookup"><span data-stu-id="b89c3-136">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

## <a name="create-an-aspnet-core-razor-pages-app"></a><span data-ttu-id="b89c3-137">创建 ASP.NET Core Razor Pages 应用</span><span class="sxs-lookup"><span data-stu-id="b89c3-137">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="b89c3-138">按照 <xref:getting-started> 教程创建 Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="b89c3-138">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="b89c3-139">发布和部署应用</span><span class="sxs-lookup"><span data-stu-id="b89c3-139">Publish and deploy the app</span></span>

<span data-ttu-id="b89c3-140">发布应用意味着生成可由服务器托管的编译应用  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-140">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="b89c3-141">部署应用意味着将发布的应用移动到托管系统  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-141">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="b89c3-142">发布步骤由 [.NET Core SDK ](/dotnet/core/sdk) 处理，而部署步骤可以通过各种方法处理。</span><span class="sxs-lookup"><span data-stu-id="b89c3-142">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="b89c3-143">本教程采用“文件夹”部署方法，即  ：</span><span class="sxs-lookup"><span data-stu-id="b89c3-143">This tutorial adopts the *folder* deployment approach, where:</span></span>

* <span data-ttu-id="b89c3-144">将应用发布到一个文件夹。</span><span class="sxs-lookup"><span data-stu-id="b89c3-144">The app is published to a folder.</span></span>
* <span data-ttu-id="b89c3-145">文件夹的内容将移动到 IIS 站点的文件夹（IIS 管理器中站点的物理路径）  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-145">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b89c3-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b89c3-146">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b89c3-147">在“解决方案资源管理器”  中右键单击该项目，然后选择“发布”  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-147">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="b89c3-148">在“选择发布目标”对话框中，选择“文件夹”发布选项   。</span><span class="sxs-lookup"><span data-stu-id="b89c3-148">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="b89c3-149">设置“文件夹或文件共享”路径  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-149">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="b89c3-150">如果为开发计算机上可用作网络共享的 IIS 站点创建了一个文件夹，请提供该共享的路径。</span><span class="sxs-lookup"><span data-stu-id="b89c3-150">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="b89c3-151">当前用户必须具有写入权限才能发布到共享。</span><span class="sxs-lookup"><span data-stu-id="b89c3-151">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="b89c3-152">如果无法直接部署到 IIS 服务器上的 IIS 站点文件夹，请发布到可移动介质上的文件夹，并将已发布的应用物理移动到服务器上的 IIS 站点文件夹，该文件夹是该站点在 IIS 管理器中的物理路径  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-152">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="b89c3-153">将 bin/Release/{TARGET FRAMEWORK}/publish 文件夹的内容移动到服务器上的 IIS 站点文件夹，该文件夹是该站点在 IIS 管理器中的物理路径   。</span><span class="sxs-lookup"><span data-stu-id="b89c3-153">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b89c3-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b89c3-154">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="b89c3-155">在命令 shell 中，使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令在发布配置中发布应用：</span><span class="sxs-lookup"><span data-stu-id="b89c3-155">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="b89c3-156">将 bin/Release/{TARGET FRAMEWORK}/publish 文件夹的内容移动到服务器上的 IIS 站点文件夹，该文件夹是该站点在 IIS 管理器中的物理路径   。</span><span class="sxs-lookup"><span data-stu-id="b89c3-156">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b89c3-157">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b89c3-157">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b89c3-158">右键单击“解决方案”中的项目，然后选择“发布” > “发布到文件夹”    。</span><span class="sxs-lookup"><span data-stu-id="b89c3-158">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="b89c3-159">设置“选择文件夹”路径  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-159">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="b89c3-160">如果为开发计算机上可用作网络共享的 IIS 站点创建了一个文件夹，请提供该共享的路径。</span><span class="sxs-lookup"><span data-stu-id="b89c3-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="b89c3-161">当前用户必须具有写入权限才能发布到共享。</span><span class="sxs-lookup"><span data-stu-id="b89c3-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="b89c3-162">如果无法直接部署到 IIS 服务器上的 IIS 站点文件夹，请发布到可移动介质上的文件夹，并将已发布的应用物理移动到服务器上的 IIS 站点文件夹，该文件夹是该站点在 IIS 管理器中的物理路径  。</span><span class="sxs-lookup"><span data-stu-id="b89c3-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="b89c3-163">将 bin/Release/{TARGET FRAMEWORK}/publish 文件夹的内容移动到服务器上的 IIS 站点文件夹，该文件夹是该站点在 IIS 管理器中的物理路径   。</span><span class="sxs-lookup"><span data-stu-id="b89c3-163">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="b89c3-164">浏览网站</span><span class="sxs-lookup"><span data-stu-id="b89c3-164">Browse the website</span></span>

<span data-ttu-id="b89c3-165">应用收到第一个请求后，可以在浏览器中访问该应用。</span><span class="sxs-lookup"><span data-stu-id="b89c3-165">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="b89c3-166">在站点的 IIS 管理器中创建的终结点绑定上发出对应用的请求。</span><span class="sxs-lookup"><span data-stu-id="b89c3-166">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b89c3-167">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b89c3-167">Next steps</span></span>

<span data-ttu-id="b89c3-168">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="b89c3-168">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="b89c3-169">在 Windows Server 上安装.NET Core Hosting Bundle。</span><span class="sxs-lookup"><span data-stu-id="b89c3-169">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="b89c3-170">在 IIS 管理器中创建 IIS 站点。</span><span class="sxs-lookup"><span data-stu-id="b89c3-170">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="b89c3-171">部署 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b89c3-171">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="b89c3-172">若要了解有关在 IIS 上托管 ASP.NET Core 应用的详细信息，请参阅 IIS 概述文章：</span><span class="sxs-lookup"><span data-stu-id="b89c3-172">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="b89c3-173">其他资源</span><span class="sxs-lookup"><span data-stu-id="b89c3-173">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="b89c3-174">ASP.NET Core 文档集中的文章</span><span class="sxs-lookup"><span data-stu-id="b89c3-174">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="b89c3-175">有关 ASP.NET Core 应用部署的文章</span><span class="sxs-lookup"><span data-stu-id="b89c3-175">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="b89c3-176">使用 Visual Studio for Mac 将 Web 应用发布到文件夹</span><span class="sxs-lookup"><span data-stu-id="b89c3-176">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="b89c3-177">有关 IIS HTTPS 配置的文章</span><span class="sxs-lookup"><span data-stu-id="b89c3-177">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="b89c3-178">在 IIS 管理器中配置 SSL</span><span class="sxs-lookup"><span data-stu-id="b89c3-178">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="b89c3-179">如何在 IIS 上设置 SSL</span><span class="sxs-lookup"><span data-stu-id="b89c3-179">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="b89c3-180">有关 IIS 和 Windows Server 的文章</span><span class="sxs-lookup"><span data-stu-id="b89c3-180">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="b89c3-181">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="b89c3-181">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="b89c3-182">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="b89c3-182">Windows Server technical content library</span></span>](/windows-server/windows-server)
