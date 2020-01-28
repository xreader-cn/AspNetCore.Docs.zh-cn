---
title: ASP.NET Core 中的配置
author: guardrex
description: 理解如何使用配置 API 配置 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 141ae5cda7672159032013cbda1ef4bfa7c142dd
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726979"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="497d6-103">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="497d6-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="497d6-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="497d6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="497d6-105">ASP.NET Core 中的应用配置基于配置提供程序  建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="497d6-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="497d6-106">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="497d6-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="497d6-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="497d6-107">Azure Key Vault</span></span>
* <span data-ttu-id="497d6-108">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="497d6-108">Azure App Configuration</span></span>
* <span data-ttu-id="497d6-109">命令行参数</span><span class="sxs-lookup"><span data-stu-id="497d6-109">Command-line arguments</span></span>
* <span data-ttu-id="497d6-110">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="497d6-111">目录文件</span><span class="sxs-lookup"><span data-stu-id="497d6-111">Directory files</span></span>
* <span data-ttu-id="497d6-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="497d6-112">Environment variables</span></span>
* <span data-ttu-id="497d6-113">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="497d6-113">In-memory .NET objects</span></span>
* <span data-ttu-id="497d6-114">设置文件</span><span class="sxs-lookup"><span data-stu-id="497d6-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="497d6-115">框架隐式包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="497d6-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="497d6-116">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="497d6-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="497d6-117">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="497d6-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="497d6-118">选项模式  是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="497d6-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="497d6-119">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="497d6-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="497d6-120">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="497d6-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="497d6-121">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="497d6-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="497d6-122">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="497d6-122">Host versus app configuration</span></span>

