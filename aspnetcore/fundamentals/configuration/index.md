---
title: ASP.NET Core 中的配置
author: rick-anderson
description: 理解如何使用配置 API 配置 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d76ca78bc988f859b4e99752a0e88735e1df1d82
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501331"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8b09a-103">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8b09a-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8b09a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b09a-105">ASP.NET Core 中的配置是使用一个或多个[配置提供程序](#cp)执行的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="8b09a-106">配置提供程序使用各种配置源从键值对读取配置数据：</span><span class="sxs-lookup"><span data-stu-id="8b09a-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="8b09a-107">设置文件，例如 appsettings.json </span><span class="sxs-lookup"><span data-stu-id="8b09a-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="8b09a-108">环境变量</span><span class="sxs-lookup"><span data-stu-id="8b09a-108">Environment variables</span></span>
* <span data-ttu-id="8b09a-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b09a-109">Azure Key Vault</span></span>
* <span data-ttu-id="8b09a-110">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-110">Azure App Configuration</span></span>
* <span data-ttu-id="8b09a-111">命令行参数</span><span class="sxs-lookup"><span data-stu-id="8b09a-111">Command-line arguments</span></span>
* <span data-ttu-id="8b09a-112">已安装或已创建的自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="8b09a-113">目录文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-113">Directory files</span></span>
* <span data-ttu-id="8b09a-114">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="8b09a-114">In-memory .NET objects</span></span>

<span data-ttu-id="8b09a-115">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8b09a-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="8b09a-116">默认配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-116">Default configuration</span></span>

