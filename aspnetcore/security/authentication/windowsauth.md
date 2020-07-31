---
title: 在 ASP.NET Core 中配置 Windows 身份验证
author: scottaddie
description: 了解如何在 ASP.NET Core for IIS 和 HTTP.sys 中配置 Windows 身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330674"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="f4ea8-103">在 ASP.NET Core 中配置 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="f4ea8-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="f4ea8-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="f4ea8-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4ea8-105">Windows 身份验证（也称为 "协商"、"Kerberos" 或 "NTLM 身份验证"）可为用[IIS](xref:host-and-deploy/iis/index)、 [Kestrel](xref:fundamentals/servers/kestrel)或[HTTP.sys](xref:fundamentals/servers/httpsys)托管的 ASP.NET Core 应用程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f4ea8-106">Windows 身份验证（也称为 "协商"、"Kerberos" 或 "NTLM 身份验证"）可为通过[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)承载 ASP.NET Core 应用进行配置。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="f4ea8-107">Windows 身份验证依赖于操作系统对 ASP.NET Core 应用程序的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="f4ea8-108">当你的服务器使用 Active Directory 域标识或 Windows 帐户来标识用户时，你可以使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="f4ea8-109">Windows 身份验证最适合用于用户、客户端应用和 web 服务器属于同一 Windows 域的 intranet 环境。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="f4ea8-110">HTTP/2 不支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="f4ea8-111">身份验证质询可通过 HTTP/2 响应发送，但客户端必须在进行身份验证之前降级到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="f4ea8-112">代理和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="f4ea8-112">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="f4ea8-113">Windows 身份验证是主要在 intranet 中使用的有状态方案，在此方案中，代理或负载均衡器通常不处理客户端和服务器之间的流量。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-113">Windows Authentication is a stateful scenario primarily used in an intranet, where a proxy or load balancer doesn't usually handle traffic between clients and servers.</span></span> <span data-ttu-id="f4ea8-114">如果使用代理或负载平衡器，Windows 身份验证仅适用于代理或负载平衡器：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-114">If a proxy or load balancer is used, Windows Authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="f4ea8-115">处理身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-115">Handles the authentication.</span></span>
* <span data-ttu-id="f4ea8-116">将用户身份验证信息传递给应用程序（例如，在请求标头中），该信息对身份验证信息起作用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-116">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="f4ea8-117">在使用代理和负载平衡器的环境中，Windows 身份验证的一种替代方法是使用 OpenID Connect （OIDC） Active Directory 联合服务（ADFS）。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-117">An alternative to Windows Authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="f4ea8-118">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="f4ea8-118">IIS/IIS Express</span></span>

<span data-ttu-id="f4ea8-119">通过 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> 在中调用（命名空间）来添加身份验证服务 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-119">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="f4ea8-120">启动设置（调试器）</span><span class="sxs-lookup"><span data-stu-id="f4ea8-120">Launch settings (debugger)</span></span>

<span data-ttu-id="f4ea8-121">启动设置的配置仅影响 IIS Express 文件*上的属性/launchSettings.js* ，而不会将 IIS 配置为 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-121">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="f4ea8-122">[IIS](#iis)部分介绍了服务器配置。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-122">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="f4ea8-123">可通过 Visual Studio 或 .NET Core CLI 配置的**Web 应用程序**模板可配置为支持 Windows 身份验证，这将自动更新文件的*属性/launchSettings.js* 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-123">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f4ea8-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f4ea8-124">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f4ea8-125">**新项目**</span><span class="sxs-lookup"><span data-stu-id="f4ea8-125">**New project**</span></span>

