---
title: ASP.NET Core 中的配置
author: guardrex
description: 理解如何使用配置 API 配置 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2019
uid: fundamentals/configuration/index
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="b0dea-103">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="b0dea-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="b0dea-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b0dea-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b0dea-105">ASP.NET Core 中的应用配置基于配置提供程序建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="b0dea-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="b0dea-106">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="b0dea-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="b0dea-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0dea-107">Azure Key Vault</span></span>
* <span data-ttu-id="b0dea-108">命令行参数</span><span class="sxs-lookup"><span data-stu-id="b0dea-108">Command-line arguments</span></span>
* <span data-ttu-id="b0dea-109">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b0dea-110">目录文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-110">Directory files</span></span>
* <span data-ttu-id="b0dea-111">环境变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-111">Environment variables</span></span>
* <span data-ttu-id="b0dea-112">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="b0dea-112">In-memory .NET objects</span></span>
* <span data-ttu-id="b0dea-113">设置文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-113">Settings files</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

* <span data-ttu-id="b0dea-114">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0dea-114">Azure Key Vault</span></span>
* <span data-ttu-id="b0dea-115">命令行参数</span><span class="sxs-lookup"><span data-stu-id="b0dea-115">Command-line arguments</span></span>
* <span data-ttu-id="b0dea-116">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-116">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b0dea-117">环境变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-117">Environment variables</span></span>
* <span data-ttu-id="b0dea-118">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="b0dea-118">In-memory .NET objects</span></span>
* <span data-ttu-id="b0dea-119">设置文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-119">Settings files</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.0"

* <span data-ttu-id="b0dea-120">命令行参数</span><span class="sxs-lookup"><span data-stu-id="b0dea-120">Command-line arguments</span></span>
* <span data-ttu-id="b0dea-121">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-121">Custom providers (installed or created)</span></span>
* <span data-ttu-id="b0dea-122">环境变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-122">Environment variables</span></span>
* <span data-ttu-id="b0dea-123">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="b0dea-123">In-memory .NET objects</span></span>
* <span data-ttu-id="b0dea-124">设置文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-124">Settings files</span></span>

::: moniker-end