<span data-ttu-id="8b09a-117">通过 [dotnet new](/dotnet/core/tools/dotnet-new) 或 Visual Studio 创建的 ASP.NET Core Web 应用会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="8b09a-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="8b09a-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="8b09a-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource)：添加现有 `IConfiguration` 作为源。</span><span class="sxs-lookup"><span data-stu-id="8b09a-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="8b09a-120">在默认配置示例中，添加[主机](#hvac)配置，并将它设置为应用  配置的第一个源。</span><span class="sxs-lookup"><span data-stu-id="8b09a-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="8b09a-121">使用 [JSON 配置提供程序](#file-configuration-provider)通过 [appsettings.json](#appsettingsjson) 提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="8b09a-122">使用 [JSON 配置提供程序](#file-configuration-provider)通过 appsettings.`Environment`.json 提供   。</span><span class="sxs-lookup"><span data-stu-id="8b09a-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="8b09a-123">例如，appsettings.Production.json 和 appsettings.Development.json       。</span><span class="sxs-lookup"><span data-stu-id="8b09a-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="8b09a-124">应用在 `Development` 环境中运行时的[应用机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="8b09a-125">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8b09a-126">使用[命令行配置提供程序](#command-line)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="8b09a-127">后来添加的配置提供程序会替代之前的密钥设置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="8b09a-128">例如，如果在 appsettings.json 和环境中设置了 `MyKey`，则会使用环境值  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="8b09a-129">使用默认配置提供程序，[命令行配置提供程序](#command-line-configuration-provider)将替代所有其他的提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="8b09a-130">若要详细了解 `CreateDefaultBuilder`，请参阅[默认生成器设置](xref:fundamentals/host/generic-host#default-builder-settings)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="8b09a-131">以下代码按添加顺序显示了已启用的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b09a-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="8b09a-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="8b09a-132">appsettings.json</span></span>

<span data-ttu-id="8b09a-133">请考虑使用以下 appsettings.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8b09a-134">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-135">默认的 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 会按以下顺序加载配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="8b09a-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8b09a-136">*appsettings.json*</span></span>
1. <span data-ttu-id="8b09a-137">appsettings.`Environment`.json   ：例如，appsettings.Production.json 和 appsettings.Development.json       文件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="8b09a-138">文件的环境版本是根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8b09a-139">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8b09a-140">appsettings.`Environment`.json 值将替代 appsettings.json 中的密钥    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="8b09a-141">例如，默认情况下：</span><span class="sxs-lookup"><span data-stu-id="8b09a-141">For example, by default:</span></span>

* <span data-ttu-id="8b09a-142">在开发环境中，appsettings.Development.json 配置将覆盖在 appsettings.json 中找到的值     。</span><span class="sxs-lookup"><span data-stu-id="8b09a-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="8b09a-143">在生产环境中，appsettings.Production.json 配置将覆盖在 appsettings.json 中找到的值     。</span><span class="sxs-lookup"><span data-stu-id="8b09a-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="8b09a-144">例如，在将应用部署到 Azure 时。</span><span class="sxs-lookup"><span data-stu-id="8b09a-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="8b09a-145">使用选项模式绑定分层配置数据</span><span class="sxs-lookup"><span data-stu-id="8b09a-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="8b09a-146">读取相关配置值的首选方法是使用[选项模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8b09a-147">例如，若要读取以下配置值，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8b09a-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="8b09a-148">创建以下 `PositionOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="8b09a-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="8b09a-149">类型的所有公共读写属性均已绑定。</span><span class="sxs-lookup"><span data-stu-id="8b09a-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="8b09a-150">不会绑定字段。</span><span class="sxs-lookup"><span data-stu-id="8b09a-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="8b09a-151">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="8b09a-151">The following code:</span></span>

* <span data-ttu-id="8b09a-152">调用 [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) 将 `PositionOptions` 类绑定到 `Position` 部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="8b09a-153">显示 `Position` 配置数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定的类型。</span><span class="sxs-lookup"><span data-stu-id="8b09a-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8b09a-155">使用 `ConfigurationBinder.Get<T>` 可能比使用 `ConfigurationBinder.Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="8b09a-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="8b09a-156">下面的代码演示如何将 `ConfigurationBinder.Get<T>` 与 `PositionOptions` 类配合使用：</span><span class="sxs-lookup"><span data-stu-id="8b09a-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-157">使用选项模式时的替代方法是绑定 `Position` 部分并将它添加到[依赖项注入服务容器](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8b09a-158">在以下代码中，`PositionOptions` 已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 被添加到了服务容器并已绑定到了配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="8b09a-159">通过使用前面的代码，以下代码将读取位置选项：</span><span class="sxs-lookup"><span data-stu-id="8b09a-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-160">使用[默认](#default)配置，会通过 [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) 启用 appsettings.json 和 appsettings.`Environment`.json 文件    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="8b09a-161">应用启动后，对 appsettings.json 和 appsettings.`Environment`.json 文件做出的更改将由 [JSON 配置提供程序](#jcp)读取    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8b09a-162">有关添加其他 JSON 配置文件的信息，请参阅本文档中的 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="8b09a-163">安全和机密管理器</span><span class="sxs-lookup"><span data-stu-id="8b09a-163">Security and secret manager</span></span>

<span data-ttu-id="8b09a-164">配置数据指南：</span><span class="sxs-lookup"><span data-stu-id="8b09a-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="8b09a-165">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="8b09a-166">[机密管理器](xref:security/app-secrets)可用于存储开发环境中的机密。</span><span class="sxs-lookup"><span data-stu-id="8b09a-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="8b09a-167">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="8b09a-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8b09a-168">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="8b09a-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8b09a-169">[默认情况下](#default)，[机密管理器](xref:security/app-secrets)会在 appsettings.json 和 appsettings.`Environment`.json 之后读取配置设置    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="8b09a-170">有关存储密码或其他敏感数据的详细信息：</span><span class="sxs-lookup"><span data-stu-id="8b09a-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8b09a-171"><xref:security/app-secrets>：包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="8b09a-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8b09a-172">机密管理器使用[文件配置提供程序](#fcp)将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="8b09a-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="8b09a-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="8b09a-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8b09a-174">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="8b09a-175">环境变量</span><span class="sxs-lookup"><span data-stu-id="8b09a-175">Environment variables</span></span>

<span data-ttu-id="8b09a-176">使用[默认](#default)配置，<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 会在读取 appsettings.json、appsettings.`Environment`.json 和[机密管理器](xref:security/app-secrets)后从环境变量键值对加载配置    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8b09a-177">因此，从环境中读取的键值会替代从 appsettings.json、appsettings.`Environment`.json 和机密管理器中读取的值    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8b09a-178">以下 `set` 命令：</span><span class="sxs-lookup"><span data-stu-id="8b09a-178">The following `set` commands:</span></span>

* <span data-ttu-id="8b09a-179">在 Windows 上设置[上述示例](#appsettingsjson)的环境键和值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="8b09a-180">在使用[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)时测试设置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="8b09a-181">`dotnet run` 命令必须在项目目录中运行。</span><span class="sxs-lookup"><span data-stu-id="8b09a-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="8b09a-182">前面的环境设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-182">The preceding environment settings:</span></span>

* <span data-ttu-id="8b09a-183">仅在进程中设置，这些进程是从设置进程的命令窗口启动的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="8b09a-184">不会由通过 Visual Studio 启动的浏览器读取。</span><span class="sxs-lookup"><span data-stu-id="8b09a-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="8b09a-185">以下 [setx](/windows-server/administration/windows-commands/setx) 命令可用于在 Windows 上设置环境键和值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="8b09a-186">与 `set` 不同，`setx` 设置是持久的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="8b09a-187">`/M` 在系统环境中设置变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="8b09a-188">如果未使用 `/M` 开关，则会设置用户环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="8b09a-189">测试前面的命令是否会替代 appsettings.json 和 appsettings.`Environment`.json：   </span><span class="sxs-lookup"><span data-stu-id="8b09a-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="8b09a-190">使用 Visual Studio：退出并重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="8b09a-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="8b09a-191">使用 CLI：启动新的命令窗口并输入 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="8b09a-192">使用字符串调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 以指定环境变量的前缀：</span><span class="sxs-lookup"><span data-stu-id="8b09a-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="8b09a-193">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="8b09a-193">In the preceding code:</span></span>

* <span data-ttu-id="8b09a-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` 被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="8b09a-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8b09a-195">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="8b09a-196">使用 `MyCustomPrefix_` 前缀设置的环境变量将替代[默认配置提供程序](#default)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="8b09a-197">这包括没有前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="8b09a-198">前缀会在读取配置键值对时被去除。</span><span class="sxs-lookup"><span data-stu-id="8b09a-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="8b09a-199">以下命令对自定义前缀进行测试：</span><span class="sxs-lookup"><span data-stu-id="8b09a-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="8b09a-200">[默认配置](#default)会加载前缀为 `DOTNET_` 和 `ASPNETCORE_` 的环境变量和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b09a-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="8b09a-201">`DOTNET_` 和 `ASPNETCORE_` 前缀会由 ASP.NET Core 用于[主机和应用配置](xref:fundamentals/host/generic-host#host-configuration)，但不用于用户配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="8b09a-202">有关主机和应用配置的详细信息，请参阅 [.NET 通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="8b09a-203">在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)上，选择“设置”>“配置”页面上的“新应用程序设置”   。</span><span class="sxs-lookup"><span data-stu-id="8b09a-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="8b09a-204">Azure 应用服务应用程序设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="8b09a-205">已静态加密且通过加密的通道进行传输。</span><span class="sxs-lookup"><span data-stu-id="8b09a-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="8b09a-206">已作为环境变量公开。</span><span class="sxs-lookup"><span data-stu-id="8b09a-206">Exposed as environment variables.</span></span>

<span data-ttu-id="8b09a-207">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8b09a-208">有关 Azure 数据库连接字符串的信息，请参阅[连接字符串前缀](#constr)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="8b09a-209">命令行</span><span class="sxs-lookup"><span data-stu-id="8b09a-209">Command-line</span></span>

<span data-ttu-id="8b09a-210">使用[默认](#default)配置，<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 会从以下配置源后的命令行参数键值对中加载配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="8b09a-211">appsettings.json 和 appsettings.`Environment`.json 文件    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8b09a-212">开发环境中的[应用机密（机密管理器）](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8b09a-213">环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-213">Environment variables.</span></span>

<span data-ttu-id="8b09a-214">[默认情况下](#default)，在命令行上设置的配置值会替代通过所有其他配置提供程序设置的配置值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="8b09a-215">命令行参数</span><span class="sxs-lookup"><span data-stu-id="8b09a-215">Command-line arguments</span></span>

<span data-ttu-id="8b09a-216">以下命令使用 `=` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="8b09a-217">以下命令使用 `/` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="8b09a-218">以下命令使用 `--` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="8b09a-219">键值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-219">The key value:</span></span>

* <span data-ttu-id="8b09a-220">必须后跟 `=`，或者当值后跟一个空格时，键必须具有一个 `--` 或 `/` 的前缀。</span><span class="sxs-lookup"><span data-stu-id="8b09a-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="8b09a-221">如果使用 `=`，则不是必需的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="8b09a-222">例如 `MySetting=`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="8b09a-223">在同一命令中，请勿将使用 `=` 的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="8b09a-224">交换映射</span><span class="sxs-lookup"><span data-stu-id="8b09a-224">Switch mappings</span></span>

<span data-ttu-id="8b09a-225">交换映射支持键名替换逻辑  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="8b09a-226">提供针对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法的交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="8b09a-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8b09a-227">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8b09a-228">如果在字典中找到了命令行键，则会传回字典值将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8b09a-229">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8b09a-230">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="8b09a-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8b09a-231">交换必须以 `-` 或 `--` 开头。</span><span class="sxs-lookup"><span data-stu-id="8b09a-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="8b09a-232">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8b09a-233">若要使用交换映射字典，请将它传递到对 `AddCommandLine` 的调用中：</span><span class="sxs-lookup"><span data-stu-id="8b09a-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="8b09a-234">下面的代码显示了替换后的键的键值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-235">运行以下命令以测试键替换：</span><span class="sxs-lookup"><span data-stu-id="8b09a-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8b09a-236">注意：目前，`=` 不能用于设置带有单划线 `-` 的键替换值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="8b09a-237">请参阅[此 GitHub 问题](https://github.com/dotnet/extensions/issues/3059)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="8b09a-238">以下命令可用于测试键替换：</span><span class="sxs-lookup"><span data-stu-id="8b09a-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="8b09a-239">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="8b09a-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8b09a-240">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b09a-241">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="8b09a-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8b09a-242">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="8b09a-242">Hierarchical configuration data</span></span>

<span data-ttu-id="8b09a-243">配置 API 在配置键中使用分隔符来展平分层数据，以此来读取分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8b09a-244">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 appsettings.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="8b09a-245">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-246">读取分层配置数据的首选方法是使用选项模式。</span><span class="sxs-lookup"><span data-stu-id="8b09a-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="8b09a-247">有关详细信息，请参阅本文档中的[绑定分层配置数据](#optpat)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="8b09a-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="8b09a-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8b09a-249">稍后将在 [GetSection、GetChildren 和 Exists](#getsection) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="8b09a-250">配置键和值</span><span class="sxs-lookup"><span data-stu-id="8b09a-250">Configuration keys and values</span></span>

<span data-ttu-id="8b09a-251">配置键：</span><span class="sxs-lookup"><span data-stu-id="8b09a-251">Configuration keys:</span></span>

* <span data-ttu-id="8b09a-252">不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8b09a-252">Are case-insensitive.</span></span> <span data-ttu-id="8b09a-253">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8b09a-254">如果在多个配置提供程序中设置了某一键和值，则会使用最后添加的提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="8b09a-255">有关详细信息，请参阅[默认配置](#default)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="8b09a-256">分层键</span><span class="sxs-lookup"><span data-stu-id="8b09a-256">Hierarchical keys</span></span>
  * <span data-ttu-id="8b09a-257">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="8b09a-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8b09a-258">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="8b09a-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8b09a-259">所有平台均支持采用双下划线 `__`，并且它会自动转换为冒号 `:`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="8b09a-260">在 Azure Key Vault 中，分层键使用 `--` 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="8b09a-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="8b09a-261">在机密加载到应用的配置中时，写入代码以用 `:` 替换 `--`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-261">Write code to replace the `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8b09a-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="8b09a-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8b09a-263">数组绑定将在[将数组绑定到类](#boa)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="8b09a-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="8b09a-264">配置值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-264">Configuration values:</span></span>

* <span data-ttu-id="8b09a-265">为字符串。</span><span class="sxs-lookup"><span data-stu-id="8b09a-265">Are strings.</span></span>
* <span data-ttu-id="8b09a-266">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="8b09a-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="8b09a-267">配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-267">Configuration providers</span></span>

<span data-ttu-id="8b09a-268">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8b09a-269">提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-269">Provider</span></span> | <span data-ttu-id="8b09a-270">通过以下对象提供配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="8b09a-271">Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="8b09a-272">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b09a-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="8b09a-273">Azure 应用配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="8b09a-274">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="8b09a-275">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="8b09a-276">命令行参数</span><span class="sxs-lookup"><span data-stu-id="8b09a-276">Command-line parameters</span></span> |
| [<span data-ttu-id="8b09a-277">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8b09a-278">自定义源</span><span class="sxs-lookup"><span data-stu-id="8b09a-278">Custom source</span></span> |
| [<span data-ttu-id="8b09a-279">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="8b09a-280">环境变量</span><span class="sxs-lookup"><span data-stu-id="8b09a-280">Environment variables</span></span> |
| [<span data-ttu-id="8b09a-281">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8b09a-282">INI、JSON 和 XML 文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="8b09a-283">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8b09a-284">目录文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-284">Directory files</span></span> |
| [<span data-ttu-id="8b09a-285">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8b09a-286">内存中集合</span><span class="sxs-lookup"><span data-stu-id="8b09a-286">In-memory collections</span></span> |
| [<span data-ttu-id="8b09a-287">机密管理器</span><span class="sxs-lookup"><span data-stu-id="8b09a-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="8b09a-288">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-288">File in the user profile directory</span></span> |

<span data-ttu-id="8b09a-289">按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="8b09a-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="8b09a-290">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="8b09a-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8b09a-291">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="8b09a-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8b09a-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="8b09a-292">*appsettings.json*</span></span>
1. <span data-ttu-id="8b09a-293">appsettings.`Environment`.json  </span><span class="sxs-lookup"><span data-stu-id="8b09a-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="8b09a-294">机密管理器</span><span class="sxs-lookup"><span data-stu-id="8b09a-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="8b09a-295">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="8b09a-296">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="8b09a-297">通常的做法是将命令行配置提供程序添加到一系列提供程序的末尾，使命令行参数能够替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8b09a-298">[默认配置](#default)中使用了上述提供程序顺序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="8b09a-299">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="8b09a-299">Connection string prefixes</span></span>

<span data-ttu-id="8b09a-300">对于四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="8b09a-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="8b09a-301">这些连接字符串涉及了为应用环境配置 Azure 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8b09a-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8b09a-302">使用[默认配置](#default)或没有向 `AddEnvironmentVariables` 应用前缀时，具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="8b09a-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8b09a-303">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="8b09a-303">Connection string prefix</span></span> | <span data-ttu-id="8b09a-304">提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8b09a-305">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8b09a-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="8b09a-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8b09a-307">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="8b09a-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8b09a-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8b09a-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8b09a-309">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="8b09a-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8b09a-310">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8b09a-311">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="8b09a-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8b09a-312">环境变量键</span><span class="sxs-lookup"><span data-stu-id="8b09a-312">Environment variable key</span></span> | <span data-ttu-id="8b09a-313">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="8b09a-313">Converted configuration key</span></span> | <span data-ttu-id="8b09a-314">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="8b09a-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-315">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="8b09a-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-316">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8b09a-317">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b09a-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-318">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8b09a-319">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b09a-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-320">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8b09a-321">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b09a-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="8b09a-322">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-322">JSON configuration provider</span></span>

<span data-ttu-id="8b09a-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="8b09a-324">重载可以指定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-324">Overloads can specify:</span></span>

* <span data-ttu-id="8b09a-325">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="8b09a-325">Whether the file is optional.</span></span>
* <span data-ttu-id="8b09a-326">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="8b09a-327">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="8b09a-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="8b09a-328">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8b09a-328">The preceding code:</span></span>

* <span data-ttu-id="8b09a-329">通过以下选项将 JSON 配置提供程序配置为加载 MyConfig.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="8b09a-330">`optional: true`：文件是可选的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="8b09a-331">`reloadOnChange: true`：保存更改后会重载文件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="8b09a-332">读取 MyConfig.json 文件之前的[默认配置提供程序](#default)  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="8b09a-333">MyConfig.json 文件中的设置会替代默认配置提供程序中的设置，包括[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8b09a-334">通常情况下，你不会希望自定义 JSON 文件替代在[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中设置的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8b09a-335">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b09a-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="8b09a-336">在前面的代码中，MyConfig.json 和 MyConfig.`Environment`.json 文件中的设置    ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="8b09a-337">会替代 appsettings.json 和 appsettings.`Environment`.json 文件中的设置    。</span><span class="sxs-lookup"><span data-stu-id="8b09a-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="8b09a-338">会被[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中的设置所替代。</span><span class="sxs-lookup"><span data-stu-id="8b09a-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8b09a-339">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyConfig.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="8b09a-340">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="8b09a-341">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-341">File configuration provider</span></span>

<span data-ttu-id="8b09a-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="8b09a-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8b09a-343">以下配置提供程序派生自 `FileConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="8b09a-344">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8b09a-345">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="8b09a-346">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8b09a-347">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-347">INI configuration provider</span></span>

<span data-ttu-id="8b09a-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8b09a-349">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b09a-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="8b09a-350">在前面的代码中，MyIniConfig.ini 和 MyIniConfig.`Environment`.ini 文件中的设置会被以下提供程序中的设置替代    ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8b09a-351">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-351">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8b09a-352">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8b09a-353">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyIniConfig.ini 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="8b09a-354">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="8b09a-355">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-355">XML configuration provider</span></span>

<span data-ttu-id="8b09a-356"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8b09a-357">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b09a-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="8b09a-358">在前面的代码中，MyXMLFile.xml 和 MyXMLFile.`Environment`.xml 文件中的设置会被以下提供程序中的设置替代    ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="8b09a-359">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-359">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="8b09a-360">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="8b09a-361">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyXMLFile.xml 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="8b09a-362">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-363">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="8b09a-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="8b09a-364">以下代码会读取前面的配置文件并显示键和值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-365">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8b09a-366">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="8b09a-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b09a-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8b09a-367">key:attribute</span></span>
* <span data-ttu-id="8b09a-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8b09a-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8b09a-369">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="8b09a-370"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8b09a-371">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="8b09a-371">The key is the file name.</span></span> <span data-ttu-id="8b09a-372">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="8b09a-372">The value contains the file's contents.</span></span> <span data-ttu-id="8b09a-373">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="8b09a-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8b09a-374">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8b09a-375">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8b09a-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8b09a-376">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b09a-377">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="8b09a-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8b09a-378">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="8b09a-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8b09a-379">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="8b09a-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8b09a-380">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8b09a-381">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="8b09a-382">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-382">Memory configuration provider</span></span>

<span data-ttu-id="8b09a-383"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8b09a-384">以下代码将内存集合添加到配置系统中：</span><span class="sxs-lookup"><span data-stu-id="8b09a-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="8b09a-385">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述配置设置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-386">在前面的代码中，`config.AddInMemoryCollection(Dict)` 会被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="8b09a-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="8b09a-387">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8b09a-388">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="8b09a-389">有关使用 `MemoryConfigurationProvider` 的其他示例，请参阅[绑定数组](#boa)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="8b09a-390">GetValue</span><span class="sxs-lookup"><span data-stu-id="8b09a-390">GetValue</span></span>

<span data-ttu-id="8b09a-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的类型：</span><span class="sxs-lookup"><span data-stu-id="8b09a-391">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-392">在前面的代码中，如果在配置中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-392">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8b09a-393">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="8b09a-393">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8b09a-394">对于下面的示例，请考虑以下 MySubsection.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-394">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="8b09a-395">以下代码将 MySubsection.json 添加到配置提供程序  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-395">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="8b09a-396">GetSection</span><span class="sxs-lookup"><span data-stu-id="8b09a-396">GetSection</span></span>

<span data-ttu-id="8b09a-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 会返回具有指定子节键的配置子节。</span><span class="sxs-lookup"><span data-stu-id="8b09a-397">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8b09a-398">以下代码将返回 `section1` 的值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-398">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-399">以下代码将返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-399">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-400">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-400">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8b09a-401">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-401">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8b09a-402">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="8b09a-402">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8b09a-403">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-403">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="8b09a-404">GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="8b09a-404">GetChildren and Exists</span></span>

<span data-ttu-id="8b09a-405">以下代码将调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 并返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-405">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-406">前面的代码将调用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 以验证该节是否存在：</span><span class="sxs-lookup"><span data-stu-id="8b09a-406">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="8b09a-407">绑定数组</span><span class="sxs-lookup"><span data-stu-id="8b09a-407">Bind an array</span></span>

<span data-ttu-id="8b09a-408">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="8b09a-408">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8b09a-409">公开数值键段的任何数组格式都能够与 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="8b09a-409">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="8b09a-410">请考虑[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的 MyArray.json  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-410">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="8b09a-411">以下代码将 MyArray.json 添加到配置提供程序  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-411">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="8b09a-412">以下代码将读取配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-412">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-413">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="8b09a-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="8b09a-414">在前面的输出中，索引 3 具有值 `value40`，与 MyArray.json 中的 `"4": "value40",` 相对应  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-414">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="8b09a-415">绑定的数组索引是连续的，并且未绑定到配置键索引。</span><span class="sxs-lookup"><span data-stu-id="8b09a-415">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="8b09a-416">配置绑定器不能绑定 NULL 值，也不能在绑定的对象中创建 NULL 条目</span><span class="sxs-lookup"><span data-stu-id="8b09a-416">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="8b09a-417">以下代码将通过 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法加载 `array:entries` 配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-417">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="8b09a-418">以下代码将读取 `arrayDict` `Dictionary` 中的配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-418">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-419">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="8b09a-419">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="8b09a-420">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-420">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8b09a-421">当绑定包含数组的配置数据时，配置键中的数组索引用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-421">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8b09a-422">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="8b09a-422">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8b09a-423">可以在由任何读取索引 &num;3 键/值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="8b09a-423">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="8b09a-424">请考虑示例下载中的以下 Value3.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-424">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="8b09a-425">以下代码包含 Value3.json 和 `arrayDict` `Dictionary` 的配置  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-425">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="8b09a-426">以下代码将读取上述配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-426">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="8b09a-427">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="8b09a-427">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="8b09a-428">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="8b09a-428">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="8b09a-429">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-429">Custom configuration provider</span></span>

<span data-ttu-id="8b09a-430">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-430">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8b09a-431">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="8b09a-431">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8b09a-432">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-432">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8b09a-433">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8b09a-433">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8b09a-434">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-434">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8b09a-435">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="8b09a-435">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8b09a-436">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="8b09a-436">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8b09a-437">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="8b09a-437">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8b09a-438">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-438">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8b09a-439">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-439">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8b09a-440">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-440">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8b09a-441">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="8b09a-441">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8b09a-442">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-442">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8b09a-443">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-443">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8b09a-444">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="8b09a-444">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8b09a-445">由于[配置密钥不区分大小写](#keys)，因此用来初始化数据库的字典是用不区分大小写的比较程序 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 创建的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-445">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8b09a-446">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-446">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8b09a-447">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-447">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8b09a-448">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-448">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8b09a-449">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-449">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="8b09a-450">访问 Startup 中的配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-450">Access configuration in Startup</span></span>

<span data-ttu-id="8b09a-451">以下代码显示 `Startup` 方法中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="8b09a-451">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="8b09a-452">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-452">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="8b09a-453">访问 Razor Pages 中的配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-453">Access configuration in Razor Pages</span></span>

<span data-ttu-id="8b09a-454">以下代码显示 Razor 页面中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="8b09a-454">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="8b09a-455">访问 MVC 视图文件中的配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-455">Access configuration in a MVC view file</span></span>

<span data-ttu-id="8b09a-456">以下代码显示 MVC 视图中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="8b09a-456">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8b09a-457">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-457">Host versus app configuration</span></span>

<span data-ttu-id="8b09a-458">在配置并启动应用之前，配置并启动主机  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-458">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8b09a-459">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="8b09a-459">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8b09a-460">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-460">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8b09a-461">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-461">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8b09a-462">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-462">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="8b09a-463">默认主机配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-463">Default host configuration</span></span>

<span data-ttu-id="8b09a-464">有关使用 [Web 主机](xref:fundamentals/host/web-host)时默认配置的详细信息，请参阅[本主题的 ASP.NET Core 2.2 版本](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-464">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8b09a-465">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="8b09a-465">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8b09a-466">使用[环境变量配置提供程序](#environment-variables-configuration-provider)通过前缀为 `DOTNET_`的环境变量（例如，`DOTNET_ENVIRONMENT`）提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-466">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8b09a-467">在配置键值对加载后，前缀 (`DOTNET_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="8b09a-467">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8b09a-468">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-468">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8b09a-469">已建立 Web 主机默认配置 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="8b09a-469">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8b09a-470">Kestrel 用作 Web 服务器，并使用应用的配置提供程序对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-470">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8b09a-471">添加主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-471">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8b09a-472">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则添加转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-472">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8b09a-473">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="8b09a-473">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8b09a-474">其他配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-474">Other configuration</span></span>

<span data-ttu-id="8b09a-475">本主题仅与应用配置相关  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-475">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8b09a-476">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-476">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8b09a-477">launch.json  /launchSettings.json  是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="8b09a-477">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8b09a-478"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="8b09a-478">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8b09a-479">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-479">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8b09a-480">web.config  是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="8b09a-480">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8b09a-481">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8b09a-482">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="8b09a-483">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="8b09a-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8b09a-484">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b09a-485">其他资源</span><span class="sxs-lookup"><span data-stu-id="8b09a-485">Additional resources</span></span>

* [<span data-ttu-id="8b09a-486">配置源代码</span><span class="sxs-lookup"><span data-stu-id="8b09a-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b09a-487">ASP.NET Core 中的应用配置基于配置提供程序  建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8b09a-488">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="8b09a-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8b09a-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b09a-489">Azure Key Vault</span></span>
* <span data-ttu-id="8b09a-490">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-490">Azure App Configuration</span></span>
* <span data-ttu-id="8b09a-491">命令行参数</span><span class="sxs-lookup"><span data-stu-id="8b09a-491">Command-line arguments</span></span>
* <span data-ttu-id="8b09a-492">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8b09a-493">目录文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-493">Directory files</span></span>
* <span data-ttu-id="8b09a-494">环境变量</span><span class="sxs-lookup"><span data-stu-id="8b09a-494">Environment variables</span></span>
* <span data-ttu-id="8b09a-495">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="8b09a-495">In-memory .NET objects</span></span>
* <span data-ttu-id="8b09a-496">设置文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-496">Settings files</span></span>

<span data-ttu-id="8b09a-497">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="8b09a-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8b09a-498">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="8b09a-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8b09a-499">选项模式  是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="8b09a-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8b09a-500">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="8b09a-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8b09a-501">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8b09a-502">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8b09a-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8b09a-503">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-503">Host versus app configuration</span></span>

<span data-ttu-id="8b09a-504">在配置并启动应用之前，配置并启动主机  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8b09a-505">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="8b09a-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8b09a-506">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8b09a-507">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8b09a-508">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="8b09a-509">其他配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-509">Other configuration</span></span>

<span data-ttu-id="8b09a-510">本主题仅与应用配置相关  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="8b09a-511">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="8b09a-512">launch.json  /launchSettings.json  是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="8b09a-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="8b09a-513"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="8b09a-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="8b09a-514">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="8b09a-515">web.config  是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="8b09a-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="8b09a-516">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8b09a-517">默认配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-517">Default configuration</span></span>

<span data-ttu-id="8b09a-518">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8b09a-519">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8b09a-520">以下内容适用于使用 [Web 主机](xref:fundamentals/host/web-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8b09a-521">有关使用[通用主机](xref:fundamentals/host/generic-host)时默认配置的详细信息，请参阅[本主题的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8b09a-522">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="8b09a-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8b09a-523">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8b09a-524">在配置键值对加载后，前缀 (`ASPNETCORE_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="8b09a-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8b09a-525">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8b09a-526">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="8b09a-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="8b09a-527">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json  提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8b09a-528">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json  提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8b09a-529">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8b09a-530">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8b09a-531">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="8b09a-532">安全性</span><span class="sxs-lookup"><span data-stu-id="8b09a-532">Security</span></span>

<span data-ttu-id="8b09a-533">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="8b09a-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8b09a-534">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8b09a-535">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="8b09a-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8b09a-536">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="8b09a-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8b09a-537">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="8b09a-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8b09a-538"><xref:security/app-secrets> &ndash; 包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="8b09a-538"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8b09a-539">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="8b09a-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8b09a-540">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8b09a-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="8b09a-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8b09a-542">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8b09a-543">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="8b09a-543">Hierarchical configuration data</span></span>

<span data-ttu-id="8b09a-544">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8b09a-545">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="8b09a-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8b09a-546">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="8b09a-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8b09a-547">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="8b09a-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8b09a-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-548">section0:key0</span></span>
* <span data-ttu-id="8b09a-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-549">section0:key1</span></span>
* <span data-ttu-id="8b09a-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-550">section1:key0</span></span>
* <span data-ttu-id="8b09a-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-551">section1:key1</span></span>

<span data-ttu-id="8b09a-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="8b09a-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8b09a-553">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8b09a-554">约定</span><span class="sxs-lookup"><span data-stu-id="8b09a-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8b09a-555">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-555">Sources and providers</span></span>

<span data-ttu-id="8b09a-556">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="8b09a-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8b09a-557">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8b09a-558">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="8b09a-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8b09a-559">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8b09a-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 来获取以下类的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8b09a-561">在下面的示例中，使用 `_config` 字段来访问配置值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="8b09a-562">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8b09a-563">键</span><span class="sxs-lookup"><span data-stu-id="8b09a-563">Keys</span></span>

<span data-ttu-id="8b09a-564">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8b09a-565">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8b09a-565">Keys are case-insensitive.</span></span> <span data-ttu-id="8b09a-566">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8b09a-567">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="8b09a-568">分层键</span><span class="sxs-lookup"><span data-stu-id="8b09a-568">Hierarchical keys</span></span>
  * <span data-ttu-id="8b09a-569">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="8b09a-569">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8b09a-570">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="8b09a-570">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8b09a-571">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="8b09a-571">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8b09a-572">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="8b09a-572">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8b09a-573">将机密加载到应用的配置中时，用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="8b09a-573">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8b09a-574"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="8b09a-574">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8b09a-575">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="8b09a-575">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8b09a-576">值</span><span class="sxs-lookup"><span data-stu-id="8b09a-576">Values</span></span>

<span data-ttu-id="8b09a-577">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-577">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8b09a-578">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="8b09a-578">Values are strings.</span></span>
* <span data-ttu-id="8b09a-579">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="8b09a-579">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8b09a-580">提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-580">Providers</span></span>

<span data-ttu-id="8b09a-581">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-581">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8b09a-582">提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-582">Provider</span></span> | <span data-ttu-id="8b09a-583">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="8b09a-583">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8b09a-584">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="8b09a-584">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8b09a-585">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b09a-585">Azure Key Vault</span></span> |
| <span data-ttu-id="8b09a-586">[Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="8b09a-586">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8b09a-587">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-587">Azure App Configuration</span></span> |
| [<span data-ttu-id="8b09a-588">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-588">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8b09a-589">命令行参数</span><span class="sxs-lookup"><span data-stu-id="8b09a-589">Command-line parameters</span></span> |
| [<span data-ttu-id="8b09a-590">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-590">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8b09a-591">自定义源</span><span class="sxs-lookup"><span data-stu-id="8b09a-591">Custom source</span></span> |
| [<span data-ttu-id="8b09a-592">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-592">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8b09a-593">环境变量</span><span class="sxs-lookup"><span data-stu-id="8b09a-593">Environment variables</span></span> |
| [<span data-ttu-id="8b09a-594">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-594">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8b09a-595">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="8b09a-595">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8b09a-596">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-596">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8b09a-597">目录文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-597">Directory files</span></span> |
| [<span data-ttu-id="8b09a-598">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-598">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8b09a-599">内存中集合</span><span class="sxs-lookup"><span data-stu-id="8b09a-599">In-memory collections</span></span> |
| <span data-ttu-id="8b09a-600">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="8b09a-600">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8b09a-601">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="8b09a-601">File in the user profile directory</span></span> |

<span data-ttu-id="8b09a-602">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="8b09a-602">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8b09a-603">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="8b09a-603">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8b09a-604">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="8b09a-604">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8b09a-605">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="8b09a-605">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8b09a-606">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境）  </span><span class="sxs-lookup"><span data-stu-id="8b09a-606">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8b09a-607">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="8b09a-607">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8b09a-608">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="8b09a-608">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8b09a-609">环境变量</span><span class="sxs-lookup"><span data-stu-id="8b09a-609">Environment variables</span></span>
1. <span data-ttu-id="8b09a-610">命令行参数</span><span class="sxs-lookup"><span data-stu-id="8b09a-610">Command-line arguments</span></span>

<span data-ttu-id="8b09a-611">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-611">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8b09a-612">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="8b09a-612">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b09a-613">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-613">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8b09a-614">用 UseConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="8b09a-614">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8b09a-615">若要配置主机生成器，请使用配置在主机生成器上调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-615">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8b09a-616">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8b09a-616">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8b09a-617">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b09a-617">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8b09a-618">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-618">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8b09a-619">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-619">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8b09a-620">删除 CreateDefaultBuilder 添加的提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-620">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8b09a-621">要删除 `CreateDefaultBuilder` 添加的提供程序，请先对 [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) 调用 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)：</span><span class="sxs-lookup"><span data-stu-id="8b09a-621">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8b09a-622">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-622">Consume configuration during app startup</span></span>

<span data-ttu-id="8b09a-623">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-623">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b09a-624">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-624">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8b09a-625">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-625">Command-line Configuration Provider</span></span>

<span data-ttu-id="8b09a-626"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-626">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8b09a-627">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-627">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b09a-628">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-628">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8b09a-629">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-629">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8b09a-630">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="8b09a-630">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8b09a-631">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="8b09a-631">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8b09a-632">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="8b09a-632">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8b09a-633">环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-633">Environment variables.</span></span>

<span data-ttu-id="8b09a-634">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-634">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8b09a-635">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-635">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8b09a-636">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-636">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8b09a-637">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="8b09a-637">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8b09a-638">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-638">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b09a-639">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-639">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8b09a-640">**示例**</span><span class="sxs-lookup"><span data-stu-id="8b09a-640">**Example**</span></span>

<span data-ttu-id="8b09a-641">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-641">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8b09a-642">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="8b09a-642">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8b09a-643">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-643">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8b09a-644">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="8b09a-644">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8b09a-645">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-645">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8b09a-646">自变量</span><span class="sxs-lookup"><span data-stu-id="8b09a-646">Arguments</span></span>

<span data-ttu-id="8b09a-647">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="8b09a-647">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8b09a-648">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-648">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8b09a-649">键前缀</span><span class="sxs-lookup"><span data-stu-id="8b09a-649">Key prefix</span></span>               | <span data-ttu-id="8b09a-650">示例</span><span class="sxs-lookup"><span data-stu-id="8b09a-650">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8b09a-651">无前缀</span><span class="sxs-lookup"><span data-stu-id="8b09a-651">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8b09a-652">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="8b09a-652">Two dashes (`--`)</span></span>        | <span data-ttu-id="8b09a-653">`--CommandLineKey2=value2`，`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8b09a-653">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8b09a-654">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="8b09a-654">Forward slash (`/`)</span></span>      | <span data-ttu-id="8b09a-655">`/CommandLineKey3=value3`，`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8b09a-655">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8b09a-656">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-656">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8b09a-657">示例命令：</span><span class="sxs-lookup"><span data-stu-id="8b09a-657">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8b09a-658">交换映射</span><span class="sxs-lookup"><span data-stu-id="8b09a-658">Switch mappings</span></span>

<span data-ttu-id="8b09a-659">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b09a-659">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8b09a-660">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，需要为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="8b09a-660">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8b09a-661">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-661">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8b09a-662">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-662">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8b09a-663">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-663">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8b09a-664">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="8b09a-664">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8b09a-665">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="8b09a-665">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8b09a-666">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-666">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8b09a-667">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="8b09a-667">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8b09a-668">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="8b09a-668">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8b09a-669">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-669">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8b09a-670">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="8b09a-670">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8b09a-671">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-671">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b09a-672">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="8b09a-672">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8b09a-673">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-673">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8b09a-674">键</span><span class="sxs-lookup"><span data-stu-id="8b09a-674">Key</span></span>       | <span data-ttu-id="8b09a-675">“值”</span><span class="sxs-lookup"><span data-stu-id="8b09a-675">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8b09a-676">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-676">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8b09a-677">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-677">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8b09a-678">键</span><span class="sxs-lookup"><span data-stu-id="8b09a-678">Key</span></span>               | <span data-ttu-id="8b09a-679">“值”</span><span class="sxs-lookup"><span data-stu-id="8b09a-679">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8b09a-680">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-680">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8b09a-681"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-681">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8b09a-682">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-682">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8b09a-683">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-683">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8b09a-684">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-684">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="8b09a-685">如果使用 [Web 主机](xref:fundamentals/host/web-host)初始化新的主机生成器，且调用 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-685">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8b09a-686">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-686">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8b09a-687">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="8b09a-687">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8b09a-688">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-688">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8b09a-689">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="8b09a-689">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8b09a-690">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="8b09a-690">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8b09a-691">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b09a-691">Command-line arguments.</span></span>

<span data-ttu-id="8b09a-692">环境变量配置提供程序是在配置已根据用户机密和 appsettings  文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-692">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8b09a-693">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-693">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8b09a-694">要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-694">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8b09a-695">最后调用 `AddEnvironmentVariables`让带给定前缀的环境变量可替代其他提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-695">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8b09a-696">**示例**</span><span class="sxs-lookup"><span data-stu-id="8b09a-696">**Example**</span></span>

<span data-ttu-id="8b09a-697">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-697">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8b09a-698">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-698">Run the sample app.</span></span> <span data-ttu-id="8b09a-699">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="8b09a-699">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8b09a-700">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-700">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8b09a-701">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-701">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8b09a-702">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-702">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8b09a-703">请参阅示例应用的“Pages/Index.cshtml.cs”文件  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-703">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8b09a-704">要公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-704">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8b09a-705">前缀</span><span class="sxs-lookup"><span data-stu-id="8b09a-705">Prefixes</span></span>

<span data-ttu-id="8b09a-706">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-706">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8b09a-707">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b09a-707">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8b09a-708">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="8b09a-708">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8b09a-709">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="8b09a-709">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8b09a-710">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-710">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8b09a-711">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="8b09a-711">**Connection string prefixes**</span></span>

<span data-ttu-id="8b09a-712">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="8b09a-712">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8b09a-713">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="8b09a-713">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8b09a-714">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="8b09a-714">Connection string prefix</span></span> | <span data-ttu-id="8b09a-715">提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-715">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8b09a-716">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-716">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8b09a-717">MySQL</span><span class="sxs-lookup"><span data-stu-id="8b09a-717">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8b09a-718">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="8b09a-718">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8b09a-719">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8b09a-719">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8b09a-720">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="8b09a-720">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8b09a-721">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-721">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8b09a-722">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="8b09a-722">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8b09a-723">环境变量键</span><span class="sxs-lookup"><span data-stu-id="8b09a-723">Environment variable key</span></span> | <span data-ttu-id="8b09a-724">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="8b09a-724">Converted configuration key</span></span> | <span data-ttu-id="8b09a-725">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="8b09a-725">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-726">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="8b09a-726">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-727">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8b09a-728">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b09a-728">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-729">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8b09a-730">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b09a-730">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="8b09a-731">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-731">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="8b09a-732">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b09a-732">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="8b09a-733">**示例**</span><span class="sxs-lookup"><span data-stu-id="8b09a-733">**Example**</span></span>

<span data-ttu-id="8b09a-734">在服务器上创建了一个自定义连接字符串环境变量：</span><span class="sxs-lookup"><span data-stu-id="8b09a-734">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="8b09a-735">名称 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="8b09a-735">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="8b09a-736">值 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="8b09a-736">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="8b09a-737">如果 `IConfiguration` 已引入并分配给名为 `_config` 的字段，请读取值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-737">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="8b09a-738">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-738">File Configuration Provider</span></span>

<span data-ttu-id="8b09a-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="8b09a-739"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8b09a-740">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="8b09a-740">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8b09a-741">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-741">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8b09a-742">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-742">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8b09a-743">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-743">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8b09a-744">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-744">INI Configuration Provider</span></span>

<span data-ttu-id="8b09a-745"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-745">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8b09a-746">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-746">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b09a-747">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="8b09a-747">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8b09a-748">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-748">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b09a-749">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="8b09a-749">Whether the file is optional.</span></span>
* <span data-ttu-id="8b09a-750">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-750">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8b09a-751"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-751">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8b09a-752">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-752">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8b09a-753">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="8b09a-753">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8b09a-754">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="8b09a-754">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b09a-755">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-755">section0:key0</span></span>
* <span data-ttu-id="8b09a-756">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-756">section0:key1</span></span>
* <span data-ttu-id="8b09a-757">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8b09a-757">section1:subsection:key</span></span>
* <span data-ttu-id="8b09a-758">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8b09a-758">section2:subsection0:key</span></span>
* <span data-ttu-id="8b09a-759">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8b09a-759">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8b09a-760">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-760">JSON Configuration Provider</span></span>

<span data-ttu-id="8b09a-761"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-761">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8b09a-762">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-762">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b09a-763">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-763">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b09a-764">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="8b09a-764">Whether the file is optional.</span></span>
* <span data-ttu-id="8b09a-765">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-765">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8b09a-766"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-766">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8b09a-767">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，会自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-767">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b09a-768">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-768">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8b09a-769">appsettings.json &ndash; 首先读取此文件  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-769">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="8b09a-770">该文件的环境版本可以替代 appsettings.json  文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-770">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8b09a-771">appsettings.{Environment}.json &ndash; 根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载文件的环境版本  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-771">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8b09a-772">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-772">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8b09a-773">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="8b09a-773">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8b09a-774">环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b09a-774">Environment variables.</span></span>
* <span data-ttu-id="8b09a-775">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="8b09a-775">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8b09a-776">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b09a-776">Command-line arguments.</span></span>

<span data-ttu-id="8b09a-777">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-777">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8b09a-778">因此，用户机密、环境变量和命令行参数会替代由 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-778">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8b09a-779">构建主机时调用 `ConfigureAppConfiguration` 以指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置：  </span><span class="sxs-lookup"><span data-stu-id="8b09a-779">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8b09a-780">**示例**</span><span class="sxs-lookup"><span data-stu-id="8b09a-780">**Example**</span></span>

<span data-ttu-id="8b09a-781">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用：</span><span class="sxs-lookup"><span data-stu-id="8b09a-781">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="8b09a-782">第一次调用 `AddJsonFile` 会从 appsettings 加载配置  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-782">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8b09a-783">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-783">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8b09a-784">对于示例应用中的 appsettings.Development.json，将加载以下文件  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-784">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8b09a-785">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-785">Run the sample app.</span></span> <span data-ttu-id="8b09a-786">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="8b09a-786">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8b09a-787">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="8b09a-787">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8b09a-788">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-788">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8b09a-789">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="8b09a-789">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8b09a-790">打开 Properties/launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="8b09a-790">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8b09a-791">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-791">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8b09a-792">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-792">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8b09a-793">appsettings.Development.json 中的设置不再替代 appsettings.json 中的设置   。</span><span class="sxs-lookup"><span data-stu-id="8b09a-793">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8b09a-794">键 `Logging:LogLevel:Default` 的日志级别为 `Warning`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-794">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="8b09a-795">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-795">XML Configuration Provider</span></span>

<span data-ttu-id="8b09a-796"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-796">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8b09a-797">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-797">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b09a-798">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-798">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b09a-799">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="8b09a-799">Whether the file is optional.</span></span>
* <span data-ttu-id="8b09a-800">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-800">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8b09a-801"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-801">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8b09a-802">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="8b09a-802">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8b09a-803">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="8b09a-803">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8b09a-804">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-804">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8b09a-805">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="8b09a-805">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8b09a-806">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="8b09a-806">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b09a-807">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-807">section0:key0</span></span>
* <span data-ttu-id="8b09a-808">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-808">section0:key1</span></span>
* <span data-ttu-id="8b09a-809">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-809">section1:key0</span></span>
* <span data-ttu-id="8b09a-810">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-810">section1:key1</span></span>

<span data-ttu-id="8b09a-811">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="8b09a-811">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8b09a-812">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="8b09a-812">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b09a-813">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-813">section:section0:key:key0</span></span>
* <span data-ttu-id="8b09a-814">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-814">section:section0:key:key1</span></span>
* <span data-ttu-id="8b09a-815">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-815">section:section1:key:key0</span></span>
* <span data-ttu-id="8b09a-816">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-816">section:section1:key:key1</span></span>

<span data-ttu-id="8b09a-817">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-817">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8b09a-818">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="8b09a-818">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b09a-819">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8b09a-819">key:attribute</span></span>
* <span data-ttu-id="8b09a-820">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8b09a-820">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8b09a-821">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-821">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8b09a-822"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-822">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8b09a-823">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="8b09a-823">The key is the file name.</span></span> <span data-ttu-id="8b09a-824">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="8b09a-824">The value contains the file's contents.</span></span> <span data-ttu-id="8b09a-825">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="8b09a-825">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8b09a-826">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-826">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8b09a-827">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="8b09a-827">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8b09a-828">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="8b09a-828">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b09a-829">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="8b09a-829">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8b09a-830">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="8b09a-830">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8b09a-831">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="8b09a-831">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8b09a-832">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-832">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8b09a-833">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-833">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8b09a-834">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-834">Memory Configuration Provider</span></span>

<span data-ttu-id="8b09a-835"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="8b09a-835">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8b09a-836">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8b09a-836">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b09a-837">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-837">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8b09a-838">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-838">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8b09a-839">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="8b09a-839">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8b09a-840">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="8b09a-840">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8b09a-841">GetValue</span><span class="sxs-lookup"><span data-stu-id="8b09a-841">GetValue</span></span>

<span data-ttu-id="8b09a-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的非集合类型。</span><span class="sxs-lookup"><span data-stu-id="8b09a-842">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8b09a-843">重载接受默认值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-843">An overload accepts a default value.</span></span>

<span data-ttu-id="8b09a-844">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="8b09a-844">The following example:</span></span>

* <span data-ttu-id="8b09a-845">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-845">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8b09a-846">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-846">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8b09a-847">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-847">Types the value as an `int`.</span></span>
* <span data-ttu-id="8b09a-848">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="8b09a-848">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8b09a-849">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="8b09a-849">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8b09a-850">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="8b09a-850">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8b09a-851">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="8b09a-851">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8b09a-852">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-852">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8b09a-853">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-853">section0:key0</span></span>
* <span data-ttu-id="8b09a-854">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-854">section0:key1</span></span>
* <span data-ttu-id="8b09a-855">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-855">section1:key0</span></span>
* <span data-ttu-id="8b09a-856">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-856">section1:key1</span></span>
* <span data-ttu-id="8b09a-857">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-857">section2:subsection0:key0</span></span>
* <span data-ttu-id="8b09a-858">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-858">section2:subsection0:key1</span></span>
* <span data-ttu-id="8b09a-859">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8b09a-859">section2:subsection1:key0</span></span>
* <span data-ttu-id="8b09a-860">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8b09a-860">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8b09a-861">GetSection</span><span class="sxs-lookup"><span data-stu-id="8b09a-861">GetSection</span></span>

<span data-ttu-id="8b09a-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="8b09a-862">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8b09a-863">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="8b09a-863">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8b09a-864">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="8b09a-864">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8b09a-865">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="8b09a-865">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8b09a-866">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-866">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8b09a-867">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-867">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8b09a-868">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="8b09a-868">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8b09a-869">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="8b09a-869">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8b09a-870">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8b09a-870">GetChildren</span></span>

<span data-ttu-id="8b09a-871">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="8b09a-871">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8b09a-872">存在</span><span class="sxs-lookup"><span data-stu-id="8b09a-872">Exists</span></span>

<span data-ttu-id="8b09a-873">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="8b09a-873">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8b09a-874">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="8b09a-874">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8b09a-875">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="8b09a-875">Bind to an object graph</span></span>

<span data-ttu-id="8b09a-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="8b09a-876"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="8b09a-877">与绑定简单对象一样，只绑定公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="8b09a-877">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="8b09a-878">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs  )：</span><span class="sxs-lookup"><span data-stu-id="8b09a-878">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="8b09a-879">示例应用有一个包含配置数据的 tvshow.xml  文件：</span><span class="sxs-lookup"><span data-stu-id="8b09a-879">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="8b09a-880">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="8b09a-880">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8b09a-881">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="8b09a-881">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8b09a-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定的类型。</span><span class="sxs-lookup"><span data-stu-id="8b09a-882">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8b09a-883">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="8b09a-883">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8b09a-884">以下代码演示了如何通过前述示例使用 `Get<T>`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-884">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8b09a-885">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="8b09a-885">Bind an array to a class</span></span>

<span data-ttu-id="8b09a-886">示例应用演示了本部分中介绍的概念。 </span><span class="sxs-lookup"><span data-stu-id="8b09a-886">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8b09a-887"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="8b09a-887">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8b09a-888">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="8b09a-888">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8b09a-889">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-889">Binding is provided by convention.</span></span> <span data-ttu-id="8b09a-890">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="8b09a-890">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8b09a-891">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="8b09a-891">**In-memory array processing**</span></span>

<span data-ttu-id="8b09a-892">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-892">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8b09a-893">键</span><span class="sxs-lookup"><span data-stu-id="8b09a-893">Key</span></span>             | <span data-ttu-id="8b09a-894">“值”</span><span class="sxs-lookup"><span data-stu-id="8b09a-894">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8b09a-895">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="8b09a-895">array:entries:0</span></span> | <span data-ttu-id="8b09a-896">value0</span><span class="sxs-lookup"><span data-stu-id="8b09a-896">value0</span></span> |
| <span data-ttu-id="8b09a-897">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="8b09a-897">array:entries:1</span></span> | <span data-ttu-id="8b09a-898">value1</span><span class="sxs-lookup"><span data-stu-id="8b09a-898">value1</span></span> |
| <span data-ttu-id="8b09a-899">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="8b09a-899">array:entries:2</span></span> | <span data-ttu-id="8b09a-900">value2</span><span class="sxs-lookup"><span data-stu-id="8b09a-900">value2</span></span> |
| <span data-ttu-id="8b09a-901">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="8b09a-901">array:entries:4</span></span> | <span data-ttu-id="8b09a-902">value4</span><span class="sxs-lookup"><span data-stu-id="8b09a-902">value4</span></span> |
| <span data-ttu-id="8b09a-903">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="8b09a-903">array:entries:5</span></span> | <span data-ttu-id="8b09a-904">value5</span><span class="sxs-lookup"><span data-stu-id="8b09a-904">value5</span></span> |

<span data-ttu-id="8b09a-905">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="8b09a-905">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="8b09a-906">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-906">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8b09a-907">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="8b09a-907">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8b09a-908">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="8b09a-908">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="8b09a-909">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="8b09a-909">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8b09a-910">还可以使用 [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="8b09a-910">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="8b09a-911">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-911">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8b09a-912">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="8b09a-912">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8b09a-913">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="8b09a-913">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8b09a-914">0</span><span class="sxs-lookup"><span data-stu-id="8b09a-914">0</span></span>                            | <span data-ttu-id="8b09a-915">value0</span><span class="sxs-lookup"><span data-stu-id="8b09a-915">value0</span></span>                       |
| <span data-ttu-id="8b09a-916">1</span><span class="sxs-lookup"><span data-stu-id="8b09a-916">1</span></span>                            | <span data-ttu-id="8b09a-917">value1</span><span class="sxs-lookup"><span data-stu-id="8b09a-917">value1</span></span>                       |
| <span data-ttu-id="8b09a-918">2</span><span class="sxs-lookup"><span data-stu-id="8b09a-918">2</span></span>                            | <span data-ttu-id="8b09a-919">value2</span><span class="sxs-lookup"><span data-stu-id="8b09a-919">value2</span></span>                       |
| <span data-ttu-id="8b09a-920">3</span><span class="sxs-lookup"><span data-stu-id="8b09a-920">3</span></span>                            | <span data-ttu-id="8b09a-921">value4</span><span class="sxs-lookup"><span data-stu-id="8b09a-921">value4</span></span>                       |
| <span data-ttu-id="8b09a-922">4</span><span class="sxs-lookup"><span data-stu-id="8b09a-922">4</span></span>                            | <span data-ttu-id="8b09a-923">value5</span><span class="sxs-lookup"><span data-stu-id="8b09a-923">value5</span></span>                       |

<span data-ttu-id="8b09a-924">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-924">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8b09a-925">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="8b09a-925">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8b09a-926">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="8b09a-926">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8b09a-927">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="8b09a-927">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8b09a-928">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="8b09a-928">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8b09a-929">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8b09a-929">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8b09a-930">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="8b09a-930">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8b09a-931">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="8b09a-931">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8b09a-932">键</span><span class="sxs-lookup"><span data-stu-id="8b09a-932">Key</span></span>             | <span data-ttu-id="8b09a-933">“值”</span><span class="sxs-lookup"><span data-stu-id="8b09a-933">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8b09a-934">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="8b09a-934">array:entries:3</span></span> | <span data-ttu-id="8b09a-935">value3</span><span class="sxs-lookup"><span data-stu-id="8b09a-935">value3</span></span> |

<span data-ttu-id="8b09a-936">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-936">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8b09a-937">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="8b09a-937">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8b09a-938">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="8b09a-938">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8b09a-939">0</span><span class="sxs-lookup"><span data-stu-id="8b09a-939">0</span></span>                            | <span data-ttu-id="8b09a-940">value0</span><span class="sxs-lookup"><span data-stu-id="8b09a-940">value0</span></span>                       |
| <span data-ttu-id="8b09a-941">1</span><span class="sxs-lookup"><span data-stu-id="8b09a-941">1</span></span>                            | <span data-ttu-id="8b09a-942">value1</span><span class="sxs-lookup"><span data-stu-id="8b09a-942">value1</span></span>                       |
| <span data-ttu-id="8b09a-943">2</span><span class="sxs-lookup"><span data-stu-id="8b09a-943">2</span></span>                            | <span data-ttu-id="8b09a-944">value2</span><span class="sxs-lookup"><span data-stu-id="8b09a-944">value2</span></span>                       |
| <span data-ttu-id="8b09a-945">3</span><span class="sxs-lookup"><span data-stu-id="8b09a-945">3</span></span>                            | <span data-ttu-id="8b09a-946">value3</span><span class="sxs-lookup"><span data-stu-id="8b09a-946">value3</span></span>                       |
| <span data-ttu-id="8b09a-947">4</span><span class="sxs-lookup"><span data-stu-id="8b09a-947">4</span></span>                            | <span data-ttu-id="8b09a-948">value4</span><span class="sxs-lookup"><span data-stu-id="8b09a-948">value4</span></span>                       |
| <span data-ttu-id="8b09a-949">5</span><span class="sxs-lookup"><span data-stu-id="8b09a-949">5</span></span>                            | <span data-ttu-id="8b09a-950">value5</span><span class="sxs-lookup"><span data-stu-id="8b09a-950">value5</span></span>                       |

<span data-ttu-id="8b09a-951">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="8b09a-951">**JSON array processing**</span></span>

<span data-ttu-id="8b09a-952">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="8b09a-952">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8b09a-953">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="8b09a-953">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="8b09a-954">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="8b09a-954">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8b09a-955">键</span><span class="sxs-lookup"><span data-stu-id="8b09a-955">Key</span></span>                     | <span data-ttu-id="8b09a-956">“值”</span><span class="sxs-lookup"><span data-stu-id="8b09a-956">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8b09a-957">json_array:key</span><span class="sxs-lookup"><span data-stu-id="8b09a-957">json_array:key</span></span>          | <span data-ttu-id="8b09a-958">valueA</span><span class="sxs-lookup"><span data-stu-id="8b09a-958">valueA</span></span> |
| <span data-ttu-id="8b09a-959">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="8b09a-959">json_array:subsection:0</span></span> | <span data-ttu-id="8b09a-960">valueB</span><span class="sxs-lookup"><span data-stu-id="8b09a-960">valueB</span></span> |
| <span data-ttu-id="8b09a-961">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="8b09a-961">json_array:subsection:1</span></span> | <span data-ttu-id="8b09a-962">valueC</span><span class="sxs-lookup"><span data-stu-id="8b09a-962">valueC</span></span> |
| <span data-ttu-id="8b09a-963">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="8b09a-963">json_array:subsection:2</span></span> | <span data-ttu-id="8b09a-964">valueD</span><span class="sxs-lookup"><span data-stu-id="8b09a-964">valueD</span></span> |

<span data-ttu-id="8b09a-965">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="8b09a-965">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="8b09a-966">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-966">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8b09a-967">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="8b09a-967">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8b09a-968">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="8b09a-968">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8b09a-969">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="8b09a-969">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8b09a-970">0</span><span class="sxs-lookup"><span data-stu-id="8b09a-970">0</span></span>                                   | <span data-ttu-id="8b09a-971">valueB</span><span class="sxs-lookup"><span data-stu-id="8b09a-971">valueB</span></span>                              |
| <span data-ttu-id="8b09a-972">1</span><span class="sxs-lookup"><span data-stu-id="8b09a-972">1</span></span>                                   | <span data-ttu-id="8b09a-973">valueC</span><span class="sxs-lookup"><span data-stu-id="8b09a-973">valueC</span></span>                              |
| <span data-ttu-id="8b09a-974">2</span><span class="sxs-lookup"><span data-stu-id="8b09a-974">2</span></span>                                   | <span data-ttu-id="8b09a-975">valueD</span><span class="sxs-lookup"><span data-stu-id="8b09a-975">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8b09a-976">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="8b09a-976">Custom configuration provider</span></span>

<span data-ttu-id="8b09a-977">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-977">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8b09a-978">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="8b09a-978">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8b09a-979">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="8b09a-979">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8b09a-980">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="8b09a-980">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8b09a-981">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="8b09a-981">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8b09a-982">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="8b09a-982">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8b09a-983">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="8b09a-983">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8b09a-984">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="8b09a-984">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="8b09a-985">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-985">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8b09a-986">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-986">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8b09a-987">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-987">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8b09a-988">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="8b09a-988">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8b09a-989">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-989">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8b09a-990">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b09a-990">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8b09a-991">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="8b09a-991">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8b09a-992">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="8b09a-992">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8b09a-993">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="8b09a-993">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8b09a-994">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="8b09a-994">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8b09a-995">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="8b09a-995">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8b09a-996">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-996">Access configuration during startup</span></span>

<span data-ttu-id="8b09a-997">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="8b09a-997">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b09a-998">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="8b09a-998">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8b09a-999">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="8b09a-999">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="8b09a-1000">在 Razor Pages 页或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-1000">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8b09a-1001">若要访问 Razor Pages 页或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration 命名空间](xref:Microsoft.Extensions.Configuration)添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入页面或视图。</span><span class="sxs-lookup"><span data-stu-id="8b09a-1001">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8b09a-1002">在 Razor 页面页中：</span><span class="sxs-lookup"><span data-stu-id="8b09a-1002">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8b09a-1003">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="8b09a-1003">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8b09a-1004">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="8b09a-1004">Add configuration from an external assembly</span></span>

<span data-ttu-id="8b09a-1005">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="8b09a-1005">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8b09a-1006">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b09a-1006">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b09a-1007">其他资源</span><span class="sxs-lookup"><span data-stu-id="8b09a-1007">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
