---
title: 在 Windows 服务中托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows 服务中托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/08/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: ecc7f3a8cd813c2803d03294e38d726905eeb1b8
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841418"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="cad05-103">在 Windows 服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cad05-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="cad05-104">作者：[Luke Latham](https://github.com/guardrex)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="cad05-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="cad05-105">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="cad05-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="cad05-106">作为 Windows 服务进行托管时，应用将在重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="cad05-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="cad05-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cad05-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cad05-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="cad05-108">Prerequisites</span></span>

* [<span data-ttu-id="cad05-109">PowerShell 6</span><span class="sxs-lookup"><span data-stu-id="cad05-109">PowerShell 6</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="deployment-type"></a><span data-ttu-id="cad05-110">部署类型</span><span class="sxs-lookup"><span data-stu-id="cad05-110">Deployment type</span></span>

<span data-ttu-id="cad05-111">可以创建依赖框架的 Windows 服务部署或独立的 Windows 服务部署。</span><span class="sxs-lookup"><span data-stu-id="cad05-111">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="cad05-112">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="cad05-112">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="cad05-113">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="cad05-113">Framework-dependent deployment</span></span>

<span data-ttu-id="cad05-114">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="cad05-114">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="cad05-115">当 FDD 方案用于 ASP.NET Core Windows 服务应用时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (*\*.exe*)。</span><span class="sxs-lookup"><span data-stu-id="cad05-115">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="cad05-116">独立部署</span><span class="sxs-lookup"><span data-stu-id="cad05-116">Self-contained deployment</span></span>

<span data-ttu-id="cad05-117">独立部署 (SCD) 不依赖目标系统上存在的共享组件。</span><span class="sxs-lookup"><span data-stu-id="cad05-117">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="cad05-118">运行时和应用的依赖项将与应用一起部署到托管系统。</span><span class="sxs-lookup"><span data-stu-id="cad05-118">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="cad05-119">将项目转换为 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="cad05-119">Convert a project into a Windows Service</span></span>

<span data-ttu-id="cad05-120">对现有 ASP.NET Core 项目进行以下更改，以将应用作为服务运行：</span><span class="sxs-lookup"><span data-stu-id="cad05-120">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="cad05-121">项目文件更新</span><span class="sxs-lookup"><span data-stu-id="cad05-121">Project file updates</span></span>

<span data-ttu-id="cad05-122">根据所选的[部署类型](#deployment-type)，更新项目文件：</span><span class="sxs-lookup"><span data-stu-id="cad05-122">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="cad05-123">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="cad05-123">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="cad05-124">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中。</span><span class="sxs-lookup"><span data-stu-id="cad05-124">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="cad05-125">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="cad05-125">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="cad05-126">将 `<SelfContained>` 属性集添加到 `false`。</span><span class="sxs-lookup"><span data-stu-id="cad05-126">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="cad05-127">这些属性指示 SDK 为 Windows 生成可执行 (.exe) 文件。</span><span class="sxs-lookup"><span data-stu-id="cad05-127">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="cad05-128">web.config文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="cad05-128">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="cad05-129">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="cad05-129">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="cad05-130">将 `<UseAppHost>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="cad05-130">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="cad05-131">此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe）。</span><span class="sxs-lookup"><span data-stu-id="cad05-131">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="cad05-132">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="cad05-132">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="cad05-133">确认是否存在 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog)，或将 RID 添加到包含目标框架的 `<PropertyGroup>` 中。</span><span class="sxs-lookup"><span data-stu-id="cad05-133">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="cad05-134">通过将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true` 来禁止创建 web.config文件。</span><span class="sxs-lookup"><span data-stu-id="cad05-134">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="cad05-135">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="cad05-135">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="cad05-136">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="cad05-136">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="cad05-137">使用属性名称 `<RuntimeIdentifiers>`（复数）。</span><span class="sxs-lookup"><span data-stu-id="cad05-137">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="cad05-138">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="cad05-138">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="cad05-139">为 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="cad05-139">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="cad05-140">若要启用 Windows 事件日志记录，请添加 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="cad05-140">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="cad05-141">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="cad05-141">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="cad05-142">Program.Main 更新</span><span class="sxs-lookup"><span data-stu-id="cad05-142">Program.Main updates</span></span>

<span data-ttu-id="cad05-143">在 `Program.Main` 中，进行下列更改：</span><span class="sxs-lookup"><span data-stu-id="cad05-143">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="cad05-144">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="cad05-144">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="cad05-145">检查是否已连接调试器或是否存在 `--console` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="cad05-145">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="cad05-146">如果其中一个条件为 true（应用不作为服务运行），请在 Web 主机上调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="cad05-146">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="cad05-147">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="cad05-147">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="cad05-148">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="cad05-148">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="cad05-149">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="cad05-149">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="cad05-150">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="cad05-150">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="cad05-151">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="cad05-151">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="cad05-152">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="cad05-152">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="cad05-153">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="cad05-153">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="cad05-154">使用 appsettings.Production.json文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="cad05-154">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="cad05-155">出于演示和测试目的，示例应用的生产设置文件会将日志记录级别设置为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="cad05-155">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="cad05-156">在生产中，通常会将值设置为 `Error`。</span><span class="sxs-lookup"><span data-stu-id="cad05-156">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="cad05-157">有关更多信息，请参见<xref:fundamentals/logging/index#windows-eventlog-provider>。</span><span class="sxs-lookup"><span data-stu-id="cad05-157">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="publish-the-app"></a><span data-ttu-id="cad05-158">发布应用</span><span class="sxs-lookup"><span data-stu-id="cad05-158">Publish the app</span></span>

<span data-ttu-id="cad05-159">使用 [dotnet publish](/dotnet/articles/core/tools/dotnet-publish)、[Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles) 或 Visual Studio Code 发布应用。</span><span class="sxs-lookup"><span data-stu-id="cad05-159">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="cad05-160">使用 Visual Studio 时，先选择“FolderProfile”并配置“目标位置”，再选择“发布”按钮。</span><span class="sxs-lookup"><span data-stu-id="cad05-160">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="cad05-161">若要使用命令行接口 (CLI) 工具发布示例应用，请在项目文件夹的命令提示符处运行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令，并将发布配置传递到 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 选项。</span><span class="sxs-lookup"><span data-stu-id="cad05-161">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="cad05-162">使用带有路径的 [-o|--output](/dotnet/core/tools/dotnet-publish#options) 选项发布到应用之外的文件夹。</span><span class="sxs-lookup"><span data-stu-id="cad05-162">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="cad05-163">发布依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="cad05-163">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="cad05-164">在以下示例中，将应用发布到 *c:\\svc* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="cad05-164">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="cad05-165">发布独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="cad05-165">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="cad05-166">必须在项目文件的 `<RuntimeIdenfifier>`（或 `<RuntimeIdentifiers>`）属性中指定 RID。</span><span class="sxs-lookup"><span data-stu-id="cad05-166">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="cad05-167">将运行时提供到 `dotnet publish` 命令的 [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) 选项。</span><span class="sxs-lookup"><span data-stu-id="cad05-167">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="cad05-168">在以下示例中，为 `win7-x64` 运行时发布应用并发布到 c:\\svc 文件夹：</span><span class="sxs-lookup"><span data-stu-id="cad05-168">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

## <a name="create-a-user-account"></a><span data-ttu-id="cad05-169">创建用户帐户</span><span class="sxs-lookup"><span data-stu-id="cad05-169">Create a user account</span></span>

<span data-ttu-id="cad05-170">使用管理 PowerShell 6 命令 shell 中的 `net user` 命令为服务创建用户帐户：</span><span class="sxs-lookup"><span data-stu-id="cad05-170">Create a user account for the service using the `net user` command from an administrative PowerShell 6 command shell:</span></span>

```powershell
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="cad05-171">默认密码有效期为 6 个星期。</span><span class="sxs-lookup"><span data-stu-id="cad05-171">The default password expiration is six weeks.</span></span>

<span data-ttu-id="cad05-172">对于示例应用，使用用户名 `ServiceUser` 和密码创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="cad05-172">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="cad05-173">在下面的命令中，将 `{PASSWORD}` 替换为[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="cad05-173">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```powershell
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="cad05-174">如果需要将用户添加到组中，请运行 `net localgroup` 命令（其中 `{GROUP}` 是组名称）：</span><span class="sxs-lookup"><span data-stu-id="cad05-174">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```powershell
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="cad05-175">有关详细信息，请参阅[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="cad05-175">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="cad05-176">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="cad05-176">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="cad05-177">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="cad05-177">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="set-permission-log-on-as-a-service"></a><span data-ttu-id="cad05-178">设置权限：以服务身份登录</span><span class="sxs-lookup"><span data-stu-id="cad05-178">Set permission: Log on as a service</span></span>

<span data-ttu-id="cad05-179">运行 [icacls](/windows-server/administration/windows-commands/icacls) 命令，授予对应用文件夹的写入/读取/执行权限：</span><span class="sxs-lookup"><span data-stu-id="cad05-179">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command:</span></span>

```powershell
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="cad05-180">`{PATH}` &ndash; 应用文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="cad05-180">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="cad05-181">`{USER ACCOUNT}` &ndash; 用户帐户 (SID)。</span><span class="sxs-lookup"><span data-stu-id="cad05-181">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="cad05-182">`(OI)` &ndash; 对象继承标志将权限传播给从属文件。</span><span class="sxs-lookup"><span data-stu-id="cad05-182">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="cad05-183">`(CI)` &ndash; 容器继承标志将权限传播给从属文件夹。</span><span class="sxs-lookup"><span data-stu-id="cad05-183">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="cad05-184">`{PERMISSION FLAGS}` &ndash; 设置应用的访问权限。</span><span class="sxs-lookup"><span data-stu-id="cad05-184">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="cad05-185">写入 (`W`)</span><span class="sxs-lookup"><span data-stu-id="cad05-185">Write (`W`)</span></span>
  * <span data-ttu-id="cad05-186">读取 (`R`)</span><span class="sxs-lookup"><span data-stu-id="cad05-186">Read (`R`)</span></span>
  * <span data-ttu-id="cad05-187">执行 (`X`)</span><span class="sxs-lookup"><span data-stu-id="cad05-187">Execute (`X`)</span></span>
  * <span data-ttu-id="cad05-188">完全 (`F`)</span><span class="sxs-lookup"><span data-stu-id="cad05-188">Full (`F`)</span></span>
  * <span data-ttu-id="cad05-189">修改 (`M`)</span><span class="sxs-lookup"><span data-stu-id="cad05-189">Modify (`M`)</span></span>
* <span data-ttu-id="cad05-190">`/t` &ndash; 以递归方式应用于现有从属文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="cad05-190">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="cad05-191">对于发布到 c:\\sv 文件夹的示例应用，以及拥有写入/读取/执行权限的 `ServiceUser` 帐户，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cad05-191">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```powershell
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="cad05-192">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls)。</span><span class="sxs-lookup"><span data-stu-id="cad05-192">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="create-the-service"></a><span data-ttu-id="cad05-193">创建服务</span><span class="sxs-lookup"><span data-stu-id="cad05-193">Create the service</span></span>

<span data-ttu-id="cad05-194">使用 [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) PowerShell 脚本来注册服务。</span><span class="sxs-lookup"><span data-stu-id="cad05-194">Use the [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) PowerShell script to register the service.</span></span> <span data-ttu-id="cad05-195">从 PowerShell 6 命令提示符处，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cad05-195">From an administrative PowerShell 6 command prompt, execute the following command:</span></span>

```powershell
.\RegisterService.ps1 
    -Name {NAME} 
    -DisplayName "{DISPLAY NAME}" 
    -Description "{DESCRIPTION}" 
    -Path "{PATH}" 
    -Exe {ASSEMBLY}.exe 
    -User {DOMAIN\USER}
```

<span data-ttu-id="cad05-196">在下面的示例应用示例中：</span><span class="sxs-lookup"><span data-stu-id="cad05-196">In the following example for the sample app:</span></span>

* <span data-ttu-id="cad05-197">服务名为 MyService。</span><span class="sxs-lookup"><span data-stu-id="cad05-197">The service is named **MyService**.</span></span>
* <span data-ttu-id="cad05-198">已发布的服务驻留在 c:\\svc 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="cad05-198">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="cad05-199">应用可执行文件名为 SampleApp.exe。</span><span class="sxs-lookup"><span data-stu-id="cad05-199">The app executable is named *SampleApp.exe*.</span></span>
* <span data-ttu-id="cad05-200">服务是使用 `ServiceUser` 帐户运行。</span><span class="sxs-lookup"><span data-stu-id="cad05-200">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="cad05-201">在下例中，本地计算机名称为 `Desktop-PC`。</span><span class="sxs-lookup"><span data-stu-id="cad05-201">In the following example, the local machine name is `Desktop-PC`.</span></span>

```powershell
.\RegisterService.ps1 
    -Name MyService 
    -DisplayName "My Cool Service" 
    -Description "This is the Sample App service." 
    -Path "c:\svc" 
    -Exe SampleApp.exe 
    -User Desktop-PC\ServiceUser
```

## <a name="manage-the-service"></a><span data-ttu-id="cad05-202">管理服务</span><span class="sxs-lookup"><span data-stu-id="cad05-202">Manage the service</span></span>

### <a name="start-the-service"></a><span data-ttu-id="cad05-203">启动服务</span><span class="sxs-lookup"><span data-stu-id="cad05-203">Start the service</span></span>

<span data-ttu-id="cad05-204">使用 `Start-Service -Name {NAME}` PowerShell 6 命令启动服务。</span><span class="sxs-lookup"><span data-stu-id="cad05-204">Start the service with the `Start-Service -Name {NAME}` PowerShell 6 command.</span></span>

<span data-ttu-id="cad05-205">要启动示例应用服务，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="cad05-205">To start the sample app service, use the following command:</span></span>

```powershell
Start-Service -Name MyService
```

<span data-ttu-id="cad05-206">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="cad05-206">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="cad05-207">确定服务状态</span><span class="sxs-lookup"><span data-stu-id="cad05-207">Determine the service status</span></span>

<span data-ttu-id="cad05-208">要检查服务的状态，请使用 `Get-Service -Name {NAME}` PowerShell 6 命令。</span><span class="sxs-lookup"><span data-stu-id="cad05-208">To check the status of the service, use the `Get-Service -Name {NAME}` PowerShell 6 command.</span></span> <span data-ttu-id="cad05-209">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="cad05-209">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

<span data-ttu-id="cad05-210">使用以下命令检查示例应用服务的状态：</span><span class="sxs-lookup"><span data-stu-id="cad05-210">Use the following command to check the status of the sample app service:</span></span>

```powershell
Get-Service -Name MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="cad05-211">浏览 Web 应用服务</span><span class="sxs-lookup"><span data-stu-id="cad05-211">Browse a web app service</span></span>

<span data-ttu-id="cad05-212">当服务处于 `RUNNING` 状态并且服务是 Web 应用时，在应用所在路径中浏览应用（默认路径为 `http://localhost:5000`；在使用 [HTTPS 重定向中间件](xref:security/enforcing-ssl)时，它将重定向到 `https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="cad05-212">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="cad05-213">对于示例应用服务，请在 `http://localhost:5000` 浏览应用。</span><span class="sxs-lookup"><span data-stu-id="cad05-213">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="cad05-214">停止服务</span><span class="sxs-lookup"><span data-stu-id="cad05-214">Stop the service</span></span>

<span data-ttu-id="cad05-215">使用 `Stop-Service -Name {NAME}` Powershell 6 命令停止服务。</span><span class="sxs-lookup"><span data-stu-id="cad05-215">Stop the service with the `Stop-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="cad05-216">以下命令可停止示例应用服务：</span><span class="sxs-lookup"><span data-stu-id="cad05-216">The following command stops the sample app service:</span></span>

```powershell
Stop-Service -Name MyService
```

### <a name="remove-the-service"></a><span data-ttu-id="cad05-217">删除服务</span><span class="sxs-lookup"><span data-stu-id="cad05-217">Remove the service</span></span>

<span data-ttu-id="cad05-218">在停止服务一小段时间后，使用 `Remove-Service -Name {NAME}` Powershell 6 命令删除服务。</span><span class="sxs-lookup"><span data-stu-id="cad05-218">After a short delay to stop a service, remove the service with the `Remove-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="cad05-219">检查示例应用服务的状态：</span><span class="sxs-lookup"><span data-stu-id="cad05-219">Check the status of the sample app service:</span></span>

```powershell
Remove-Service -Name MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="cad05-220">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="cad05-220">Handle starting and stopping events</span></span>

<span data-ttu-id="cad05-221">若要处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件，请执行以下其他更改：</span><span class="sxs-lookup"><span data-stu-id="cad05-221">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="cad05-222">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="cad05-222">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="cad05-223">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="cad05-223">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="cad05-224">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="cad05-224">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="cad05-225">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[将项目转换为 Windows 服务](#convert-a-project-into-a-windows-service)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="cad05-225">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="cad05-226">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="cad05-226">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="cad05-227">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="cad05-227">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="cad05-228">有关更多信息，请参见<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="cad05-228">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="cad05-229">配置 HTTPS</span><span class="sxs-lookup"><span data-stu-id="cad05-229">Configure HTTPS</span></span>

<span data-ttu-id="cad05-230">若要为服务配置安全终结点，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cad05-230">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="cad05-231">使用平台的证书获取和部署机制，为托管系统创建 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="cad05-231">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="cad05-232">将 [Kestrel 服务器 HTTPS 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)指定为使用此证书。</span><span class="sxs-lookup"><span data-stu-id="cad05-232">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="cad05-233">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="cad05-233">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="cad05-234">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="cad05-234">Current directory and content root</span></span>

<span data-ttu-id="cad05-235">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="cad05-235">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="cad05-236">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="cad05-236">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="cad05-237">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="cad05-237">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="cad05-238">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="cad05-238">Set the content root path to the app's folder</span></span>

<span data-ttu-id="cad05-239"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="cad05-239">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="cad05-240">请调用包含应用内容根的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="cad05-240">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="cad05-241">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="cad05-241">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="cad05-242">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="cad05-242">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="cad05-243">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="cad05-243">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cad05-244">其他资源</span><span class="sxs-lookup"><span data-stu-id="cad05-244">Additional resources</span></span>

* <span data-ttu-id="cad05-245">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="cad05-245">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
