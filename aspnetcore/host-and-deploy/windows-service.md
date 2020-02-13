---
title: 在 Windows 服务中托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows 服务中托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 829c282606e60a80682233555e1268acb706090e
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172329"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="1f5e8-103">在 Windows 服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f5e8-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="1f5e8-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1f5e8-105">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1f5e8-106">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1f5e8-107">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1f5e8-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f5e8-108">先决条件</span><span class="sxs-lookup"><span data-stu-id="1f5e8-108">Prerequisites</span></span>

* [<span data-ttu-id="1f5e8-109">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="1f5e8-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1f5e8-110">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="1f5e8-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="1f5e8-111">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="1f5e8-111">Worker Service template</span></span>

<span data-ttu-id="1f5e8-112">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="1f5e8-113">要使用该模板作为编写 Windows 服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="1f5e8-114">从 .NET Core 模板创建辅助角色服务应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="1f5e8-115">按照[应用配置](#app-configuration)部分中的指导来更新辅助角色服务应用，以便它可以作为 Windows 服务运行。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="1f5e8-116">应用配置</span><span class="sxs-lookup"><span data-stu-id="1f5e8-116">App configuration</span></span>

<span data-ttu-id="1f5e8-117">应用需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="1f5e8-118">生成主机时会调用 `IHostBuilder.UseWindowsService`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="1f5e8-119">若应用作为 Windows 服务运行，方法为：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="1f5e8-120">将主机生存期设置为 `WindowsServiceLifetime`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="1f5e8-121">将[内容根](xref:fundamentals/index#content-root)设置为 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-121">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="1f5e8-122">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-122">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="1f5e8-123">为事件日志启用日志记录：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-123">Enables logging to the event log:</span></span>
  * <span data-ttu-id="1f5e8-124">应用名称用作默认源名称。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-124">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="1f5e8-125">对于基于 ASP.NET Core 模板且调用 `CreateDefaultBuilder` 生成主机的应用，默认日志级别为“警告”  或更高级别。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-125">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="1f5e8-126">在 appsettings.json  /appsettings.{Environment}.json  或其他配置提供程序中，使用 `Logging:EventLog:LogLevel:Default` 键覆盖默认日志级别。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-126">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="1f5e8-127">只有管理员可以创建新的事件源。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-127">Only administrators can create new event sources.</span></span> <span data-ttu-id="1f5e8-128">无法使用应用程序名称创建事件源时，应用程序源将记录一条警告，并禁用事件源。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-128">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="1f5e8-129">在 Program.cs 的 `CreateHostBuilder` 中  ：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-129">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="1f5e8-130">本主题附带了以下示例应用：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-130">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="1f5e8-131">后台辅助角色服务示例 &ndash; 一个基于[辅助角色服务模板](#worker-service-template)的非 Web 应用示例，该模板使用[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-131">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="1f5e8-132">Web 应用服务示例 &ndash; 一个作为 Windows 服务运行的 Razor Pages Web 应用示例，通过[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-132">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="1f5e8-133">有关 MVC 指南，请参阅 <xref:mvc/overview> 和 <xref:migration/22-to-30> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-133">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="1f5e8-134">部署类型</span><span class="sxs-lookup"><span data-stu-id="1f5e8-134">Deployment type</span></span>

<span data-ttu-id="1f5e8-135">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-135">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1f5e8-136">SDK</span><span class="sxs-lookup"><span data-stu-id="1f5e8-136">SDK</span></span>

<span data-ttu-id="1f5e8-137">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-137">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1f5e8-138">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-138">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1f5e8-139">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-139">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1f5e8-140">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-140">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1f5e8-141">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-141">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="1f5e8-142">如果使用 [Web SDK](#sdk)，则 web.config 文件（通常在发布 ASP.NET Core 应用时生成）不是 Windows 服务应用的必要文件  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-142">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1f5e8-143">若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-143">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1f5e8-144">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-144">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1f5e8-145">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-145">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1f5e8-146">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-146">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1f5e8-147">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-147">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1f5e8-148">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-148">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1f5e8-149">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-149">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1f5e8-150">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-150">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1f5e8-151">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-151">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="1f5e8-152">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="1f5e8-152">Service user account</span></span>

<span data-ttu-id="1f5e8-153">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-153">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1f5e8-154">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-154">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-155">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-155">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1f5e8-156">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-156">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1f5e8-157">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-157">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1f5e8-158">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-158">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1f5e8-159">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-159">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1f5e8-160">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-160">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1f5e8-161">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="1f5e8-161">Log on as a service rights</span></span>

<span data-ttu-id="1f5e8-162">为服务用户帐户创建“以服务身份登录”权限： </span><span class="sxs-lookup"><span data-stu-id="1f5e8-162">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1f5e8-163">通过运行 secpool.msc  ，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-163">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="1f5e8-164">展开“本地策略”节点，选择“用户权限分配”。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-164">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="1f5e8-165">打开“以服务身份登录”策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-165">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1f5e8-166">选择“添加用户或组”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-166">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="1f5e8-167">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-167">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1f5e8-168">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-168">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1f5e8-169">选择“高级”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-169">Select **Advanced**.</span></span> <span data-ttu-id="1f5e8-170">选择“开始查找”。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-170">Select **Find Now**.</span></span> <span data-ttu-id="1f5e8-171">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-171">Select the user account from the list.</span></span> <span data-ttu-id="1f5e8-172">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-172">Select **OK**.</span></span> <span data-ttu-id="1f5e8-173">再次选择“确定”，以将该用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-173">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1f5e8-174">选择“确定”或“应用”，以接受更改。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-174">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1f5e8-175">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-175">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1f5e8-176">创建服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-176">Create a service</span></span>

<span data-ttu-id="1f5e8-177">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-177">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1f5e8-178">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-178">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1f5e8-179">`{EXE PATH}` &ndash; 应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-179">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1f5e8-180">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-180">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1f5e8-181">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-181">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1f5e8-182">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; 服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-182">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1f5e8-183">`{SERVICE NAME}` &ndash; 服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-183">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1f5e8-184">`{EXE FILE PATH}` &ndash; 应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-184">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1f5e8-185">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-185">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1f5e8-186">`{DESCRIPTION}` &ndash; 服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-186">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1f5e8-187">`{DISPLAY NAME}` &ndash; 服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-187">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1f5e8-188">启动服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-188">Start a service</span></span>

<span data-ttu-id="1f5e8-189">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-189">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-190">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-190">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1f5e8-191">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="1f5e8-191">Determine a service's status</span></span>

<span data-ttu-id="1f5e8-192">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-192">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-193">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-193">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1f5e8-194">停止服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-194">Stop a service</span></span>

<span data-ttu-id="1f5e8-195">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-195">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1f5e8-196">删除服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-196">Remove a service</span></span>

<span data-ttu-id="1f5e8-197">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-197">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1f5e8-198">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="1f5e8-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1f5e8-199">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-199">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1f5e8-200">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-200">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1f5e8-201">配置终结点</span><span class="sxs-lookup"><span data-stu-id="1f5e8-201">Configure endpoints</span></span>

<span data-ttu-id="1f5e8-202">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-202">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1f5e8-203">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-203">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1f5e8-204">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-204">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1f5e8-205">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-205">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1f5e8-206">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-206">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1f5e8-207">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-207">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1f5e8-208">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="1f5e8-208">Current directory and content root</span></span>

<span data-ttu-id="1f5e8-209">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-209">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1f5e8-210">system32 文件夹不是存储服务文件（如设置文件）的合适位置  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-210">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1f5e8-211">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-211">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="1f5e8-212">使用 ContentRootPath 或 ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="1f5e8-212">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="1f5e8-213">使用 [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 或 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> 查找应用的资源。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-213">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="1f5e8-214">应用作为服务运行时，<xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> 将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> 设置为 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-214">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="1f5e8-215">通过调用 [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host)，从应用的内容根加载应用的默认设置文件 appsettings.json 和 appsettings.{Environment}.json。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-215">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="1f5e8-216">对于 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 中的开发人员代码加载的其他设置文件，无需调用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-216">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="1f5e8-217">在下面的示例中，custom_settings.json 文件位于应用的内容根，加载它时未显式设置基本路径： </span><span class="sxs-lookup"><span data-stu-id="1f5e8-217">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="1f5e8-218">请勿尝试使用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取资源路径，因为 Windows 服务应用会返回“C:\\WINDOWS\\system32”文件夹作为其当前目录。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-218">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1f5e8-219">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="1f5e8-219">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1f5e8-220">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-220">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1f5e8-221">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f5e8-221">Troubleshoot</span></span>

<span data-ttu-id="1f5e8-222">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-222">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1f5e8-223">常见错误</span><span class="sxs-lookup"><span data-stu-id="1f5e8-223">Common errors</span></span>

* <span data-ttu-id="1f5e8-224">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-224">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1f5e8-225">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-225">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1f5e8-226">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-226">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1f5e8-227">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-227">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1f5e8-228">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-228">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1f5e8-229">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-229">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1f5e8-230">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-230">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1f5e8-231">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-231">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1f5e8-232">Windows 服务的基本路径是“c:\\Windows\\System32”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-232">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="1f5e8-233">用户没有“以服务身份登录”权限。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-233">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1f5e8-234">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-234">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1f5e8-235">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-235">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1f5e8-236">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-236">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1f5e8-237">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="1f5e8-237">System and Application Event Logs</span></span>

<span data-ttu-id="1f5e8-238">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-238">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1f5e8-239">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-239">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1f5e8-240">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-240">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1f5e8-241">选择“系统”，以打开系统事件日志。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-241">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1f5e8-242">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-242">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1f5e8-243">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-243">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1f5e8-244">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="1f5e8-244">Run the app at a command prompt</span></span>

<span data-ttu-id="1f5e8-245">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-245">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1f5e8-246">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-246">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1f5e8-247">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-247">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1f5e8-248">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="1f5e8-248">Clear package caches</span></span>

<span data-ttu-id="1f5e8-249">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-249">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1f5e8-250">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-250">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1f5e8-251">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-251">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1f5e8-252">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-252">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1f5e8-253">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-253">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1f5e8-254">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-254">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1f5e8-255">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-255">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1f5e8-256">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-256">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1f5e8-257">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-257">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1f5e8-258">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="1f5e8-258">Slow or hanging app</span></span>

<span data-ttu-id="1f5e8-259">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-259">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1f5e8-260">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="1f5e8-260">App crashes or encounters an exception</span></span>

<span data-ttu-id="1f5e8-261">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-261">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1f5e8-262">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-262">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1f5e8-263">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-263">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1f5e8-264">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-264">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1f5e8-265">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-265">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1f5e8-266">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-266">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1f5e8-267">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-267">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1f5e8-268">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-268">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1f5e8-269">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="1f5e8-269">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1f5e8-270">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-270">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1f5e8-271">分析转储</span><span class="sxs-lookup"><span data-stu-id="1f5e8-271">Analyze the dump</span></span>

<span data-ttu-id="1f5e8-272">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-272">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1f5e8-273">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-273">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f5e8-274">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f5e8-274">Additional resources</span></span>

* <span data-ttu-id="1f5e8-275">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="1f5e8-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1f5e8-276">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1f5e8-277">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1f5e8-278">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1f5e8-278">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f5e8-279">先决条件</span><span class="sxs-lookup"><span data-stu-id="1f5e8-279">Prerequisites</span></span>

* [<span data-ttu-id="1f5e8-280">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="1f5e8-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1f5e8-281">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="1f5e8-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="1f5e8-282">应用配置</span><span class="sxs-lookup"><span data-stu-id="1f5e8-282">App configuration</span></span>

<span data-ttu-id="1f5e8-283">应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1f5e8-284">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1f5e8-285">检查是否已连接调试器或是否存在 `--console` 开关。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="1f5e8-286">如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="1f5e8-287">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="1f5e8-288">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1f5e8-289">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1f5e8-290">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="1f5e8-291">请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="1f5e8-292">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="1f5e8-293">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="1f5e8-294">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1f5e8-295">使用 appsettings.Production.json  文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="1f5e8-296">示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="1f5e8-297">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="1f5e8-298">部署类型</span><span class="sxs-lookup"><span data-stu-id="1f5e8-298">Deployment type</span></span>

<span data-ttu-id="1f5e8-299">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1f5e8-300">SDK</span><span class="sxs-lookup"><span data-stu-id="1f5e8-300">SDK</span></span>

<span data-ttu-id="1f5e8-301">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1f5e8-302">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1f5e8-303">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1f5e8-304">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1f5e8-305">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="1f5e8-306">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1f5e8-307">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1f5e8-308">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1f5e8-309">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe  ) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1f5e8-310">web.config  文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1f5e8-311">若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1f5e8-312">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1f5e8-313">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1f5e8-314">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1f5e8-315">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1f5e8-316">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1f5e8-317">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1f5e8-318">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1f5e8-319">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="1f5e8-320">将 `<SelfContained>` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="1f5e8-321">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="1f5e8-321">Service user account</span></span>

<span data-ttu-id="1f5e8-322">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1f5e8-323">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-324">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1f5e8-325">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1f5e8-326">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1f5e8-327">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1f5e8-328">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1f5e8-329">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1f5e8-330">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="1f5e8-330">Log on as a service rights</span></span>

<span data-ttu-id="1f5e8-331">为服务用户帐户创建“以服务身份登录”权限： </span><span class="sxs-lookup"><span data-stu-id="1f5e8-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1f5e8-332">通过运行 secpool.msc  ，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="1f5e8-333">展开“本地策略”节点，选择“用户权限分配”。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="1f5e8-334">打开“以服务身份登录”策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1f5e8-335">选择“添加用户或组”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="1f5e8-336">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1f5e8-337">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1f5e8-338">选择“高级”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-338">Select **Advanced**.</span></span> <span data-ttu-id="1f5e8-339">选择“开始查找”。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-339">Select **Find Now**.</span></span> <span data-ttu-id="1f5e8-340">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-340">Select the user account from the list.</span></span> <span data-ttu-id="1f5e8-341">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-341">Select **OK**.</span></span> <span data-ttu-id="1f5e8-342">再次选择“确定”，以将该用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1f5e8-343">选择“确定”或“应用”，以接受更改。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1f5e8-344">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1f5e8-345">创建服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-345">Create a service</span></span>

<span data-ttu-id="1f5e8-346">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1f5e8-347">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1f5e8-348">`{EXE PATH}` &ndash; 应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-348">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1f5e8-349">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1f5e8-350">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1f5e8-351">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; 服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-351">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1f5e8-352">`{SERVICE NAME}` &ndash; 服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-352">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1f5e8-353">`{EXE FILE PATH}` &ndash; 应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-353">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1f5e8-354">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1f5e8-355">`{DESCRIPTION}` &ndash; 服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-355">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1f5e8-356">`{DISPLAY NAME}` &ndash; 服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-356">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1f5e8-357">启动服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-357">Start a service</span></span>

<span data-ttu-id="1f5e8-358">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-359">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1f5e8-360">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="1f5e8-360">Determine a service's status</span></span>

<span data-ttu-id="1f5e8-361">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-362">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1f5e8-363">停止服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-363">Stop a service</span></span>

<span data-ttu-id="1f5e8-364">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1f5e8-365">删除服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-365">Remove a service</span></span>

<span data-ttu-id="1f5e8-366">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1f5e8-367">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="1f5e8-367">Handle starting and stopping events</span></span>

<span data-ttu-id="1f5e8-368">处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="1f5e8-369">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1f5e8-370">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1f5e8-371">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1f5e8-372">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1f5e8-373">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="1f5e8-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1f5e8-374">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1f5e8-375">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1f5e8-376">配置终结点</span><span class="sxs-lookup"><span data-stu-id="1f5e8-376">Configure endpoints</span></span>

<span data-ttu-id="1f5e8-377">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1f5e8-378">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1f5e8-379">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1f5e8-380">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1f5e8-381">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1f5e8-382">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1f5e8-383">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="1f5e8-383">Current directory and content root</span></span>

<span data-ttu-id="1f5e8-384">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1f5e8-385">system32 文件夹不是存储服务文件（如设置文件）的合适位置  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1f5e8-386">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1f5e8-387">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="1f5e8-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1f5e8-388"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="1f5e8-389">请调用包含应用[内容根目录](xref:fundamentals/index#content-root)的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="1f5e8-390">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1f5e8-391">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="1f5e8-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1f5e8-392">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1f5e8-393">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f5e8-393">Troubleshoot</span></span>

<span data-ttu-id="1f5e8-394">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1f5e8-395">常见错误</span><span class="sxs-lookup"><span data-stu-id="1f5e8-395">Common errors</span></span>

* <span data-ttu-id="1f5e8-396">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1f5e8-397">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1f5e8-398">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1f5e8-399">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1f5e8-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1f5e8-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1f5e8-402">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1f5e8-403">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1f5e8-404">Windows 服务的基本路径是“c:\\Windows\\System32”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="1f5e8-405">用户没有“以服务身份登录”权限。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1f5e8-406">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1f5e8-407">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1f5e8-408">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1f5e8-409">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="1f5e8-409">System and Application Event Logs</span></span>

<span data-ttu-id="1f5e8-410">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1f5e8-411">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1f5e8-412">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1f5e8-413">选择“系统”，以打开系统事件日志。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1f5e8-414">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1f5e8-415">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1f5e8-416">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="1f5e8-416">Run the app at a command prompt</span></span>

<span data-ttu-id="1f5e8-417">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1f5e8-418">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1f5e8-419">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1f5e8-420">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="1f5e8-420">Clear package caches</span></span>

<span data-ttu-id="1f5e8-421">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1f5e8-422">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1f5e8-423">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1f5e8-424">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1f5e8-425">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1f5e8-426">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1f5e8-427">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1f5e8-428">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1f5e8-429">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1f5e8-430">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="1f5e8-430">Slow or hanging app</span></span>

<span data-ttu-id="1f5e8-431">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1f5e8-432">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="1f5e8-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="1f5e8-433">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1f5e8-434">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1f5e8-435">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-435">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1f5e8-436">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1f5e8-437">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1f5e8-438">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1f5e8-439">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1f5e8-440">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1f5e8-441">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="1f5e8-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1f5e8-442">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1f5e8-443">分析转储</span><span class="sxs-lookup"><span data-stu-id="1f5e8-443">Analyze the dump</span></span>

<span data-ttu-id="1f5e8-444">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1f5e8-445">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f5e8-446">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f5e8-446">Additional resources</span></span>

* <span data-ttu-id="1f5e8-447">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="1f5e8-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1f5e8-448">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="1f5e8-449">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="1f5e8-450">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1f5e8-450">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f5e8-451">先决条件</span><span class="sxs-lookup"><span data-stu-id="1f5e8-451">Prerequisites</span></span>

* [<span data-ttu-id="1f5e8-452">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="1f5e8-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="1f5e8-453">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="1f5e8-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="1f5e8-454">应用配置</span><span class="sxs-lookup"><span data-stu-id="1f5e8-454">App configuration</span></span>

<span data-ttu-id="1f5e8-455">应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="1f5e8-456">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="1f5e8-457">检查是否已连接调试器或是否存在 `--console` 开关。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="1f5e8-458">如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="1f5e8-459">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="1f5e8-460">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="1f5e8-461">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="1f5e8-462">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="1f5e8-463">请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="1f5e8-464">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="1f5e8-465">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="1f5e8-466">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="1f5e8-467">使用 appsettings.Production.json  文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="1f5e8-468">示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="1f5e8-469">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="1f5e8-470">部署类型</span><span class="sxs-lookup"><span data-stu-id="1f5e8-470">Deployment type</span></span>

<span data-ttu-id="1f5e8-471">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="1f5e8-472">SDK</span><span class="sxs-lookup"><span data-stu-id="1f5e8-472">SDK</span></span>

<span data-ttu-id="1f5e8-473">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="1f5e8-474">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="1f5e8-475">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="1f5e8-476">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="1f5e8-477">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="1f5e8-478">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="1f5e8-479">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="1f5e8-480">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="1f5e8-481">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe  ) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="1f5e8-482">`<UseAppHost>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="1f5e8-483">此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe  ）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="1f5e8-484">web.config  文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="1f5e8-485">若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="1f5e8-486">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="1f5e8-487">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="1f5e8-488">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="1f5e8-489">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="1f5e8-490">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="1f5e8-491">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="1f5e8-492">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="1f5e8-493">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="1f5e8-494">将 `<SelfContained>` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="1f5e8-495">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="1f5e8-495">Service user account</span></span>

<span data-ttu-id="1f5e8-496">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="1f5e8-497">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-498">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="1f5e8-499">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="1f5e8-500">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="1f5e8-501">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="1f5e8-502">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="1f5e8-503">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="1f5e8-504">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="1f5e8-504">Log on as a service rights</span></span>

<span data-ttu-id="1f5e8-505">为服务用户帐户创建“以服务身份登录”权限： </span><span class="sxs-lookup"><span data-stu-id="1f5e8-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="1f5e8-506">通过运行 secpool.msc  ，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="1f5e8-507">展开“本地策略”节点，选择“用户权限分配”。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="1f5e8-508">打开“以服务身份登录”策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="1f5e8-509">选择“添加用户或组”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="1f5e8-510">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="1f5e8-511">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="1f5e8-512">选择“高级”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-512">Select **Advanced**.</span></span> <span data-ttu-id="1f5e8-513">选择“开始查找”。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-513">Select **Find Now**.</span></span> <span data-ttu-id="1f5e8-514">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-514">Select the user account from the list.</span></span> <span data-ttu-id="1f5e8-515">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-515">Select **OK**.</span></span> <span data-ttu-id="1f5e8-516">再次选择“确定”，以将该用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="1f5e8-517">选择“确定”或“应用”，以接受更改。  </span><span class="sxs-lookup"><span data-stu-id="1f5e8-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="1f5e8-518">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="1f5e8-519">创建服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-519">Create a service</span></span>

<span data-ttu-id="1f5e8-520">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="1f5e8-521">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="1f5e8-522">`{EXE PATH}` &ndash; 应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-522">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="1f5e8-523">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="1f5e8-524">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="1f5e8-525">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; 服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-525">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="1f5e8-526">`{SERVICE NAME}` &ndash; 服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-526">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="1f5e8-527">`{EXE FILE PATH}` &ndash; 应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-527">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="1f5e8-528">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="1f5e8-529">`{DESCRIPTION}` &ndash; 服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-529">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="1f5e8-530">`{DISPLAY NAME}` &ndash; 服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-530">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="1f5e8-531">启动服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-531">Start a service</span></span>

<span data-ttu-id="1f5e8-532">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-533">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="1f5e8-534">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="1f5e8-534">Determine a service's status</span></span>

<span data-ttu-id="1f5e8-535">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="1f5e8-536">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="1f5e8-537">停止服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-537">Stop a service</span></span>

<span data-ttu-id="1f5e8-538">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="1f5e8-539">删除服务</span><span class="sxs-lookup"><span data-stu-id="1f5e8-539">Remove a service</span></span>

<span data-ttu-id="1f5e8-540">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="1f5e8-541">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="1f5e8-541">Handle starting and stopping events</span></span>

<span data-ttu-id="1f5e8-542">处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="1f5e8-543">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="1f5e8-544">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="1f5e8-545">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="1f5e8-546">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="1f5e8-547">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="1f5e8-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="1f5e8-548">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="1f5e8-549">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="1f5e8-550">配置终结点</span><span class="sxs-lookup"><span data-stu-id="1f5e8-550">Configure endpoints</span></span>

<span data-ttu-id="1f5e8-551">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="1f5e8-552">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="1f5e8-553">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="1f5e8-554">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="1f5e8-555">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="1f5e8-556">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="1f5e8-557">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="1f5e8-557">Current directory and content root</span></span>

<span data-ttu-id="1f5e8-558">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="1f5e8-559">system32 文件夹不是存储服务文件（如设置文件）的合适位置  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="1f5e8-560">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="1f5e8-561">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="1f5e8-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="1f5e8-562"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="1f5e8-563">请调用包含应用[内容根目录](xref:fundamentals/index#content-root)的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="1f5e8-564">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="1f5e8-565">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="1f5e8-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="1f5e8-566">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="1f5e8-567">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1f5e8-567">Troubleshoot</span></span>

<span data-ttu-id="1f5e8-568">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="1f5e8-569">常见错误</span><span class="sxs-lookup"><span data-stu-id="1f5e8-569">Common errors</span></span>

* <span data-ttu-id="1f5e8-570">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="1f5e8-571">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="1f5e8-572">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="1f5e8-573">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="1f5e8-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="1f5e8-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="1f5e8-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="1f5e8-576">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="1f5e8-577">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="1f5e8-578">Windows 服务的基本路径是“c:\\Windows\\System32”  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="1f5e8-579">用户没有“以服务身份登录”权限。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="1f5e8-580">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="1f5e8-581">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="1f5e8-582">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="1f5e8-583">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="1f5e8-583">System and Application Event Logs</span></span>

<span data-ttu-id="1f5e8-584">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="1f5e8-585">打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1f5e8-586">在“事件查看器”  中，打开“Windows 日志”  节点。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1f5e8-587">选择“系统”，以打开系统事件日志。 </span><span class="sxs-lookup"><span data-stu-id="1f5e8-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="1f5e8-588">选择“应用程序”  以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1f5e8-589">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1f5e8-590">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="1f5e8-590">Run the app at a command prompt</span></span>

<span data-ttu-id="1f5e8-591">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="1f5e8-592">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="1f5e8-593">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="1f5e8-594">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="1f5e8-594">Clear package caches</span></span>

<span data-ttu-id="1f5e8-595">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="1f5e8-596">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1f5e8-597">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1f5e8-598">删除 bin  和 obj  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1f5e8-599">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="1f5e8-600">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1f5e8-601">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="1f5e8-602">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1f5e8-603">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="1f5e8-604">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="1f5e8-604">Slow or hanging app</span></span>

<span data-ttu-id="1f5e8-605">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1f5e8-606">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="1f5e8-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="1f5e8-607">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1f5e8-608">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="1f5e8-609">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-609">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="1f5e8-610">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1f5e8-611">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="1f5e8-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="1f5e8-612">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1f5e8-613">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1f5e8-614">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1f5e8-615">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="1f5e8-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1f5e8-616">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="1f5e8-617">分析转储</span><span class="sxs-lookup"><span data-stu-id="1f5e8-617">Analyze the dump</span></span>

<span data-ttu-id="1f5e8-618">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1f5e8-619">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="1f5e8-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f5e8-620">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f5e8-620">Additional resources</span></span>

* <span data-ttu-id="1f5e8-621">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="1f5e8-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