<span data-ttu-id="b0dea-125">选项模式是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="b0dea-125">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="b0dea-126">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="b0dea-126">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="b0dea-127">有关使用选项模式的详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-127">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b0dea-128">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b0dea-128">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-129">这三个包均包括在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-129">These three packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0dea-130">这三个包均包括在 [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-130">These three packages are included in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

## <a name="host-vs-app-configuration"></a><span data-ttu-id="b0dea-131">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="b0dea-131">Host vs. app configuration</span></span>

<span data-ttu-id="b0dea-132">在配置并启动应用之前，配置并启动主机。</span><span class="sxs-lookup"><span data-stu-id="b0dea-132">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="b0dea-133">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="b0dea-133">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="b0dea-134">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-134">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="b0dea-135">主机配置键值对成为应用的全局配置的一部分。</span><span class="sxs-lookup"><span data-stu-id="b0dea-135">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="b0dea-136">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅[主机](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-136">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="b0dea-137">默认配置</span><span class="sxs-lookup"><span data-stu-id="b0dea-137">Default configuration</span></span>

<span data-ttu-id="b0dea-138">基于 ASP.NET Core [dotnet 新](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-138">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="b0dea-139">`CreateDefaultBuilder` 为应用提供默认配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-139">`CreateDefaultBuilder` provides default configuration for the app.</span></span>

* <span data-ttu-id="b0dea-140">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="b0dea-140">Host configuration is provided from:</span></span>
  * <span data-ttu-id="b0dea-141">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="b0dea-141">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="b0dea-142">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="b0dea-142">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="b0dea-143">应用配置通过以下方式提供（按以下顺序）：</span><span class="sxs-lookup"><span data-stu-id="b0dea-143">App configuration is provided from (in the following order):</span></span>
  * <span data-ttu-id="b0dea-144">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json 提供。</span><span class="sxs-lookup"><span data-stu-id="b0dea-144">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b0dea-145">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json 提供。</span><span class="sxs-lookup"><span data-stu-id="b0dea-145">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="b0dea-146">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-146">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="b0dea-147">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="b0dea-147">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="b0dea-148">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="b0dea-148">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="b0dea-149">本主题后面将介绍配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-149">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="b0dea-150">有关主机和 `CreateDefaultBuilder` 的更多信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-150">For more information on the host and `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="b0dea-151">安全性</span><span class="sxs-lookup"><span data-stu-id="b0dea-151">Security</span></span>

<span data-ttu-id="b0dea-152">采用以下最佳实践：</span><span class="sxs-lookup"><span data-stu-id="b0dea-152">Adopt the following best practices:</span></span>

* <span data-ttu-id="b0dea-153">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="b0dea-153">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="b0dea-154">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="b0dea-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="b0dea-155">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="b0dea-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="b0dea-156">详细了解[如何使用多个环境](xref:fundamentals/environments)和管理[使用 Secret Manager 的开发中的应用机密的安全存储](xref:security/app-secrets)（包括使用环境变量存储敏感数据的建议）。</span><span class="sxs-lookup"><span data-stu-id="b0dea-156">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="b0dea-157">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-157">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="b0dea-158">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-158">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b0dea-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 是安全存储应用机密的一种选择。</span><span class="sxs-lookup"><span data-stu-id="b0dea-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="b0dea-160">有关更多信息，请参见<xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="b0dea-161">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="b0dea-161">Hierarchical configuration data</span></span>

<span data-ttu-id="b0dea-162">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="b0dea-162">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="b0dea-163">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="b0dea-163">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="b0dea-164">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="b0dea-164">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="b0dea-165">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="b0dea-165">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="b0dea-166">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-166">section0:key0</span></span>
* <span data-ttu-id="b0dea-167">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-167">section0:key1</span></span>
* <span data-ttu-id="b0dea-168">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-168">section1:key0</span></span>
* <span data-ttu-id="b0dea-169">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-169">section1:key1</span></span>

<span data-ttu-id="b0dea-170"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="b0dea-170"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="b0dea-171">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-171">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="b0dea-172">`GetSection` 在 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-172">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="b0dea-173">约定</span><span class="sxs-lookup"><span data-stu-id="b0dea-173">Conventions</span></span>

<span data-ttu-id="b0dea-174">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="b0dea-174">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="b0dea-175">应用启动后，在更改基础设置文件时，文件配置提供程序可以重载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-175">File Configuration Providers have the ability to reload configuration when an underlying settings file is changed after app startup.</span></span> <span data-ttu-id="b0dea-176">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-176">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="b0dea-177">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-177"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [Dependency Injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b0dea-178">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-178">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="b0dea-179">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="b0dea-179">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="b0dea-180">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="b0dea-180">Keys are case-insensitive.</span></span> <span data-ttu-id="b0dea-181">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="b0dea-181">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="b0dea-182">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-182">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="b0dea-183">分层键</span><span class="sxs-lookup"><span data-stu-id="b0dea-183">Hierarchical keys</span></span>
  * <span data-ttu-id="b0dea-184">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="b0dea-184">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="b0dea-185">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="b0dea-185">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="b0dea-186">而所有平台均支持采用双下划线 (`__`)，并可以将其转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="b0dea-186">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="b0dea-187">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="b0dea-187">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="b0dea-188">将机密加载到应用的配置中时，必须提供代码以用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="b0dea-188">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="b0dea-189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="b0dea-189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b0dea-190">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="b0dea-190">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="b0dea-191">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="b0dea-191">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="b0dea-192">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="b0dea-192">Values are strings.</span></span>
* <span data-ttu-id="b0dea-193">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="b0dea-193">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="b0dea-194">提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-194">Providers</span></span>

<span data-ttu-id="b0dea-195">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-195">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

::: moniker range=">= aspnetcore-2.1"

| <span data-ttu-id="b0dea-196">提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-196">Provider</span></span> | <span data-ttu-id="b0dea-197">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="b0dea-197">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b0dea-198">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="b0dea-198">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b0dea-199">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0dea-199">Azure Key Vault</span></span> |
| [<span data-ttu-id="b0dea-200">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-200">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b0dea-201">命令行参数</span><span class="sxs-lookup"><span data-stu-id="b0dea-201">Command-line parameters</span></span> |
| [<span data-ttu-id="b0dea-202">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-202">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b0dea-203">自定义源</span><span class="sxs-lookup"><span data-stu-id="b0dea-203">Custom source</span></span> |
| [<span data-ttu-id="b0dea-204">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-204">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b0dea-205">环境变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-205">Environment variables</span></span> |
| [<span data-ttu-id="b0dea-206">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-206">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b0dea-207">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="b0dea-207">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b0dea-208">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-208">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="b0dea-209">目录文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-209">Directory files</span></span> |
| [<span data-ttu-id="b0dea-210">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-210">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b0dea-211">内存中集合</span><span class="sxs-lookup"><span data-stu-id="b0dea-211">In-memory collections</span></span> |
| <span data-ttu-id="b0dea-212">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="b0dea-212">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b0dea-213">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-213">File in the user profile directory</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.0 || aspnetcore-1.1"

| <span data-ttu-id="b0dea-214">提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-214">Provider</span></span> | <span data-ttu-id="b0dea-215">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="b0dea-215">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="b0dea-216">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="b0dea-216">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="b0dea-217">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="b0dea-217">Azure Key Vault</span></span> |
| [<span data-ttu-id="b0dea-218">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-218">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b0dea-219">命令行参数</span><span class="sxs-lookup"><span data-stu-id="b0dea-219">Command-line parameters</span></span> |
| [<span data-ttu-id="b0dea-220">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-220">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b0dea-221">自定义源</span><span class="sxs-lookup"><span data-stu-id="b0dea-221">Custom source</span></span> |
| [<span data-ttu-id="b0dea-222">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-222">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b0dea-223">环境变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-223">Environment variables</span></span> |
| [<span data-ttu-id="b0dea-224">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-224">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b0dea-225">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="b0dea-225">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b0dea-226">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-226">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b0dea-227">内存中集合</span><span class="sxs-lookup"><span data-stu-id="b0dea-227">In-memory collections</span></span> |
| <span data-ttu-id="b0dea-228">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="b0dea-228">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b0dea-229">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-229">File in the user profile directory</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-1.0"

| <span data-ttu-id="b0dea-230">提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-230">Provider</span></span> | <span data-ttu-id="b0dea-231">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="b0dea-231">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="b0dea-232">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-232">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="b0dea-233">命令行参数</span><span class="sxs-lookup"><span data-stu-id="b0dea-233">Command-line parameters</span></span> |
| [<span data-ttu-id="b0dea-234">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-234">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="b0dea-235">自定义源</span><span class="sxs-lookup"><span data-stu-id="b0dea-235">Custom source</span></span> |
| [<span data-ttu-id="b0dea-236">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-236">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="b0dea-237">环境变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-237">Environment variables</span></span> |
| [<span data-ttu-id="b0dea-238">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-238">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="b0dea-239">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="b0dea-239">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="b0dea-240">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-240">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="b0dea-241">内存中集合</span><span class="sxs-lookup"><span data-stu-id="b0dea-241">In-memory collections</span></span> |
| <span data-ttu-id="b0dea-242">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="b0dea-242">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="b0dea-243">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="b0dea-243">File in the user profile directory</span></span> |

::: moniker-end

<span data-ttu-id="b0dea-244">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="b0dea-244">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="b0dea-245">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="b0dea-245">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="b0dea-246">代码中的配置提供程序应以特定顺序排列以符合基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="b0dea-246">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="b0dea-247">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="b0dea-247">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="b0dea-248">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境）</span><span class="sxs-lookup"><span data-stu-id="b0dea-248">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="b0dea-249">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="b0dea-249">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="b0dea-250">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="b0dea-250">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="b0dea-251">环境变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-251">Environment variables</span></span>
1. <span data-ttu-id="b0dea-252">命令行参数</span><span class="sxs-lookup"><span data-stu-id="b0dea-252">Command-line arguments</span></span>

<span data-ttu-id="b0dea-253">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-253">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-254">在使用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 初始化新的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，将使用此提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="b0dea-254">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b0dea-255">有关详细信息，请参阅 [Web 主机：设置主机](xref:fundamentals/host/web-host#set-up-a-host)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-255">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-256">可以使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 和在 `Startup` 中调用其 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*> 方法来为应用（而不是主机）创建此提供程序序列：</span><span class="sxs-lookup"><span data-stu-id="b0dea-256">This sequence of providers can be created for the app (not the host) with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> and a call to its <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder.Build*> method in `Startup`:</span></span>

```csharp
public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
            reloadOnChange: true);

    var appAssembly = Assembly.Load(new AssemblyName(env.ApplicationName));

    if (appAssembly != null)
    {
        builder.AddUserSecrets(appAssembly, optional: true);
    }

    builder.AddEnvironmentVariables();

    Configuration = builder.Build();
}

