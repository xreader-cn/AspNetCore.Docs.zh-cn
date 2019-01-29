---
title: 在 Windows 服务中托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows 服务中托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/22/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: eedaf64710506f2a2aac65c178a9888d2ab33d38
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837476"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="a183f-103">在 Windows 服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a183f-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="a183f-104">作者：[Luke Latham](https://github.com/guardrex)、[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="a183f-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="a183f-105">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="a183f-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="a183f-106">作为 Windows 服务进行托管时，应用将在重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="a183f-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="a183f-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a183f-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="deployment-type"></a><span data-ttu-id="a183f-108">部署类型</span><span class="sxs-lookup"><span data-stu-id="a183f-108">Deployment type</span></span>

<span data-ttu-id="a183f-109">可以创建依赖框架的 Windows 服务部署或独立的 Windows 服务部署。</span><span class="sxs-lookup"><span data-stu-id="a183f-109">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="a183f-110">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="a183f-110">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="a183f-111">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="a183f-111">Framework-dependent deployment</span></span>

<span data-ttu-id="a183f-112">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="a183f-112">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="a183f-113">当 FDD 方案用于 ASP.NET Core Windows 服务应用时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (*\*.exe*)。</span><span class="sxs-lookup"><span data-stu-id="a183f-113">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="a183f-114">独立部署</span><span class="sxs-lookup"><span data-stu-id="a183f-114">Self-contained deployment</span></span>

<span data-ttu-id="a183f-115">独立部署 (SCD) 不依赖目标系统上存在的共享组件。</span><span class="sxs-lookup"><span data-stu-id="a183f-115">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="a183f-116">运行时和应用的依赖项将与应用一起部署到托管系统。</span><span class="sxs-lookup"><span data-stu-id="a183f-116">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="a183f-117">将项目转换为 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="a183f-117">Convert a project into a Windows Service</span></span>

<span data-ttu-id="a183f-118">对现有 ASP.NET Core 项目进行以下更改，以将应用作为服务运行：</span><span class="sxs-lookup"><span data-stu-id="a183f-118">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="a183f-119">项目文件更新</span><span class="sxs-lookup"><span data-stu-id="a183f-119">Project file updates</span></span>

