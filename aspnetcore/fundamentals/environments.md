---
title: 在 ASP.NET Core 中使用多个环境
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中控制多个环境的应用行为。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330604"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>在 ASP.NET Core 中使用多个环境

::: moniker range=">= aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)

ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="environments"></a>环境

为了确定运行时环境，ASP.NET Core 从以下环境变量中读取信息：

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. `ASPNETCORE_ENVIRONMENT`（当调用 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 时）。 默认 ASP.NET Core Web 应用模板调用 `ConfigureWebHostDefaults`。 `ASPNETCORE_ENVIRONMENT` 值替代 `DOTNET_ENVIRONMENT`。

`IHostEnvironment.EnvironmentName` 可以设置为任意值，但是框架提供了下列值：

* <xref:Microsoft.Extensions.Hosting.Environments.Development>：[launchSettings.json](#lsj) 文件将本地计算机上的 `ASPNETCORE_ENVIRONMENT` 设置为 `Development`。
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>：没有设置 `DOTNET_ENVIRONMENT` 和 `ASPNETCORE_ENVIRONMENT` 时的默认值。

下面的代码：

* 当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。
* 当 `ASPNETCORE_ENVIRONMENT` 的值设置为 `Staging`、`Production` 或 `Staging_2` 时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)。
* 将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup.Configure` 中。 当应用仅需为几个代码差异最小的环境调整 `Startup.Configure` 时，这种方法非常有用。
* 类似于由 ASP.NET Core 模板生成的代码。

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) 的值在元素中包含或排除标记：

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)中的[“关于”页](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)包括前面的标记，并显示 `IWebHostEnvironment.EnvironmentName` 的值。

在 Windows 和 macOS 上，环境变量和值不区分大小写。 默认情况下，Linux 环境变量和值区分大小写。

### <a name="create-environmentssample"></a>创建 EnvironmentsSample

本文档中使用的[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)基于名为“EnvironmentsSample”的 Razor Pages 项目。

以下代码创建并运行名为“EnvironmentsSample”的 Web 应用：

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

当应用运行时，它会显示下面的部分输出内容：

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a>开发和 launchSettings.json

开发环境可以启用不应该在生产中公开的功能。 例如，ASP.NET Core 模板在开发环境中启用了[开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)。

本地计算机开发环境可以在项目的 Properties\launchSettings.json 文件中设置。 在 launchSettings.json 中设置的环境值替代在系统环境中设置的值。

launchSettings.json 文件：

* 仅在本地开发计算机上使用。
* 未部署。
* 包含配置文件设置。

下面的 JSON 显示名为“EnvironmentsSample”的 ASP.NET Core Web 项目的 launchSettings.json 文件，此项目是通过 Visual Studio 或 `dotnet new` 创建的：

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

前面的标记包含两个配置文件：

* `IIS Express`：在 Visual Studio 中启动应用时使用的默认配置文件。 由于 `"commandName"` 键的值为 `"IISExpress"`，因此 [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) 是 Web 服务器。 可以将启动配置文件设置为此项目或所包含的其他任何配置文件。 例如，在下图中，选择项目名称会启动 [Kestrel Web 服务器](xref:fundamentals/servers/kestrel)。

  ![使用菜单启动 IIS Express](environments/_static/iisx2.png)
* `EnvironmentsSample`：配置文件名称是项目名称。 当使用 `dotnet run` 启动应用时，默认使用此配置文件。  由于 `"commandName"` 键的值为 `"Project"`，因此启动的是 [Kestrel Web 服务器](xref:fundamentals/servers/kestrel)。

`commandName` 的值可指定要启动的 Web 服务器。 `commandName` 可为以下任一项：

* `IISExpress`：启动 IIS Express。
* `IIS`：不启动任何 Web 服务器。 IIS 预计可用。
* `Project`：启动 Kestrel。

Visual Studio 项目属性“调试”选项卡提供了 GUI 来编辑 launchSettings.json 文件。 在 Web 服务器重新启动之前，对项目配置文件所做的更改可能不会生效。 必须重新启动 Kestrel 才能检测到对其环境所做的更改。

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

以下 launchSettings.json 文件包含多个配置文件：

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

可通过以下方式来选择配置文件：

* 在 Visual Studio UI 中。
* 在命令行界面中使用 [`dotnet run`](/dotnet/core/tools/dotnet-run) 命令，并将 `--launch-profile` 选项设置为配置文件名称。 这种方法只支持 Kestrel 配置文件。

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> launchSettings.json 不应存储机密。 [机密管理器工具](xref:security/app-secrets)可用于存储本地开发的机密。

使用 [Visual Studio Code](https://code.visualstudio.com/) 时，可以在 .vscode/launch.json 文件中设置环境变量。 下面的示例设置了多个[主机配置值环境变量](xref:fundamentals/host/web-host#host-configuration-values)：

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

.vscode/launch.json 文件仅由 Visual Studio Code 使用。

### <a name="production"></a>生产

生产环境应配置为最大限度地提高安全性、[性能](xref:performance/performance-best-practices)和应用可靠性。 不同于开发的一些通用设置包括：

* [缓存](xref:performance/caching/memory)。
* 客户端资源被捆绑和缩小，并可能从 CDN 提供。
* 已禁用诊断错误页。
* 已启用友好错误页。
* 已启用生产[记录](xref:fundamentals/logging/index)和监视。 例如，使用 [Application Insights](/azure/application-insights/app-insights-asp-net-core)。

## <a name="set-the-environment"></a>设置环境

通常，可以使用环境变量或平台设置来设置用于测试的特定环境。 如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。 设置环境的方法取决于操作系统。

构建主机时，应用读取的最后一个环境设置将决定应用的环境。 应用运行时无法更改应用的环境。

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)中的[“关于”页](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)显示 `IWebHostEnvironment.EnvironmentName` 的值。

### <a name="azure-app-service"></a>Azure 应用服务

若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：

1. 从“应用服务”边栏选项卡中选择应用。
1. 在“设置”组中，选择“配置”边栏选项卡 。
1. 在“应用程序设置”选项卡中，选择“新建应用程序设置”。 
1. 在“添加/编辑应用程序设置”窗口中，在“名称”中提供 `ASPNETCORE_ENVIRONMENT`。 在“值”中提供环境（例如 `Staging`）。
1. 交换部署槽位时，如果希望环境设置保持当前槽位，请选中“部署槽位设置”复选框。 有关详细信息，请参阅 Azure 文档中的[在 Azure 应用服务中设置过渡环境](/azure/app-service/web-sites-staged-publishing)。
1. 选择“确定”以关闭“添加/编辑应用程序设置”窗口。 
1. 选择“配置”边栏选项卡顶部的“保存” 。

在 Azure 门户中添加、更改或删除应用设置后，Azure 应用服务自动重启应用。

### <a name="windows"></a>Windows

launchSettings.json 中的环境值替代在系统环境中设置的值。

若要在使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动该应用时为当前会话设置 `ASPNETCORE_ENVIRONMENT`，则使用以下命令：

**命令提示符**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

前面的命令只为通过此命令窗口启动的进程设置 `ASPNETCORE_ENVIRONMENT`。

若要在 Windows 中全局设置值，请采用下列两种方法之一：

* 依次打开“控制面板”>“系统”>“高级系统设置”，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：

  ![系统高级属性](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 环境变量](environments/_static/windows_aspnetcore_environment.png)

* 打开管理命令提示符并运行 `setx` 命令，或打开管理 PowerShell 命令提示符并运行 `[Environment]::SetEnvironmentVariable`：

  **命令提示符**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  `/M` 开关指明，在系统一级设置环境变量。 如果未使用 `/M` 开关，就会为用户帐户设置环境变量。

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  `Machine` 选项值指明，在系统一级设置环境变量。 如果将选项值更改为 `User`，就会为用户帐户设置环境变量。

如果全局设置 `ASPNETCORE_ENVIRONMENT` 环境变量，它就会对在值设置后打开的任何命令窗口中对 `dotnet run` 起作用。 launchSettings.json 中的环境值替代在系统环境中设置的值。

**web.config**

若要使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”部分。

**项目文件或发布配置文件**

**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。 此方法在发布项目时设置 web.config 中的环境：

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**每个 IIS 应用程序池**

若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。 为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。

在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：

* 在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。
* 重新启动服务器。

#### <a name="macos"></a>macOS

设置 macOS 的当前环境可在运行应用时完成：

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

或者，在运行应用前使用 `export` 设置环境：

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量 。 使用任意文本编辑器编辑文件。 添加以下语句：

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

对于 Linux 发行版，在命令提示符处对基于会话的变量设置使用 `export` 命令，并对计算机级别环境设置使用 bash_profile 文件。

### <a name="set-the-environment-in-code"></a>使用代码设置环境

生成主机时，调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>。 请参阅 <xref:fundamentals/host/generic-host#environmentname>。

### <a name="configuration-by-environment"></a>按环境配置

若要按环境加载配置，请参阅 <xref:fundamentals/configuration/index#json-configuration-provider>。

## <a name="environment-based-startup-class-and-methods"></a>基于环境的 Startup 类和方法

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>将 IWebHostEnvironment 注入 Startup 类

将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入 `Startup` 构造函数。 当应用仅需为几个代码差异最小的环境配置 `Startup` 时，这种方法非常有用。

如下示例中：

* 环境保存在 `_env` 字段中。
* `_env` 在 `ConfigureServices` 和 `Configure` 中用于根据应用的环境应用启动配置。

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Startup 类约定

当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。 应用可以为不同的环境定义多个 `Startup` 类。 在运行时选择适当的 `Startup` 类。 优先考虑名称后缀与当前环境相匹配的类。 如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。 当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。 典型的应用不需要这种方法。

若要实现基于环境的 `Startup` 类，请创建 `Startup{EnvironmentName}` 类和回退 `Startup` 类：

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Startup 方法约定

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本。 当应用需要为多个环境（每个环境有许多代码差异）配置启动时，这种方法就非常有用：

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="environments"></a>环境

ASP.NET Core 在应用启动时读取环境变量 `ASPNETCORE_ENVIRONMENT`，并将该值存储在 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName) 中。 `ASPNETCORE_ENVIRONMENT` 可设置为任意值，但框架提供三个值：

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>（默认值）

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

前面的代码：

* 当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。
* 当 `ASPNETCORE_ENVIRONMENT` 的值设置为下列之一时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)：

  * `Staging`
  * `Production`
  * `Staging_2`

[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 `IHostingEnvironment.EnvironmentName` 的值来包含或排除元素中的标记：

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

在 Windows 和 macOS 上，环境变量和值不区分大小写。 默认情况下，Linux 环境变量和值区分大小写。

### <a name="development"></a>开发

开发环境可以启用不应该在生产中公开的功能。 例如，ASP.NET Core 模板在开发环境中启用了[开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)。

本地计算机开发环境可以在项目的 Properties\launchSettings.json 文件中设置。 在 launchSettings.json 中设置的环境值替代在系统环境中设置的值。

以下 JSON 显示 launchSettings.json 文件中的三个配置文件：

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> launchSettings.json 中的 `applicationUrl` 属性可指定服务器 URL 的列表。 在列表中的 URL 之间使用分号：
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时，使用具有 `"commandName": "Project"` 的第一个配置文件。 `commandName` 的值指定要启动的 Web 服务器。 `commandName` 可为以下任一项：

* `IISExpress`
* `IIS`
* `Project`（启动 Kestrel 的项目）

使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动应用时：

* 如果可用，读取 launchSettings.json。 launchSettings.json 中的 `environmentVariables` 设置会替代环境变量。
* 此时显示承载环境。

以下输出显示了使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动的应用：

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio 项目属性“调试”选项卡提供 GUI 来编辑 launchSettings.json 文件：

![项目属性设置环境变量](environments/_static/project-properties-debug.png)

在 Web 服务器重新启动之前，对项目配置文件所做的更改可能不会生效。 必须重新启动 Kestrel 才能检测到对其环境所做的更改。

> [!WARNING]
> launchSettings.json 不应存储机密。 [机密管理器工具](xref:security/app-secrets)可用于存储本地开发的机密。

使用 [Visual Studio Code](https://code.visualstudio.com/) 时，可以在 .vscode/launch.json 文件中设置环境变量。 以下示例将环境设置为 `Development`：

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

使用与 Properties/launchSettings.json 相同的方法通过 `dotnet run` 启动应用时，不读取项目中的 .vscode/launch.json 文件 。 在没有 launchSettings.json 文件的 Development 环境中启动应用时，需要使用环境变量设置环境或者将命令行参数设为 `dotnet run` 命令。

### <a name="production"></a>生产

Production 环境应配置为最大限度地提高安全性、性能和应用可靠性。 不同于开发的一些通用设置包括：

* 缓存。
* 客户端资源被捆绑和缩小，并可能从 CDN 提供。
* 已禁用诊断错误页。
* 已启用友好错误页。
* 已启用生产记录和监视。 例如，[Application Insights](/azure/application-insights/app-insights-asp-net-core)。

## <a name="set-the-environment"></a>设置环境

通常，可以使用环境变量或平台设置来设置用于测试的特定环境。 如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。 设置环境的方法取决于操作系统。

构建主机时，应用读取的最后一个环境设置将决定应用的环境。 应用运行时无法更改应用的环境。

### <a name="environment-variable-or-platform-setting"></a>环境变量或平台设置

#### <a name="azure-app-service"></a>Azure 应用服务

若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：

1. 从“应用服务”边栏选项卡中选择应用。
1. 在“设置”组中，选择“配置”边栏选项卡 。
1. 在“应用程序设置”选项卡中，选择“新建应用程序设置”。 
1. 在“添加/编辑应用程序设置”窗口中，在“名称”中提供 `ASPNETCORE_ENVIRONMENT`。 在“值”中提供环境（例如 `Staging`）。
1. 交换部署槽位时，如果希望环境设置保持当前槽位，请选中“部署槽位设置”复选框。 有关详细信息，请参阅 Azure 文档中的[在 Azure 应用服务中设置过渡环境](/azure/app-service/web-sites-staged-publishing)。
1. 选择“确定”以关闭“添加/编辑应用程序设置”窗口。 
1. 选择“配置”边栏选项卡顶部的“保存” 。

在 Azure 门户中添加、更改或删除应用设置（环境变量）后，Azure 应用服务自动重启应用。

#### <a name="windows"></a>Windows

若要在使用 [dotnet run](/dotnet/core/tools/dotnet-run) 启动该应用时为当前会话设置 `ASPNETCORE_ENVIRONMENT`，则使用以下命令：

**命令提示符**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

这些命令仅对当前窗口有效。 窗口关闭时，`ASPNETCORE_ENVIRONMENT` 设置将恢复为默认设置或计算机值。

若要在 Windows 中全局设置值，请采用下列两种方法之一：

* 依次打开“控制面板”>“系统”>“高级系统设置”，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：

  ![系统高级属性](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 环境变量](environments/_static/windows_aspnetcore_environment.png)

* 打开管理命令提示符并运行 `setx` 命令，或打开管理 PowerShell 命令提示符并运行 `[Environment]::SetEnvironmentVariable`：

  **命令提示符**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M` 开关指明，在系统一级设置环境变量。 如果未使用 `/M` 开关，就会为用户帐户设置环境变量。

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine` 选项值指明，在系统一级设置环境变量。 如果将选项值更改为 `User`，就会为用户帐户设置环境变量。

如果全局设置 `ASPNETCORE_ENVIRONMENT` 环境变量，它就会对在值设置后打开的任何命令窗口中对 `dotnet run` 起作用。

**web.config**

若要使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”部分。

**项目文件或发布配置文件**

**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 或项目文件中。 此方法在发布项目时设置 web.config 中的环境：

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**每个 IIS 应用程序池**

若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。 为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。

> [!IMPORTANT]
> 在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：
>
> * 在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。
> * 重新启动服务器。

#### <a name="macos"></a>macOS

设置 macOS 的当前环境可在运行应用时完成：

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

或者，在运行应用前使用 `export` 设置环境：

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量 。 使用任意文本编辑器编辑文件。 添加以下语句：

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

对于 Linux 发行版，在命令提示符处对基于会话的变量设置使用 `export` 命令，并对计算机级别环境设置使用 bash_profile 文件。

### <a name="set-the-environment-in-code"></a>使用代码设置环境

生成主机时，调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>。 请参阅 <xref:fundamentals/host/web-host#environment>。

### <a name="configuration-by-environment"></a>按环境配置

若要按环境加载配置，我们建议：

* appsettings 文件 (appsettings.Environment>.json) 。 请参阅 <xref:fundamentals/configuration/index#json-configuration-provider>。
* 环境变量（在托管应用的每个系统上进行设置）。 请参见 <xref:fundamentals/host/web-host#environment> 和 <xref:security/app-secrets#environment-variables>。
* 密码管理器（仅限开发环境中）。 请参阅 <xref:security/app-secrets>。

## <a name="environment-based-startup-class-and-methods"></a>基于环境的 Startup 类和方法

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>将 IHostingEnvironment 注入 Startup.Configure

将 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 注入 `Startup.Configure`。 当应用仅需为几个代码差异最小的环境配置 `Startup.Configure` 时，这种方法非常有用。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>将 IHostingEnvironment 注入 Startup 类

将 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 注入 `Startup` 构造函数，并将服务分配给一个字段，以便在整个 `Startup` 类中使用。 当应用需为几个代码差异最小的环境配置启动时，这种方法非常有用。

如下示例中：

* 环境保存在 `_env` 字段中。
* `_env` 在 `ConfigureServices` 和 `Configure` 中用于根据应用的环境应用启动配置。

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a>Startup 类约定

当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。 应用可以为不同的环境定义单独的 `Startup` 类（例如 `StartupDevelopment`）。 在运行时选择适当的 `Startup` 类。 优先考虑名称后缀与当前环境相匹配的类。 如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。 当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。

若要实现基于环境的 `Startup` 类，请为使用中的每个环境创建 `Startup{EnvironmentName}` 类，并创建回退 `Startup` 类：

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Startup 方法约定

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本。 当应用需要为各环境之间存在许多代码差异的多个环境配置启动时，这种方法非常有用。

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