public IConfiguration Configuration { get; }

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IConfiguration>(Configuration);
}
```

<span data-ttu-id="b0dea-257">在前面的示例中，<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供了环境名称 (`env.EnvironmentName`) 和应用程序集名称 (`env.ApplicationName`)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-257">In the preceding example, the environment name (`env.EnvironmentName`) and app assembly name (`env.ApplicationName`) are provided by the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>.</span></span> <span data-ttu-id="b0dea-258">有关更多信息，请参见<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-258">For more information, see <xref:fundamentals/environments>.</span></span> <span data-ttu-id="b0dea-259">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-259">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-260">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-260">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
<span data-ttu-id="b0dea-261">.</span><span class="sxs-lookup"><span data-stu-id="b0dea-261">.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="configureappconfiguration"></a><span data-ttu-id="b0dea-262">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="b0dea-262">ConfigureAppConfiguration</span></span>

<span data-ttu-id="b0dea-263">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置提供程序以及 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="b0dea-263">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

::: moniker-end

## <a name="command-line-configuration-provider"></a><span data-ttu-id="b0dea-264">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-264">Command-line Configuration Provider</span></span>

<span data-ttu-id="b0dea-265"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-265">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="b0dea-266">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-266">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-267">使用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 初始化新的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-267">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b0dea-268">有关详细信息，请参阅 [Web 主机：设置主机](xref:fundamentals/host/web-host#set-up-a-host)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-268">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b0dea-269">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="b0dea-269">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b0dea-270">appsettings.json 和 appsettings.{Environment}.json 的可选配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-270">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b0dea-271">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="b0dea-271">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b0dea-272">环境变量。</span><span class="sxs-lookup"><span data-stu-id="b0dea-272">Environment variables.</span></span>

<span data-ttu-id="b0dea-273">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-273">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="b0dea-274">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-274">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="b0dea-275">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-275">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="b0dea-276">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="b0dea-276">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-277">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-277">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b0dea-278">`CreateDefaultBuilder` 已经调用了 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-278">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b0dea-279">如果需要提供应用配置并仍然能够使用命令行参数覆盖该配置，请在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 中调用应用的其他提供程序并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-279">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-280">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-280">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0dea-281">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-281">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method.</span></span>

<span data-ttu-id="b0dea-282">调用 `UseConfiguration` 时，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-282">`AddCommandLine` has already been called by `CreateDefaultBuilder` when `UseConfiguration` is called.</span></span> <span data-ttu-id="b0dea-283">如果需要提供应用配置并仍然能够使用命令行参数覆盖该配置，请在 `ConfigurationBuilder` 中调用应用的其他提供程序并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-283">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers on a `ConfigurationBuilder` and call `AddCommandLine` last.</span></span>

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
            // Call other providers here and call AddCommandLine last.
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0dea-284">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-284">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-285">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-285">To activate command-line configuration, call the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0dea-286">最后调用提供程序，以允许在运行时传递的命令行参数替代由其他配置提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-286">Call the provider last to allow the command-line arguments passed at runtime to override configuration set by other configuration providers.</span></span>

<span data-ttu-id="b0dea-287">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-287">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b0dea-288">**示例**</span><span class="sxs-lookup"><span data-stu-id="b0dea-288">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-289">2.x 示例应用利用静态便捷方法 `CreateDefaultBuilder` 来构建主机，其中包括对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-289">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-290">1.x 示例应用在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-290">The 1.x sample app calls <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> on a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

::: moniker-end

1. <span data-ttu-id="b0dea-291">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="b0dea-291">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="b0dea-292">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-292">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="b0dea-293">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="b0dea-293">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b0dea-294">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="b0dea-294">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="b0dea-295">自变量</span><span class="sxs-lookup"><span data-stu-id="b0dea-295">Arguments</span></span>

<span data-ttu-id="b0dea-296">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="b0dea-296">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="b0dea-297">如果使用等号（例如，`CommandLineKey=`），则该值可以为 null。</span><span class="sxs-lookup"><span data-stu-id="b0dea-297">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="b0dea-298">键前缀</span><span class="sxs-lookup"><span data-stu-id="b0dea-298">Key prefix</span></span>               | <span data-ttu-id="b0dea-299">示例</span><span class="sxs-lookup"><span data-stu-id="b0dea-299">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="b0dea-300">无前缀</span><span class="sxs-lookup"><span data-stu-id="b0dea-300">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="b0dea-301">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="b0dea-301">Two dashes (`--`)</span></span>        | <span data-ttu-id="b0dea-302">`--CommandLineKey2=value2`， `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="b0dea-302">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="b0dea-303">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="b0dea-303">Forward slash (`/`)</span></span>      | <span data-ttu-id="b0dea-304">`/CommandLineKey3=value3`， `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="b0dea-304">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="b0dea-305">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-305">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="b0dea-306">示例命令：</span><span class="sxs-lookup"><span data-stu-id="b0dea-306">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="b0dea-307">交换映射</span><span class="sxs-lookup"><span data-stu-id="b0dea-307">Switch mappings</span></span>

<span data-ttu-id="b0dea-308">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="b0dea-308">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="b0dea-309">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，可以为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="b0dea-309">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="b0dea-310">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="b0dea-310">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="b0dea-311">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-311">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="b0dea-312">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="b0dea-312">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="b0dea-313">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="b0dea-313">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="b0dea-314">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="b0dea-314">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="b0dea-315">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="b0dea-315">The switch mappings dictionary must not contain duplicate keys.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-316">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-316">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-317">如前面的示例所示，当使用交换映射时，对 `CreateDefaultBuilder` 的调用不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="b0dea-317">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="b0dea-318">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-318">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b0dea-319">如果参数包含映射的交换并传递给 `CreateDefaultBuilder`，则其 `AddCommandLine` 提供程序无法使用 <xref:System.FormatException> 进行初始化。</span><span class="sxs-lookup"><span data-stu-id="b0dea-319">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="b0dea-320">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="b0dea-320">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var switchMappings = new Dictionary<string, string>
            {
                { "-CLKey1", "CommandLineKey1" },
                { "-CLKey2", "CommandLineKey2" }
            };

        var config = new ConfigurationBuilder()
            .AddCommandLine(args, switchMappings)
            .Build();

        // Do not pass the args to CreateDefaultBuilder
        return WebHost.CreateDefaultBuilder()
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0dea-321">如前面的示例所示，当使用交换映射时，对 `CreateDefaultBuilder` 的调用不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="b0dea-321">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="b0dea-322">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-322">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="b0dea-323">如果参数包含映射的交换并传递给 `CreateDefaultBuilder`，则其 `AddCommandLine` 提供程序无法使用 <xref:System.FormatException> 进行初始化。</span><span class="sxs-lookup"><span data-stu-id="b0dea-323">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="b0dea-324">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="b0dea-324">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    var switchMappings = new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    var config = new ConfigurationBuilder()
        .AddCommandLine(args, switchMappings)
        .Build();

    var host = new WebHostBuilder()
        .UseConfiguration(config)
        .UseKestrel()
        .UseStartup<Startup>()
        .Start();

    using (host)
    {
        Console.ReadLine();
    }
}
```

::: moniker-end

<span data-ttu-id="b0dea-325">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="b0dea-325">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="b0dea-326">键</span><span class="sxs-lookup"><span data-stu-id="b0dea-326">Key</span></span>       | <span data-ttu-id="b0dea-327">值</span><span class="sxs-lookup"><span data-stu-id="b0dea-327">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="b0dea-328">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="b0dea-328">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="b0dea-329">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-329">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="b0dea-330">键</span><span class="sxs-lookup"><span data-stu-id="b0dea-330">Key</span></span>               | <span data-ttu-id="b0dea-331">值</span><span class="sxs-lookup"><span data-stu-id="b0dea-331">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="b0dea-332">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-332">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="b0dea-333"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-333">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="b0dea-334">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-334">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0dea-335">在环境变量中使用分层键时，冒号分隔符 (`:`) 可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="b0dea-335">When working with hierarchical keys in environment variables, a colon separator (`:`) may not work on all platforms.</span></span> <span data-ttu-id="b0dea-336">所有平台均支持采用双下划线 (`__`)，并可以用冒号替换。</span><span class="sxs-lookup"><span data-stu-id="b0dea-336">A double underscore (`__`) is supported by all platforms and is replaced by a colon.</span></span>

