---
title: 在 Windows 服务中托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows 服务中托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 014585cd1e170fc94f7f577e11ec19824e54572f
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659849"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="fd7f6-103">在 Windows 服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd7f6-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="fd7f6-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fd7f6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fd7f6-105">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="fd7f6-106">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="fd7f6-107">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fd7f6-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fd7f6-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="fd7f6-108">Prerequisites</span></span>

* [<span data-ttu-id="fd7f6-109">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="fd7f6-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="fd7f6-110">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="fd7f6-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="fd7f6-111">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="fd7f6-111">Worker Service template</span></span>

<span data-ttu-id="fd7f6-112">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="fd7f6-113">要使用该模板作为编写 Windows 服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="fd7f6-114">从 .NET Core 模板创建辅助角色服务应用。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="fd7f6-115">按照[应用配置](#app-configuration)部分中的指导来更新辅助角色服务应用，以便它可以作为 Windows 服务运行。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="fd7f6-116">应用配置</span><span class="sxs-lookup"><span data-stu-id="fd7f6-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fd7f6-117">应用需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="fd7f6-118">生成主机时会调用 `IHostBuilder.UseWindowsService`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="fd7f6-119">若应用作为 Windows 服务运行，方法为：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="fd7f6-120">将主机生存期设置为 `WindowsServiceLifetime`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="fd7f6-121">设置[内容根目录](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-121">Sets the [content root](xref:fundamentals/index#content-root).</span></span>
* <span data-ttu-id="fd7f6-122">启用事件日志记录，并将应用程序名称作为默认源名称。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-122">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="fd7f6-123">可以使用 appsettings.Production.json  文件中的 `Logging:LogLevel:Default` 键配置日志级别。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-123">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="fd7f6-124">只有管理员可以创建新的事件源。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-124">Only administrators can create new event sources.</span></span> <span data-ttu-id="fd7f6-125">无法使用应用程序名称创建事件源时，应用程序源将记录一条警告，并禁用事件源。 </span><span class="sxs-lookup"><span data-stu-id="fd7f6-125">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="fd7f6-126">在 Program.cs 的 `CreateHostBuilder` 中  ：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-126">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="fd7f6-127">本主题附带了以下示例应用：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-127">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="fd7f6-128">后台辅助角色服务示例 &ndash; 一个基于[辅助角色服务模板](#worker-service-template)的非 Web 应用示例，该模板使用[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-128">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="fd7f6-129">Web 应用服务示例 &ndash; 一个作为 Windows 服务运行的 Razor Pages Web 应用示例，通过[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-129">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="fd7f6-130">有关 MVC 指南，请参阅 <xref:mvc/overview> 和 <xref:migration/22-to-30> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-130">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fd7f6-131">应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-131">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="fd7f6-132">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-132">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="fd7f6-133">检查是否已连接调试器或是否存在 `--console` 开关。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-133">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="fd7f6-134">如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-134">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="fd7f6-135">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-135">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="fd7f6-136">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-136">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="fd7f6-137">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32  文件夹。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-137">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="fd7f6-138">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-138">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="fd7f6-139">请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-139">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="fd7f6-140">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-140">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="fd7f6-141">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-141">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="fd7f6-142">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-142">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="fd7f6-143">使用 appsettings.Production.json  文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-143">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="fd7f6-144">示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-144">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="fd7f6-145">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-145">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="fd7f6-146">部署类型</span><span class="sxs-lookup"><span data-stu-id="fd7f6-146">Deployment type</span></span>

<span data-ttu-id="fd7f6-147">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-147">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="fd7f6-148">SDK 中 IsInRole 中的声明</span><span class="sxs-lookup"><span data-stu-id="fd7f6-148">SDK</span></span>

<span data-ttu-id="fd7f6-149">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-149">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="fd7f6-150">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-150">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="fd7f6-151">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="fd7f6-151">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="fd7f6-152">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-152">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="fd7f6-153">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。  </span><span class="sxs-lookup"><span data-stu-id="fd7f6-153">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fd7f6-154">如果使用 [Web SDK](#sdk)，则 web.config 文件（通常在发布 ASP.NET Core 应用时生成）不是 Windows 服务应用的必要文件  。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-154">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fd7f6-155">若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-155">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fd7f6-156">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-156">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="fd7f6-157">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-157">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="fd7f6-158">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-158">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="fd7f6-159">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe  ) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-159">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="fd7f6-160">web.config  文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-160">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fd7f6-161">若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-161">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="fd7f6-162">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-162">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="fd7f6-163">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-163">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="fd7f6-164">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-164">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="fd7f6-165">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe  ) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-165">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="fd7f6-166">`<UseAppHost>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-166">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="fd7f6-167">此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe  ）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-167">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="fd7f6-168">web.config  文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-168">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fd7f6-169">若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-169">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="fd7f6-170">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="fd7f6-170">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="fd7f6-171">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-171">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="fd7f6-172">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-172">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="fd7f6-173">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-173">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="fd7f6-174">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-174">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="fd7f6-175">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-175">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="fd7f6-176">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-176">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="fd7f6-177">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-177">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fd7f6-178">将 `<SelfContained>` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-178">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="fd7f6-179">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="fd7f6-179">Service user account</span></span>

<span data-ttu-id="fd7f6-180">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-180">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="fd7f6-181">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-181">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="fd7f6-182">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-182">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="fd7f6-183">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-183">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="fd7f6-184">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-184">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="fd7f6-185">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-185">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="fd7f6-186">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-186">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="fd7f6-187">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-187">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="fd7f6-188">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="fd7f6-188">Log on as a service rights</span></span>

<span data-ttu-id="fd7f6-189">为服务用户帐户创建“以服务身份登录”权限： </span><span class="sxs-lookup"><span data-stu-id="fd7f6-189">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="fd7f6-190">通过运行 secpool.msc  ，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-190">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="fd7f6-191">展开“本地策略”节点，选择“用户权限分配”。  </span><span class="sxs-lookup"><span data-stu-id="fd7f6-191">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="fd7f6-192">打开“以服务身份登录”策略。 </span><span class="sxs-lookup"><span data-stu-id="fd7f6-192">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="fd7f6-193">选择“添加用户或组”  。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-193">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="fd7f6-194">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-194">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="fd7f6-195">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="fd7f6-195">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="fd7f6-196">选择“高级”。 </span><span class="sxs-lookup"><span data-stu-id="fd7f6-196">Select **Advanced**.</span></span> <span data-ttu-id="fd7f6-197">选择“开始查找”。 </span><span class="sxs-lookup"><span data-stu-id="fd7f6-197">Select **Find Now**.</span></span> <span data-ttu-id="fd7f6-198">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-198">Select the user account from the list.</span></span> <span data-ttu-id="fd7f6-199">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-199">Select **OK**.</span></span> <span data-ttu-id="fd7f6-200">再次选择“确定”，以将该用户添加到策略。 </span><span class="sxs-lookup"><span data-stu-id="fd7f6-200">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="fd7f6-201">选择“确定”或“应用”，以接受更改。  </span><span class="sxs-lookup"><span data-stu-id="fd7f6-201">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="fd7f6-202">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="fd7f6-202">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="fd7f6-203">创建服务</span><span class="sxs-lookup"><span data-stu-id="fd7f6-203">Create a service</span></span>

<span data-ttu-id="fd7f6-204">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-204">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="fd7f6-205">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-205">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="fd7f6-206">`{EXE PATH}` &ndash; 应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-206">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="fd7f6-207">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-207">Don't include the app's executable in the path.</span></span> <span data-ttu-id="fd7f6-208">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-208">A trailing slash isn't required.</span></span>
* <span data-ttu-id="fd7f6-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; 服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-209">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="fd7f6-210">`{NAME}` &ndash; 服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-210">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="fd7f6-211">`{EXE FILE PATH}` &ndash; 应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-211">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="fd7f6-212">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-212">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="fd7f6-213">`{DESCRIPTION}` &ndash; 服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-213">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="fd7f6-214">`{DISPLAY NAME}` &ndash; 服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-214">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="fd7f6-215">启动服务</span><span class="sxs-lookup"><span data-stu-id="fd7f6-215">Start a service</span></span>

<span data-ttu-id="fd7f6-216">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-216">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="fd7f6-217">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-217">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="fd7f6-218">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="fd7f6-218">Determine a service's status</span></span>

<span data-ttu-id="fd7f6-219">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-219">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="fd7f6-220">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-220">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="fd7f6-221">停止服务</span><span class="sxs-lookup"><span data-stu-id="fd7f6-221">Stop a service</span></span>

<span data-ttu-id="fd7f6-222">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-222">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="fd7f6-223">删除服务</span><span class="sxs-lookup"><span data-stu-id="fd7f6-223">Remove a service</span></span>

<span data-ttu-id="fd7f6-224">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-224">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="fd7f6-225">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="fd7f6-225">Handle starting and stopping events</span></span>

<span data-ttu-id="fd7f6-226">处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-226">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="fd7f6-227">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-227">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="fd7f6-228">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-228">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="fd7f6-229">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-229">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="fd7f6-230">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-230">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fd7f6-231">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="fd7f6-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fd7f6-232">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-232">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="fd7f6-233">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-233">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="fd7f6-234">配置终结点</span><span class="sxs-lookup"><span data-stu-id="fd7f6-234">Configure endpoints</span></span>

<span data-ttu-id="fd7f6-235">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-235">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="fd7f6-236">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-236">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="fd7f6-237">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-237">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="fd7f6-238">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-238">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="fd7f6-239">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-239">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="fd7f6-240">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-240">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="fd7f6-241">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="fd7f6-241">Current directory and content root</span></span>

<span data-ttu-id="fd7f6-242">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32  文件夹。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-242">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="fd7f6-243">system32 文件夹不是存储服务文件（如设置文件）的合适位置  。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-243">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="fd7f6-244">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-244">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="fd7f6-245">使用 ContentRootPath 或 ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="fd7f6-245">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="fd7f6-246">使用 [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 或 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> 查找应用的资源。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-246">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="fd7f6-247">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="fd7f6-247">Set the content root path to the app's folder</span></span>

<span data-ttu-id="fd7f6-248"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-248">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="fd7f6-249">请调用包含应用[内容根目录](xref:fundamentals/index#content-root)的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-249">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="fd7f6-250">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="fd7f6-250">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="fd7f6-251">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="fd7f6-251">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="fd7f6-252">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="fd7f6-252">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd7f6-253">其他资源</span><span class="sxs-lookup"><span data-stu-id="fd7f6-253">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="fd7f6-254">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="fd7f6-254">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="fd7f6-255">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="fd7f6-255">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
