---
<span data-ttu-id="21f7e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="21f7e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="21f7e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="21f7e-102">'Blazor'</span></span>
- <span data-ttu-id="21f7e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="21f7e-103">'Identity'</span></span>
- <span data-ttu-id="21f7e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="21f7e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="21f7e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="21f7e-105">'Razor'</span></span>
- <span data-ttu-id="21f7e-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="21f7e-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="21f7e-107">在 Windows 服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21f7e-107">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="21f7e-108">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="21f7e-108">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="21f7e-109">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="21f7e-109">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="21f7e-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="21f7e-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21f7e-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="21f7e-111">Prerequisites</span></span>

* [<span data-ttu-id="21f7e-112">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="21f7e-112">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="21f7e-113">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="21f7e-113">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="21f7e-114">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="21f7e-114">Worker Service template</span></span>

<span data-ttu-id="21f7e-115">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="21f7e-115">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="21f7e-116">要使用该模板作为编写 Windows 服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="21f7e-116">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="21f7e-117">从 .NET Core 模板创建辅助角色服务应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-117">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="21f7e-118">按照[应用配置](#app-configuration)部分中的指导来更新辅助角色服务应用，以便它可以作为 Windows 服务运行。</span><span class="sxs-lookup"><span data-stu-id="21f7e-118">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="21f7e-119">应用配置</span><span class="sxs-lookup"><span data-stu-id="21f7e-119">App configuration</span></span>

<span data-ttu-id="21f7e-120">应用需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-120">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="21f7e-121">生成主机时会调用 `IHostBuilder.UseWindowsService`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-121">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="21f7e-122">若应用作为 Windows 服务运行，方法为：</span><span class="sxs-lookup"><span data-stu-id="21f7e-122">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="21f7e-123">将主机生存期设置为 `WindowsServiceLifetime`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-123">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="21f7e-124">将[内容根](xref:fundamentals/index#content-root)设置为 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-124">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="21f7e-125">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="21f7e-125">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="21f7e-126">为事件日志启用日志记录：</span><span class="sxs-lookup"><span data-stu-id="21f7e-126">Enables logging to the event log:</span></span>
  * <span data-ttu-id="21f7e-127">应用名称用作默认源名称。</span><span class="sxs-lookup"><span data-stu-id="21f7e-127">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="21f7e-128">对于基于 ASP.NET Core 模板且调用 `CreateDefaultBuilder` 生成主机的应用，默认日志级别为“警告”或更高级别。</span><span class="sxs-lookup"><span data-stu-id="21f7e-128">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="21f7e-129">在 appsettings.json/appsettings.{Environment}.json 或其他配置提供程序中，使用 `Logging:EventLog:LogLevel:Default` 键覆盖默认日志级别。</span><span class="sxs-lookup"><span data-stu-id="21f7e-129">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="21f7e-130">只有管理员可以创建新的事件源。</span><span class="sxs-lookup"><span data-stu-id="21f7e-130">Only administrators can create new event sources.</span></span> <span data-ttu-id="21f7e-131">无法使用应用程序名称创建事件源时，应用程序源将记录一条警告，并禁用事件源。</span><span class="sxs-lookup"><span data-stu-id="21f7e-131">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="21f7e-132">在 Program.cs 的 `CreateHostBuilder` 中：</span><span class="sxs-lookup"><span data-stu-id="21f7e-132">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="21f7e-133">本主题附带了以下示例应用：</span><span class="sxs-lookup"><span data-stu-id="21f7e-133">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="21f7e-134">后台辅助角色服务示例：一个基于[辅助角色服务模板](#worker-service-template)的非 Web 应用示例，该模板使用[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-134">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="21f7e-135">Web 应用服务示例：一个作为 Windows 服务运行的 Razor Pages Web 应用示例，通过[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-135">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="21f7e-136">有关 MVC 指南，请参阅 <xref:mvc/overview> 和 <xref:migration/22-to-30> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="21f7e-136">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="21f7e-137">部署类型</span><span class="sxs-lookup"><span data-stu-id="21f7e-137">Deployment type</span></span>

<span data-ttu-id="21f7e-138">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-138">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="21f7e-139">SDK</span><span class="sxs-lookup"><span data-stu-id="21f7e-139">SDK</span></span>

<span data-ttu-id="21f7e-140">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="21f7e-140">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="21f7e-141">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="21f7e-141">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="21f7e-142">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="21f7e-143">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="21f7e-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="21f7e-144">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="21f7e-145">如果使用 [Web SDK](#sdk)，则 web.config 文件（通常在发布 ASP.NET Core 应用时生成）不是 Windows 服务应用的必要文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-145">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="21f7e-146">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-146">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="21f7e-147">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-147">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="21f7e-148">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="21f7e-148">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="21f7e-149">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="21f7e-149">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="21f7e-150">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="21f7e-150">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="21f7e-151">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="21f7e-151">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="21f7e-152">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="21f7e-152">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="21f7e-153">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-153">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="21f7e-154">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-154">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="21f7e-155">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="21f7e-155">Service user account</span></span>

<span data-ttu-id="21f7e-156">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-156">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="21f7e-157">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="21f7e-157">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-158">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="21f7e-158">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="21f7e-159">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-159">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="21f7e-160">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="21f7e-160">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="21f7e-161">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-161">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="21f7e-162">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-162">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="21f7e-163">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-163">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="21f7e-164">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="21f7e-164">Log on as a service rights</span></span>

<span data-ttu-id="21f7e-165">为服务用户帐户创建“以服务身份登录”权限：</span><span class="sxs-lookup"><span data-stu-id="21f7e-165">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="21f7e-166">通过运行 secpool.msc，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="21f7e-166">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="21f7e-167">展开“本地策略”节点，选择“用户权限分配”。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-167">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="21f7e-168">打开“以服务身份登录”策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-168">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="21f7e-169">选择“添加用户或组”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-169">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="21f7e-170">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="21f7e-170">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="21f7e-171">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-171">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="21f7e-172">选择“高级”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-172">Select **Advanced**.</span></span> <span data-ttu-id="21f7e-173">选择“开始查找”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-173">Select **Find Now**.</span></span> <span data-ttu-id="21f7e-174">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-174">Select the user account from the list.</span></span> <span data-ttu-id="21f7e-175">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-175">Select **OK**.</span></span> <span data-ttu-id="21f7e-176">再次选择“确定”，以将该用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-176">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="21f7e-177">选择“确定”或“应用”，以接受更改。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-177">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="21f7e-178">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-178">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="21f7e-179">创建服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-179">Create a service</span></span>

<span data-ttu-id="21f7e-180">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-180">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="21f7e-181">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="21f7e-181">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="21f7e-182">`{EXE PATH}`：应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-182">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="21f7e-183">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-183">Don't include the app's executable in the path.</span></span> <span data-ttu-id="21f7e-184">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="21f7e-184">A trailing slash isn't required.</span></span>
* <span data-ttu-id="21f7e-185">`{DOMAIN OR COMPUTER NAME\USER}`：服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-185">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="21f7e-186">`{SERVICE NAME}`：服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-186">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="21f7e-187">`{EXE FILE PATH}`：应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-187">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="21f7e-188">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="21f7e-188">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="21f7e-189">`{DESCRIPTION}`：服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-189">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="21f7e-190">`{DISPLAY NAME}`：服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-190">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="21f7e-191">启动服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-191">Start a service</span></span>

<span data-ttu-id="21f7e-192">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-192">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-193">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-193">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="21f7e-194">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="21f7e-194">Determine a service's status</span></span>

<span data-ttu-id="21f7e-195">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="21f7e-195">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-196">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="21f7e-196">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="21f7e-197">停止服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-197">Stop a service</span></span>

<span data-ttu-id="21f7e-198">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-198">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="21f7e-199">删除服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-199">Remove a service</span></span>

<span data-ttu-id="21f7e-200">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-200">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="21f7e-201">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="21f7e-201">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="21f7e-202">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-202">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="21f7e-203">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-203">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="21f7e-204">配置终结点</span><span class="sxs-lookup"><span data-stu-id="21f7e-204">Configure endpoints</span></span>

<span data-ttu-id="21f7e-205">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-205">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="21f7e-206">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="21f7e-206">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="21f7e-207">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="21f7e-207">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="21f7e-208">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="21f7e-208">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="21f7e-209">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-209">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="21f7e-210">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="21f7e-210">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="21f7e-211">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="21f7e-211">Current directory and content root</span></span>

<span data-ttu-id="21f7e-212">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-212">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="21f7e-213">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-213">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="21f7e-214">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-214">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="21f7e-215">使用 ContentRootPath 或 ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="21f7e-215">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="21f7e-216">使用 [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 或 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> 查找应用的资源。</span><span class="sxs-lookup"><span data-stu-id="21f7e-216">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="21f7e-217">应用作为服务运行时，<xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> 将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> 设置为 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-217">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="21f7e-218">通过调用 [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host)，从应用的内容根加载应用的默认设置文件 appsettings.json 和 appsettings.{Environment}.json。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-218">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="21f7e-219">对于 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 中的开发人员代码加载的其他设置文件，无需调用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-219">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="21f7e-220">在下面的示例中，custom_settings.json 文件位于应用的内容根，加载它时未显式设置基本路径：</span><span class="sxs-lookup"><span data-stu-id="21f7e-220">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="21f7e-221">请勿尝试使用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取资源路径，因为 Windows 服务应用会返回“C:\\WINDOWS\\system32”文件夹作为其当前目录。</span><span class="sxs-lookup"><span data-stu-id="21f7e-221">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="21f7e-222">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="21f7e-222">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="21f7e-223">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-223">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="21f7e-224">疑难解答</span><span class="sxs-lookup"><span data-stu-id="21f7e-224">Troubleshoot</span></span>

<span data-ttu-id="21f7e-225">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-225">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="21f7e-226">常见错误</span><span class="sxs-lookup"><span data-stu-id="21f7e-226">Common errors</span></span>

* <span data-ttu-id="21f7e-227">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="21f7e-227">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="21f7e-228">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。</span><span class="sxs-lookup"><span data-stu-id="21f7e-228">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="21f7e-229">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="21f7e-229">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="21f7e-230">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="21f7e-230">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="21f7e-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-231">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="21f7e-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-232">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="21f7e-233">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="21f7e-233">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="21f7e-234">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="21f7e-234">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="21f7e-235">Windows 服务的基本路径是“c:\\Windows\\System32”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-235">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="21f7e-236">用户没有“以服务身份登录”权限。</span><span class="sxs-lookup"><span data-stu-id="21f7e-236">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="21f7e-237">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="21f7e-237">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="21f7e-238">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-238">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="21f7e-239">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-239">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="21f7e-240">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="21f7e-240">System and Application Event Logs</span></span>

<span data-ttu-id="21f7e-241">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="21f7e-241">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="21f7e-242">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-242">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="21f7e-243">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="21f7e-243">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="21f7e-244">选择“系统”，以打开系统事件日志。</span><span class="sxs-lookup"><span data-stu-id="21f7e-244">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="21f7e-245">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="21f7e-245">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="21f7e-246">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="21f7e-246">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="21f7e-247">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="21f7e-247">Run the app at a command prompt</span></span>

<span data-ttu-id="21f7e-248">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="21f7e-248">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="21f7e-249">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="21f7e-249">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="21f7e-250">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-250">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="21f7e-251">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="21f7e-251">Clear package caches</span></span>

<span data-ttu-id="21f7e-252">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="21f7e-252">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="21f7e-253">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="21f7e-254">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="21f7e-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="21f7e-255">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="21f7e-256">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="21f7e-256">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="21f7e-257">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="21f7e-257">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="21f7e-258">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="21f7e-258">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="21f7e-259">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="21f7e-259">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="21f7e-260">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-260">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="21f7e-261">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="21f7e-261">Slow or hanging app</span></span>

<span data-ttu-id="21f7e-262">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="21f7e-262">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="21f7e-263">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="21f7e-263">App crashes or encounters an exception</span></span>

<span data-ttu-id="21f7e-264">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="21f7e-264">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="21f7e-265">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-265">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="21f7e-266">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="21f7e-266">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="21f7e-267">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-267">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="21f7e-268">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="21f7e-268">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="21f7e-269">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-269">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="21f7e-270">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-270">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="21f7e-271">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-271">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="21f7e-272">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="21f7e-272">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="21f7e-273">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="21f7e-273">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="21f7e-274">分析转储</span><span class="sxs-lookup"><span data-stu-id="21f7e-274">Analyze the dump</span></span>

<span data-ttu-id="21f7e-275">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="21f7e-275">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="21f7e-276">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-276">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21f7e-277">其他资源</span><span class="sxs-lookup"><span data-stu-id="21f7e-277">Additional resources</span></span>

* <span data-ttu-id="21f7e-278">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="21f7e-278">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="21f7e-279">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="21f7e-279">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="21f7e-280">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="21f7e-280">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="21f7e-281">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="21f7e-281">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21f7e-282">先决条件</span><span class="sxs-lookup"><span data-stu-id="21f7e-282">Prerequisites</span></span>

* [<span data-ttu-id="21f7e-283">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="21f7e-283">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="21f7e-284">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="21f7e-284">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="21f7e-285">应用配置</span><span class="sxs-lookup"><span data-stu-id="21f7e-285">App configuration</span></span>

<span data-ttu-id="21f7e-286">应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。</span><span class="sxs-lookup"><span data-stu-id="21f7e-286">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="21f7e-287">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="21f7e-287">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="21f7e-288">检查是否已连接调试器或是否存在 `--console` 开关。</span><span class="sxs-lookup"><span data-stu-id="21f7e-288">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="21f7e-289">如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-289">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="21f7e-290">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="21f7e-290">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="21f7e-291">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-291">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="21f7e-292">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-292">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="21f7e-293">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="21f7e-293">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="21f7e-294">请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-294">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="21f7e-295">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="21f7e-295">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="21f7e-296">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="21f7e-296">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="21f7e-297">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-297">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="21f7e-298">使用 appsettings.Production.json文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="21f7e-298">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="21f7e-299">示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-299">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="21f7e-300">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="21f7e-300">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="21f7e-301">部署类型</span><span class="sxs-lookup"><span data-stu-id="21f7e-301">Deployment type</span></span>

<span data-ttu-id="21f7e-302">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-302">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="21f7e-303">SDK</span><span class="sxs-lookup"><span data-stu-id="21f7e-303">SDK</span></span>

<span data-ttu-id="21f7e-304">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="21f7e-304">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="21f7e-305">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="21f7e-305">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="21f7e-306">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-306">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="21f7e-307">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="21f7e-307">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="21f7e-308">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-308">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="21f7e-309">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="21f7e-309">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="21f7e-310">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-310">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="21f7e-311">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-311">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="21f7e-312">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-312">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="21f7e-313">web.config文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="21f7e-313">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="21f7e-314">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-314">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="21f7e-315">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-315">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="21f7e-316">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="21f7e-316">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="21f7e-317">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="21f7e-317">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="21f7e-318">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="21f7e-318">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="21f7e-319">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="21f7e-319">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="21f7e-320">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="21f7e-320">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="21f7e-321">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-321">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="21f7e-322">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-322">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="21f7e-323">将 `<SelfContained>` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="21f7e-323">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="21f7e-324">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="21f7e-324">Service user account</span></span>

<span data-ttu-id="21f7e-325">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-325">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="21f7e-326">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="21f7e-326">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-327">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="21f7e-327">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="21f7e-328">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-328">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="21f7e-329">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="21f7e-329">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="21f7e-330">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-330">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="21f7e-331">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-331">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="21f7e-332">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-332">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="21f7e-333">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="21f7e-333">Log on as a service rights</span></span>

<span data-ttu-id="21f7e-334">为服务用户帐户创建“以服务身份登录”权限：</span><span class="sxs-lookup"><span data-stu-id="21f7e-334">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="21f7e-335">通过运行 secpool.msc，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="21f7e-335">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="21f7e-336">展开“本地策略”节点，选择“用户权限分配”。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-336">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="21f7e-337">打开“以服务身份登录”策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-337">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="21f7e-338">选择“添加用户或组”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-338">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="21f7e-339">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="21f7e-339">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="21f7e-340">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-340">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="21f7e-341">选择“高级”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-341">Select **Advanced**.</span></span> <span data-ttu-id="21f7e-342">选择“开始查找”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-342">Select **Find Now**.</span></span> <span data-ttu-id="21f7e-343">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-343">Select the user account from the list.</span></span> <span data-ttu-id="21f7e-344">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-344">Select **OK**.</span></span> <span data-ttu-id="21f7e-345">再次选择“确定”，以将该用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-345">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="21f7e-346">选择“确定”或“应用”，以接受更改。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-346">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="21f7e-347">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-347">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="21f7e-348">创建服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-348">Create a service</span></span>

<span data-ttu-id="21f7e-349">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-349">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="21f7e-350">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="21f7e-350">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="21f7e-351">`{EXE PATH}`：应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-351">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="21f7e-352">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-352">Don't include the app's executable in the path.</span></span> <span data-ttu-id="21f7e-353">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="21f7e-353">A trailing slash isn't required.</span></span>
* <span data-ttu-id="21f7e-354">`{DOMAIN OR COMPUTER NAME\USER}`：服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-354">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="21f7e-355">`{SERVICE NAME}`：服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-355">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="21f7e-356">`{EXE FILE PATH}`：应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-356">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="21f7e-357">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="21f7e-357">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="21f7e-358">`{DESCRIPTION}`：服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-358">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="21f7e-359">`{DISPLAY NAME}`：服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-359">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="21f7e-360">启动服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-360">Start a service</span></span>

<span data-ttu-id="21f7e-361">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-361">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-362">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-362">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="21f7e-363">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="21f7e-363">Determine a service's status</span></span>

<span data-ttu-id="21f7e-364">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="21f7e-364">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-365">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="21f7e-365">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="21f7e-366">停止服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-366">Stop a service</span></span>

<span data-ttu-id="21f7e-367">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-367">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="21f7e-368">删除服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-368">Remove a service</span></span>

<span data-ttu-id="21f7e-369">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-369">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="21f7e-370">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="21f7e-370">Handle starting and stopping events</span></span>

<span data-ttu-id="21f7e-371">处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="21f7e-371">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="21f7e-372">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="21f7e-372">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="21f7e-373">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="21f7e-373">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="21f7e-374">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="21f7e-374">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="21f7e-375">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="21f7e-375">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="21f7e-376">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="21f7e-376">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="21f7e-377">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-377">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="21f7e-378">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-378">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="21f7e-379">配置终结点</span><span class="sxs-lookup"><span data-stu-id="21f7e-379">Configure endpoints</span></span>

<span data-ttu-id="21f7e-380">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-380">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="21f7e-381">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="21f7e-381">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="21f7e-382">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="21f7e-382">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="21f7e-383">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="21f7e-383">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="21f7e-384">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-384">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="21f7e-385">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="21f7e-385">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="21f7e-386">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="21f7e-386">Current directory and content root</span></span>

<span data-ttu-id="21f7e-387">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-387">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="21f7e-388">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-388">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="21f7e-389">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-389">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="21f7e-390">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="21f7e-390">Set the content root path to the app's folder</span></span>

<span data-ttu-id="21f7e-391"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-391">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="21f7e-392">请调用包含应用[内容根目录](xref:fundamentals/index#content-root)的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-392">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="21f7e-393">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="21f7e-393">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="21f7e-394">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="21f7e-394">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="21f7e-395">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-395">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="21f7e-396">疑难解答</span><span class="sxs-lookup"><span data-stu-id="21f7e-396">Troubleshoot</span></span>

<span data-ttu-id="21f7e-397">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-397">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="21f7e-398">常见错误</span><span class="sxs-lookup"><span data-stu-id="21f7e-398">Common errors</span></span>

* <span data-ttu-id="21f7e-399">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="21f7e-399">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="21f7e-400">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。</span><span class="sxs-lookup"><span data-stu-id="21f7e-400">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="21f7e-401">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="21f7e-401">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="21f7e-402">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="21f7e-402">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="21f7e-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-403">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="21f7e-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-404">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="21f7e-405">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="21f7e-405">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="21f7e-406">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="21f7e-406">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="21f7e-407">Windows 服务的基本路径是“c:\\Windows\\System32”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-407">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="21f7e-408">用户没有“以服务身份登录”权限。</span><span class="sxs-lookup"><span data-stu-id="21f7e-408">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="21f7e-409">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="21f7e-409">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="21f7e-410">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-410">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="21f7e-411">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-411">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="21f7e-412">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="21f7e-412">System and Application Event Logs</span></span>

<span data-ttu-id="21f7e-413">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="21f7e-413">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="21f7e-414">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-414">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="21f7e-415">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="21f7e-415">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="21f7e-416">选择“系统”，以打开系统事件日志。</span><span class="sxs-lookup"><span data-stu-id="21f7e-416">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="21f7e-417">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="21f7e-417">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="21f7e-418">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="21f7e-418">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="21f7e-419">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="21f7e-419">Run the app at a command prompt</span></span>

<span data-ttu-id="21f7e-420">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="21f7e-420">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="21f7e-421">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="21f7e-421">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="21f7e-422">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-422">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="21f7e-423">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="21f7e-423">Clear package caches</span></span>

<span data-ttu-id="21f7e-424">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="21f7e-424">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="21f7e-425">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-425">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="21f7e-426">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="21f7e-426">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="21f7e-427">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-427">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="21f7e-428">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="21f7e-428">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="21f7e-429">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="21f7e-429">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="21f7e-430">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="21f7e-430">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="21f7e-431">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="21f7e-431">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="21f7e-432">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-432">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="21f7e-433">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="21f7e-433">Slow or hanging app</span></span>

<span data-ttu-id="21f7e-434">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="21f7e-434">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="21f7e-435">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="21f7e-435">App crashes or encounters an exception</span></span>

<span data-ttu-id="21f7e-436">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="21f7e-436">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="21f7e-437">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-437">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="21f7e-438">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="21f7e-438">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="21f7e-439">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-439">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="21f7e-440">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="21f7e-440">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="21f7e-441">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-441">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="21f7e-442">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-442">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="21f7e-443">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-443">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="21f7e-444">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="21f7e-444">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="21f7e-445">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="21f7e-445">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="21f7e-446">分析转储</span><span class="sxs-lookup"><span data-stu-id="21f7e-446">Analyze the dump</span></span>

<span data-ttu-id="21f7e-447">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="21f7e-447">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="21f7e-448">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-448">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21f7e-449">其他资源</span><span class="sxs-lookup"><span data-stu-id="21f7e-449">Additional resources</span></span>

* <span data-ttu-id="21f7e-450">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="21f7e-450">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="21f7e-451">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="21f7e-451">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="21f7e-452">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="21f7e-452">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="21f7e-453">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="21f7e-453">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="21f7e-454">先决条件</span><span class="sxs-lookup"><span data-stu-id="21f7e-454">Prerequisites</span></span>

* [<span data-ttu-id="21f7e-455">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="21f7e-455">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="21f7e-456">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="21f7e-456">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="21f7e-457">应用配置</span><span class="sxs-lookup"><span data-stu-id="21f7e-457">App configuration</span></span>

<span data-ttu-id="21f7e-458">应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。</span><span class="sxs-lookup"><span data-stu-id="21f7e-458">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="21f7e-459">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="21f7e-459">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="21f7e-460">检查是否已连接调试器或是否存在 `--console` 开关。</span><span class="sxs-lookup"><span data-stu-id="21f7e-460">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="21f7e-461">如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-461">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="21f7e-462">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="21f7e-462">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="21f7e-463">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-463">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="21f7e-464">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-464">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="21f7e-465">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="21f7e-465">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="21f7e-466">请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-466">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="21f7e-467">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="21f7e-467">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="21f7e-468">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="21f7e-468">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="21f7e-469">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-469">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="21f7e-470">使用 appsettings.Production.json文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="21f7e-470">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="21f7e-471">示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-471">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="21f7e-472">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="21f7e-472">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="21f7e-473">部署类型</span><span class="sxs-lookup"><span data-stu-id="21f7e-473">Deployment type</span></span>

<span data-ttu-id="21f7e-474">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-474">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="21f7e-475">SDK</span><span class="sxs-lookup"><span data-stu-id="21f7e-475">SDK</span></span>

<span data-ttu-id="21f7e-476">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="21f7e-476">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="21f7e-477">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="21f7e-477">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="21f7e-478">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-478">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="21f7e-479">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="21f7e-479">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="21f7e-480">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-480">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="21f7e-481">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="21f7e-481">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="21f7e-482">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-482">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="21f7e-483">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-483">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="21f7e-484">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-484">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="21f7e-485">`<UseAppHost>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-485">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="21f7e-486">此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-486">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="21f7e-487">web.config文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="21f7e-487">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="21f7e-488">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-488">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="21f7e-489">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-489">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="21f7e-490">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="21f7e-490">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="21f7e-491">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="21f7e-491">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="21f7e-492">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="21f7e-492">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="21f7e-493">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="21f7e-493">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="21f7e-494">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="21f7e-494">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="21f7e-495">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-495">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="21f7e-496">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-496">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="21f7e-497">将 `<SelfContained>` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="21f7e-497">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="21f7e-498">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="21f7e-498">Service user account</span></span>

<span data-ttu-id="21f7e-499">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-499">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="21f7e-500">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="21f7e-500">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-501">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="21f7e-501">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="21f7e-502">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-502">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="21f7e-503">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="21f7e-503">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="21f7e-504">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-504">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="21f7e-505">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-505">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="21f7e-506">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-506">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="21f7e-507">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="21f7e-507">Log on as a service rights</span></span>

<span data-ttu-id="21f7e-508">为服务用户帐户创建“以服务身份登录”权限：</span><span class="sxs-lookup"><span data-stu-id="21f7e-508">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="21f7e-509">通过运行 secpool.msc，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="21f7e-509">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="21f7e-510">展开“本地策略”节点，选择“用户权限分配”。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-510">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="21f7e-511">打开“以服务身份登录”策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-511">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="21f7e-512">选择“添加用户或组”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-512">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="21f7e-513">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="21f7e-513">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="21f7e-514">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-514">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="21f7e-515">选择“高级”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-515">Select **Advanced**.</span></span> <span data-ttu-id="21f7e-516">选择“开始查找”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-516">Select **Find Now**.</span></span> <span data-ttu-id="21f7e-517">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="21f7e-517">Select the user account from the list.</span></span> <span data-ttu-id="21f7e-518">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-518">Select **OK**.</span></span> <span data-ttu-id="21f7e-519">再次选择“确定”，以将该用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="21f7e-519">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="21f7e-520">选择“确定”或“应用”，以接受更改。 </span><span class="sxs-lookup"><span data-stu-id="21f7e-520">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="21f7e-521">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-521">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="21f7e-522">创建服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-522">Create a service</span></span>

<span data-ttu-id="21f7e-523">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-523">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="21f7e-524">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="21f7e-524">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="21f7e-525">`{EXE PATH}`：应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-525">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="21f7e-526">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-526">Don't include the app's executable in the path.</span></span> <span data-ttu-id="21f7e-527">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="21f7e-527">A trailing slash isn't required.</span></span>
* <span data-ttu-id="21f7e-528">`{DOMAIN OR COMPUTER NAME\USER}`：服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-528">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="21f7e-529">`{SERVICE NAME}`：服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-529">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="21f7e-530">`{EXE FILE PATH}`：应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-530">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="21f7e-531">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="21f7e-531">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="21f7e-532">`{DESCRIPTION}`：服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-532">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="21f7e-533">`{DISPLAY NAME}`：服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-533">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="21f7e-534">启动服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-534">Start a service</span></span>

<span data-ttu-id="21f7e-535">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-535">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-536">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="21f7e-536">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="21f7e-537">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="21f7e-537">Determine a service's status</span></span>

<span data-ttu-id="21f7e-538">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="21f7e-538">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="21f7e-539">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="21f7e-539">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="21f7e-540">停止服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-540">Stop a service</span></span>

<span data-ttu-id="21f7e-541">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-541">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="21f7e-542">删除服务</span><span class="sxs-lookup"><span data-stu-id="21f7e-542">Remove a service</span></span>

<span data-ttu-id="21f7e-543">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="21f7e-543">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="21f7e-544">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="21f7e-544">Handle starting and stopping events</span></span>

<span data-ttu-id="21f7e-545">处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="21f7e-545">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="21f7e-546">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="21f7e-546">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="21f7e-547">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="21f7e-547">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="21f7e-548">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="21f7e-548">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="21f7e-549">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="21f7e-549">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="21f7e-550">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="21f7e-550">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="21f7e-551">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-551">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="21f7e-552">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-552">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="21f7e-553">配置终结点</span><span class="sxs-lookup"><span data-stu-id="21f7e-553">Configure endpoints</span></span>

<span data-ttu-id="21f7e-554">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-554">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="21f7e-555">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="21f7e-555">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="21f7e-556">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="21f7e-556">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="21f7e-557">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="21f7e-557">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="21f7e-558">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-558">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="21f7e-559">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="21f7e-559">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="21f7e-560">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="21f7e-560">Current directory and content root</span></span>

<span data-ttu-id="21f7e-561">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-561">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="21f7e-562">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-562">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="21f7e-563">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-563">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="21f7e-564">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="21f7e-564">Set the content root path to the app's folder</span></span>

<span data-ttu-id="21f7e-565"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-565">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="21f7e-566">请调用包含应用[内容根目录](xref:fundamentals/index#content-root)的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-566">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="21f7e-567">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="21f7e-567">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="21f7e-568">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="21f7e-568">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="21f7e-569">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="21f7e-569">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="21f7e-570">疑难解答</span><span class="sxs-lookup"><span data-stu-id="21f7e-570">Troubleshoot</span></span>

<span data-ttu-id="21f7e-571">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="21f7e-571">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="21f7e-572">常见错误</span><span class="sxs-lookup"><span data-stu-id="21f7e-572">Common errors</span></span>

* <span data-ttu-id="21f7e-573">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="21f7e-573">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="21f7e-574">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。</span><span class="sxs-lookup"><span data-stu-id="21f7e-574">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="21f7e-575">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="21f7e-575">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="21f7e-576">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="21f7e-576">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="21f7e-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-577">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="21f7e-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="21f7e-578">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="21f7e-579">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="21f7e-579">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="21f7e-580">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="21f7e-580">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="21f7e-581">Windows 服务的基本路径是“c:\\Windows\\System32”。</span><span class="sxs-lookup"><span data-stu-id="21f7e-581">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="21f7e-582">用户没有“以服务身份登录”权限。</span><span class="sxs-lookup"><span data-stu-id="21f7e-582">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="21f7e-583">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="21f7e-583">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="21f7e-584">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-584">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="21f7e-585">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="21f7e-585">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="21f7e-586">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="21f7e-586">System and Application Event Logs</span></span>

<span data-ttu-id="21f7e-587">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="21f7e-587">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="21f7e-588">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-588">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="21f7e-589">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="21f7e-589">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="21f7e-590">选择“系统”，以打开系统事件日志。</span><span class="sxs-lookup"><span data-stu-id="21f7e-590">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="21f7e-591">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="21f7e-591">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="21f7e-592">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="21f7e-592">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="21f7e-593">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="21f7e-593">Run the app at a command prompt</span></span>

<span data-ttu-id="21f7e-594">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="21f7e-594">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="21f7e-595">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="21f7e-595">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="21f7e-596">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-596">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="21f7e-597">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="21f7e-597">Clear package caches</span></span>

<span data-ttu-id="21f7e-598">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="21f7e-598">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="21f7e-599">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-599">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="21f7e-600">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="21f7e-600">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="21f7e-601">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="21f7e-601">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="21f7e-602">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="21f7e-602">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="21f7e-603">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="21f7e-603">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="21f7e-604">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="21f7e-604">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="21f7e-605">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="21f7e-605">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="21f7e-606">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="21f7e-606">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="21f7e-607">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="21f7e-607">Slow or hanging app</span></span>

<span data-ttu-id="21f7e-608">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="21f7e-608">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="21f7e-609">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="21f7e-609">App crashes or encounters an exception</span></span>

<span data-ttu-id="21f7e-610">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="21f7e-610">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="21f7e-611">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="21f7e-611">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="21f7e-612">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="21f7e-612">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="21f7e-613">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-613">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="21f7e-614">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="21f7e-614">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="21f7e-615">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="21f7e-615">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="21f7e-616">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-616">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="21f7e-617">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="21f7e-617">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="21f7e-618">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="21f7e-618">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="21f7e-619">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="21f7e-619">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="21f7e-620">分析转储</span><span class="sxs-lookup"><span data-stu-id="21f7e-620">Analyze the dump</span></span>

<span data-ttu-id="21f7e-621">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="21f7e-621">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="21f7e-622">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="21f7e-622">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="21f7e-623">其他资源</span><span class="sxs-lookup"><span data-stu-id="21f7e-623">Additional resources</span></span>

* <span data-ttu-id="21f7e-624">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="21f7e-624">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
