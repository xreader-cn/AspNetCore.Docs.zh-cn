---
title: ASP.NET Core 中的配置
author: rick-anderson
description: 理解如何使用配置 API 配置 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/configuration/index
ms.openlocfilehash: 9e744ec6d0f0dd72bded8284e98fd9ce53056b84
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057968"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="d47c1-103">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="d47c1-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d47c1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d47c1-105">ASP.NET Core 中的配置是使用一个或多个[配置提供程序](#cp)执行的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="d47c1-106">配置提供程序使用各种配置源从键值对读取配置数据：</span><span class="sxs-lookup"><span data-stu-id="d47c1-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="d47c1-107">设置文件，例如 :::no-loc(appsettings.json):::</span><span class="sxs-lookup"><span data-stu-id="d47c1-107">Settings files, such as *:::no-loc(appsettings.json):::*</span></span>
* <span data-ttu-id="d47c1-108">环境变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-108">Environment variables</span></span>
* <span data-ttu-id="d47c1-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d47c1-109">Azure Key Vault</span></span>
* <span data-ttu-id="d47c1-110">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-110">Azure App Configuration</span></span>
* <span data-ttu-id="d47c1-111">命令行参数</span><span class="sxs-lookup"><span data-stu-id="d47c1-111">Command-line arguments</span></span>
* <span data-ttu-id="d47c1-112">已安装或已创建的自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="d47c1-113">目录文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-113">Directory files</span></span>
* <span data-ttu-id="d47c1-114">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="d47c1-114">In-memory .NET objects</span></span>

<span data-ttu-id="d47c1-115">本主题介绍 ASP.NET Core 中的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="d47c1-116">若要了解如何使用控制台应用中的配置，请参阅 [.NET 配置](/dotnet/core/extensions/configuration)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="d47c1-117">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d47c1-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="d47c1-118">默认配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-118">Default configuration</span></span>

