---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
title: 配置 Web 服务器的 Web 部署发布 （Web 部署处理程序） |Microsoft Docs
author: jrjlee
description: 本主题介绍如何配置 Internet 信息服务 (IIS) web 服务器以支持 web 发布和使用 IIS Web 部署 Han 部署...
ms.author: riande
ms.date: 01/29/2017
ms.assetid: 90ebf911-1c46-4470-b876-1335bd0f590f
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
msc.type: authoredcontent
ms.openlocfilehash: cf18a8860d34daa23f61e3dde13c2c79c6c0d4a5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667318"
---
# <a name="configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler"></a><span data-ttu-id="ec4c8-103">配置用于 Web 部署发布的 Web 服务器（Web 部署处理程序）</span><span class="sxs-lookup"><span data-stu-id="ec4c8-103">Configuring a Web Server for Web Deploy Publishing (Web Deploy Handler)</span></span>

[<span data-ttu-id="ec4c8-104">下载 PDF</span><span class="sxs-lookup"><span data-stu-id="ec4c8-104">Download PDF</span></span>](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> <span data-ttu-id="ec4c8-105">本主题介绍如何配置 Internet 信息服务 (IIS) web 服务器以支持 web 发布和部署使用 IIS Web 部署处理程序。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-105">This topic describes how to configure an Internet Information Services (IIS) web server to support web publishing and deployment using the IIS Web Deploy Handler.</span></span>
> 
> <span data-ttu-id="ec4c8-106">在处理 Web Deploy 2.0 或更高版本时，有三种主要方法可用于获取你的应用程序或 web 服务器上的站点。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-106">When you work with Web Deploy 2.0 or later, there are three main approaches you can use to get your applications or sites onto a web server.</span></span> <span data-ttu-id="ec4c8-107">你可以：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-107">You can:</span></span>
> 
> - <span data-ttu-id="ec4c8-108">使用*Web 部署远程代理服务*。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-108">Use the *Web Deploy Remote Agent Service*.</span></span> <span data-ttu-id="ec4c8-109">这种方法需要较少配置 web 服务器，但您需要提供的本地服务器管理员凭据才能将任何内容部署到服务器。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-109">This approach requires less configuration of the web server, but you need to provide the credentials of a local server administrator in order to deploy anything to the server.</span></span>
> - <span data-ttu-id="ec4c8-110">使用*Web 部署处理程序*。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-110">Use the *Web Deploy Handler*.</span></span> <span data-ttu-id="ec4c8-111">这种方法更复杂，需要更多的初始工作来设置 web 服务器。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-111">This approach is a lot more complex and requires more initial effort to set up the web server.</span></span> <span data-ttu-id="ec4c8-112">但是，在使用此方法时，你可以配置 IIS 以允许非管理员用户来执行部署。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-112">However, when you use this approach, you can configure IIS to allow non-administrator users to perform the deployment.</span></span> <span data-ttu-id="ec4c8-113">Web 部署处理程序只是在 IIS 7 或更高版本中可用。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-113">The Web Deploy Handler is only available in IIS version 7 or later.</span></span>
> - <span data-ttu-id="ec4c8-114">使用*离线部署*。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-114">Use *offline deployment*.</span></span> <span data-ttu-id="ec4c8-115">这种方法需要 web 服务器的最低配置，但服务器管理员必须手动复制到服务器上的 web 包并将其导入通过 IIS 管理器。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-115">This approach requires the least configuration of the web server, but a server administrator must manually copy the web package onto the server and import it through IIS Manager.</span></span>
> 
> <span data-ttu-id="ec4c8-116">主要功能、 优势和一种方法的缺点的详细信息，请参阅[选择右方法对 Web 部署](choosing-the-right-approach-to-web-deployment.md)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-116">For more information on the key features, advantages, and disadvantages of these approaches, see [Choosing the Right Approach to Web Deployment](choosing-the-right-approach-to-web-deployment.md).</span></span>


<span data-ttu-id="ec4c8-117">是，如果你想要允许非管理员用户将内容部署到特定的 IIS 网站。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-117">Yes, if you want to allow non-administrator users to deploy content to specific IIS websites.</span></span> <span data-ttu-id="ec4c8-118">这种方法通常是这些类型的方案中所需的：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-118">This approach is often desirable in these types of scenarios:</span></span>

- <span data-ttu-id="ec4c8-119">过渡或生产环境中，会触发远程部署的人员或服务帐户是不太可能有权访问的服务器管理员凭据。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-119">Staging or production environments, where the person or service account that triggers the remote deployment is unlikely to have access to the credentials of a server administrator.</span></span>
- <span data-ttu-id="ec4c8-120">托管的环境中，你想要使远程用户能够更新自己的网站，而不为他们提供 web 服务器 （或对其他任何人的网站的访问） 的完全控制。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-120">Hosted environments, where you want to give remote users the ability to update their websites without giving them full control of your web servers (or access to anyone else's websites).</span></span>

<span data-ttu-id="ec4c8-121">在开发或测试方案中，或在较小的组织中，使用服务器管理员凭据的部署内容通常是小于争用资源。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-121">In development or test scenarios, or in smaller organizations, deploying content using server administrator credentials is often less contentious.</span></span> <span data-ttu-id="ec4c8-122">在这些情况下，在 web 服务器配置为支持部署使用的[Web 部署远程代理服务](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)提供更直接的方法。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-122">In these scenarios, configuring your web servers to support deployment using the [Web Deploy Remote Agent Service](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md) offers a more straightforward approach.</span></span>

## <a name="task-overview"></a><span data-ttu-id="ec4c8-123">任务概述</span><span class="sxs-lookup"><span data-stu-id="ec4c8-123">Task Overview</span></span>

<span data-ttu-id="ec4c8-124">若要配置 web 服务器来接受和部署 web 包从远程计算机使用 Web 部署处理程序方法，将需要：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-124">To configure the web server to accept and deploy web packages from a remote computer using the Web Deploy Handler approach, you'll need to:</span></span>

- <span data-ttu-id="ec4c8-125">创建或选择域用户帐户 （"非管理员用户"） 将用来执行部署其凭据。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-125">Create, or choose, a domain user account (the "non-administrator user") whose credentials you'll use to perform deployments.</span></span>
- <span data-ttu-id="ec4c8-126">安装 IIS 7.5，包括 Web 管理服务和基本身份验证模块。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-126">Install IIS 7.5, including the Web Management Service and the Basic Authentication module.</span></span>
- <span data-ttu-id="ec4c8-127">安装 Web 部署 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-127">Install Web Deploy 2.1 or later.</span></span>
- <span data-ttu-id="ec4c8-128">配置 Web 管理服务，以允许远程连接，并启动服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-128">Configure the Web Management Service to allow remote connections, and start the service.</span></span>
- <span data-ttu-id="ec4c8-129">创建一个 IIS 网站来承载部署的内容。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-129">Create an IIS website to host the deployed content.</span></span>
- <span data-ttu-id="ec4c8-130">授予你对你的网站在 IIS 管理器的非管理员用户权限。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-130">Grant your non-administrator user permissions on your website in IIS Manager.</span></span>
- <span data-ttu-id="ec4c8-131">确保委派规则允许要添加和更改网站内容使用非管理员用户帐户的服务的 Web 管理服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-131">Ensure that the Web Management Service delegation rules permit the service to add and change website content using your non-administrator user account.</span></span>
- <span data-ttu-id="ec4c8-132">配置所有防火墙以允许端口 8172 上的传入连接。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-132">Configure any firewalls to allow incoming connections on port 8172.</span></span>

<span data-ttu-id="ec4c8-133">若要专门托管 ContactManager 示例解决方案，还需要为：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-133">To host the ContactManager sample solution specifically, you'll also need to:</span></span>

- <span data-ttu-id="ec4c8-134">安装.NET Framework 4.0。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-134">Install the .NET Framework 4.0.</span></span>
- <span data-ttu-id="ec4c8-135">安装 ASP.NET MVC 3。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-135">Install ASP.NET MVC 3.</span></span>

<span data-ttu-id="ec4c8-136">本主题将演示如何执行每个这些过程。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-136">This topic will show you how to perform each of these procedures.</span></span> <span data-ttu-id="ec4c8-137">任务和本主题中的演练假定您正在使用运行 Windows Server 2016 的全新服务器内部版本。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-137">The tasks and walkthroughs in this topic assume that you're starting with a clean server build running Windows Server 2016.</span></span> <span data-ttu-id="ec4c8-138">在继续之前，请确保：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-138">Before you continue, ensure that:</span></span>

- <span data-ttu-id="ec4c8-139">Windows 2016 Server</span><span class="sxs-lookup"><span data-stu-id="ec4c8-139">Windows Server 2016</span></span>
- <span data-ttu-id="ec4c8-140">服务器已加入域。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-140">The server is domain-joined.</span></span>
- <span data-ttu-id="ec4c8-141">在服务器具有静态 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-141">The server has a static IP address.</span></span>

> [!NOTE]
> <span data-ttu-id="ec4c8-142">有关将计算机加入到域的详细信息，请参阅[将计算机加入到域并登录](https://technet.microsoft.com/library/cc725618(v=WS.10).aspx)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-142">For more information on joining computers to a domain, see [Joining Computers to the Domain and Logging On](https://technet.microsoft.com/library/cc725618(v=WS.10).aspx).</span></span> <span data-ttu-id="ec4c8-143">有关配置静态 IP 地址的详细信息，请参阅[配置静态 IP 地址](https://technet.microsoft.com/library/cc754203(v=ws.10).aspx)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-143">For more information on configuring static IP addresses, see [Configure a Static IP Address](https://technet.microsoft.com/library/cc754203(v=ws.10).aspx).</span></span>


## <a name="install-products-and-components"></a><span data-ttu-id="ec4c8-144">安装的产品和组件</span><span class="sxs-lookup"><span data-stu-id="ec4c8-144">Install Products and Components</span></span>

<span data-ttu-id="ec4c8-145">本部分将指导您完成在 web 服务器上安装所需的产品和组件。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-145">This section will guide you through installing the required products and components on the web server.</span></span> <span data-ttu-id="ec4c8-146">在开始之前，一个好的做法是运行 Windows 更新，以确保你的服务器是完全保持最新。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-146">Before you begin, a good practice is to run Windows Update to ensure that your server is fully up to date.</span></span>

<span data-ttu-id="ec4c8-147">在这种情况下，你需要安装以下事项：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-147">In this case, you need to install these things:</span></span>

- <span data-ttu-id="ec4c8-148">**IIS 7 推荐配置**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-148">**IIS 7 Recommended Configuration**.</span></span> <span data-ttu-id="ec4c8-149">这使得**Web 服务器 (IIS)** 角色在 web 服务器上的和安装的 IIS 模块和托管的 ASP.NET 应用程序所需的组件组。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-149">This enables the **Web Server (IIS)** role on your web server and installs the set of IIS modules and components that you need in order to host an ASP.NET application.</span></span>
- <span data-ttu-id="ec4c8-150">**IIS:管理服务**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-150">**IIS: Management Service**.</span></span> <span data-ttu-id="ec4c8-151">将在 IIS 中安装 Web 管理服务 (WMSvc)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-151">This installs the Web Management Service (WMSvc) in IIS.</span></span> <span data-ttu-id="ec4c8-152">此服务使您能够远程管理 IIS 网站，并公开给客户端的 Web 部署处理程序终结点。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-152">This service enables remote management of IIS websites and exposes the Web Deploy Handler endpoint to clients.</span></span>
- <span data-ttu-id="ec4c8-153">**IIS:基本身份验证**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-153">**IIS: Basic Authentication**.</span></span> <span data-ttu-id="ec4c8-154">这会安装 IIS 基本身份验证模块。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-154">This installs the IIS Basic Authentication module.</span></span> <span data-ttu-id="ec4c8-155">此允许 Web 管理服务 (WMSvc) 进行身份验证所提供的凭据。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-155">This lets the Web Management Service (WMSvc) authenticate the credentials you provide.</span></span>
- <span data-ttu-id="ec4c8-156">**Web 部署工具 2.1 或更高版本**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-156">**Web Deployment Tool 2.1 or later**.</span></span> <span data-ttu-id="ec4c8-157">这在你的服务器上安装 Web 部署 （和其基础可执行文件，MSDeploy.exe）。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-157">This installs Web Deploy (and its underlying executable, MSDeploy.exe) on your server.</span></span> <span data-ttu-id="ec4c8-158">作为此过程的一部分，它将安装 Web 部署处理程序和与 Web 管理服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-158">As part of this process, it installs the Web Deploy Handler and integrates it with the Web Management Service.</span></span>
- <span data-ttu-id="ec4c8-159">**.NET framework 4.0**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-159">**.NET Framework 4.0**.</span></span> <span data-ttu-id="ec4c8-160">这是运行此版本的.NET Framework 生成的应用程序所需要的。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-160">This is required to run applications that were built on this version of the .NET Framework.</span></span>
- <span data-ttu-id="ec4c8-161">**ASP.NET MVC 3**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-161">**ASP.NET MVC 3**.</span></span> <span data-ttu-id="ec4c8-162">这会安装您需要运行 MVC 3 应用程序的程序集。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-162">This installs the assemblies you need to run MVC 3 applications.</span></span>

> [!NOTE]
> <span data-ttu-id="ec4c8-163">本演练介绍如何使用 Web 平台安装程序来安装和配置各种组件。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-163">This walkthrough describes the use of the Web Platform Installer to install and configure various components.</span></span> <span data-ttu-id="ec4c8-164">尽管不一定要使用 Web 平台安装程序，它通过简化了安装过程会自动检测的依赖关系以及确保始终获得最新的产品版本。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-164">Although you don't have to use the Web Platform Installer, it simplifies the installation process by automatically detecting dependencies and ensuring that you always get the latest product versions.</span></span> <span data-ttu-id="ec4c8-165">有关详细信息，请参阅[Microsoft Web 平台安装程序](https://go.microsoft.com/?linkid=9805118)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-165">For more information, see [Microsoft Web Platform Installer](https://go.microsoft.com/?linkid=9805118).</span></span>


<span data-ttu-id="ec4c8-166">**若要安装必需的产品和组件**</span><span class="sxs-lookup"><span data-stu-id="ec4c8-166">**To install the required products and components**</span></span>

1. <span data-ttu-id="ec4c8-167">下载并安装[Web 平台安装程序](https://go.microsoft.com/?linkid=9805118)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-167">Download and install the [Web Platform Installer](https://go.microsoft.com/?linkid=9805118).</span></span>
2. <span data-ttu-id="ec4c8-168">安装完成后，Web 平台安装程序将自动启动。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-168">When installation is complete, the Web Platform Installer will launch automatically.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ec4c8-169">现在可以在任何时候从启动 Web 平台安装程序**启动**菜单。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-169">You can now launch the Web Platform Installer at any time from the **Start** menu.</span></span> <span data-ttu-id="ec4c8-170">为此，请在**启动**菜单上，单击**所有程序**，然后单击**Microsoft Web 平台安装程序**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-170">To do this, on the **Start** menu, click **All Programs**, and then click **Microsoft Web Platform Installer**.</span></span>
3. <span data-ttu-id="ec4c8-171">在顶部**Web 平台安装程序**窗口中，单击**产品**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-171">At the top of the **Web Platform Installer** window, click **Products**.</span></span>
4. <span data-ttu-id="ec4c8-172">在左侧和右侧的窗口中，在导航窗格中，单击**框架**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-172">On the left side of the window, in the navigation pane, click **Frameworks**.</span></span>
5. <span data-ttu-id="ec4c8-173">在中**Microsoft.NET Framework 4**行，如果尚未安装.NET Framework，单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-173">In the **Microsoft .NET Framework 4** row, if the .NET Framework is not already installed, click **Add**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ec4c8-174">你可能已安装.NET Framework 4.0 到 Windows 更新。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-174">You may have already installed the .NET Framework 4.0 through Windows Update.</span></span> <span data-ttu-id="ec4c8-175">如果已安装的产品或组件，Web 平台安装程序将此信息指示通过替换**外**按钮，其文本**已安装**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-175">If a product or component is already installed, the Web Platform Installer will indicate this by replacing the **Add** button with the text **Installed**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image1.png)
6. <span data-ttu-id="ec4c8-176">在中**ASP.NET MVC 3 (Visual Studio 2010)** 行中，单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-176">In the **ASP.NET MVC 3 (Visual Studio 2010)** row, click **Add**.</span></span>
7. <span data-ttu-id="ec4c8-177">在导航窗格中，单击**Server**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-177">In the navigation pane, click **Server**.</span></span>
8. <span data-ttu-id="ec4c8-178">在 **IIS 7 建议配置** 行中，单击 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-178">In the **IIS 7 Recommended Configuration** row, click **Add**.</span></span>
9. <span data-ttu-id="ec4c8-179">在中**Web 部署工具 2.1**行中，单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-179">In the **Web Deployment Tool 2.1** row, click **Add**.</span></span>
10. <span data-ttu-id="ec4c8-180">在**IIS:基本身份验证**行中，单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-180">In the **IIS: Basic Authentication** row, click **Add**.</span></span>
11. <span data-ttu-id="ec4c8-181">在**IIS:管理服务**行中，单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-181">In the **IIS: Management Service** row, click **Add**.</span></span>
12. <span data-ttu-id="ec4c8-182">单击“安装” 。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-182">Click **Install**.</span></span> <span data-ttu-id="ec4c8-183">Web 平台安装程序将显示产品列表&#x2014;以及任何关联的依赖关系&#x2014;安装，并将提示你接受许可条款。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-183">The Web Platform Installer will show you a list of products&#x2014;together with any associated dependencies&#x2014;to be installed and will prompt you to accept the license terms.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image2.png)
13. <span data-ttu-id="ec4c8-184">查看许可条款，如果同意条款，单击**我接受**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-184">Review the license terms, and if you consent to the terms, click **I Accept**.</span></span>
14. <span data-ttu-id="ec4c8-185">安装完成后，单击**完成**，然后关闭**Web 平台安装程序**窗口。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-185">When the installation is complete, click **Finish**, and then close the **Web Platform Installer** window.</span></span>

<span data-ttu-id="ec4c8-186">如果在安装 IIS 之前安装.NET Framework 4.0，您将需要运行[ASP.NET IIS 注册工具](https://msdn.microsoft.com/library/k6h9cz8h(v=VS.100).aspx)(aspnet\_regiis.exe) 若要向 IIS 注册 ASP.NET 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-186">If you installed the .NET Framework 4.0 before you installed IIS, you'll need to run the [ASP.NET IIS Registration Tool](https://msdn.microsoft.com/library/k6h9cz8h(v=VS.100).aspx) (aspnet\_regiis.exe) to register the latest version of ASP.NET with IIS.</span></span> <span data-ttu-id="ec4c8-187">如果不这样做，您会发现 IIS 将 （如 HTML 文件） 中提供静态内容没有任何问题，但它将返回**HTTP 错误 404.0-未找到**尝试浏览到 ASP.NET 内容时。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-187">If you don't do this, you'll find that IIS will serve static content (like HTML files) without any problems, but it will return **HTTP Error 404.0 – Not Found** when you attempt to browse to ASP.NET content.</span></span> <span data-ttu-id="ec4c8-188">下一步过程可用于确保在注册 ASP.NET 4.0。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-188">You can use the next procedure to ensure that ASP.NET 4.0 is registered.</span></span>

<span data-ttu-id="ec4c8-189">**若要向 IIS 注册 ASP.NET 4.0**</span><span class="sxs-lookup"><span data-stu-id="ec4c8-189">**To register ASP.NET 4.0 with IIS**</span></span>

1. <span data-ttu-id="ec4c8-190">单击**启动**，然后键入**命令提示符下**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-190">Click **Start**, and then type **Command Prompt**.</span></span>
2. <span data-ttu-id="ec4c8-191">在搜索结果中，右键单击**命令提示符**，然后单击**以管理员身份运行**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-191">In the search results, right-click **Command Prompt**, and then click **Run as administrator**.</span></span>
3. <span data-ttu-id="ec4c8-192">在命令提示符窗口中， 导航到 **%WINDIR%\Microsoft.NET\Framework\v4.0.30319** 目录。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-192">In the Command Prompt window, navigate to the **%WINDIR%\Microsoft.NET\Framework\v4.0.30319** directory.</span></span>
4. <span data-ttu-id="ec4c8-193">键入以下命令，然后按 Enter:</span><span class="sxs-lookup"><span data-stu-id="ec4c8-193">Type this command, and then press Enter:</span></span>

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample1.cmd)]
5. <span data-ttu-id="ec4c8-194">如果计划托管 64 位 web 应用程序，任何时候，则应该向 IIS 注册 ASP.NET 的 64 位版本。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-194">If you plan to host 64-bit web applications at any point, you should also register the 64-bit version of ASP.NET with IIS.</span></span> <span data-ttu-id="ec4c8-195">为此，请在命令提示符窗口中，导航到 **%WINDIR%\Microsoft.NET\Framework64\v4.0.30319** 目录。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-195">To do this, in the Command Prompt window, navigate to the **%WINDIR%\Microsoft.NET\Framework64\v4.0.30319** directory.</span></span>
6. <span data-ttu-id="ec4c8-196">键入以下命令，然后按 Enter:</span><span class="sxs-lookup"><span data-stu-id="ec4c8-196">Type this command, and then press Enter:</span></span>

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample2.cmd)]