<span data-ttu-id="a183f-120">根据所选的[部署类型](#deployment-type)，更新项目文件：</span><span class="sxs-lookup"><span data-stu-id="a183f-120">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="a183f-121">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="a183f-121">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="a183f-122">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中。</span><span class="sxs-lookup"><span data-stu-id="a183f-122">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="a183f-123">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="a183f-123">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="a183f-124">将 `<SelfContained>` 属性集添加到 `false`。</span><span class="sxs-lookup"><span data-stu-id="a183f-124">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="a183f-125">这些属性指示 SDK 为 Windows 生成可执行 (.exe) 文件。</span><span class="sxs-lookup"><span data-stu-id="a183f-125">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="a183f-126">web.config文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="a183f-126">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="a183f-127">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="a183f-127">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="a183f-128">将 `<UseAppHost>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="a183f-128">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="a183f-129">此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe）。</span><span class="sxs-lookup"><span data-stu-id="a183f-129">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="a183f-130">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="a183f-130">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="a183f-131">确认是否存在 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog)，或将 RID 添加到包含目标框架的 `<PropertyGroup>` 中。</span><span class="sxs-lookup"><span data-stu-id="a183f-131">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="a183f-132">通过将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true` 来禁止创建 web.config文件。</span><span class="sxs-lookup"><span data-stu-id="a183f-132">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="a183f-133">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="a183f-133">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="a183f-134">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="a183f-134">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="a183f-135">使用属性名称 `<RuntimeIdentifiers>`（复数）。</span><span class="sxs-lookup"><span data-stu-id="a183f-135">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="a183f-136">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="a183f-136">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="a183f-137">为 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="a183f-137">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="a183f-138">若要启用 Windows 事件日志记录，请添加 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="a183f-138">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="a183f-139">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="a183f-139">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="a183f-140">Program.Main 更新</span><span class="sxs-lookup"><span data-stu-id="a183f-140">Program.Main updates</span></span>

<span data-ttu-id="a183f-141">在 `Program.Main` 中，进行下列更改：</span><span class="sxs-lookup"><span data-stu-id="a183f-141">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="a183f-142">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="a183f-142">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="a183f-143">检查是否已连接调试器或是否存在 `--console` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="a183f-143">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="a183f-144">如果其中一个条件为 true（应用不作为服务运行），请在 Web 主机上调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="a183f-144">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="a183f-145">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="a183f-145">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="a183f-146">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="a183f-146">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="a183f-147">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 `GetCurrentDirectory` 时，Windows 服务应用将返回 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="a183f-147">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when `GetCurrentDirectory` is called.</span></span> <span data-ttu-id="a183f-148">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="a183f-148">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="a183f-149">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="a183f-149">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="a183f-150">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="a183f-150">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="a183f-151">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="a183f-151">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="a183f-152">使用 appsettings.Production.json文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="a183f-152">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="a183f-153">出于演示和测试目的，示例应用的生产设置文件会将日志记录级别设置为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="a183f-153">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="a183f-154">在生产中，通常会将值设置为 `Error`。</span><span class="sxs-lookup"><span data-stu-id="a183f-154">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="a183f-155">有关更多信息，请参见<xref:fundamentals/logging/index#windows-eventlog-provider>。</span><span class="sxs-lookup"><span data-stu-id="a183f-155">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a><span data-ttu-id="a183f-156">发布应用</span><span class="sxs-lookup"><span data-stu-id="a183f-156">Publish the app</span></span>

<span data-ttu-id="a183f-157">使用 [dotnet publish](/dotnet/articles/core/tools/dotnet-publish)、[Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles) 或 Visual Studio Code 发布应用。</span><span class="sxs-lookup"><span data-stu-id="a183f-157">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="a183f-158">使用 Visual Studio 时，先选择“FolderProfile”并配置“目标位置”，再选择“发布”按钮。</span><span class="sxs-lookup"><span data-stu-id="a183f-158">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="a183f-159">若要使用命令行接口 (CLI) 工具发布示例应用，请在项目文件夹的命令提示符处运行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令，并将发布配置传递到 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 选项。</span><span class="sxs-lookup"><span data-stu-id="a183f-159">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="a183f-160">使用带有路径的 [-o|--output](/dotnet/core/tools/dotnet-publish#options) 选项发布到应用之外的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a183f-160">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

#### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="a183f-161">发布依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="a183f-161">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="a183f-162">在以下示例中，将应用发布到 *c:\\svc* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="a183f-162">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="a183f-163">发布独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="a183f-163">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="a183f-164">必须在项目文件的 `<RuntimeIdenfifier>`（或 `<RuntimeIdentifiers>`）属性中指定 RID。</span><span class="sxs-lookup"><span data-stu-id="a183f-164">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="a183f-165">将运行时提供到 `dotnet publish` 命令的 [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) 选项。</span><span class="sxs-lookup"><span data-stu-id="a183f-165">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="a183f-166">在以下示例中，为 `win7-x64` 运行时发布应用并发布到 c:\\svc 文件夹：</span><span class="sxs-lookup"><span data-stu-id="a183f-166">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a><span data-ttu-id="a183f-167">创建用户帐户</span><span class="sxs-lookup"><span data-stu-id="a183f-167">Create a user account</span></span>

<span data-ttu-id="a183f-168">运行 `net user` 命令，创建服务用户帐户：</span><span class="sxs-lookup"><span data-stu-id="a183f-168">Create a user account for the service using the `net user` command:</span></span>

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="a183f-169">对于示例应用，使用用户名 `ServiceUser` 和密码创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="a183f-169">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="a183f-170">在下面的命令中，将 `{PASSWORD}` 替换为[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="a183f-170">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```console
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="a183f-171">如果需要将用户添加到组中，请运行 `net localgroup` 命令（其中 `{GROUP}` 是组名称）：</span><span class="sxs-lookup"><span data-stu-id="a183f-171">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="a183f-172">有关详细信息，请参阅[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="a183f-172">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

### <a name="set-permissions"></a><span data-ttu-id="a183f-173">设置权限</span><span class="sxs-lookup"><span data-stu-id="a183f-173">Set permissions</span></span>

<span data-ttu-id="a183f-174">运行 [icacls](/windows-server/administration/windows-commands/icacls) 命令，授予对应用文件夹的写入/读取/执行权限：</span><span class="sxs-lookup"><span data-stu-id="a183f-174">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command:</span></span>

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="a183f-175">`{PATH}` &ndash; 应用文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="a183f-175">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="a183f-176">`{USER ACCOUNT}` &ndash; 用户帐户 (SID)。</span><span class="sxs-lookup"><span data-stu-id="a183f-176">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="a183f-177">`(OI)` &ndash; 对象继承标志将权限传播给从属文件。</span><span class="sxs-lookup"><span data-stu-id="a183f-177">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="a183f-178">`(CI)` &ndash; 容器继承标志将权限传播给从属文件夹。</span><span class="sxs-lookup"><span data-stu-id="a183f-178">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="a183f-179">`{PERMISSION FLAGS}` &ndash; 设置应用的访问权限。</span><span class="sxs-lookup"><span data-stu-id="a183f-179">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="a183f-180">写入 (`W`)</span><span class="sxs-lookup"><span data-stu-id="a183f-180">Write (`W`)</span></span>
  * <span data-ttu-id="a183f-181">读取 (`R`)</span><span class="sxs-lookup"><span data-stu-id="a183f-181">Read (`R`)</span></span>
  * <span data-ttu-id="a183f-182">执行 (`X`)</span><span class="sxs-lookup"><span data-stu-id="a183f-182">Execute (`X`)</span></span>
  * <span data-ttu-id="a183f-183">完全 (`F`)</span><span class="sxs-lookup"><span data-stu-id="a183f-183">Full (`F`)</span></span>
  * <span data-ttu-id="a183f-184">修改 (`M`)</span><span class="sxs-lookup"><span data-stu-id="a183f-184">Modify (`M`)</span></span>
* <span data-ttu-id="a183f-185">`/t` &ndash; 以递归方式应用于现有从属文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="a183f-185">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="a183f-186">对于发布到 c:\\sv 文件夹的示例应用，以及拥有写入/读取/执行权限的 `ServiceUser` 帐户，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a183f-186">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="a183f-187">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls)。</span><span class="sxs-lookup"><span data-stu-id="a183f-187">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="manage-the-service"></a><span data-ttu-id="a183f-188">管理服务</span><span class="sxs-lookup"><span data-stu-id="a183f-188">Manage the service</span></span>

### <a name="create-the-service"></a><span data-ttu-id="a183f-189">创建服务</span><span class="sxs-lookup"><span data-stu-id="a183f-189">Create the service</span></span>

<span data-ttu-id="a183f-190">使用 [sc.exe](https://technet.microsoft.com/library/bb490995) 命令行工具创建服务。</span><span class="sxs-lookup"><span data-stu-id="a183f-190">Use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create the service.</span></span> <span data-ttu-id="a183f-191">`binPath` 值是应用的可执行文件的路径，其中包括可执行文件的文件名。</span><span class="sxs-lookup"><span data-stu-id="a183f-191">The `binPath` value is the path to the app's executable, which includes the executable file name.</span></span> <span data-ttu-id="a183f-192">每个参数和值的等于号和引号字符之间必须有空格。</span><span class="sxs-lookup"><span data-stu-id="a183f-192">**The space between the equal sign and the quote character of each parameter and value is required.**</span></span>

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* <span data-ttu-id="a183f-193">`{SERVICE NAME}` &ndash; 要在[服务控制管理器](/windows/desktop/services/service-control-manager)中分配给服务的名称。</span><span class="sxs-lookup"><span data-stu-id="a183f-193">`{SERVICE NAME}` &ndash; The name to assign to the service in [Service Control Manager](/windows/desktop/services/service-control-manager).</span></span>
* <span data-ttu-id="a183f-194">`{PATH}` &ndash; 可执行服务的路径。</span><span class="sxs-lookup"><span data-stu-id="a183f-194">`{PATH}` &ndash; The path to the service executable.</span></span>
* <span data-ttu-id="a183f-195">`{DOMAIN}` &ndash; 已加入域的计算机的域。</span><span class="sxs-lookup"><span data-stu-id="a183f-195">`{DOMAIN}` &ndash; The domain of a domain-joined machine.</span></span> <span data-ttu-id="a183f-196">如果计算机未加入域，则为本地计算机名称。</span><span class="sxs-lookup"><span data-stu-id="a183f-196">If the machine isn't domain-joined, the local machine name.</span></span>
* <span data-ttu-id="a183f-197">`{USER ACCOUNT}` &ndash; 运行服务所使用的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="a183f-197">`{USER ACCOUNT}` &ndash; The user account under which the service runs.</span></span>
* <span data-ttu-id="a183f-198">`{PASSWORD}` &ndash; 用户帐户密码。</span><span class="sxs-lookup"><span data-stu-id="a183f-198">`{PASSWORD}` &ndash; The user account password.</span></span>

> [!WARNING]
> <span data-ttu-id="a183f-199">请勿省略 `obj` 参数。</span><span class="sxs-lookup"><span data-stu-id="a183f-199">Do **not** omit the `obj` parameter.</span></span> <span data-ttu-id="a183f-200">`obj` 的默认值为 [LocalSystem 帐户](/windows/desktop/services/localsystem-account)。</span><span class="sxs-lookup"><span data-stu-id="a183f-200">The default value for `obj` is the [LocalSystem account](/windows/desktop/services/localsystem-account) account.</span></span> <span data-ttu-id="a183f-201">使用 `LocalSystem` 帐户运行服务会带来重大安全风险。</span><span class="sxs-lookup"><span data-stu-id="a183f-201">Running a service under the `LocalSystem` account presents a significant security risk.</span></span> <span data-ttu-id="a183f-202">请始终使用拥有有限权限的用户帐户运行服务。</span><span class="sxs-lookup"><span data-stu-id="a183f-202">Always run a service with a user account that has restricted privileges.</span></span>

<span data-ttu-id="a183f-203">在下面的示例应用示例中：</span><span class="sxs-lookup"><span data-stu-id="a183f-203">In the following example for the sample app:</span></span>

* <span data-ttu-id="a183f-204">服务名为 MyService。</span><span class="sxs-lookup"><span data-stu-id="a183f-204">The service is named **MyService**.</span></span>
* <span data-ttu-id="a183f-205">已发布的服务驻留在 c:\\svc 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="a183f-205">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="a183f-206">应用可执行文件名为 SampleApp.exe。</span><span class="sxs-lookup"><span data-stu-id="a183f-206">The app executable is named *SampleApp.exe*.</span></span> <span data-ttu-id="a183f-207">用双引号 (") 将 `binPath` 值引起来。</span><span class="sxs-lookup"><span data-stu-id="a183f-207">Enclose the `binPath` value in double quotation marks (").</span></span>
* <span data-ttu-id="a183f-208">服务是使用 `ServiceUser` 帐户运行。</span><span class="sxs-lookup"><span data-stu-id="a183f-208">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="a183f-209">将 `{DOMAIN}` 替换为用户帐户的域或本地计算机名称。</span><span class="sxs-lookup"><span data-stu-id="a183f-209">Replace `{DOMAIN}` with the user account's domain or local machine name.</span></span> <span data-ttu-id="a183f-210">用双引号 (") 将 `obj` 值引起来。</span><span class="sxs-lookup"><span data-stu-id="a183f-210">Enclose the `obj` value in double quotation marks (").</span></span> <span data-ttu-id="a183f-211">示例:如果托管系统是名为 `MairaPC` 的本地计算机，请将 `obj` 设置为 `"MairaPC\ServiceUser"`。</span><span class="sxs-lookup"><span data-stu-id="a183f-211">Example: If the hosting system is a local machine named `MairaPC`, set `obj` to `"MairaPC\ServiceUser"`.</span></span>
* <span data-ttu-id="a183f-212">将 `{PASSWORD}` 替换为用户帐户密码。</span><span class="sxs-lookup"><span data-stu-id="a183f-212">Replace `{PASSWORD}` with the user account's password.</span></span> <span data-ttu-id="a183f-213">用双引号 (") 将 `password` 值引起来。</span><span class="sxs-lookup"><span data-stu-id="a183f-213">Enclose the `password` value in double quotation marks (").</span></span>

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> <span data-ttu-id="a183f-214">请确保参数的等于号和参数值之间有空格。</span><span class="sxs-lookup"><span data-stu-id="a183f-214">Make sure that the spaces between the parameters' equal signs and the parameters' values are present.</span></span>

### <a name="start-the-service"></a><span data-ttu-id="a183f-215">启动服务</span><span class="sxs-lookup"><span data-stu-id="a183f-215">Start the service</span></span>

<span data-ttu-id="a183f-216">使用 `sc start {SERVICE NAME}` 命令启动服务。</span><span class="sxs-lookup"><span data-stu-id="a183f-216">Start the service with the `sc start {SERVICE NAME}` command.</span></span>

<span data-ttu-id="a183f-217">要启动示例应用服务，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="a183f-217">To start the sample app service, use the following command:</span></span>

```console
sc start MyService
```

<span data-ttu-id="a183f-218">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="a183f-218">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="a183f-219">确定服务状态</span><span class="sxs-lookup"><span data-stu-id="a183f-219">Determine the service status</span></span>

<span data-ttu-id="a183f-220">要检查服务的状态，请使用 `sc query {SERVICE NAME}` 命令。</span><span class="sxs-lookup"><span data-stu-id="a183f-220">To check the status of the service, use the `sc query {SERVICE NAME}` command.</span></span> <span data-ttu-id="a183f-221">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="a183f-221">The status is reported as one of the following values:</span></span>

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

<span data-ttu-id="a183f-222">使用以下命令检查示例应用服务的状态：</span><span class="sxs-lookup"><span data-stu-id="a183f-222">Use the following command to check the status of the sample app service:</span></span>

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="a183f-223">浏览 Web 应用服务</span><span class="sxs-lookup"><span data-stu-id="a183f-223">Browse a web app service</span></span>

<span data-ttu-id="a183f-224">当服务处于 `RUNNING` 状态并且服务是 Web 应用时，在应用所在路径中浏览应用（默认路径为 `http://localhost:5000`；在使用 [HTTPS 重定向中间件](xref:security/enforcing-ssl)时，它将重定向到 `https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="a183f-224">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="a183f-225">对于示例应用服务，请在 `http://localhost:5000` 浏览应用。</span><span class="sxs-lookup"><span data-stu-id="a183f-225">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="a183f-226">停止服务</span><span class="sxs-lookup"><span data-stu-id="a183f-226">Stop the service</span></span>

<span data-ttu-id="a183f-227">使用 `sc stop {SERVICE NAME}` 命令停止服务。</span><span class="sxs-lookup"><span data-stu-id="a183f-227">Stop the service with the `sc stop {SERVICE NAME}` command.</span></span>

<span data-ttu-id="a183f-228">以下命令可停止示例应用服务：</span><span class="sxs-lookup"><span data-stu-id="a183f-228">The following command stops the sample app service:</span></span>

```console
sc stop MyService
```

### <a name="delete-the-service"></a><span data-ttu-id="a183f-229">删除服务</span><span class="sxs-lookup"><span data-stu-id="a183f-229">Delete the service</span></span>

<span data-ttu-id="a183f-230">停止服务并经过短暂延迟后，使用 `sc delete {SERVICE NAME}` 命令卸载服务。</span><span class="sxs-lookup"><span data-stu-id="a183f-230">After a short delay to stop a service, uninstall the service with the `sc delete {SERVICE NAME}` command.</span></span>

<span data-ttu-id="a183f-231">检查示例应用服务的状态：</span><span class="sxs-lookup"><span data-stu-id="a183f-231">Check the status of the sample app service:</span></span>

```console
sc query MyService
```

<span data-ttu-id="a183f-232">当示例应用服务处于 `STOPPED` 状态时，使用以下命令卸载示例应用服务：</span><span class="sxs-lookup"><span data-stu-id="a183f-232">When the sample app service is in the `STOPPED` state, use the following command to uninstall the sample app service:</span></span>

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="a183f-233">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="a183f-233">Handle starting and stopping events</span></span>

<span data-ttu-id="a183f-234">若要处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件，请执行以下其他更改：</span><span class="sxs-lookup"><span data-stu-id="a183f-234">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="a183f-235">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="a183f-235">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="a183f-236">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a183f-236">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="a183f-237">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="a183f-237">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="a183f-238">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[将项目转换为 Windows 服务](#convert-a-project-into-a-windows-service)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="a183f-238">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a183f-239">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="a183f-239">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a183f-240">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="a183f-240">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="a183f-241">有关更多信息，请参见<xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="a183f-241">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="a183f-242">配置 HTTPS</span><span class="sxs-lookup"><span data-stu-id="a183f-242">Configure HTTPS</span></span>

<span data-ttu-id="a183f-243">若要为服务配置安全终结点，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a183f-243">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="a183f-244">使用平台的证书获取和部署机制，为托管系统创建 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="a183f-244">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="a183f-245">将 [Kestrel 服务器 HTTPS 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)指定为使用此证书。</span><span class="sxs-lookup"><span data-stu-id="a183f-245">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="a183f-246">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="a183f-246">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="a183f-247">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="a183f-247">Current directory and content root</span></span>

<span data-ttu-id="a183f-248">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="a183f-248">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="a183f-249">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="a183f-249">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="a183f-250">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="a183f-250">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="a183f-251">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="a183f-251">Set the content root path to the app's folder</span></span>

<span data-ttu-id="a183f-252"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="a183f-252">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="a183f-253">请调用包含应用内容根的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="a183f-253">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="a183f-254">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="a183f-254">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="a183f-255">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="a183f-255">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="a183f-256">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="a183f-256">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a183f-257">其他资源</span><span class="sxs-lookup"><span data-stu-id="a183f-257">Additional resources</span></span>

* <span data-ttu-id="a183f-258">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="a183f-258">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
