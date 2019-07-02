---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: scottaddie
description: 了解如何为 IIS 和 HTTP.sys 在 ASP.NET Core 中配置 Windows 身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/01/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 30f1f554a29412ed6b84115d457d2da1aba91c17
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500505"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="5c734-103">在 ASP.NET Core 中配置 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="5c734-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="5c734-104">通过[Scott Addie](https://twitter.com/Scott_Addie)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c734-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c734-105">Windows 身份验证 （也称为协商、 Kerberos 或 NTLM 的身份验证） 可以配置为使用托管的 ASP.NET Core 应用[IIS](xref:host-and-deploy/iis/index)， [Kestrel](xref:fundamentals/servers/kestrel)，或[HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="5c734-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c734-106">Windows 身份验证 （也称为协商、 Kerberos 或 NTLM 的身份验证） 可以配置为使用托管的 ASP.NET Core 应用[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)。</span><span class="sxs-lookup"><span data-stu-id="5c734-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="5c734-107">Windows 身份验证依赖于操作系统的 ASP.NET Core 应用的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="5c734-108">使用 Active Directory 域标识或 Windows 帐户标识的用户在公司网络上运行你的服务器时，可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="5c734-109">Windows 身份验证是最适合于用户、 客户端应用程序和 web 服务器属于同一个 Windows 域的 intranet 环境。</span><span class="sxs-lookup"><span data-stu-id="5c734-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="5c734-110">使用 HTTP/2 不支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="5c734-111">可以在 HTTP/2 响应发送身份验证质询，但客户端必须降级到 HTTP/1.1 之前进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="5c734-112">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="5c734-112">IIS/IIS Express</span></span>

<span data-ttu-id="5c734-113">添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName>命名空间) 中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5c734-113">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="5c734-114">启动设置 （调试器）</span><span class="sxs-lookup"><span data-stu-id="5c734-114">Launch settings (debugger)</span></span>

<span data-ttu-id="5c734-115">用于配置启动设置，只会影响*Properties/launchSettings.json* IIS express 文件，并不会配置 IIS 的 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-115">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="5c734-116">服务器配置中所述[IIS](#iis)部分。</span><span class="sxs-lookup"><span data-stu-id="5c734-116">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="5c734-117">**Web 应用程序**可通过 Visual Studio 或.NET Core CLI 的模板可以配置为支持 Windows 身份验证，这会更新*Properties/launchSettings.json*文件是自动的。</span><span class="sxs-lookup"><span data-stu-id="5c734-117">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c734-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c734-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c734-119">**新项目**</span><span class="sxs-lookup"><span data-stu-id="5c734-119">**New project**</span></span>

1. <span data-ttu-id="5c734-120">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="5c734-120">Create a new project.</span></span>
1. <span data-ttu-id="5c734-121">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="5c734-121">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5c734-122">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="5c734-122">Select **Next**.</span></span>
1. <span data-ttu-id="5c734-123">中提供名称**项目名称**字段。</span><span class="sxs-lookup"><span data-stu-id="5c734-123">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="5c734-124">确认**位置**条目是否正确，或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="5c734-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5c734-125">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="5c734-125">Select **Create**.</span></span>
1. <span data-ttu-id="5c734-126">选择**更改**下**身份验证**。</span><span class="sxs-lookup"><span data-stu-id="5c734-126">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="5c734-127">在中**更改身份验证**窗口中，选择**Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="5c734-127">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="5c734-128">选择 **确定**。</span><span class="sxs-lookup"><span data-stu-id="5c734-128">Select **OK**.</span></span>
1. <span data-ttu-id="5c734-129">选择“Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="5c734-129">Select **Web Application**.</span></span>
1. <span data-ttu-id="5c734-130">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="5c734-130">Select **Create**.</span></span>

<span data-ttu-id="5c734-131">运行应用。</span><span class="sxs-lookup"><span data-stu-id="5c734-131">Run the app.</span></span> <span data-ttu-id="5c734-132">用户名将显示在呈现的应用程序用户界面。</span><span class="sxs-lookup"><span data-stu-id="5c734-132">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="5c734-133">**现有项目**</span><span class="sxs-lookup"><span data-stu-id="5c734-133">**Existing project**</span></span>