<span data-ttu-id="b0dea-337">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，用户可以在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="b0dea-337">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="b0dea-338">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-338">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-339">初始化一个新的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，对于前缀为 `ASPNETCORE_` 的环境变量，会自动调用 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-339">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="b0dea-340">有关详细信息，请参阅 [Web 主机：设置主机](xref:fundamentals/host/web-host#set-up-a-host)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-340">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b0dea-341">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="b0dea-341">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b0dea-342">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-342">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="b0dea-343">appsettings.json 和 appsettings.{Environment}.json 的可选配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-343">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="b0dea-344">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="b0dea-344">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b0dea-345">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="b0dea-345">Command-line arguments.</span></span>

<span data-ttu-id="b0dea-346">环境变量配置提供程序是在配置已根据用户机密和 appsettings 文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-346">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="b0dea-347">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-347">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-348">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-348">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="b0dea-349">如果需要从其他环境变量提供应用配置，请在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-349">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow environment
                // variables to override values from other providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-350">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-350">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0dea-351">在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 `AddEnvironmentVariables` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-351">Call the `AddEnvironmentVariables` extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b0dea-352">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-352">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method.</span></span>

<span data-ttu-id="b0dea-353">如果需要从其他环境变量提供应用配置，请在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-353">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

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
            // Call additional providers here as needed.
            // Call AddEnvironmentVariables last if you need to allow environment
            // variables to override values from other providers.
            .AddEnvironmentVariables(prefix: "PREFIX_")
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0dea-354">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-354">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-355">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-355">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b0dea-356">**示例**</span><span class="sxs-lookup"><span data-stu-id="b0dea-356">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-357">2.x 示例应用利用静态便捷方法 `CreateDefaultBuilder` 来构建主机，其中包括对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-357">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-358">1.x 示例应用在 `ConfigurationBuilder` 上调用 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-358">The 1.x sample app calls `AddEnvironmentVariables` on a `ConfigurationBuilder`.</span></span>

::: moniker-end

1. <span data-ttu-id="b0dea-359">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-359">Run the sample app.</span></span> <span data-ttu-id="b0dea-360">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="b0dea-360">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b0dea-361">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="b0dea-361">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="b0dea-362">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-362">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="b0dea-363">为了使应用呈现的环境变量列表简短，应用将环境变量筛选为以下列内容开头的变量：</span><span class="sxs-lookup"><span data-stu-id="b0dea-363">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="b0dea-364">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="b0dea-364">ASPNETCORE_</span></span>
* <span data-ttu-id="b0dea-365">urls</span><span class="sxs-lookup"><span data-stu-id="b0dea-365">urls</span></span>
* <span data-ttu-id="b0dea-366">日志记录</span><span class="sxs-lookup"><span data-stu-id="b0dea-366">Logging</span></span>
* <span data-ttu-id="b0dea-367">ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="b0dea-367">ENVIRONMENT</span></span>
* <span data-ttu-id="b0dea-368">contentRoot</span><span class="sxs-lookup"><span data-stu-id="b0dea-368">contentRoot</span></span>
* <span data-ttu-id="b0dea-369">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b0dea-369">AllowedHosts</span></span>
* <span data-ttu-id="b0dea-370">applicationName</span><span class="sxs-lookup"><span data-stu-id="b0dea-370">applicationName</span></span>
* <span data-ttu-id="b0dea-371">CommandLine</span><span class="sxs-lookup"><span data-stu-id="b0dea-371">CommandLine</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-372">如果希望公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="b0dea-372">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-373">如果希望公开应用可用的所有环境变量，请将 Controllers/HomeController.cs 中的 `FilteredConfiguration` 更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="b0dea-373">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Controllers/HomeController.cs* to the following:</span></span>

::: moniker-end

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="b0dea-374">前缀</span><span class="sxs-lookup"><span data-stu-id="b0dea-374">Prefixes</span></span>

<span data-ttu-id="b0dea-375">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="b0dea-375">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="b0dea-376">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="b0dea-376">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="b0dea-377">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="b0dea-377">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-378">静态便捷方法 `CreateDefaultBuilder` 创建一个 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 以建立应用的主机。</span><span class="sxs-lookup"><span data-stu-id="b0dea-378">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="b0dea-379">创建 `WebHostBuilder` 时，它会在前缀为 `ASPNETCORE_` 的环境变量中找到其主机配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-379">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

::: moniker-end

<span data-ttu-id="b0dea-380">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="b0dea-380">**Connection string prefixes**</span></span>

<span data-ttu-id="b0dea-381">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="b0dea-381">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="b0dea-382">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-382">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="b0dea-383">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="b0dea-383">Connection string prefix</span></span> | <span data-ttu-id="b0dea-384">提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-384">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="b0dea-385">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-385">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="b0dea-386">MySQL</span><span class="sxs-lookup"><span data-stu-id="b0dea-386">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="b0dea-387">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="b0dea-387">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="b0dea-388">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b0dea-388">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="b0dea-389">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="b0dea-389">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="b0dea-390">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="b0dea-390">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="b0dea-391">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="b0dea-391">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="b0dea-392">环境变量键</span><span class="sxs-lookup"><span data-stu-id="b0dea-392">Environment variable key</span></span> | <span data-ttu-id="b0dea-393">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="b0dea-393">Converted configuration key</span></span> | <span data-ttu-id="b0dea-394">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="b0dea-394">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0dea-395">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="b0dea-395">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0dea-396">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="b0dea-396">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b0dea-397">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="b0dea-397">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0dea-398">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="b0dea-398">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b0dea-399">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b0dea-399">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="b0dea-400">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="b0dea-400">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="b0dea-401">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="b0dea-401">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="b0dea-402">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-402">File Configuration Provider</span></span>

<span data-ttu-id="b0dea-403"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="b0dea-403"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="b0dea-404">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="b0dea-404">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="b0dea-405">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-405">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="b0dea-406">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-406">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="b0dea-407">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-407">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="b0dea-408">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-408">INI Configuration Provider</span></span>