<span data-ttu-id="d47c1-119">通过 [dotnet new](/dotnet/core/tools/dotnet-new) 或 Visual Studio 创建的 ASP.NET Core Web 应用会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="d47c1-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="d47c1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="d47c1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource)：添加现有 `IConfiguration` 作为源。</span><span class="sxs-lookup"><span data-stu-id="d47c1-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="d47c1-122">在默认配置示例中，添加[主机](#hvac)配置，并将它设置为应用配置的第一个源。</span><span class="sxs-lookup"><span data-stu-id="d47c1-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="d47c1-123">使用 [JSON 配置提供程序](#file-configuration-provider)的 [:::no-loc(appsettings.json):::](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-123">[:::no-loc(appsettings.json):::](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="d47c1-124">使用 [JSON 配置提供程序](#file-configuration-provider)通过 appsettings.`Environment`.json 提供 。</span><span class="sxs-lookup"><span data-stu-id="d47c1-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="d47c1-125">例如，appsettings.Production.json 和 appsettings.Development.json 。</span><span class="sxs-lookup"><span data-stu-id="d47c1-125">For example, *appsettings*. ***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="d47c1-126">应用在 `Development` 环境中运行时的[应用机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="d47c1-127">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d47c1-128">使用[命令行配置提供程序](#command-line)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="d47c1-129">后来添加的配置提供程序会替代之前的密钥设置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="d47c1-130">例如，如果 :::no-loc(appsettings.json)::: 和环境中都设置了 `MyKey`，则使用环境值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-130">For example, if `MyKey` is set in both *:::no-loc(appsettings.json):::* and the environment, the environment value is used.</span></span> <span data-ttu-id="d47c1-131">使用默认配置提供程序，[命令行配置提供程序](#clcp)将替代所有其他的提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="d47c1-132">若要详细了解 `CreateDefaultBuilder`，请参阅[默认生成器设置](xref:fundamentals/host/generic-host#default-builder-settings)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="d47c1-133">以下代码按添加顺序显示了已启用的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### :::no-loc(appsettings.json):::

<span data-ttu-id="d47c1-134">请考虑以下 :::no-loc(appsettings.json)::: 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-134">Consider the following *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="d47c1-135">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-136">默认的 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 会按以下顺序加载配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="d47c1-137">appsettings.`Environment`.json ：例如，appsettings.Production.json 和 appsettings.Development.json 。</span><span class="sxs-lookup"><span data-stu-id="d47c1-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*. ***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="d47c1-138">文件的环境版本是根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="d47c1-139">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d47c1-140">appsettings.`Environment`.json 值将替代 :::no-loc(appsettings.json)::: 中的键  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-140">*appsettings*.`Environment`. *json* values override keys in *:::no-loc(appsettings.json):::*.</span></span> <span data-ttu-id="d47c1-141">例如，默认情况下：</span><span class="sxs-lookup"><span data-stu-id="d47c1-141">For example, by default:</span></span>

* <span data-ttu-id="d47c1-142">在开发环境中，appsettings.Development.json 配置会覆盖在 :::no-loc(appsettings.json)::: 中找到的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-142">In development, *appsettings*.\* **Development** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::*.</span></span>
* <span data-ttu-id="d47c1-143">在生产环境中，appsettings.Production.json 配置会覆盖在 :::no-loc(appsettings.json)::: 中找到的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-143">In production, *appsettings*.\* **Production** _._json\* configuration overwrites values found in *:::no-loc(appsettings.json):::*.</span></span> <span data-ttu-id="d47c1-144">例如，在将应用部署到 Azure 时。</span><span class="sxs-lookup"><span data-stu-id="d47c1-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="d47c1-145">使用选项模式绑定分层配置数据</span><span class="sxs-lookup"><span data-stu-id="d47c1-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="d47c1-146">使用[默认](#default)配置时，会通过 [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) 启用 :::no-loc(appsettings.json)::: 和 appsettings.`Environment`.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-146">Using the [default](#default) configuration, the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="d47c1-147">应用启动后，对 :::no-loc(appsettings.json)::: 和 appsettings.`Environment`.json 文件所作的更改将由 [JSON 配置提供程序](#jcp)读取  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-147">Changes made to the *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* file \* **after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d47c1-148">有关添加其他 JSON 配置文件的信息，请参阅本文档中的 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="d47c1-149">合并服务集合</span><span class="sxs-lookup"><span data-stu-id="d47c1-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="d47c1-150">安全和机密管理器</span><span class="sxs-lookup"><span data-stu-id="d47c1-150">Security and secret manager</span></span>

<span data-ttu-id="d47c1-151">配置数据指南：</span><span class="sxs-lookup"><span data-stu-id="d47c1-151">Configuration data guidelines:</span></span>

<span data-ttu-id="d47c1-152">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="d47c1-153">[机密管理器](xref:security/app-secrets)可用于存储开发环境中的机密。</span><span class="sxs-lookup"><span data-stu-id="d47c1-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="d47c1-154">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="d47c1-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d47c1-155">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="d47c1-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d47c1-156">[默认情况下](#default)，[机密管理器](xref:security/app-secrets)会在 :::no-loc(appsettings.json)::: 和 appsettings.`Environment`.json 之后读取配置设置  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="d47c1-157">有关存储密码或其他敏感数据的详细信息：</span><span class="sxs-lookup"><span data-stu-id="d47c1-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d47c1-158"><xref:security/app-secrets>：包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="d47c1-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d47c1-159">机密管理器使用[文件配置提供程序](#fcp)将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="d47c1-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="d47c1-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="d47c1-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d47c1-161">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="d47c1-162">环境变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-162">Environment variables</span></span>

<span data-ttu-id="d47c1-163">使用[默认](#default)配置时，<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 会在读取 :::no-loc(appsettings.json):::、appsettings.`Environment`.json 和[机密管理器](xref:security/app-secrets)之后，从环境变量键值对中加载配置  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d47c1-164">因此，从环境中读取的键值会替代从 :::no-loc(appsettings.json):::、appsettings.`Environment`.json 和机密管理器中读取的值  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-164">Therefore, key values read from the environment override values read from *:::no-loc(appsettings.json):::* , *appsettings.*`Environment`*.json* , and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d47c1-165">以下 `set` 命令：</span><span class="sxs-lookup"><span data-stu-id="d47c1-165">The following `set` commands:</span></span>

* <span data-ttu-id="d47c1-166">在 Windows 上设置[上述示例](#appsettingsjson)的环境键和值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="d47c1-167">在使用[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)时测试设置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="d47c1-168">`dotnet run` 命令必须在项目目录中运行。</span><span class="sxs-lookup"><span data-stu-id="d47c1-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="d47c1-169">前面的环境设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-169">The preceding environment settings:</span></span>

* <span data-ttu-id="d47c1-170">仅在进程中设置，这些进程是从设置进程的命令窗口启动的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="d47c1-171">不会由通过 Visual Studio 启动的浏览器读取。</span><span class="sxs-lookup"><span data-stu-id="d47c1-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="d47c1-172">以下 [setx](/windows-server/administration/windows-commands/setx) 命令可用于在 Windows 上设置环境键和值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="d47c1-173">与 `set` 不同，`setx` 设置是持久的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="d47c1-174">`/M` 在系统环境中设置变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="d47c1-175">如果未使用 `/M` 开关，则会设置用户环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="d47c1-176">若要测试前面的命令是否会替代 :::no-loc(appsettings.json)::: 和 appsettings.`Environment`.json  ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-176">To test that the preceding commands override *:::no-loc(appsettings.json):::* and *appsettings.*`Environment`*.json* :</span></span>

* <span data-ttu-id="d47c1-177">使用 Visual Studio：退出并重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="d47c1-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="d47c1-178">使用 CLI：启动新的命令窗口并输入 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="d47c1-179">使用字符串调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 以指定环境变量的前缀：</span><span class="sxs-lookup"><span data-stu-id="d47c1-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="d47c1-180">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="d47c1-180">In the preceding code:</span></span>

* <span data-ttu-id="d47c1-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` 被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="d47c1-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d47c1-182">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="d47c1-183">使用 `MyCustomPrefix_` 前缀设置的环境变量将替代[默认配置提供程序](#default)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="d47c1-184">这包括没有前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="d47c1-185">前缀会在读取配置键值对时被去除。</span><span class="sxs-lookup"><span data-stu-id="d47c1-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="d47c1-186">以下命令对自定义前缀进行测试：</span><span class="sxs-lookup"><span data-stu-id="d47c1-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="d47c1-187">[默认配置](#default)会加载前缀为 `DOTNET_` 和 `ASPNETCORE_` 的环境变量和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="d47c1-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="d47c1-188">`DOTNET_` 和 `ASPNETCORE_` 前缀会由 ASP.NET Core 用于[主机和应用配置](xref:fundamentals/host/generic-host#host-configuration)，但不用于用户配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="d47c1-189">有关主机和应用配置的详细信息，请参阅 [.NET 通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="d47c1-190">在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)上，选择“设置”>“配置”页面上的“新应用程序设置” 。</span><span class="sxs-lookup"><span data-stu-id="d47c1-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="d47c1-191">Azure 应用服务应用程序设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="d47c1-192">已静态加密且通过加密的通道进行传输。</span><span class="sxs-lookup"><span data-stu-id="d47c1-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="d47c1-193">已作为环境变量公开。</span><span class="sxs-lookup"><span data-stu-id="d47c1-193">Exposed as environment variables.</span></span>

<span data-ttu-id="d47c1-194">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d47c1-195">有关 Azure 数据库连接字符串的信息，请参阅[连接字符串前缀](#constr)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="d47c1-196">在 launchSettings.json 中设置的环境变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="d47c1-197">在 launchSettings.json 中设置的环境变量将替代在系统环境中设置的变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="d47c1-198">命令行</span><span class="sxs-lookup"><span data-stu-id="d47c1-198">Command-line</span></span>

<span data-ttu-id="d47c1-199">使用[默认](#default)配置，<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 会从以下配置源后的命令行参数键值对中加载配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="d47c1-200">:::no-loc(appsettings.json)::: 和 appsettings.`Environment`.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-200">*:::no-loc(appsettings.json):::* and *appsettings*.`Environment`. *json* files.</span></span>
* <span data-ttu-id="d47c1-201">开发环境中的[应用机密（机密管理器）](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d47c1-202">环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-202">Environment variables.</span></span>

<span data-ttu-id="d47c1-203">[默认情况下](#default)，在命令行上设置的配置值会替代通过所有其他配置提供程序设置的配置值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="d47c1-204">命令行参数</span><span class="sxs-lookup"><span data-stu-id="d47c1-204">Command-line arguments</span></span>

<span data-ttu-id="d47c1-205">以下命令使用 `=` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="d47c1-206">以下命令使用 `/` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="d47c1-207">以下命令使用 `--` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="d47c1-208">键值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-208">The key value:</span></span>

* <span data-ttu-id="d47c1-209">必须后跟 `=`，或者当值后跟一个空格时，键必须具有一个 `--` 或 `/` 的前缀。</span><span class="sxs-lookup"><span data-stu-id="d47c1-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="d47c1-210">如果使用 `=`，则不是必需的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="d47c1-211">例如 `MySetting=`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="d47c1-212">在同一命令中，请勿将使用 `=` 的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="d47c1-213">交换映射</span><span class="sxs-lookup"><span data-stu-id="d47c1-213">Switch mappings</span></span>

<span data-ttu-id="d47c1-214">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="d47c1-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="d47c1-215">提供针对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法的交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="d47c1-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d47c1-216">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d47c1-217">如果在字典中找到了命令行键，则会传回字典值将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d47c1-218">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d47c1-219">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="d47c1-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d47c1-220">交换必须以 `-` 或 `--` 开头。</span><span class="sxs-lookup"><span data-stu-id="d47c1-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="d47c1-221">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d47c1-222">若要使用交换映射字典，请将它传递到对 `AddCommandLine` 的调用中：</span><span class="sxs-lookup"><span data-stu-id="d47c1-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="d47c1-223">下面的代码显示了替换后的键的键值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-224">以下命令可用于测试键替换：</span><span class="sxs-lookup"><span data-stu-id="d47c1-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="d47c1-225">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="d47c1-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d47c1-226">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d47c1-227">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="d47c1-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d47c1-228">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="d47c1-228">Hierarchical configuration data</span></span>

<span data-ttu-id="d47c1-229">配置 API 在配置键中使用分隔符来展平分层数据，以此来读取分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d47c1-230">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 :::no-loc(appsettings.json)::: 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/:::no-loc(appsettings.json):::)]

<span data-ttu-id="d47c1-231">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-232">读取分层配置数据的首选方法是使用选项模式。</span><span class="sxs-lookup"><span data-stu-id="d47c1-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="d47c1-233">有关详细信息，请参阅本文档中的[绑定分层配置数据](#optpat)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="d47c1-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="d47c1-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d47c1-235">稍后将在 [GetSection、GetChildren 和 Exists](#getsection) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="d47c1-236">配置键和值</span><span class="sxs-lookup"><span data-stu-id="d47c1-236">Configuration keys and values</span></span>

<span data-ttu-id="d47c1-237">配置键：</span><span class="sxs-lookup"><span data-stu-id="d47c1-237">Configuration keys:</span></span>

* <span data-ttu-id="d47c1-238">不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="d47c1-238">Are case-insensitive.</span></span> <span data-ttu-id="d47c1-239">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d47c1-240">如果在多个配置提供程序中设置了某一键和值，则会使用最后添加的提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="d47c1-241">有关详细信息，请参阅[默认配置](#default)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="d47c1-242">分层键</span><span class="sxs-lookup"><span data-stu-id="d47c1-242">Hierarchical keys</span></span>
  * <span data-ttu-id="d47c1-243">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="d47c1-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d47c1-244">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="d47c1-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d47c1-245">所有平台均支持采用双下划线 `__`，并且它会自动转换为冒号 `:`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="d47c1-246">在 Azure Key Vault 中，分层键使用 `--` 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="d47c1-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="d47c1-247">当机密加载到应用的配置中时，[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration) 会自动将 `--` 替换为 `:`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d47c1-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="d47c1-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d47c1-249">数组绑定将在[将数组绑定到类](#boa)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="d47c1-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="d47c1-250">配置值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-250">Configuration values:</span></span>

* <span data-ttu-id="d47c1-251">为字符串。</span><span class="sxs-lookup"><span data-stu-id="d47c1-251">Are strings.</span></span>
* <span data-ttu-id="d47c1-252">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="d47c1-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="d47c1-253">配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-253">Configuration providers</span></span>

<span data-ttu-id="d47c1-254">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d47c1-255">提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-255">Provider</span></span> | <span data-ttu-id="d47c1-256">通过以下对象提供配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="d47c1-257">Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="d47c1-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d47c1-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="d47c1-259">Azure 应用配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="d47c1-260">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="d47c1-261">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="d47c1-262">命令行参数</span><span class="sxs-lookup"><span data-stu-id="d47c1-262">Command-line parameters</span></span> |
| [<span data-ttu-id="d47c1-263">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d47c1-264">自定义源</span><span class="sxs-lookup"><span data-stu-id="d47c1-264">Custom source</span></span> |
| [<span data-ttu-id="d47c1-265">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="d47c1-266">环境变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-266">Environment variables</span></span> |
| [<span data-ttu-id="d47c1-267">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d47c1-268">INI、JSON 和 XML 文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="d47c1-269">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d47c1-270">目录文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-270">Directory files</span></span> |
| [<span data-ttu-id="d47c1-271">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d47c1-272">内存中集合</span><span class="sxs-lookup"><span data-stu-id="d47c1-272">In-memory collections</span></span> |
| [<span data-ttu-id="d47c1-273">机密管理器</span><span class="sxs-lookup"><span data-stu-id="d47c1-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="d47c1-274">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-274">File in the user profile directory</span></span> |

<span data-ttu-id="d47c1-275">按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="d47c1-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="d47c1-276">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="d47c1-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d47c1-277">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="d47c1-277">A typical sequence of configuration providers is:</span></span>

1. *:::no-loc(appsettings.json):::*
1. <span data-ttu-id="d47c1-278">appsettings.`Environment`.json</span><span class="sxs-lookup"><span data-stu-id="d47c1-278">*appsettings*.`Environment`. *json*</span></span>
1. [<span data-ttu-id="d47c1-279">机密管理器</span><span class="sxs-lookup"><span data-stu-id="d47c1-279">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="d47c1-280">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-280">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="d47c1-281">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-281">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="d47c1-282">通常的做法是将命令行配置提供程序添加到一系列提供程序的末尾，使命令行参数能够替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-282">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d47c1-283">[默认配置](#default)中使用了上述提供程序顺序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-283">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="d47c1-284">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="d47c1-284">Connection string prefixes</span></span>

<span data-ttu-id="d47c1-285">对于四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="d47c1-285">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="d47c1-286">这些连接字符串涉及了为应用环境配置 Azure 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d47c1-286">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d47c1-287">使用[默认配置](#default)或没有向 `AddEnvironmentVariables` 应用前缀时，具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="d47c1-287">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d47c1-288">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="d47c1-288">Connection string prefix</span></span> | <span data-ttu-id="d47c1-289">提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-289">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d47c1-290">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-290">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d47c1-291">MySQL</span><span class="sxs-lookup"><span data-stu-id="d47c1-291">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d47c1-292">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="d47c1-292">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d47c1-293">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d47c1-293">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d47c1-294">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="d47c1-294">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d47c1-295">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-295">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d47c1-296">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="d47c1-296">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d47c1-297">环境变量键</span><span class="sxs-lookup"><span data-stu-id="d47c1-297">Environment variable key</span></span> | <span data-ttu-id="d47c1-298">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="d47c1-298">Converted configuration key</span></span> | <span data-ttu-id="d47c1-299">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="d47c1-299">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-300">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="d47c1-300">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-301">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-301">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d47c1-302">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d47c1-302">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-303">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d47c1-304">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d47c1-304">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-305">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d47c1-306">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d47c1-306">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="d47c1-307">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-307">File configuration provider</span></span>

<span data-ttu-id="d47c1-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="d47c1-308"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d47c1-309">以下配置提供程序派生自 `FileConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-309">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="d47c1-310">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-310">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d47c1-311">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-311">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="d47c1-312">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-312">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d47c1-313">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-313">INI configuration provider</span></span>

<span data-ttu-id="d47c1-314"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-314">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d47c1-315">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-315">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="d47c1-316">在前面的代码中，MyIniConfig.ini 和 MyIniConfig.`Environment`.ini 文件中的设置会被以下提供程序中的设置替代  ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-316">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`. *ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d47c1-317">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-317">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d47c1-318">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-318">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d47c1-319">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyIniConfig.ini 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-319">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="d47c1-320">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-320">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="d47c1-321">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-321">JSON configuration provider</span></span>

<span data-ttu-id="d47c1-322"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-322">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="d47c1-323">重载可以指定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-323">Overloads can specify:</span></span>

* <span data-ttu-id="d47c1-324">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="d47c1-324">Whether the file is optional.</span></span>
* <span data-ttu-id="d47c1-325">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-325">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="d47c1-326">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="d47c1-326">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="d47c1-327">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="d47c1-327">The preceding code:</span></span>

* <span data-ttu-id="d47c1-328">通过以下选项将 JSON 配置提供程序配置为加载 MyConfig.json 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-328">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="d47c1-329">`optional: true`：文件是可选的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-329">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="d47c1-330">`reloadOnChange: true`：保存更改后会重载文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-330">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="d47c1-331">读取 MyConfig.json 文件之前的[默认配置提供程序](#default)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-331">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="d47c1-332">MyConfig.json 文件中的设置会替代默认配置提供程序中的设置，包括[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-332">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d47c1-333">通常，你不会希望自定义 JSON 文件替代在[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中设置的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-333">You typically \* **don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d47c1-334">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-334">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="d47c1-335">在前面的代码中，MyConfig.json 和 MyConfig.`Environment`.json 文件中的设置 ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-335">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`. *json* files:</span></span>

* <span data-ttu-id="d47c1-336">会替代 :::no-loc(appsettings.json)::: 和 appsettings.`Environment`.json 文件中的设置  。</span><span class="sxs-lookup"><span data-stu-id="d47c1-336">Override settings in the *:::no-loc(appsettings.json):::* and *appsettings*.`Environment`. *json* files.</span></span>
* <span data-ttu-id="d47c1-337">会被[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中的设置所替代。</span><span class="sxs-lookup"><span data-stu-id="d47c1-337">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d47c1-338">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyConfig.json 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-338">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="d47c1-339">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-339">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="d47c1-340">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-340">XML configuration provider</span></span>

<span data-ttu-id="d47c1-341"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-341">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d47c1-342">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-342">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="d47c1-343">在前面的代码中，MyXMLFile.xml 和 MyXMLFile.`Environment`.xml 文件中的设置会被以下提供程序中的设置替代  ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-343">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`. *xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="d47c1-344">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-344">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="d47c1-345">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-345">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="d47c1-346">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyXMLFile.xml 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-346">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="d47c1-347">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-347">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-348">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="d47c1-348">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="d47c1-349">以下代码会读取前面的配置文件并显示键和值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-349">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-350">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-350">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d47c1-351">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="d47c1-351">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d47c1-352">key:attribute</span><span class="sxs-lookup"><span data-stu-id="d47c1-352">key:attribute</span></span>
* <span data-ttu-id="d47c1-353">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="d47c1-353">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d47c1-354">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-354">Key-per-file configuration provider</span></span>

<span data-ttu-id="d47c1-355"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-355">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d47c1-356">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="d47c1-356">The key is the file name.</span></span> <span data-ttu-id="d47c1-357">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="d47c1-357">The value contains the file's contents.</span></span> <span data-ttu-id="d47c1-358">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="d47c1-358">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d47c1-359">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-359">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d47c1-360">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="d47c1-360">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d47c1-361">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-361">Overloads permit specifying:</span></span>

* <span data-ttu-id="d47c1-362">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="d47c1-362">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d47c1-363">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="d47c1-363">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d47c1-364">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="d47c1-364">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d47c1-365">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-365">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d47c1-366">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-366">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="d47c1-367">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-367">Memory configuration provider</span></span>

<span data-ttu-id="d47c1-368"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-368">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d47c1-369">以下代码将内存集合添加到配置系统中：</span><span class="sxs-lookup"><span data-stu-id="d47c1-369">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="d47c1-370">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述配置设置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-370">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-371">在前面的代码中，`config.AddInMemoryCollection(Dict)` 会被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="d47c1-371">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="d47c1-372">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-372">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="d47c1-373">有关使用 `MemoryConfigurationProvider` 的其他示例，请参阅[绑定数组](#boa)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-373">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="d47c1-374">GetValue</span><span class="sxs-lookup"><span data-stu-id="d47c1-374">GetValue</span></span>

<span data-ttu-id="d47c1-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的类型：</span><span class="sxs-lookup"><span data-stu-id="d47c1-375">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-376">在前面的代码中，如果在配置中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-376">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d47c1-377">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="d47c1-377">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d47c1-378">对于下面的示例，请考虑以下 MySubsection.json 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-378">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="d47c1-379">以下代码将 MySubsection.json 添加到配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-379">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="d47c1-380">GetSection</span><span class="sxs-lookup"><span data-stu-id="d47c1-380">GetSection</span></span>

<span data-ttu-id="d47c1-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 会返回具有指定子节键的配置子节。</span><span class="sxs-lookup"><span data-stu-id="d47c1-381">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d47c1-382">以下代码将返回 `section1` 的值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-382">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-383">以下代码将返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-383">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-384">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-384">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d47c1-385">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-385">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d47c1-386">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="d47c1-386">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d47c1-387">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-387">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="d47c1-388">GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="d47c1-388">GetChildren and Exists</span></span>

<span data-ttu-id="d47c1-389">以下代码将调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 并返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-389">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-390">前面的代码将调用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 以验证该节是否存在：</span><span class="sxs-lookup"><span data-stu-id="d47c1-390">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="d47c1-391">绑定数组</span><span class="sxs-lookup"><span data-stu-id="d47c1-391">Bind an array</span></span>

<span data-ttu-id="d47c1-392">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="d47c1-392">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d47c1-393">公开数值键段的任何数组格式都能够与 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="d47c1-393">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="d47c1-394">请考虑[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的 MyArray.json：</span><span class="sxs-lookup"><span data-stu-id="d47c1-394">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="d47c1-395">以下代码将 MyArray.json 添加到配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-395">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="d47c1-396">以下代码将读取配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-396">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-397">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="d47c1-397">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="d47c1-398">在前面的输出中，索引 3 具有值 `value40`，与 MyArray.json 中的 `"4": "value40",` 相对应。</span><span class="sxs-lookup"><span data-stu-id="d47c1-398">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="d47c1-399">绑定的数组索引是连续的，并且未绑定到配置键索引。</span><span class="sxs-lookup"><span data-stu-id="d47c1-399">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="d47c1-400">配置绑定器不能绑定 NULL 值，也不能在绑定的对象中创建 NULL 条目</span><span class="sxs-lookup"><span data-stu-id="d47c1-400">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="d47c1-401">以下代码将通过 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法加载 `array:entries` 配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-401">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="d47c1-402">以下代码将读取 `arrayDict` `Dictionary` 中的配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-402">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-403">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="d47c1-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="d47c1-404">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-404">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d47c1-405">当绑定包含数组的配置数据时，配置键中的数组索引用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-405">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d47c1-406">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="d47c1-406">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d47c1-407">可以在由任何读取索引 &num;3 键/值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="d47c1-407">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="d47c1-408">请考虑示例下载中的以下 Value3.json 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-408">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="d47c1-409">以下代码包含 Value3.json 和 `arrayDict` `Dictionary` 的配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-409">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="d47c1-410">以下代码将读取上述配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-410">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-411">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="d47c1-411">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="d47c1-412">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="d47c1-412">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="d47c1-413">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-413">Custom configuration provider</span></span>

<span data-ttu-id="d47c1-414">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-414">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d47c1-415">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="d47c1-415">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d47c1-416">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-416">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d47c1-417">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d47c1-417">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d47c1-418">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-418">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d47c1-419">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="d47c1-419">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d47c1-420">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="d47c1-420">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d47c1-421">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="d47c1-421">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d47c1-422">*Models/EFConfigurationValue.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-422">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d47c1-423">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-423">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d47c1-424">*EFConfigurationProvider/EFConfigurationContext.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-424">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d47c1-425">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="d47c1-425">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d47c1-426">*EFConfigurationProvider/EFConfigurationSource.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-426">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d47c1-427">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-427">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d47c1-428">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="d47c1-428">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="d47c1-429">由于[配置密钥不区分大小写](#keys)，因此用来初始化数据库的字典是用不区分大小写的比较程序 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 创建的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-429">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="d47c1-430">*EFConfigurationProvider/EFConfigurationProvider.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-430">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d47c1-431">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-431">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d47c1-432">Extensions/EntityFrameworkExtensions.cs：</span><span class="sxs-lookup"><span data-stu-id="d47c1-432">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d47c1-433">下面的代码演示如何在 Program.cs 中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-433">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="d47c1-434">访问 Startup 中的配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-434">Access configuration in Startup</span></span>

<span data-ttu-id="d47c1-435">以下代码显示 `Startup` 方法中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="d47c1-435">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="d47c1-436">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-436">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="d47c1-437">访问 :::no-loc(Razor)::: Pages 中的配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-437">Access configuration in :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="d47c1-438">以下代码显示 :::no-loc(Razor)::: Pages 中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="d47c1-438">The following code displays configuration data in a :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="d47c1-439">在以下代码中，`MyOptions` 已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 被添加到了服务容器并已绑定到了配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-439">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d47c1-440">以下标记使用 [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: 指令来解析和显示选项值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-440">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="d47c1-441">访问 MVC 视图文件中的配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-441">Access configuration in a MVC view file</span></span>

<span data-ttu-id="d47c1-442">以下代码显示 MVC 视图中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="d47c1-442">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="d47c1-443">使用委托来配置选项</span><span class="sxs-lookup"><span data-stu-id="d47c1-443">Configure options with a delegate</span></span>

<span data-ttu-id="d47c1-444">在委托中配置的选项替代在配置提供程序中设置的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-444">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="d47c1-445">示例应用中的示例 2 展示了如何使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="d47c1-445">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d47c1-446">在以下代码中，向服务容器添加了 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="d47c1-446">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d47c1-447">它使用委托来配置 `MyOptions` 的值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-447">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="d47c1-448">以下代码显示选项值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-448">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="d47c1-449">在前面的示例中，`Option1` 和 `Option2` 的值在 :::no-loc(appsettings.json)::: 中指定，然后被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="d47c1-449">In the preceding example, the values of `Option1` and `Option2` are specified in *:::no-loc(appsettings.json):::* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d47c1-450">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-450">Host versus app configuration</span></span>

<span data-ttu-id="d47c1-451">在配置并启动应用之前，配置并启动主机。</span><span class="sxs-lookup"><span data-stu-id="d47c1-451">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d47c1-452">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="d47c1-452">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d47c1-453">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-453">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d47c1-454">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-454">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d47c1-455">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-455">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="d47c1-456">默认主机配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-456">Default host configuration</span></span>

<span data-ttu-id="d47c1-457">有关使用 [Web 主机](xref:fundamentals/host/web-host)时默认配置的详细信息，请参阅[本主题的 ASP.NET Core 2.2 版本](?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-457">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="d47c1-458">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="d47c1-458">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d47c1-459">使用[环境变量配置提供程序](#environment-variables)通过前缀为 `DOTNET_`的环境变量（例如，`DOTNET_ENVIRONMENT`）提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-459">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="d47c1-460">在配置键值对加载后，前缀 (`DOTNET_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="d47c1-460">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d47c1-461">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-461">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d47c1-462">已建立 Web 主机默认配置 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="d47c1-462">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="d47c1-463">Kestrel 用作 Web 服务器，并使用应用的配置提供程序对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-463">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="d47c1-464">添加主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-464">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="d47c1-465">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则添加转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-465">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="d47c1-466">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="d47c1-466">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d47c1-467">其他配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-467">Other configuration</span></span>

<span data-ttu-id="d47c1-468">本主题仅与应用配置相关。</span><span class="sxs-lookup"><span data-stu-id="d47c1-468">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d47c1-469">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-469">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d47c1-470">launch.json/launchSettings.json 是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="d47c1-470">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d47c1-471"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="d47c1-471">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d47c1-472">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-472">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d47c1-473">web.config 是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="d47c1-473">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d47c1-474">在 launchSettings.json 中设置的环境变量将替代在系统环境中设置的变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-474">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="d47c1-475">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-475">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d47c1-476">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-476">Add configuration from an external assembly</span></span>

<span data-ttu-id="d47c1-477">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="d47c1-477">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d47c1-478">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-478">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d47c1-479">其他资源</span><span class="sxs-lookup"><span data-stu-id="d47c1-479">Additional resources</span></span>

* [<span data-ttu-id="d47c1-480">配置源代码</span><span class="sxs-lookup"><span data-stu-id="d47c1-480">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d47c1-481">ASP.NET Core 中的应用配置基于配置提供程序建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-481">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="d47c1-482">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="d47c1-482">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="d47c1-483">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d47c1-483">Azure Key Vault</span></span>
* <span data-ttu-id="d47c1-484">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-484">Azure App Configuration</span></span>
* <span data-ttu-id="d47c1-485">命令行参数</span><span class="sxs-lookup"><span data-stu-id="d47c1-485">Command-line arguments</span></span>
* <span data-ttu-id="d47c1-486">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-486">Custom providers (installed or created)</span></span>
* <span data-ttu-id="d47c1-487">目录文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-487">Directory files</span></span>
* <span data-ttu-id="d47c1-488">环境变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-488">Environment variables</span></span>
* <span data-ttu-id="d47c1-489">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="d47c1-489">In-memory .NET objects</span></span>
* <span data-ttu-id="d47c1-490">设置文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-490">Settings files</span></span>

<span data-ttu-id="d47c1-491">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="d47c1-491">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d47c1-492">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="d47c1-492">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="d47c1-493">选项模式是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="d47c1-493">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="d47c1-494">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="d47c1-494">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="d47c1-495">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-495">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="d47c1-496">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d47c1-496">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="d47c1-497">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-497">Host versus app configuration</span></span>

<span data-ttu-id="d47c1-498">在配置并启动应用之前，配置并启动主机。</span><span class="sxs-lookup"><span data-stu-id="d47c1-498">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="d47c1-499">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="d47c1-499">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d47c1-500">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-500">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="d47c1-501">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-501">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="d47c1-502">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-502">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="d47c1-503">其他配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-503">Other configuration</span></span>

<span data-ttu-id="d47c1-504">本主题仅与应用配置相关。</span><span class="sxs-lookup"><span data-stu-id="d47c1-504">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="d47c1-505">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-505">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="d47c1-506">launch.json/launchSettings.json 是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="d47c1-506">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="d47c1-507"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="d47c1-507">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="d47c1-508">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-508">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="d47c1-509">web.config 是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="d47c1-509">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="d47c1-510">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-510">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="d47c1-511">默认配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-511">Default configuration</span></span>

<span data-ttu-id="d47c1-512">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-512">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="d47c1-513">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-513">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="d47c1-514">以下内容适用于使用 [Web 主机](xref:fundamentals/host/web-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-514">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d47c1-515">有关使用[通用主机](xref:fundamentals/host/generic-host)时默认配置的详细信息，请参阅[本主题的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-515">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="d47c1-516">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="d47c1-516">Host configuration is provided from:</span></span>
  * <span data-ttu-id="d47c1-517">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-517">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d47c1-518">在配置键值对加载后，前缀 (`ASPNETCORE_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="d47c1-518">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="d47c1-519">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-519">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="d47c1-520">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="d47c1-520">App configuration is provided from:</span></span>
  * <span data-ttu-id="d47c1-521">使用[文件配置提供程序](#file-configuration-provider)的 :::no-loc(appsettings.json):::。</span><span class="sxs-lookup"><span data-stu-id="d47c1-521">*:::no-loc(appsettings.json):::* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d47c1-522">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json 提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-522">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="d47c1-523">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-523">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="d47c1-524">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-524">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="d47c1-525">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="d47c1-526">安全性</span><span class="sxs-lookup"><span data-stu-id="d47c1-526">Security</span></span>

<span data-ttu-id="d47c1-527">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="d47c1-527">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="d47c1-528">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-528">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="d47c1-529">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="d47c1-529">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="d47c1-530">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="d47c1-530">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="d47c1-531">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="d47c1-531">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="d47c1-532"><xref:security/app-secrets>：包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="d47c1-532"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="d47c1-533">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="d47c1-533">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="d47c1-534">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-534">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="d47c1-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="d47c1-535">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="d47c1-536">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-536">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="d47c1-537">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="d47c1-537">Hierarchical configuration data</span></span>

<span data-ttu-id="d47c1-538">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-538">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="d47c1-539">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="d47c1-539">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="d47c1-540">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="d47c1-540">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="d47c1-541">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="d47c1-541">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="d47c1-542">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-542">section0:key0</span></span>
* <span data-ttu-id="d47c1-543">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-543">section0:key1</span></span>
* <span data-ttu-id="d47c1-544">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-544">section1:key0</span></span>
* <span data-ttu-id="d47c1-545">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-545">section1:key1</span></span>

<span data-ttu-id="d47c1-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="d47c1-546"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="d47c1-547">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-547">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="d47c1-548">约定</span><span class="sxs-lookup"><span data-stu-id="d47c1-548">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="d47c1-549">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-549">Sources and providers</span></span>

<span data-ttu-id="d47c1-550">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="d47c1-550">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="d47c1-551">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-551">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="d47c1-552">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="d47c1-552">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="d47c1-553">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-553"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d47c1-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 :::no-loc(Razor)::: Pages <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 中，以获取类的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a :::no-loc(Razor)::: Pages <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="d47c1-555">在下面的示例中，使用 `_config` 字段来访问配置值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-555">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="d47c1-556">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-556">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="d47c1-557">键</span><span class="sxs-lookup"><span data-stu-id="d47c1-557">Keys</span></span>

<span data-ttu-id="d47c1-558">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-558">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="d47c1-559">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="d47c1-559">Keys are case-insensitive.</span></span> <span data-ttu-id="d47c1-560">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-560">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="d47c1-561">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-561">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="d47c1-562">要详细了解重复的 JSON 密钥，请参阅[此 GitHub 问题](https://github.com/dotnet/extensions/issues/2381)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-562">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="d47c1-563">分层键</span><span class="sxs-lookup"><span data-stu-id="d47c1-563">Hierarchical keys</span></span>
  * <span data-ttu-id="d47c1-564">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="d47c1-564">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="d47c1-565">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="d47c1-565">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="d47c1-566">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="d47c1-566">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="d47c1-567">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="d47c1-567">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d47c1-568">将机密加载到应用的配置中时，用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="d47c1-568">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="d47c1-569"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="d47c1-569">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d47c1-570">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="d47c1-570">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="d47c1-571">值</span><span class="sxs-lookup"><span data-stu-id="d47c1-571">Values</span></span>

<span data-ttu-id="d47c1-572">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-572">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="d47c1-573">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="d47c1-573">Values are strings.</span></span>
* <span data-ttu-id="d47c1-574">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="d47c1-574">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="d47c1-575">提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-575">Providers</span></span>

<span data-ttu-id="d47c1-576">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-576">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="d47c1-577">提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-577">Provider</span></span> | <span data-ttu-id="d47c1-578">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="d47c1-578">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="d47c1-579">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="d47c1-579">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) ( *Security* topics)</span></span> | <span data-ttu-id="d47c1-580">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d47c1-580">Azure Key Vault</span></span> |
| <span data-ttu-id="d47c1-581">[Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="d47c1-581">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="d47c1-582">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-582">Azure App Configuration</span></span> |
| [<span data-ttu-id="d47c1-583">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-583">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="d47c1-584">命令行参数</span><span class="sxs-lookup"><span data-stu-id="d47c1-584">Command-line parameters</span></span> |
| [<span data-ttu-id="d47c1-585">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-585">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="d47c1-586">自定义源</span><span class="sxs-lookup"><span data-stu-id="d47c1-586">Custom source</span></span> |
| [<span data-ttu-id="d47c1-587">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-587">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="d47c1-588">环境变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-588">Environment variables</span></span> |
| [<span data-ttu-id="d47c1-589">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-589">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="d47c1-590">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="d47c1-590">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="d47c1-591">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-591">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="d47c1-592">目录文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-592">Directory files</span></span> |
| [<span data-ttu-id="d47c1-593">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-593">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="d47c1-594">内存中集合</span><span class="sxs-lookup"><span data-stu-id="d47c1-594">In-memory collections</span></span> |
| <span data-ttu-id="d47c1-595">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="d47c1-595">[User secrets (Secret Manager)](xref:security/app-secrets) ( *Security* topics)</span></span> | <span data-ttu-id="d47c1-596">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="d47c1-596">File in the user profile directory</span></span> |

<span data-ttu-id="d47c1-597">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="d47c1-597">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="d47c1-598">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="d47c1-598">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="d47c1-599">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="d47c1-599">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="d47c1-600">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="d47c1-600">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="d47c1-601">文件（:::no-loc(appsettings.json):::、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境） </span><span class="sxs-lookup"><span data-stu-id="d47c1-601">Files ( *:::no-loc(appsettings.json):::* , *appsettings.{Environment}.json* , where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="d47c1-602">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="d47c1-602">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="d47c1-603">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="d47c1-603">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="d47c1-604">环境变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-604">Environment variables</span></span>
1. <span data-ttu-id="d47c1-605">命令行参数</span><span class="sxs-lookup"><span data-stu-id="d47c1-605">Command-line arguments</span></span>

<span data-ttu-id="d47c1-606">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-606">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="d47c1-607">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="d47c1-607">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d47c1-608">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-608">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="d47c1-609">用 UseConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="d47c1-609">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="d47c1-610">若要配置主机生成器，请使用配置在主机生成器上调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-610">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="d47c1-611">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d47c1-611">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d47c1-612">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-612">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="d47c1-613">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-613">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="d47c1-614">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-614">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="d47c1-615">删除 CreateDefaultBuilder 添加的提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-615">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="d47c1-616">要删除 `CreateDefaultBuilder` 添加的提供程序，请先对 [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) 调用 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)：</span><span class="sxs-lookup"><span data-stu-id="d47c1-616">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="d47c1-617">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-617">Consume configuration during app startup</span></span>

<span data-ttu-id="d47c1-618">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-618">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d47c1-619">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-619">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="d47c1-620">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-620">Command-line Configuration Provider</span></span>

<span data-ttu-id="d47c1-621"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-621">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="d47c1-622">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-622">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d47c1-623">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-623">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="d47c1-624">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-624">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d47c1-625">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="d47c1-625">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d47c1-626">:::no-loc(appsettings.json)::: 和 appsettings.{Environment}.json 文件中的可选配置 。</span><span class="sxs-lookup"><span data-stu-id="d47c1-626">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d47c1-627">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="d47c1-627">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d47c1-628">环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-628">Environment variables.</span></span>

<span data-ttu-id="d47c1-629">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-629">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="d47c1-630">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-630">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="d47c1-631">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-631">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="d47c1-632">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="d47c1-632">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="d47c1-633">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-633">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d47c1-634">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-634">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="d47c1-635">**示例**</span><span class="sxs-lookup"><span data-stu-id="d47c1-635">**Example**</span></span>

<span data-ttu-id="d47c1-636">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-636">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="d47c1-637">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="d47c1-637">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="d47c1-638">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-638">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="d47c1-639">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="d47c1-639">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d47c1-640">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-640">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="d47c1-641">自变量</span><span class="sxs-lookup"><span data-stu-id="d47c1-641">Arguments</span></span>

<span data-ttu-id="d47c1-642">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="d47c1-642">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="d47c1-643">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-643">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="d47c1-644">键前缀</span><span class="sxs-lookup"><span data-stu-id="d47c1-644">Key prefix</span></span>               | <span data-ttu-id="d47c1-645">示例</span><span class="sxs-lookup"><span data-stu-id="d47c1-645">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="d47c1-646">无前缀</span><span class="sxs-lookup"><span data-stu-id="d47c1-646">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="d47c1-647">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="d47c1-647">Two dashes (`--`)</span></span>        | <span data-ttu-id="d47c1-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="d47c1-648">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="d47c1-649">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="d47c1-649">Forward slash (`/`)</span></span>      | <span data-ttu-id="d47c1-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="d47c1-650">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="d47c1-651">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-651">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="d47c1-652">示例命令：</span><span class="sxs-lookup"><span data-stu-id="d47c1-652">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="d47c1-653">交换映射</span><span class="sxs-lookup"><span data-stu-id="d47c1-653">Switch mappings</span></span>

<span data-ttu-id="d47c1-654">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="d47c1-654">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="d47c1-655">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，需要为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="d47c1-655">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="d47c1-656">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-656">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="d47c1-657">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-657">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="d47c1-658">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-658">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="d47c1-659">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="d47c1-659">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="d47c1-660">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="d47c1-660">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="d47c1-661">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-661">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="d47c1-662">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="d47c1-662">Create a switch mappings dictionary.</span></span> <span data-ttu-id="d47c1-663">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="d47c1-663">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="d47c1-664">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-664">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="d47c1-665">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="d47c1-665">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="d47c1-666">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-666">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d47c1-667">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="d47c1-667">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="d47c1-668">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-668">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="d47c1-669">密钥</span><span class="sxs-lookup"><span data-stu-id="d47c1-669">Key</span></span>       | <span data-ttu-id="d47c1-670">值</span><span class="sxs-lookup"><span data-stu-id="d47c1-670">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="d47c1-671">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-671">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="d47c1-672">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-672">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="d47c1-673">键</span><span class="sxs-lookup"><span data-stu-id="d47c1-673">Key</span></span>               | <span data-ttu-id="d47c1-674">“值”</span><span class="sxs-lookup"><span data-stu-id="d47c1-674">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="d47c1-675">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-675">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="d47c1-676"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-676">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="d47c1-677">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-677">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d47c1-678">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-678">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="d47c1-679">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-679">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="d47c1-680">如果使用 [Web 主机](xref:fundamentals/host/web-host)初始化新的主机生成器，且调用 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-680">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="d47c1-681">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-681">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d47c1-682">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="d47c1-682">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d47c1-683">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-683">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="d47c1-684">:::no-loc(appsettings.json)::: 和 appsettings.{Environment}.json 文件中的可选配置 。</span><span class="sxs-lookup"><span data-stu-id="d47c1-684">Optional configuration from *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="d47c1-685">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="d47c1-685">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d47c1-686">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="d47c1-686">Command-line arguments.</span></span>

<span data-ttu-id="d47c1-687">环境变量配置提供程序是在配置已根据用户机密和 appsettings 文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-687">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="d47c1-688">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-688">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="d47c1-689">要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-689">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="d47c1-690">最后调用 `AddEnvironmentVariables`让带给定前缀的环境变量可替代其他提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-690">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="d47c1-691">**示例**</span><span class="sxs-lookup"><span data-stu-id="d47c1-691">**Example**</span></span>

<span data-ttu-id="d47c1-692">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-692">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="d47c1-693">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-693">Run the sample app.</span></span> <span data-ttu-id="d47c1-694">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="d47c1-694">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d47c1-695">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-695">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="d47c1-696">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-696">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="d47c1-697">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-697">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="d47c1-698">请参阅示例应用的“Pages/Index.cshtml.cs”文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-698">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="d47c1-699">要公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="d47c1-699">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="d47c1-700">前缀</span><span class="sxs-lookup"><span data-stu-id="d47c1-700">Prefixes</span></span>

<span data-ttu-id="d47c1-701">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-701">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="d47c1-702">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="d47c1-702">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="d47c1-703">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="d47c1-703">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="d47c1-704">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="d47c1-704">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="d47c1-705">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-705">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d47c1-706">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="d47c1-706">**Connection string prefixes**</span></span>

<span data-ttu-id="d47c1-707">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="d47c1-707">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="d47c1-708">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="d47c1-708">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="d47c1-709">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="d47c1-709">Connection string prefix</span></span> | <span data-ttu-id="d47c1-710">提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-710">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="d47c1-711">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-711">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="d47c1-712">MySQL</span><span class="sxs-lookup"><span data-stu-id="d47c1-712">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="d47c1-713">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="d47c1-713">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="d47c1-714">SQL Server</span><span class="sxs-lookup"><span data-stu-id="d47c1-714">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="d47c1-715">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="d47c1-715">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="d47c1-716">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-716">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="d47c1-717">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="d47c1-717">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="d47c1-718">环境变量键</span><span class="sxs-lookup"><span data-stu-id="d47c1-718">Environment variable key</span></span> | <span data-ttu-id="d47c1-719">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="d47c1-719">Converted configuration key</span></span> | <span data-ttu-id="d47c1-720">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="d47c1-720">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-721">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="d47c1-721">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-722">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-722">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d47c1-723">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="d47c1-723">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-724">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d47c1-725">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d47c1-725">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="d47c1-726">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="d47c1-727">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="d47c1-727">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="d47c1-728">**示例**</span><span class="sxs-lookup"><span data-stu-id="d47c1-728">**Example**</span></span>

<span data-ttu-id="d47c1-729">在服务器上创建了一个自定义连接字符串环境变量：</span><span class="sxs-lookup"><span data-stu-id="d47c1-729">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="d47c1-730">名称：`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="d47c1-730">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="d47c1-731">值：`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="d47c1-731">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="d47c1-732">如果 `IConfiguration` 已引入并分配给名为 `_config` 的字段，请读取值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-732">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="d47c1-733">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-733">File Configuration Provider</span></span>

<span data-ttu-id="d47c1-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="d47c1-734"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="d47c1-735">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="d47c1-735">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="d47c1-736">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-736">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="d47c1-737">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-737">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="d47c1-738">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-738">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="d47c1-739">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-739">INI Configuration Provider</span></span>

<span data-ttu-id="d47c1-740"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-740">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="d47c1-741">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-741">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d47c1-742">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="d47c1-742">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="d47c1-743">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-743">Overloads permit specifying:</span></span>

* <span data-ttu-id="d47c1-744">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="d47c1-744">Whether the file is optional.</span></span>
* <span data-ttu-id="d47c1-745">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-745">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d47c1-746"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-746">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d47c1-747">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-747">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d47c1-748">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="d47c1-748">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="d47c1-749">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="d47c1-749">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d47c1-750">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-750">section0:key0</span></span>
* <span data-ttu-id="d47c1-751">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-751">section0:key1</span></span>
* <span data-ttu-id="d47c1-752">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="d47c1-752">section1:subsection:key</span></span>
* <span data-ttu-id="d47c1-753">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="d47c1-753">section2:subsection0:key</span></span>
* <span data-ttu-id="d47c1-754">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="d47c1-754">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="d47c1-755">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-755">JSON Configuration Provider</span></span>

<span data-ttu-id="d47c1-756"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-756">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="d47c1-757">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-757">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d47c1-758">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-758">Overloads permit specifying:</span></span>

* <span data-ttu-id="d47c1-759">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="d47c1-759">Whether the file is optional.</span></span>
* <span data-ttu-id="d47c1-760">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-760">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d47c1-761"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-761">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d47c1-762">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，会自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-762">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d47c1-763">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-763">The method is called to load configuration from:</span></span>

* <span data-ttu-id="d47c1-764">:::no-loc(appsettings.json):::：先读取此文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-764">*:::no-loc(appsettings.json):::* : This file is read first.</span></span> <span data-ttu-id="d47c1-765">文件的环境版本可替代 :::no-loc(appsettings.json)::: 文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-765">The environment version of the file can override the values provided by the *:::no-loc(appsettings.json):::* file.</span></span>
* <span data-ttu-id="d47c1-766">appsettings.{Environment}.json：文件的环境版本是根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-766">*appsettings.{Environment}.json* : The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="d47c1-767">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-767">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="d47c1-768">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="d47c1-768">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="d47c1-769">环境变量。</span><span class="sxs-lookup"><span data-stu-id="d47c1-769">Environment variables.</span></span>
* <span data-ttu-id="d47c1-770">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="d47c1-770">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="d47c1-771">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="d47c1-771">Command-line arguments.</span></span>

<span data-ttu-id="d47c1-772">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-772">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="d47c1-773">因此，用户机密、环境变量和命令行参数会替代由 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-773">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="d47c1-774">构建主机时，调用 `ConfigureAppConfiguration` 来指定除 :::no-loc(appsettings.json)::: 和 appsettings.{Environment}.json 以外的文件的应用配置 ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-774">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *:::no-loc(appsettings.json):::* and *appsettings.{Environment}.json* :</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d47c1-775">**示例**</span><span class="sxs-lookup"><span data-stu-id="d47c1-775">**Example**</span></span>

<span data-ttu-id="d47c1-776">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用：</span><span class="sxs-lookup"><span data-stu-id="d47c1-776">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="d47c1-777">第一次调用 `AddJsonFile` 会从 :::no-loc(appsettings.json)::: 加载配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-777">The first call to `AddJsonFile` loads configuration from *:::no-loc(appsettings.json):::* :</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/:::no-loc(appsettings.json):::)]

* <span data-ttu-id="d47c1-778">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-778">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="d47c1-779">对于示例应用中的 appsettings.Development.json，将加载以下文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-779">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="d47c1-780">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-780">Run the sample app.</span></span> <span data-ttu-id="d47c1-781">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="d47c1-781">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="d47c1-782">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="d47c1-782">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="d47c1-783">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-783">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="d47c1-784">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="d47c1-784">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="d47c1-785">打开 Properties/launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-785">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="d47c1-786">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-786">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="d47c1-787">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-787">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="d47c1-788">appsettings.Development.json 中的设置不再会替代 :::no-loc(appsettings.json)::: 中的设置 。</span><span class="sxs-lookup"><span data-stu-id="d47c1-788">The settings in the *appsettings.Development.json* no longer override the settings in *:::no-loc(appsettings.json):::*.</span></span> <span data-ttu-id="d47c1-789">键 `Logging:LogLevel:Default` 的日志级别为 `Warning`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-789">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="d47c1-790">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-790">XML Configuration Provider</span></span>

<span data-ttu-id="d47c1-791"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-791">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="d47c1-792">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-792">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d47c1-793">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-793">Overloads permit specifying:</span></span>

* <span data-ttu-id="d47c1-794">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="d47c1-794">Whether the file is optional.</span></span>
* <span data-ttu-id="d47c1-795">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-795">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="d47c1-796"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-796">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="d47c1-797">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="d47c1-797">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="d47c1-798">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="d47c1-798">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="d47c1-799">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-799">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="d47c1-800">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="d47c1-800">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="d47c1-801">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="d47c1-801">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d47c1-802">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-802">section0:key0</span></span>
* <span data-ttu-id="d47c1-803">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-803">section0:key1</span></span>
* <span data-ttu-id="d47c1-804">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-804">section1:key0</span></span>
* <span data-ttu-id="d47c1-805">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-805">section1:key1</span></span>

<span data-ttu-id="d47c1-806">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="d47c1-806">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="d47c1-807">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="d47c1-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d47c1-808">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-808">section:section0:key:key0</span></span>
* <span data-ttu-id="d47c1-809">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-809">section:section0:key:key1</span></span>
* <span data-ttu-id="d47c1-810">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-810">section:section1:key:key0</span></span>
* <span data-ttu-id="d47c1-811">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-811">section:section1:key:key1</span></span>

<span data-ttu-id="d47c1-812">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-812">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="d47c1-813">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="d47c1-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="d47c1-814">key:attribute</span><span class="sxs-lookup"><span data-stu-id="d47c1-814">key:attribute</span></span>
* <span data-ttu-id="d47c1-815">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="d47c1-815">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="d47c1-816">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-816">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="d47c1-817"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-817">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="d47c1-818">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="d47c1-818">The key is the file name.</span></span> <span data-ttu-id="d47c1-819">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="d47c1-819">The value contains the file's contents.</span></span> <span data-ttu-id="d47c1-820">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="d47c1-820">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="d47c1-821">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-821">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="d47c1-822">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="d47c1-822">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="d47c1-823">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="d47c1-823">Overloads permit specifying:</span></span>

* <span data-ttu-id="d47c1-824">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="d47c1-824">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="d47c1-825">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="d47c1-825">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="d47c1-826">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="d47c1-826">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="d47c1-827">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-827">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="d47c1-828">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="d47c1-829">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-829">Memory Configuration Provider</span></span>

<span data-ttu-id="d47c1-830"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="d47c1-830">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="d47c1-831">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d47c1-831">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="d47c1-832">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-832">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="d47c1-833">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="d47c1-834">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="d47c1-834">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="d47c1-835">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="d47c1-835">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="d47c1-836">GetValue</span><span class="sxs-lookup"><span data-stu-id="d47c1-836">GetValue</span></span>

<span data-ttu-id="d47c1-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的非集合类型。</span><span class="sxs-lookup"><span data-stu-id="d47c1-837">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="d47c1-838">重载接受默认值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-838">An overload accepts a default value.</span></span>

<span data-ttu-id="d47c1-839">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="d47c1-839">The following example:</span></span>

* <span data-ttu-id="d47c1-840">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-840">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="d47c1-841">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-841">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="d47c1-842">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-842">Types the value as an `int`.</span></span>
* <span data-ttu-id="d47c1-843">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="d47c1-843">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="d47c1-844">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="d47c1-844">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="d47c1-845">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="d47c1-845">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="d47c1-846">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="d47c1-846">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="d47c1-847">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-847">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="d47c1-848">section0:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-848">section0:key0</span></span>
* <span data-ttu-id="d47c1-849">section0:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-849">section0:key1</span></span>
* <span data-ttu-id="d47c1-850">section1:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-850">section1:key0</span></span>
* <span data-ttu-id="d47c1-851">section1:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-851">section1:key1</span></span>
* <span data-ttu-id="d47c1-852">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-852">section2:subsection0:key0</span></span>
* <span data-ttu-id="d47c1-853">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-853">section2:subsection0:key1</span></span>
* <span data-ttu-id="d47c1-854">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="d47c1-854">section2:subsection1:key0</span></span>
* <span data-ttu-id="d47c1-855">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="d47c1-855">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="d47c1-856">GetSection</span><span class="sxs-lookup"><span data-stu-id="d47c1-856">GetSection</span></span>

<span data-ttu-id="d47c1-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="d47c1-857">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="d47c1-858">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="d47c1-858">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="d47c1-859">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="d47c1-859">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="d47c1-860">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="d47c1-860">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="d47c1-861">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-861">`GetSection` never returns `null`.</span></span> <span data-ttu-id="d47c1-862">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-862">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="d47c1-863">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="d47c1-863">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="d47c1-864">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="d47c1-864">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="d47c1-865">GetChildren</span><span class="sxs-lookup"><span data-stu-id="d47c1-865">GetChildren</span></span>

<span data-ttu-id="d47c1-866">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="d47c1-866">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="d47c1-867">存在</span><span class="sxs-lookup"><span data-stu-id="d47c1-867">Exists</span></span>

<span data-ttu-id="d47c1-868">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="d47c1-868">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="d47c1-869">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="d47c1-869">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="d47c1-870">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="d47c1-870">Bind to an object graph</span></span>

<span data-ttu-id="d47c1-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="d47c1-871"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="d47c1-872">与绑定简单对象一样，只绑定公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="d47c1-872">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="d47c1-873">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs)：</span><span class="sxs-lookup"><span data-stu-id="d47c1-873">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes ( *Models/TvShow.cs* ):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="d47c1-874">示例应用有一个包含配置数据的 tvshow.xml 文件：</span><span class="sxs-lookup"><span data-stu-id="d47c1-874">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="d47c1-875">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="d47c1-875">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="d47c1-876">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="d47c1-876">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="d47c1-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定的类型。</span><span class="sxs-lookup"><span data-stu-id="d47c1-877">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="d47c1-878">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="d47c1-878">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="d47c1-879">以下代码演示了如何通过前述示例使用 `Get<T>`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-879">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d47c1-880">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="d47c1-880">Bind an array to a class</span></span>

<span data-ttu-id="d47c1-881">示例应用演示了本部分中介绍的概念。</span><span class="sxs-lookup"><span data-stu-id="d47c1-881">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="d47c1-882"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="d47c1-882">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="d47c1-883">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="d47c1-883">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="d47c1-884">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-884">Binding is provided by convention.</span></span> <span data-ttu-id="d47c1-885">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="d47c1-885">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="d47c1-886">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="d47c1-886">**In-memory array processing**</span></span>

<span data-ttu-id="d47c1-887">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-887">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="d47c1-888">键</span><span class="sxs-lookup"><span data-stu-id="d47c1-888">Key</span></span>             | <span data-ttu-id="d47c1-889">“值”</span><span class="sxs-lookup"><span data-stu-id="d47c1-889">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d47c1-890">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="d47c1-890">array:entries:0</span></span> | <span data-ttu-id="d47c1-891">value0</span><span class="sxs-lookup"><span data-stu-id="d47c1-891">value0</span></span> |
| <span data-ttu-id="d47c1-892">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="d47c1-892">array:entries:1</span></span> | <span data-ttu-id="d47c1-893">value1</span><span class="sxs-lookup"><span data-stu-id="d47c1-893">value1</span></span> |
| <span data-ttu-id="d47c1-894">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="d47c1-894">array:entries:2</span></span> | <span data-ttu-id="d47c1-895">value2</span><span class="sxs-lookup"><span data-stu-id="d47c1-895">value2</span></span> |
| <span data-ttu-id="d47c1-896">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="d47c1-896">array:entries:4</span></span> | <span data-ttu-id="d47c1-897">value4</span><span class="sxs-lookup"><span data-stu-id="d47c1-897">value4</span></span> |
| <span data-ttu-id="d47c1-898">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="d47c1-898">array:entries:5</span></span> | <span data-ttu-id="d47c1-899">value5</span><span class="sxs-lookup"><span data-stu-id="d47c1-899">value5</span></span> |

<span data-ttu-id="d47c1-900">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="d47c1-900">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="d47c1-901">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-901">The array skips a value for index &num;3.</span></span> <span data-ttu-id="d47c1-902">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="d47c1-902">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="d47c1-903">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="d47c1-903">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="d47c1-904">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="d47c1-904">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="d47c1-905">还可以使用 [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="d47c1-905">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="d47c1-906">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-906">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="d47c1-907">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="d47c1-907">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d47c1-908">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="d47c1-908">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d47c1-909">0</span><span class="sxs-lookup"><span data-stu-id="d47c1-909">0</span></span>                            | <span data-ttu-id="d47c1-910">value0</span><span class="sxs-lookup"><span data-stu-id="d47c1-910">value0</span></span>                       |
| <span data-ttu-id="d47c1-911">1</span><span class="sxs-lookup"><span data-stu-id="d47c1-911">1</span></span>                            | <span data-ttu-id="d47c1-912">value1</span><span class="sxs-lookup"><span data-stu-id="d47c1-912">value1</span></span>                       |
| <span data-ttu-id="d47c1-913">2</span><span class="sxs-lookup"><span data-stu-id="d47c1-913">2</span></span>                            | <span data-ttu-id="d47c1-914">value2</span><span class="sxs-lookup"><span data-stu-id="d47c1-914">value2</span></span>                       |
| <span data-ttu-id="d47c1-915">3</span><span class="sxs-lookup"><span data-stu-id="d47c1-915">3</span></span>                            | <span data-ttu-id="d47c1-916">value4</span><span class="sxs-lookup"><span data-stu-id="d47c1-916">value4</span></span>                       |
| <span data-ttu-id="d47c1-917">4</span><span class="sxs-lookup"><span data-stu-id="d47c1-917">4</span></span>                            | <span data-ttu-id="d47c1-918">value5</span><span class="sxs-lookup"><span data-stu-id="d47c1-918">value5</span></span>                       |

<span data-ttu-id="d47c1-919">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-919">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="d47c1-920">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="d47c1-920">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="d47c1-921">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="d47c1-921">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="d47c1-922">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="d47c1-922">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="d47c1-923">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="d47c1-923">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="d47c1-924">*missing_value.json* :</span><span class="sxs-lookup"><span data-stu-id="d47c1-924">*missing_value.json* :</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="d47c1-925">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="d47c1-925">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="d47c1-926">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="d47c1-926">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="d47c1-927">密钥</span><span class="sxs-lookup"><span data-stu-id="d47c1-927">Key</span></span>             | <span data-ttu-id="d47c1-928">值</span><span class="sxs-lookup"><span data-stu-id="d47c1-928">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="d47c1-929">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="d47c1-929">array:entries:3</span></span> | <span data-ttu-id="d47c1-930">value3</span><span class="sxs-lookup"><span data-stu-id="d47c1-930">value3</span></span> |

<span data-ttu-id="d47c1-931">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-931">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="d47c1-932">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="d47c1-932">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="d47c1-933">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="d47c1-933">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="d47c1-934">0</span><span class="sxs-lookup"><span data-stu-id="d47c1-934">0</span></span>                            | <span data-ttu-id="d47c1-935">value0</span><span class="sxs-lookup"><span data-stu-id="d47c1-935">value0</span></span>                       |
| <span data-ttu-id="d47c1-936">1</span><span class="sxs-lookup"><span data-stu-id="d47c1-936">1</span></span>                            | <span data-ttu-id="d47c1-937">value1</span><span class="sxs-lookup"><span data-stu-id="d47c1-937">value1</span></span>                       |
| <span data-ttu-id="d47c1-938">2</span><span class="sxs-lookup"><span data-stu-id="d47c1-938">2</span></span>                            | <span data-ttu-id="d47c1-939">value2</span><span class="sxs-lookup"><span data-stu-id="d47c1-939">value2</span></span>                       |
| <span data-ttu-id="d47c1-940">3</span><span class="sxs-lookup"><span data-stu-id="d47c1-940">3</span></span>                            | <span data-ttu-id="d47c1-941">value3</span><span class="sxs-lookup"><span data-stu-id="d47c1-941">value3</span></span>                       |
| <span data-ttu-id="d47c1-942">4</span><span class="sxs-lookup"><span data-stu-id="d47c1-942">4</span></span>                            | <span data-ttu-id="d47c1-943">value4</span><span class="sxs-lookup"><span data-stu-id="d47c1-943">value4</span></span>                       |
| <span data-ttu-id="d47c1-944">5</span><span class="sxs-lookup"><span data-stu-id="d47c1-944">5</span></span>                            | <span data-ttu-id="d47c1-945">value5</span><span class="sxs-lookup"><span data-stu-id="d47c1-945">value5</span></span>                       |

<span data-ttu-id="d47c1-946">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="d47c1-946">**JSON array processing**</span></span>

<span data-ttu-id="d47c1-947">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="d47c1-947">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="d47c1-948">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="d47c1-948">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="d47c1-949">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="d47c1-949">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="d47c1-950">键</span><span class="sxs-lookup"><span data-stu-id="d47c1-950">Key</span></span>                     | <span data-ttu-id="d47c1-951">值</span><span class="sxs-lookup"><span data-stu-id="d47c1-951">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="d47c1-952">json_array:key</span><span class="sxs-lookup"><span data-stu-id="d47c1-952">json_array:key</span></span>          | <span data-ttu-id="d47c1-953">valueA</span><span class="sxs-lookup"><span data-stu-id="d47c1-953">valueA</span></span> |
| <span data-ttu-id="d47c1-954">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="d47c1-954">json_array:subsection:0</span></span> | <span data-ttu-id="d47c1-955">valueB</span><span class="sxs-lookup"><span data-stu-id="d47c1-955">valueB</span></span> |
| <span data-ttu-id="d47c1-956">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="d47c1-956">json_array:subsection:1</span></span> | <span data-ttu-id="d47c1-957">valueC</span><span class="sxs-lookup"><span data-stu-id="d47c1-957">valueC</span></span> |
| <span data-ttu-id="d47c1-958">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="d47c1-958">json_array:subsection:2</span></span> | <span data-ttu-id="d47c1-959">valueD</span><span class="sxs-lookup"><span data-stu-id="d47c1-959">valueD</span></span> |

<span data-ttu-id="d47c1-960">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="d47c1-960">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="d47c1-961">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-961">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="d47c1-962">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="d47c1-962">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="d47c1-963">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="d47c1-963">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="d47c1-964">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="d47c1-964">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="d47c1-965">0</span><span class="sxs-lookup"><span data-stu-id="d47c1-965">0</span></span>                                   | <span data-ttu-id="d47c1-966">valueB</span><span class="sxs-lookup"><span data-stu-id="d47c1-966">valueB</span></span>                              |
| <span data-ttu-id="d47c1-967">1</span><span class="sxs-lookup"><span data-stu-id="d47c1-967">1</span></span>                                   | <span data-ttu-id="d47c1-968">valueC</span><span class="sxs-lookup"><span data-stu-id="d47c1-968">valueC</span></span>                              |
| <span data-ttu-id="d47c1-969">2</span><span class="sxs-lookup"><span data-stu-id="d47c1-969">2</span></span>                                   | <span data-ttu-id="d47c1-970">valueD</span><span class="sxs-lookup"><span data-stu-id="d47c1-970">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="d47c1-971">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="d47c1-971">Custom configuration provider</span></span>

<span data-ttu-id="d47c1-972">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-972">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="d47c1-973">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="d47c1-973">The provider has the following characteristics:</span></span>

* <span data-ttu-id="d47c1-974">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="d47c1-974">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="d47c1-975">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d47c1-975">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="d47c1-976">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="d47c1-976">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="d47c1-977">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="d47c1-977">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="d47c1-978">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="d47c1-978">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="d47c1-979">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="d47c1-979">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="d47c1-980">*Models/EFConfigurationValue.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-980">*Models/EFConfigurationValue.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="d47c1-981">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-981">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="d47c1-982">*EFConfigurationProvider/EFConfigurationContext.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-982">*EFConfigurationProvider/EFConfigurationContext.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="d47c1-983">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="d47c1-983">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="d47c1-984">*EFConfigurationProvider/EFConfigurationSource.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-984">*EFConfigurationProvider/EFConfigurationSource.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="d47c1-985">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d47c1-985">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="d47c1-986">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="d47c1-986">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="d47c1-987">*EFConfigurationProvider/EFConfigurationProvider.cs* ：</span><span class="sxs-lookup"><span data-stu-id="d47c1-987">*EFConfigurationProvider/EFConfigurationProvider.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="d47c1-988">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="d47c1-988">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="d47c1-989">Extensions/EntityFrameworkExtensions.cs：</span><span class="sxs-lookup"><span data-stu-id="d47c1-989">*Extensions/EntityFrameworkExtensions.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="d47c1-990">下面的代码演示如何在 Program.cs 中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="d47c1-990">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs* :</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="d47c1-991">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-991">Access configuration during startup</span></span>

<span data-ttu-id="d47c1-992">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="d47c1-992">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d47c1-993">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="d47c1-993">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="d47c1-994">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="d47c1-994">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="d47c1-995">在 :::no-loc(Razor)::: Pages 页面或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-995">Access configuration in a :::no-loc(Razor)::: Pages page or MVC view</span></span>

<span data-ttu-id="d47c1-996">若要访问 :::no-loc(Razor)::: Pages 页面或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) 命名空间添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入该页面或视图。</span><span class="sxs-lookup"><span data-stu-id="d47c1-996">To access configuration settings in a :::no-loc(Razor)::: Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="d47c1-997">在 :::no-loc(Razor)::: Pages 页面中：</span><span class="sxs-lookup"><span data-stu-id="d47c1-997">In a :::no-loc(Razor)::: Pages page:</span></span>

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
    <h1>Access configuration in a :::no-loc(Razor)::: Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="d47c1-998">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="d47c1-998">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="d47c1-999">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="d47c1-999">Add configuration from an external assembly</span></span>

<span data-ttu-id="d47c1-1000">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="d47c1-1000">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d47c1-1001">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="d47c1-1001">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d47c1-1002">其他资源</span><span class="sxs-lookup"><span data-stu-id="d47c1-1002">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end