1. <span data-ttu-id="f4ea8-126">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-126">Create a new project.</span></span>
1. <span data-ttu-id="f4ea8-127">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f4ea8-128">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-128">Select **Next**.</span></span>
1. <span data-ttu-id="f4ea8-129">在 "**项目名称**" 字段中提供名称。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-129">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="f4ea8-130">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-130">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f4ea8-131">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-131">Select **Create**.</span></span>
1. <span data-ttu-id="f4ea8-132">选择 "**身份验证**" 下的 "**更改**"。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-132">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="f4ea8-133">在 "**更改身份验证**" 窗口中，选择 " **Windows 身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-133">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="f4ea8-134">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-134">Select **OK**.</span></span>
1. <span data-ttu-id="f4ea8-135">选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-135">Select **Web Application**.</span></span>
1. <span data-ttu-id="f4ea8-136">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-136">Select **Create**.</span></span>

<span data-ttu-id="f4ea8-137">运行应用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-137">Run the app.</span></span> <span data-ttu-id="f4ea8-138">用户名将显示在呈现的应用的用户界面中。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-138">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="f4ea8-139">**现有项目**</span><span class="sxs-lookup"><span data-stu-id="f4ea8-139">**Existing project**</span></span>

<span data-ttu-id="f4ea8-140">项目的属性启用 Windows 身份验证并禁用匿名身份验证：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-140">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="f4ea8-141">右键单击“解决方案资源管理器”中的项目，再选择“属性” 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-141">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="f4ea8-142">选择“调试”选项卡。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-142">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="f4ea8-143">清除 "**启用匿名身份验证**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-143">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="f4ea8-144">选中 "**启用 Windows 身份验证**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-144">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="f4ea8-145">保存并关闭属性页。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-145">Save and close the property page.</span></span>

<span data-ttu-id="f4ea8-146">或者，可以在文件的launchSettings.js的节点中配置属性 `iisSettings` ： *launchSettings.json*</span><span class="sxs-lookup"><span data-stu-id="f4ea8-146">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f4ea8-147">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f4ea8-147">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f4ea8-148">**新项目**</span><span class="sxs-lookup"><span data-stu-id="f4ea8-148">**New project**</span></span>