<span data-ttu-id="497d6-123">在配置并启动应用之前，配置并启动主机  。</span><span class="sxs-lookup"><span data-stu-id="497d6-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="497d6-124">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="497d6-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="497d6-125">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="497d6-126">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="497d6-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="497d6-127">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="497d6-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="497d6-128">默认配置</span><span class="sxs-lookup"><span data-stu-id="497d6-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="497d6-129">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="497d6-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="497d6-130">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="497d6-131">以下内容适用于使用[通用主机](xref:fundamentals/host/generic-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="497d6-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="497d6-132">有关使用 [Web 主机](xref:fundamentals/host/web-host)时默认配置的详细信息，请参阅[本主题的 ASP.NET Core 2.2 版本](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="497d6-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="497d6-133">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="497d6-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="497d6-134">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `DOTNET_`（例如，`DOTNET_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="497d6-135">在配置键值对加载后，前缀 (`DOTNET_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="497d6-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="497d6-136">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="497d6-137">已建立 Web 主机默认配置 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="497d6-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="497d6-138">Kestrel 用作 Web 服务器，并使用应用的配置提供程序对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="497d6-139">添加主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="497d6-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="497d6-140">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则添加转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="497d6-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="497d6-141">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="497d6-141">Enable IIS integration.</span></span>
* <span data-ttu-id="497d6-142">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="497d6-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="497d6-143">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json  提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="497d6-144">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json  提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="497d6-145">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="497d6-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="497d6-146">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="497d6-147">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="497d6-148">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="497d6-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="497d6-149">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="497d6-150">以下内容适用于使用 [Web 主机](xref:fundamentals/host/web-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="497d6-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="497d6-151">有关使用[通用主机](xref:fundamentals/host/generic-host)时默认配置的详细信息，请参阅[本主题的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="497d6-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="497d6-152">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="497d6-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="497d6-153">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="497d6-154">在配置键值对加载后，前缀 (`ASPNETCORE_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="497d6-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="497d6-155">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="497d6-156">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="497d6-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="497d6-157">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json  提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="497d6-158">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json  提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="497d6-159">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="497d6-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="497d6-160">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="497d6-161">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="497d6-162">安全性</span><span class="sxs-lookup"><span data-stu-id="497d6-162">Security</span></span>

<span data-ttu-id="497d6-163">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="497d6-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="497d6-164">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="497d6-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="497d6-165">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="497d6-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="497d6-166">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="497d6-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="497d6-167">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="497d6-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="497d6-168"><xref:security/app-secrets> &ndash; 包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="497d6-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="497d6-169">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="497d6-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="497d6-170">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="497d6-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="497d6-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="497d6-172">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="497d6-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="497d6-173">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="497d6-173">Hierarchical configuration data</span></span>

<span data-ttu-id="497d6-174">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="497d6-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="497d6-175">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="497d6-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="497d6-176">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="497d6-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="497d6-177">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="497d6-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="497d6-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-178">section0:key0</span></span>
* <span data-ttu-id="497d6-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-179">section0:key1</span></span>
* <span data-ttu-id="497d6-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-180">section1:key0</span></span>
* <span data-ttu-id="497d6-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-181">section1:key1</span></span>

<span data-ttu-id="497d6-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="497d6-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="497d6-183">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="497d6-184">约定</span><span class="sxs-lookup"><span data-stu-id="497d6-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="497d6-185">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-185">Sources and providers</span></span>

<span data-ttu-id="497d6-186">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="497d6-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="497d6-187">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="497d6-188">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="497d6-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="497d6-189">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="497d6-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="497d6-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 来获取以下类的配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="497d6-191">在下面的示例中，使用 `_config` 字段来访问配置值：</span><span class="sxs-lookup"><span data-stu-id="497d6-191">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="497d6-192">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="497d6-192">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="497d6-193">键</span><span class="sxs-lookup"><span data-stu-id="497d6-193">Keys</span></span>

<span data-ttu-id="497d6-194">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="497d6-194">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="497d6-195">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="497d6-195">Keys are case-insensitive.</span></span> <span data-ttu-id="497d6-196">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="497d6-196">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="497d6-197">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="497d6-197">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="497d6-198">分层键</span><span class="sxs-lookup"><span data-stu-id="497d6-198">Hierarchical keys</span></span>
  * <span data-ttu-id="497d6-199">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="497d6-199">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="497d6-200">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="497d6-200">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="497d6-201">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="497d6-201">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="497d6-202">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="497d6-202">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="497d6-203">将机密加载到应用的配置中时，用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="497d6-203">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="497d6-204"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="497d6-204">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="497d6-205">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="497d6-205">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="497d6-206">值</span><span class="sxs-lookup"><span data-stu-id="497d6-206">Values</span></span>

<span data-ttu-id="497d6-207">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="497d6-207">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="497d6-208">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="497d6-208">Values are strings.</span></span>
* <span data-ttu-id="497d6-209">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="497d6-209">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="497d6-210">提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-210">Providers</span></span>

<span data-ttu-id="497d6-211">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-211">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="497d6-212">提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-212">Provider</span></span> | <span data-ttu-id="497d6-213">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="497d6-213">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="497d6-214">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="497d6-214">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="497d6-215">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="497d6-215">Azure Key Vault</span></span> |
| <span data-ttu-id="497d6-216">[Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="497d6-216">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="497d6-217">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="497d6-217">Azure App Configuration</span></span> |
| [<span data-ttu-id="497d6-218">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-218">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="497d6-219">命令行参数</span><span class="sxs-lookup"><span data-stu-id="497d6-219">Command-line parameters</span></span> |
| [<span data-ttu-id="497d6-220">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-220">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="497d6-221">自定义源</span><span class="sxs-lookup"><span data-stu-id="497d6-221">Custom source</span></span> |
| [<span data-ttu-id="497d6-222">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-222">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="497d6-223">环境变量</span><span class="sxs-lookup"><span data-stu-id="497d6-223">Environment variables</span></span> |
| [<span data-ttu-id="497d6-224">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-224">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="497d6-225">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="497d6-225">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="497d6-226">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-226">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="497d6-227">目录文件</span><span class="sxs-lookup"><span data-stu-id="497d6-227">Directory files</span></span> |
| [<span data-ttu-id="497d6-228">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-228">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="497d6-229">内存中集合</span><span class="sxs-lookup"><span data-stu-id="497d6-229">In-memory collections</span></span> |
| <span data-ttu-id="497d6-230">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="497d6-230">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="497d6-231">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="497d6-231">File in the user profile directory</span></span> |

<span data-ttu-id="497d6-232">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="497d6-232">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="497d6-233">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="497d6-233">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="497d6-234">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="497d6-234">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="497d6-235">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="497d6-235">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="497d6-236">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境）  </span><span class="sxs-lookup"><span data-stu-id="497d6-236">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="497d6-237">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="497d6-237">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="497d6-238">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="497d6-238">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="497d6-239">环境变量</span><span class="sxs-lookup"><span data-stu-id="497d6-239">Environment variables</span></span>
1. <span data-ttu-id="497d6-240">命令行参数</span><span class="sxs-lookup"><span data-stu-id="497d6-240">Command-line arguments</span></span>

<span data-ttu-id="497d6-241">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-241">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="497d6-242">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="497d6-242">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="497d6-243">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-243">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="497d6-244">用 ConfigureHostConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="497d6-244">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="497d6-245">若要配置主机生成器，请调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 并提供配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-245">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="497d6-246">用 `ConfigureHostConfiguration` 初始化 <xref:Microsoft.Extensions.Hosting.IHostEnvironment>，以便稍后在生成过程中使用。</span><span class="sxs-lookup"><span data-stu-id="497d6-246">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="497d6-247">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="497d6-247">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="497d6-248">用 UseConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="497d6-248">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="497d6-249">若要配置主机生成器，请使用配置在主机生成器上调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="497d6-249">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

::: moniker-end

## <a name="configureappconfiguration"></a><span data-ttu-id="497d6-250">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="497d6-250">ConfigureAppConfiguration</span></span>

<span data-ttu-id="497d6-251">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="497d6-251">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="497d6-252">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="497d6-252">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="497d6-253">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="497d6-253">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="497d6-254">删除 CreateDefaultBuilder 添加的提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-254">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="497d6-255">要删除 `CreateDefaultBuilder` 添加的提供程序，请先对 [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) 调用 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)：</span><span class="sxs-lookup"><span data-stu-id="497d6-255">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="497d6-256">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="497d6-256">Consume configuration during app startup</span></span>

<span data-ttu-id="497d6-257">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="497d6-257">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="497d6-258">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-258">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="497d6-259">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-259">Command-line Configuration Provider</span></span>

<span data-ttu-id="497d6-260"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-260">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="497d6-261">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-261">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="497d6-262">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="497d6-262">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="497d6-263">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-263">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="497d6-264">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="497d6-264">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="497d6-265">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="497d6-265">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="497d6-266">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="497d6-266">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="497d6-267">环境变量。</span><span class="sxs-lookup"><span data-stu-id="497d6-267">Environment variables.</span></span>

<span data-ttu-id="497d6-268">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-268">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="497d6-269">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-269">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="497d6-270">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="497d6-270">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="497d6-271">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="497d6-271">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="497d6-272">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="497d6-272">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="497d6-273">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="497d6-273">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="497d6-274">**示例**</span><span class="sxs-lookup"><span data-stu-id="497d6-274">**Example**</span></span>

<span data-ttu-id="497d6-275">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="497d6-275">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="497d6-276">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="497d6-276">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="497d6-277">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="497d6-277">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="497d6-278">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="497d6-278">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="497d6-279">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="497d6-279">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="497d6-280">自变量</span><span class="sxs-lookup"><span data-stu-id="497d6-280">Arguments</span></span>

<span data-ttu-id="497d6-281">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="497d6-281">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="497d6-282">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="497d6-282">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="497d6-283">键前缀</span><span class="sxs-lookup"><span data-stu-id="497d6-283">Key prefix</span></span>               | <span data-ttu-id="497d6-284">示例</span><span class="sxs-lookup"><span data-stu-id="497d6-284">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="497d6-285">无前缀</span><span class="sxs-lookup"><span data-stu-id="497d6-285">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="497d6-286">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="497d6-286">Two dashes (`--`)</span></span>        | <span data-ttu-id="497d6-287">`--CommandLineKey2=value2`，`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="497d6-287">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="497d6-288">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="497d6-288">Forward slash (`/`)</span></span>      | <span data-ttu-id="497d6-289">`/CommandLineKey3=value3`，`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="497d6-289">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="497d6-290">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="497d6-290">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="497d6-291">示例命令：</span><span class="sxs-lookup"><span data-stu-id="497d6-291">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="497d6-292">交换映射</span><span class="sxs-lookup"><span data-stu-id="497d6-292">Switch mappings</span></span>

<span data-ttu-id="497d6-293">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="497d6-293">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="497d6-294">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，需要为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="497d6-294">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="497d6-295">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="497d6-295">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="497d6-296">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-296">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="497d6-297">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="497d6-297">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="497d6-298">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="497d6-298">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="497d6-299">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="497d6-299">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="497d6-300">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="497d6-300">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="497d6-301">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="497d6-301">Create a switch mappings dictionary.</span></span> <span data-ttu-id="497d6-302">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="497d6-302">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="497d6-303">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="497d6-303">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="497d6-304">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="497d6-304">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="497d6-305">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="497d6-305">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="497d6-306">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="497d6-306">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="497d6-307">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="497d6-307">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="497d6-308">键</span><span class="sxs-lookup"><span data-stu-id="497d6-308">Key</span></span>       | <span data-ttu-id="497d6-309">“值”</span><span class="sxs-lookup"><span data-stu-id="497d6-309">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="497d6-310">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="497d6-310">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="497d6-311">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="497d6-311">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="497d6-312">键</span><span class="sxs-lookup"><span data-stu-id="497d6-312">Key</span></span>               | <span data-ttu-id="497d6-313">“值”</span><span class="sxs-lookup"><span data-stu-id="497d6-313">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="497d6-314">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-314">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="497d6-315"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-315">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="497d6-316">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-316">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="497d6-317">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="497d6-317">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="497d6-318">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="497d6-318">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="497d6-319">如果使用[通用主机](xref:fundamentals/host/generic-host)初始化新的主机生成器，且调用了 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="497d6-319">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="497d6-320">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-320">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="497d6-321">如果使用 [Web 主机](xref:fundamentals/host/web-host)初始化新的主机生成器，且调用 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="497d6-321">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="497d6-322">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-322">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="497d6-323">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="497d6-323">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="497d6-324">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="497d6-324">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="497d6-325">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="497d6-325">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="497d6-326">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="497d6-326">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="497d6-327">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="497d6-327">Command-line arguments.</span></span>

<span data-ttu-id="497d6-328">环境变量配置提供程序是在配置已根据用户机密和 appsettings  文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="497d6-328">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="497d6-329">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-329">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="497d6-330">要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`：</span><span class="sxs-lookup"><span data-stu-id="497d6-330">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="497d6-331">最后调用 `AddEnvironmentVariables`让带给定前缀的环境变量可替代其他提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="497d6-331">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="497d6-332">**示例**</span><span class="sxs-lookup"><span data-stu-id="497d6-332">**Example**</span></span>

<span data-ttu-id="497d6-333">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="497d6-333">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="497d6-334">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="497d6-334">Run the sample app.</span></span> <span data-ttu-id="497d6-335">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="497d6-335">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="497d6-336">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="497d6-336">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="497d6-337">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="497d6-337">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="497d6-338">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="497d6-338">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="497d6-339">请参阅示例应用的“Pages/Index.cshtml.cs”文件  。</span><span class="sxs-lookup"><span data-stu-id="497d6-339">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="497d6-340">要公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容  ：</span><span class="sxs-lookup"><span data-stu-id="497d6-340">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="497d6-341">前缀</span><span class="sxs-lookup"><span data-stu-id="497d6-341">Prefixes</span></span>

<span data-ttu-id="497d6-342">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="497d6-342">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="497d6-343">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="497d6-343">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="497d6-344">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="497d6-344">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="497d6-345">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="497d6-345">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="497d6-346">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-346">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="497d6-347">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="497d6-347">**Connection string prefixes**</span></span>

<span data-ttu-id="497d6-348">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="497d6-348">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="497d6-349">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="497d6-349">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="497d6-350">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="497d6-350">Connection string prefix</span></span> | <span data-ttu-id="497d6-351">提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-351">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="497d6-352">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-352">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="497d6-353">MySQL</span><span class="sxs-lookup"><span data-stu-id="497d6-353">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="497d6-354">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="497d6-354">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="497d6-355">SQL Server</span><span class="sxs-lookup"><span data-stu-id="497d6-355">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="497d6-356">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="497d6-356">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="497d6-357">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="497d6-357">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="497d6-358">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="497d6-358">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="497d6-359">环境变量键</span><span class="sxs-lookup"><span data-stu-id="497d6-359">Environment variable key</span></span> | <span data-ttu-id="497d6-360">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="497d6-360">Converted configuration key</span></span> | <span data-ttu-id="497d6-361">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="497d6-361">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="497d6-362">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="497d6-362">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="497d6-363">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="497d6-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="497d6-364">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="497d6-364">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="497d6-365">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="497d6-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="497d6-366">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="497d6-366">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="497d6-367">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="497d6-367">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="497d6-368">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="497d6-368">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="497d6-369">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-369">File Configuration Provider</span></span>

<span data-ttu-id="497d6-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="497d6-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="497d6-371">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="497d6-371">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="497d6-372">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-372">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="497d6-373">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-373">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="497d6-374">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-374">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="497d6-375">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-375">INI Configuration Provider</span></span>

<span data-ttu-id="497d6-376"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-376">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="497d6-377">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-377">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="497d6-378">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="497d6-378">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="497d6-379">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="497d6-379">Overloads permit specifying:</span></span>

* <span data-ttu-id="497d6-380">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="497d6-380">Whether the file is optional.</span></span>
* <span data-ttu-id="497d6-381">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-381">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="497d6-382"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="497d6-382">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="497d6-383">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-383">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="497d6-384">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="497d6-384">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="497d6-385">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="497d6-385">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="497d6-386">section0:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-386">section0:key0</span></span>
* <span data-ttu-id="497d6-387">section0:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-387">section0:key1</span></span>
* <span data-ttu-id="497d6-388">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="497d6-388">section1:subsection:key</span></span>
* <span data-ttu-id="497d6-389">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="497d6-389">section2:subsection0:key</span></span>
* <span data-ttu-id="497d6-390">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="497d6-390">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="497d6-391">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-391">JSON Configuration Provider</span></span>

<span data-ttu-id="497d6-392"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-392">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="497d6-393">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-393">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="497d6-394">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="497d6-394">Overloads permit specifying:</span></span>

* <span data-ttu-id="497d6-395">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="497d6-395">Whether the file is optional.</span></span>
* <span data-ttu-id="497d6-396">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-396">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="497d6-397"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="497d6-397">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="497d6-398">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，会自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="497d6-398">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="497d6-399">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-399">The method is called to load configuration from:</span></span>

* <span data-ttu-id="497d6-400">appsettings.json &ndash; 首先读取此文件  。</span><span class="sxs-lookup"><span data-stu-id="497d6-400">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="497d6-401">该文件的环境版本可以替代 appsettings.json  文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="497d6-401">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="497d6-402">appsettings.{Environment}.json &ndash; 根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载文件的环境版本  。</span><span class="sxs-lookup"><span data-stu-id="497d6-402">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="497d6-403">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-403">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="497d6-404">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="497d6-404">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="497d6-405">环境变量。</span><span class="sxs-lookup"><span data-stu-id="497d6-405">Environment variables.</span></span>
* <span data-ttu-id="497d6-406">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="497d6-406">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="497d6-407">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="497d6-407">Command-line arguments.</span></span>

<span data-ttu-id="497d6-408">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-408">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="497d6-409">因此，用户机密、环境变量和命令行参数会替代由 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-409">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="497d6-410">构建主机时调用 `ConfigureAppConfiguration` 以指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置：  </span><span class="sxs-lookup"><span data-stu-id="497d6-410">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="497d6-411">**示例**</span><span class="sxs-lookup"><span data-stu-id="497d6-411">**Example**</span></span>

<span data-ttu-id="497d6-412">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用：</span><span class="sxs-lookup"><span data-stu-id="497d6-412">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="497d6-413">第一次调用 `AddJsonFile` 会从 appsettings 加载配置  ：</span><span class="sxs-lookup"><span data-stu-id="497d6-413">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="497d6-414">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置  。</span><span class="sxs-lookup"><span data-stu-id="497d6-414">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="497d6-415">对于示例应用中的 appsettings.Development.json，将加载以下文件  ：</span><span class="sxs-lookup"><span data-stu-id="497d6-415">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="497d6-416">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="497d6-416">Run the sample app.</span></span> <span data-ttu-id="497d6-417">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="497d6-417">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="497d6-418">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="497d6-418">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="497d6-419">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="497d6-419">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="497d6-420">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="497d6-420">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="497d6-421">打开 Properties/launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="497d6-421">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="497d6-422">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="497d6-422">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="497d6-423">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="497d6-423">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="497d6-424">appsettings.Development.json 中的设置不再替代 appsettings.json 中的设置   。</span><span class="sxs-lookup"><span data-stu-id="497d6-424">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="497d6-425">键 `Logging:LogLevel:Default` 的日志级别为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="497d6-425">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="497d6-426">第一次调用 `AddJsonFile` 会从 appsettings 加载配置  ：</span><span class="sxs-lookup"><span data-stu-id="497d6-426">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="497d6-427">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置  。</span><span class="sxs-lookup"><span data-stu-id="497d6-427">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="497d6-428">对于示例应用中的 appsettings.Development.json，将加载以下文件  ：</span><span class="sxs-lookup"><span data-stu-id="497d6-428">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="497d6-429">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="497d6-429">Run the sample app.</span></span> <span data-ttu-id="497d6-430">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="497d6-430">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="497d6-431">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="497d6-431">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="497d6-432">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="497d6-432">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="497d6-433">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="497d6-433">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="497d6-434">打开 Properties/launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="497d6-434">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="497d6-435">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="497d6-435">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="497d6-436">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="497d6-436">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="497d6-437">appsettings.Development.json 中的设置不再替代 appsettings.json 中的设置   。</span><span class="sxs-lookup"><span data-stu-id="497d6-437">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="497d6-438">键 `Logging:LogLevel:Default` 的日志级别为 `Warning`。</span><span class="sxs-lookup"><span data-stu-id="497d6-438">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="497d6-439">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-439">XML Configuration Provider</span></span>

<span data-ttu-id="497d6-440"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-440">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="497d6-441">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-441">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="497d6-442">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="497d6-442">Overloads permit specifying:</span></span>

* <span data-ttu-id="497d6-443">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="497d6-443">Whether the file is optional.</span></span>
* <span data-ttu-id="497d6-444">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-444">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="497d6-445"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="497d6-445">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="497d6-446">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="497d6-446">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="497d6-447">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="497d6-447">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="497d6-448">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-448">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="497d6-449">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="497d6-449">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="497d6-450">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="497d6-450">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="497d6-451">section0:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-451">section0:key0</span></span>
* <span data-ttu-id="497d6-452">section0:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-452">section0:key1</span></span>
* <span data-ttu-id="497d6-453">section1:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-453">section1:key0</span></span>
* <span data-ttu-id="497d6-454">section1:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-454">section1:key1</span></span>

<span data-ttu-id="497d6-455">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="497d6-455">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="497d6-456">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="497d6-456">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="497d6-457">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-457">section:section0:key:key0</span></span>
* <span data-ttu-id="497d6-458">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-458">section:section0:key:key1</span></span>
* <span data-ttu-id="497d6-459">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-459">section:section1:key:key0</span></span>
* <span data-ttu-id="497d6-460">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-460">section:section1:key:key1</span></span>

<span data-ttu-id="497d6-461">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="497d6-461">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="497d6-462">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="497d6-462">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="497d6-463">key:attribute</span><span class="sxs-lookup"><span data-stu-id="497d6-463">key:attribute</span></span>
* <span data-ttu-id="497d6-464">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="497d6-464">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="497d6-465">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-465">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="497d6-466"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="497d6-466">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="497d6-467">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="497d6-467">The key is the file name.</span></span> <span data-ttu-id="497d6-468">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="497d6-468">The value contains the file's contents.</span></span> <span data-ttu-id="497d6-469">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="497d6-469">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="497d6-470">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-470">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="497d6-471">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="497d6-471">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="497d6-472">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="497d6-472">Overloads permit specifying:</span></span>

* <span data-ttu-id="497d6-473">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="497d6-473">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="497d6-474">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="497d6-474">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="497d6-475">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="497d6-475">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="497d6-476">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="497d6-476">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="497d6-477">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-477">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="497d6-478">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-478">Memory Configuration Provider</span></span>

<span data-ttu-id="497d6-479"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="497d6-479">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="497d6-480">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-480">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="497d6-481">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-481">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="497d6-482">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-482">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="497d6-483">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="497d6-483">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="497d6-484">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-484">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="497d6-485">GetValue</span><span class="sxs-lookup"><span data-stu-id="497d6-485">GetValue</span></span>

<span data-ttu-id="497d6-486">[ConfigurationBinder.GetValueT\<>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将其转换为指定的非集合类型。</span><span class="sxs-lookup"><span data-stu-id="497d6-486">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="497d6-487">重载接受默认值。</span><span class="sxs-lookup"><span data-stu-id="497d6-487">An overload accepts a default value.</span></span>

<span data-ttu-id="497d6-488">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="497d6-488">The following example:</span></span>

* <span data-ttu-id="497d6-489">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="497d6-489">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="497d6-490">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="497d6-490">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="497d6-491">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="497d6-491">Types the value as an `int`.</span></span>
* <span data-ttu-id="497d6-492">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="497d6-492">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="497d6-493">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="497d6-493">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="497d6-494">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="497d6-494">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="497d6-495">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="497d6-495">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="497d6-496">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="497d6-496">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="497d6-497">section0:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-497">section0:key0</span></span>
* <span data-ttu-id="497d6-498">section0:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-498">section0:key1</span></span>
* <span data-ttu-id="497d6-499">section1:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-499">section1:key0</span></span>
* <span data-ttu-id="497d6-500">section1:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-500">section1:key1</span></span>
* <span data-ttu-id="497d6-501">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-501">section2:subsection0:key0</span></span>
* <span data-ttu-id="497d6-502">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-502">section2:subsection0:key1</span></span>
* <span data-ttu-id="497d6-503">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="497d6-503">section2:subsection1:key0</span></span>
* <span data-ttu-id="497d6-504">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="497d6-504">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="497d6-505">GetSection</span><span class="sxs-lookup"><span data-stu-id="497d6-505">GetSection</span></span>

<span data-ttu-id="497d6-506">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="497d6-506">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="497d6-507">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="497d6-507">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="497d6-508">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="497d6-508">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="497d6-509">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="497d6-509">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="497d6-510">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="497d6-510">`GetSection` never returns `null`.</span></span> <span data-ttu-id="497d6-511">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="497d6-511">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="497d6-512">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="497d6-512">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="497d6-513">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="497d6-513">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="497d6-514">GetChildren</span><span class="sxs-lookup"><span data-stu-id="497d6-514">GetChildren</span></span>

<span data-ttu-id="497d6-515">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="497d6-515">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="497d6-516">存在</span><span class="sxs-lookup"><span data-stu-id="497d6-516">Exists</span></span>

<span data-ttu-id="497d6-517">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="497d6-517">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="497d6-518">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="497d6-518">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="497d6-519">绑定至类</span><span class="sxs-lookup"><span data-stu-id="497d6-519">Bind to a class</span></span>

<span data-ttu-id="497d6-520">可以使用选项模式  将配置绑定到表示相关设置组的类。</span><span class="sxs-lookup"><span data-stu-id="497d6-520">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="497d6-521">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="497d6-521">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="497d6-522">配置值作为字符串返回，但调用 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以构造 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 对象。</span><span class="sxs-lookup"><span data-stu-id="497d6-522">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="497d6-523">绑定器将值绑定到所提供类型的所有公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="497d6-523">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="497d6-524">不会绑定字段  。</span><span class="sxs-lookup"><span data-stu-id="497d6-524">Fields are **not** bound.</span></span>

<span data-ttu-id="497d6-525">示例应用包含 `Starship` 模型 (Models/Starship.cs  )：</span><span class="sxs-lookup"><span data-stu-id="497d6-525">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="497d6-526">当示例应用使用 JSON 配置提供程序加载配置时，starship.json  文件的 `starship` 节会创建配置：</span><span class="sxs-lookup"><span data-stu-id="497d6-526">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="497d6-527">创建以下配置键值对：</span><span class="sxs-lookup"><span data-stu-id="497d6-527">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="497d6-528">键</span><span class="sxs-lookup"><span data-stu-id="497d6-528">Key</span></span>                   | <span data-ttu-id="497d6-529">“值”</span><span class="sxs-lookup"><span data-stu-id="497d6-529">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="497d6-530">starship:name</span><span class="sxs-lookup"><span data-stu-id="497d6-530">starship:name</span></span>         | <span data-ttu-id="497d6-531">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="497d6-531">USS Enterprise</span></span>                                    |
| <span data-ttu-id="497d6-532">starship:registry</span><span class="sxs-lookup"><span data-stu-id="497d6-532">starship:registry</span></span>     | <span data-ttu-id="497d6-533">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="497d6-533">NCC-1701</span></span>                                          |
| <span data-ttu-id="497d6-534">starship:class</span><span class="sxs-lookup"><span data-stu-id="497d6-534">starship:class</span></span>        | <span data-ttu-id="497d6-535">Constitution</span><span class="sxs-lookup"><span data-stu-id="497d6-535">Constitution</span></span>                                      |
| <span data-ttu-id="497d6-536">starship:length</span><span class="sxs-lookup"><span data-stu-id="497d6-536">starship:length</span></span>       | <span data-ttu-id="497d6-537">304.8</span><span class="sxs-lookup"><span data-stu-id="497d6-537">304.8</span></span>                                             |
| <span data-ttu-id="497d6-538">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="497d6-538">starship:commissioned</span></span> | <span data-ttu-id="497d6-539">False</span><span class="sxs-lookup"><span data-stu-id="497d6-539">False</span></span>                                             |
| <span data-ttu-id="497d6-540">trademark</span><span class="sxs-lookup"><span data-stu-id="497d6-540">trademark</span></span>             | <span data-ttu-id="497d6-541">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="497d6-541">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="497d6-542">示例应用使用 `starship` 键调用 `GetSection`。</span><span class="sxs-lookup"><span data-stu-id="497d6-542">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="497d6-543">`starship` 键值对是独立的。</span><span class="sxs-lookup"><span data-stu-id="497d6-543">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="497d6-544">在子节传入 `Starship` 类的实例时调用 `Bind` 方法。</span><span class="sxs-lookup"><span data-stu-id="497d6-544">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="497d6-545">绑定实例值后，将实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="497d6-545">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="497d6-546">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="497d6-546">Bind to an object graph</span></span>

<span data-ttu-id="497d6-547"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="497d6-547"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="497d6-548">与绑定简单对象一样，只绑定公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="497d6-548">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="497d6-549">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs  )：</span><span class="sxs-lookup"><span data-stu-id="497d6-549">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="497d6-550">示例应用有一个包含配置数据的 tvshow.xml  文件：</span><span class="sxs-lookup"><span data-stu-id="497d6-550">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="497d6-551">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="497d6-551">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="497d6-552">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="497d6-552">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="497d6-553">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定类型。</span><span class="sxs-lookup"><span data-stu-id="497d6-553">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="497d6-554">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="497d6-554">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="497d6-555">以下代码显示如何将 `Get<T>` 与前面的示例一起使用，该示例允许将绑定实例直接分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="497d6-555">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="497d6-556">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="497d6-556">Bind an array to a class</span></span>

<span data-ttu-id="497d6-557">示例应用演示了本部分中介绍的概念。 </span><span class="sxs-lookup"><span data-stu-id="497d6-557">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="497d6-558"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="497d6-558">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="497d6-559">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="497d6-559">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="497d6-560">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="497d6-560">Binding is provided by convention.</span></span> <span data-ttu-id="497d6-561">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="497d6-561">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="497d6-562">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="497d6-562">**In-memory array processing**</span></span>

<span data-ttu-id="497d6-563">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="497d6-563">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="497d6-564">键</span><span class="sxs-lookup"><span data-stu-id="497d6-564">Key</span></span>             | <span data-ttu-id="497d6-565">“值”</span><span class="sxs-lookup"><span data-stu-id="497d6-565">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="497d6-566">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="497d6-566">array:entries:0</span></span> | <span data-ttu-id="497d6-567">value0</span><span class="sxs-lookup"><span data-stu-id="497d6-567">value0</span></span> |
| <span data-ttu-id="497d6-568">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="497d6-568">array:entries:1</span></span> | <span data-ttu-id="497d6-569">value1</span><span class="sxs-lookup"><span data-stu-id="497d6-569">value1</span></span> |
| <span data-ttu-id="497d6-570">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="497d6-570">array:entries:2</span></span> | <span data-ttu-id="497d6-571">value2</span><span class="sxs-lookup"><span data-stu-id="497d6-571">value2</span></span> |
| <span data-ttu-id="497d6-572">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="497d6-572">array:entries:4</span></span> | <span data-ttu-id="497d6-573">value4</span><span class="sxs-lookup"><span data-stu-id="497d6-573">value4</span></span> |
| <span data-ttu-id="497d6-574">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="497d6-574">array:entries:5</span></span> | <span data-ttu-id="497d6-575">value5</span><span class="sxs-lookup"><span data-stu-id="497d6-575">value5</span></span> |

<span data-ttu-id="497d6-576">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="497d6-576">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="497d6-577">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="497d6-577">The array skips a value for index &num;3.</span></span> <span data-ttu-id="497d6-578">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="497d6-578">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="497d6-579">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="497d6-579">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="497d6-580">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="497d6-580">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="497d6-581">还可以使用 [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="497d6-581">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="497d6-582">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="497d6-582">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="497d6-583">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="497d6-583">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="497d6-584">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="497d6-584">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="497d6-585">0</span><span class="sxs-lookup"><span data-stu-id="497d6-585">0</span></span>                            | <span data-ttu-id="497d6-586">value0</span><span class="sxs-lookup"><span data-stu-id="497d6-586">value0</span></span>                       |
| <span data-ttu-id="497d6-587">1</span><span class="sxs-lookup"><span data-stu-id="497d6-587">1</span></span>                            | <span data-ttu-id="497d6-588">value1</span><span class="sxs-lookup"><span data-stu-id="497d6-588">value1</span></span>                       |
| <span data-ttu-id="497d6-589">2</span><span class="sxs-lookup"><span data-stu-id="497d6-589">2</span></span>                            | <span data-ttu-id="497d6-590">value2</span><span class="sxs-lookup"><span data-stu-id="497d6-590">value2</span></span>                       |
| <span data-ttu-id="497d6-591">3</span><span class="sxs-lookup"><span data-stu-id="497d6-591">3</span></span>                            | <span data-ttu-id="497d6-592">value4</span><span class="sxs-lookup"><span data-stu-id="497d6-592">value4</span></span>                       |
| <span data-ttu-id="497d6-593">4</span><span class="sxs-lookup"><span data-stu-id="497d6-593">4</span></span>                            | <span data-ttu-id="497d6-594">value5</span><span class="sxs-lookup"><span data-stu-id="497d6-594">value5</span></span>                       |

<span data-ttu-id="497d6-595">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="497d6-595">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="497d6-596">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="497d6-596">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="497d6-597">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="497d6-597">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="497d6-598">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="497d6-598">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="497d6-599">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="497d6-599">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="497d6-600">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="497d6-600">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="497d6-601">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="497d6-601">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="497d6-602">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="497d6-602">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="497d6-603">键</span><span class="sxs-lookup"><span data-stu-id="497d6-603">Key</span></span>             | <span data-ttu-id="497d6-604">“值”</span><span class="sxs-lookup"><span data-stu-id="497d6-604">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="497d6-605">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="497d6-605">array:entries:3</span></span> | <span data-ttu-id="497d6-606">value3</span><span class="sxs-lookup"><span data-stu-id="497d6-606">value3</span></span> |

<span data-ttu-id="497d6-607">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="497d6-607">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="497d6-608">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="497d6-608">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="497d6-609">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="497d6-609">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="497d6-610">0</span><span class="sxs-lookup"><span data-stu-id="497d6-610">0</span></span>                            | <span data-ttu-id="497d6-611">value0</span><span class="sxs-lookup"><span data-stu-id="497d6-611">value0</span></span>                       |
| <span data-ttu-id="497d6-612">1</span><span class="sxs-lookup"><span data-stu-id="497d6-612">1</span></span>                            | <span data-ttu-id="497d6-613">value1</span><span class="sxs-lookup"><span data-stu-id="497d6-613">value1</span></span>                       |
| <span data-ttu-id="497d6-614">2</span><span class="sxs-lookup"><span data-stu-id="497d6-614">2</span></span>                            | <span data-ttu-id="497d6-615">value2</span><span class="sxs-lookup"><span data-stu-id="497d6-615">value2</span></span>                       |
| <span data-ttu-id="497d6-616">3</span><span class="sxs-lookup"><span data-stu-id="497d6-616">3</span></span>                            | <span data-ttu-id="497d6-617">value3</span><span class="sxs-lookup"><span data-stu-id="497d6-617">value3</span></span>                       |
| <span data-ttu-id="497d6-618">4</span><span class="sxs-lookup"><span data-stu-id="497d6-618">4</span></span>                            | <span data-ttu-id="497d6-619">value4</span><span class="sxs-lookup"><span data-stu-id="497d6-619">value4</span></span>                       |
| <span data-ttu-id="497d6-620">5</span><span class="sxs-lookup"><span data-stu-id="497d6-620">5</span></span>                            | <span data-ttu-id="497d6-621">value5</span><span class="sxs-lookup"><span data-stu-id="497d6-621">value5</span></span>                       |

<span data-ttu-id="497d6-622">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="497d6-622">**JSON array processing**</span></span>

<span data-ttu-id="497d6-623">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="497d6-623">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="497d6-624">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="497d6-624">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="497d6-625">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="497d6-625">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="497d6-626">键</span><span class="sxs-lookup"><span data-stu-id="497d6-626">Key</span></span>                     | <span data-ttu-id="497d6-627">“值”</span><span class="sxs-lookup"><span data-stu-id="497d6-627">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="497d6-628">json_array:key</span><span class="sxs-lookup"><span data-stu-id="497d6-628">json_array:key</span></span>          | <span data-ttu-id="497d6-629">valueA</span><span class="sxs-lookup"><span data-stu-id="497d6-629">valueA</span></span> |
| <span data-ttu-id="497d6-630">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="497d6-630">json_array:subsection:0</span></span> | <span data-ttu-id="497d6-631">valueB</span><span class="sxs-lookup"><span data-stu-id="497d6-631">valueB</span></span> |
| <span data-ttu-id="497d6-632">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="497d6-632">json_array:subsection:1</span></span> | <span data-ttu-id="497d6-633">valueC</span><span class="sxs-lookup"><span data-stu-id="497d6-633">valueC</span></span> |
| <span data-ttu-id="497d6-634">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="497d6-634">json_array:subsection:2</span></span> | <span data-ttu-id="497d6-635">valueD</span><span class="sxs-lookup"><span data-stu-id="497d6-635">valueD</span></span> |

<span data-ttu-id="497d6-636">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="497d6-636">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="497d6-637">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="497d6-637">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="497d6-638">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="497d6-638">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="497d6-639">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="497d6-639">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="497d6-640">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="497d6-640">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="497d6-641">0</span><span class="sxs-lookup"><span data-stu-id="497d6-641">0</span></span>                                   | <span data-ttu-id="497d6-642">valueB</span><span class="sxs-lookup"><span data-stu-id="497d6-642">valueB</span></span>                              |
| <span data-ttu-id="497d6-643">1</span><span class="sxs-lookup"><span data-stu-id="497d6-643">1</span></span>                                   | <span data-ttu-id="497d6-644">valueC</span><span class="sxs-lookup"><span data-stu-id="497d6-644">valueC</span></span>                              |
| <span data-ttu-id="497d6-645">2</span><span class="sxs-lookup"><span data-stu-id="497d6-645">2</span></span>                                   | <span data-ttu-id="497d6-646">valueD</span><span class="sxs-lookup"><span data-stu-id="497d6-646">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="497d6-647">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="497d6-647">Custom configuration provider</span></span>

<span data-ttu-id="497d6-648">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-648">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="497d6-649">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="497d6-649">The provider has the following characteristics:</span></span>

* <span data-ttu-id="497d6-650">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="497d6-650">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="497d6-651">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="497d6-651">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="497d6-652">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="497d6-652">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="497d6-653">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="497d6-653">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="497d6-654">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="497d6-654">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="497d6-655">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="497d6-655">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="497d6-656">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-656">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="497d6-657">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="497d6-657">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="497d6-658">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="497d6-659">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="497d6-659">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="497d6-660">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="497d6-661">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-661">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="497d6-662">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="497d6-662">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="497d6-663">由于[配置密钥不区分大小写](#keys)，因此用来初始化数据库的字典是用不区分大小写的比较程序 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 创建的。</span><span class="sxs-lookup"><span data-stu-id="497d6-663">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="497d6-664">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-664">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="497d6-665">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="497d6-665">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="497d6-666">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="497d6-666">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="497d6-667">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="497d6-667">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="497d6-668">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-668">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="497d6-669">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="497d6-669">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="497d6-670">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-670">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="497d6-671">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="497d6-671">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="497d6-672">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-672">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="497d6-673">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="497d6-673">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="497d6-674">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="497d6-674">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="497d6-675">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="497d6-675">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="497d6-676">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="497d6-676">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="497d6-677">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="497d6-677">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="497d6-678">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="497d6-678">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="497d6-679">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="497d6-679">Access configuration during startup</span></span>

<span data-ttu-id="497d6-680">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="497d6-680">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="497d6-681">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="497d6-681">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="497d6-682">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="497d6-682">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="497d6-683">在 Razor Pages 页或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="497d6-683">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="497d6-684">若要访问 Razor Pages 页或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration 命名空间](xref:Microsoft.Extensions.Configuration)添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入页面或视图。</span><span class="sxs-lookup"><span data-stu-id="497d6-684">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="497d6-685">在 Razor 页面页中：</span><span class="sxs-lookup"><span data-stu-id="497d6-685">In a Razor Pages page:</span></span>

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

<span data-ttu-id="497d6-686">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="497d6-686">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="497d6-687">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="497d6-687">Add configuration from an external assembly</span></span>

<span data-ttu-id="497d6-688">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="497d6-688">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="497d6-689">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="497d6-689">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="497d6-690">其他资源</span><span class="sxs-lookup"><span data-stu-id="497d6-690">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
