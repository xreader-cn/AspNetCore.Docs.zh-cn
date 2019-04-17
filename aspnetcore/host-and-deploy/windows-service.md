---
title: 在 Windows 服务中托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows 服务中托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/04/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 544eefa87898e82ec2bf8f9f61ce4e26dd554bb7
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068331"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="3dd9e-103">在 Windows 服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dd9e-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="3dd9e-104">作者：[Luke Latham](https://github.com/guardrex)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3dd9e-105">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="3dd9e-106">作为 Windows 服务进行托管时，应用将在重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="3dd9e-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3dd9e-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3dd9e-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="3dd9e-108">Prerequisites</span></span>

* [<span data-ttu-id="3dd9e-109">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3dd9e-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

> [!NOTE]
> <span data-ttu-id="3dd9e-110">对于版本低于 Windows 10 2018 年 10 月更新（版本 1809/生成号 10.0.17763）的 Windows OS，[Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts) 模块必须与 [WindowsCompatibility 模块](https://github.com/PowerShell/WindowsCompatibility)一起导入，才能有权访问[创建用户帐户](#create-a-user-account)部分中使用的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-110">For Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763), the [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts) module must be imported with the [WindowsCompatibility module](https://github.com/PowerShell/WindowsCompatibility) to gain access to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet used in the [Create a user account](#create-a-user-account) section:</span></span>
>
> ```powershell
> Install-Module WindowsCompatibility -Scope CurrentUser
> Import-WinModule Microsoft.PowerShell.LocalAccounts
> ```

## <a name="deployment-type"></a><span data-ttu-id="3dd9e-111">部署类型</span><span class="sxs-lookup"><span data-stu-id="3dd9e-111">Deployment type</span></span>

<span data-ttu-id="3dd9e-112">可以创建依赖框架的 Windows 服务部署或独立的 Windows 服务部署。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-112">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="3dd9e-113">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-113">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="3dd9e-114">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="3dd9e-114">Framework-dependent deployment</span></span>

<span data-ttu-id="3dd9e-115">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-115">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="3dd9e-116">当 FDD 方案用于 ASP.NET Core Windows 服务应用时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (*\*.exe*)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-116">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="3dd9e-117">独立部署</span><span class="sxs-lookup"><span data-stu-id="3dd9e-117">Self-contained deployment</span></span>

<span data-ttu-id="3dd9e-118">独立部署 (SCD) 不依赖目标系统上存在的共享组件。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-118">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="3dd9e-119">运行时和应用的依赖项将与应用一起部署到托管系统。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-119">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="3dd9e-120">将项目转换为 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="3dd9e-120">Convert a project into a Windows Service</span></span>

<span data-ttu-id="3dd9e-121">对现有 ASP.NET Core 项目进行以下更改，以将应用作为服务运行：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-121">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="3dd9e-122">项目文件更新</span><span class="sxs-lookup"><span data-stu-id="3dd9e-122">Project file updates</span></span>

<span data-ttu-id="3dd9e-123">根据所选的[部署类型](#deployment-type)，更新项目文件：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-123">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="3dd9e-124">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-124">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="3dd9e-125">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-125">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="3dd9e-126">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-126">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="3dd9e-127">将 `<SelfContained>` 属性集添加到 `false`。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-127">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="3dd9e-128">这些属性指示 SDK 为 Windows 生成可执行 (.exe) 文件。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-128">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="3dd9e-129">web.config文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-129">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="3dd9e-130">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-130">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3dd9e-131">将 `<UseAppHost>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-131">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="3dd9e-132">此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe）。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-132">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="3dd9e-133">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-133">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="3dd9e-134">确认是否存在 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog)，或将 RID 添加到包含目标框架的 `<PropertyGroup>` 中。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-134">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="3dd9e-135">通过将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true` 来禁止创建 web.config文件。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-135">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="3dd9e-136">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-136">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="3dd9e-137">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-137">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="3dd9e-138">使用属性名称 `<RuntimeIdentifiers>`（复数）。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-138">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="3dd9e-139">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-139">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="3dd9e-140">为 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-140">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="3dd9e-141">若要启用 Windows 事件日志记录，请添加 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-141">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="3dd9e-142">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-142">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="3dd9e-143">Program.Main 更新</span><span class="sxs-lookup"><span data-stu-id="3dd9e-143">Program.Main updates</span></span>

<span data-ttu-id="3dd9e-144">在 `Program.Main` 中，进行下列更改：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-144">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="3dd9e-145">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-145">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="3dd9e-146">检查是否已连接调试器或是否存在 `--console` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-146">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="3dd9e-147">如果其中一个条件为 true（应用不作为服务运行），请在 Web 主机上调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-147">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="3dd9e-148">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-148">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="3dd9e-149">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-149">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="3dd9e-150">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-150">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="3dd9e-151">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-151">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="3dd9e-152">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-152">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="3dd9e-153">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-153">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="3dd9e-154">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-154">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="3dd9e-155">使用 appsettings.Production.json文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-155">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="3dd9e-156">出于演示和测试目的，示例应用的生产设置文件会将日志记录级别设置为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-156">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="3dd9e-157">在生产中，通常会将值设置为 `Error`。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-157">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="3dd9e-158">有关更多信息，请参见<xref:fundamentals/logging/index#windows-eventlog-provider>。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-158">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="publish-the-app"></a><span data-ttu-id="3dd9e-159">发布应用</span><span class="sxs-lookup"><span data-stu-id="3dd9e-159">Publish the app</span></span>

<span data-ttu-id="3dd9e-160">使用 [dotnet publish](/dotnet/articles/core/tools/dotnet-publish)、[Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles) 或 Visual Studio Code 发布应用。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-160">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="3dd9e-161">使用 Visual Studio 时，先选择“FolderProfile”并配置“目标位置”，再选择“发布”按钮。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-161">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="3dd9e-162">若要使用命令行接口 (CLI) 工具发布示例应用，请在项目文件夹的 Windows 命令行界面中运行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令，并将发布配置传递到 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 选项。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-162">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command in a Windows command shell from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="3dd9e-163">使用带有路径的 [-o|--output](/dotnet/core/tools/dotnet-publish#options) 选项发布到应用之外的文件夹。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-163">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="3dd9e-164">发布依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-164">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="3dd9e-165">在以下示例中，将应用发布到 *c:\\svc* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-165">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="3dd9e-166">发布独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-166">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="3dd9e-167">必须在项目文件的 `<RuntimeIdenfifier>`（或 `<RuntimeIdentifiers>`）属性中指定 RID。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-167">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="3dd9e-168">将运行时提供到 `dotnet publish` 命令的 [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) 选项。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-168">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="3dd9e-169">在以下示例中，为 `win7-x64` 运行时发布应用并发布到 c:\\svc 文件夹：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-169">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

## <a name="create-a-user-account"></a><span data-ttu-id="3dd9e-170">创建用户帐户</span><span class="sxs-lookup"><span data-stu-id="3dd9e-170">Create a user account</span></span>

<span data-ttu-id="3dd9e-171">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-171">Create a user account for the service using the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell:</span></span>

```powershell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="3dd9e-172">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-172">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="3dd9e-173">对于示例应用，创建名为 `ServiceUser` 的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-173">For the sample app, create a user account with the name `ServiceUser`.</span></span>

```powershell
New-LocalUser -Name ServiceUser
```

<span data-ttu-id="3dd9e-174">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-174">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="3dd9e-175">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-175">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="3dd9e-176">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-176">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="3dd9e-177">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-177">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="set-permission-log-on-as-a-service"></a><span data-ttu-id="3dd9e-178">设置权限：以服务身份登录</span><span class="sxs-lookup"><span data-stu-id="3dd9e-178">Set permission: Log on as a service</span></span>

<span data-ttu-id="3dd9e-179">在管理 PowerShell 6 命令行界面中运行 [icacls](/windows-server/administration/windows-commands/icacls) 命令，授予对应用文件夹的写入/读取/执行权限。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-179">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command an administrative PowerShell 6 command shell.</span></span>

```powershell
icacls "{PATH}" /grant "{USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS}" /t
```

* `{PATH}` <span data-ttu-id="3dd9e-180">&ndash; 应用文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-180">&ndash; Path to the app's folder.</span></span>
* `{USER ACCOUNT}` <span data-ttu-id="3dd9e-181">&ndash; 用户帐户 (SID)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-181">&ndash; The user account (SID).</span></span>
* `(OI)` <span data-ttu-id="3dd9e-182">&ndash; 对象继承标志将权限传播给从属文件。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-182">&ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* `(CI)` <span data-ttu-id="3dd9e-183">&ndash; 容器继承标志将权限传播给从属文件夹。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-183">&ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* `{PERMISSION FLAGS}` <span data-ttu-id="3dd9e-184">&ndash; 设置应用的访问权限。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-184">&ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="3dd9e-185">写入 (`W`)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-185">Write (`W`)</span></span>
  * <span data-ttu-id="3dd9e-186">读取 (`R`)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-186">Read (`R`)</span></span>
  * <span data-ttu-id="3dd9e-187">执行 (`X`)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-187">Execute (`X`)</span></span>
  * <span data-ttu-id="3dd9e-188">完全 (`F`)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-188">Full (`F`)</span></span>
  * <span data-ttu-id="3dd9e-189">修改 (`M`)</span><span class="sxs-lookup"><span data-stu-id="3dd9e-189">Modify (`M`)</span></span>
* `/t` <span data-ttu-id="3dd9e-190">&ndash; 以递归方式应用于现有从属文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-190">&ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="3dd9e-191">对于发布到 c:\\sv 文件夹的示例应用，以及拥有写入/读取/执行权限的 `ServiceUser` 帐户，请在管理 PowerShell 6 命令行界面中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-191">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command an administrative PowerShell 6 command shell.</span></span>

```powershell
icacls "c:\svc" /grant "ServiceUser:(OI)(CI)WRX" /t
```

<span data-ttu-id="3dd9e-192">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls)。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-192">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="create-the-service"></a><span data-ttu-id="3dd9e-193">创建服务</span><span class="sxs-lookup"><span data-stu-id="3dd9e-193">Create the service</span></span>

<span data-ttu-id="3dd9e-194">使用 [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) PowerShell 脚本来注册服务。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-194">Use the [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) PowerShell script to register the service.</span></span> <span data-ttu-id="3dd9e-195">在管理 PowerShell 6 命令行界面中，使用以下命令执行脚本：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-195">From an administrative PowerShell 6 command shell, execute the script with the following command:</span></span>

```powershell
.\RegisterService.ps1 
    -Name {NAME} 
    -DisplayName "{DISPLAY NAME}" 
    -Description "{DESCRIPTION}" 
    -Exe "{PATH TO EXE}\{ASSEMBLY NAME}.exe" 
    -User {DOMAIN\USER}
```

<span data-ttu-id="3dd9e-196">在下面的示例应用示例中：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-196">In the following example for the sample app:</span></span>

* <span data-ttu-id="3dd9e-197">服务名为 MyService。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-197">The service is named **MyService**.</span></span>
* <span data-ttu-id="3dd9e-198">已发布的服务驻留在 c:\\svc 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-198">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="3dd9e-199">应用可执行文件名为 SampleApp.exe。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-199">The app executable is named *SampleApp.exe*.</span></span>
* <span data-ttu-id="3dd9e-200">服务是使用 `ServiceUser` 帐户运行。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-200">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="3dd9e-201">在下面的示例命令中，本地计算机名为 `Desktop-PC`。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-201">In the following example command, the local machine name is `Desktop-PC`.</span></span> <span data-ttu-id="3dd9e-202">将 `Desktop-PC` 替换为系统的计算机名或域。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-202">Replace `Desktop-PC` with the computer name or domain for your system.</span></span>

```powershell
.\RegisterService.ps1 
    -Name MyService 
    -DisplayName "My Cool Service" 
    -Description "This is the Sample App service." 
    -Exe "c:\svc\SampleApp.exe" 
    -User Desktop-PC\ServiceUser
```

## <a name="manage-the-service"></a><span data-ttu-id="3dd9e-203">管理服务</span><span class="sxs-lookup"><span data-stu-id="3dd9e-203">Manage the service</span></span>

### <a name="start-the-service"></a><span data-ttu-id="3dd9e-204">启动服务</span><span class="sxs-lookup"><span data-stu-id="3dd9e-204">Start the service</span></span>

<span data-ttu-id="3dd9e-205">使用 `Start-Service -Name {NAME}` PowerShell 6 命令启动服务。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-205">Start the service with the `Start-Service -Name {NAME}` PowerShell 6 command.</span></span>

<span data-ttu-id="3dd9e-206">要启动示例应用服务，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-206">To start the sample app service, use the following command:</span></span>

```powershell
Start-Service -Name MyService
```

<span data-ttu-id="3dd9e-207">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-207">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="3dd9e-208">确定服务状态</span><span class="sxs-lookup"><span data-stu-id="3dd9e-208">Determine the service status</span></span>

<span data-ttu-id="3dd9e-209">要检查服务的状态，请使用 `Get-Service -Name {NAME}` PowerShell 6 命令。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-209">To check the status of the service, use the `Get-Service -Name {NAME}` PowerShell 6 command.</span></span> <span data-ttu-id="3dd9e-210">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-210">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

<span data-ttu-id="3dd9e-211">使用以下命令检查示例应用服务的状态：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-211">Use the following command to check the status of the sample app service:</span></span>

```powershell
Get-Service -Name MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="3dd9e-212">浏览 Web 应用服务</span><span class="sxs-lookup"><span data-stu-id="3dd9e-212">Browse a web app service</span></span>

<span data-ttu-id="3dd9e-213">当服务处于 `RUNNING` 状态并且服务是 Web 应用时，在应用所在路径中浏览应用（默认路径为 `http://localhost:5000`；在使用 [HTTPS 重定向中间件](xref:security/enforcing-ssl)时，它将重定向到 `https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-213">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="3dd9e-214">对于示例应用服务，请在 `http://localhost:5000` 浏览应用。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-214">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="3dd9e-215">停止服务</span><span class="sxs-lookup"><span data-stu-id="3dd9e-215">Stop the service</span></span>

<span data-ttu-id="3dd9e-216">使用 `Stop-Service -Name {NAME}` Powershell 6 命令停止服务。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-216">Stop the service with the `Stop-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="3dd9e-217">以下命令可停止示例应用服务：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-217">The following command stops the sample app service:</span></span>

```powershell
Stop-Service -Name MyService
```

### <a name="remove-the-service"></a><span data-ttu-id="3dd9e-218">删除服务</span><span class="sxs-lookup"><span data-stu-id="3dd9e-218">Remove the service</span></span>

<span data-ttu-id="3dd9e-219">在停止服务一小段时间后，使用 `Remove-Service -Name {NAME}` Powershell 6 命令删除服务。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-219">After a short delay to stop a service, remove the service with the `Remove-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="3dd9e-220">检查示例应用服务的状态：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-220">Check the status of the sample app service:</span></span>

```powershell
Remove-Service -Name MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="3dd9e-221">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="3dd9e-221">Handle starting and stopping events</span></span>

<span data-ttu-id="3dd9e-222">若要处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件，请执行以下其他更改：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-222">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="3dd9e-223">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-223">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="3dd9e-224">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-224">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="3dd9e-225">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-225">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="3dd9e-226">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[将项目转换为 Windows 服务](#convert-a-project-into-a-windows-service)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-226">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="3dd9e-227">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="3dd9e-227">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="3dd9e-228">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-228">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="3dd9e-229">有关更多信息，请参见<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-229">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="3dd9e-230">配置 HTTPS</span><span class="sxs-lookup"><span data-stu-id="3dd9e-230">Configure HTTPS</span></span>

<span data-ttu-id="3dd9e-231">若要为服务配置安全终结点，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-231">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="3dd9e-232">使用平台的证书获取和部署机制，为托管系统创建 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-232">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="3dd9e-233">将 [Kestrel 服务器 HTTPS 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)指定为使用此证书。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-233">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="3dd9e-234">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-234">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="3dd9e-235">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="3dd9e-235">Current directory and content root</span></span>

<span data-ttu-id="3dd9e-236">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-236">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="3dd9e-237">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-237">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="3dd9e-238">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-238">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="3dd9e-239">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="3dd9e-239">Set the content root path to the app's folder</span></span>

<span data-ttu-id="3dd9e-240"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-240">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="3dd9e-241">请调用包含应用内容根的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-241">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="3dd9e-242">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="3dd9e-242">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="3dd9e-243">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="3dd9e-243">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="3dd9e-244">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3dd9e-244">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3dd9e-245">其他资源</span><span class="sxs-lookup"><span data-stu-id="3dd9e-245">Additional resources</span></span>

* <span data-ttu-id="3dd9e-246">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="3dd9e-246">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