<span data-ttu-id="f4ea8-149">用[dotnet new](/dotnet/core/tools/dotnet-new) `webapp` 参数（ASP.NET Core Web 应用）和开关执行 dotnet new 命令 `--auth Windows` ：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-149">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```dotnetcli
dotnet new webapp --auth Windows
```

<span data-ttu-id="f4ea8-150">**现有项目**</span><span class="sxs-lookup"><span data-stu-id="f4ea8-150">**Existing project**</span></span>

<span data-ttu-id="f4ea8-151">更新 `iisSettings` 文件*上launchSettings.js*的节点：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-151">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="f4ea8-152">修改现有项目时，确认项目文件包括[AspNetCore 元包](xref:fundamentals/metapackage-app)的包引用**或** [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-152">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="f4ea8-153">IIS</span><span class="sxs-lookup"><span data-stu-id="f4ea8-153">IIS</span></span>

<span data-ttu-id="f4ea8-154">IIS 使用[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)来托管 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-154">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="f4ea8-155">Windows 身份验证是通过*web.config*文件配置的。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-155">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="f4ea8-156">以下部分介绍如何执行这些操作：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-156">The following sections show how to:</span></span>

* <span data-ttu-id="f4ea8-157">提供一个本地*web.config*文件，用于在部署应用时在服务器上激活 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-157">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="f4ea8-158">使用 IIS 管理器配置已部署到服务器的 ASP.NET Core 应用的*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-158">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="f4ea8-159">如果尚未执行此操作，请启用 IIS 以托管 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-159">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="f4ea8-160">有关详细信息，请参阅 <xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-160">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="f4ea8-161">为 Windows 身份验证启用 IIS 角色服务。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-161">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="f4ea8-162">有关详细信息，请参阅[在 IIS 角色服务中启用 Windows 身份验证（请参阅步骤2）](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-162">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="f4ea8-163">默认情况下，将[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)配置为自动对请求进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-163">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="f4ea8-164">有关详细信息，请参阅[在 Windows 上利用 iis 选项主机 ASP.NET Core： iis 选项（AutomaticAuthentication）](xref:host-and-deploy/iis/index#iis-options)。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-164">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="f4ea8-165">默认情况下，ASP.NET Core 模块配置为将 Windows 身份验证令牌转发到应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-165">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="f4ea8-166">有关详细信息，请参阅[ASP.NET Core 模块配置参考： aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-166">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="f4ea8-167">使用**以下方法之一：**</span><span class="sxs-lookup"><span data-stu-id="f4ea8-167">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="f4ea8-168">在**发布和部署项目之前，请**将以下*web.config*文件添加到项目根目录：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-168">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="f4ea8-169">当项目由 .NET Core SDK 发布（ `<IsTransformWebConfigDisabled>` 在项目文件中没有属性设置为 `true` ）时，已发布的*web.config*文件包含 `<location><system.webServer><security><authentication>` 节。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-169">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="f4ea8-170">有关属性的详细信息 `<IsTransformWebConfigDisabled>` ，请参阅 <xref:host-and-deploy/iis/index#webconfig-file> 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-170">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="f4ea8-171">**发布和部署项目后，请**在 IIS 管理器中执行服务器端配置：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-171">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="f4ea8-172">在 IIS 管理器中，选择 "**连接**" 边栏的 "**站点**" 节点下的 IIS 站点。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-172">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="f4ea8-173">双击**IIS**区域中的 "**身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-173">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="f4ea8-174">选择 "**匿名身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-174">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="f4ea8-175">选择 "**操作**" 侧栏中的 "**禁用**"。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-175">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="f4ea8-176">选择 **“Windows 身份验证”**。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-176">Select **Windows Authentication**.</span></span> <span data-ttu-id="f4ea8-177">选择 "**操作**" 侧栏中的 "**启用**"。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-177">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="f4ea8-178">执行这些操作时，IIS 管理器会修改应用的*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-178">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="f4ea8-179">`<system.webServer><security><authentication>`添加了和的更新设置的节点 `anonymousAuthentication` `windowsAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-179">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="f4ea8-180">`<system.webServer>`IIS 管理器添加到*web.config*文件的部分不在 `<location>` 应用发布时 .NET Core SDK 添加的应用部分。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-180">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="f4ea8-181">由于将节添加到了节点外部 `<location>` ，因此任何[子应用](xref:host-and-deploy/iis/index#sub-applications)都将这些设置继承到当前应用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-181">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="f4ea8-182">若要防止继承，请将添加的部分移动到 `<security>` `<location><system.webServer>` .NET Core SDK 提供的部分中。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-182">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="f4ea8-183">当使用 IIS 管理器添加 IIS 配置时，它只会影响该应用程序在服务器上的*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-183">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="f4ea8-184">如果*web.config*的服务器副本替换为项目的*web.config*文件，则该应用的后续部署可能会覆盖服务器上的设置。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-184">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="f4ea8-185">使用以下**两**种方法之一来管理设置：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-185">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="f4ea8-186">在部署时覆盖文件后，使用 IIS 管理器重置*web.config*文件中的设置。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-186">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="f4ea8-187">使用设置在本地将*web.config 文件*添加到应用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-187">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="f4ea8-188">Kestrel</span><span class="sxs-lookup"><span data-stu-id="f4ea8-188">Kestrel</span></span>

<span data-ttu-id="f4ea8-189">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 包可与[Kestrel](xref:fundamentals/servers/kestrel)配合使用，以支持在 Windows、Linux 和 macOS 上使用 Negotiate 和 Kerberos 的 windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-189">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate and Kerberos on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="f4ea8-190">可以在连接的请求间保留凭据。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-190">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="f4ea8-191">*除非代理维护1:1 连接关联（持久性连接）与 Kestrel，否则协商身份验证不能与代理一起使用。*</span><span class="sxs-lookup"><span data-stu-id="f4ea8-191">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="f4ea8-192">Negotiate 处理程序检测基础服务器是否支持本机 Windows 身份验证以及是否已启用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-192">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="f4ea8-193">如果服务器支持 Windows 身份验证，但禁用了它，则会引发错误，要求你启用服务器实现。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-193">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="f4ea8-194">如果在服务器中启用了 Windows 身份验证，则协商处理程序会以透明方式转发到该身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-194">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

<span data-ttu-id="f4ea8-195">通过调用和中的来添加身份验证服务 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-195">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> in `Startup.ConfigureServices`:</span></span>

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="f4ea8-196">通过在中调用来添加身份验证中间件 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-196">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();
```

<span data-ttu-id="f4ea8-197">有关中间件的详细信息，请参阅 <xref:fundamentals/middleware/index> 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-197">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="f4ea8-198">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-198">Anonymous requests are allowed.</span></span> <span data-ttu-id="f4ea8-199">使用[ASP.NET Core 授权](xref:security/authorization/introduction)质询请求身份验证的匿名请求。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-199">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="f4ea8-200">Windows 环境配置</span><span class="sxs-lookup"><span data-stu-id="f4ea8-200">Windows environment configuration</span></span>

<span data-ttu-id="f4ea8-201">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)组件执行用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-201">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="f4ea8-202">必须将服务主体名称（Spn）添加到运行服务的用户帐户，而不是计算机帐户。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-202">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="f4ea8-203">`setspn -S HTTP/myservername.mydomain.com myuser`在管理命令行界面中执行。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-203">Execute `setspn -S HTTP/myservername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="f4ea8-204">Linux 和 macOS 环境配置</span><span class="sxs-lookup"><span data-stu-id="f4ea8-204">Linux and macOS environment configuration</span></span>

<span data-ttu-id="f4ea8-205">有关将 Linux 或 macOS 计算机加入到 Windows 域的说明，请访问[使用 windows 身份验证将 Azure Data Studio 连接到 SQL Server-Kerberos 一](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)文。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-205">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="f4ea8-206">说明为域中的 Linux 计算机创建计算机帐户。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-206">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="f4ea8-207">必须将 Spn 添加到该计算机帐户。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-207">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="f4ea8-208">按照[使用 Windows 身份验证将 Azure Data Studio 连接到 SQL Server](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller)中的指导进行时，请在需要时将替换 `python-software-properties` 为 `python3-software-properties` 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-208">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="f4ea8-209">将 Linux 或 macOS 计算机加入到域后，还需要执行其他步骤以使用 Spn 提供[keytab 文件](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-209">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="f4ea8-210">在域控制器上，向计算机帐户添加新的 web 服务 Spn：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-210">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="f4ea8-211">使用[ktpass](/windows-server/administration/windows-commands/ktpass)生成 keytab 文件：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-211">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="f4ea8-212">某些字段必须以大写字母指定，如所示。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-212">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="f4ea8-213">将 keytab 文件复制到 Linux 或 macOS 计算机。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-213">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="f4ea8-214">通过环境变量选择 keytab 文件：`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="f4ea8-214">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="f4ea8-215">调用 `klist` 以显示当前可供使用的 spn。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-215">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="f4ea8-216">Keytab 文件包含域访问凭据并且必须相应地受到保护。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-216">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="f4ea8-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="f4ea8-217">HTTP.sys</span></span>

<span data-ttu-id="f4ea8-218">[HTTP.sys](xref:fundamentals/servers/httpsys)支持使用协商、NTLM 或基本身份验证的内核模式 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-218">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="f4ea8-219">通过 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 在中调用（命名空间）来添加身份验证服务 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f4ea8-219">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="f4ea8-220">将应用程序的 web 主机配置为使用 HTTP.sys Windows 身份验证（*Program.cs*）。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-220">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="f4ea8-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>位于 <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-221"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f4ea8-222">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-222">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="f4ea8-223">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-223">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="f4ea8-224">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-224">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="f4ea8-225">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-225">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="f4ea8-226">Nano Server 版本1709或更高版本不支持 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-226">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="f4ea8-227">若要将 Windows 身份验证和 HTTP.sys 与 Nano Server 一起使用，请使用[服务器核心（microsoft/windowsservercore）容器](https://hub.docker.com/r/microsoft/windowsservercore/)。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-227">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="f4ea8-228">有关服务器核心的详细信息，请参阅[Windows server 中的什么是服务器核心安装选项？](/windows-server/administration/server-core/what-is-server-core)。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-228">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="f4ea8-229">对用户授权</span><span class="sxs-lookup"><span data-stu-id="f4ea8-229">Authorize users</span></span>

<span data-ttu-id="f4ea8-230">匿名访问的配置状态决定了 `[Authorize]` 和 `[AllowAnonymous]` 属性在应用中的使用方式。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-230">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="f4ea8-231">以下两节说明如何处理匿名访问的禁止和允许的配置状态。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-231">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="f4ea8-232">禁止匿名访问</span><span class="sxs-lookup"><span data-stu-id="f4ea8-232">Disallow anonymous access</span></span>

<span data-ttu-id="f4ea8-233">启用 Windows 身份验证并禁用匿名访问时， `[Authorize]` 和 `[AllowAnonymous]` 属性不起作用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-233">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="f4ea8-234">如果将 IIS 站点配置为不允许匿名访问，则请求将永远不会到达该应用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-234">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="f4ea8-235">出于此原因，该 `[AllowAnonymous]` 属性不适用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-235">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="f4ea8-236">允许匿名访问</span><span class="sxs-lookup"><span data-stu-id="f4ea8-236">Allow anonymous access</span></span>

<span data-ttu-id="f4ea8-237">同时启用 Windows 身份验证和匿名访问时，请使用 `[Authorize]` 和 `[AllowAnonymous]` 属性。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-237">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="f4ea8-238">`[Authorize]`属性允许保护需要身份验证的应用的终结点。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-238">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="f4ea8-239">`[AllowAnonymous]`特性替代 `[Authorize]` 允许匿名访问的应用程序中的特性。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-239">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="f4ea8-240">有关属性用法的详细信息，请参阅 <xref:security/authorization/simple> 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-240">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="f4ea8-241">默认情况下，如果用户无权访问页面，则会显示空的 HTTP 403 响应。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-241">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="f4ea8-242">可以将[StatusCodePages 中间件](xref:fundamentals/error-handling#usestatuscodepages)配置为向用户提供更好的 "访问被拒绝" 体验。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-242">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="f4ea8-243">模拟</span><span class="sxs-lookup"><span data-stu-id="f4ea8-243">Impersonation</span></span>

<span data-ttu-id="f4ea8-244">ASP.NET Core 不实现模拟。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-244">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="f4ea8-245">应用使用应用池或进程标识，使用应用的标识运行所有请求。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-245">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="f4ea8-246">如果应用应代表用户执行操作，请在中的[终端内联中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)中使用[RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 。 `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="f4ea8-246">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="f4ea8-247">在此上下文中运行单个操作，然后关闭上下文。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-247">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="f4ea8-248">`RunImpersonated`不支持异步操作，因此不应用于复杂方案。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-248">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="f4ea8-249">例如，不支持包装整个请求或中间件链，也不建议使用。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-249">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4ea8-250">尽管[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)包在 Windows、Linux 和 macOS 上启用身份验证，但仅在 windows 上支持模拟。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-250">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="f4ea8-251">声明转换</span><span class="sxs-lookup"><span data-stu-id="f4ea8-251">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4ea8-252">当使用 IIS 进行托管时， <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 不会在内部调用来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-252">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f4ea8-253">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-253">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f4ea8-254">有关激活声明转换的详细信息和代码示例，请参阅 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-254">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f4ea8-255">当使用 IIS 进程内模式进行承载时， <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 不会在内部调用来初始化用户。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-255">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f4ea8-256">因此，默认情况下不激活每次身份验证后用于转换声明的 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 实现。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-256">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f4ea8-257">有关在进程内承载时激活声明转换的详细信息和代码示例，请参阅 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> 。</span><span class="sxs-lookup"><span data-stu-id="f4ea8-257">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f4ea8-258">其他资源</span><span class="sxs-lookup"><span data-stu-id="f4ea8-258">Additional resources</span></span>

* [<span data-ttu-id="f4ea8-259">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="f4ea8-259">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