<span data-ttu-id="b0dea-409"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-409">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="b0dea-410">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-410">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0dea-411">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="b0dea-411">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="b0dea-412">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="b0dea-412">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0dea-413">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="b0dea-413">Whether the file is optional.</span></span>
* <span data-ttu-id="b0dea-414">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-414">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b0dea-415"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="b0dea-415">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-416">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-416">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile("config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-417">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-417">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-418">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-418">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-419">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-419">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0dea-420">调用 `CreateDefaultBuilder` 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-420">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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
            .AddIniFile("config.ini", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0dea-421">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-421">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-422">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-422">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-423">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-423">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-424">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-424">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b0dea-425">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-425">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-426">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-426">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-427">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="b0dea-427">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="b0dea-428">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="b0dea-428">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0dea-429">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-429">section0:key0</span></span>
* <span data-ttu-id="b0dea-430">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-430">section0:key1</span></span>
* <span data-ttu-id="b0dea-431">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="b0dea-431">section1:subsection:key</span></span>
* <span data-ttu-id="b0dea-432">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="b0dea-432">section2:subsection0:key</span></span>
* <span data-ttu-id="b0dea-433">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="b0dea-433">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="b0dea-434">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-434">JSON Configuration Provider</span></span>

<span data-ttu-id="b0dea-435"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-435">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="b0dea-436">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-436">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0dea-437">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="b0dea-437">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0dea-438">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="b0dea-438">Whether the file is optional.</span></span>
* <span data-ttu-id="b0dea-439">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-439">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b0dea-440"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="b0dea-440">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-441">使用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 初始化新的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，会自动调用 `AddJsonFile` 两次。</span><span class="sxs-lookup"><span data-stu-id="b0dea-441">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="b0dea-442">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-442">The method is called to load configuration from:</span></span>

* <span data-ttu-id="b0dea-443">appsettings.json &ndash; 首先读取此文件。</span><span class="sxs-lookup"><span data-stu-id="b0dea-443">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="b0dea-444">该文件的环境版本可以替代 appsettings.json 文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-444">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="b0dea-445">*appsettings.{Environment}.json*&ndash; 根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载文件的环境版本。</span><span class="sxs-lookup"><span data-stu-id="b0dea-445">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="b0dea-446">有关详细信息，请参阅 [Web 主机：设置主机](xref:fundamentals/host/web-host#set-up-a-host)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-446">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="b0dea-447">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="b0dea-447">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="b0dea-448">环境变量。</span><span class="sxs-lookup"><span data-stu-id="b0dea-448">Environment variables.</span></span>
* <span data-ttu-id="b0dea-449">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="b0dea-449">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="b0dea-450">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="b0dea-450">Command-line arguments.</span></span>

<span data-ttu-id="b0dea-451">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-451">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="b0dea-452">因此，用户机密、环境变量和命令行参数会替代由 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-452">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-453">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-453">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile("config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-454">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-454">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-455">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-455">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-456">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-456">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0dea-457">还可以在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上直接调用 `AddJsonFile` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-457">You can also directly call the `AddJsonFile` extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0dea-458">调用 `CreateDefaultBuilder` 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-458">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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
            .AddJsonFile("config.json", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0dea-459">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-459">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-460">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-460">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-461">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-461">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-462">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-462">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b0dea-463">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-463">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-464">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-464">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-465">**示例**</span><span class="sxs-lookup"><span data-stu-id="b0dea-465">**Example**</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-466">2.x 示例应用利用静态便捷方法 `CreateDefaultBuilder` 来构建主机，其中包括对 `AddJsonFile` 的两次调用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-466">The 2.x sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="b0dea-467">配置从 appsettings.json 和 appsettings.{Environment}.json 进行加载。</span><span class="sxs-lookup"><span data-stu-id="b0dea-467">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-468">1.x 示例应用在 `ConfigurationBuilder` 上调用 `AddJsonFile` 两次。</span><span class="sxs-lookup"><span data-stu-id="b0dea-468">The 1.x sample app calls `AddJsonFile` twice on a `ConfigurationBuilder`.</span></span> <span data-ttu-id="b0dea-469">配置从 appsettings.json 和 appsettings.{Environment}.json 进行加载。</span><span class="sxs-lookup"><span data-stu-id="b0dea-469">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

::: moniker-end

1. <span data-ttu-id="b0dea-470">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="b0dea-470">Run the sample app.</span></span> <span data-ttu-id="b0dea-471">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="b0dea-471">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="b0dea-472">观察输出是否包含表中所示的配置的键值对，具体取决于环境。</span><span class="sxs-lookup"><span data-stu-id="b0dea-472">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="b0dea-473">记录配置键使用冒号 (`:`) 作为分层分隔符。</span><span class="sxs-lookup"><span data-stu-id="b0dea-473">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="b0dea-474">键</span><span class="sxs-lookup"><span data-stu-id="b0dea-474">Key</span></span>                        | <span data-ttu-id="b0dea-475">开发值</span><span class="sxs-lookup"><span data-stu-id="b0dea-475">Development Value</span></span> | <span data-ttu-id="b0dea-476">生产值</span><span class="sxs-lookup"><span data-stu-id="b0dea-476">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="b0dea-477">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="b0dea-477">Logging:LogLevel:System</span></span>    | <span data-ttu-id="b0dea-478">信息</span><span class="sxs-lookup"><span data-stu-id="b0dea-478">Information</span></span>       | <span data-ttu-id="b0dea-479">信息</span><span class="sxs-lookup"><span data-stu-id="b0dea-479">Information</span></span>      |
| <span data-ttu-id="b0dea-480">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="b0dea-480">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="b0dea-481">信息</span><span class="sxs-lookup"><span data-stu-id="b0dea-481">Information</span></span>       | <span data-ttu-id="b0dea-482">信息</span><span class="sxs-lookup"><span data-stu-id="b0dea-482">Information</span></span>      |
| <span data-ttu-id="b0dea-483">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="b0dea-483">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="b0dea-484">调试</span><span class="sxs-lookup"><span data-stu-id="b0dea-484">Debug</span></span>             | <span data-ttu-id="b0dea-485">Error</span><span class="sxs-lookup"><span data-stu-id="b0dea-485">Error</span></span>            |
| <span data-ttu-id="b0dea-486">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="b0dea-486">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="b0dea-487">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-487">XML Configuration Provider</span></span>

<span data-ttu-id="b0dea-488"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-488">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="b0dea-489">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-489">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0dea-490">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="b0dea-490">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0dea-491">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="b0dea-491">Whether the file is optional.</span></span>
* <span data-ttu-id="b0dea-492">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-492">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="b0dea-493"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="b0dea-493">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="b0dea-494">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="b0dea-494">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="b0dea-495">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="b0dea-495">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-496">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-496">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile("config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-497">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-497">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-498">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-498">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-499">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-499">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0dea-500">调用 `CreateDefaultBuilder` 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-500">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

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
            .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(config)
            .UseStartup<Startup>();
    }
}
```

<span data-ttu-id="b0dea-501">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-501">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-502">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-502">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-503">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-503">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-504">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-504">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="b0dea-505">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-505">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-506">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-506">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-507">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="b0dea-507">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="b0dea-508">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="b0dea-508">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0dea-509">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-509">section0:key0</span></span>
* <span data-ttu-id="b0dea-510">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-510">section0:key1</span></span>
* <span data-ttu-id="b0dea-511">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-511">section1:key0</span></span>
* <span data-ttu-id="b0dea-512">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-512">section1:key1</span></span>

<span data-ttu-id="b0dea-513">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="b0dea-513">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="b0dea-514">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="b0dea-514">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0dea-515">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-515">section:section0:key:key0</span></span>
* <span data-ttu-id="b0dea-516">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-516">section:section0:key:key1</span></span>
* <span data-ttu-id="b0dea-517">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-517">section:section1:key:key0</span></span>
* <span data-ttu-id="b0dea-518">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-518">section:section1:key:key1</span></span>

<span data-ttu-id="b0dea-519">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="b0dea-519">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="b0dea-520">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="b0dea-520">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="b0dea-521">key:attribute</span><span class="sxs-lookup"><span data-stu-id="b0dea-521">key:attribute</span></span>
* <span data-ttu-id="b0dea-522">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="b0dea-522">section:key:attribute</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="b0dea-523">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-523">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="b0dea-524"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="b0dea-524">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="b0dea-525">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="b0dea-525">The key is the file name.</span></span> <span data-ttu-id="b0dea-526">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="b0dea-526">The value contains the file's contents.</span></span> <span data-ttu-id="b0dea-527">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="b0dea-527">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="b0dea-528">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-528">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="b0dea-529">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="b0dea-529">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="b0dea-530">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="b0dea-530">Overloads permit specifying:</span></span>

* <span data-ttu-id="b0dea-531">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="b0dea-531">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="b0dea-532">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="b0dea-532">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="b0dea-533">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="b0dea-533">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="b0dea-534">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-534">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="b0dea-535">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-535">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-536">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-536">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="b0dea-537">`SetBasePath` 在 [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-537">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-538">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-538">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

::: moniker-end

## <a name="memory-configuration-provider"></a><span data-ttu-id="b0dea-539">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-539">Memory Configuration Provider</span></span>

<span data-ttu-id="b0dea-540"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="b0dea-540">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="b0dea-541">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-541">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="b0dea-542">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-542">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b0dea-543">构建主机时调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-543">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="b0dea-544">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-544">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b0dea-545">调用 `CreateDefaultBuilder` 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-545">When calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

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
}
```

<span data-ttu-id="b0dea-546">直接创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 时，请使用以下配置调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-546">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-547">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 方法将配置应用到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>：</span><span class="sxs-lookup"><span data-stu-id="b0dea-547">Apply the configuration to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> method:</span></span>

::: moniker-end

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a><span data-ttu-id="b0dea-548">GetValue</span><span class="sxs-lookup"><span data-stu-id="b0dea-548">GetValue</span></span>

<span data-ttu-id="b0dea-549">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从具有指定键的配置中提取一个值，并将其转换为指定类型。</span><span class="sxs-lookup"><span data-stu-id="b0dea-549">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="b0dea-550">如果未找到该键，则过载允许你提供默认值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-550">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="b0dea-551">以下示例使用键 `NumberKey` 从配置中提取字符串值，键入该值作为 `int`，并将值存储在变量 `intValue` 中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-551">The following example extracts the string value from configuration with the key `NumberKey`, types the value as an `int`, and stores the value in the variable `intValue`.</span></span> <span data-ttu-id="b0dea-552">如果在配置键中找不到 `NumberKey`，则 `intValue` 会接收 `99` 的默认值：</span><span class="sxs-lookup"><span data-stu-id="b0dea-552">If `NumberKey` isn't found in the configuration keys, `intValue` receives the default value of `99`:</span></span>

```csharp
var intValue = config.GetValue<int>("NumberKey", 99);
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="b0dea-553">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="b0dea-553">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="b0dea-554">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="b0dea-554">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="b0dea-555">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="b0dea-555">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="b0dea-556">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="b0dea-556">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="b0dea-557">section0:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-557">section0:key0</span></span>
* <span data-ttu-id="b0dea-558">section0:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-558">section0:key1</span></span>
* <span data-ttu-id="b0dea-559">section1:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-559">section1:key0</span></span>
* <span data-ttu-id="b0dea-560">section1:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-560">section1:key1</span></span>
* <span data-ttu-id="b0dea-561">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-561">section2:subsection0:key0</span></span>
* <span data-ttu-id="b0dea-562">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-562">section2:subsection0:key1</span></span>
* <span data-ttu-id="b0dea-563">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="b0dea-563">section2:subsection1:key0</span></span>
* <span data-ttu-id="b0dea-564">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="b0dea-564">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="b0dea-565">GetSection</span><span class="sxs-lookup"><span data-stu-id="b0dea-565">GetSection</span></span>

<span data-ttu-id="b0dea-566">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="b0dea-566">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="b0dea-567">`GetSection` 在 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-567">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-568">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="b0dea-568">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="b0dea-569">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="b0dea-569">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="b0dea-570">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="b0dea-570">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="b0dea-571">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-571">`GetSection` never returns `null`.</span></span> <span data-ttu-id="b0dea-572">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-572">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="b0dea-573">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="b0dea-573">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="b0dea-574">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="b0dea-574">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="b0dea-575">GetChildren</span><span class="sxs-lookup"><span data-stu-id="b0dea-575">GetChildren</span></span>

<span data-ttu-id="b0dea-576">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="b0dea-576">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

::: moniker range=">= aspnetcore-2.0"

### <a name="exists"></a><span data-ttu-id="b0dea-577">存在</span><span class="sxs-lookup"><span data-stu-id="b0dea-577">Exists</span></span>

<span data-ttu-id="b0dea-578">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="b0dea-578">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="b0dea-579">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="b0dea-579">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

::: moniker-end

## <a name="bind-to-a-class"></a><span data-ttu-id="b0dea-580">绑定至类</span><span class="sxs-lookup"><span data-stu-id="b0dea-580">Bind to a class</span></span>

<span data-ttu-id="b0dea-581">可以使用选项模式将配置绑定到表示相关设置组的类。</span><span class="sxs-lookup"><span data-stu-id="b0dea-581">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="b0dea-582">有关更多信息，请参见<xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-582">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="b0dea-583">配置值作为字符串返回，但调用 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以构造 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 对象。</span><span class="sxs-lookup"><span data-stu-id="b0dea-583">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="b0dea-584">`Bind` 在 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-584">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-585">示例应用包含 `Starship` 模型 (Models/Starship.cs)：</span><span class="sxs-lookup"><span data-stu-id="b0dea-585">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-586">当示例应用使用 JSON 配置提供程序加载配置时，starship.json 文件的 `starship` 节会创建配置：</span><span class="sxs-lookup"><span data-stu-id="b0dea-586">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="b0dea-587">创建以下配置键值对：</span><span class="sxs-lookup"><span data-stu-id="b0dea-587">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="b0dea-588">键</span><span class="sxs-lookup"><span data-stu-id="b0dea-588">Key</span></span>                   | <span data-ttu-id="b0dea-589">值</span><span class="sxs-lookup"><span data-stu-id="b0dea-589">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="b0dea-590">starship:name</span><span class="sxs-lookup"><span data-stu-id="b0dea-590">starship:name</span></span>         | <span data-ttu-id="b0dea-591">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="b0dea-591">USS Enterprise</span></span>                                    |
| <span data-ttu-id="b0dea-592">starship:registry</span><span class="sxs-lookup"><span data-stu-id="b0dea-592">starship:registry</span></span>     | <span data-ttu-id="b0dea-593">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="b0dea-593">NCC-1701</span></span>                                          |
| <span data-ttu-id="b0dea-594">starship:class</span><span class="sxs-lookup"><span data-stu-id="b0dea-594">starship:class</span></span>        | <span data-ttu-id="b0dea-595">Constitution</span><span class="sxs-lookup"><span data-stu-id="b0dea-595">Constitution</span></span>                                      |
| <span data-ttu-id="b0dea-596">starship:length</span><span class="sxs-lookup"><span data-stu-id="b0dea-596">starship:length</span></span>       | <span data-ttu-id="b0dea-597">304.8</span><span class="sxs-lookup"><span data-stu-id="b0dea-597">304.8</span></span>                                             |
| <span data-ttu-id="b0dea-598">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="b0dea-598">starship:commissioned</span></span> | <span data-ttu-id="b0dea-599">False</span><span class="sxs-lookup"><span data-stu-id="b0dea-599">False</span></span>                                             |
| <span data-ttu-id="b0dea-600">trademark</span><span class="sxs-lookup"><span data-stu-id="b0dea-600">trademark</span></span>             | <span data-ttu-id="b0dea-601">Paramount Pictures Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="b0dea-601">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="b0dea-602">示例应用使用 `starship` 键调用 `GetSection`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-602">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="b0dea-603">`starship` 键值对是独立的。</span><span class="sxs-lookup"><span data-stu-id="b0dea-603">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="b0dea-604">在子节传入 `Starship` 类的实例时调用 `Bind` 方法。</span><span class="sxs-lookup"><span data-stu-id="b0dea-604">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="b0dea-605">绑定实例值后，将实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="b0dea-605">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_starship)]

::: moniker-end

<span data-ttu-id="b0dea-606">`GetSection` 在 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-606">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="b0dea-607">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="b0dea-607">Bind to an object graph</span></span>

<span data-ttu-id="b0dea-608"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="b0dea-608"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="b0dea-609">`Bind` 在 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-609">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b0dea-610">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs)：</span><span class="sxs-lookup"><span data-stu-id="b0dea-610">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-611">示例应用有一个包含配置数据的 tvshow.xml 文件：</span><span class="sxs-lookup"><span data-stu-id="b0dea-611">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-xml[](index/samples/1.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="b0dea-612">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="b0dea-612">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="b0dea-613">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="b0dea-613">The bound instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
viewModel.TvShow = tvShow;
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b0dea-614">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定类型。</span><span class="sxs-lookup"><span data-stu-id="b0dea-614">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="b0dea-615">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="b0dea-615">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="b0dea-616">以下代码显示如何将 `Get<T>` 与前面的示例一起使用，该示例允许将绑定实例直接分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="b0dea-616">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_tvshow)]

::: moniker-end

<span data-ttu-id="b0dea-617"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> 在 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-617"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b0dea-618">ASP.NET Core 1.1 或更高版本中提供了 `Get<T>`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-618">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="b0dea-619">`GetSection` 在 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-619">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="b0dea-620">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="b0dea-620">Bind an array to a class</span></span>

<span data-ttu-id="b0dea-621">示例应用演示了本部分中介绍的概念。</span><span class="sxs-lookup"><span data-stu-id="b0dea-621">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="b0dea-622"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="b0dea-622">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="b0dea-623">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="b0dea-623">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="b0dea-624">\`Bind\`\` 在 [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-624">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="b0dea-625">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="b0dea-625">Binding is provided by convention.</span></span> <span data-ttu-id="b0dea-626">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="b0dea-626">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="b0dea-627">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="b0dea-627">**In-memory array processing**</span></span>

<span data-ttu-id="b0dea-628">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-628">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="b0dea-629">键</span><span class="sxs-lookup"><span data-stu-id="b0dea-629">Key</span></span>             | <span data-ttu-id="b0dea-630">值</span><span class="sxs-lookup"><span data-stu-id="b0dea-630">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b0dea-631">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="b0dea-631">array:entries:0</span></span> | <span data-ttu-id="b0dea-632">value0</span><span class="sxs-lookup"><span data-stu-id="b0dea-632">value0</span></span> |
| <span data-ttu-id="b0dea-633">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="b0dea-633">array:entries:1</span></span> | <span data-ttu-id="b0dea-634">value1</span><span class="sxs-lookup"><span data-stu-id="b0dea-634">value1</span></span> |
| <span data-ttu-id="b0dea-635">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="b0dea-635">array:entries:2</span></span> | <span data-ttu-id="b0dea-636">value2</span><span class="sxs-lookup"><span data-stu-id="b0dea-636">value2</span></span> |
| <span data-ttu-id="b0dea-637">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="b0dea-637">array:entries:4</span></span> | <span data-ttu-id="b0dea-638">value4</span><span class="sxs-lookup"><span data-stu-id="b0dea-638">value4</span></span> |
| <span data-ttu-id="b0dea-639">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="b0dea-639">array:entries:5</span></span> | <span data-ttu-id="b0dea-640">value5</span><span class="sxs-lookup"><span data-stu-id="b0dea-640">value5</span></span> |

<span data-ttu-id="b0dea-641">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="b0dea-641">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=5-12,16)]

::: moniker-end

<span data-ttu-id="b0dea-642">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-642">The array skips a value for index &num;3.</span></span> <span data-ttu-id="b0dea-643">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="b0dea-643">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="b0dea-644">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="b0dea-644">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-645">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="b0dea-645">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="b0dea-646">`GetSection` 在 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) 包中，后者在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-646">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b0dea-647">还可以使用 [ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，从而产生更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="b0dea-647">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Controllers/HomeController.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="b0dea-648">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="b0dea-648">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="b0dea-649">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="b0dea-649">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b0dea-650">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="b0dea-650">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b0dea-651">0</span><span class="sxs-lookup"><span data-stu-id="b0dea-651">0</span></span>                            | <span data-ttu-id="b0dea-652">value0</span><span class="sxs-lookup"><span data-stu-id="b0dea-652">value0</span></span>                       |
| <span data-ttu-id="b0dea-653">1</span><span class="sxs-lookup"><span data-stu-id="b0dea-653">1</span></span>                            | <span data-ttu-id="b0dea-654">value1</span><span class="sxs-lookup"><span data-stu-id="b0dea-654">value1</span></span>                       |
| <span data-ttu-id="b0dea-655">2</span><span class="sxs-lookup"><span data-stu-id="b0dea-655">2</span></span>                            | <span data-ttu-id="b0dea-656">value2</span><span class="sxs-lookup"><span data-stu-id="b0dea-656">value2</span></span>                       |
| <span data-ttu-id="b0dea-657">3</span><span class="sxs-lookup"><span data-stu-id="b0dea-657">3</span></span>                            | <span data-ttu-id="b0dea-658">value4</span><span class="sxs-lookup"><span data-stu-id="b0dea-658">value4</span></span>                       |
| <span data-ttu-id="b0dea-659">4</span><span class="sxs-lookup"><span data-stu-id="b0dea-659">4</span></span>                            | <span data-ttu-id="b0dea-660">value5</span><span class="sxs-lookup"><span data-stu-id="b0dea-660">value5</span></span>                       |

<span data-ttu-id="b0dea-661">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-661">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="b0dea-662">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="b0dea-662">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="b0dea-663">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="b0dea-663">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="b0dea-664">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="b0dea-664">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="b0dea-665">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="b0dea-665">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="b0dea-666">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="b0dea-666">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b0dea-667">在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>中：</span><span class="sxs-lookup"><span data-stu-id="b0dea-667">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b0dea-668">在 `Startup` 构造函数中：</span><span class="sxs-lookup"><span data-stu-id="b0dea-668">In the `Startup` constructor:</span></span>

```csharp
.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

::: moniker-end

<span data-ttu-id="b0dea-669">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-669">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="b0dea-670">键</span><span class="sxs-lookup"><span data-stu-id="b0dea-670">Key</span></span>             | <span data-ttu-id="b0dea-671">值</span><span class="sxs-lookup"><span data-stu-id="b0dea-671">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="b0dea-672">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="b0dea-672">array:entries:3</span></span> | <span data-ttu-id="b0dea-673">value3</span><span class="sxs-lookup"><span data-stu-id="b0dea-673">value3</span></span> |

<span data-ttu-id="b0dea-674">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-674">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="b0dea-675">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="b0dea-675">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="b0dea-676">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="b0dea-676">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="b0dea-677">0</span><span class="sxs-lookup"><span data-stu-id="b0dea-677">0</span></span>                            | <span data-ttu-id="b0dea-678">value0</span><span class="sxs-lookup"><span data-stu-id="b0dea-678">value0</span></span>                       |
| <span data-ttu-id="b0dea-679">1</span><span class="sxs-lookup"><span data-stu-id="b0dea-679">1</span></span>                            | <span data-ttu-id="b0dea-680">value1</span><span class="sxs-lookup"><span data-stu-id="b0dea-680">value1</span></span>                       |
| <span data-ttu-id="b0dea-681">2</span><span class="sxs-lookup"><span data-stu-id="b0dea-681">2</span></span>                            | <span data-ttu-id="b0dea-682">value2</span><span class="sxs-lookup"><span data-stu-id="b0dea-682">value2</span></span>                       |
| <span data-ttu-id="b0dea-683">3</span><span class="sxs-lookup"><span data-stu-id="b0dea-683">3</span></span>                            | <span data-ttu-id="b0dea-684">value3</span><span class="sxs-lookup"><span data-stu-id="b0dea-684">value3</span></span>                       |
| <span data-ttu-id="b0dea-685">4</span><span class="sxs-lookup"><span data-stu-id="b0dea-685">4</span></span>                            | <span data-ttu-id="b0dea-686">value4</span><span class="sxs-lookup"><span data-stu-id="b0dea-686">value4</span></span>                       |
| <span data-ttu-id="b0dea-687">5</span><span class="sxs-lookup"><span data-stu-id="b0dea-687">5</span></span>                            | <span data-ttu-id="b0dea-688">value5</span><span class="sxs-lookup"><span data-stu-id="b0dea-688">value5</span></span>                       |

<span data-ttu-id="b0dea-689">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="b0dea-689">**JSON array processing**</span></span>

<span data-ttu-id="b0dea-690">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="b0dea-690">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="b0dea-691">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="b0dea-691">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-json[](index/samples/1.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="b0dea-692">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="b0dea-692">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="b0dea-693">键</span><span class="sxs-lookup"><span data-stu-id="b0dea-693">Key</span></span>                     | <span data-ttu-id="b0dea-694">“值”</span><span class="sxs-lookup"><span data-stu-id="b0dea-694">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="b0dea-695">json_array:key</span><span class="sxs-lookup"><span data-stu-id="b0dea-695">json_array:key</span></span>          | <span data-ttu-id="b0dea-696">valueA</span><span class="sxs-lookup"><span data-stu-id="b0dea-696">valueA</span></span> |
| <span data-ttu-id="b0dea-697">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="b0dea-697">json_array:subsection:0</span></span> | <span data-ttu-id="b0dea-698">valueB</span><span class="sxs-lookup"><span data-stu-id="b0dea-698">valueB</span></span> |
| <span data-ttu-id="b0dea-699">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="b0dea-699">json_array:subsection:1</span></span> | <span data-ttu-id="b0dea-700">valueC</span><span class="sxs-lookup"><span data-stu-id="b0dea-700">valueC</span></span> |
| <span data-ttu-id="b0dea-701">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="b0dea-701">json_array:subsection:2</span></span> | <span data-ttu-id="b0dea-702">valueD</span><span class="sxs-lookup"><span data-stu-id="b0dea-702">valueD</span></span> |

<span data-ttu-id="b0dea-703">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="b0dea-703">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-704">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-704">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="b0dea-705">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="b0dea-705">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="b0dea-706">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="b0dea-706">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="b0dea-707">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="b0dea-707">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="b0dea-708">0</span><span class="sxs-lookup"><span data-stu-id="b0dea-708">0</span></span>                                   | <span data-ttu-id="b0dea-709">valueB</span><span class="sxs-lookup"><span data-stu-id="b0dea-709">valueB</span></span>                              |
| <span data-ttu-id="b0dea-710">1</span><span class="sxs-lookup"><span data-stu-id="b0dea-710">1</span></span>                                   | <span data-ttu-id="b0dea-711">valueC</span><span class="sxs-lookup"><span data-stu-id="b0dea-711">valueC</span></span>                              |
| <span data-ttu-id="b0dea-712">2</span><span class="sxs-lookup"><span data-stu-id="b0dea-712">2</span></span>                                   | <span data-ttu-id="b0dea-713">valueD</span><span class="sxs-lookup"><span data-stu-id="b0dea-713">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="b0dea-714">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="b0dea-714">Custom configuration provider</span></span>

<span data-ttu-id="b0dea-715">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-715">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="b0dea-716">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="b0dea-716">The provider has the following characteristics:</span></span>

* <span data-ttu-id="b0dea-717">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="b0dea-717">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="b0dea-718">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="b0dea-718">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="b0dea-719">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="b0dea-719">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="b0dea-720">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="b0dea-720">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="b0dea-721">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="b0dea-721">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="b0dea-722">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="b0dea-722">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="b0dea-723">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="b0dea-723">*Models/EFConfigurationValue.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-724">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-724">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="b0dea-725">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="b0dea-725">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-726">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="b0dea-726">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="b0dea-727">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="b0dea-727">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-728">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="b0dea-728">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="b0dea-729">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="b0dea-729">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="b0dea-730">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="b0dea-730">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-731">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="b0dea-731">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="b0dea-732">Extensions/EntityFrameworkExtensions.cs：</span><span class="sxs-lookup"><span data-stu-id="b0dea-732">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="b0dea-733">下面的代码演示如何在 Program.cs 中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="b0dea-733">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/ConfigurationSample/Startup.cs?name=snippet_Startup&highlight=24)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="b0dea-734">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="b0dea-734">Access configuration during startup</span></span>

<span data-ttu-id="b0dea-735">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="b0dea-735">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b0dea-736">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="b0dea-736">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="b0dea-737">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="b0dea-737">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="b0dea-738">在 Razor Pages 页或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="b0dea-738">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="b0dea-739">若要访问 Razor Pages 页或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration 命名空间](xref:Microsoft.Extensions.Configuration)添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入页面或视图。</span><span class="sxs-lookup"><span data-stu-id="b0dea-739">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="b0dea-740">在 Razor 页面页中：</span><span class="sxs-lookup"><span data-stu-id="b0dea-740">In a Razor Pages page:</span></span>

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

<span data-ttu-id="b0dea-741">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="b0dea-741">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="b0dea-742">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="b0dea-742">Add configuration from an external assembly</span></span>

<span data-ttu-id="b0dea-743">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="b0dea-743">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="b0dea-744">有关更多信息，请参见<xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="b0dea-744">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0dea-745">其他资源</span><span class="sxs-lookup"><span data-stu-id="b0dea-745">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="b0dea-746">深入了解 Microsoft 配置</span><span class="sxs-lookup"><span data-stu-id="b0dea-746">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)