<span data-ttu-id="ec4c8-197">很好的做法是，Windows 更新再次使用此时若要下载并安装新的产品和组件已安装所有可用更新。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-197">As a good practice, use Windows Update again at this point to download and install any available updates for the new products and components you've installed.</span></span>

## <a name="configure-the-web-management-service"></a><span data-ttu-id="ec4c8-198">配置 Web 管理服务</span><span class="sxs-lookup"><span data-stu-id="ec4c8-198">Configure the Web Management Service</span></span>

<span data-ttu-id="ec4c8-199">至此，已安装所需的一切下, 一步是在 IIS 中配置 Web 管理服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-199">Now that you've installed everything you need, the next step is to configure the Web Management Service in IIS.</span></span> <span data-ttu-id="ec4c8-200">在高级别中，将需要完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-200">At a high level, you'll need to complete these tasks:</span></span>

- <span data-ttu-id="ec4c8-201">启用基本身份验证在服务器级别。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-201">Enable basic authentication at the server level.</span></span>
- <span data-ttu-id="ec4c8-202">Web 管理服务配置为接受远程连接。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-202">Configure the Web Management Service to accept remote connections.</span></span>
- <span data-ttu-id="ec4c8-203">启动 Web 管理服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-203">Start the Web Management Service.</span></span>
- <span data-ttu-id="ec4c8-204">检查所需的 Web 管理服务委派规则已到位。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-204">Check that the required Web Management Service delegation rules are in place.</span></span>

