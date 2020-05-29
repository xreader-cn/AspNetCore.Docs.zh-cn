---
<span data-ttu-id="52609-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-102">'Blazor'</span></span>
- <span data-ttu-id="52609-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-103">'Identity'</span></span>
- <span data-ttu-id="52609-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-105">'Razor'</span></span>
- <span data-ttu-id="52609-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="52609-107">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="52609-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="52609-108">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="52609-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52609-109">ASP.NET Core 中的配置是使用一个或多个[配置提供程序](#cp)执行的。</span><span class="sxs-lookup"><span data-stu-id="52609-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="52609-110">配置提供程序使用各种配置源从键值对读取配置数据：</span><span class="sxs-lookup"><span data-stu-id="52609-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="52609-111">设置文件，例如 appsettings.json</span><span class="sxs-lookup"><span data-stu-id="52609-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="52609-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="52609-112">Environment variables</span></span>
* <span data-ttu-id="52609-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52609-113">Azure Key Vault</span></span>
* <span data-ttu-id="52609-114">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="52609-114">Azure App Configuration</span></span>
* <span data-ttu-id="52609-115">命令行参数</span><span class="sxs-lookup"><span data-stu-id="52609-115">Command-line arguments</span></span>
* <span data-ttu-id="52609-116">已安装或已创建的自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="52609-117">目录文件</span><span class="sxs-lookup"><span data-stu-id="52609-117">Directory files</span></span>
* <span data-ttu-id="52609-118">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="52609-118">In-memory .NET objects</span></span>

<span data-ttu-id="52609-119">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="52609-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="52609-120">默认配置</span><span class="sxs-lookup"><span data-stu-id="52609-120">Default configuration</span></span>

<span data-ttu-id="52609-121">通过 [dotnet new](/dotnet/core/tools/dotnet-new) 或 Visual Studio 创建的 ASP.NET Core Web 应用会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="52609-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="52609-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="52609-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="52609-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource)：添加现有 `IConfiguration` 作为源。</span><span class="sxs-lookup"><span data-stu-id="52609-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="52609-124">在默认配置示例中，添加[主机](#hvac)配置，并将它设置为应用配置的第一个源。</span><span class="sxs-lookup"><span data-stu-id="52609-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="52609-125">使用 [JSON 配置提供程序](#file-configuration-provider)通过 [appsettings.json](#appsettingsjson) 提供。</span><span class="sxs-lookup"><span data-stu-id="52609-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="52609-126">使用 [JSON 配置提供程序](#file-configuration-provider)通过 appsettings.`Environment`.json 提供 。</span><span class="sxs-lookup"><span data-stu-id="52609-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="52609-127">例如，appsettings.Production.json 和 appsettings.Development.json 。</span><span class="sxs-lookup"><span data-stu-id="52609-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="52609-128">应用在 `Development` 环境中运行时的[应用机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="52609-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="52609-129">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="52609-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="52609-130">使用[命令行配置提供程序](#command-line)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="52609-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="52609-131">后来添加的配置提供程序会替代之前的密钥设置。</span><span class="sxs-lookup"><span data-stu-id="52609-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="52609-132">例如，如果在 appsettings.json 和环境中设置了 `MyKey`，则会使用环境值。</span><span class="sxs-lookup"><span data-stu-id="52609-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="52609-133">使用默认配置提供程序，[命令行配置提供程序](#command-line-configuration-provider)将替代所有其他的提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="52609-134">若要详细了解 `CreateDefaultBuilder`，请参阅[默认生成器设置](xref:fundamentals/host/generic-host#default-builder-settings)。</span><span class="sxs-lookup"><span data-stu-id="52609-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="52609-135">以下代码按添加顺序显示了已启用的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="52609-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="52609-136">appsettings.json</span></span>

<span data-ttu-id="52609-137">请考虑使用以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="52609-138">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="52609-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-139">默认的 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 会按以下顺序加载配置：</span><span class="sxs-lookup"><span data-stu-id="52609-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="52609-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="52609-140">*appsettings.json*</span></span>
1. <span data-ttu-id="52609-141">appsettings.`Environment`.json ：例如，appsettings.Production.json 和 appsettings.Development.json  文件。</span><span class="sxs-lookup"><span data-stu-id="52609-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="52609-142">文件的环境版本是根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载的。</span><span class="sxs-lookup"><span data-stu-id="52609-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="52609-143">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="52609-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="52609-144">appsettings.`Environment`.json 值将替代 appsettings.json 中的密钥  。</span><span class="sxs-lookup"><span data-stu-id="52609-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="52609-145">例如，默认情况下：</span><span class="sxs-lookup"><span data-stu-id="52609-145">For example, by default:</span></span>

* <span data-ttu-id="52609-146">在开发环境中，appsettings.Development.json 配置将覆盖在 appsettings.json 中找到的值 。</span><span class="sxs-lookup"><span data-stu-id="52609-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="52609-147">在生产环境中，appsettings.Production.json 配置将覆盖在 appsettings.json 中找到的值 。</span><span class="sxs-lookup"><span data-stu-id="52609-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="52609-148">例如，在将应用部署到 Azure 时。</span><span class="sxs-lookup"><span data-stu-id="52609-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="52609-149">使用选项模式绑定分层配置数据</span><span class="sxs-lookup"><span data-stu-id="52609-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="52609-150">使用[默认](#default)配置，会通过 [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) 启用 appsettings.json 和 appsettings.`Environment`.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="52609-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="52609-151">应用启动后，对 appsettings.json 和 appsettings.`Environment`.json 文件做出的更改将由 [JSON 配置提供程序](#jcp)读取  。</span><span class="sxs-lookup"><span data-stu-id="52609-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="52609-152">有关添加其他 JSON 配置文件的信息，请参阅本文档中的 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="52609-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="52609-153">安全和机密管理器</span><span class="sxs-lookup"><span data-stu-id="52609-153">Security and secret manager</span></span>

<span data-ttu-id="52609-154">配置数据指南：</span><span class="sxs-lookup"><span data-stu-id="52609-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="52609-155">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="52609-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="52609-156">[机密管理器](xref:security/app-secrets)可用于存储开发环境中的机密。</span><span class="sxs-lookup"><span data-stu-id="52609-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="52609-157">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="52609-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="52609-158">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="52609-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="52609-159">[默认情况下](#default)，[机密管理器](xref:security/app-secrets)会在 appsettings.json 和 appsettings.`Environment`.json 之后读取配置设置  。</span><span class="sxs-lookup"><span data-stu-id="52609-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="52609-160">有关存储密码或其他敏感数据的详细信息：</span><span class="sxs-lookup"><span data-stu-id="52609-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="52609-161"><xref:security/app-secrets>：包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="52609-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="52609-162">机密管理器使用[文件配置提供程序](#fcp)将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="52609-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="52609-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="52609-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="52609-164">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="52609-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="52609-165">环境变量</span><span class="sxs-lookup"><span data-stu-id="52609-165">Environment variables</span></span>

<span data-ttu-id="52609-166">使用[默认](#default)配置，<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 会在读取 appsettings.json、appsettings.`Environment`.json 和[机密管理器](xref:security/app-secrets)后从环境变量键值对加载配置  。</span><span class="sxs-lookup"><span data-stu-id="52609-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="52609-167">因此，从环境中读取的键值会替代从 appsettings.json、appsettings.`Environment`.json 和机密管理器中读取的值  。</span><span class="sxs-lookup"><span data-stu-id="52609-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="52609-168">以下 `set` 命令：</span><span class="sxs-lookup"><span data-stu-id="52609-168">The following `set` commands:</span></span>

* <span data-ttu-id="52609-169">在 Windows 上设置[上述示例](#appsettingsjson)的环境键和值。</span><span class="sxs-lookup"><span data-stu-id="52609-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="52609-170">在使用[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)时测试设置。</span><span class="sxs-lookup"><span data-stu-id="52609-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="52609-171">`dotnet run` 命令必须在项目目录中运行。</span><span class="sxs-lookup"><span data-stu-id="52609-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="52609-172">前面的环境设置：</span><span class="sxs-lookup"><span data-stu-id="52609-172">The preceding environment settings:</span></span>

* <span data-ttu-id="52609-173">仅在进程中设置，这些进程是从设置进程的命令窗口启动的。</span><span class="sxs-lookup"><span data-stu-id="52609-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="52609-174">不会由通过 Visual Studio 启动的浏览器读取。</span><span class="sxs-lookup"><span data-stu-id="52609-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="52609-175">以下 [setx](/windows-server/administration/windows-commands/setx) 命令可用于在 Windows 上设置环境键和值。</span><span class="sxs-lookup"><span data-stu-id="52609-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="52609-176">与 `set` 不同，`setx` 设置是持久的。</span><span class="sxs-lookup"><span data-stu-id="52609-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="52609-177">`/M` 在系统环境中设置变量。</span><span class="sxs-lookup"><span data-stu-id="52609-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="52609-178">如果未使用 `/M` 开关，则会设置用户环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="52609-179">测试前面的命令是否会替代 appsettings.json 和 appsettings.`Environment`.json：  </span><span class="sxs-lookup"><span data-stu-id="52609-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="52609-180">使用 Visual Studio：退出并重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="52609-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="52609-181">使用 CLI：启动新的命令窗口并输入 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="52609-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="52609-182">使用字符串调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 以指定环境变量的前缀：</span><span class="sxs-lookup"><span data-stu-id="52609-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="52609-183">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="52609-183">In the preceding code:</span></span>

* <span data-ttu-id="52609-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` 被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="52609-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="52609-185">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="52609-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="52609-186">使用 `MyCustomPrefix_` 前缀设置的环境变量将替代[默认配置提供程序](#default)。</span><span class="sxs-lookup"><span data-stu-id="52609-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="52609-187">这包括没有前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="52609-188">前缀会在读取配置键值对时被去除。</span><span class="sxs-lookup"><span data-stu-id="52609-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="52609-189">以下命令对自定义前缀进行测试：</span><span class="sxs-lookup"><span data-stu-id="52609-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="52609-190">[默认配置](#default)会加载前缀为 `DOTNET_` 和 `ASPNETCORE_` 的环境变量和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="52609-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="52609-191">`DOTNET_` 和 `ASPNETCORE_` 前缀会由 ASP.NET Core 用于[主机和应用配置](xref:fundamentals/host/generic-host#host-configuration)，但不用于用户配置。</span><span class="sxs-lookup"><span data-stu-id="52609-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="52609-192">有关主机和应用配置的详细信息，请参阅 [.NET 通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="52609-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="52609-193">在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)上，选择“设置”>“配置”页面上的“新应用程序设置” 。</span><span class="sxs-lookup"><span data-stu-id="52609-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="52609-194">Azure 应用服务应用程序设置：</span><span class="sxs-lookup"><span data-stu-id="52609-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="52609-195">已静态加密且通过加密的通道进行传输。</span><span class="sxs-lookup"><span data-stu-id="52609-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="52609-196">已作为环境变量公开。</span><span class="sxs-lookup"><span data-stu-id="52609-196">Exposed as environment variables.</span></span>

<span data-ttu-id="52609-197">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="52609-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="52609-198">有关 Azure 数据库连接字符串的信息，请参阅[连接字符串前缀](#constr)。</span><span class="sxs-lookup"><span data-stu-id="52609-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="52609-199">命令行</span><span class="sxs-lookup"><span data-stu-id="52609-199">Command-line</span></span>

<span data-ttu-id="52609-200">使用[默认](#default)配置，<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 会从以下配置源后的命令行参数键值对中加载配置：</span><span class="sxs-lookup"><span data-stu-id="52609-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="52609-201">appsettings.json 和 appsettings.`Environment`.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="52609-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="52609-202">开发环境中的[应用机密（机密管理器）](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="52609-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52609-203">环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-203">Environment variables.</span></span>

<span data-ttu-id="52609-204">[默认情况下](#default)，在命令行上设置的配置值会替代通过所有其他配置提供程序设置的配置值。</span><span class="sxs-lookup"><span data-stu-id="52609-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="52609-205">命令行参数</span><span class="sxs-lookup"><span data-stu-id="52609-205">Command-line arguments</span></span>

<span data-ttu-id="52609-206">以下命令使用 `=` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="52609-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="52609-207">以下命令使用 `/` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="52609-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="52609-208">以下命令使用 `--` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="52609-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="52609-209">键值：</span><span class="sxs-lookup"><span data-stu-id="52609-209">The key value:</span></span>

* <span data-ttu-id="52609-210">必须后跟 `=`，或者当值后跟一个空格时，键必须具有一个 `--` 或 `/` 的前缀。</span><span class="sxs-lookup"><span data-stu-id="52609-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="52609-211">如果使用 `=`，则不是必需的。</span><span class="sxs-lookup"><span data-stu-id="52609-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="52609-212">例如 `MySetting=`。</span><span class="sxs-lookup"><span data-stu-id="52609-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="52609-213">在同一命令中，请勿将使用 `=` 的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="52609-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="52609-214">交换映射</span><span class="sxs-lookup"><span data-stu-id="52609-214">Switch mappings</span></span>

<span data-ttu-id="52609-215">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="52609-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="52609-216">提供针对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法的交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="52609-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="52609-217">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="52609-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="52609-218">如果在字典中找到了命令行键，则会传回字典值将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="52609-219">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="52609-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="52609-220">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="52609-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="52609-221">交换必须以 `-` 或 `--` 开头。</span><span class="sxs-lookup"><span data-stu-id="52609-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="52609-222">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="52609-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="52609-223">若要使用交换映射字典，请将它传递到对 `AddCommandLine` 的调用中：</span><span class="sxs-lookup"><span data-stu-id="52609-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="52609-224">下面的代码显示了替换后的键的键值：</span><span class="sxs-lookup"><span data-stu-id="52609-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-225">运行以下命令以测试键替换：</span><span class="sxs-lookup"><span data-stu-id="52609-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="52609-226">注意：目前，`=` 不能用于设置带有单划线 `-` 的键替换值。</span><span class="sxs-lookup"><span data-stu-id="52609-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="52609-227">请参阅[此 GitHub 问题](https://github.com/dotnet/extensions/issues/3059)。</span><span class="sxs-lookup"><span data-stu-id="52609-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="52609-228">以下命令可用于测试键替换：</span><span class="sxs-lookup"><span data-stu-id="52609-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="52609-229">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="52609-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="52609-230">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="52609-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52609-231">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="52609-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="52609-232">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="52609-232">Hierarchical configuration data</span></span>

<span data-ttu-id="52609-233">配置 API 在配置键中使用分隔符来展平分层数据，以此来读取分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="52609-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="52609-234">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="52609-235">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="52609-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-236">读取分层配置数据的首选方法是使用选项模式。</span><span class="sxs-lookup"><span data-stu-id="52609-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="52609-237">有关详细信息，请参阅本文档中的[绑定分层配置数据](#optpat)。</span><span class="sxs-lookup"><span data-stu-id="52609-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="52609-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="52609-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="52609-239">稍后将在 [GetSection、GetChildren 和 Exists](#getsection) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="52609-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="52609-240">配置键和值</span><span class="sxs-lookup"><span data-stu-id="52609-240">Configuration keys and values</span></span>

<span data-ttu-id="52609-241">配置键：</span><span class="sxs-lookup"><span data-stu-id="52609-241">Configuration keys:</span></span>

* <span data-ttu-id="52609-242">不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="52609-242">Are case-insensitive.</span></span> <span data-ttu-id="52609-243">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="52609-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="52609-244">如果在多个配置提供程序中设置了某一键和值，则会使用最后添加的提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="52609-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="52609-245">有关详细信息，请参阅[默认配置](#default)。</span><span class="sxs-lookup"><span data-stu-id="52609-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="52609-246">分层键</span><span class="sxs-lookup"><span data-stu-id="52609-246">Hierarchical keys</span></span>
  * <span data-ttu-id="52609-247">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="52609-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="52609-248">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="52609-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="52609-249">所有平台均支持采用双下划线 `__`，并且它会自动转换为冒号 `:`。</span><span class="sxs-lookup"><span data-stu-id="52609-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="52609-250">在 Azure Key Vault 中，分层键使用 `--` 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="52609-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="52609-251">当机密加载到应用的配置中时，[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration) 会自动将 `--` 替换为 `:`。</span><span class="sxs-lookup"><span data-stu-id="52609-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="52609-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="52609-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52609-253">数组绑定将在[将数组绑定到类](#boa)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="52609-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="52609-254">配置值：</span><span class="sxs-lookup"><span data-stu-id="52609-254">Configuration values:</span></span>

* <span data-ttu-id="52609-255">为字符串。</span><span class="sxs-lookup"><span data-stu-id="52609-255">Are strings.</span></span>
* <span data-ttu-id="52609-256">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="52609-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="52609-257">配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-257">Configuration providers</span></span>

<span data-ttu-id="52609-258">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="52609-259">提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-259">Provider</span></span> | <span data-ttu-id="52609-260">通过以下对象提供配置</span><span class="sxs-lookup"><span data-stu-id="52609-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="52609-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-262">'Blazor'</span></span>
- <span data-ttu-id="52609-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-263">'Identity'</span></span>
- <span data-ttu-id="52609-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-265">'Razor'</span></span>
- <span data-ttu-id="52609-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-268">'Blazor'</span></span>
- <span data-ttu-id="52609-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-269">'Identity'</span></span>
- <span data-ttu-id="52609-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-271">'Razor'</span></span>
- <span data-ttu-id="52609-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-272">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-274">'Blazor'</span></span>
- <span data-ttu-id="52609-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-275">'Identity'</span></span>
- <span data-ttu-id="52609-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-277">'Razor'</span></span>
- <span data-ttu-id="52609-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-280">'Blazor'</span></span>
- <span data-ttu-id="52609-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-281">'Identity'</span></span>
- <span data-ttu-id="52609-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-283">'Razor'</span></span>
- <span data-ttu-id="52609-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-286">'Blazor'</span></span>
- <span data-ttu-id="52609-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-287">'Identity'</span></span>
- <span data-ttu-id="52609-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-289">'Razor'</span></span>
- <span data-ttu-id="52609-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-292">'Blazor'</span></span>
- <span data-ttu-id="52609-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-293">'Identity'</span></span>
- <span data-ttu-id="52609-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-295">'Razor'</span></span>
- <span data-ttu-id="52609-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-298">'Blazor'</span></span>
- <span data-ttu-id="52609-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-299">'Identity'</span></span>
- <span data-ttu-id="52609-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-301">'Razor'</span></span>
- <span data-ttu-id="52609-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-304">'Blazor'</span></span>
- <span data-ttu-id="52609-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-305">'Identity'</span></span>
- <span data-ttu-id="52609-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-307">'Razor'</span></span>
- <span data-ttu-id="52609-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-310">'Blazor'</span></span>
- <span data-ttu-id="52609-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-311">'Identity'</span></span>
- <span data-ttu-id="52609-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-313">'Razor'</span></span>
- <span data-ttu-id="52609-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-316">'Blazor'</span></span>
- <span data-ttu-id="52609-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-317">'Identity'</span></span>
- <span data-ttu-id="52609-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-319">'Razor'</span></span>
- <span data-ttu-id="52609-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-322">'Blazor'</span></span>
- <span data-ttu-id="52609-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-323">'Identity'</span></span>
- <span data-ttu-id="52609-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-325">'Razor'</span></span>
- <span data-ttu-id="52609-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-328">'Blazor'</span></span>
- <span data-ttu-id="52609-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-329">'Identity'</span></span>
- <span data-ttu-id="52609-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-331">'Razor'</span></span>
- <span data-ttu-id="52609-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-334">'Blazor'</span></span>
- <span data-ttu-id="52609-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-335">'Identity'</span></span>
- <span data-ttu-id="52609-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-337">'Razor'</span></span>
- <span data-ttu-id="52609-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-340">'Blazor'</span></span>
- <span data-ttu-id="52609-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-341">'Identity'</span></span>
- <span data-ttu-id="52609-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-343">'Razor'</span></span>
- <span data-ttu-id="52609-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-346">'Blazor'</span></span>
- <span data-ttu-id="52609-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-347">'Identity'</span></span>
- <span data-ttu-id="52609-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-349">'Razor'</span></span>
- <span data-ttu-id="52609-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-352">'Blazor'</span></span>
- <span data-ttu-id="52609-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-353">'Identity'</span></span>
- <span data-ttu-id="52609-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-355">'Razor'</span></span>
- <span data-ttu-id="52609-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-358">'Blazor'</span></span>
- <span data-ttu-id="52609-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-359">'Identity'</span></span>
- <span data-ttu-id="52609-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-361">'Razor'</span></span>
- <span data-ttu-id="52609-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-362">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-363">------------------ | | [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure 应用程序配置 | | [命令行配置提供程序](#clcp) | 命令行参数 | | [自定义配置提供程序](#custom-configuration-provider) | 自定义源 | | [环境变量配置提供程序](#evcp) | 环境变量 | | [文件配置提供程序](#file-configuration-provider) | INI、JSON 和 XML 文件 | | [每文件密钥配置提供程序](#key-per-file-configuration-provider) | 目录文件 | | [内存配置提供程序](#memory-configuration-provider) | 内存中集合 | | [机密管理器](xref:security/app-secrets) | 用户配置文件目录中的文件 |</span><span class="sxs-lookup"><span data-stu-id="52609-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="52609-364">按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="52609-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="52609-365">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="52609-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="52609-366">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="52609-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="52609-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="52609-367">*appsettings.json*</span></span>
1. <span data-ttu-id="52609-368">appsettings.`Environment`.json </span><span class="sxs-lookup"><span data-stu-id="52609-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="52609-369">机密管理器</span><span class="sxs-lookup"><span data-stu-id="52609-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="52609-370">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="52609-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="52609-371">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="52609-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="52609-372">通常的做法是将命令行配置提供程序添加到一系列提供程序的末尾，使命令行参数能够替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="52609-373">[默认配置](#default)中使用了上述提供程序顺序。</span><span class="sxs-lookup"><span data-stu-id="52609-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="52609-374">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="52609-374">Connection string prefixes</span></span>

<span data-ttu-id="52609-375">对于四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="52609-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="52609-376">这些连接字符串涉及了为应用环境配置 Azure 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="52609-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="52609-377">使用[默认配置](#default)或没有向 `AddEnvironmentVariables` 应用前缀时，具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="52609-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="52609-378">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="52609-378">Connection string prefix</span></span> | <span data-ttu-id="52609-379">提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-379">Provider</span></span> |
| ---
<span data-ttu-id="52609-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-381">'Blazor'</span></span>
- <span data-ttu-id="52609-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-382">'Identity'</span></span>
- <span data-ttu-id="52609-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-384">'Razor'</span></span>
- <span data-ttu-id="52609-385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-387">'Blazor'</span></span>
- <span data-ttu-id="52609-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-388">'Identity'</span></span>
- <span data-ttu-id="52609-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-390">'Razor'</span></span>
- <span data-ttu-id="52609-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-393">'Blazor'</span></span>
- <span data-ttu-id="52609-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-394">'Identity'</span></span>
- <span data-ttu-id="52609-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-396">'Razor'</span></span>
- <span data-ttu-id="52609-397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-399">'Blazor'</span></span>
- <span data-ttu-id="52609-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-400">'Identity'</span></span>
- <span data-ttu-id="52609-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-402">'Razor'</span></span>
- <span data-ttu-id="52609-403">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-405">'Blazor'</span></span>
- <span data-ttu-id="52609-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-406">'Identity'</span></span>
- <span data-ttu-id="52609-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-408">'Razor'</span></span>
- <span data-ttu-id="52609-409">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-411">'Blazor'</span></span>
- <span data-ttu-id="52609-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-412">'Identity'</span></span>
- <span data-ttu-id="52609-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-414">'Razor'</span></span>
- <span data-ttu-id="52609-415">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-417">'Blazor'</span></span>
- <span data-ttu-id="52609-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-418">'Identity'</span></span>
- <span data-ttu-id="52609-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-420">'Razor'</span></span>
- <span data-ttu-id="52609-421">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-423">'Blazor'</span></span>
- <span data-ttu-id="52609-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-424">'Identity'</span></span>
- <span data-ttu-id="52609-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-426">'Razor'</span></span>
- <span data-ttu-id="52609-427">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-429">'Blazor'</span></span>
- <span data-ttu-id="52609-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-430">'Identity'</span></span>
- <span data-ttu-id="52609-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-432">'Razor'</span></span>
- <span data-ttu-id="52609-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-435">'Blazor'</span></span>
- <span data-ttu-id="52609-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-436">'Identity'</span></span>
- <span data-ttu-id="52609-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-438">'Razor'</span></span>
- <span data-ttu-id="52609-439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-439">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-441">'Blazor'</span></span>
- <span data-ttu-id="52609-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-442">'Identity'</span></span>
- <span data-ttu-id="52609-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-444">'Razor'</span></span>
- <span data-ttu-id="52609-445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-447">'Blazor'</span></span>
- <span data-ttu-id="52609-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-448">'Identity'</span></span>
- <span data-ttu-id="52609-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-450">'Razor'</span></span>
- <span data-ttu-id="52609-451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-451">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-452">---- | | `CUSTOMCONNSTR_` | 自定义提供程序 | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="52609-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="52609-453">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="52609-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="52609-454">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="52609-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="52609-455">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="52609-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="52609-456">环境变量键</span><span class="sxs-lookup"><span data-stu-id="52609-456">Environment variable key</span></span> | <span data-ttu-id="52609-457">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="52609-457">Converted configuration key</span></span> | <span data-ttu-id="52609-458">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="52609-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="52609-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-460">'Blazor'</span></span>
- <span data-ttu-id="52609-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-461">'Identity'</span></span>
- <span data-ttu-id="52609-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-463">'Razor'</span></span>
- <span data-ttu-id="52609-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-466">'Blazor'</span></span>
- <span data-ttu-id="52609-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-467">'Identity'</span></span>
- <span data-ttu-id="52609-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-469">'Razor'</span></span>
- <span data-ttu-id="52609-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-472">'Blazor'</span></span>
- <span data-ttu-id="52609-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-473">'Identity'</span></span>
- <span data-ttu-id="52609-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-475">'Razor'</span></span>
- <span data-ttu-id="52609-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-478">'Blazor'</span></span>
- <span data-ttu-id="52609-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-479">'Identity'</span></span>
- <span data-ttu-id="52609-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-481">'Razor'</span></span>
- <span data-ttu-id="52609-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-484">'Blazor'</span></span>
- <span data-ttu-id="52609-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-485">'Identity'</span></span>
- <span data-ttu-id="52609-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-487">'Razor'</span></span>
- <span data-ttu-id="52609-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-490">'Blazor'</span></span>
- <span data-ttu-id="52609-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-491">'Identity'</span></span>
- <span data-ttu-id="52609-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-493">'Razor'</span></span>
- <span data-ttu-id="52609-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-496">'Blazor'</span></span>
- <span data-ttu-id="52609-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-497">'Identity'</span></span>
- <span data-ttu-id="52609-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-499">'Razor'</span></span>
- <span data-ttu-id="52609-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-502">'Blazor'</span></span>
- <span data-ttu-id="52609-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-503">'Identity'</span></span>
- <span data-ttu-id="52609-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-505">'Razor'</span></span>
- <span data-ttu-id="52609-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-508">'Blazor'</span></span>
- <span data-ttu-id="52609-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-509">'Identity'</span></span>
- <span data-ttu-id="52609-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-511">'Razor'</span></span>
- <span data-ttu-id="52609-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-514">'Blazor'</span></span>
- <span data-ttu-id="52609-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-515">'Identity'</span></span>
- <span data-ttu-id="52609-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-517">'Razor'</span></span>
- <span data-ttu-id="52609-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-518">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-520">'Blazor'</span></span>
- <span data-ttu-id="52609-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-521">'Identity'</span></span>
- <span data-ttu-id="52609-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-523">'Razor'</span></span>
- <span data-ttu-id="52609-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-526">'Blazor'</span></span>
- <span data-ttu-id="52609-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-527">'Identity'</span></span>
- <span data-ttu-id="52609-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-529">'Razor'</span></span>
- <span data-ttu-id="52609-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-532">'Blazor'</span></span>
- <span data-ttu-id="52609-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-533">'Identity'</span></span>
- <span data-ttu-id="52609-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-535">'Razor'</span></span>
- <span data-ttu-id="52609-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-538">'Blazor'</span></span>
- <span data-ttu-id="52609-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-539">'Identity'</span></span>
- <span data-ttu-id="52609-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-541">'Razor'</span></span>
- <span data-ttu-id="52609-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-544">'Blazor'</span></span>
- <span data-ttu-id="52609-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-545">'Identity'</span></span>
- <span data-ttu-id="52609-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-547">'Razor'</span></span>
- <span data-ttu-id="52609-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-550">'Blazor'</span></span>
- <span data-ttu-id="52609-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-551">'Identity'</span></span>
- <span data-ttu-id="52609-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-553">'Razor'</span></span>
- <span data-ttu-id="52609-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-556">'Blazor'</span></span>
- <span data-ttu-id="52609-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-557">'Identity'</span></span>
- <span data-ttu-id="52609-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-559">'Razor'</span></span>
- <span data-ttu-id="52609-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-562">'Blazor'</span></span>
- <span data-ttu-id="52609-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-563">'Identity'</span></span>
- <span data-ttu-id="52609-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-565">'Razor'</span></span>
- <span data-ttu-id="52609-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-568">'Blazor'</span></span>
- <span data-ttu-id="52609-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-569">'Identity'</span></span>
- <span data-ttu-id="52609-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-571">'Razor'</span></span>
- <span data-ttu-id="52609-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-574">'Blazor'</span></span>
- <span data-ttu-id="52609-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-575">'Identity'</span></span>
- <span data-ttu-id="52609-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-577">'Razor'</span></span>
- <span data-ttu-id="52609-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-580">'Blazor'</span></span>
- <span data-ttu-id="52609-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-581">'Identity'</span></span>
- <span data-ttu-id="52609-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-583">'Razor'</span></span>
- <span data-ttu-id="52609-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-584">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-586">'Blazor'</span></span>
- <span data-ttu-id="52609-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-587">'Identity'</span></span>
- <span data-ttu-id="52609-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-589">'Razor'</span></span>
- <span data-ttu-id="52609-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-592">'Blazor'</span></span>
- <span data-ttu-id="52609-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-593">'Identity'</span></span>
- <span data-ttu-id="52609-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-595">'Razor'</span></span>
- <span data-ttu-id="52609-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-598">'Blazor'</span></span>
- <span data-ttu-id="52609-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-599">'Identity'</span></span>
- <span data-ttu-id="52609-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-601">'Razor'</span></span>
- <span data-ttu-id="52609-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-604">'Blazor'</span></span>
- <span data-ttu-id="52609-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-605">'Identity'</span></span>
- <span data-ttu-id="52609-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-607">'Razor'</span></span>
- <span data-ttu-id="52609-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-610">'Blazor'</span></span>
- <span data-ttu-id="52609-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-611">'Identity'</span></span>
- <span data-ttu-id="52609-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-613">'Razor'</span></span>
- <span data-ttu-id="52609-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-616">'Blazor'</span></span>
- <span data-ttu-id="52609-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-617">'Identity'</span></span>
- <span data-ttu-id="52609-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-619">'Razor'</span></span>
- <span data-ttu-id="52609-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-622">'Blazor'</span></span>
- <span data-ttu-id="52609-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-623">'Identity'</span></span>
- <span data-ttu-id="52609-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-625">'Razor'</span></span>
- <span data-ttu-id="52609-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-628">'Blazor'</span></span>
- <span data-ttu-id="52609-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-629">'Identity'</span></span>
- <span data-ttu-id="52609-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-631">'Razor'</span></span>
- <span data-ttu-id="52609-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-634">'Blazor'</span></span>
- <span data-ttu-id="52609-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-635">'Identity'</span></span>
- <span data-ttu-id="52609-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-637">'Razor'</span></span>
- <span data-ttu-id="52609-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-640">'Blazor'</span></span>
- <span data-ttu-id="52609-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-641">'Identity'</span></span>
- <span data-ttu-id="52609-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-643">'Razor'</span></span>
- <span data-ttu-id="52609-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-646">'Blazor'</span></span>
- <span data-ttu-id="52609-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-647">'Identity'</span></span>
- <span data-ttu-id="52609-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-649">'Razor'</span></span>
- <span data-ttu-id="52609-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-652">'Blazor'</span></span>
- <span data-ttu-id="52609-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-653">'Identity'</span></span>
- <span data-ttu-id="52609-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-655">'Razor'</span></span>
- <span data-ttu-id="52609-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-658">'Blazor'</span></span>
- <span data-ttu-id="52609-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-659">'Identity'</span></span>
- <span data-ttu-id="52609-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-661">'Razor'</span></span>
- <span data-ttu-id="52609-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-664">'Blazor'</span></span>
- <span data-ttu-id="52609-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-665">'Identity'</span></span>
- <span data-ttu-id="52609-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-667">'Razor'</span></span>
- <span data-ttu-id="52609-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-670">'Blazor'</span></span>
- <span data-ttu-id="52609-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-671">'Identity'</span></span>
- <span data-ttu-id="52609-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-673">'Razor'</span></span>
- <span data-ttu-id="52609-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-676">'Blazor'</span></span>
- <span data-ttu-id="52609-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-677">'Identity'</span></span>
- <span data-ttu-id="52609-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-679">'Razor'</span></span>
- <span data-ttu-id="52609-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-682">'Blazor'</span></span>
- <span data-ttu-id="52609-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-683">'Identity'</span></span>
- <span data-ttu-id="52609-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-685">'Razor'</span></span>
- <span data-ttu-id="52609-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-688">'Blazor'</span></span>
- <span data-ttu-id="52609-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-689">'Identity'</span></span>
- <span data-ttu-id="52609-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-691">'Razor'</span></span>
- <span data-ttu-id="52609-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-694">'Blazor'</span></span>
- <span data-ttu-id="52609-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-695">'Identity'</span></span>
- <span data-ttu-id="52609-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-697">'Razor'</span></span>
- <span data-ttu-id="52609-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-700">'Blazor'</span></span>
- <span data-ttu-id="52609-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-701">'Identity'</span></span>
- <span data-ttu-id="52609-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-703">'Razor'</span></span>
- <span data-ttu-id="52609-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-706">'Blazor'</span></span>
- <span data-ttu-id="52609-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-707">'Identity'</span></span>
- <span data-ttu-id="52609-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-709">'Razor'</span></span>
- <span data-ttu-id="52609-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-712">'Blazor'</span></span>
- <span data-ttu-id="52609-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-713">'Identity'</span></span>
- <span data-ttu-id="52609-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-715">'Razor'</span></span>
- <span data-ttu-id="52609-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-718">'Blazor'</span></span>
- <span data-ttu-id="52609-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-719">'Identity'</span></span>
- <span data-ttu-id="52609-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-721">'Razor'</span></span>
- <span data-ttu-id="52609-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-724">'Blazor'</span></span>
- <span data-ttu-id="52609-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-725">'Identity'</span></span>
- <span data-ttu-id="52609-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-727">'Razor'</span></span>
- <span data-ttu-id="52609-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-730">'Blazor'</span></span>
- <span data-ttu-id="52609-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-731">'Identity'</span></span>
- <span data-ttu-id="52609-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-733">'Razor'</span></span>
- <span data-ttu-id="52609-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-736">'Blazor'</span></span>
- <span data-ttu-id="52609-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-737">'Identity'</span></span>
- <span data-ttu-id="52609-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-739">'Razor'</span></span>
- <span data-ttu-id="52609-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-742">'Blazor'</span></span>
- <span data-ttu-id="52609-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-743">'Identity'</span></span>
- <span data-ttu-id="52609-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-745">'Razor'</span></span>
- <span data-ttu-id="52609-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-748">'Blazor'</span></span>
- <span data-ttu-id="52609-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-749">'Identity'</span></span>
- <span data-ttu-id="52609-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-751">'Razor'</span></span>
- <span data-ttu-id="52609-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-754">'Blazor'</span></span>
- <span data-ttu-id="52609-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-755">'Identity'</span></span>
- <span data-ttu-id="52609-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-757">'Razor'</span></span>
- <span data-ttu-id="52609-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-760">'Blazor'</span></span>
- <span data-ttu-id="52609-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-761">'Identity'</span></span>
- <span data-ttu-id="52609-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-763">'Razor'</span></span>
- <span data-ttu-id="52609-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-766">'Blazor'</span></span>
- <span data-ttu-id="52609-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-767">'Identity'</span></span>
- <span data-ttu-id="52609-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-769">'Razor'</span></span>
- <span data-ttu-id="52609-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-772">'Blazor'</span></span>
- <span data-ttu-id="52609-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-773">'Identity'</span></span>
- <span data-ttu-id="52609-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-775">'Razor'</span></span>
- <span data-ttu-id="52609-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-778">'Blazor'</span></span>
- <span data-ttu-id="52609-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-779">'Identity'</span></span>
- <span data-ttu-id="52609-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-781">'Razor'</span></span>
- <span data-ttu-id="52609-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-784">'Blazor'</span></span>
- <span data-ttu-id="52609-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-785">'Identity'</span></span>
- <span data-ttu-id="52609-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-787">'Razor'</span></span>
- <span data-ttu-id="52609-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-790">'Blazor'</span></span>
- <span data-ttu-id="52609-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-791">'Identity'</span></span>
- <span data-ttu-id="52609-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-793">'Razor'</span></span>
- <span data-ttu-id="52609-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-796">'Blazor'</span></span>
- <span data-ttu-id="52609-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-797">'Identity'</span></span>
- <span data-ttu-id="52609-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-799">'Razor'</span></span>
- <span data-ttu-id="52609-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-802">'Blazor'</span></span>
- <span data-ttu-id="52609-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-803">'Identity'</span></span>
- <span data-ttu-id="52609-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-805">'Razor'</span></span>
- <span data-ttu-id="52609-806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-806">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}` | 配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="52609-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="52609-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}` | 键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="52609-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52609-809">值：`MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}` | 键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="52609-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52609-810">值：`System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}` | 键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="52609-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52609-811">值：`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="52609-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="52609-812">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-812">JSON configuration provider</span></span>

<span data-ttu-id="52609-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="52609-814">重载可以指定：</span><span class="sxs-lookup"><span data-stu-id="52609-814">Overloads can specify:</span></span>

* <span data-ttu-id="52609-815">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="52609-815">Whether the file is optional.</span></span>
* <span data-ttu-id="52609-816">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="52609-817">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="52609-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="52609-818">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="52609-818">The preceding code:</span></span>

* <span data-ttu-id="52609-819">通过以下选项将 JSON 配置提供程序配置为加载 MyConfig.json 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="52609-820">`optional: true`：文件是可选的。</span><span class="sxs-lookup"><span data-stu-id="52609-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="52609-821">`reloadOnChange: true`：保存更改后会重载文件。</span><span class="sxs-lookup"><span data-stu-id="52609-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="52609-822">读取 MyConfig.json 文件之前的[默认配置提供程序](#default)。</span><span class="sxs-lookup"><span data-stu-id="52609-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="52609-823">MyConfig.json 文件中的设置会替代默认配置提供程序中的设置，包括[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="52609-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52609-824">通常情况下，你不会希望自定义 JSON 文件替代在[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中设置的值。</span><span class="sxs-lookup"><span data-stu-id="52609-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52609-825">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="52609-826">在前面的代码中，MyConfig.json 和 MyConfig.`Environment`.json 文件中的设置  ：</span><span class="sxs-lookup"><span data-stu-id="52609-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="52609-827">会替代 appsettings.json 和 appsettings.`Environment`.json 文件中的设置  。</span><span class="sxs-lookup"><span data-stu-id="52609-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="52609-828">会被[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中的设置所替代。</span><span class="sxs-lookup"><span data-stu-id="52609-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52609-829">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyConfig.json 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="52609-830">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="52609-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="52609-831">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-831">File configuration provider</span></span>

<span data-ttu-id="52609-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="52609-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="52609-833">以下配置提供程序派生自 `FileConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="52609-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="52609-834">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="52609-835">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="52609-836">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="52609-837">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-837">INI configuration provider</span></span>

<span data-ttu-id="52609-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="52609-839">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="52609-840">在前面的代码中，MyIniConfig.ini 和 MyIniConfig.`Environment`.ini 文件中的设置会被以下提供程序中的设置替代  ：</span><span class="sxs-lookup"><span data-stu-id="52609-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="52609-841">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="52609-842">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="52609-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52609-843">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyIniConfig.ini 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="52609-844">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="52609-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="52609-845">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-845">XML configuration provider</span></span>

<span data-ttu-id="52609-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="52609-847">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="52609-848">在前面的代码中，MyXMLFile.xml 和 MyXMLFile.`Environment`.xml 文件中的设置会被以下提供程序中的设置替代  ：</span><span class="sxs-lookup"><span data-stu-id="52609-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="52609-849">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="52609-850">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="52609-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52609-851">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyXMLFile.xml 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="52609-852">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="52609-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-853">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="52609-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="52609-854">以下代码会读取前面的配置文件并显示键和值：</span><span class="sxs-lookup"><span data-stu-id="52609-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-855">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="52609-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="52609-856">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="52609-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52609-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="52609-857">key:attribute</span></span>
* <span data-ttu-id="52609-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="52609-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="52609-859">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="52609-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="52609-861">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="52609-861">The key is the file name.</span></span> <span data-ttu-id="52609-862">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="52609-862">The value contains the file's contents.</span></span> <span data-ttu-id="52609-863">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="52609-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="52609-864">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="52609-865">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="52609-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="52609-866">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="52609-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="52609-867">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="52609-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="52609-868">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="52609-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="52609-869">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="52609-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="52609-870">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="52609-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="52609-871">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="52609-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="52609-872">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-872">Memory configuration provider</span></span>

<span data-ttu-id="52609-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="52609-874">以下代码将内存集合添加到配置系统中：</span><span class="sxs-lookup"><span data-stu-id="52609-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="52609-875">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述配置设置：</span><span class="sxs-lookup"><span data-stu-id="52609-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-876">在前面的代码中，`config.AddInMemoryCollection(Dict)` 会被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="52609-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="52609-877">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="52609-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="52609-878">有关使用 `MemoryConfigurationProvider` 的其他示例，请参阅[绑定数组](#boa)。</span><span class="sxs-lookup"><span data-stu-id="52609-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="52609-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="52609-879">GetValue</span></span>

<span data-ttu-id="52609-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的类型：</span><span class="sxs-lookup"><span data-stu-id="52609-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-881">在前面的代码中，如果在配置中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="52609-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="52609-882">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="52609-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="52609-883">对于下面的示例，请考虑以下 MySubsection.json 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="52609-884">以下代码将 MySubsection.json 添加到配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="52609-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="52609-885">GetSection</span></span>

<span data-ttu-id="52609-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 会返回具有指定子节键的配置子节。</span><span class="sxs-lookup"><span data-stu-id="52609-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="52609-887">以下代码将返回 `section1` 的值：</span><span class="sxs-lookup"><span data-stu-id="52609-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-888">以下代码将返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="52609-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-889">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="52609-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="52609-890">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="52609-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="52609-891">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="52609-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="52609-892">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="52609-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="52609-893">GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="52609-893">GetChildren and Exists</span></span>

<span data-ttu-id="52609-894">以下代码将调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 并返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="52609-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-895">前面的代码将调用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 以验证该节是否存在：</span><span class="sxs-lookup"><span data-stu-id="52609-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="52609-896">绑定数组</span><span class="sxs-lookup"><span data-stu-id="52609-896">Bind an array</span></span>

<span data-ttu-id="52609-897">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="52609-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52609-898">公开数值键段的任何数组格式都能够与 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="52609-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="52609-899">请考虑[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的 MyArray.json：</span><span class="sxs-lookup"><span data-stu-id="52609-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="52609-900">以下代码将 MyArray.json 添加到配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="52609-901">以下代码将读取配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="52609-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-902">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="52609-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="52609-903">在前面的输出中，索引 3 具有值 `value40`，与 MyArray.json 中的 `"4": "value40",` 相对应。</span><span class="sxs-lookup"><span data-stu-id="52609-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="52609-904">绑定的数组索引是连续的，并且未绑定到配置键索引。</span><span class="sxs-lookup"><span data-stu-id="52609-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="52609-905">配置绑定器不能绑定 NULL 值，也不能在绑定的对象中创建 NULL 条目</span><span class="sxs-lookup"><span data-stu-id="52609-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="52609-906">以下代码将通过 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法加载 `array:entries` 配置：</span><span class="sxs-lookup"><span data-stu-id="52609-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="52609-907">以下代码将读取 `arrayDict` `Dictionary` 中的配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="52609-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-908">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="52609-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="52609-909">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="52609-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="52609-910">当绑定包含数组的配置数据时，配置键中的数组索引用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="52609-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="52609-911">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="52609-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="52609-912">可以在由任何读取索引 &num;3 键/值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="52609-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="52609-913">请考虑示例下载中的以下 Value3.json 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="52609-914">以下代码包含 Value3.json 和 `arrayDict` `Dictionary` 的配置：</span><span class="sxs-lookup"><span data-stu-id="52609-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="52609-915">以下代码将读取上述配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="52609-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-916">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="52609-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="52609-917">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="52609-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="52609-918">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-918">Custom configuration provider</span></span>

<span data-ttu-id="52609-919">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="52609-920">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="52609-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="52609-921">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="52609-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="52609-922">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="52609-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="52609-923">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="52609-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="52609-924">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="52609-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="52609-925">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="52609-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="52609-926">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="52609-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="52609-927">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="52609-928">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="52609-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="52609-929">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="52609-930">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="52609-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="52609-931">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="52609-932">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="52609-933">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="52609-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="52609-934">由于[配置密钥不区分大小写](#keys)，因此用来初始化数据库的字典是用不区分大小写的比较程序 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 创建的。</span><span class="sxs-lookup"><span data-stu-id="52609-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="52609-935">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="52609-936">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="52609-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="52609-937">Extensions/EntityFrameworkExtensions.cs：</span><span class="sxs-lookup"><span data-stu-id="52609-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="52609-938">下面的代码演示如何在 Program.cs 中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="52609-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="52609-939">访问 Startup 中的配置</span><span class="sxs-lookup"><span data-stu-id="52609-939">Access configuration in Startup</span></span>

<span data-ttu-id="52609-940">以下代码显示 `Startup` 方法中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="52609-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="52609-941">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="52609-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="52609-942">访问 Razor Pages 中的配置</span><span class="sxs-lookup"><span data-stu-id="52609-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="52609-943">以下代码显示 Razor Pages 中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="52609-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="52609-944">在以下代码中，`MyOptions` 已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 被添加到了服务容器并已绑定到了配置：</span><span class="sxs-lookup"><span data-stu-id="52609-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="52609-945">以下标记使用 [`@inject`](xref:mvc/views/razor#inject) Razor 指令来解析和显示选项值：</span><span class="sxs-lookup"><span data-stu-id="52609-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="52609-946">访问 MVC 视图文件中的配置</span><span class="sxs-lookup"><span data-stu-id="52609-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="52609-947">以下代码显示 MVC 视图中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="52609-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="52609-948">使用委托来配置选项</span><span class="sxs-lookup"><span data-stu-id="52609-948">Configure options with a delegate</span></span>

<span data-ttu-id="52609-949">在委托中配置的选项替代在配置提供程序中设置的值。</span><span class="sxs-lookup"><span data-stu-id="52609-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="52609-950">示例应用中的示例 2 展示了如何使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="52609-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="52609-951">在以下代码中，向服务容器添加了 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="52609-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="52609-952">它使用委托来配置 `MyOptions` 的值：</span><span class="sxs-lookup"><span data-stu-id="52609-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="52609-953">以下代码显示选项值：</span><span class="sxs-lookup"><span data-stu-id="52609-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="52609-954">在前面的示例中，`Option1` 和 `Option2` 的值在 appsettings.json 中指定，然后被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="52609-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="52609-955">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="52609-955">Host versus app configuration</span></span>

<span data-ttu-id="52609-956">在配置并启动应用之前，配置并启动主机。</span><span class="sxs-lookup"><span data-stu-id="52609-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="52609-957">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="52609-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="52609-958">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="52609-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="52609-959">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="52609-960">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="52609-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="52609-961">默认主机配置</span><span class="sxs-lookup"><span data-stu-id="52609-961">Default host configuration</span></span>

<span data-ttu-id="52609-962">有关使用 [Web 主机](xref:fundamentals/host/web-host)时默认配置的详细信息，请参阅[本主题的 ASP.NET Core 2.2 版本](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="52609-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="52609-963">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="52609-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="52609-964">使用[环境变量配置提供程序](#environment-variables-configuration-provider)通过前缀为 `DOTNET_`的环境变量（例如，`DOTNET_ENVIRONMENT`）提供。</span><span class="sxs-lookup"><span data-stu-id="52609-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="52609-965">在配置键值对加载后，前缀 (`DOTNET_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="52609-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="52609-966">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="52609-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="52609-967">已建立 Web 主机默认配置 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="52609-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="52609-968">Kestrel 用作 Web 服务器，并使用应用的配置提供程序对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="52609-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="52609-969">添加主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="52609-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="52609-970">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则添加转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="52609-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="52609-971">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="52609-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="52609-972">其他配置</span><span class="sxs-lookup"><span data-stu-id="52609-972">Other configuration</span></span>

<span data-ttu-id="52609-973">本主题仅与应用配置相关。</span><span class="sxs-lookup"><span data-stu-id="52609-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="52609-974">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="52609-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="52609-975">launch.json/launchSettings.json 是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="52609-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="52609-976"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="52609-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="52609-977">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="52609-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="52609-978">web.config 是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="52609-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="52609-979">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="52609-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="52609-980">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="52609-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="52609-981">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="52609-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="52609-982">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="52609-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52609-983">其他资源</span><span class="sxs-lookup"><span data-stu-id="52609-983">Additional resources</span></span>

* [<span data-ttu-id="52609-984">配置源代码</span><span class="sxs-lookup"><span data-stu-id="52609-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52609-985">ASP.NET Core 中的应用配置基于配置提供程序建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="52609-986">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="52609-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="52609-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52609-987">Azure Key Vault</span></span>
* <span data-ttu-id="52609-988">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="52609-988">Azure App Configuration</span></span>
* <span data-ttu-id="52609-989">命令行参数</span><span class="sxs-lookup"><span data-stu-id="52609-989">Command-line arguments</span></span>
* <span data-ttu-id="52609-990">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="52609-991">目录文件</span><span class="sxs-lookup"><span data-stu-id="52609-991">Directory files</span></span>
* <span data-ttu-id="52609-992">环境变量</span><span class="sxs-lookup"><span data-stu-id="52609-992">Environment variables</span></span>
* <span data-ttu-id="52609-993">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="52609-993">In-memory .NET objects</span></span>
* <span data-ttu-id="52609-994">设置文件</span><span class="sxs-lookup"><span data-stu-id="52609-994">Settings files</span></span>

<span data-ttu-id="52609-995">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="52609-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="52609-996">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="52609-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="52609-997">选项模式是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="52609-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="52609-998">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="52609-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="52609-999">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="52609-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="52609-1000">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="52609-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="52609-1001">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="52609-1001">Host versus app configuration</span></span>

<span data-ttu-id="52609-1002">在配置并启动应用之前，配置并启动主机。</span><span class="sxs-lookup"><span data-stu-id="52609-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="52609-1003">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="52609-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="52609-1004">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="52609-1005">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="52609-1006">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="52609-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="52609-1007">其他配置</span><span class="sxs-lookup"><span data-stu-id="52609-1007">Other configuration</span></span>

<span data-ttu-id="52609-1008">本主题仅与应用配置相关。</span><span class="sxs-lookup"><span data-stu-id="52609-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="52609-1009">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="52609-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="52609-1010">launch.json/launchSettings.json 是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="52609-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="52609-1011"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="52609-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="52609-1012">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="52609-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="52609-1013">web.config 是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="52609-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="52609-1014">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="52609-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="52609-1015">默认配置</span><span class="sxs-lookup"><span data-stu-id="52609-1015">Default configuration</span></span>

<span data-ttu-id="52609-1016">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="52609-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="52609-1017">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="52609-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="52609-1018">以下内容适用于使用 [Web 主机](xref:fundamentals/host/web-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="52609-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="52609-1019">有关使用[通用主机](xref:fundamentals/host/generic-host)时默认配置的详细信息，请参阅[本主题的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="52609-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="52609-1020">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="52609-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="52609-1021">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="52609-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="52609-1022">在配置键值对加载后，前缀 (`ASPNETCORE_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="52609-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="52609-1023">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="52609-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="52609-1024">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="52609-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="52609-1025">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json 提供。</span><span class="sxs-lookup"><span data-stu-id="52609-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="52609-1026">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json 提供。</span><span class="sxs-lookup"><span data-stu-id="52609-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="52609-1027">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="52609-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="52609-1028">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="52609-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="52609-1029">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="52609-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="52609-1030">安全性</span><span class="sxs-lookup"><span data-stu-id="52609-1030">Security</span></span>

<span data-ttu-id="52609-1031">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="52609-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="52609-1032">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="52609-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="52609-1033">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="52609-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="52609-1034">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="52609-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="52609-1035">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="52609-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="52609-1036"><xref:security/app-secrets>：包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="52609-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="52609-1037">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="52609-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="52609-1038">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="52609-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="52609-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="52609-1040">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="52609-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="52609-1041">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="52609-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="52609-1042">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="52609-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="52609-1043">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="52609-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="52609-1044">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="52609-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="52609-1045">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="52609-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="52609-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52609-1046">section0:key0</span></span>
* <span data-ttu-id="52609-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52609-1047">section0:key1</span></span>
* <span data-ttu-id="52609-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="52609-1048">section1:key0</span></span>
* <span data-ttu-id="52609-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="52609-1049">section1:key1</span></span>

<span data-ttu-id="52609-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="52609-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="52609-1051">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="52609-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="52609-1052">约定</span><span class="sxs-lookup"><span data-stu-id="52609-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="52609-1053">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-1053">Sources and providers</span></span>

<span data-ttu-id="52609-1054">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="52609-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="52609-1055">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="52609-1056">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="52609-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="52609-1057">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="52609-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="52609-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 中，以获取类的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="52609-1059">在下面的示例中，使用 `_config` 字段来访问配置值：</span><span class="sxs-lookup"><span data-stu-id="52609-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="52609-1060">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="52609-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="52609-1061">键</span><span class="sxs-lookup"><span data-stu-id="52609-1061">Keys</span></span>

<span data-ttu-id="52609-1062">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="52609-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="52609-1063">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="52609-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="52609-1064">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="52609-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="52609-1065">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="52609-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="52609-1066">分层键</span><span class="sxs-lookup"><span data-stu-id="52609-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="52609-1067">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="52609-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="52609-1068">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="52609-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="52609-1069">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="52609-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="52609-1070">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="52609-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="52609-1071">将机密加载到应用的配置中时，用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="52609-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="52609-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="52609-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52609-1073">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="52609-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="52609-1074">值</span><span class="sxs-lookup"><span data-stu-id="52609-1074">Values</span></span>

<span data-ttu-id="52609-1075">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="52609-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="52609-1076">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="52609-1076">Values are strings.</span></span>
* <span data-ttu-id="52609-1077">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="52609-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="52609-1078">提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-1078">Providers</span></span>

<span data-ttu-id="52609-1079">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="52609-1080">提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-1080">Provider</span></span> | <span data-ttu-id="52609-1081">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="52609-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="52609-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1083">'Blazor'</span></span>
- <span data-ttu-id="52609-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1084">'Identity'</span></span>
- <span data-ttu-id="52609-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1086">'Razor'</span></span>
- <span data-ttu-id="52609-1087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1089">'Blazor'</span></span>
- <span data-ttu-id="52609-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1090">'Identity'</span></span>
- <span data-ttu-id="52609-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1092">'Razor'</span></span>
- <span data-ttu-id="52609-1093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1095">'Blazor'</span></span>
- <span data-ttu-id="52609-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1096">'Identity'</span></span>
- <span data-ttu-id="52609-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1098">'Razor'</span></span>
- <span data-ttu-id="52609-1099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1101">'Blazor'</span></span>
- <span data-ttu-id="52609-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1102">'Identity'</span></span>
- <span data-ttu-id="52609-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1104">'Razor'</span></span>
- <span data-ttu-id="52609-1105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1107">'Blazor'</span></span>
- <span data-ttu-id="52609-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1108">'Identity'</span></span>
- <span data-ttu-id="52609-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1110">'Razor'</span></span>
- <span data-ttu-id="52609-1111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1113">'Blazor'</span></span>
- <span data-ttu-id="52609-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1114">'Identity'</span></span>
- <span data-ttu-id="52609-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1116">'Razor'</span></span>
- <span data-ttu-id="52609-1117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1119">'Blazor'</span></span>
- <span data-ttu-id="52609-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1120">'Identity'</span></span>
- <span data-ttu-id="52609-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1122">'Razor'</span></span>
- <span data-ttu-id="52609-1123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1125">'Blazor'</span></span>
- <span data-ttu-id="52609-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1126">'Identity'</span></span>
- <span data-ttu-id="52609-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1128">'Razor'</span></span>
- <span data-ttu-id="52609-1129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1131">'Blazor'</span></span>
- <span data-ttu-id="52609-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1132">'Identity'</span></span>
- <span data-ttu-id="52609-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1134">'Razor'</span></span>
- <span data-ttu-id="52609-1135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1137">'Blazor'</span></span>
- <span data-ttu-id="52609-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1138">'Identity'</span></span>
- <span data-ttu-id="52609-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1140">'Razor'</span></span>
- <span data-ttu-id="52609-1141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1143">'Blazor'</span></span>
- <span data-ttu-id="52609-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1144">'Identity'</span></span>
- <span data-ttu-id="52609-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1146">'Razor'</span></span>
- <span data-ttu-id="52609-1147">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1149">'Blazor'</span></span>
- <span data-ttu-id="52609-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1150">'Identity'</span></span>
- <span data-ttu-id="52609-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1152">'Razor'</span></span>
- <span data-ttu-id="52609-1153">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1155">'Blazor'</span></span>
- <span data-ttu-id="52609-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1156">'Identity'</span></span>
- <span data-ttu-id="52609-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1158">'Razor'</span></span>
- <span data-ttu-id="52609-1159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1161">'Blazor'</span></span>
- <span data-ttu-id="52609-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1162">'Identity'</span></span>
- <span data-ttu-id="52609-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1164">'Razor'</span></span>
- <span data-ttu-id="52609-1165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1167">'Blazor'</span></span>
- <span data-ttu-id="52609-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1168">'Identity'</span></span>
- <span data-ttu-id="52609-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1170">'Razor'</span></span>
- <span data-ttu-id="52609-1171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1173">'Blazor'</span></span>
- <span data-ttu-id="52609-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1174">'Identity'</span></span>
- <span data-ttu-id="52609-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1176">'Razor'</span></span>
- <span data-ttu-id="52609-1177">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1179">'Blazor'</span></span>
- <span data-ttu-id="52609-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1180">'Identity'</span></span>
- <span data-ttu-id="52609-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1182">'Razor'</span></span>
- <span data-ttu-id="52609-1183">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1184">------------------ | | [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（“安全性”主题）| Azure Key Vault | | [Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）| Azure 应用程序配置 | | [命令行配置提供程序](#command-line-configuration-provider) | 命令行参数 | | [自定义配置提供程序](#custom-configuration-provider) | 自定义源 | | [环境变量配置提供程序](#environment-variables-configuration-provider) | 环境变量 | | [文件配置提供程序](#file-configuration-provider) | 文件（INI、JSON、XML）| | [每文件密钥配置提供程序](#key-per-file-configuration-provider) | 目录文件 | | [内存配置提供程序](#memory-configuration-provider) | 内存中集合 | | [用户机密（机密管理器）](xref:security/app-secrets)（“安全性” 主题）| 用户配置文件目录中的文件 |</span><span class="sxs-lookup"><span data-stu-id="52609-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="52609-1185">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="52609-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="52609-1186">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="52609-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="52609-1187">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="52609-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="52609-1188">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="52609-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="52609-1189">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境） </span><span class="sxs-lookup"><span data-stu-id="52609-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="52609-1190">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="52609-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="52609-1191">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="52609-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="52609-1192">环境变量</span><span class="sxs-lookup"><span data-stu-id="52609-1192">Environment variables</span></span>
1. <span data-ttu-id="52609-1193">命令行参数</span><span class="sxs-lookup"><span data-stu-id="52609-1193">Command-line arguments</span></span>

<span data-ttu-id="52609-1194">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="52609-1195">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="52609-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52609-1196">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="52609-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="52609-1197">用 UseConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="52609-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="52609-1198">若要配置主机生成器，请使用配置在主机生成器上调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="52609-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="52609-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="52609-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="52609-1200">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="52609-1201">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="52609-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="52609-1202">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="52609-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="52609-1203">删除 CreateDefaultBuilder 添加的提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="52609-1204">要删除 `CreateDefaultBuilder` 添加的提供程序，请先对 [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) 调用 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)：</span><span class="sxs-lookup"><span data-stu-id="52609-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="52609-1205">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="52609-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="52609-1206">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="52609-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="52609-1207">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="52609-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="52609-1208">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="52609-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="52609-1210">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52609-1211">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="52609-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="52609-1212">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="52609-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52609-1213">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="52609-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="52609-1214">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置 。</span><span class="sxs-lookup"><span data-stu-id="52609-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="52609-1215">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="52609-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52609-1216">环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-1216">Environment variables.</span></span>

<span data-ttu-id="52609-1217">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="52609-1218">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="52609-1219">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="52609-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="52609-1220">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="52609-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="52609-1221">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="52609-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52609-1222">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="52609-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="52609-1223">**示例**</span><span class="sxs-lookup"><span data-stu-id="52609-1223">**Example**</span></span>

<span data-ttu-id="52609-1224">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="52609-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="52609-1225">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="52609-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="52609-1226">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="52609-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="52609-1227">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="52609-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="52609-1228">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="52609-1229">自变量</span><span class="sxs-lookup"><span data-stu-id="52609-1229">Arguments</span></span>

<span data-ttu-id="52609-1230">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="52609-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="52609-1231">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="52609-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="52609-1232">键前缀</span><span class="sxs-lookup"><span data-stu-id="52609-1232">Key prefix</span></span>               | <span data-ttu-id="52609-1233">示例</span><span class="sxs-lookup"><span data-stu-id="52609-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="52609-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1235">'Blazor'</span></span>
- <span data-ttu-id="52609-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1236">'Identity'</span></span>
- <span data-ttu-id="52609-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1238">'Razor'</span></span>
- <span data-ttu-id="52609-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1241">'Blazor'</span></span>
- <span data-ttu-id="52609-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1242">'Identity'</span></span>
- <span data-ttu-id="52609-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1244">'Razor'</span></span>
- <span data-ttu-id="52609-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1247">'Blazor'</span></span>
- <span data-ttu-id="52609-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1248">'Identity'</span></span>
- <span data-ttu-id="52609-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1250">'Razor'</span></span>
- <span data-ttu-id="52609-1251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1253">'Blazor'</span></span>
- <span data-ttu-id="52609-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1254">'Identity'</span></span>
- <span data-ttu-id="52609-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1256">'Razor'</span></span>
- <span data-ttu-id="52609-1257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1259">'Blazor'</span></span>
- <span data-ttu-id="52609-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1260">'Identity'</span></span>
- <span data-ttu-id="52609-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1262">'Razor'</span></span>
- <span data-ttu-id="52609-1263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1265">'Blazor'</span></span>
- <span data-ttu-id="52609-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1266">'Identity'</span></span>
- <span data-ttu-id="52609-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1268">'Razor'</span></span>
- <span data-ttu-id="52609-1269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1271">'Blazor'</span></span>
- <span data-ttu-id="52609-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1272">'Identity'</span></span>
- <span data-ttu-id="52609-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1274">'Razor'</span></span>
- <span data-ttu-id="52609-1275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1277">'Blazor'</span></span>
- <span data-ttu-id="52609-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1278">'Identity'</span></span>
- <span data-ttu-id="52609-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1280">'Razor'</span></span>
- <span data-ttu-id="52609-1281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1283">'Blazor'</span></span>
- <span data-ttu-id="52609-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1284">'Identity'</span></span>
- <span data-ttu-id="52609-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1286">'Razor'</span></span>
- <span data-ttu-id="52609-1287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1289">'Blazor'</span></span>
- <span data-ttu-id="52609-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1290">'Identity'</span></span>
- <span data-ttu-id="52609-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1292">'Razor'</span></span>
- <span data-ttu-id="52609-1293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1295">'Blazor'</span></span>
- <span data-ttu-id="52609-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1296">'Identity'</span></span>
- <span data-ttu-id="52609-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1298">'Razor'</span></span>
- <span data-ttu-id="52609-1299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1301">'Blazor'</span></span>
- <span data-ttu-id="52609-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1302">'Identity'</span></span>
- <span data-ttu-id="52609-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1304">'Razor'</span></span>
- <span data-ttu-id="52609-1305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1307">'Blazor'</span></span>
- <span data-ttu-id="52609-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1308">'Identity'</span></span>
- <span data-ttu-id="52609-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1310">'Razor'</span></span>
- <span data-ttu-id="52609-1311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1313">'Blazor'</span></span>
- <span data-ttu-id="52609-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1314">'Identity'</span></span>
- <span data-ttu-id="52609-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1316">'Razor'</span></span>
- <span data-ttu-id="52609-1317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1319">'Blazor'</span></span>
- <span data-ttu-id="52609-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1320">'Identity'</span></span>
- <span data-ttu-id="52609-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1322">'Razor'</span></span>
- <span data-ttu-id="52609-1323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1325">'Blazor'</span></span>
- <span data-ttu-id="52609-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1326">'Identity'</span></span>
- <span data-ttu-id="52609-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1328">'Razor'</span></span>
- <span data-ttu-id="52609-1329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1331">'Blazor'</span></span>
- <span data-ttu-id="52609-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1332">'Identity'</span></span>
- <span data-ttu-id="52609-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1334">'Razor'</span></span>
- <span data-ttu-id="52609-1335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1337">'Blazor'</span></span>
- <span data-ttu-id="52609-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1338">'Identity'</span></span>
- <span data-ttu-id="52609-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1340">'Razor'</span></span>
- <span data-ttu-id="52609-1341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1343">'Blazor'</span></span>
- <span data-ttu-id="52609-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1344">'Identity'</span></span>
- <span data-ttu-id="52609-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1346">'Razor'</span></span>
- <span data-ttu-id="52609-1347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1349">'Blazor'</span></span>
- <span data-ttu-id="52609-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1350">'Identity'</span></span>
- <span data-ttu-id="52609-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1352">'Razor'</span></span>
- <span data-ttu-id="52609-1353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1355">'Blazor'</span></span>
- <span data-ttu-id="52609-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1356">'Identity'</span></span>
- <span data-ttu-id="52609-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1358">'Razor'</span></span>
- <span data-ttu-id="52609-1359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1361">'Blazor'</span></span>
- <span data-ttu-id="52609-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1362">'Identity'</span></span>
- <span data-ttu-id="52609-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1364">'Razor'</span></span>
- <span data-ttu-id="52609-1365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1367">'Blazor'</span></span>
- <span data-ttu-id="52609-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1368">'Identity'</span></span>
- <span data-ttu-id="52609-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1370">'Razor'</span></span>
- <span data-ttu-id="52609-1371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1373">'Blazor'</span></span>
- <span data-ttu-id="52609-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1374">'Identity'</span></span>
- <span data-ttu-id="52609-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1376">'Razor'</span></span>
- <span data-ttu-id="52609-1377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1379">'Blazor'</span></span>
- <span data-ttu-id="52609-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1380">'Identity'</span></span>
- <span data-ttu-id="52609-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1382">'Razor'</span></span>
- <span data-ttu-id="52609-1383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1385">'Blazor'</span></span>
- <span data-ttu-id="52609-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1386">'Identity'</span></span>
- <span data-ttu-id="52609-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1388">'Razor'</span></span>
- <span data-ttu-id="52609-1389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1391">'Blazor'</span></span>
- <span data-ttu-id="52609-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1392">'Identity'</span></span>
- <span data-ttu-id="52609-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1394">'Razor'</span></span>
- <span data-ttu-id="52609-1395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1397">'Blazor'</span></span>
- <span data-ttu-id="52609-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1398">'Identity'</span></span>
- <span data-ttu-id="52609-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1400">'Razor'</span></span>
- <span data-ttu-id="52609-1401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1403">'Blazor'</span></span>
- <span data-ttu-id="52609-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1404">'Identity'</span></span>
- <span data-ttu-id="52609-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1406">'Razor'</span></span>
- <span data-ttu-id="52609-1407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1409">'Blazor'</span></span>
- <span data-ttu-id="52609-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1410">'Identity'</span></span>
- <span data-ttu-id="52609-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1412">'Razor'</span></span>
- <span data-ttu-id="52609-1413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1415">'Blazor'</span></span>
- <span data-ttu-id="52609-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1416">'Identity'</span></span>
- <span data-ttu-id="52609-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1418">'Razor'</span></span>
- <span data-ttu-id="52609-1419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1421">'Blazor'</span></span>
- <span data-ttu-id="52609-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1422">'Identity'</span></span>
- <span data-ttu-id="52609-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1424">'Razor'</span></span>
- <span data-ttu-id="52609-1425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1427">'Blazor'</span></span>
- <span data-ttu-id="52609-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1428">'Identity'</span></span>
- <span data-ttu-id="52609-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1430">'Razor'</span></span>
- <span data-ttu-id="52609-1431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1433">'Blazor'</span></span>
- <span data-ttu-id="52609-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1434">'Identity'</span></span>
- <span data-ttu-id="52609-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1436">'Razor'</span></span>
- <span data-ttu-id="52609-1437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1439">'Blazor'</span></span>
- <span data-ttu-id="52609-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1440">'Identity'</span></span>
- <span data-ttu-id="52609-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1442">'Razor'</span></span>
- <span data-ttu-id="52609-1443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1444">--------------------------- | | 无前缀                | `CommandLineKey1=value1`                               |
| 双破折号 (`--`)        | `--CommandLineKey2=value2`、`--CommandLineKey2 value2` |
| 正斜杠 (`/`)      | `/CommandLineKey3=value3`、`/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="52609-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="52609-1445">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="52609-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="52609-1446">示例命令：</span><span class="sxs-lookup"><span data-stu-id="52609-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="52609-1447">交换映射</span><span class="sxs-lookup"><span data-stu-id="52609-1447">Switch mappings</span></span>

<span data-ttu-id="52609-1448">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="52609-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="52609-1449">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，需要为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="52609-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="52609-1450">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="52609-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="52609-1451">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="52609-1452">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="52609-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="52609-1453">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="52609-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="52609-1454">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="52609-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="52609-1455">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="52609-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="52609-1456">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="52609-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="52609-1457">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="52609-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="52609-1458">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="52609-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="52609-1459">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="52609-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="52609-1460">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="52609-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52609-1461">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="52609-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="52609-1462">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="52609-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="52609-1463">键</span><span class="sxs-lookup"><span data-stu-id="52609-1463">Key</span></span>       | <span data-ttu-id="52609-1464">“值”</span><span class="sxs-lookup"><span data-stu-id="52609-1464">Value</span></span>             |
| ---
<span data-ttu-id="52609-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1466">'Blazor'</span></span>
- <span data-ttu-id="52609-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1467">'Identity'</span></span>
- <span data-ttu-id="52609-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1469">'Razor'</span></span>
- <span data-ttu-id="52609-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1472">'Blazor'</span></span>
- <span data-ttu-id="52609-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1473">'Identity'</span></span>
- <span data-ttu-id="52609-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1475">'Razor'</span></span>
- <span data-ttu-id="52609-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1478">'Blazor'</span></span>
- <span data-ttu-id="52609-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1479">'Identity'</span></span>
- <span data-ttu-id="52609-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1481">'Razor'</span></span>
- <span data-ttu-id="52609-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1484">'Blazor'</span></span>
- <span data-ttu-id="52609-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1485">'Identity'</span></span>
- <span data-ttu-id="52609-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1487">'Razor'</span></span>
- <span data-ttu-id="52609-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1490">'Blazor'</span></span>
- <span data-ttu-id="52609-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1491">'Identity'</span></span>
- <span data-ttu-id="52609-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1493">'Razor'</span></span>
- <span data-ttu-id="52609-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1496">'Blazor'</span></span>
- <span data-ttu-id="52609-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1497">'Identity'</span></span>
- <span data-ttu-id="52609-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1499">'Razor'</span></span>
- <span data-ttu-id="52609-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1502">'Blazor'</span></span>
- <span data-ttu-id="52609-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1503">'Identity'</span></span>
- <span data-ttu-id="52609-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1505">'Razor'</span></span>
- <span data-ttu-id="52609-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1508">'Blazor'</span></span>
- <span data-ttu-id="52609-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1509">'Identity'</span></span>
- <span data-ttu-id="52609-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1511">'Razor'</span></span>
- <span data-ttu-id="52609-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="52609-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="52609-1514">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="52609-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="52609-1515">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="52609-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="52609-1516">键</span><span class="sxs-lookup"><span data-stu-id="52609-1516">Key</span></span>               | <span data-ttu-id="52609-1517">“值”</span><span class="sxs-lookup"><span data-stu-id="52609-1517">Value</span></span>    |
| ---
<span data-ttu-id="52609-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1519">'Blazor'</span></span>
- <span data-ttu-id="52609-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1520">'Identity'</span></span>
- <span data-ttu-id="52609-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1522">'Razor'</span></span>
- <span data-ttu-id="52609-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1525">'Blazor'</span></span>
- <span data-ttu-id="52609-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1526">'Identity'</span></span>
- <span data-ttu-id="52609-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1528">'Razor'</span></span>
- <span data-ttu-id="52609-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1531">'Blazor'</span></span>
- <span data-ttu-id="52609-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1532">'Identity'</span></span>
- <span data-ttu-id="52609-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1534">'Razor'</span></span>
- <span data-ttu-id="52609-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1537">'Blazor'</span></span>
- <span data-ttu-id="52609-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1538">'Identity'</span></span>
- <span data-ttu-id="52609-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1540">'Razor'</span></span>
- <span data-ttu-id="52609-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1543">'Blazor'</span></span>
- <span data-ttu-id="52609-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1544">'Identity'</span></span>
- <span data-ttu-id="52609-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1546">'Razor'</span></span>
- <span data-ttu-id="52609-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1549">'Blazor'</span></span>
- <span data-ttu-id="52609-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1550">'Identity'</span></span>
- <span data-ttu-id="52609-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1552">'Razor'</span></span>
- <span data-ttu-id="52609-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1555">'Blazor'</span></span>
- <span data-ttu-id="52609-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1556">'Identity'</span></span>
- <span data-ttu-id="52609-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1558">'Razor'</span></span>
- <span data-ttu-id="52609-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1561">'Blazor'</span></span>
- <span data-ttu-id="52609-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1562">'Identity'</span></span>
- <span data-ttu-id="52609-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1564">'Razor'</span></span>
- <span data-ttu-id="52609-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="52609-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="52609-1567">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="52609-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="52609-1569">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="52609-1570">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="52609-1571">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="52609-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="52609-1572">如果使用 [Web 主机](xref:fundamentals/host/web-host)初始化新的主机生成器，且调用 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="52609-1573">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="52609-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52609-1574">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="52609-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="52609-1575">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="52609-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="52609-1576">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置 。</span><span class="sxs-lookup"><span data-stu-id="52609-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="52609-1577">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="52609-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52609-1578">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="52609-1578">Command-line arguments.</span></span>

<span data-ttu-id="52609-1579">环境变量配置提供程序是在配置已根据用户机密和 appsettings 文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="52609-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="52609-1580">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="52609-1581">要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`：</span><span class="sxs-lookup"><span data-stu-id="52609-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="52609-1582">最后调用 `AddEnvironmentVariables`让带给定前缀的环境变量可替代其他提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="52609-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="52609-1583">**示例**</span><span class="sxs-lookup"><span data-stu-id="52609-1583">**Example**</span></span>

<span data-ttu-id="52609-1584">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="52609-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="52609-1585">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="52609-1585">Run the sample app.</span></span> <span data-ttu-id="52609-1586">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="52609-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="52609-1587">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="52609-1588">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="52609-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="52609-1589">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="52609-1590">请参阅示例应用的“Pages/Index.cshtml.cs”文件。</span><span class="sxs-lookup"><span data-stu-id="52609-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="52609-1591">要公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="52609-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="52609-1592">前缀</span><span class="sxs-lookup"><span data-stu-id="52609-1592">Prefixes</span></span>

<span data-ttu-id="52609-1593">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="52609-1594">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="52609-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="52609-1595">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="52609-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="52609-1596">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="52609-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="52609-1597">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="52609-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52609-1598">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="52609-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="52609-1599">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="52609-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="52609-1600">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="52609-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="52609-1601">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="52609-1601">Connection string prefix</span></span> | <span data-ttu-id="52609-1602">提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-1602">Provider</span></span> |
| ---
<span data-ttu-id="52609-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1604">'Blazor'</span></span>
- <span data-ttu-id="52609-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1605">'Identity'</span></span>
- <span data-ttu-id="52609-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1607">'Razor'</span></span>
- <span data-ttu-id="52609-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1610">'Blazor'</span></span>
- <span data-ttu-id="52609-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1611">'Identity'</span></span>
- <span data-ttu-id="52609-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1613">'Razor'</span></span>
- <span data-ttu-id="52609-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1616">'Blazor'</span></span>
- <span data-ttu-id="52609-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1617">'Identity'</span></span>
- <span data-ttu-id="52609-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1619">'Razor'</span></span>
- <span data-ttu-id="52609-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1622">'Blazor'</span></span>
- <span data-ttu-id="52609-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1623">'Identity'</span></span>
- <span data-ttu-id="52609-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1625">'Razor'</span></span>
- <span data-ttu-id="52609-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1628">'Blazor'</span></span>
- <span data-ttu-id="52609-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1629">'Identity'</span></span>
- <span data-ttu-id="52609-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1631">'Razor'</span></span>
- <span data-ttu-id="52609-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1634">'Blazor'</span></span>
- <span data-ttu-id="52609-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1635">'Identity'</span></span>
- <span data-ttu-id="52609-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1637">'Razor'</span></span>
- <span data-ttu-id="52609-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1640">'Blazor'</span></span>
- <span data-ttu-id="52609-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1641">'Identity'</span></span>
- <span data-ttu-id="52609-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1643">'Razor'</span></span>
- <span data-ttu-id="52609-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1646">'Blazor'</span></span>
- <span data-ttu-id="52609-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1647">'Identity'</span></span>
- <span data-ttu-id="52609-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1649">'Razor'</span></span>
- <span data-ttu-id="52609-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1652">'Blazor'</span></span>
- <span data-ttu-id="52609-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1653">'Identity'</span></span>
- <span data-ttu-id="52609-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1655">'Razor'</span></span>
- <span data-ttu-id="52609-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1658">'Blazor'</span></span>
- <span data-ttu-id="52609-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1659">'Identity'</span></span>
- <span data-ttu-id="52609-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1661">'Razor'</span></span>
- <span data-ttu-id="52609-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1664">'Blazor'</span></span>
- <span data-ttu-id="52609-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1665">'Identity'</span></span>
- <span data-ttu-id="52609-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1667">'Razor'</span></span>
- <span data-ttu-id="52609-1668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1670">'Blazor'</span></span>
- <span data-ttu-id="52609-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1671">'Identity'</span></span>
- <span data-ttu-id="52609-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1673">'Razor'</span></span>
- <span data-ttu-id="52609-1674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1675">---- | | `CUSTOMCONNSTR_` | 自定义提供程序 | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="52609-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="52609-1676">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="52609-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="52609-1677">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="52609-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="52609-1678">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="52609-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="52609-1679">环境变量键</span><span class="sxs-lookup"><span data-stu-id="52609-1679">Environment variable key</span></span> | <span data-ttu-id="52609-1680">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="52609-1680">Converted configuration key</span></span> | <span data-ttu-id="52609-1681">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="52609-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="52609-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1683">'Blazor'</span></span>
- <span data-ttu-id="52609-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1684">'Identity'</span></span>
- <span data-ttu-id="52609-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1686">'Razor'</span></span>
- <span data-ttu-id="52609-1687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1689">'Blazor'</span></span>
- <span data-ttu-id="52609-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1690">'Identity'</span></span>
- <span data-ttu-id="52609-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1692">'Razor'</span></span>
- <span data-ttu-id="52609-1693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1695">'Blazor'</span></span>
- <span data-ttu-id="52609-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1696">'Identity'</span></span>
- <span data-ttu-id="52609-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1698">'Razor'</span></span>
- <span data-ttu-id="52609-1699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1701">'Blazor'</span></span>
- <span data-ttu-id="52609-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1702">'Identity'</span></span>
- <span data-ttu-id="52609-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1704">'Razor'</span></span>
- <span data-ttu-id="52609-1705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1707">'Blazor'</span></span>
- <span data-ttu-id="52609-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1708">'Identity'</span></span>
- <span data-ttu-id="52609-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1710">'Razor'</span></span>
- <span data-ttu-id="52609-1711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1713">'Blazor'</span></span>
- <span data-ttu-id="52609-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1714">'Identity'</span></span>
- <span data-ttu-id="52609-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1716">'Razor'</span></span>
- <span data-ttu-id="52609-1717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1719">'Blazor'</span></span>
- <span data-ttu-id="52609-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1720">'Identity'</span></span>
- <span data-ttu-id="52609-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1722">'Razor'</span></span>
- <span data-ttu-id="52609-1723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1725">'Blazor'</span></span>
- <span data-ttu-id="52609-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1726">'Identity'</span></span>
- <span data-ttu-id="52609-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1728">'Razor'</span></span>
- <span data-ttu-id="52609-1729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1731">'Blazor'</span></span>
- <span data-ttu-id="52609-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1732">'Identity'</span></span>
- <span data-ttu-id="52609-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1734">'Razor'</span></span>
- <span data-ttu-id="52609-1735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1737">'Blazor'</span></span>
- <span data-ttu-id="52609-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1738">'Identity'</span></span>
- <span data-ttu-id="52609-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1740">'Razor'</span></span>
- <span data-ttu-id="52609-1741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1743">'Blazor'</span></span>
- <span data-ttu-id="52609-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1744">'Identity'</span></span>
- <span data-ttu-id="52609-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1746">'Razor'</span></span>
- <span data-ttu-id="52609-1747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1749">'Blazor'</span></span>
- <span data-ttu-id="52609-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1750">'Identity'</span></span>
- <span data-ttu-id="52609-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1752">'Razor'</span></span>
- <span data-ttu-id="52609-1753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1755">'Blazor'</span></span>
- <span data-ttu-id="52609-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1756">'Identity'</span></span>
- <span data-ttu-id="52609-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1758">'Razor'</span></span>
- <span data-ttu-id="52609-1759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1761">'Blazor'</span></span>
- <span data-ttu-id="52609-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1762">'Identity'</span></span>
- <span data-ttu-id="52609-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1764">'Razor'</span></span>
- <span data-ttu-id="52609-1765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1767">'Blazor'</span></span>
- <span data-ttu-id="52609-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1768">'Identity'</span></span>
- <span data-ttu-id="52609-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1770">'Razor'</span></span>
- <span data-ttu-id="52609-1771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1773">'Blazor'</span></span>
- <span data-ttu-id="52609-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1774">'Identity'</span></span>
- <span data-ttu-id="52609-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1776">'Razor'</span></span>
- <span data-ttu-id="52609-1777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1779">'Blazor'</span></span>
- <span data-ttu-id="52609-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1780">'Identity'</span></span>
- <span data-ttu-id="52609-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1782">'Razor'</span></span>
- <span data-ttu-id="52609-1783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1785">'Blazor'</span></span>
- <span data-ttu-id="52609-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1786">'Identity'</span></span>
- <span data-ttu-id="52609-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1788">'Razor'</span></span>
- <span data-ttu-id="52609-1789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1791">'Blazor'</span></span>
- <span data-ttu-id="52609-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1792">'Identity'</span></span>
- <span data-ttu-id="52609-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1794">'Razor'</span></span>
- <span data-ttu-id="52609-1795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1797">'Blazor'</span></span>
- <span data-ttu-id="52609-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1798">'Identity'</span></span>
- <span data-ttu-id="52609-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1800">'Razor'</span></span>
- <span data-ttu-id="52609-1801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1803">'Blazor'</span></span>
- <span data-ttu-id="52609-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1804">'Identity'</span></span>
- <span data-ttu-id="52609-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1806">'Razor'</span></span>
- <span data-ttu-id="52609-1807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1809">'Blazor'</span></span>
- <span data-ttu-id="52609-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1810">'Identity'</span></span>
- <span data-ttu-id="52609-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1812">'Razor'</span></span>
- <span data-ttu-id="52609-1813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1815">'Blazor'</span></span>
- <span data-ttu-id="52609-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1816">'Identity'</span></span>
- <span data-ttu-id="52609-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1818">'Razor'</span></span>
- <span data-ttu-id="52609-1819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1821">'Blazor'</span></span>
- <span data-ttu-id="52609-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1822">'Identity'</span></span>
- <span data-ttu-id="52609-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1824">'Razor'</span></span>
- <span data-ttu-id="52609-1825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1827">'Blazor'</span></span>
- <span data-ttu-id="52609-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1828">'Identity'</span></span>
- <span data-ttu-id="52609-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1830">'Razor'</span></span>
- <span data-ttu-id="52609-1831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1833">'Blazor'</span></span>
- <span data-ttu-id="52609-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1834">'Identity'</span></span>
- <span data-ttu-id="52609-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1836">'Razor'</span></span>
- <span data-ttu-id="52609-1837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1839">'Blazor'</span></span>
- <span data-ttu-id="52609-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1840">'Identity'</span></span>
- <span data-ttu-id="52609-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1842">'Razor'</span></span>
- <span data-ttu-id="52609-1843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1845">'Blazor'</span></span>
- <span data-ttu-id="52609-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1846">'Identity'</span></span>
- <span data-ttu-id="52609-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1848">'Razor'</span></span>
- <span data-ttu-id="52609-1849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1851">'Blazor'</span></span>
- <span data-ttu-id="52609-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1852">'Identity'</span></span>
- <span data-ttu-id="52609-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1854">'Razor'</span></span>
- <span data-ttu-id="52609-1855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1857">'Blazor'</span></span>
- <span data-ttu-id="52609-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1858">'Identity'</span></span>
- <span data-ttu-id="52609-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1860">'Razor'</span></span>
- <span data-ttu-id="52609-1861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1863">'Blazor'</span></span>
- <span data-ttu-id="52609-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1864">'Identity'</span></span>
- <span data-ttu-id="52609-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1866">'Razor'</span></span>
- <span data-ttu-id="52609-1867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1869">'Blazor'</span></span>
- <span data-ttu-id="52609-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1870">'Identity'</span></span>
- <span data-ttu-id="52609-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1872">'Razor'</span></span>
- <span data-ttu-id="52609-1873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1875">'Blazor'</span></span>
- <span data-ttu-id="52609-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1876">'Identity'</span></span>
- <span data-ttu-id="52609-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1878">'Razor'</span></span>
- <span data-ttu-id="52609-1879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1881">'Blazor'</span></span>
- <span data-ttu-id="52609-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1882">'Identity'</span></span>
- <span data-ttu-id="52609-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1884">'Razor'</span></span>
- <span data-ttu-id="52609-1885">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1887">'Blazor'</span></span>
- <span data-ttu-id="52609-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1888">'Identity'</span></span>
- <span data-ttu-id="52609-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1890">'Razor'</span></span>
- <span data-ttu-id="52609-1891">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1893">'Blazor'</span></span>
- <span data-ttu-id="52609-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1894">'Identity'</span></span>
- <span data-ttu-id="52609-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1896">'Razor'</span></span>
- <span data-ttu-id="52609-1897">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1899">'Blazor'</span></span>
- <span data-ttu-id="52609-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1900">'Identity'</span></span>
- <span data-ttu-id="52609-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1902">'Razor'</span></span>
- <span data-ttu-id="52609-1903">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1905">'Blazor'</span></span>
- <span data-ttu-id="52609-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1906">'Identity'</span></span>
- <span data-ttu-id="52609-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1908">'Razor'</span></span>
- <span data-ttu-id="52609-1909">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1911">'Blazor'</span></span>
- <span data-ttu-id="52609-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1912">'Identity'</span></span>
- <span data-ttu-id="52609-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1914">'Razor'</span></span>
- <span data-ttu-id="52609-1915">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1917">'Blazor'</span></span>
- <span data-ttu-id="52609-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1918">'Identity'</span></span>
- <span data-ttu-id="52609-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1920">'Razor'</span></span>
- <span data-ttu-id="52609-1921">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1923">'Blazor'</span></span>
- <span data-ttu-id="52609-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1924">'Identity'</span></span>
- <span data-ttu-id="52609-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1926">'Razor'</span></span>
- <span data-ttu-id="52609-1927">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1929">'Blazor'</span></span>
- <span data-ttu-id="52609-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1930">'Identity'</span></span>
- <span data-ttu-id="52609-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1932">'Razor'</span></span>
- <span data-ttu-id="52609-1933">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1935">'Blazor'</span></span>
- <span data-ttu-id="52609-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1936">'Identity'</span></span>
- <span data-ttu-id="52609-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1938">'Razor'</span></span>
- <span data-ttu-id="52609-1939">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1941">'Blazor'</span></span>
- <span data-ttu-id="52609-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1942">'Identity'</span></span>
- <span data-ttu-id="52609-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1944">'Razor'</span></span>
- <span data-ttu-id="52609-1945">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1947">'Blazor'</span></span>
- <span data-ttu-id="52609-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1948">'Identity'</span></span>
- <span data-ttu-id="52609-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1950">'Razor'</span></span>
- <span data-ttu-id="52609-1951">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1953">'Blazor'</span></span>
- <span data-ttu-id="52609-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1954">'Identity'</span></span>
- <span data-ttu-id="52609-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1956">'Razor'</span></span>
- <span data-ttu-id="52609-1957">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1959">'Blazor'</span></span>
- <span data-ttu-id="52609-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1960">'Identity'</span></span>
- <span data-ttu-id="52609-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1962">'Razor'</span></span>
- <span data-ttu-id="52609-1963">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1965">'Blazor'</span></span>
- <span data-ttu-id="52609-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1966">'Identity'</span></span>
- <span data-ttu-id="52609-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1968">'Razor'</span></span>
- <span data-ttu-id="52609-1969">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1971">'Blazor'</span></span>
- <span data-ttu-id="52609-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1972">'Identity'</span></span>
- <span data-ttu-id="52609-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1974">'Razor'</span></span>
- <span data-ttu-id="52609-1975">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1977">'Blazor'</span></span>
- <span data-ttu-id="52609-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1978">'Identity'</span></span>
- <span data-ttu-id="52609-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1980">'Razor'</span></span>
- <span data-ttu-id="52609-1981">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1983">'Blazor'</span></span>
- <span data-ttu-id="52609-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1984">'Identity'</span></span>
- <span data-ttu-id="52609-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1986">'Razor'</span></span>
- <span data-ttu-id="52609-1987">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1989">'Blazor'</span></span>
- <span data-ttu-id="52609-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1990">'Identity'</span></span>
- <span data-ttu-id="52609-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1992">'Razor'</span></span>
- <span data-ttu-id="52609-1993">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-1995">'Blazor'</span></span>
- <span data-ttu-id="52609-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-1996">'Identity'</span></span>
- <span data-ttu-id="52609-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-1998">'Razor'</span></span>
- <span data-ttu-id="52609-1999">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2001">'Blazor'</span></span>
- <span data-ttu-id="52609-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2002">'Identity'</span></span>
- <span data-ttu-id="52609-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2004">'Razor'</span></span>
- <span data-ttu-id="52609-2005">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2007">'Blazor'</span></span>
- <span data-ttu-id="52609-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2008">'Identity'</span></span>
- <span data-ttu-id="52609-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2010">'Razor'</span></span>
- <span data-ttu-id="52609-2011">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2013">'Blazor'</span></span>
- <span data-ttu-id="52609-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2014">'Identity'</span></span>
- <span data-ttu-id="52609-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2016">'Razor'</span></span>
- <span data-ttu-id="52609-2017">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2019">'Blazor'</span></span>
- <span data-ttu-id="52609-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2020">'Identity'</span></span>
- <span data-ttu-id="52609-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2022">'Razor'</span></span>
- <span data-ttu-id="52609-2023">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2025">'Blazor'</span></span>
- <span data-ttu-id="52609-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2026">'Identity'</span></span>
- <span data-ttu-id="52609-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2028">'Razor'</span></span>
- <span data-ttu-id="52609-2029">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}` | 配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="52609-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="52609-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}` | 键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="52609-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52609-2032">值：`MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}` | 键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="52609-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52609-2033">值：`System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}` | 键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="52609-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52609-2034">值：`System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="52609-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="52609-2035">**示例**</span><span class="sxs-lookup"><span data-stu-id="52609-2035">**Example**</span></span>

<span data-ttu-id="52609-2036">在服务器上创建了一个自定义连接字符串环境变量：</span><span class="sxs-lookup"><span data-stu-id="52609-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="52609-2037">名称：`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="52609-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="52609-2038">值：`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="52609-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="52609-2039">如果 `IConfiguration` 已引入并分配给名为 `_config` 的字段，请读取值：</span><span class="sxs-lookup"><span data-stu-id="52609-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="52609-2040">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2040">File Configuration Provider</span></span>

<span data-ttu-id="52609-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="52609-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="52609-2042">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="52609-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="52609-2043">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="52609-2044">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="52609-2045">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="52609-2046">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2046">INI Configuration Provider</span></span>

<span data-ttu-id="52609-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="52609-2048">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52609-2049">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="52609-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="52609-2050">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="52609-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="52609-2051">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="52609-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="52609-2052">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="52609-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="52609-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="52609-2054">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="52609-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="52609-2055">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="52609-2055">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="52609-2056">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="52609-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52609-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2057">section0:key0</span></span>
* <span data-ttu-id="52609-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2058">section0:key1</span></span>
* <span data-ttu-id="52609-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="52609-2059">section1:subsection:key</span></span>
* <span data-ttu-id="52609-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="52609-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="52609-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="52609-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="52609-2062">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="52609-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="52609-2064">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52609-2065">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="52609-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="52609-2066">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="52609-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="52609-2067">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="52609-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="52609-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="52609-2069">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，会自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="52609-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52609-2070">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="52609-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="52609-2071">appsettings.json：先读取此文件。</span><span class="sxs-lookup"><span data-stu-id="52609-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="52609-2072">该文件的环境版本可以替代 appsettings.json 文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="52609-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="52609-2073">appsettings.{Environment}.json：文件的环境版本是根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载的。</span><span class="sxs-lookup"><span data-stu-id="52609-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="52609-2074">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="52609-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52609-2075">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="52609-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="52609-2076">环境变量。</span><span class="sxs-lookup"><span data-stu-id="52609-2076">Environment variables.</span></span>
* <span data-ttu-id="52609-2077">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="52609-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52609-2078">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="52609-2078">Command-line arguments.</span></span>

<span data-ttu-id="52609-2079">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="52609-2080">因此，用户机密、环境变量和命令行参数会替代由 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="52609-2081">构建主机时调用 `ConfigureAppConfiguration` 以指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置： </span><span class="sxs-lookup"><span data-stu-id="52609-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="52609-2082">**示例**</span><span class="sxs-lookup"><span data-stu-id="52609-2082">**Example**</span></span>

<span data-ttu-id="52609-2083">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用：</span><span class="sxs-lookup"><span data-stu-id="52609-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="52609-2084">第一次调用 `AddJsonFile` 会从 appsettings 加载配置：</span><span class="sxs-lookup"><span data-stu-id="52609-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="52609-2085">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="52609-2086">对于示例应用中的 appsettings.Development.json，将加载以下文件：</span><span class="sxs-lookup"><span data-stu-id="52609-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="52609-2087">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="52609-2087">Run the sample app.</span></span> <span data-ttu-id="52609-2088">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="52609-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="52609-2089">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="52609-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="52609-2090">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="52609-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="52609-2091">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="52609-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="52609-2092">打开 Properties/launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="52609-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="52609-2093">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="52609-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="52609-2094">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="52609-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="52609-2095">appsettings.Development.json 中的设置不再替代 appsettings.json 中的设置 。</span><span class="sxs-lookup"><span data-stu-id="52609-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="52609-2096">键 `Logging:LogLevel:Default` 的日志级别为 `Warning`。</span><span class="sxs-lookup"><span data-stu-id="52609-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="52609-2097">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2097">XML Configuration Provider</span></span>

<span data-ttu-id="52609-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="52609-2099">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52609-2100">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="52609-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="52609-2101">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="52609-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="52609-2102">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="52609-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="52609-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="52609-2104">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="52609-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="52609-2105">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="52609-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="52609-2106">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="52609-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="52609-2107">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="52609-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="52609-2108">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="52609-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52609-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2109">section0:key0</span></span>
* <span data-ttu-id="52609-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2110">section0:key1</span></span>
* <span data-ttu-id="52609-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2111">section1:key0</span></span>
* <span data-ttu-id="52609-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2112">section1:key1</span></span>

<span data-ttu-id="52609-2113">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="52609-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="52609-2114">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="52609-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52609-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="52609-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="52609-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="52609-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2118">section:section1:key:key1</span></span>

<span data-ttu-id="52609-2119">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="52609-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="52609-2120">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="52609-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52609-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="52609-2121">key:attribute</span></span>
* <span data-ttu-id="52609-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="52609-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="52609-2123">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="52609-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="52609-2125">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="52609-2125">The key is the file name.</span></span> <span data-ttu-id="52609-2126">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="52609-2126">The value contains the file's contents.</span></span> <span data-ttu-id="52609-2127">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="52609-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="52609-2128">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="52609-2129">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="52609-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="52609-2130">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="52609-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="52609-2131">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="52609-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="52609-2132">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="52609-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="52609-2133">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="52609-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="52609-2134">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="52609-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="52609-2135">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="52609-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="52609-2136">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="52609-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="52609-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="52609-2138">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="52609-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52609-2139">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="52609-2140">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="52609-2141">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="52609-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="52609-2142">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="52609-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="52609-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="52609-2143">GetValue</span></span>

<span data-ttu-id="52609-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的非集合类型。</span><span class="sxs-lookup"><span data-stu-id="52609-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="52609-2145">重载接受默认值。</span><span class="sxs-lookup"><span data-stu-id="52609-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="52609-2146">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="52609-2146">The following example:</span></span>

* <span data-ttu-id="52609-2147">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="52609-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="52609-2148">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="52609-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="52609-2149">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="52609-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="52609-2150">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="52609-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="52609-2151">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="52609-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="52609-2152">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="52609-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="52609-2153">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="52609-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="52609-2154">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="52609-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="52609-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2155">section0:key0</span></span>
* <span data-ttu-id="52609-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2156">section0:key1</span></span>
* <span data-ttu-id="52609-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2157">section1:key0</span></span>
* <span data-ttu-id="52609-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2158">section1:key1</span></span>
* <span data-ttu-id="52609-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="52609-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="52609-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="52609-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="52609-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="52609-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="52609-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="52609-2163">GetSection</span></span>

<span data-ttu-id="52609-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="52609-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="52609-2165">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="52609-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="52609-2166">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="52609-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="52609-2167">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="52609-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="52609-2168">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="52609-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="52609-2169">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="52609-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="52609-2170">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="52609-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="52609-2171">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="52609-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="52609-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="52609-2172">GetChildren</span></span>

<span data-ttu-id="52609-2173">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="52609-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="52609-2174">存在</span><span class="sxs-lookup"><span data-stu-id="52609-2174">Exists</span></span>

<span data-ttu-id="52609-2175">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="52609-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="52609-2176">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="52609-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="52609-2177">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="52609-2177">Bind to an object graph</span></span>

<span data-ttu-id="52609-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="52609-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="52609-2179">与绑定简单对象一样，只绑定公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="52609-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="52609-2180">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs)：</span><span class="sxs-lookup"><span data-stu-id="52609-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="52609-2181">示例应用有一个包含配置数据的 tvshow.xml 文件：</span><span class="sxs-lookup"><span data-stu-id="52609-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="52609-2182">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="52609-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="52609-2183">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="52609-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="52609-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定的类型。</span><span class="sxs-lookup"><span data-stu-id="52609-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="52609-2185">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="52609-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="52609-2186">以下代码演示了如何通过前述示例使用 `Get<T>`：</span><span class="sxs-lookup"><span data-stu-id="52609-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="52609-2187">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="52609-2187">Bind an array to a class</span></span>

<span data-ttu-id="52609-2188">示例应用演示了本部分中介绍的概念。</span><span class="sxs-lookup"><span data-stu-id="52609-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="52609-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="52609-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52609-2190">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="52609-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="52609-2191">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="52609-2191">Binding is provided by convention.</span></span> <span data-ttu-id="52609-2192">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="52609-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="52609-2193">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="52609-2193">**In-memory array processing**</span></span>

<span data-ttu-id="52609-2194">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="52609-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="52609-2195">键</span><span class="sxs-lookup"><span data-stu-id="52609-2195">Key</span></span>             | <span data-ttu-id="52609-2196">“值”</span><span class="sxs-lookup"><span data-stu-id="52609-2196">Value</span></span>  |
| :---
<span data-ttu-id="52609-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2198">'Blazor'</span></span>
- <span data-ttu-id="52609-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2199">'Identity'</span></span>
- <span data-ttu-id="52609-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2201">'Razor'</span></span>
- <span data-ttu-id="52609-2202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2204">'Blazor'</span></span>
- <span data-ttu-id="52609-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2205">'Identity'</span></span>
- <span data-ttu-id="52609-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2207">'Razor'</span></span>
- <span data-ttu-id="52609-2208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2210">'Blazor'</span></span>
- <span data-ttu-id="52609-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2211">'Identity'</span></span>
- <span data-ttu-id="52609-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2213">'Razor'</span></span>
- <span data-ttu-id="52609-2214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2216">'Blazor'</span></span>
- <span data-ttu-id="52609-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2217">'Identity'</span></span>
- <span data-ttu-id="52609-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2219">'Razor'</span></span>
- <span data-ttu-id="52609-2220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="52609-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="52609-2222">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="52609-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="52609-2223">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="52609-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="52609-2224">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="52609-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="52609-2225">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="52609-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="52609-2226">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="52609-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="52609-2227">还可以使用 [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="52609-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="52609-2228">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="52609-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="52609-2229">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="52609-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="52609-2230">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="52609-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="52609-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2232">'Blazor'</span></span>
- <span data-ttu-id="52609-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2233">'Identity'</span></span>
- <span data-ttu-id="52609-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2235">'Razor'</span></span>
- <span data-ttu-id="52609-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2238">'Blazor'</span></span>
- <span data-ttu-id="52609-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2239">'Identity'</span></span>
- <span data-ttu-id="52609-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2241">'Razor'</span></span>
- <span data-ttu-id="52609-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2244">'Blazor'</span></span>
- <span data-ttu-id="52609-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2245">'Identity'</span></span>
- <span data-ttu-id="52609-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2247">'Razor'</span></span>
- <span data-ttu-id="52609-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2250">'Blazor'</span></span>
- <span data-ttu-id="52609-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2251">'Identity'</span></span>
- <span data-ttu-id="52609-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2253">'Razor'</span></span>
- <span data-ttu-id="52609-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2256">'Blazor'</span></span>
- <span data-ttu-id="52609-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2257">'Identity'</span></span>
- <span data-ttu-id="52609-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2259">'Razor'</span></span>
- <span data-ttu-id="52609-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2262">'Blazor'</span></span>
- <span data-ttu-id="52609-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2263">'Identity'</span></span>
- <span data-ttu-id="52609-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2265">'Razor'</span></span>
- <span data-ttu-id="52609-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2268">'Blazor'</span></span>
- <span data-ttu-id="52609-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2269">'Identity'</span></span>
- <span data-ttu-id="52609-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2271">'Razor'</span></span>
- <span data-ttu-id="52609-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2274">'Blazor'</span></span>
- <span data-ttu-id="52609-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2275">'Identity'</span></span>
- <span data-ttu-id="52609-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2277">'Razor'</span></span>
- <span data-ttu-id="52609-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2280">'Blazor'</span></span>
- <span data-ttu-id="52609-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2281">'Identity'</span></span>
- <span data-ttu-id="52609-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2283">'Razor'</span></span>
- <span data-ttu-id="52609-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2286">'Blazor'</span></span>
- <span data-ttu-id="52609-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2287">'Identity'</span></span>
- <span data-ttu-id="52609-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2289">'Razor'</span></span>
- <span data-ttu-id="52609-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2292">'Blazor'</span></span>
- <span data-ttu-id="52609-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2293">'Identity'</span></span>
- <span data-ttu-id="52609-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2295">'Razor'</span></span>
- <span data-ttu-id="52609-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2298">'Blazor'</span></span>
- <span data-ttu-id="52609-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2299">'Identity'</span></span>
- <span data-ttu-id="52609-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2301">'Razor'</span></span>
- <span data-ttu-id="52609-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2304">'Blazor'</span></span>
- <span data-ttu-id="52609-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2305">'Identity'</span></span>
- <span data-ttu-id="52609-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2307">'Razor'</span></span>
- <span data-ttu-id="52609-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2310">'Blazor'</span></span>
- <span data-ttu-id="52609-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2311">'Identity'</span></span>
- <span data-ttu-id="52609-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2313">'Razor'</span></span>
- <span data-ttu-id="52609-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2316">'Blazor'</span></span>
- <span data-ttu-id="52609-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2317">'Identity'</span></span>
- <span data-ttu-id="52609-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2319">'Razor'</span></span>
- <span data-ttu-id="52609-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2322">'Blazor'</span></span>
- <span data-ttu-id="52609-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2323">'Identity'</span></span>
- <span data-ttu-id="52609-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2325">'Razor'</span></span>
- <span data-ttu-id="52609-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2328">'Blazor'</span></span>
- <span data-ttu-id="52609-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2329">'Identity'</span></span>
- <span data-ttu-id="52609-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2331">'Razor'</span></span>
- <span data-ttu-id="52609-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2334">'Blazor'</span></span>
- <span data-ttu-id="52609-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2335">'Identity'</span></span>
- <span data-ttu-id="52609-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2337">'Razor'</span></span>
- <span data-ttu-id="52609-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2340">'Blazor'</span></span>
- <span data-ttu-id="52609-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2341">'Identity'</span></span>
- <span data-ttu-id="52609-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2343">'Razor'</span></span>
- <span data-ttu-id="52609-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2346">'Blazor'</span></span>
- <span data-ttu-id="52609-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2347">'Identity'</span></span>
- <span data-ttu-id="52609-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2349">'Razor'</span></span>
- <span data-ttu-id="52609-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2352">'Blazor'</span></span>
- <span data-ttu-id="52609-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2353">'Identity'</span></span>
- <span data-ttu-id="52609-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2355">'Razor'</span></span>
- <span data-ttu-id="52609-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2358">'Blazor'</span></span>
- <span data-ttu-id="52609-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2359">'Identity'</span></span>
- <span data-ttu-id="52609-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2361">'Razor'</span></span>
- <span data-ttu-id="52609-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="52609-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="52609-2364">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="52609-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="52609-2365">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="52609-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="52609-2366">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="52609-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="52609-2367">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="52609-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="52609-2368">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="52609-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="52609-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="52609-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="52609-2370">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="52609-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="52609-2371">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="52609-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="52609-2372">键</span><span class="sxs-lookup"><span data-stu-id="52609-2372">Key</span></span>             | <span data-ttu-id="52609-2373">“值”</span><span class="sxs-lookup"><span data-stu-id="52609-2373">Value</span></span>  |
| :---
<span data-ttu-id="52609-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2375">'Blazor'</span></span>
- <span data-ttu-id="52609-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2376">'Identity'</span></span>
- <span data-ttu-id="52609-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2378">'Razor'</span></span>
- <span data-ttu-id="52609-2379">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2381">'Blazor'</span></span>
- <span data-ttu-id="52609-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2382">'Identity'</span></span>
- <span data-ttu-id="52609-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2384">'Razor'</span></span>
- <span data-ttu-id="52609-2385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2387">'Blazor'</span></span>
- <span data-ttu-id="52609-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2388">'Identity'</span></span>
- <span data-ttu-id="52609-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2390">'Razor'</span></span>
- <span data-ttu-id="52609-2391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2393">'Blazor'</span></span>
- <span data-ttu-id="52609-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2394">'Identity'</span></span>
- <span data-ttu-id="52609-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2396">'Razor'</span></span>
- <span data-ttu-id="52609-2397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2398">-------: | :----: | | array:entries:3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="52609-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="52609-2399">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="52609-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="52609-2400">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="52609-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="52609-2401">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="52609-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="52609-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2403">'Blazor'</span></span>
- <span data-ttu-id="52609-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2404">'Identity'</span></span>
- <span data-ttu-id="52609-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2406">'Razor'</span></span>
- <span data-ttu-id="52609-2407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2409">'Blazor'</span></span>
- <span data-ttu-id="52609-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2410">'Identity'</span></span>
- <span data-ttu-id="52609-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2412">'Razor'</span></span>
- <span data-ttu-id="52609-2413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2415">'Blazor'</span></span>
- <span data-ttu-id="52609-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2416">'Identity'</span></span>
- <span data-ttu-id="52609-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2418">'Razor'</span></span>
- <span data-ttu-id="52609-2419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2421">'Blazor'</span></span>
- <span data-ttu-id="52609-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2422">'Identity'</span></span>
- <span data-ttu-id="52609-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2424">'Razor'</span></span>
- <span data-ttu-id="52609-2425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2427">'Blazor'</span></span>
- <span data-ttu-id="52609-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2428">'Identity'</span></span>
- <span data-ttu-id="52609-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2430">'Razor'</span></span>
- <span data-ttu-id="52609-2431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2433">'Blazor'</span></span>
- <span data-ttu-id="52609-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2434">'Identity'</span></span>
- <span data-ttu-id="52609-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2436">'Razor'</span></span>
- <span data-ttu-id="52609-2437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2439">'Blazor'</span></span>
- <span data-ttu-id="52609-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2440">'Identity'</span></span>
- <span data-ttu-id="52609-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2442">'Razor'</span></span>
- <span data-ttu-id="52609-2443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2445">'Blazor'</span></span>
- <span data-ttu-id="52609-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2446">'Identity'</span></span>
- <span data-ttu-id="52609-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2448">'Razor'</span></span>
- <span data-ttu-id="52609-2449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2451">'Blazor'</span></span>
- <span data-ttu-id="52609-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2452">'Identity'</span></span>
- <span data-ttu-id="52609-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2454">'Razor'</span></span>
- <span data-ttu-id="52609-2455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2457">'Blazor'</span></span>
- <span data-ttu-id="52609-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2458">'Identity'</span></span>
- <span data-ttu-id="52609-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2460">'Razor'</span></span>
- <span data-ttu-id="52609-2461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2463">'Blazor'</span></span>
- <span data-ttu-id="52609-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2464">'Identity'</span></span>
- <span data-ttu-id="52609-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2466">'Razor'</span></span>
- <span data-ttu-id="52609-2467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2469">'Blazor'</span></span>
- <span data-ttu-id="52609-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2470">'Identity'</span></span>
- <span data-ttu-id="52609-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2472">'Razor'</span></span>
- <span data-ttu-id="52609-2473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2475">'Blazor'</span></span>
- <span data-ttu-id="52609-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2476">'Identity'</span></span>
- <span data-ttu-id="52609-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2478">'Razor'</span></span>
- <span data-ttu-id="52609-2479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2481">'Blazor'</span></span>
- <span data-ttu-id="52609-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2482">'Identity'</span></span>
- <span data-ttu-id="52609-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2484">'Razor'</span></span>
- <span data-ttu-id="52609-2485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2487">'Blazor'</span></span>
- <span data-ttu-id="52609-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2488">'Identity'</span></span>
- <span data-ttu-id="52609-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2490">'Razor'</span></span>
- <span data-ttu-id="52609-2491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2493">'Blazor'</span></span>
- <span data-ttu-id="52609-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2494">'Identity'</span></span>
- <span data-ttu-id="52609-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2496">'Razor'</span></span>
- <span data-ttu-id="52609-2497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2499">'Blazor'</span></span>
- <span data-ttu-id="52609-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2500">'Identity'</span></span>
- <span data-ttu-id="52609-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2502">'Razor'</span></span>
- <span data-ttu-id="52609-2503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2505">'Blazor'</span></span>
- <span data-ttu-id="52609-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2506">'Identity'</span></span>
- <span data-ttu-id="52609-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2508">'Razor'</span></span>
- <span data-ttu-id="52609-2509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2511">'Blazor'</span></span>
- <span data-ttu-id="52609-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2512">'Identity'</span></span>
- <span data-ttu-id="52609-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2514">'Razor'</span></span>
- <span data-ttu-id="52609-2515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2517">'Blazor'</span></span>
- <span data-ttu-id="52609-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2518">'Identity'</span></span>
- <span data-ttu-id="52609-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2520">'Razor'</span></span>
- <span data-ttu-id="52609-2521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2523">'Blazor'</span></span>
- <span data-ttu-id="52609-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2524">'Identity'</span></span>
- <span data-ttu-id="52609-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2526">'Razor'</span></span>
- <span data-ttu-id="52609-2527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2529">'Blazor'</span></span>
- <span data-ttu-id="52609-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2530">'Identity'</span></span>
- <span data-ttu-id="52609-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2532">'Razor'</span></span>
- <span data-ttu-id="52609-2533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="52609-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="52609-2535">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="52609-2535">**JSON array processing**</span></span>

<span data-ttu-id="52609-2536">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="52609-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="52609-2537">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="52609-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="52609-2538">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="52609-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="52609-2539">键</span><span class="sxs-lookup"><span data-stu-id="52609-2539">Key</span></span>                     | <span data-ttu-id="52609-2540">“值”</span><span class="sxs-lookup"><span data-stu-id="52609-2540">Value</span></span>  |
| ---
<span data-ttu-id="52609-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2542">'Blazor'</span></span>
- <span data-ttu-id="52609-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2543">'Identity'</span></span>
- <span data-ttu-id="52609-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2545">'Razor'</span></span>
- <span data-ttu-id="52609-2546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2548">'Blazor'</span></span>
- <span data-ttu-id="52609-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2549">'Identity'</span></span>
- <span data-ttu-id="52609-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2551">'Razor'</span></span>
- <span data-ttu-id="52609-2552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2554">'Blazor'</span></span>
- <span data-ttu-id="52609-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2555">'Identity'</span></span>
- <span data-ttu-id="52609-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2557">'Razor'</span></span>
- <span data-ttu-id="52609-2558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2560">'Blazor'</span></span>
- <span data-ttu-id="52609-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2561">'Identity'</span></span>
- <span data-ttu-id="52609-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2563">'Razor'</span></span>
- <span data-ttu-id="52609-2564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2566">'Blazor'</span></span>
- <span data-ttu-id="52609-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2567">'Identity'</span></span>
- <span data-ttu-id="52609-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2569">'Razor'</span></span>
- <span data-ttu-id="52609-2570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2572">'Blazor'</span></span>
- <span data-ttu-id="52609-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2573">'Identity'</span></span>
- <span data-ttu-id="52609-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2575">'Razor'</span></span>
- <span data-ttu-id="52609-2576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2578">'Blazor'</span></span>
- <span data-ttu-id="52609-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2579">'Identity'</span></span>
- <span data-ttu-id="52609-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2581">'Razor'</span></span>
- <span data-ttu-id="52609-2582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2584">'Blazor'</span></span>
- <span data-ttu-id="52609-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2585">'Identity'</span></span>
- <span data-ttu-id="52609-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2587">'Razor'</span></span>
- <span data-ttu-id="52609-2588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2590">'Blazor'</span></span>
- <span data-ttu-id="52609-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2591">'Identity'</span></span>
- <span data-ttu-id="52609-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2593">'Razor'</span></span>
- <span data-ttu-id="52609-2594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="52609-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="52609-2596">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="52609-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="52609-2597">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="52609-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="52609-2598">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="52609-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="52609-2599">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="52609-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="52609-2600">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="52609-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="52609-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2602">'Blazor'</span></span>
- <span data-ttu-id="52609-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2603">'Identity'</span></span>
- <span data-ttu-id="52609-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2605">'Razor'</span></span>
- <span data-ttu-id="52609-2606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2608">'Blazor'</span></span>
- <span data-ttu-id="52609-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2609">'Identity'</span></span>
- <span data-ttu-id="52609-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2611">'Razor'</span></span>
- <span data-ttu-id="52609-2612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2614">'Blazor'</span></span>
- <span data-ttu-id="52609-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2615">'Identity'</span></span>
- <span data-ttu-id="52609-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2617">'Razor'</span></span>
- <span data-ttu-id="52609-2618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2620">'Blazor'</span></span>
- <span data-ttu-id="52609-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2621">'Identity'</span></span>
- <span data-ttu-id="52609-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2623">'Razor'</span></span>
- <span data-ttu-id="52609-2624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2626">'Blazor'</span></span>
- <span data-ttu-id="52609-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2627">'Identity'</span></span>
- <span data-ttu-id="52609-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2629">'Razor'</span></span>
- <span data-ttu-id="52609-2630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2632">'Blazor'</span></span>
- <span data-ttu-id="52609-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2633">'Identity'</span></span>
- <span data-ttu-id="52609-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2635">'Razor'</span></span>
- <span data-ttu-id="52609-2636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2638">'Blazor'</span></span>
- <span data-ttu-id="52609-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2639">'Identity'</span></span>
- <span data-ttu-id="52609-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2641">'Razor'</span></span>
- <span data-ttu-id="52609-2642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2644">'Blazor'</span></span>
- <span data-ttu-id="52609-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2645">'Identity'</span></span>
- <span data-ttu-id="52609-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2647">'Razor'</span></span>
- <span data-ttu-id="52609-2648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2650">'Blazor'</span></span>
- <span data-ttu-id="52609-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2651">'Identity'</span></span>
- <span data-ttu-id="52609-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2653">'Razor'</span></span>
- <span data-ttu-id="52609-2654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2656">'Blazor'</span></span>
- <span data-ttu-id="52609-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2657">'Identity'</span></span>
- <span data-ttu-id="52609-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2659">'Razor'</span></span>
- <span data-ttu-id="52609-2660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2662">'Blazor'</span></span>
- <span data-ttu-id="52609-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2663">'Identity'</span></span>
- <span data-ttu-id="52609-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2665">'Razor'</span></span>
- <span data-ttu-id="52609-2666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2668">'Blazor'</span></span>
- <span data-ttu-id="52609-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2669">'Identity'</span></span>
- <span data-ttu-id="52609-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2671">'Razor'</span></span>
- <span data-ttu-id="52609-2672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2674">'Blazor'</span></span>
- <span data-ttu-id="52609-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2675">'Identity'</span></span>
- <span data-ttu-id="52609-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2677">'Razor'</span></span>
- <span data-ttu-id="52609-2678">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2680">'Blazor'</span></span>
- <span data-ttu-id="52609-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2681">'Identity'</span></span>
- <span data-ttu-id="52609-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2683">'Razor'</span></span>
- <span data-ttu-id="52609-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2686">'Blazor'</span></span>
- <span data-ttu-id="52609-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2687">'Identity'</span></span>
- <span data-ttu-id="52609-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2689">'Razor'</span></span>
- <span data-ttu-id="52609-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2692">'Blazor'</span></span>
- <span data-ttu-id="52609-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2693">'Identity'</span></span>
- <span data-ttu-id="52609-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2695">'Razor'</span></span>
- <span data-ttu-id="52609-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2698">'Blazor'</span></span>
- <span data-ttu-id="52609-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2699">'Identity'</span></span>
- <span data-ttu-id="52609-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2701">'Razor'</span></span>
- <span data-ttu-id="52609-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2704">'Blazor'</span></span>
- <span data-ttu-id="52609-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2705">'Identity'</span></span>
- <span data-ttu-id="52609-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2707">'Razor'</span></span>
- <span data-ttu-id="52609-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2710">'Blazor'</span></span>
- <span data-ttu-id="52609-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2711">'Identity'</span></span>
- <span data-ttu-id="52609-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2713">'Razor'</span></span>
- <span data-ttu-id="52609-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2716">'Blazor'</span></span>
- <span data-ttu-id="52609-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2717">'Identity'</span></span>
- <span data-ttu-id="52609-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2719">'Razor'</span></span>
- <span data-ttu-id="52609-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2722">'Blazor'</span></span>
- <span data-ttu-id="52609-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2723">'Identity'</span></span>
- <span data-ttu-id="52609-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2725">'Razor'</span></span>
- <span data-ttu-id="52609-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2728">'Blazor'</span></span>
- <span data-ttu-id="52609-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2729">'Identity'</span></span>
- <span data-ttu-id="52609-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2731">'Razor'</span></span>
- <span data-ttu-id="52609-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2734">'Blazor'</span></span>
- <span data-ttu-id="52609-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2735">'Identity'</span></span>
- <span data-ttu-id="52609-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2737">'Razor'</span></span>
- <span data-ttu-id="52609-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2740">'Blazor'</span></span>
- <span data-ttu-id="52609-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2741">'Identity'</span></span>
- <span data-ttu-id="52609-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2743">'Razor'</span></span>
- <span data-ttu-id="52609-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2746">'Blazor'</span></span>
- <span data-ttu-id="52609-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2747">'Identity'</span></span>
- <span data-ttu-id="52609-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2749">'Razor'</span></span>
- <span data-ttu-id="52609-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2752">'Blazor'</span></span>
- <span data-ttu-id="52609-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2753">'Identity'</span></span>
- <span data-ttu-id="52609-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2755">'Razor'</span></span>
- <span data-ttu-id="52609-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2758">'Blazor'</span></span>
- <span data-ttu-id="52609-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2759">'Identity'</span></span>
- <span data-ttu-id="52609-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2761">'Razor'</span></span>
- <span data-ttu-id="52609-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="52609-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="52609-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="52609-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="52609-2764">'Blazor'</span></span>
- <span data-ttu-id="52609-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="52609-2765">'Identity'</span></span>
- <span data-ttu-id="52609-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="52609-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="52609-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="52609-2767">'Razor'</span></span>
- <span data-ttu-id="52609-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="52609-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="52609-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span><span class="sxs-lookup"><span data-stu-id="52609-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="52609-2770">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="52609-2770">Custom configuration provider</span></span>

<span data-ttu-id="52609-2771">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="52609-2772">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="52609-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="52609-2773">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="52609-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="52609-2774">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="52609-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="52609-2775">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="52609-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="52609-2776">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="52609-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="52609-2777">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="52609-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="52609-2778">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="52609-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="52609-2779">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="52609-2780">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="52609-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="52609-2781">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="52609-2782">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="52609-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="52609-2783">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="52609-2784">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="52609-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="52609-2785">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="52609-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="52609-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="52609-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="52609-2787">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="52609-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="52609-2788">Extensions/EntityFrameworkExtensions.cs：</span><span class="sxs-lookup"><span data-stu-id="52609-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="52609-2789">下面的代码演示如何在 Program.cs 中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="52609-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="52609-2790">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="52609-2790">Access configuration during startup</span></span>

<span data-ttu-id="52609-2791">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="52609-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="52609-2792">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="52609-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="52609-2793">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="52609-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="52609-2794">在 Razor Pages 页面或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="52609-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="52609-2795">若要访问 Razor Pages 页面或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) 命名空间添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入该页面或视图。</span><span class="sxs-lookup"><span data-stu-id="52609-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="52609-2796">在 Razor Pages 页面中：</span><span class="sxs-lookup"><span data-stu-id="52609-2796">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="52609-2797">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="52609-2797">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="52609-2798">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="52609-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="52609-2799">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="52609-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="52609-2800">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="52609-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52609-2801">其他资源</span><span class="sxs-lookup"><span data-stu-id="52609-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
