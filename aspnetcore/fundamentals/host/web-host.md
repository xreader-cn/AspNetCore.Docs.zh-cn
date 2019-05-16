---
title: ASP.NET Core Web 主机
author: guardrex
description: 了解有关 ASP.NET Core 中负责应用启动和生存期管理的 Web 主机。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: 48f3b664d901bdfb27cdf9e798fa60c0587d1def
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610291"
---
# <a name="aspnet-core-web-host"></a>ASP.NET Core Web 主机

作者：[Luke Latham](https://github.com/guardrex)

ASP.NET Core 应用配置和启动“主机”。 主机负责应用程序启动和生存期管理。 至少，主机配置服务器和请求处理管道。 主机还可以设置日志记录、依赖关系注入和配置。

::: moniker range="<= aspnetcore-1.1"

对于本主题的 1.1 版本，请下载 [ASP.NET Core Web 主机（版本 1.1，PDF）](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf)。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

本文介绍了 ASP.NET Core Web 主机 (<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>)，该主机用于托管 Web 应用。 有关 .NET 泛型主机 ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)) 的信息，请参阅 <xref:fundamentals/host/generic-host>。

::: moniker-end

::: moniker range="> aspnetcore-2.2"

本文介绍了 ASP.NET Core Web 主机 ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder))。 在 ASP.NET Core 3.0 中，泛型主机替代了 Web 主机。 有关详细信息，请参阅[主机](xref:fundamentals/index#host)。

::: moniker-end

## <a name="set-up-a-host"></a>设置主机

创建使用 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 实例的主机。 通常在应用的入口点来执行 `Main` 方法。 生成器方法名称 `CreateWebHostBuilder` 是向外部组件（如[实体框架](/ef/core/)）标识生成器方法的特殊名称。

在项目模板中，`Main` 位于 Program.cs。 典型应用调用 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 来开始创建主机：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

`CreateDefaultBuilder` 执行下列任务：

* 使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器。 有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。
* 将内容根设置为由 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory) 返回的路径。
* 通过以下对象加载[主机配置](#host-configuration-values)：
  * 前缀为 `ASPNETCORE_` 的环境变量（例如，`ASPNETCORE_ENVIRONMENT`）。
  * 命令行参数。
* 按以下顺序加载应用配置：
  * appsettings.json。
  * appsettings.{Environment}.json。
  * 应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。
  * 环境变量。
  * 命令行参数。
* 配置控制台和调试输出的[日志记录](xref:fundamentals/logging/index)。 日志记录包含 appsettings.json 或 appsettings.{Environment}.json 文件的日志记录配置部分中指定的[日志筛选](xref:fundamentals/logging/index#log-filtering)规则。
* 使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)在 IIS 后面运行时，`CreateDefaultBuilder` 会启用 [IIS 集成](xref:host-and-deploy/iis/index)，这会配置应用的基址和端口。 IIS 集成还配置应用以[捕获启动错误](#capture-startup-errors)。 有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。
* 如果应用环境为“开发”，请将 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) 设为 `true`。 有关详细信息，请参阅[作用域验证](#scope-validation)。

[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)、[ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) 以及 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 的其他方法和扩展方法可重写和增强 `CreateDefaultBuilder` 定义的配置。 下面是一些示例：

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) 用于指定应用的其他 `IConfiguration`。 下面的 `ConfigureAppConfiguration` 调用添加委托，以在 appsettings.xml 文件中添加应用配置。 可多次调用 `ConfigureAppConfiguration`。 请注意，此配置不适用于主机（例如，服务器 URL 或环境）。 请参阅[主机配置值](#host-configuration-values)部分。

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* 下面的 `ConfigureLogging` 调用添加委托，以将最小日志记录级别 ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) 配置为 [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel)。 此设置重写 `CreateDefaultBuilder` 在 appsettings.Development.json 和 appsettings.Production.json 中配置的设置，分别为 `LogLevel.Debug` 和 `LogLevel.Error`。 可多次调用 `ConfigureLogging`。

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* 下面调用 `ConfigureKestrel` 来重写 `CreateDefaultBuilder` 在配置 Kestrel 时建立的 30,000,000 字节默认 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)：

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* 下面调用 [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) 来重写 `CreateDefaultBuilder` 在配置 Kestrel 时建立的 30,000,000 字节默认 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)：

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