<span data-ttu-id="5c734-134">项目的属性启用 Windows 身份验证并禁用匿名身份验证：</span><span class="sxs-lookup"><span data-stu-id="5c734-134">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="5c734-135">右键单击“解决方案资源管理器”中的项目，再选择“属性”   。</span><span class="sxs-lookup"><span data-stu-id="5c734-135">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="5c734-136">选择“调试”选项卡  。</span><span class="sxs-lookup"><span data-stu-id="5c734-136">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="5c734-137">清除的复选框**启用匿名身份验证**。</span><span class="sxs-lookup"><span data-stu-id="5c734-137">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="5c734-138">选中的复选框**启用 Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="5c734-138">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="5c734-139">保存并关闭属性页。</span><span class="sxs-lookup"><span data-stu-id="5c734-139">Save and close the property page.</span></span>

<span data-ttu-id="5c734-140">或者，可以在配置属性`iisSettings`的节点*launchSettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="5c734-140">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="5c734-141">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c734-141">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="5c734-142">**新项目**</span><span class="sxs-lookup"><span data-stu-id="5c734-142">**New project**</span></span>

<span data-ttu-id="5c734-143">执行[dotnet 新](/dotnet/core/tools/dotnet-new)命令`webapp`自变量 （ASP.NET Core Web 应用） 和`--auth Windows`切换：</span><span class="sxs-lookup"><span data-stu-id="5c734-143">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="5c734-144">**现有项目**</span><span class="sxs-lookup"><span data-stu-id="5c734-144">**Existing project**</span></span>

