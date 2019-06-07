---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: scottaddie
description: 了解如何为 IIS 和 HTTP.sys 在 ASP.NET Core 中配置 Windows 身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/05/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 900bbf5f14b1876ad537b2b77e4ba07d7aa168f2
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750162"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="a40fc-103">在 ASP.NET Core 中配置 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="a40fc-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="a40fc-104">通过[Scott Addie](https://twitter.com/Scott_Addie)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a40fc-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a40fc-105">[Windows 身份验证](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)可以配置为使用托管的 ASP.NET Core 应用[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="a40fc-106">Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="a40fc-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="a40fc-107">使用 Active Directory 域标识或 Windows 帐户标识的用户在公司网络上运行你的服务器时，可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="a40fc-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="a40fc-108">Windows 身份验证是最适合于用户、 客户端应用程序和 web 服务器属于同一个 Windows 域的 intranet 环境。</span><span class="sxs-lookup"><span data-stu-id="a40fc-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="a40fc-109">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="a40fc-109">IIS/IIS Express</span></span>

<span data-ttu-id="a40fc-110">添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName>命名空间) 中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a40fc-110">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="a40fc-111">启动设置 （调试器）</span><span class="sxs-lookup"><span data-stu-id="a40fc-111">Launch settings (debugger)</span></span>