内容根确定主机搜索内容文件（如 MVC 视图文件）的位置。 应用从项目的根文件夹启动时，会将项目的根文件夹用作内容根。 这是 [Visual Studio](https://visualstudio.microsoft.com) 和 [dotnet new 模板](/dotnet/core/tools/dotnet-new)中使用的默认值。

有关应用配置的详细信息，请参阅 <xref:fundamentals/configuration/index>。

> [!NOTE]
> 作为使用静态 `CreateDefaultBuilder` 方法的替代方法，从 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 创建主机是一种受 ASP.NET Core 2.x 支持的方法。 有关详细信息，请参阅 ASP.NET Core 1.x 选项卡。

设置主机时，可以提供[配置](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1)和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) 方法。 如果指定 `Startup` 类，必须定义 `Configure` 方法。 有关更多信息，请参见<xref:fundamentals/startup>。 多次调用 `ConfigureServices` 将追加到另一个。 多次调用 `WebHostBuilder` 上的 `Configure` 或 `UseStartup` 将替换以前的设置。

## <a name="host-configuration-values"></a>主机配置值

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 依赖于以下的方法设置主机配置值：

* 主机生成器配置，其中包括格式 `ASPNETCORE_{configurationKey}` 的环境变量。 例如 `ASPNETCORE_ENVIRONMENT`。
* [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) 和 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 等扩展（请参阅[重写配置](#override-configuration)部分）。
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) 和关联键。 使用 `UseSetting` 设置值时，该值设置为无论何种类型的字符串。

主机使用任何一个选项设置上一个值。 有关详细信息，请参阅下一部分中的[重写配置](#override-configuration)。

### <a name="application-key-name"></a>应用程序键（名称）

在主机构造期间调用 [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 或 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) 时，会自动设置 [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) 属性。 该值设置为包含应用入口点的程序集的名称。 要显式设置值，请使用 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)：

**密钥**：applicationName  
**类型**：string  
**默认值**：包含应用入口点的程序集的名称。  
**设置使用**：`UseSetting`  
**环境变量**：`ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>捕获启动错误

此设置控制启动错误的捕获。

**键**：captureStartupErrors  
**类型**：布尔型（`true` 或 `1`）  
**默认值**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。  
**设置使用**：`CaptureStartupErrors`  
**环境变量**：`ASPNETCORE_CAPTURESTARTUPERRORS`

当 `false` 时，启动期间出错导致主机退出。 当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>内容根

此设置确定 ASP.NET Core 开始搜索内容文件，如 MVC 视图等。 

**键**：contentRoot  
**类型**：string  
**默认值**：默认为应用程序集所在的文件夹。  
**设置使用**：`UseContentRoot`  
**环境变量**：`ASPNETCORE_CONTENTROOT`

内容根也用作 [Web 根设置](#web-root)的基路径。 如果路径不存在，主机将无法启动。

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a>详细错误

确定是否应捕获详细错误。

**键**：detailedErrors  
**类型**：布尔型（`true` 或 `1`）  
**默认值**：false  
**设置使用**：`UseSetting`  
**环境变量**：`ASPNETCORE_DETAILEDERRORS`

启用（或当<a href="#environment">环境</a>设置为 `Development` ）时，应用捕获详细的异常。

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>环境

设置应用的环境。

**键**：环境  
**类型**：string  
**默认值**：生产  
**设置使用**：`UseEnvironment`  
**环境变量**：`ASPNETCORE_ENVIRONMENT`

环境可以设置为任何值。 框架定义的值包括 `Development``Staging` 和 `Production`。 值不区分大小写。 默认情况下，从 `ASPNETCORE_ENVIRONMENT` 环境变量读取环境。 使用 [Visual Studio](https://visualstudio.microsoft.com) 时，可能会在 launchSettings.json 文件中设置环境变量。 有关更多信息，请参见<xref:fundamentals/environments>。

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>承载启动程序集

设置应用的承载启动程序集。

**键**：hostingStartupAssemblies  
**类型**：string  
**默认值**：空字符串  
**设置使用**：`UseSetting`  
**环境变量**：`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

承载启动程序集的以分号分隔的字符串在启动时加载。

虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。 提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>HTTPS 端口

设置 HTTPS 重定向端口。 用于[强制实施 HTTPS](xref:security/enforcing-ssl)。

键：https_port；类型：字符串；
默认值：未设置默认值。
设置使用：`UseSetting`
环境变量：`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>承载启动排除程序集

承载启动程序集的以分号分隔的字符串在启动时排除。

键：hostingStartupExcludeAssemblies  
**类型**：string  
**默认值**：空字符串  
**设置使用**：`UseSetting`  
**环境变量**：`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>首选承载 URL

指示主机是否应该侦听使用 `WebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。

**键**：preferHostingUrls  
**类型**：布尔型（`true` 或 `1`）  
**默认值**：true  
**设置使用**：`PreferHostingUrls`  
**环境变量**：`ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>阻止承载启动

阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。 有关更多信息，请参见<xref:fundamentals/configuration/platform-specific-configuration>。

**键**：preventHostingStartup  
**类型**：布尔型（`true` 或 `1`）  
**默认值**：false  
**设置使用**：`UseSetting`  
**环境变量**：`ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>服务器 URL

指示 IP 地址或主机地址，其中包含服务器应针对请求侦听的端口和协议。

**键**：urls  
**类型**：string  
**默认**： http://localhost:5000  
**设置使用**：`UseUrls`  
**环境变量**：`ASPNETCORE_URLS`

设置为服务器应响应的以分号分隔 (;) 的 URL 前缀列表。 例如 `http://localhost:123`。 使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。 协议（`http://` 或 `https://`）必须包含每个 URL。 不同的服务器支持的格式有所不同。

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Kestrel 具有自己的终结点配置 API。 有关更多信息，请参见<xref:fundamentals/servers/kestrel#endpoint-configuration>。

### <a name="shutdown-timeout"></a>关闭超时

指定等待 Web 主机关闭的时长。

**键**：shutdownTimeoutSeconds  
**类型**：int  
**默认值**：5  
**设置使用**：`UseShutdownTimeout`  
**环境变量**：`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

虽然键使用 `UseSetting` 接受 int（例如 `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`），但是 [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 扩展方法采用 [TimeSpan](/dotnet/api/system.timespan)。

在超时时间段中，托管：

* 触发器 [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)。
* 尝试停止托管服务，对服务停止失败的任何错误进行日志记录。

如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。 即使没有完成处理工作，服务也会停止。 如果停止服务需要额外的时间，请增加超时时间。

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>启动程序集

确定要在其中搜索 `Startup` 类的程序集。

**键**：startupAssembly  
**类型**：string  
**默认值**：应用的程序集  
**设置使用**：`UseStartup`  
**环境变量**：`ASPNETCORE_STARTUPASSEMBLY`

按名称（`string`）或类型（`TStartup`）的程序集可以引用。 如果调用多个 `UseStartup` 方法，优先选择最后一个方法。

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Web 根路径

设置应用的静态资产的相对路径。

**键**：webroot  
**类型**：string  
**默认值**：如果未指定，默认值是“(Content Root)/wwwroot”（如果该路径存在）。 如果该路径不存在，则使用无操作文件提供程序。  
**设置使用**：`UseWebRoot`  
**环境变量**：`ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a>重写配置

使用[配置](xref:fundamentals/configuration/index)可以配置 Web 主机。 在下面的示例中，主机配置是根据需要在 hostsettings.json 文件中指定。 命令行参数可能会重写从 hostsettings.json 文件加载的任何配置。 生成的配置（在 `config` 中）用于通过 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 配置主机。 `IWebHostBuilder` 配置会添加到应用配置中，但反之不亦然&mdash;`ConfigureAppConfiguration` 不影响 `IWebHostBuilder` 配置。

先用 hostsettings.json config 重写 `UseUrls` 提供的配置，再用命令行参数 config：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

hostsettings.json：

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 扩展方法当前不能分析由 `GetSection` 返回的配置部分（例如 `.UseConfiguration(Configuration.GetSection("section"))`。 `GetSection` 方法将配置键筛选到所请求的部分，但将节名称保留在键上（例如 `section:urls`、`section:environment`）。 `UseConfiguration` 方法需要键来匹配 `WebHostBuilder` 键（例如 `urls`、`environment`）。 键上存在的节名称阻止节的值配置主机。 将在即将发布的版本中解决此问题。 有关详细信息和解决方法，请参阅[将配置节传入到 WebHostBuilder.UseConfiguration 使用完整的键](https://github.com/aspnet/Hosting/issues/839)。
>
> `UseConfiguration` 只将所提供的 `IConfiguration` 中的密钥复制到主机生成器配置中。 因此，JSON、INI 和 XML 设置文件的设置 `reloadOnChange: true` 没有任何影响。

若要指定在特定的 URL 上运行的主机，所需的值可以在执行 [dotnet 运行](/dotnet/core/tools/dotnet-run)时从命令提示符传入。 命令行参数重写 hostsettings.json 文件中的 `urls` 值，且服务器侦听端口 8080：

```console
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>管理主机

**运行**

`Run` 方法启动 Web 应用并阻止调用线程，直到关闭主机：

```csharp
host.Run();
```

**Start**

通过调用 `Start` 方法以非阻止方式运行主机：

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

如果 URL 列表传递给 `Start` 方法，该列表侦听指定的 URL：

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

应用可以使用通过静态便捷方法预配置的 `CreateDefaultBuilder` 默认值初始化并启动新的主机。 这些方法在没有控制台输出的情况下启动服务器，并使用 [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) 等待中断（Ctrl-C/SIGINT 或 SIGTERM）：

**Start(RequestDelegate app)**

从 `RequestDelegate` 开始：

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

在浏览器中向 `http://localhost:5000` 发出请求，接收响应“Hello World!” `WaitForShutdown` 受到阻止，直到发出中断（Ctrl-C/SIGINT 或 SIGTERM）。 应用显示 `Console.WriteLine` 消息并等待 keypress 退出。

**Start(string url, RequestDelegate app)**

从 URL 和 `RequestDelegate` 开始：

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

生成与 Start(RequestDelegate app) 相同的结果，除非应用在 `http://localhost:8080` 上响应。

**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**

使用 `IRouteBuilder` 的实例 ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) 用于路由中间件：

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