<span data-ttu-id="ec4c8-205">**若要配置 Web 管理服务**</span><span class="sxs-lookup"><span data-stu-id="ec4c8-205">**To configure the Web Management Service**</span></span>

1. <span data-ttu-id="ec4c8-206">上**启动**菜单，依次指向**管理工具**，然后单击**Internet Information Services (IIS) Manager**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-206">On the **Start** menu, point to **Administrative Tools**, and then click **Internet Information Services (IIS) Manager**.</span></span>
2. <span data-ttu-id="ec4c8-207">在 IIS 管理器中，在**连接**窗格中，单击服务器节点 (例如， **STAGEWEB1**)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-207">In IIS Manager, in the **Connections** pane, click the server node (for example, **STAGEWEB1**).</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image3.png)
3. <span data-ttu-id="ec4c8-208">在中心窗格中下, **IIS**，双击**身份验证**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-208">In the center pane, under **IIS**, double-click **Authentication**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image20.png)
4. <span data-ttu-id="ec4c8-209">右键单击**基本身份验证**，然后单击**启用**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-209">Right-click **Basic Authentication**, and then click **Enable**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image5.png)
5. <span data-ttu-id="ec4c8-210">在中**连接**窗格中，单击服务器节点，以返回到顶级设置。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-210">In the **Connections** pane, click the server node again to return to the top-level settings.</span></span>
6. <span data-ttu-id="ec4c8-211">在中心窗格中下,**管理**，双击**管理服务**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-211">In the center pane, under **Management**, double-click **Management Service**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image6.png)
7. <span data-ttu-id="ec4c8-212">在中心窗格中，选择**启用远程连接**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-212">In the center pane, select **Enable remote connections**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ec4c8-213">如果已在运行 Web 管理服务，你将需要首先停止。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-213">If the Web Management Service is already running, you'll need to stop it first.</span></span>
8. <span data-ttu-id="ec4c8-214">在中**操作**窗格中，单击**启动**启动 Web 管理服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-214">In the **Actions** pane, click **Start** to start the Web Management Service.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image7.png)
9. <span data-ttu-id="ec4c8-215">如果系统提示保存设置，请单击**是**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-215">If you're prompted to save your settings, click **Yes**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="ec4c8-216">您可能想要将服务配置为自动启动。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-216">You may also want to configure the service to start automatically.</span></span> <span data-ttu-id="ec4c8-217">若要执行此操作，打开服务控制台，右键单击**Web Management Service**，然后单击**属性**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-217">To do this, open the Services console, right-click **Web Management Service**, and then click **Properties**.</span></span> <span data-ttu-id="ec4c8-218">在中**启动类型**下拉列表中选择**自动**，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-218">In the **Startup type** dropdown list, select **Automatic**, and then click **OK**.</span></span>
10. <span data-ttu-id="ec4c8-219">在中**连接**窗格中，单击服务器节点，以返回到顶级设置。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-219">In the **Connections** pane, click the server node again to return to the top-level settings.</span></span>
11. <span data-ttu-id="ec4c8-220">在中心窗格中下,**管理**，双击**管理服务的委派**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-220">In the center pane, under **Management**, double-click **Management Service Delegation**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image8.png)
12. <span data-ttu-id="ec4c8-221">验证在中心窗格包含一组规则。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-221">Verify that the center pane contains a set of rules.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image9.png)

    <span data-ttu-id="ec4c8-222">这些规则允许经授权的 Web Management Service 用户使用各种 Web Deploy 提供程序。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-222">These rules allow authorized Web Management Service users to use various Web Deploy providers.</span></span> <span data-ttu-id="ec4c8-223">例如，若要部署到通过 Web 部署处理程序的 IIS web 应用程序和内容，必须有一个允许所有经过身份验证 Web Management Service 用户使用的委派规则**contentPath**和**iisApp**提供程序 （您所见的屏幕截图中的最后一个规则）。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-223">For example, to deploy web applications and content to IIS through the Web Deploy Handler, there must be a delegation rule that allows all authenticated Web Management Service users to use the **contentPath** and **iisApp** providers (the last rule that you can see in the screenshot).</span></span>

    <span data-ttu-id="ec4c8-224">如果你的产品和组件的顺序安装本主题中所述，最新版本的 Web 部署自动应将所有所需的委派规则添加到 Web 管理服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-224">If you installed products and components in the order described in this topic, the latest version of Web Deploy should automatically add all the required delegation rules to the Web Management Service.</span></span> <span data-ttu-id="ec4c8-225">如果管理服务的委派页未显示任何规则，你将需要自行创建。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-225">If the Management Service Delegation page does not show any rules, you'll need to create them yourself.</span></span> <span data-ttu-id="ec4c8-226">有关如何执行此操作的说明，请参阅[配置 Web 部署处理程序](https://go.microsoft.com/?linkid=9805124)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-226">For instructions on how to do this, see [Configure the Web Deployment Handler](https://go.microsoft.com/?linkid=9805124).</span></span>