<span data-ttu-id="5c734-145">更新`iisSettings`的节点*launchSettings.json*文件：</span><span class="sxs-lookup"><span data-stu-id="5c734-145">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="5c734-146">在修改现有项目，确认项目文件包含的包引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)**或** [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="5c734-146">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="5c734-147">IIS</span><span class="sxs-lookup"><span data-stu-id="5c734-147">IIS</span></span>

<span data-ttu-id="5c734-148">IIS 使用[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)到承载 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="5c734-148">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="5c734-149">Windows 身份验证配置为通过 IIS *web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="5c734-149">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="5c734-150">以下部分介绍如何：</span><span class="sxs-lookup"><span data-stu-id="5c734-150">The following sections show how to:</span></span>

* <span data-ttu-id="5c734-151">提供本地*web.config*在部署应用时在服务器激活 Windows 身份验证的文件。</span><span class="sxs-lookup"><span data-stu-id="5c734-151">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="5c734-152">使用 IIS 管理器配置*web.config*已部署到服务器的 ASP.NET Core 应用的文件。</span><span class="sxs-lookup"><span data-stu-id="5c734-152">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="5c734-153">如果尚未执行此操作，使 IIS 能够承载 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="5c734-153">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="5c734-154">有关详细信息，请参阅 <xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="5c734-154">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="5c734-155">启用 Windows 身份验证的 IIS 角色服务。</span><span class="sxs-lookup"><span data-stu-id="5c734-155">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="5c734-156">有关详细信息，请参阅[IIS 角色服务 （请参阅步骤 2） 中启用 Windows 身份验证](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="5c734-156">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="5c734-157">[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)默认配置为自动进行身份验证请求。</span><span class="sxs-lookup"><span data-stu-id="5c734-157">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="5c734-158">有关详细信息，请参阅[与 IIS 的 Windows 上托管 ASP.NET Core:IIS 选项 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)。</span><span class="sxs-lookup"><span data-stu-id="5c734-158">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="5c734-159">默认情况下，ASP.NET Core 模块配置为转发到应用的 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="5c734-159">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="5c734-160">有关详细信息，请参阅[ASP.NET Core 模块配置参考：AspNetCore 元素的特性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="5c734-160">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="5c734-161">使用**任一**以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="5c734-161">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="5c734-162">**发布和部署该项目之前,** 添加以下*web.config*到项目根目录的文件：</span><span class="sxs-lookup"><span data-stu-id="5c734-162">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="5c734-163">由.NET Core SDK 发布项目时 (而无需`<IsTransformWebConfigDisabled>`属性设置为`true`项目文件中)，已发布*web.config*文件包括`<location><system.webServer><security><authentication>`部分。</span><span class="sxs-lookup"><span data-stu-id="5c734-163">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="5c734-164">有关详细信息`<IsTransformWebConfigDisabled>`属性，请参阅<xref:host-and-deploy/iis/index#webconfig-file>。</span><span class="sxs-lookup"><span data-stu-id="5c734-164">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="5c734-165">**发布和部署该项目之后,** 执行服务器端配置使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="5c734-165">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="5c734-166">在 IIS 管理器中，选择下的 IIS 站点**站点**的节点**连接**侧栏。</span><span class="sxs-lookup"><span data-stu-id="5c734-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="5c734-167">双击**身份验证**中**IIS**区域。</span><span class="sxs-lookup"><span data-stu-id="5c734-167">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="5c734-168">选择**匿名身份验证**。</span><span class="sxs-lookup"><span data-stu-id="5c734-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="5c734-169">选择**禁用**中**操作**侧栏。</span><span class="sxs-lookup"><span data-stu-id="5c734-169">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="5c734-170">选择**Windows 身份验证**。</span><span class="sxs-lookup"><span data-stu-id="5c734-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="5c734-171">选择**启用**中**操作**侧栏。</span><span class="sxs-lookup"><span data-stu-id="5c734-171">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="5c734-172">IIS 管理器时执行这些操作，会应用的修改*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="5c734-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="5c734-173">一个`<system.webServer><security><authentication>`使用更新的设置的添加节点`anonymousAuthentication`和`windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="5c734-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="5c734-174">`<system.webServer>`部分添加到*web.config*文件由 IIS 管理器是在应用外`<location>`发布应用时由.NET Core SDK 添加的部分。</span><span class="sxs-lookup"><span data-stu-id="5c734-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="5c734-175">因为部分添加之外`<location>`节点中，设置由任何继承[子应用](xref:host-and-deploy/iis/index#sub-applications)到当前应用。</span><span class="sxs-lookup"><span data-stu-id="5c734-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="5c734-176">若要阻止继承，将在添加`<security>`内的部分`<location><system.webServer>`.NET Core SDK 提供的部分。</span><span class="sxs-lookup"><span data-stu-id="5c734-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="5c734-177">当使用 IIS 管理器添加 IIS 配置时，它只影响应用程序的*web.config*在服务器上的文件。</span><span class="sxs-lookup"><span data-stu-id="5c734-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="5c734-178">应用程序的后续部署可能会覆盖服务器上的设置，如果服务器的副本*web.config*替换为项目的*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="5c734-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="5c734-179">使用**任一**以下方法来管理的设置之一：</span><span class="sxs-lookup"><span data-stu-id="5c734-179">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="5c734-180">使用 IIS 管理器中的设置重置*web.config*文件后部署上覆盖该文件。</span><span class="sxs-lookup"><span data-stu-id="5c734-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="5c734-181">添加*web.config 文件*向本地使用的设置应用程序。</span><span class="sxs-lookup"><span data-stu-id="5c734-181">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="5c734-182">Kestrel</span><span class="sxs-lookup"><span data-stu-id="5c734-182">Kestrel</span></span>

 <span data-ttu-id="5c734-183">[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 包可用于[Kestrel](xref:fundamentals/servers/kestrel)以支持 Windows、 Linux 和 macOS 上使用 Negotiate、 Kerberos 和 NTLM 的 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-183">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate, Kerberos, and NTLM on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="5c734-184">凭据可以在连接上的请求之间得以保持。</span><span class="sxs-lookup"><span data-stu-id="5c734-184">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="5c734-185">*协商身份验证不得使用代理中使用，除非代理维护与 Kestrel 1 对 1 连接关联 （持续性连接）。*</span><span class="sxs-lookup"><span data-stu-id="5c734-185">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="5c734-186">Negotiate 处理程序会检测基础服务器以本机方式支持 Windows 身份验证，并启用它。</span><span class="sxs-lookup"><span data-stu-id="5c734-186">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="5c734-187">如果服务器支持 Windows 身份验证，但它处于禁用状态，要求你启用服务器实现引发错误。</span><span class="sxs-lookup"><span data-stu-id="5c734-187">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="5c734-188">当服务器中启用 Windows 身份验证时，Negotiate 处理程序以透明方式将转发给它。</span><span class="sxs-lookup"><span data-stu-id="5c734-188">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

 <span data-ttu-id="5c734-189">添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(`Microsoft.AspNetCore.Authentication.Negotiate`命名空间) 和`AddNegotitate`(`Microsoft.AspNetCore.Authentication.Negotiate`命名空间) 中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5c734-189">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) and `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) in `Startup.ConfigureServices`:</span></span>

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="5c734-190">通过调用添加身份验证中间件<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>在`Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5c734-190">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

<span data-ttu-id="5c734-191">中间件的详细信息，请参阅<xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="5c734-191">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="5c734-192">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="5c734-192">Anonymous requests are allowed.</span></span> <span data-ttu-id="5c734-193">使用[ASP.NET Core 授权](xref:security/authorization/introduction)质询的身份验证的匿名请求。</span><span class="sxs-lookup"><span data-stu-id="5c734-193">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="5c734-194">Windows 环境配置</span><span class="sxs-lookup"><span data-stu-id="5c734-194">Windows environment configuration</span></span>

<span data-ttu-id="5c734-195">[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)组件执行用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-195">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="5c734-196">服务主体名称 (Spn) 必须添加到运行服务，而不是计算机帐户的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="5c734-196">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="5c734-197">执行`setspn -S HTTP/mysrevername.mydomain.com myuser`管理命令行界面中。</span><span class="sxs-lookup"><span data-stu-id="5c734-197">Execute `setspn -S HTTP/mysrevername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="5c734-198">Linux 和 macOS 的环境配置</span><span class="sxs-lookup"><span data-stu-id="5c734-198">Linux and macOS environment configuration</span></span>

<span data-ttu-id="5c734-199">中提供了有关在 Linux 或 macOS 计算机加入 Windows 域的说明[Azure 数据 Studio 连接到 SQL Server 使用 Windows 身份验证的 Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)一文。</span><span class="sxs-lookup"><span data-stu-id="5c734-199">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="5c734-200">说明在域中创建的 Linux 计算机的计算机帐户。</span><span class="sxs-lookup"><span data-stu-id="5c734-200">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="5c734-201">必须将 Spn 添加到该计算机帐户。</span><span class="sxs-lookup"><span data-stu-id="5c734-201">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="5c734-202">时中的指南[Azure 数据 Studio 连接到 SQL Server 使用 Windows 身份验证的 Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)文章中，替换`python-software-properties`与`python3-software-properties`必要。</span><span class="sxs-lookup"><span data-stu-id="5c734-202">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="5c734-203">一旦在 Linux 或 macOS 计算机加入到域，需要提供额外的步骤[keytab 文件](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)的 spn:</span><span class="sxs-lookup"><span data-stu-id="5c734-203">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="5c734-204">在域控制器上，将添加到的计算机帐户的新的 web 服务的 Spn:</span><span class="sxs-lookup"><span data-stu-id="5c734-204">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="5c734-205">使用[ktpass](/windows-server/administration/windows-commands/ktpass)生成 keytab 文件：</span><span class="sxs-lookup"><span data-stu-id="5c734-205">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="5c734-206">某些字段必须以指定大写所示。</span><span class="sxs-lookup"><span data-stu-id="5c734-206">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="5c734-207">将 keytab 文件复制到 Linux 或 macOS 计算机。</span><span class="sxs-lookup"><span data-stu-id="5c734-207">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="5c734-208">选择通过环境变量的 keytab 文件： `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="5c734-208">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="5c734-209">调用`klist`以显示当前可供使用的 Spn。</span><span class="sxs-lookup"><span data-stu-id="5c734-209">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="5c734-210">Keytab 文件包含域访问凭据，必须相应地保护。</span><span class="sxs-lookup"><span data-stu-id="5c734-210">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="5c734-211">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="5c734-211">HTTP.sys</span></span>

<span data-ttu-id="5c734-212">[HTTP.sys](xref:fundamentals/servers/httpsys)支持内核模式 Windows 身份验证使用 Negotiate、 NTLM 或基本身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-212">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="5c734-213">添加身份验证服务通过调用<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>(<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>命名空间) 中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5c734-213">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="5c734-214">配置应用程序的 web 主机，以使用 Windows 身份验证使用 HTTP.sys (*Program.cs*)。</span><span class="sxs-lookup"><span data-stu-id="5c734-214">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="5c734-215"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 在<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>命名空间。</span><span class="sxs-lookup"><span data-stu-id="5c734-215"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5c734-216">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-216">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="5c734-217">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-217">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="5c734-218">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="5c734-218">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="5c734-219">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="5c734-219">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="5c734-220">HTTP.sys 不支持 Nano Server 版本 1709年或更高版本上。</span><span class="sxs-lookup"><span data-stu-id="5c734-220">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="5c734-221">若要使用 Windows 身份验证和 HTTP.sys 与 Nano Server，使用[Server Core (microsoft/windowsservercore) 容器](https://hub.docker.com/r/microsoft/windowsservercore/)。</span><span class="sxs-lookup"><span data-stu-id="5c734-221">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="5c734-222">在 Server Core 上的详细信息，请参阅[什么是 Windows Server 中的服务器核心安装选项？](/windows-server/administration/server-core/what-is-server-core)。</span><span class="sxs-lookup"><span data-stu-id="5c734-222">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="5c734-223">为用户授权</span><span class="sxs-lookup"><span data-stu-id="5c734-223">Authorize users</span></span>

<span data-ttu-id="5c734-224">匿名访问的配置状态确定的方式`[Authorize]`和`[AllowAnonymous]`应用中使用属性。</span><span class="sxs-lookup"><span data-stu-id="5c734-224">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="5c734-225">以下两个部分介绍如何处理的匿名访问权限的禁止和允许配置状态。</span><span class="sxs-lookup"><span data-stu-id="5c734-225">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="5c734-226">不允许匿名访问</span><span class="sxs-lookup"><span data-stu-id="5c734-226">Disallow anonymous access</span></span>

<span data-ttu-id="5c734-227">启用 Windows 身份验证，并且禁用了匿名访问，则`[Authorize]`和`[AllowAnonymous]`特性不起作用。</span><span class="sxs-lookup"><span data-stu-id="5c734-227">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="5c734-228">如果 IIS 站点配置为不允许匿名访问，请求将永远不会到达该应用程序。</span><span class="sxs-lookup"><span data-stu-id="5c734-228">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="5c734-229">出于此原因，`[AllowAnonymous]`属性不适用。</span><span class="sxs-lookup"><span data-stu-id="5c734-229">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="5c734-230">允许匿名访问</span><span class="sxs-lookup"><span data-stu-id="5c734-230">Allow anonymous access</span></span>

<span data-ttu-id="5c734-231">启用 Windows 身份验证和匿名访问，使用`[Authorize]`和`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="5c734-231">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="5c734-232">`[Authorize]`特性，您可以保护的应用程序的要求进行身份验证的终结点。</span><span class="sxs-lookup"><span data-stu-id="5c734-232">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="5c734-233">`[AllowAnonymous]`特性重写`[Authorize]`允许匿名访问的应用中的属性。</span><span class="sxs-lookup"><span data-stu-id="5c734-233">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="5c734-234">属性使用情况详细信息，请参阅<xref:security/authorization/simple>。</span><span class="sxs-lookup"><span data-stu-id="5c734-234">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="5c734-235">默认情况下，缺少授权可访问的页面的用户会显示 HTTP 403 响应为空。</span><span class="sxs-lookup"><span data-stu-id="5c734-235">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="5c734-236">[StatusCodePages 中间件](xref:fundamentals/error-handling#usestatuscodepages)可以配置为向用户提供更好的"拒绝访问"体验。</span><span class="sxs-lookup"><span data-stu-id="5c734-236">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="5c734-237">Impersonation</span><span class="sxs-lookup"><span data-stu-id="5c734-237">Impersonation</span></span>

<span data-ttu-id="5c734-238">ASP.NET Core 未实现模拟。</span><span class="sxs-lookup"><span data-stu-id="5c734-238">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="5c734-239">应用程序运行具有的所有请求，使用应用程序池或进程标识的应用的标识。</span><span class="sxs-lookup"><span data-stu-id="5c734-239">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="5c734-240">如果应用程序应执行代表用户操作，使用[WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*)中[终端的内联中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)中`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="5c734-240">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="5c734-241">在此上下文中运行的单个操作，然后关闭上下文。</span><span class="sxs-lookup"><span data-stu-id="5c734-241">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="5c734-242">`RunImpersonated` 不支持异步操作，不应该用于复杂的方案。</span><span class="sxs-lookup"><span data-stu-id="5c734-242">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="5c734-243">例如，包装整个请求或中间件链不受支持或推荐的。</span><span class="sxs-lookup"><span data-stu-id="5c734-243">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c734-244">虽然[Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)程序包可启用 Windows 上的身份验证，在 Windows 上仅支持 Linux 和 macOS 的模拟。</span><span class="sxs-lookup"><span data-stu-id="5c734-244">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="5c734-245">声明转换</span><span class="sxs-lookup"><span data-stu-id="5c734-245">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c734-246">使用 IIS，承载时<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>不在内部调用以初始化用户。</span><span class="sxs-lookup"><span data-stu-id="5c734-246">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5c734-247">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="5c734-247">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5c734-248">有关详细信息和激活声明转换的代码示例，请参阅<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="5c734-248">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c734-249">承载与 IIS 进程内模式时<xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*>不在内部调用以初始化用户。</span><span class="sxs-lookup"><span data-stu-id="5c734-249">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="5c734-250">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="5c734-250">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="5c734-251">有关详细信息和托管进程中时将激活声明转换的代码示例，请参阅<xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="5c734-251">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5c734-252">其他资源</span><span class="sxs-lookup"><span data-stu-id="5c734-252">Additional resources</span></span>

* [<span data-ttu-id="5c734-253">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="5c734-253">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
