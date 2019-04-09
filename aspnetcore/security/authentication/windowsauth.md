---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: scottaddie
description: 了解如何在 ASP.NET Core，使用 IIS Express、 IIS 和 HTTP.sys 中配置 Windows 身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/03/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 9b53f523cf579aeb0e7dd37ccf5f161269a54913
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068256"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="3b08f-103">在 ASP.NET Core 中配置 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="3b08f-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="3b08f-104">通过[Scott Addie](https://twitter.com/Scott_Addie)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3b08f-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3b08f-105">[Windows 身份验证](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)可以配置为使用托管的 ASP.NET Core 应用[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="3b08f-106">Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3b08f-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="3b08f-107">使用 Active Directory 域标识或 Windows 帐户标识的用户在公司网络上运行你的服务器时，可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="3b08f-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="3b08f-108">Windows 身份验证是最适合于用户、 客户端应用程序和 web 服务器属于同一个 Windows 域的 intranet 环境。</span><span class="sxs-lookup"><span data-stu-id="3b08f-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="3b08f-109">启用 Windows 身份验证在 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="3b08f-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="3b08f-110">**Web 应用程序**模板可通过 Visual Studio 或.NET Core CLI 可以配置为支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="3b08f-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="3b08f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b08f-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="3b08f-112">新的项目中使用 Windows 身份验证应用程序模板</span><span class="sxs-lookup"><span data-stu-id="3b08f-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="3b08f-113">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="3b08f-113">In Visual Studio:</span></span>

1. <span data-ttu-id="3b08f-114">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="3b08f-114">Create a new project.</span></span>
1. <span data-ttu-id="3b08f-115">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="3b08f-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3b08f-116">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3b08f-116">Select **Next**.</span></span>
1. <span data-ttu-id="3b08f-117">中提供名称**项目名称**字段。</span><span class="sxs-lookup"><span data-stu-id="3b08f-117">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="3b08f-118">确认**位置**条目是否正确，或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="3b08f-118">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3b08f-119">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b08f-119">Select **Create**.</span></span>
1. <span data-ttu-id="3b08f-120">选择**更改**下**身份验证**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-120">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="3b08f-121">在中**更改身份验证**窗口中，选择**Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-121">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="3b08f-122">选择 **确定**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-122">Select **OK**.</span></span>
1. <span data-ttu-id="3b08f-123">选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="3b08f-123">Select **Web Application**.</span></span>
1. <span data-ttu-id="3b08f-124">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b08f-124">Select **Create**.</span></span>

<span data-ttu-id="3b08f-125">运行应用。</span><span class="sxs-lookup"><span data-stu-id="3b08f-125">Run the app.</span></span> <span data-ttu-id="3b08f-126">用户名将显示在呈现的应用程序用户界面。</span><span class="sxs-lookup"><span data-stu-id="3b08f-126">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="3b08f-127">手动配置为现有项目的</span><span class="sxs-lookup"><span data-stu-id="3b08f-127">Manual configuration for an existing project</span></span>

<span data-ttu-id="3b08f-128">项目的属性，您可以启用 Windows 身份验证并禁用匿名身份验证：</span><span class="sxs-lookup"><span data-stu-id="3b08f-128">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="3b08f-129">右键单击 Visual Studio 中的项目**解决方案资源管理器**，然后选择**属性**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-129">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="3b08f-130">选择“调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="3b08f-130">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="3b08f-131">清除的复选框**启用匿名身份验证**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-131">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="3b08f-132">选中的复选框**启用 Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-132">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="3b08f-133">或者，可以在配置属性`iisSettings`的节点*launchSettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="3b08f-133">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# [<a name="net-core-cli"></a><span data-ttu-id="3b08f-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b08f-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3b08f-135">使用**Windows 身份验证**应用模板。</span><span class="sxs-lookup"><span data-stu-id="3b08f-135">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="3b08f-136">执行[dotnet 新](/dotnet/core/tools/dotnet-new)命令`webapp`自变量 （ASP.NET Core Web 应用） 和`--auth Windows`切换：</span><span class="sxs-lookup"><span data-stu-id="3b08f-136">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

<span data-ttu-id="3b08f-137">在修改现有项目，确认项目文件包含的包引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)**或** [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="3b08f-137">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="3b08f-138">启用与 IIS Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="3b08f-138">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="3b08f-139">IIS 使用[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)到承载 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="3b08f-139">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="3b08f-140">Windows 身份验证配置为通过 IIS *web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="3b08f-140">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="3b08f-141">以下部分介绍如何：</span><span class="sxs-lookup"><span data-stu-id="3b08f-141">The following sections show how to:</span></span>

* <span data-ttu-id="3b08f-142">提供本地*web.config*在部署应用时在服务器激活 Windows 身份验证的文件。</span><span class="sxs-lookup"><span data-stu-id="3b08f-142">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="3b08f-143">使用 IIS 管理器配置*web.config*已部署到服务器的 ASP.NET Core 应用的文件。</span><span class="sxs-lookup"><span data-stu-id="3b08f-143">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="3b08f-144">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="3b08f-144">IIS configuration</span></span>

<span data-ttu-id="3b08f-145">如果尚未执行此操作，使 IIS 能够承载 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="3b08f-145">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="3b08f-146">有关详细信息，请参阅 <xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="3b08f-146">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="3b08f-147">启用 Windows 身份验证的 IIS 角色服务。</span><span class="sxs-lookup"><span data-stu-id="3b08f-147">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="3b08f-148">有关详细信息，请参阅[IIS 角色服务 （请参阅步骤 2） 中启用 Windows 身份验证](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-148">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="3b08f-149">默认情况下，IIS 集成中间件配置为自动进行身份验证请求。</span><span class="sxs-lookup"><span data-stu-id="3b08f-149">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="3b08f-150">有关详细信息，请参阅[与 IIS 的 Windows 上托管 ASP.NET Core:IIS 选项 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-150">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="3b08f-151">默认情况下，ASP.NET Core 模块配置为转发到应用的 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="3b08f-151">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="3b08f-152">有关详细信息，请参阅[ASP.NET Core 模块配置参考：AspNetCore 元素的特性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-152">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="3b08f-153">创建新的 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="3b08f-153">Create a new IIS site</span></span>

<span data-ttu-id="3b08f-154">指定的名称和文件夹，并允许它创建新的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="3b08f-154">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="3b08f-155">启用 IIS 中应用的 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="3b08f-155">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="3b08f-156">使用**任一**以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3b08f-156">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="3b08f-157">[发布应用程序之前对开发端配置](#development-side-configuration-with-a-local-webconfig-file)(*推荐*)</span><span class="sxs-lookup"><span data-stu-id="3b08f-157">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="3b08f-158">发布应用后端服务器的配置</span><span class="sxs-lookup"><span data-stu-id="3b08f-158">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="3b08f-159">开发端配置与本地 web.config 文件</span><span class="sxs-lookup"><span data-stu-id="3b08f-159">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="3b08f-160">执行以下步骤**之前**您[发布和部署你的项目](#publish-and-deploy-your-project-to-the-iis-site-folder)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-160">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="3b08f-161">添加以下*web.config*到项目根目录的文件：</span><span class="sxs-lookup"><span data-stu-id="3b08f-161">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="3b08f-162">由 SDK 发布项目时 (而无需`<IsTransformWebConfigDisabled>`属性设置为`true`项目文件中)，已发布*web.config*文件包括`<location><system.webServer><security><authentication>`部分。</span><span class="sxs-lookup"><span data-stu-id="3b08f-162">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="3b08f-163">有关详细信息`<IsTransformWebConfigDisabled>`属性，请参阅<xref:host-and-deploy/iis/index#webconfig-file>。</span><span class="sxs-lookup"><span data-stu-id="3b08f-163">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="3b08f-164">服务器端配置使用 IIS 管理器</span><span class="sxs-lookup"><span data-stu-id="3b08f-164">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="3b08f-165">执行以下步骤**后**您[发布和部署你的项目](#publish-and-deploy-your-project-to-the-iis-site-folder)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-165">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="3b08f-166">在 IIS 管理器中，选择下的 IIS 站点**站点**的节点**连接**侧栏。</span><span class="sxs-lookup"><span data-stu-id="3b08f-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="3b08f-167">双击**身份验证**中**IIS**区域。</span><span class="sxs-lookup"><span data-stu-id="3b08f-167">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="3b08f-168">选择**匿名身份验证**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="3b08f-169">选择**禁用**中**操作**侧栏。</span><span class="sxs-lookup"><span data-stu-id="3b08f-169">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="3b08f-170">选择**Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="3b08f-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="3b08f-171">选择**启用**中**操作**侧栏。</span><span class="sxs-lookup"><span data-stu-id="3b08f-171">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="3b08f-172">IIS 管理器时执行这些操作，会应用的修改*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="3b08f-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="3b08f-173">一个`<system.webServer><security><authentication>`使用更新的设置的添加节点`anonymousAuthentication`和`windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="3b08f-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="3b08f-174">`<system.webServer>`部分添加到*web.config*文件由 IIS 管理器是在应用外`<location>`发布应用时由.NET Core SDK 添加的部分。</span><span class="sxs-lookup"><span data-stu-id="3b08f-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="3b08f-175">因为部分添加之外`<location>`节点中，设置由任何继承[子应用](xref:host-and-deploy/iis/index#sub-applications)到当前应用。</span><span class="sxs-lookup"><span data-stu-id="3b08f-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="3b08f-176">若要阻止继承，将在添加`<security>`内的部分`<location><system.webServer>`SDK 提供的部分。</span><span class="sxs-lookup"><span data-stu-id="3b08f-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="3b08f-177">当使用 IIS 管理器添加 IIS 配置时，它只影响应用程序的*web.config*在服务器上的文件。</span><span class="sxs-lookup"><span data-stu-id="3b08f-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="3b08f-178">应用程序的后续部署可能会覆盖服务器上的设置，如果服务器的副本*web.config*替换为项目的*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="3b08f-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="3b08f-179">使用**任一**以下方法来管理的设置之一：</span><span class="sxs-lookup"><span data-stu-id="3b08f-179">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="3b08f-180">使用 IIS 管理器中的设置重置*web.config*文件后部署上覆盖该文件。</span><span class="sxs-lookup"><span data-stu-id="3b08f-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="3b08f-181">添加*web.config 文件*向本地使用的设置应用程序。</span><span class="sxs-lookup"><span data-stu-id="3b08f-181">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="3b08f-182">有关详细信息，请参阅[开发端配置](#development-side-configuration-with-a-local-webconfig-file)部分。</span><span class="sxs-lookup"><span data-stu-id="3b08f-182">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="3b08f-183">发布并将项目部署到 IIS 的站点文件夹</span><span class="sxs-lookup"><span data-stu-id="3b08f-183">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="3b08f-184">使用 Visual Studio 或.NET Core CLI，发布，并将应用部署到目标文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b08f-184">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="3b08f-185">有关使用 IIS 承载的详细信息，发布和部署，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="3b08f-185">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="3b08f-186">dotnet 发布</span><span class="sxs-lookup"><span data-stu-id="3b08f-186">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="3b08f-187">启动应用程序以验证 Windows 身份验证正常工作。</span><span class="sxs-lookup"><span data-stu-id="3b08f-187">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="3b08f-188">启用 Windows 身份验证使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="3b08f-188">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="3b08f-189">虽然 Kestrel 不支持 Windows 身份验证，您可以使用[HTTP.sys](xref:fundamentals/servers/httpsys)以在 Windows 上支持自承载的方案。</span><span class="sxs-lookup"><span data-stu-id="3b08f-189">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="3b08f-190">下面的示例配置以使用 Windows 身份验证使用 HTTP.sys 的应用程序的 web 主机：</span><span class="sxs-lookup"><span data-stu-id="3b08f-190">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="3b08f-191">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="3b08f-191">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="3b08f-192">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="3b08f-192">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="3b08f-193">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3b08f-193">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="3b08f-194">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="3b08f-194">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3b08f-195">HTTP.sys 不支持 Nano Server 版本 1709年或更高版本上。</span><span class="sxs-lookup"><span data-stu-id="3b08f-195">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="3b08f-196">若要使用 Windows 身份验证和 HTTP.sys 与 Nano Server，使用[Server Core (microsoft/windowsservercore) 容器](https://hub.docker.com/r/microsoft/windowsservercore/)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-196">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="3b08f-197">在 Server Core 上的详细信息，请参阅[什么是 Windows Server 中的服务器核心安装选项？](/windows-server/administration/server-core/what-is-server-core)。</span><span class="sxs-lookup"><span data-stu-id="3b08f-197">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="3b08f-198">使用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="3b08f-198">Work with Windows Authentication</span></span>

<span data-ttu-id="3b08f-199">匿名访问的配置状态确定的方式`[Authorize]`和`[AllowAnonymous]`应用中使用属性。</span><span class="sxs-lookup"><span data-stu-id="3b08f-199">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="3b08f-200">以下两个部分介绍如何处理的匿名访问权限的禁止和允许配置状态。</span><span class="sxs-lookup"><span data-stu-id="3b08f-200">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="3b08f-201">不允许匿名访问</span><span class="sxs-lookup"><span data-stu-id="3b08f-201">Disallow anonymous access</span></span>

<span data-ttu-id="3b08f-202">启用 Windows 身份验证，并且禁用了匿名访问，则`[Authorize]`和`[AllowAnonymous]`特性不起作用。</span><span class="sxs-lookup"><span data-stu-id="3b08f-202">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="3b08f-203">如果 IIS 站点 （或 HTTP.sys） 配置为不允许匿名访问，请求将永远不会到达您的应用程序。</span><span class="sxs-lookup"><span data-stu-id="3b08f-203">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="3b08f-204">出于此原因，`[AllowAnonymous]`属性不适用。</span><span class="sxs-lookup"><span data-stu-id="3b08f-204">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="3b08f-205">允许匿名访问</span><span class="sxs-lookup"><span data-stu-id="3b08f-205">Allow anonymous access</span></span>

<span data-ttu-id="3b08f-206">启用 Windows 身份验证和匿名访问，使用`[Authorize]`和`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="3b08f-206">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="3b08f-207">`[Authorize]`属性允许你保护部分，该应用程序的真正需要 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="3b08f-207">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="3b08f-208">`[AllowAnonymous]`特性重写`[Authorize]`特性允许匿名访问的应用中的使用情况。</span><span class="sxs-lookup"><span data-stu-id="3b08f-208">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="3b08f-209">请参阅[简单授权](xref:security/authorization/simple)属性使用情况详细信息。</span><span class="sxs-lookup"><span data-stu-id="3b08f-209">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="3b08f-210">在 ASP.NET Core 2.x，请`[Authorize]`属性需要进行额外配置中的*Startup.cs*以进行 Windows 身份验证质询匿名请求。</span><span class="sxs-lookup"><span data-stu-id="3b08f-210">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="3b08f-211">建议的配置略有根据正在使用的 web 服务器而异。</span><span class="sxs-lookup"><span data-stu-id="3b08f-211">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="3b08f-212">默认情况下，缺少授权可访问的页面的用户会显示 HTTP 403 响应为空。</span><span class="sxs-lookup"><span data-stu-id="3b08f-212">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="3b08f-213">[StatusCodePages 中间件](xref:fundamentals/error-handling#configure-status-code-pages)可以配置为向用户提供更好的"拒绝访问"体验。</span><span class="sxs-lookup"><span data-stu-id="3b08f-213">The [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="3b08f-214">IIS</span><span class="sxs-lookup"><span data-stu-id="3b08f-214">IIS</span></span>

<span data-ttu-id="3b08f-215">如果使用 IIS，添加以下代码到`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="3b08f-215">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="3b08f-216">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="3b08f-216">HTTP.sys</span></span>

<span data-ttu-id="3b08f-217">如果使用 HTTP.sys，添加以下代码到`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="3b08f-217">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="3b08f-218">Impersonation</span><span class="sxs-lookup"><span data-stu-id="3b08f-218">Impersonation</span></span>

<span data-ttu-id="3b08f-219">ASP.NET Core 未实现模拟。</span><span class="sxs-lookup"><span data-stu-id="3b08f-219">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="3b08f-220">应用程序运行具有的所有请求，使用应用程序池或进程标识的应用的标识。</span><span class="sxs-lookup"><span data-stu-id="3b08f-220">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="3b08f-221">如果您需要显式执行代表用户操作，使用[WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)中[终端的内联中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)中`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="3b08f-221">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="3b08f-222">在此上下文中运行的单个操作，然后关闭上下文。</span><span class="sxs-lookup"><span data-stu-id="3b08f-222">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` <span data-ttu-id="3b08f-223">不支持异步操作，不应该用于复杂的方案。</span><span class="sxs-lookup"><span data-stu-id="3b08f-223">doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="3b08f-224">例如，包装整个请求或中间件链不受支持或推荐的。</span><span class="sxs-lookup"><span data-stu-id="3b08f-224">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

### <a name="claims-transformations"></a><span data-ttu-id="3b08f-225">声明转换</span><span class="sxs-lookup"><span data-stu-id="3b08f-225">Claims transformations</span></span>

<span data-ttu-id="3b08f-226">承载与 IIS 进程内模式时<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>不在内部调用以初始化用户。</span><span class="sxs-lookup"><span data-stu-id="3b08f-226">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="3b08f-227">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="3b08f-227">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="3b08f-228">有关详细信息和托管进程中时将激活声明转换的代码示例，请参阅<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="3b08f-228">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>