13. <span data-ttu-id="ec4c8-227">在中**连接**窗格中，单击服务器节点，以返回到顶级设置。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-227">In the **Connections** pane, click the server node again to return to the top-level settings.</span></span>

## <a name="create-and-configure-an-iis-website"></a><span data-ttu-id="ec4c8-228">创建和配置 IIS 网站</span><span class="sxs-lookup"><span data-stu-id="ec4c8-228">Create and Configure an IIS Website</span></span>

<span data-ttu-id="ec4c8-229">可以将 web 内容部署到你的服务器之前，您需要创建和配置 IIS 网站来承载的内容。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-229">Before you can deploy web content to your server, you need to create and configure an IIS website to host the content.</span></span> <span data-ttu-id="ec4c8-230">Web 部署可以仅将 web 包部署到现有 IIS 网站;它不能为您创建的网站。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-230">Web Deploy can only deploy web packages to an existing IIS website; it can't create the website for you.</span></span> <span data-ttu-id="ec4c8-231">此外需要执行一些额外的配置，以允许非管理员帐户将远程部署内容。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-231">You also need to do a little extra configuration to allow your non-administrator account to deploy content remotely.</span></span> <span data-ttu-id="ec4c8-232">在高级别中，将需要完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-232">At a high level, you'll need to complete these tasks:</span></span>