该示例中使用以下浏览器请求：

| 请求                                    | 响应                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Hello, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | 使用“ooops!”字符串引发异常 |
| `http://localhost:5000/throw`              | 使用“Uh oh!”字符串引发异常 |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Hello World!                             |

`WaitForShutdown` 受到阻止，直到发出中断（Ctrl-C/SIGINT 或 SIGTERM）。 应用显示 `Console.WriteLine` 消息并等待 keypress 退出。

**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**

使用 URL 和 `IRouteBuilder` 实例：

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

生成与 Start(Action&lt;IRouteBuilder&gt; routeBuilder) 相同的结果，除非应用在 `http://localhost:8080` 上响应。

**StartWith(Action&lt;IApplicationBuilder&gt; app)**

提供委托以配置 `IApplicationBuilder`：

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

在浏览器中向 `http://localhost:5000` 发出请求，接收响应“Hello World!” `WaitForShutdown` 受到阻止，直到发出中断（Ctrl-C/SIGINT 或 SIGTERM）。 应用显示 `Console.WriteLine` 消息并等待 keypress 退出。

**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**

提供 URL 和委托以配置 `IApplicationBuilder`：

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

生成与 StartWith(Action&lt;IApplicationBuilder&gt; app) 相同的结果，除非应用在 `http://localhost:8080` 上响应。

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment 接口

[IHostingEnvironment 接口](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)提供有关应用的 Web 承载环境的信息。 使用[构造函数注入](xref:fundamentals/dependency-injection)获取 `IHostingEnvironment` 以使用其属性和扩展方法：

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

[基于约定的方法](xref:fundamentals/environments#environment-based-startup-class-and-methods)可以用于在启动时基于环境配置应用。 或者，将 `IHostingEnvironment` 注入到 `Startup` 构造函数用于 `ConfigureServices`：

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> 除了 `IsDevelopment` 扩展方法，`IHostingEnvironment` 提供 `IsStaging`、`IsProduction` 和 `IsEnvironment(string environmentName)` 方法。 有关更多信息，请参见<xref:fundamentals/environments>。

`IHostingEnvironment` 服务还可以直接注入到 `Configure` 方法以设置处理管道：

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

创建自定义[中间件](xref:fundamentals/middleware/write)时可以将 `IHostingEnvironment` 注入 `Invoke` 方法：

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime 接口

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) 允许后启动和关闭活动。 接口上的三个属性是用于注册 `Action` 方法（用于定义启动和关闭事件）的取消标记。

| 取消标记    | 触发条件 |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | 主机已完全启动。 |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | 主机正在完成正常关闭。 应处理所有请求。 关闭受到阻止，直到完成此事件。 |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | 主机正在执行正常关闭。 仍在处理请求。 关闭受到阻止，直到完成此事件。 |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) 请求应用终止。 以下类在调用类的 `Shutdown` 方法时使用 `StopApplication` 正常关闭应用：

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

## <a name="scope-validation"></a>作用域验证

如果应用环境为“开发”，则 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 将 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) 设为 `true`。

若将 `ValidateScopes` 设为 `true`，默认服务提供程序会执行检查来验证以下内容：

* 没有从根服务提供程序直接或间接解析到有作用域的服务。
* 未将有作用域的服务直接或间接注入到单一实例。

调用 [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) 时，会创建根服务提供程序。 在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。

有作用域的服务由创建它们的容器释放。 如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。 验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。

若要始终验证作用域（包括在生存环境中验证），请使用主机生成器上的 [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) 配置 [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions)：

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>其他资源

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
