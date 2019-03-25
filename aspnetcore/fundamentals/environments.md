---
title: 在 ASP.NET Core 中使用多个环境
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中控制多个环境的应用行为。
ms.author: riande
ms.date: 01/22/2019
uid: fundamentals/environments
ms.openlocfilehash: 5982f3e51a68dfa29af482067156c42006f50c0c
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208444"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>在 ASP.NET Core 中使用多个环境

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 基于使用环境变量的运行时环境配置应用行为。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="environments"></a>环境

ASP.NET Core 在应用启动时读取环境变量 `ASPNETCORE_ENVIRONMENT`，并将该值存储在 [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname) 中。 `ASPNETCORE_ENVIRONMENT` 可设置为任意值，但框架支持[三个值](/dotnet/api/microsoft.aspnetcore.hosting.environmentname)：[Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development)、[Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) 和 [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production)。 如果未设置 `ASPNETCORE_ENVIRONMENT`，则默认为 `Production`。

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

前面的代码：

* 当 `ASPNETCORE_ENVIRONMENT` 设置为 `Development` 时，调用 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)。
* 当 `ASPNETCORE_ENVIRONMENT` 的值设置为下列之一时，调用 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)：

  * `Staging`
  * `Production`
  * `Staging_2`

[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)使用 `IHostingEnvironment.EnvironmentName` 的值来包含或排除元素中的标记：

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

在 Windows 和 macOS 上，环境变量和值不区分大小写。 默认情况下，Linux 环境变量和值要区分大小写。

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

::: moniker range=">= aspnetcore-2.1"

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

::: moniker-end

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

使用与 Properties/launchSettings.json 相同的方法通过 `dotnet run` 启动应用时，不读取项目中的 .vscode/launch.json 文件。 在没有 launchSettings.json 文件的 Development 环境中启动应用时，需要使用环境变量设置环境或者将命令行参数设为 `dotnet run` 命令。

### <a name="production"></a>生产

Production 环境应配置为最大限度地提高安全性、性能和应用可靠性。 不同于开发的一些通用设置包括：

* 缓存。
* 客户端资源被捆绑和缩小，并可能从 CDN 提供。
* 已禁用诊断错误页。
* 已启用友好错误页。
* 已启用生产记录和监视。 例如，[Application Insights](/azure/application-insights/app-insights-asp-net-core)。

## <a name="set-the-environment"></a>设置环境

为测试设置特定环境通常很有用。 如果未设置环境，默认值为 `Production`，这会禁用大多数调试功能。 设置环境的方法取决于操作系统。

### <a name="azure-app-service"></a>Azure 应用服务

若要在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中设置环境，请执行以下步骤：

1. 从“应用服务”边栏选项卡中选择应用。
1. 在“设置”组中，选择“应用程序设置”边栏选项卡。
1. 在“应用程序设置”区域中，选择“添加新设置”。
1. 在“输入名称”中提供 `ASPNETCORE_ENVIRONMENT`。 在“输入值”中提供环境（例如 `Staging`）。
1. 交换部署槽位时，如果希望环境设置保持当前槽位，请选中“槽位设置”复选框。 有关详细信息，请参阅 [Azure 文档：交换哪些设置？](/azure/app-service/web-sites-staged-publishing)。
1. 选择边栏选项卡顶部的“保存”。

在 Azure 门户中添加、更改或删除应用设置（环境变量）后，Azure 应用服务自动重启应用。

### <a name="windows"></a>Windows

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

* 依次打开“控制面板” > “系统” > “高级系统设置”，再添加或编辑“`ASPNETCORE_ENVIRONMENT`”值：

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

若要使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量，请参阅 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>的“设置环境变量”部分。 使用 web.config 设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。

::: moniker range=">= aspnetcore-2.2"

**项目文件或发布配置文件**

**对于 Windows IIS 部署：** 将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml）或项目文件中。 此方法在发布项目时设置 web.config 中的环境：

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

**每个 IIS 应用程序池**

若要为在独立应用池中运行的应用设置 `ASPNETCORE_ENVIRONMENT` 环境变量（IIS 10.0 或更高版本支持此操作），请参阅[环境变量 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。 为应用池设置 `ASPNETCORE_ENVIRONMENT` 环境变量后，它的值会替代系统级设置。

> [!IMPORTANT]
> 在 IIS 中托管应用并添加或更改 `ASPNETCORE_ENVIRONMENT` 环境变量时，请采用下列方法之一，让新值可供应用拾取：
>
> * 在命令提示符处依次执行 `net stop was /y` 和 `net start w3svc`。
> * 重启服务器。

### <a name="macos"></a>macOS

设置 macOS 的当前环境可在运行应用时完成：

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

或者，在运行应用前使用 `export` 设置环境：

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

在 .bashrc 或 .bash_profile 文件中设置计算机级环境变量。 使用任意文本编辑器编辑文件。 添加以下语句：

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a>Linux

对于 Linux 发行版，请在命令提示符中使用 `export` 命令进行基于会话的变量设置，并使用 bash_profile 文件进行计算机级环境设置。

### <a name="configuration-by-environment"></a>按环境配置

若要按环境加载配置，我们建议：

* appsettings 文件 (appsettings.\<Environment>.json)。 请参阅[配置：文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)。
* 环境变量（在托管应用的每个系统上进行设置）。 请参阅[配置：文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)和[开发环境中应用密码的安全存储：环境变量](xref:security/app-secrets#environment-variables)。
* 密码管理器（仅限开发环境中）。 请参阅 <xref:security/app-secrets>。

## <a name="environment-based-startup-class-and-methods"></a>基于环境的 Startup 类和方法

### <a name="startup-class-conventions"></a>Startup 类约定

当 ASP.NET Core 应用启动时，[Startup 类](xref:fundamentals/startup)启动应用。 应用可以为不同的环境单独定义 `Startup` 类（例如，`StartupDevelopment`），相应 `Startup` 类会在运行时得到选择。 优先考虑名称后缀与当前环境相匹配的类。 如果找不到匹配的 `Startup{EnvironmentName}`，就会使用 `Startup` 类。

若要实现基于环境的 `Startup` 类，请为使用中的每个环境创建 `Startup{EnvironmentName}` 类，并创建回退 `Startup` 类：

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

使用接受程序集名称的 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 重载：

::: moniker range=">= aspnetcore-2.1"

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

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    CreateWebHost(args).Run();
}

public static IWebHost CreateWebHost(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName)
        .Build();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

        var host = new WebHostBuilder()
            .UseStartup(assemblyName)
            .Build();

        host.Run();
    }
}
```

::: moniker-end

### <a name="startup-method-conventions"></a>Startup 方法约定

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) 支持窗体 `Configure<EnvironmentName>` 和 `Configure<EnvironmentName>Services` 的环境特定版本：

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,51)]

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