- <span data-ttu-id="ec4c8-233">在文件系统来托管你的内容上创建一个文件夹。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-233">Create a folder on the file system to host your content.</span></span>
- <span data-ttu-id="ec4c8-234">创建 IIS 网站提供内容，并将其与本地文件夹关联。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-234">Create an IIS website to serve the content, and associate it with the local folder.</span></span>
- <span data-ttu-id="ec4c8-235">授予读取权限的本地文件夹上的应用程序池标识。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-235">Grant read permissions to the application pool identity on the local folder.</span></span>
- <span data-ttu-id="ec4c8-236">授予对部署的 web 应用程序的域帐户所需的 IIS 权限。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-236">Grant the necessary IIS permissions to the domain account that will deploy your web application.</span></span>

<span data-ttu-id="ec4c8-237">尽管没有什么措施可以阻止您从内容部署到 IIS 中的默认网站，但不建议使用此方法用于测试或演示方案之外的任何内容。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-237">Although there's nothing stopping you from deploying content to the default website in IIS, this approach is not recommended for anything other than test or demonstration scenarios.</span></span> <span data-ttu-id="ec4c8-238">若要模拟生产环境中，应使用特定于应用程序的要求的设置创建新的 IIS 网站。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-238">To simulate a production environment, you should create a new IIS website with settings that are specific to the requirements of your application.</span></span>

<span data-ttu-id="ec4c8-239">**若要创建的 IIS 网站**</span><span class="sxs-lookup"><span data-stu-id="ec4c8-239">**To create an IIS website**</span></span>

1. <span data-ttu-id="ec4c8-240">在本地文件系统上创建一个文件夹以存储你的内容 (例如， **C:\DemoSite**)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-240">On the local file system, create a folder to store your content (for example, **C:\DemoSite**).</span></span>
2. <span data-ttu-id="ec4c8-241">上**启动**菜单，依次指向**管理工具**，然后单击**Internet Information Services (IIS) Manager**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-241">On the **Start** menu, point to **Administrative Tools**, and then click **Internet Information Services (IIS) Manager**.</span></span>
3. <span data-ttu-id="ec4c8-242">在 IIS 管理器中，在**连接**窗格中，展开服务器节点 (例如， **STAGEWEB1**)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-242">In IIS Manager, in the **Connections** pane, expand the server node (for example, **STAGEWEB1**).</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image10.png)
4. <span data-ttu-id="ec4c8-243">右键单击**站点**节点，并单击**添加网站**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-243">Right-click the **Sites** node, and then click **Add Web Site**.</span></span>
5. <span data-ttu-id="ec4c8-244">在中**站点名称**框中，键入为 IIS 网站的名称 (例如， **DemoSite**)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-244">In the **Site name** box, type a name for the IIS website (for example, **DemoSite**).</span></span>
6. <span data-ttu-id="ec4c8-245">在中**物理路径**框中，键入 （或浏览到） 到本地文件夹的路径 (例如， **C:\DemoSite**)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-245">In the **Physical path** box, type (or browse to) the path to your local folder (for example, **C:\DemoSite**).</span></span>
7. <span data-ttu-id="ec4c8-246">在中**端口**框中，键入你想要托管网站的端口号 (例如， **85**)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-246">In the **Port** box, type the port number on which you want to host the website (for example, **85**).</span></span>

    > [!NOTE]
    > <span data-ttu-id="ec4c8-247">标准端口号是 80 用于 HTTP 和 HTTPS 的 443。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-247">The standard port numbers are 80 for HTTP and 443 for HTTPS.</span></span> <span data-ttu-id="ec4c8-248">但是，如果托管该网站在端口 80 上的，你需要停止默认网站，然后才能访问你的站点。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-248">However, if you host this website on port 80, you'll need to stop the default website before you can access your site.</span></span>