<span data-ttu-id="a40fc-112">用于配置启动设置，只会影响*Properties/launchSettings.json* IIS express 文件，并不会配置 IIS 的 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="a40fc-112">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="a40fc-113">服务器配置中所述[IIS](#iis)部分。</span><span class="sxs-lookup"><span data-stu-id="a40fc-113">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="a40fc-114">**Web 应用程序**可通过 Visual Studio 或.NET Core CLI 的模板可以配置为支持 Windows 身份验证，这会更新*Properties/launchSettings.json*文件是自动的。</span><span class="sxs-lookup"><span data-stu-id="a40fc-114">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a40fc-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a40fc-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a40fc-116">**新项目**</span><span class="sxs-lookup"><span data-stu-id="a40fc-116">**New project**</span></span>

1. <span data-ttu-id="a40fc-117">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="a40fc-117">Create a new project.</span></span>
1. <span data-ttu-id="a40fc-118">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="a40fc-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a40fc-119">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="a40fc-119">Select **Next**.</span></span>
1. <span data-ttu-id="a40fc-120">中提供名称**项目名称**字段。</span><span class="sxs-lookup"><span data-stu-id="a40fc-120">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="a40fc-121">确认**位置**条目是否正确，或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="a40fc-121">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a40fc-122">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="a40fc-122">Select **Create**.</span></span>
1. <span data-ttu-id="a40fc-123">选择**更改**下**身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a40fc-123">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="a40fc-124">在中**更改身份验证**窗口中，选择**Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a40fc-124">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="a40fc-125">选择 **确定**。</span><span class="sxs-lookup"><span data-stu-id="a40fc-125">Select **OK**.</span></span>
1. <span data-ttu-id="a40fc-126">选择“Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="a40fc-126">Select **Web Application**.</span></span>
1. <span data-ttu-id="a40fc-127">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="a40fc-127">Select **Create**.</span></span>

<span data-ttu-id="a40fc-128">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a40fc-128">Run the app.</span></span> <span data-ttu-id="a40fc-129">用户名将显示在呈现的应用程序用户界面。</span><span class="sxs-lookup"><span data-stu-id="a40fc-129">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="a40fc-130">**现有项目**</span><span class="sxs-lookup"><span data-stu-id="a40fc-130">**Existing project**</span></span>

<span data-ttu-id="a40fc-131">项目的属性启用 Windows 身份验证并禁用匿名身份验证：</span><span class="sxs-lookup"><span data-stu-id="a40fc-131">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="a40fc-132">右键单击“解决方案资源管理器”中的项目，再选择“属性”   。</span><span class="sxs-lookup"><span data-stu-id="a40fc-132">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="a40fc-133">选择“调试”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="a40fc-133">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="a40fc-134">清除的复选框**启用匿名身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a40fc-134">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="a40fc-135">选中的复选框**启用 Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a40fc-135">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="a40fc-136">保存并关闭属性页。</span><span class="sxs-lookup"><span data-stu-id="a40fc-136">Save and close the property page.</span></span>

<span data-ttu-id="a40fc-137">或者，可以在配置属性`iisSettings`的节点*launchSettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="a40fc-137">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="a40fc-138">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a40fc-138">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="a40fc-139">**新项目**</span><span class="sxs-lookup"><span data-stu-id="a40fc-139">**New project**</span></span>

<span data-ttu-id="a40fc-140">执行[dotnet 新](/dotnet/core/tools/dotnet-new)命令`webapp`自变量 （ASP.NET Core Web 应用） 和`--auth Windows`切换：</span><span class="sxs-lookup"><span data-stu-id="a40fc-140">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="a40fc-141">**现有项目**</span><span class="sxs-lookup"><span data-stu-id="a40fc-141">**Existing project**</span></span>

<span data-ttu-id="a40fc-142">更新`iisSettings`的节点*launchSettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="a40fc-142">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="a40fc-143">在修改现有项目，确认项目文件包含的包引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)**或** [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a40fc-143">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="a40fc-144">IIS</span><span class="sxs-lookup"><span data-stu-id="a40fc-144">IIS</span></span>

<span data-ttu-id="a40fc-145">IIS 使用[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)到承载 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="a40fc-145">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="a40fc-146">Windows 身份验证配置为通过 IIS *web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="a40fc-146">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="a40fc-147">以下部分介绍如何：</span><span class="sxs-lookup"><span data-stu-id="a40fc-147">The following sections show how to:</span></span>

* <span data-ttu-id="a40fc-148">提供本地*web.config*在部署应用时在服务器激活 Windows 身份验证的文件。</span><span class="sxs-lookup"><span data-stu-id="a40fc-148">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="a40fc-149">使用 IIS 管理器配置*web.config*已部署到服务器的 ASP.NET Core 应用的文件。</span><span class="sxs-lookup"><span data-stu-id="a40fc-149">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="a40fc-150">如果尚未执行此操作，使 IIS 能够承载 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="a40fc-150">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="a40fc-151">有关详细信息，请参阅 <xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="a40fc-151">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="a40fc-152">启用 Windows 身份验证的 IIS 角色服务。</span><span class="sxs-lookup"><span data-stu-id="a40fc-152">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="a40fc-153">有关详细信息，请参阅[IIS 角色服务 （请参阅步骤 2） 中启用 Windows 身份验证](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-153">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="a40fc-154">[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)默认配置为自动进行身份验证请求。</span><span class="sxs-lookup"><span data-stu-id="a40fc-154">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="a40fc-155">有关详细信息，请参阅[与 IIS 的 Windows 上托管 ASP.NET Core:IIS 选项 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-155">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="a40fc-156">默认情况下，ASP.NET Core 模块配置为转发到应用的 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="a40fc-156">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="a40fc-157">有关详细信息，请参阅[ASP.NET Core 模块配置参考：AspNetCore 元素的特性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-157">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="a40fc-158">使用**任一**以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="a40fc-158">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="a40fc-159">**发布和部署该项目之前,** 添加以下*web.config*到项目根目录的文件：</span><span class="sxs-lookup"><span data-stu-id="a40fc-159">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="a40fc-160">由.NET Core SDK 发布项目时 (而无需`<IsTransformWebConfigDisabled>`属性设置为`true`项目文件中)，已发布*web.config*文件包括`<location><system.webServer><security><authentication>`部分。</span><span class="sxs-lookup"><span data-stu-id="a40fc-160">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="a40fc-161">有关详细信息`<IsTransformWebConfigDisabled>`属性，请参阅<xref:host-and-deploy/iis/index#webconfig-file>。</span><span class="sxs-lookup"><span data-stu-id="a40fc-161">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="a40fc-162">**发布和部署该项目之后,** 执行服务器端配置使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="a40fc-162">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="a40fc-163">在 IIS 管理器中，选择下的 IIS 站点**站点**的节点**连接**侧栏。</span><span class="sxs-lookup"><span data-stu-id="a40fc-163">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="a40fc-164">双击**身份验证**中**IIS**区域。</span><span class="sxs-lookup"><span data-stu-id="a40fc-164">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="a40fc-165">选择**匿名身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a40fc-165">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="a40fc-166">选择**禁用**中**操作**侧栏。</span><span class="sxs-lookup"><span data-stu-id="a40fc-166">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="a40fc-167">选择**Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a40fc-167">Select **Windows Authentication**.</span></span> <span data-ttu-id="a40fc-168">选择**启用**中**操作**侧栏。</span><span class="sxs-lookup"><span data-stu-id="a40fc-168">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="a40fc-169">IIS 管理器时执行这些操作，会应用的修改*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="a40fc-169">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="a40fc-170">一个`<system.webServer><security><authentication>`使用更新的设置的添加节点`anonymousAuthentication`和`windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="a40fc-170">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="a40fc-171">`<system.webServer>`部分添加到*web.config*文件由 IIS 管理器是在应用外`<location>`发布应用时由.NET Core SDK 添加的部分。</span><span class="sxs-lookup"><span data-stu-id="a40fc-171">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="a40fc-172">因为部分添加之外`<location>`节点中，设置由任何继承[子应用](xref:host-and-deploy/iis/index#sub-applications)到当前应用。</span><span class="sxs-lookup"><span data-stu-id="a40fc-172">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="a40fc-173">若要阻止继承，将在添加`<security>`内的部分`<location><system.webServer>`.NET Core SDK 提供的部分。</span><span class="sxs-lookup"><span data-stu-id="a40fc-173">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="a40fc-174">当使用 IIS 管理器添加 IIS 配置时，它只影响应用程序的*web.config*在服务器上的文件。</span><span class="sxs-lookup"><span data-stu-id="a40fc-174">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="a40fc-175">应用程序的后续部署可能会覆盖服务器上的设置，如果服务器的副本*web.config*替换为项目的*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="a40fc-175">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="a40fc-176">使用**任一**以下方法来管理的设置之一：</span><span class="sxs-lookup"><span data-stu-id="a40fc-176">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="a40fc-177">使用 IIS 管理器中的设置重置*web.config*文件后部署上覆盖该文件。</span><span class="sxs-lookup"><span data-stu-id="a40fc-177">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="a40fc-178">添加*web.config 文件*向本地使用的设置应用程序。</span><span class="sxs-lookup"><span data-stu-id="a40fc-178">Add a *web.config file* to the app locally with the settings.</span></span>

## <a name="httpsys"></a><span data-ttu-id="a40fc-179">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a40fc-179">HTTP.sys</span></span>

<span data-ttu-id="a40fc-180">在自承载方案中， [Kestrel](xref:fundamentals/servers/kestrel)不支持 Windows 身份验证，但你可以使用[HTTP.sys](xref:fundamentals/servers/httpsys)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-180">In self-hosted scenarios, [Kestrel](xref:fundamentals/servers/kestrel) doesn't support Windows Authentication, but you can use [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="a40fc-181">添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>命名空间) 中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a40fc-181">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="a40fc-182">配置应用程序的 web 主机，以使用 Windows 身份验证使用 HTTP.sys (*Program.cs*)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-182">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="a40fc-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 在<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>命名空间。</span><span class="sxs-lookup"><span data-stu-id="a40fc-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a40fc-184">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="a40fc-184">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a40fc-185">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="a40fc-185">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a40fc-186">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="a40fc-186">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a40fc-187">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="a40fc-187">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a40fc-188">HTTP.sys 不支持 Nano Server 版本 1709年或更高版本上。</span><span class="sxs-lookup"><span data-stu-id="a40fc-188">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="a40fc-189">若要使用 Windows 身份验证和 HTTP.sys 与 Nano Server，使用[Server Core (microsoft/windowsservercore) 容器](https://hub.docker.com/r/microsoft/windowsservercore/)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-189">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="a40fc-190">在 Server Core 上的详细信息，请参阅[什么是 Windows Server 中的服务器核心安装选项？](/windows-server/administration/server-core/what-is-server-core)。</span><span class="sxs-lookup"><span data-stu-id="a40fc-190">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="a40fc-191">为用户授权</span><span class="sxs-lookup"><span data-stu-id="a40fc-191">Authorize users</span></span>

<span data-ttu-id="a40fc-192">匿名访问的配置状态确定的方式`[Authorize]`和`[AllowAnonymous]`应用中使用属性。</span><span class="sxs-lookup"><span data-stu-id="a40fc-192">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="a40fc-193">以下两个部分介绍如何处理的匿名访问权限的禁止和允许配置状态。</span><span class="sxs-lookup"><span data-stu-id="a40fc-193">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="a40fc-194">不允许匿名访问</span><span class="sxs-lookup"><span data-stu-id="a40fc-194">Disallow anonymous access</span></span>

<span data-ttu-id="a40fc-195">启用 Windows 身份验证，并且禁用了匿名访问，则`[Authorize]`和`[AllowAnonymous]`特性不起作用。</span><span class="sxs-lookup"><span data-stu-id="a40fc-195">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="a40fc-196">如果 IIS 站点配置为不允许匿名访问，请求将永远不会到达该应用程序。</span><span class="sxs-lookup"><span data-stu-id="a40fc-196">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="a40fc-197">出于此原因，`[AllowAnonymous]`属性不适用。</span><span class="sxs-lookup"><span data-stu-id="a40fc-197">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="a40fc-198">允许匿名访问</span><span class="sxs-lookup"><span data-stu-id="a40fc-198">Allow anonymous access</span></span>

<span data-ttu-id="a40fc-199">启用 Windows 身份验证和匿名访问，使用`[Authorize]`和`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="a40fc-199">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="a40fc-200">`[Authorize]`特性，您可以保护的应用程序的要求进行身份验证的终结点。</span><span class="sxs-lookup"><span data-stu-id="a40fc-200">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="a40fc-201">`[AllowAnonymous]`特性重写`[Authorize]`允许匿名访问的应用中的属性。</span><span class="sxs-lookup"><span data-stu-id="a40fc-201">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="a40fc-202">属性使用情况详细信息，请参阅<xref:security/authorization/simple>。</span><span class="sxs-lookup"><span data-stu-id="a40fc-202">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="a40fc-203">默认情况下，缺少授权可访问的页面的用户会显示 HTTP 403 响应为空。</span><span class="sxs-lookup"><span data-stu-id="a40fc-203">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="a40fc-204">[StatusCodePages 中间件](xref:fundamentals/error-handling#usestatuscodepages)可以配置为向用户提供更好的"拒绝访问"体验。</span><span class="sxs-lookup"><span data-stu-id="a40fc-204">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="a40fc-205">Impersonation</span><span class="sxs-lookup"><span data-stu-id="a40fc-205">Impersonation</span></span>

<span data-ttu-id="a40fc-206">ASP.NET Core 未实现模拟。</span><span class="sxs-lookup"><span data-stu-id="a40fc-206">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="a40fc-207">应用程序运行具有的所有请求，使用应用程序池或进程标识的应用的标识。</span><span class="sxs-lookup"><span data-stu-id="a40fc-207">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="a40fc-208">如果应用程序应执行代表用户操作，使用[WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)中[终端的内联中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)中`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="a40fc-208">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="a40fc-209">在此上下文中运行的单个操作，然后关闭上下文。</span><span class="sxs-lookup"><span data-stu-id="a40fc-209">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="a40fc-210">`RunImpersonated` 不支持异步操作，不应该用于复杂的方案。</span><span class="sxs-lookup"><span data-stu-id="a40fc-210">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="a40fc-211">例如，包装整个请求或中间件链不受支持或推荐的。</span><span class="sxs-lookup"><span data-stu-id="a40fc-211">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

## <a name="claims-transformations"></a><span data-ttu-id="a40fc-212">声明转换</span><span class="sxs-lookup"><span data-stu-id="a40fc-212">Claims transformations</span></span>

<span data-ttu-id="a40fc-213">承载与 IIS 进程内模式时<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>不在内部调用以初始化用户。</span><span class="sxs-lookup"><span data-stu-id="a40fc-213">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="a40fc-214">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="a40fc-214">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="a40fc-215">有关详细信息和托管进程中时将激活声明转换的代码示例，请参阅<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="a40fc-215">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a40fc-216">其他资源</span><span class="sxs-lookup"><span data-stu-id="a40fc-216">Additional resources</span></span>

* [<span data-ttu-id="a40fc-217">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="a40fc-217">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
