---
title: 在 Windows 服务中托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows 服务中托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/03/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4cfca4b38543ff073bb98dc09b483d96096928ae
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692566"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>在 Windows 服务中托管 ASP.NET Core

作者：[Luke Latham](https://github.com/guardrex)、[Tom Dykstra](https://github.com/tdykstra)

不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。 作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

* [ASP.NET .NET Core SDK 2.1 或更高版本](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 或更高版本](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>辅助角色服务模板

ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。 要使用该模板作为编写 Windows 服务应用的基础：

1. 从 .NET Core 模板创建辅助角色服务应用。
1. 按照[应用配置](#app-configuration)部分中的指导来更新辅助角色服务应用，以便它可以作为 Windows 服务运行。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 创建新项目。
1. 选择“ASP.NET Core Web 应用程序”  。 选择“下一步”  。
1. 在“项目名称”字段提供项目名称，或接受默认项目名称  。 选择“创建”  。
1. 在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.0”    。
1. 选择“辅助角色服务”模板  。 选择“创建”  。

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

将辅助角色服务 (`worker`) 模板用于命令行界面中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。 下面的示例中创建了名为 `ContosoWorkerService` 的辅助角色服务应用。 执行命令时会自动为 `ContosoWorkerService` 应用创建文件夹。

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="app-configuration"></a>应用配置

::: moniker range=">= aspnetcore-3.0"

生成主机时，将调用 [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) 包提供的 `IHostBuilder.UseWindowsService`。 若应用作为 Windows 服务运行，方法为：

* 将主机生存期设置为 `WindowsServiceLifetime`。
* 设置内容根路径。
* 启用事件日志记录，并将应用程序名称作为默认源名称。
  * 可以使用 appsettings.Production.json  文件中的 `Logging:LogLevel:Default` 键配置日志级别。
  * 只有管理员可以创建新的事件源。 无法使用应用程序名称创建事件源时，应用程序源将记录一条警告，并禁用事件源。 

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。

在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。 检查是否已连接调试器或是否存在 `--console` 开关。 如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。 如果条件为 false（应用作为服务运行）：

* 调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。 不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32  文件夹。 有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。 请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。
* 调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。

由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。

若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。 使用 appsettings.Production.json  文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。

示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。 有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a>部署类型

有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。

### <a name="framework-dependent-deployment-fdd"></a>依赖框架的部署 (FDD)

依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。 按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。  

::: moniker range=">= aspnetcore-3.0"

向项目文件添加以下属性元素：

* `<OutputType>` &ndash; 应用的输出类型（`Exe` 表示可执行文件）。
* `<LangVersion>` &ndash; C# 语言版本（`latest` 或 `preview`）。

web.config  文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。 若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。 在以下示例中，将 RID 设置为 `win7-x64`。 `<SelfContained>` 属性设置为 `false`。 这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe  ) 以及一个依赖共享 NET Core 框架的应用。

web.config  文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。 若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。

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

Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。 在以下示例中，将 RID 设置为 `win7-x64`。 `<SelfContained>` 属性设置为 `false`。 这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe  ) 以及一个依赖共享 NET Core 框架的应用。

`<UseAppHost>` 属性设置为 `true`。 此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe  ）。

web.config  文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。 若要禁止创建 web.config  文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。

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

### <a name="self-contained-deployment-scd"></a>独立部署 (SCD)

独立部署 (SCD) 不依赖主机系统上存在的共享框架。 运行时和应用的依赖项将与应用一起部署。

将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

要发布多个 RID：

* 通过以分号分隔的列表提供 RID。
* 使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。

有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。

::: moniker range="< aspnetcore-3.0"

将 `<SelfContained>` 属性设置为 `true`：

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a>服务用户帐户

在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。

对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：

```PowerShell
New-LocalUser -Name {NAME}
```

对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。

除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。

有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。

使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。 有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。

## <a name="log-on-as-a-service-rights"></a>以服务身份登录权限

为服务用户帐户创建“以服务身份登录”权限： 

1. 运行 secpool.msc，打开本地安全策略编辑器。 
1. 展开“本地策略”节点，选择“用户权限分配”。  
1. 打开“以服务身份登录”策略。 
1. 选择“添加用户或组”  。
1. 使用下列方法之一提供对象名称（用户帐户）：
   1. 在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。 
   1. 选择“高级”  。 选择“开始查找”。  从列表中选择该用户帐户。 选择“确定”  。 再次选择“确定”，以将该用户添加到策略。 
1. 选择“确定”或“应用”，以接受更改。  

## <a name="create-and-manage-the-windows-service"></a>创建和管理 Windows 服务

### <a name="create-a-service"></a>创建服务

使用 PowerShell 注册服务。 从 PowerShell 6 命令行界面，执行以下命令：

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}` &ndash; 应用在主机上的文件夹的路径（如 `d:\myservice`）。 请勿在此路径中包含应用的可执行文件。 尾部反斜杠是非必需项。
* `{DOMAIN OR COMPUTER NAME\USER}` &ndash; 服务用户帐户（如 `Contoso\ServiceUser`）。
* `{NAME}` &ndash; 服务名称（如 `MyService`）。
* `{EXE FILE PATH}` &ndash; 应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。 请将可执行文件的文件名和扩展名包括在内。
* `{DESCRIPTION}` &ndash; 服务说明（如 `My sample service`）。
* `{DISPLAY NAME}` &ndash; 服务显示名称（如 `My Service`）。

### <a name="start-a-service"></a>启动服务

使用以下 PowerShell 6 命令启动服务：

```powershell
Start-Service -Name {NAME}
```

此命令需要几秒钟才能启动服务。

### <a name="determine-a-services-status"></a>确信服务状态

要检查服务的状态，请使用以下 PowerShell 6 命令：

```powershell
Get-Service -Name {NAME}
```

状态报告为以下值之一：

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>停止服务

使用以下 Powershell 6 命令停止服务：

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a>删除服务

在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a>处理启动和停止事件

处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：

1. 使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. 创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. 在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：

   ```csharp
   host.RunAsCustomService();
   ```

   若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>代理服务器和负载均衡器方案

与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。 有关更多信息，请参见<xref:host-and-deploy/proxy-load-balancer>。

## <a name="configure-https"></a>配置 HTTPS

若要为服务配置安全终结点，请执行以下操作：

1. 使用平台的证书获取和部署机制，为托管系统创建 X.509 证书。

1. 将 [Kestrel 服务器 HTTPS 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)指定为使用此证书。

不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。

## <a name="current-directory-and-content-root"></a>当前目录和内容根

通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32  文件夹。 system32 文件夹不是存储服务文件（如设置文件）的合适位置  。 使用以下方法之一来维护和访问服务的资产和设置文件。

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>使用 ContentRootPath 或 ContentRootFileProvider

使用 [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 或 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> 查找应用的资源。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a>设置应用文件夹的内容根路径

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。 请调用包含应用内容根的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。

在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>将服务的文件存储在磁盘中的合适位置

使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。

## <a name="additional-resources"></a>其他资源

::: moniker range=">= aspnetcore-3.0"

* [Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