8. <span data-ttu-id="ec4c8-249">将保留**主机名**框保留为空，除非你想要配置该网站的域名系统 (DNS) 记录，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-249">Leave the **Host name** box blank, unless you want to configure a Domain Name System (DNS) record for the website, and then click **OK**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image11.png)

    > [!NOTE]
    > <span data-ttu-id="ec4c8-250">在生产环境中，你将很可能想要托管你的网站在端口 80 上并配置主机标头，以及匹配的 DNS 记录。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-250">In a production environment, you'll likely want to host your website on port 80 and configure a host header, together with matching DNS records.</span></span> <span data-ttu-id="ec4c8-251">在 IIS 7 中配置主机标头的详细信息，请参阅[为网站 (IIS 7) 配置主机标头](https://technet.microsoft.com/library/cc753195(WS.10).aspx)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-251">For more information on configuring host headers in IIS 7, see [Configure a Host Header for a Web Site (IIS 7)](https://technet.microsoft.com/library/cc753195(WS.10).aspx).</span></span> <span data-ttu-id="ec4c8-252">Windows Server 中的 DNS 服务器角色的详细信息，请参阅[DNS 服务器概述](https://technet.microsoft.com/en-gb/library/cc770392.aspx)并[DNS 服务器](https://technet.microsoft.com/windowsserver/dd448607)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-252">For more information on the DNS Server role in Windows Server, see [DNS Server Overview](https://technet.microsoft.com/en-gb/library/cc770392.aspx) and [DNS Server](https://technet.microsoft.com/windowsserver/dd448607).</span></span>
9. <span data-ttu-id="ec4c8-253">在中**操作**窗格下**编辑站点**，单击**绑定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-253">In the **Actions** pane, under **Edit Site**, click **Bindings**.</span></span>
10. <span data-ttu-id="ec4c8-254">在中**站点绑定**对话框中，单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-254">In the **Site Bindings** dialog box, click **Add**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image12.png)
11. <span data-ttu-id="ec4c8-255">在中**添加网站绑定**对话框中，将**IP 地址**并**端口**以匹配您现有的站点配置。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-255">In the **Add Site Binding** dialog box, set the **IP address** and **Port** to match your existing site configuration.</span></span>
12. <span data-ttu-id="ec4c8-256">在中**主机名**框中，键入你的 web 服务器的名称 (例如， **STAGEWEB1**)，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-256">In the **Host name** box, type the name of your web server (for example, **STAGEWEB1**), and then click **OK**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image13.png)

    > [!NOTE]
    > <span data-ttu-id="ec4c8-257">第一个站点绑定使你可以访问站点使用的 IP 地址和端口在本地或`http://localhost:85`。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-257">The first site binding allows you to access the site locally using the IP address and port or `http://localhost:85`.</span></span> <span data-ttu-id="ec4c8-258">第二个站点绑定使你可以使用计算机名称在域上的其他计算机访问站点 (例如， http://stageweb1:85)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-258">The second site binding allows you to access the site from other computers on the domain using the machine name (for example, http://stageweb1:85).</span></span>
13. <span data-ttu-id="ec4c8-259">在中**站点绑定**对话框中，单击**关闭**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-259">In the **Site Bindings** dialog box, click **Close**.</span></span>
14. <span data-ttu-id="ec4c8-260">在中**连接**窗格中，单击**应用程序池**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-260">In the **Connections** pane, click **Application Pools**.</span></span>
15. <span data-ttu-id="ec4c8-261">在中**应用程序池**窗格中，右键单击你的应用程序池的名称，然后单击**基本设置**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-261">In the **Application Pools** pane, right-click the name of your application pool, and then click **Basic Settings**.</span></span> <span data-ttu-id="ec4c8-262">默认情况下，应用程序池的名称将与你的网站的名称匹配 (例如， **DemoSite**)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-262">By default, the name of your application pool will match the name of your website (for example, **DemoSite**).</span></span>
16. <span data-ttu-id="ec4c8-263">在中 **.NET CLR 版本**列表中，选择 **.NET CLR v4.0.30319**，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-263">In the **.NET CLR version** list, select **.NET CLR v4.0.30319**, and then click **OK**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image21.png)

    > [!NOTE]
    > <span data-ttu-id="ec4c8-264">示例解决方案需要.NET Framework 4.0。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-264">The sample solution requires .NET Framework 4.0.</span></span> <span data-ttu-id="ec4c8-265">这不是必需的 Web 部署一般情况下。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-265">This is not a requirement for Web Deploy in general.</span></span>

<span data-ttu-id="ec4c8-266">为了使你的网站提供内容，应用程序池标识必须具有读取存储内容的本地文件夹的权限。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-266">In order for your website to serve content, the application pool identity must have read permissions on the local folder that stores the content.</span></span> <span data-ttu-id="ec4c8-267">在 IIS 7.5 应用程序池以运行唯一的应用程序池标识 （与以前版本的 IIS，其中应用程序池通常运行使用网络服务帐户） 默认情况下。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-267">In IIS 7.5, application pools run with a unique application pool identity by default (in contrast to previous versions of IIS, where application pools would typically run using the Network Service account).</span></span> <span data-ttu-id="ec4c8-268">应用程序池标识不是真实的用户帐户和未出现在任何用户或组的列表&#x2014;相反，它动态启动时创建的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-268">The application pool identity is not a real user account and does not show up on any lists of users or groups&#x2014;instead, it's created dynamically when the application pool is started.</span></span> <span data-ttu-id="ec4c8-269">每个应用程序池标识添加到本地**IIS\_IUSRS**隐藏项的安全组。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-269">Each application pool identity is added to the local **IIS\_IUSRS** security group as a hidden item.</span></span>

<span data-ttu-id="ec4c8-270">若要授予对文件或文件夹上的应用程序池标识的权限，您有两个选项：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-270">To grant permissions to an application pool identity on a file or folder, you have two options:</span></span>

- <span data-ttu-id="ec4c8-271">将权限分配给应用程序池标识直接，使用格式<strong>IIS AppPool\</strong ><em>[应用程序池名称]</em>(例如， <strong>IIS AppPool\DemoSite</strong>).</span><span class="sxs-lookup"><span data-stu-id="ec4c8-271">Assign permissions to the application pool identity directly, using the format <strong>IIS AppPool\</strong><em>[application pool name]</em>(for example, <strong>IIS AppPool\DemoSite</strong>).</span></span>
- <span data-ttu-id="ec4c8-272">向其分配权限**IIS\_IUSRS**组。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-272">Assign permissions to the **IIS\_IUSRS** group.</span></span>

<span data-ttu-id="ec4c8-273">最常用的方法是将权限分配给本地**IIS\_IUSRS**组，因为这种方法允许您更改应用程序池，而无需重新配置的文件系统权限。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-273">The most common approach is to assign permissions to the local **IIS\_IUSRS** group, because this approach lets you change application pools without reconfiguring file system permissions.</span></span> <span data-ttu-id="ec4c8-274">下一个过程中使用此基于组的方法。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-274">The next procedure uses this group-based approach.</span></span>

> [!NOTE]
> <span data-ttu-id="ec4c8-275">在 IIS 7.5 的应用程序池标识的详细信息，请参阅[应用程序池标识](https://go.microsoft.com/?linkid=9805123)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-275">For more information on application pool identities in IIS 7.5, see [Application Pool Identities](https://go.microsoft.com/?linkid=9805123).</span></span>


<span data-ttu-id="ec4c8-276">**若要配置 IIS 网站的文件夹权限**</span><span class="sxs-lookup"><span data-stu-id="ec4c8-276">**To configure folder permissions for an IIS website**</span></span>

1. <span data-ttu-id="ec4c8-277">在 Windows 资源管理器，浏览到本地文件夹的位置。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-277">In Windows Explorer, browse to the location of your local folder.</span></span>
2. <span data-ttu-id="ec4c8-278">右键单击文件夹，然后依次**属性**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-278">Right-click the folder, and then click **Properties**.</span></span>
3. <span data-ttu-id="ec4c8-279">上**安全**选项卡上，单击**编辑**，然后单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-279">On the **Security** tab, click **Edit**, and then click **Add**.</span></span>
4. <span data-ttu-id="ec4c8-280">单击**位置**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-280">Click **Locations**.</span></span> <span data-ttu-id="ec4c8-281">在中**位置**对话框中，选择本地服务器，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-281">In the **Locations** dialog box, select the local server, and then click **OK**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image15.png)
5. <span data-ttu-id="ec4c8-282">在中**选择用户或组**对话框中，键入**IIS\_IUSRS**，单击**检查名称**，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-282">In the **Select Users or Groups** dialog box, type **IIS\_IUSRS**, click **Check Names**, and then click **OK**.</span></span>
6. <span data-ttu-id="ec4c8-283">在中<strong>的权限</strong><em>[文件夹名称]</em>对话框中，请注意，新的组已分配<strong>读取&amp;执行</strong>，<strong>列出文件夹内容</strong>，并<strong>读取</strong>默认情况下的权限。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-283">In the <strong>Permissions for</strong><em>[folder name]</em> dialog box, notice that the new group has been assigned the <strong>Read &amp; execute</strong>, <strong>List folder contents</strong>, and <strong>Read</strong> permissions by default.</span></span> <span data-ttu-id="ec4c8-284">保留此保持不变，然后单击<strong>确定</strong>。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-284">Leave this unchanged and click <strong>OK</strong>.</span></span>
7. <span data-ttu-id="ec4c8-285">单击<strong>确定</strong>以关闭<em>[文件夹名称]</em><strong>属性</strong>对话框。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-285">Click <strong>OK</strong> to close the <em>[folder name]</em><strong>Properties</strong> dialog box.</span></span>

<span data-ttu-id="ec4c8-286">作为最后一项任务，必须为非管理员用户将用来将内容部署其凭据授予适当的权限。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-286">As a final task, you must grant the appropriate permissions to the non-administrator user whose credentials you'll use to deploy content.</span></span> <span data-ttu-id="ec4c8-287">此用户需要远程将内容部署到你的网站的权限。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-287">This user requires the permissions to deploy content remotely to your website.</span></span>

<span data-ttu-id="ec4c8-288">**若要配置的 IIS 网站权限的非管理员域用户**</span><span class="sxs-lookup"><span data-stu-id="ec4c8-288">**To configure IIS website permissions for a non-administrator domain user**</span></span>

1. <span data-ttu-id="ec4c8-289">在 IIS 管理器中，在**连接**窗格中，右键单击你网站的节点 (例如， **DemoSite**)，指向**部署**，然后单击**配置 Web部署发布**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-289">In IIS Manager, in the **Connections** pane, right-click your website node (for example, **DemoSite**), point to **Deploy**, and then click **Configure Web Deploy Publishing**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image16.png)
2. <span data-ttu-id="ec4c8-290">在中**配置 Web 部署发布**对话框中的，右侧**选择要授予发布权限的用户**列表中，单击省略号按钮。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-290">In the **Configure Web Deploy Publishing** dialog box, to the right of the **Select a user to give publishing permissions** list, click the ellipsis button.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image17.png)
3. <span data-ttu-id="ec4c8-291">在中**允许用户**对话框框中，键入你想要使用以部署内容，然后单击的帐户的域和用户**确定**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-291">In the **Allow User** dialog box, type the domain and user name of the account you want to use to deploy content, and then click **OK**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image18.png)
4. <span data-ttu-id="ec4c8-292">在中**配置 Web 部署发布**对话框中，单击**安装程序**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-292">In the **Configure Web Deploy Publishing** dialog box, click **Setup**.</span></span>

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image19.png)

    > [!NOTE]
    > <span data-ttu-id="ec4c8-293">此操作在一个步骤中执行两个关键功能。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-293">This operation performs two key functions in one step.</span></span> <span data-ttu-id="ec4c8-294">首先，它会根据上一部分中检查委派规则授予用户权限来修改 Web 管理服务，通过远程网站。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-294">First, it grants the user permission to modify the website remotely through the Web Management Service, according to the delegation rules you examined in the previous section.</span></span> <span data-ttu-id="ec4c8-295">其次，它会授予用户完全控制的网站，这样用户就可以添加、 修改和设置权限的网站内容的源文件夹。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-295">Second, it grants the user full control of the source folder for the website, which allows the user to add, modify, and set permissions on the website content.</span></span>
5. <span data-ttu-id="ec4c8-296">在中**配置 Web 部署发布**对话框中，单击**关闭**。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-296">In the **Configure Web Deploy Publishing** dialog box, click **Close**.</span></span>

## <a name="configure-firewall-exceptions"></a><span data-ttu-id="ec4c8-297">配置防火墙例外</span><span class="sxs-lookup"><span data-stu-id="ec4c8-297">Configure Firewall Exceptions</span></span>

<span data-ttu-id="ec4c8-298">默认情况下，IIS Web 管理服务侦听 TCP 端口 8172。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-298">By default, the IIS Web Management Service listens on TCP port 8172.</span></span> <span data-ttu-id="ec4c8-299">如果 web 服务器上启用 Windows 防火墙，您将需要创建一个新入站的规则以允许端口 8172 上的 TCP 流量 （默认情况下，Windows 防火墙中允许所有出站流量）。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-299">If Windows Firewall is enabled on your web server, you'll need to create a new inbound rule to allow TCP traffic on port 8172 (all outbound traffic is permitted by default in Windows Firewall).</span></span> <span data-ttu-id="ec4c8-300">如果使用第三方防火墙，你将需要创建规则以允许流量。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-300">If you use a third-party firewall, you'll need to create rules to allow traffic.</span></span>

| <span data-ttu-id="ec4c8-301">方向</span><span class="sxs-lookup"><span data-stu-id="ec4c8-301">Direction</span></span> | <span data-ttu-id="ec4c8-302">从端口</span><span class="sxs-lookup"><span data-stu-id="ec4c8-302">From Port</span></span> | <span data-ttu-id="ec4c8-303">到端口</span><span class="sxs-lookup"><span data-stu-id="ec4c8-303">To Port</span></span> | <span data-ttu-id="ec4c8-304">端口类型</span><span class="sxs-lookup"><span data-stu-id="ec4c8-304">Port Type</span></span> |
| --- | --- | --- | --- |
| <span data-ttu-id="ec4c8-305">入站</span><span class="sxs-lookup"><span data-stu-id="ec4c8-305">Inbound</span></span> | <span data-ttu-id="ec4c8-306">任意</span><span class="sxs-lookup"><span data-stu-id="ec4c8-306">Any</span></span> | <span data-ttu-id="ec4c8-307">8172</span><span class="sxs-lookup"><span data-stu-id="ec4c8-307">8172</span></span> | <span data-ttu-id="ec4c8-308">TCP</span><span class="sxs-lookup"><span data-stu-id="ec4c8-308">TCP</span></span> |
| <span data-ttu-id="ec4c8-309">出站</span><span class="sxs-lookup"><span data-stu-id="ec4c8-309">Outbound</span></span> | <span data-ttu-id="ec4c8-310">8172</span><span class="sxs-lookup"><span data-stu-id="ec4c8-310">8172</span></span> | <span data-ttu-id="ec4c8-311">任意</span><span class="sxs-lookup"><span data-stu-id="ec4c8-311">Any</span></span> | <span data-ttu-id="ec4c8-312">TCP</span><span class="sxs-lookup"><span data-stu-id="ec4c8-312">TCP</span></span> |
  

<span data-ttu-id="ec4c8-313">在 Windows 防火墙中配置规则的详细信息，请参阅[配置防火墙规则](https://technet.microsoft.com/library/dd448559(WS.10).aspx)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-313">For more information on configuring rules in Windows Firewall, see [Configuring Firewall Rules](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span></span> <span data-ttu-id="ec4c8-314">对于第三方防火墙，请参阅产品文档。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-314">For third-party firewalls, please consult your product documentation.</span></span>

## <a name="conclusion"></a><span data-ttu-id="ec4c8-315">结束语</span><span class="sxs-lookup"><span data-stu-id="ec4c8-315">Conclusion</span></span>

<span data-ttu-id="ec4c8-316">你的 web 服务器现在应已准备好接受远程部署到 Web 部署处理程序中通过 Web 管理服务。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-316">Your web server should now be ready to accept remote deployments to the Web Deploy Handler through the Web Management Service.</span></span> <span data-ttu-id="ec4c8-317">在尝试部署到服务器的 web 应用程序之前，你可能想要检查这些关键点：</span><span class="sxs-lookup"><span data-stu-id="ec4c8-317">Before you attempt to deploy a web application to the server, you may want to check these key points:</span></span>

- <span data-ttu-id="ec4c8-318">已启用服务器级别在 IIS 中的基本身份验证？</span><span class="sxs-lookup"><span data-stu-id="ec4c8-318">Have you enabled basic authentication at the server level in IIS?</span></span>
- <span data-ttu-id="ec4c8-319">已启用远程连接到 Web 管理服务？</span><span class="sxs-lookup"><span data-stu-id="ec4c8-319">Have you enabled remote connections to the Web Management Service?</span></span>
- <span data-ttu-id="ec4c8-320">已启动 Web 管理服务？</span><span class="sxs-lookup"><span data-stu-id="ec4c8-320">Have you started the Web Management Service?</span></span>
- <span data-ttu-id="ec4c8-321">是否有管理服务委派规则在位置？</span><span class="sxs-lookup"><span data-stu-id="ec4c8-321">Are there management service delegation rules in place?</span></span>
- <span data-ttu-id="ec4c8-322">没有应用程序池标识具有读取访问权限的源文件夹的你的网站？</span><span class="sxs-lookup"><span data-stu-id="ec4c8-322">Does the application pool identity have read access to the source folder for your website?</span></span>
- <span data-ttu-id="ec4c8-323">非管理员用户帐户是否在 IIS 中拥有站点级别的权限？</span><span class="sxs-lookup"><span data-stu-id="ec4c8-323">Does the non-administrator user account have site-level permissions in IIS?</span></span>
- <span data-ttu-id="ec4c8-324">你的防火墙是否允许传入连接到 TCP 端口 8172 上的服务器？</span><span class="sxs-lookup"><span data-stu-id="ec4c8-324">Does your firewall allow incoming connections to the server on TCP port 8172?</span></span>

## <a name="further-reading"></a><span data-ttu-id="ec4c8-325">其他阅读材料</span><span class="sxs-lookup"><span data-stu-id="ec4c8-325">Further Reading</span></span>

<span data-ttu-id="ec4c8-326">有关如何配置自定义 Microsoft Build Engine (MSBuild) 项目文件，以将 web 包部署到 Web 部署处理程序的指导，请参阅[配置目标环境的部署属性](configuring-deployment-properties-for-a-target-environment.md)。</span><span class="sxs-lookup"><span data-stu-id="ec4c8-326">For guidance on how to configure custom Microsoft Build Engine (MSBuild) project files to deploy web packages to the Web Deploy Handler, see [Configuring Deployment Properties for a Target Environment](configuring-deployment-properties-for-a-target-environment.md).</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ec4c8-327">[上一页](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)
> [下一页](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md)</span><span class="sxs-lookup"><span data-stu-id="ec4c8-327">[Previous](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)
[Next](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md)</span></span>
