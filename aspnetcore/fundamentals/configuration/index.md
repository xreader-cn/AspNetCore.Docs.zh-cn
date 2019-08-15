---
title: ASP.NET Core 中的配置
author: guardrex
description: 理解如何使用配置 API 配置 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 5723295c70f8d893f758ca5dc87180c6b707f493
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994176"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="c96a3-103">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="c96a3-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c96a3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c96a3-105">ASP.NET Core 中的应用配置基于配置提供程序  建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="c96a3-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="c96a3-106">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="c96a3-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="c96a3-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c96a3-107">Azure Key Vault</span></span>
* <span data-ttu-id="c96a3-108">Azure 应用配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-108">Azure App Configuration</span></span>
* <span data-ttu-id="c96a3-109">命令行参数</span><span class="sxs-lookup"><span data-stu-id="c96a3-109">Command-line arguments</span></span>
* <span data-ttu-id="c96a3-110">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="c96a3-111">目录文件</span><span class="sxs-lookup"><span data-stu-id="c96a3-111">Directory files</span></span>
* <span data-ttu-id="c96a3-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="c96a3-112">Environment variables</span></span>
* <span data-ttu-id="c96a3-113">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="c96a3-113">In-memory .NET objects</span></span>
* <span data-ttu-id="c96a3-114">设置文件</span><span class="sxs-lookup"><span data-stu-id="c96a3-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c96a3-115">框架隐式包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="c96a3-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c96a3-116">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="c96a3-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="c96a3-117">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="c96a3-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="c96a3-118">选项模式  是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="c96a3-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="c96a3-119">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="c96a3-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="c96a3-120">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c96a3-121">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c96a3-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="c96a3-122">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-122">Host versus app configuration</span></span>

<span data-ttu-id="c96a3-123">在配置并启动应用之前，配置并启动主机  。</span><span class="sxs-lookup"><span data-stu-id="c96a3-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="c96a3-124">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="c96a3-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="c96a3-125">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="c96a3-126">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="c96a3-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="c96a3-127">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="c96a3-128">默认配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c96a3-129">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="c96a3-130">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="c96a3-131">以下内容适用于使用[通用主机](xref:fundamentals/host/generic-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="c96a3-132">有关使用 [Web 主机](xref:fundamentals/host/web-host)时默认配置的详细信息，请参阅[本主题的 ASP.NET Core 2.2 版本](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="c96a3-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="c96a3-133">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="c96a3-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="c96a3-134">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `DOTNET_`（例如，`DOTNET_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="c96a3-135">在配置键值对加载后，前缀 (`DOTNET_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="c96a3-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="c96a3-136">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="c96a3-137">已建立 Web 主机默认配置 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="c96a3-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="c96a3-138">Kestrel 用作 Web 服务器，并使用应用的配置提供程序对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="c96a3-139">添加主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="c96a3-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="c96a3-140">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则添加转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="c96a3-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="c96a3-141">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="c96a3-141">Enable IIS integration.</span></span>
* <span data-ttu-id="c96a3-142">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="c96a3-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="c96a3-143">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json  提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c96a3-144">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json  提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c96a3-145">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="c96a3-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="c96a3-146">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="c96a3-147">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c96a3-148">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="c96a3-149">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="c96a3-150">以下内容适用于使用 [Web 主机](xref:fundamentals/host/web-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="c96a3-151">有关使用[通用主机](xref:fundamentals/host/generic-host)时默认配置的详细信息，请参阅[本主题的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c96a3-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="c96a3-152">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="c96a3-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="c96a3-153">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="c96a3-154">在配置键值对加载后，前缀 (`ASPNETCORE_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="c96a3-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="c96a3-155">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="c96a3-156">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="c96a3-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="c96a3-157">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json  提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c96a3-158">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json  提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="c96a3-159">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="c96a3-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="c96a3-160">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="c96a3-161">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="c96a3-162">安全性</span><span class="sxs-lookup"><span data-stu-id="c96a3-162">Security</span></span>

<span data-ttu-id="c96a3-163">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="c96a3-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="c96a3-164">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c96a3-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="c96a3-165">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="c96a3-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="c96a3-166">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="c96a3-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="c96a3-167">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="c96a3-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="c96a3-168"><xref:security/app-secrets> &ndash; 包含有关使用环境变量存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="c96a3-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="c96a3-169">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="c96a3-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="c96a3-170">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="c96a3-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="c96a3-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="c96a3-172">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="c96a3-173">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="c96a3-173">Hierarchical configuration data</span></span>

<span data-ttu-id="c96a3-174">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="c96a3-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="c96a3-175">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="c96a3-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="c96a3-176">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="c96a3-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="c96a3-177">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="c96a3-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="c96a3-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-178">section0:key0</span></span>
* <span data-ttu-id="c96a3-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-179">section0:key1</span></span>
* <span data-ttu-id="c96a3-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-180">section1:key0</span></span>
* <span data-ttu-id="c96a3-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-181">section1:key1</span></span>

<span data-ttu-id="c96a3-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="c96a3-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="c96a3-183">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="c96a3-184">约定</span><span class="sxs-lookup"><span data-stu-id="c96a3-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="c96a3-185">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-185">Sources and providers</span></span>

<span data-ttu-id="c96a3-186">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="c96a3-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="c96a3-187">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="c96a3-188">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="c96a3-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="c96a3-189">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c96a3-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 以获取以下类的配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="c96a3-191">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="c96a3-192">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-192">Keys</span></span>

<span data-ttu-id="c96a3-193">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="c96a3-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="c96a3-194">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c96a3-194">Keys are case-insensitive.</span></span> <span data-ttu-id="c96a3-195">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="c96a3-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="c96a3-196">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="c96a3-197">分层键</span><span class="sxs-lookup"><span data-stu-id="c96a3-197">Hierarchical keys</span></span>
  * <span data-ttu-id="c96a3-198">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="c96a3-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="c96a3-199">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="c96a3-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="c96a3-200">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="c96a3-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="c96a3-201">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="c96a3-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="c96a3-202">将机密加载到应用的配置中时，必须提供代码以用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="c96a3-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="c96a3-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="c96a3-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c96a3-204">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="c96a3-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="c96a3-205">值</span><span class="sxs-lookup"><span data-stu-id="c96a3-205">Values</span></span>

<span data-ttu-id="c96a3-206">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="c96a3-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="c96a3-207">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="c96a3-207">Values are strings.</span></span>
* <span data-ttu-id="c96a3-208">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="c96a3-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="c96a3-209">提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-209">Providers</span></span>

<span data-ttu-id="c96a3-210">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="c96a3-211">提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-211">Provider</span></span> | <span data-ttu-id="c96a3-212">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="c96a3-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="c96a3-213">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="c96a3-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="c96a3-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c96a3-214">Azure Key Vault</span></span> |
| <span data-ttu-id="c96a3-215">[Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="c96a3-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="c96a3-216">Azure 应用配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="c96a3-217">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="c96a3-218">命令行参数</span><span class="sxs-lookup"><span data-stu-id="c96a3-218">Command-line parameters</span></span> |
| [<span data-ttu-id="c96a3-219">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="c96a3-220">自定义源</span><span class="sxs-lookup"><span data-stu-id="c96a3-220">Custom source</span></span> |
| [<span data-ttu-id="c96a3-221">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="c96a3-222">环境变量</span><span class="sxs-lookup"><span data-stu-id="c96a3-222">Environment variables</span></span> |
| [<span data-ttu-id="c96a3-223">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="c96a3-224">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="c96a3-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="c96a3-225">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="c96a3-226">目录文件</span><span class="sxs-lookup"><span data-stu-id="c96a3-226">Directory files</span></span> |
| [<span data-ttu-id="c96a3-227">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="c96a3-228">内存中集合</span><span class="sxs-lookup"><span data-stu-id="c96a3-228">In-memory collections</span></span> |
| <span data-ttu-id="c96a3-229">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="c96a3-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="c96a3-230">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="c96a3-230">File in the user profile directory</span></span> |

<span data-ttu-id="c96a3-231">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="c96a3-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="c96a3-232">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="c96a3-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="c96a3-233">代码中的配置提供程序应以特定顺序排列以符合基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="c96a3-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="c96a3-234">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="c96a3-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="c96a3-235">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境）  </span><span class="sxs-lookup"><span data-stu-id="c96a3-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="c96a3-236">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="c96a3-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="c96a3-237">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="c96a3-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="c96a3-238">环境变量</span><span class="sxs-lookup"><span data-stu-id="c96a3-238">Environment variables</span></span>
1. <span data-ttu-id="c96a3-239">命令行参数</span><span class="sxs-lookup"><span data-stu-id="c96a3-239">Command-line arguments</span></span>

<span data-ttu-id="c96a3-240">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="c96a3-241">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="c96a3-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c96a3-242">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="c96a3-243">用 ConfigureHostConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="c96a3-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="c96a3-244">若要配置主机生成器，请调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 并提供配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="c96a3-245">用 `ConfigureHostConfiguration` 初始化 <xref:Microsoft.Extensions.Hosting.IHostEnvironment>，以便稍后在生成过程中使用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="c96a3-246">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="c96a3-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration((hostingContext, config) =>
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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="c96a3-247">用 UseConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="c96a3-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="c96a3-248">若要配置主机生成器，请使用配置在主机生成器上调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="c96a3-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="c96a3-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="c96a3-250">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="c96a3-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="c96a3-251">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="c96a3-252">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="c96a3-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="c96a3-253">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-253">Consume configuration during app startup</span></span>

<span data-ttu-id="c96a3-254">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-254">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c96a3-255">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-255">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="c96a3-256">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-256">Command-line Configuration Provider</span></span>

<span data-ttu-id="c96a3-257"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-257">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="c96a3-258">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-258">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c96a3-259">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-259">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="c96a3-260">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-260">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="c96a3-261">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="c96a3-261">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c96a3-262">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="c96a3-262">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="c96a3-263">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="c96a3-263">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="c96a3-264">环境变量。</span><span class="sxs-lookup"><span data-stu-id="c96a3-264">Environment variables.</span></span>

<span data-ttu-id="c96a3-265">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-265">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="c96a3-266">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-266">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="c96a3-267">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-267">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="c96a3-268">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="c96a3-268">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="c96a3-269">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-269">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c96a3-270">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-270">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="c96a3-271">**示例**</span><span class="sxs-lookup"><span data-stu-id="c96a3-271">**Example**</span></span>

<span data-ttu-id="c96a3-272">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-272">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="c96a3-273">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="c96a3-273">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="c96a3-274">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-274">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="c96a3-275">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="c96a3-275">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c96a3-276">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="c96a3-276">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="c96a3-277">自变量</span><span class="sxs-lookup"><span data-stu-id="c96a3-277">Arguments</span></span>

<span data-ttu-id="c96a3-278">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="c96a3-278">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="c96a3-279">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-279">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="c96a3-280">键前缀</span><span class="sxs-lookup"><span data-stu-id="c96a3-280">Key prefix</span></span>               | <span data-ttu-id="c96a3-281">示例</span><span class="sxs-lookup"><span data-stu-id="c96a3-281">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="c96a3-282">无前缀</span><span class="sxs-lookup"><span data-stu-id="c96a3-282">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="c96a3-283">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="c96a3-283">Two dashes (`--`)</span></span>        | <span data-ttu-id="c96a3-284">`--CommandLineKey2=value2`， `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="c96a3-284">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="c96a3-285">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="c96a3-285">Forward slash (`/`)</span></span>      | <span data-ttu-id="c96a3-286">`/CommandLineKey3=value3`， `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="c96a3-286">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="c96a3-287">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-287">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="c96a3-288">示例命令：</span><span class="sxs-lookup"><span data-stu-id="c96a3-288">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="c96a3-289">交换映射</span><span class="sxs-lookup"><span data-stu-id="c96a3-289">Switch mappings</span></span>

<span data-ttu-id="c96a3-290">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="c96a3-290">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="c96a3-291">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，可以为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="c96a3-291">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="c96a3-292">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="c96a3-292">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="c96a3-293">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-293">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="c96a3-294">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="c96a3-294">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="c96a3-295">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="c96a3-295">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="c96a3-296">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="c96a3-296">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="c96a3-297">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="c96a3-297">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="c96a3-298">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="c96a3-298">Create a switch mappings dictionary.</span></span> <span data-ttu-id="c96a3-299">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="c96a3-299">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="c96a3-300">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="c96a3-300">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="c96a3-301">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="c96a3-301">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="c96a3-302">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-302">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c96a3-303">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="c96a3-303">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="c96a3-304">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="c96a3-304">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="c96a3-305">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-305">Key</span></span>       | <span data-ttu-id="c96a3-306">值</span><span class="sxs-lookup"><span data-stu-id="c96a3-306">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="c96a3-307">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="c96a3-307">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="c96a3-308">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-308">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="c96a3-309">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-309">Key</span></span>               | <span data-ttu-id="c96a3-310">值</span><span class="sxs-lookup"><span data-stu-id="c96a3-310">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="c96a3-311">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-311">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="c96a3-312"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-312">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="c96a3-313">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-313">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="c96a3-314">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，用户可以在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="c96a3-314">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="c96a3-315">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="c96a3-315">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c96a3-316">如果使用[通用主机](xref:fundamentals/host/generic-host)初始化新的主机生成器，且调用了 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="c96a3-316">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="c96a3-317">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-317">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c96a3-318">如果使用 [Web 主机](xref:fundamentals/host/web-host)初始化新的主机生成器，且调用 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="c96a3-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="c96a3-319">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="c96a3-320">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="c96a3-320">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c96a3-321">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-321">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="c96a3-322">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="c96a3-322">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="c96a3-323">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="c96a3-323">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="c96a3-324">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="c96a3-324">Command-line arguments.</span></span>

<span data-ttu-id="c96a3-325">环境变量配置提供程序是在配置已根据用户机密和 appsettings  文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-325">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="c96a3-326">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-326">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="c96a3-327">如果需要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-327">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
}
```

<span data-ttu-id="c96a3-328">**示例**</span><span class="sxs-lookup"><span data-stu-id="c96a3-328">**Example**</span></span>

<span data-ttu-id="c96a3-329">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-329">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="c96a3-330">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-330">Run the sample app.</span></span> <span data-ttu-id="c96a3-331">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="c96a3-331">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c96a3-332">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="c96a3-332">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="c96a3-333">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-333">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="c96a3-334">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="c96a3-334">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="c96a3-335">请参阅示例应用的“Pages/Index.cshtml.cs”文件  。</span><span class="sxs-lookup"><span data-stu-id="c96a3-335">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="c96a3-336">如果希望公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs  中的 `FilteredConfiguration` 更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="c96a3-336">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="c96a3-337">前缀</span><span class="sxs-lookup"><span data-stu-id="c96a3-337">Prefixes</span></span>

<span data-ttu-id="c96a3-338">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="c96a3-338">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="c96a3-339">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="c96a3-339">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="c96a3-340">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="c96a3-340">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="c96a3-341">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="c96a3-341">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="c96a3-342">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-342">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="c96a3-343">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="c96a3-343">**Connection string prefixes**</span></span>

<span data-ttu-id="c96a3-344">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="c96a3-344">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="c96a3-345">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="c96a3-345">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="c96a3-346">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="c96a3-346">Connection string prefix</span></span> | <span data-ttu-id="c96a3-347">提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-347">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="c96a3-348">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-348">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="c96a3-349">MySQL</span><span class="sxs-lookup"><span data-stu-id="c96a3-349">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="c96a3-350">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="c96a3-350">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="c96a3-351">SQL Server</span><span class="sxs-lookup"><span data-stu-id="c96a3-351">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="c96a3-352">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="c96a3-352">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="c96a3-353">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="c96a3-353">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="c96a3-354">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="c96a3-354">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="c96a3-355">环境变量键</span><span class="sxs-lookup"><span data-stu-id="c96a3-355">Environment variable key</span></span> | <span data-ttu-id="c96a3-356">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="c96a3-356">Converted configuration key</span></span> | <span data-ttu-id="c96a3-357">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="c96a3-357">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c96a3-358">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="c96a3-358">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c96a3-359">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="c96a3-359">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="c96a3-360">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="c96a3-360">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c96a3-361">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="c96a3-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="c96a3-362">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="c96a3-362">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="c96a3-363">键：`ConnectionStrings:<KEY>_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="c96a3-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="c96a3-364">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="c96a3-364">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="c96a3-365">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-365">File Configuration Provider</span></span>

<span data-ttu-id="c96a3-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="c96a3-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="c96a3-367">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="c96a3-367">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="c96a3-368">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-368">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="c96a3-369">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-369">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="c96a3-370">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-370">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="c96a3-371">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-371">INI Configuration Provider</span></span>

<span data-ttu-id="c96a3-372"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-372">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="c96a3-373">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-373">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c96a3-374">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="c96a3-374">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="c96a3-375">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="c96a3-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="c96a3-376">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="c96a3-376">Whether the file is optional.</span></span>
* <span data-ttu-id="c96a3-377">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-377">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c96a3-378"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="c96a3-378">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c96a3-379">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-379">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="c96a3-380">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-380">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="c96a3-381">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="c96a3-381">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="c96a3-382">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="c96a3-382">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c96a3-383">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-383">section0:key0</span></span>
* <span data-ttu-id="c96a3-384">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-384">section0:key1</span></span>
* <span data-ttu-id="c96a3-385">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="c96a3-385">section1:subsection:key</span></span>
* <span data-ttu-id="c96a3-386">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="c96a3-386">section2:subsection0:key</span></span>
* <span data-ttu-id="c96a3-387">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="c96a3-387">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="c96a3-388">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-388">JSON Configuration Provider</span></span>

<span data-ttu-id="c96a3-389"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-389">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="c96a3-390">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-390">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c96a3-391">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="c96a3-391">Overloads permit specifying:</span></span>

* <span data-ttu-id="c96a3-392">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="c96a3-392">Whether the file is optional.</span></span>
* <span data-ttu-id="c96a3-393">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-393">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c96a3-394"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="c96a3-394">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c96a3-395">若使用 `CreateDefaultBuilder` 初始化新的主机生成器，则自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-395">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c96a3-396">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-396">The method is called to load configuration from:</span></span>

* <span data-ttu-id="c96a3-397">appsettings.json  &ndash; 首先读取此文件。</span><span class="sxs-lookup"><span data-stu-id="c96a3-397">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="c96a3-398">该文件的环境版本可以替代 appsettings.json  文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-398">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="c96a3-399">*appsettings.{Environment}.json*&ndash; 根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载文件的环境版本。</span><span class="sxs-lookup"><span data-stu-id="c96a3-399">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="c96a3-400">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-400">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="c96a3-401">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="c96a3-401">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="c96a3-402">环境变量。</span><span class="sxs-lookup"><span data-stu-id="c96a3-402">Environment variables.</span></span>
* <span data-ttu-id="c96a3-403">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="c96a3-403">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="c96a3-404">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="c96a3-404">Command-line arguments.</span></span>

<span data-ttu-id="c96a3-405">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-405">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="c96a3-406">因此，用户机密、环境变量和命令行参数会替代由 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-406">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="c96a3-407">构建主机时调用 `ConfigureAppConfiguration` 以指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置：  </span><span class="sxs-lookup"><span data-stu-id="c96a3-407">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="c96a3-408">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-408">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="c96a3-409">**示例**</span><span class="sxs-lookup"><span data-stu-id="c96a3-409">**Example**</span></span>

<span data-ttu-id="c96a3-410">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="c96a3-411">配置从 appsettings.json 和 appsettings.{Environment}.json 进行加载   。</span><span class="sxs-lookup"><span data-stu-id="c96a3-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="c96a3-412">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-412">Run the sample app.</span></span> <span data-ttu-id="c96a3-413">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="c96a3-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="c96a3-414">观察输出是否包含表中所示的配置的键值对，具体取决于环境。</span><span class="sxs-lookup"><span data-stu-id="c96a3-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="c96a3-415">记录配置键使用冒号 (`:`) 作为分层分隔符。</span><span class="sxs-lookup"><span data-stu-id="c96a3-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="c96a3-416">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-416">Key</span></span>                        | <span data-ttu-id="c96a3-417">开发值</span><span class="sxs-lookup"><span data-stu-id="c96a3-417">Development Value</span></span> | <span data-ttu-id="c96a3-418">生产值</span><span class="sxs-lookup"><span data-stu-id="c96a3-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="c96a3-419">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="c96a3-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="c96a3-420">信息</span><span class="sxs-lookup"><span data-stu-id="c96a3-420">Information</span></span>       | <span data-ttu-id="c96a3-421">信息</span><span class="sxs-lookup"><span data-stu-id="c96a3-421">Information</span></span>      |
| <span data-ttu-id="c96a3-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="c96a3-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="c96a3-423">信息</span><span class="sxs-lookup"><span data-stu-id="c96a3-423">Information</span></span>       | <span data-ttu-id="c96a3-424">信息</span><span class="sxs-lookup"><span data-stu-id="c96a3-424">Information</span></span>      |
| <span data-ttu-id="c96a3-425">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="c96a3-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="c96a3-426">调试</span><span class="sxs-lookup"><span data-stu-id="c96a3-426">Debug</span></span>             | <span data-ttu-id="c96a3-427">Error</span><span class="sxs-lookup"><span data-stu-id="c96a3-427">Error</span></span>            |
| <span data-ttu-id="c96a3-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="c96a3-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="c96a3-429">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-429">XML Configuration Provider</span></span>

<span data-ttu-id="c96a3-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="c96a3-431">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c96a3-432">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="c96a3-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="c96a3-433">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="c96a3-433">Whether the file is optional.</span></span>
* <span data-ttu-id="c96a3-434">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="c96a3-435"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="c96a3-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="c96a3-436">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="c96a3-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="c96a3-437">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="c96a3-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="c96a3-438">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="c96a3-439">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-439">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="c96a3-440">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="c96a3-440">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="c96a3-441">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="c96a3-441">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c96a3-442">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-442">section0:key0</span></span>
* <span data-ttu-id="c96a3-443">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-443">section0:key1</span></span>
* <span data-ttu-id="c96a3-444">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-444">section1:key0</span></span>
* <span data-ttu-id="c96a3-445">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-445">section1:key1</span></span>

<span data-ttu-id="c96a3-446">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="c96a3-446">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="c96a3-447">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="c96a3-447">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c96a3-448">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-448">section:section0:key:key0</span></span>
* <span data-ttu-id="c96a3-449">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-449">section:section0:key:key1</span></span>
* <span data-ttu-id="c96a3-450">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-450">section:section1:key:key0</span></span>
* <span data-ttu-id="c96a3-451">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-451">section:section1:key:key1</span></span>

<span data-ttu-id="c96a3-452">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="c96a3-452">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="c96a3-453">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="c96a3-453">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="c96a3-454">key:attribute</span><span class="sxs-lookup"><span data-stu-id="c96a3-454">key:attribute</span></span>
* <span data-ttu-id="c96a3-455">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="c96a3-455">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="c96a3-456">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-456">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="c96a3-457"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="c96a3-457">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="c96a3-458">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="c96a3-458">The key is the file name.</span></span> <span data-ttu-id="c96a3-459">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="c96a3-459">The value contains the file's contents.</span></span> <span data-ttu-id="c96a3-460">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="c96a3-460">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="c96a3-461">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-461">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="c96a3-462">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="c96a3-462">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="c96a3-463">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="c96a3-463">Overloads permit specifying:</span></span>

* <span data-ttu-id="c96a3-464">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="c96a3-464">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="c96a3-465">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="c96a3-465">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="c96a3-466">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="c96a3-466">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="c96a3-467">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-467">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="c96a3-468">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-468">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<span data-ttu-id="c96a3-469">基路径使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 设置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-469">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

## <a name="memory-configuration-provider"></a><span data-ttu-id="c96a3-470">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-470">Memory Configuration Provider</span></span>

<span data-ttu-id="c96a3-471"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="c96a3-471">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="c96a3-472">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-472">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="c96a3-473">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-473">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="c96a3-474">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-474">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="c96a3-475">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="c96a3-475">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="c96a3-476">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-476">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="c96a3-477">GetValue</span><span class="sxs-lookup"><span data-stu-id="c96a3-477">GetValue</span></span>

<span data-ttu-id="c96a3-478">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将其转换为指定类型。</span><span class="sxs-lookup"><span data-stu-id="c96a3-478">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="c96a3-479">如果未找到该键，则过载允许你提供默认值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-479">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="c96a3-480">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="c96a3-480">The following example:</span></span>

* <span data-ttu-id="c96a3-481">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-481">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="c96a3-482">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-482">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="c96a3-483">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-483">Types the value as an `int`.</span></span>
* <span data-ttu-id="c96a3-484">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="c96a3-484">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="c96a3-485">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="c96a3-485">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="c96a3-486">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="c96a3-486">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="c96a3-487">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="c96a3-487">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="c96a3-488">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="c96a3-488">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="c96a3-489">section0:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-489">section0:key0</span></span>
* <span data-ttu-id="c96a3-490">section0:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-490">section0:key1</span></span>
* <span data-ttu-id="c96a3-491">section1:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-491">section1:key0</span></span>
* <span data-ttu-id="c96a3-492">section1:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-492">section1:key1</span></span>
* <span data-ttu-id="c96a3-493">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-493">section2:subsection0:key0</span></span>
* <span data-ttu-id="c96a3-494">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-494">section2:subsection0:key1</span></span>
* <span data-ttu-id="c96a3-495">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="c96a3-495">section2:subsection1:key0</span></span>
* <span data-ttu-id="c96a3-496">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="c96a3-496">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="c96a3-497">GetSection</span><span class="sxs-lookup"><span data-stu-id="c96a3-497">GetSection</span></span>

<span data-ttu-id="c96a3-498">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="c96a3-498">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="c96a3-499">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="c96a3-499">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="c96a3-500">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="c96a3-500">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="c96a3-501">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="c96a3-501">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="c96a3-502">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-502">`GetSection` never returns `null`.</span></span> <span data-ttu-id="c96a3-503">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-503">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="c96a3-504">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="c96a3-504">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="c96a3-505">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="c96a3-505">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="c96a3-506">GetChildren</span><span class="sxs-lookup"><span data-stu-id="c96a3-506">GetChildren</span></span>

<span data-ttu-id="c96a3-507">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="c96a3-507">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="c96a3-508">存在</span><span class="sxs-lookup"><span data-stu-id="c96a3-508">Exists</span></span>

<span data-ttu-id="c96a3-509">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="c96a3-509">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="c96a3-510">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="c96a3-510">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="c96a3-511">绑定至类</span><span class="sxs-lookup"><span data-stu-id="c96a3-511">Bind to a class</span></span>

<span data-ttu-id="c96a3-512">可以使用选项模式  将配置绑定到表示相关设置组的类。</span><span class="sxs-lookup"><span data-stu-id="c96a3-512">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="c96a3-513">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-513">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c96a3-514">配置值作为字符串返回，但调用 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以构造 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 对象。</span><span class="sxs-lookup"><span data-stu-id="c96a3-514">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="c96a3-515">示例应用包含 `Starship` 模型 (Models/Starship.cs  )：</span><span class="sxs-lookup"><span data-stu-id="c96a3-515">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c96a3-516">当示例应用使用 JSON 配置提供程序加载配置时，starship.json  文件的 `starship` 节会创建配置：</span><span class="sxs-lookup"><span data-stu-id="c96a3-516">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="c96a3-517">创建以下配置键值对：</span><span class="sxs-lookup"><span data-stu-id="c96a3-517">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="c96a3-518">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-518">Key</span></span>                   | <span data-ttu-id="c96a3-519">值</span><span class="sxs-lookup"><span data-stu-id="c96a3-519">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="c96a3-520">starship:name</span><span class="sxs-lookup"><span data-stu-id="c96a3-520">starship:name</span></span>         | <span data-ttu-id="c96a3-521">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="c96a3-521">USS Enterprise</span></span>                                    |
| <span data-ttu-id="c96a3-522">starship:registry</span><span class="sxs-lookup"><span data-stu-id="c96a3-522">starship:registry</span></span>     | <span data-ttu-id="c96a3-523">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="c96a3-523">NCC-1701</span></span>                                          |
| <span data-ttu-id="c96a3-524">starship:class</span><span class="sxs-lookup"><span data-stu-id="c96a3-524">starship:class</span></span>        | <span data-ttu-id="c96a3-525">Constitution</span><span class="sxs-lookup"><span data-stu-id="c96a3-525">Constitution</span></span>                                      |
| <span data-ttu-id="c96a3-526">starship:length</span><span class="sxs-lookup"><span data-stu-id="c96a3-526">starship:length</span></span>       | <span data-ttu-id="c96a3-527">304.8</span><span class="sxs-lookup"><span data-stu-id="c96a3-527">304.8</span></span>                                             |
| <span data-ttu-id="c96a3-528">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="c96a3-528">starship:commissioned</span></span> | <span data-ttu-id="c96a3-529">False</span><span class="sxs-lookup"><span data-stu-id="c96a3-529">False</span></span>                                             |
| <span data-ttu-id="c96a3-530">trademark</span><span class="sxs-lookup"><span data-stu-id="c96a3-530">trademark</span></span>             | <span data-ttu-id="c96a3-531">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="c96a3-531">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="c96a3-532">示例应用使用 `starship` 键调用 `GetSection`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-532">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="c96a3-533">`starship` 键值对是独立的。</span><span class="sxs-lookup"><span data-stu-id="c96a3-533">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="c96a3-534">在子节传入 `Starship` 类的实例时调用 `Bind` 方法。</span><span class="sxs-lookup"><span data-stu-id="c96a3-534">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="c96a3-535">绑定实例值后，将实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="c96a3-535">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="c96a3-536">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="c96a3-536">Bind to an object graph</span></span>

<span data-ttu-id="c96a3-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="c96a3-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="c96a3-538">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs  )：</span><span class="sxs-lookup"><span data-stu-id="c96a3-538">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c96a3-539">示例应用有一个包含配置数据的 tvshow.xml  文件：</span><span class="sxs-lookup"><span data-stu-id="c96a3-539">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="c96a3-540">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="c96a3-540">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="c96a3-541">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="c96a3-541">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="c96a3-542">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定类型。</span><span class="sxs-lookup"><span data-stu-id="c96a3-542">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="c96a3-543">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="c96a3-543">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="c96a3-544">以下代码显示如何将 `Get<T>` 与前面的示例一起使用，该示例允许将绑定实例直接分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="c96a3-544">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="c96a3-545">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="c96a3-545">Bind an array to a class</span></span>

<span data-ttu-id="c96a3-546">示例应用演示了本部分中介绍的概念。 </span><span class="sxs-lookup"><span data-stu-id="c96a3-546">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="c96a3-547"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="c96a3-547">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="c96a3-548">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="c96a3-548">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="c96a3-549">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="c96a3-549">Binding is provided by convention.</span></span> <span data-ttu-id="c96a3-550">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="c96a3-550">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="c96a3-551">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="c96a3-551">**In-memory array processing**</span></span>

<span data-ttu-id="c96a3-552">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-552">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="c96a3-553">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-553">Key</span></span>             | <span data-ttu-id="c96a3-554">值</span><span class="sxs-lookup"><span data-stu-id="c96a3-554">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="c96a3-555">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="c96a3-555">array:entries:0</span></span> | <span data-ttu-id="c96a3-556">value0</span><span class="sxs-lookup"><span data-stu-id="c96a3-556">value0</span></span> |
| <span data-ttu-id="c96a3-557">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="c96a3-557">array:entries:1</span></span> | <span data-ttu-id="c96a3-558">value1</span><span class="sxs-lookup"><span data-stu-id="c96a3-558">value1</span></span> |
| <span data-ttu-id="c96a3-559">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="c96a3-559">array:entries:2</span></span> | <span data-ttu-id="c96a3-560">value2</span><span class="sxs-lookup"><span data-stu-id="c96a3-560">value2</span></span> |
| <span data-ttu-id="c96a3-561">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="c96a3-561">array:entries:4</span></span> | <span data-ttu-id="c96a3-562">value4</span><span class="sxs-lookup"><span data-stu-id="c96a3-562">value4</span></span> |
| <span data-ttu-id="c96a3-563">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="c96a3-563">array:entries:5</span></span> | <span data-ttu-id="c96a3-564">value5</span><span class="sxs-lookup"><span data-stu-id="c96a3-564">value5</span></span> |

<span data-ttu-id="c96a3-565">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="c96a3-565">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

<span data-ttu-id="c96a3-566">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-566">The array skips a value for index &num;3.</span></span> <span data-ttu-id="c96a3-567">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="c96a3-567">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="c96a3-568">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="c96a3-568">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c96a3-569">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="c96a3-569">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="c96a3-570">还可以使用 [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="c96a3-570">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="c96a3-571">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="c96a3-571">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="c96a3-572">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="c96a3-572">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="c96a3-573">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="c96a3-573">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="c96a3-574">0</span><span class="sxs-lookup"><span data-stu-id="c96a3-574">0</span></span>                            | <span data-ttu-id="c96a3-575">value0</span><span class="sxs-lookup"><span data-stu-id="c96a3-575">value0</span></span>                       |
| <span data-ttu-id="c96a3-576">1</span><span class="sxs-lookup"><span data-stu-id="c96a3-576">1</span></span>                            | <span data-ttu-id="c96a3-577">value1</span><span class="sxs-lookup"><span data-stu-id="c96a3-577">value1</span></span>                       |
| <span data-ttu-id="c96a3-578">2</span><span class="sxs-lookup"><span data-stu-id="c96a3-578">2</span></span>                            | <span data-ttu-id="c96a3-579">value2</span><span class="sxs-lookup"><span data-stu-id="c96a3-579">value2</span></span>                       |
| <span data-ttu-id="c96a3-580">3</span><span class="sxs-lookup"><span data-stu-id="c96a3-580">3</span></span>                            | <span data-ttu-id="c96a3-581">value4</span><span class="sxs-lookup"><span data-stu-id="c96a3-581">value4</span></span>                       |
| <span data-ttu-id="c96a3-582">4</span><span class="sxs-lookup"><span data-stu-id="c96a3-582">4</span></span>                            | <span data-ttu-id="c96a3-583">value5</span><span class="sxs-lookup"><span data-stu-id="c96a3-583">value5</span></span>                       |

<span data-ttu-id="c96a3-584">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-584">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="c96a3-585">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="c96a3-585">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="c96a3-586">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="c96a3-586">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="c96a3-587">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="c96a3-587">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="c96a3-588">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="c96a3-588">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="c96a3-589">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="c96a3-589">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="c96a3-590">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="c96a3-590">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="c96a3-591">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="c96a3-591">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="c96a3-592">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-592">Key</span></span>             | <span data-ttu-id="c96a3-593">值</span><span class="sxs-lookup"><span data-stu-id="c96a3-593">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="c96a3-594">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="c96a3-594">array:entries:3</span></span> | <span data-ttu-id="c96a3-595">value3</span><span class="sxs-lookup"><span data-stu-id="c96a3-595">value3</span></span> |

<span data-ttu-id="c96a3-596">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-596">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="c96a3-597">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="c96a3-597">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="c96a3-598">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="c96a3-598">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="c96a3-599">0</span><span class="sxs-lookup"><span data-stu-id="c96a3-599">0</span></span>                            | <span data-ttu-id="c96a3-600">value0</span><span class="sxs-lookup"><span data-stu-id="c96a3-600">value0</span></span>                       |
| <span data-ttu-id="c96a3-601">1</span><span class="sxs-lookup"><span data-stu-id="c96a3-601">1</span></span>                            | <span data-ttu-id="c96a3-602">value1</span><span class="sxs-lookup"><span data-stu-id="c96a3-602">value1</span></span>                       |
| <span data-ttu-id="c96a3-603">2</span><span class="sxs-lookup"><span data-stu-id="c96a3-603">2</span></span>                            | <span data-ttu-id="c96a3-604">value2</span><span class="sxs-lookup"><span data-stu-id="c96a3-604">value2</span></span>                       |
| <span data-ttu-id="c96a3-605">3</span><span class="sxs-lookup"><span data-stu-id="c96a3-605">3</span></span>                            | <span data-ttu-id="c96a3-606">value3</span><span class="sxs-lookup"><span data-stu-id="c96a3-606">value3</span></span>                       |
| <span data-ttu-id="c96a3-607">4</span><span class="sxs-lookup"><span data-stu-id="c96a3-607">4</span></span>                            | <span data-ttu-id="c96a3-608">value4</span><span class="sxs-lookup"><span data-stu-id="c96a3-608">value4</span></span>                       |
| <span data-ttu-id="c96a3-609">5</span><span class="sxs-lookup"><span data-stu-id="c96a3-609">5</span></span>                            | <span data-ttu-id="c96a3-610">value5</span><span class="sxs-lookup"><span data-stu-id="c96a3-610">value5</span></span>                       |

<span data-ttu-id="c96a3-611">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="c96a3-611">**JSON array processing**</span></span>

<span data-ttu-id="c96a3-612">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="c96a3-612">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="c96a3-613">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="c96a3-613">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="c96a3-614">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="c96a3-614">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="c96a3-615">键</span><span class="sxs-lookup"><span data-stu-id="c96a3-615">Key</span></span>                     | <span data-ttu-id="c96a3-616">值</span><span class="sxs-lookup"><span data-stu-id="c96a3-616">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="c96a3-617">json_array:key</span><span class="sxs-lookup"><span data-stu-id="c96a3-617">json_array:key</span></span>          | <span data-ttu-id="c96a3-618">valueA</span><span class="sxs-lookup"><span data-stu-id="c96a3-618">valueA</span></span> |
| <span data-ttu-id="c96a3-619">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="c96a3-619">json_array:subsection:0</span></span> | <span data-ttu-id="c96a3-620">valueB</span><span class="sxs-lookup"><span data-stu-id="c96a3-620">valueB</span></span> |
| <span data-ttu-id="c96a3-621">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="c96a3-621">json_array:subsection:1</span></span> | <span data-ttu-id="c96a3-622">valueC</span><span class="sxs-lookup"><span data-stu-id="c96a3-622">valueC</span></span> |
| <span data-ttu-id="c96a3-623">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="c96a3-623">json_array:subsection:2</span></span> | <span data-ttu-id="c96a3-624">valueD</span><span class="sxs-lookup"><span data-stu-id="c96a3-624">valueD</span></span> |

<span data-ttu-id="c96a3-625">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="c96a3-625">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="c96a3-626">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-626">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="c96a3-627">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="c96a3-627">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="c96a3-628">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="c96a3-628">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="c96a3-629">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="c96a3-629">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="c96a3-630">0</span><span class="sxs-lookup"><span data-stu-id="c96a3-630">0</span></span>                                   | <span data-ttu-id="c96a3-631">valueB</span><span class="sxs-lookup"><span data-stu-id="c96a3-631">valueB</span></span>                              |
| <span data-ttu-id="c96a3-632">1</span><span class="sxs-lookup"><span data-stu-id="c96a3-632">1</span></span>                                   | <span data-ttu-id="c96a3-633">valueC</span><span class="sxs-lookup"><span data-stu-id="c96a3-633">valueC</span></span>                              |
| <span data-ttu-id="c96a3-634">2</span><span class="sxs-lookup"><span data-stu-id="c96a3-634">2</span></span>                                   | <span data-ttu-id="c96a3-635">valueD</span><span class="sxs-lookup"><span data-stu-id="c96a3-635">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="c96a3-636">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="c96a3-636">Custom configuration provider</span></span>

<span data-ttu-id="c96a3-637">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-637">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="c96a3-638">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="c96a3-638">The provider has the following characteristics:</span></span>

* <span data-ttu-id="c96a3-639">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="c96a3-639">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="c96a3-640">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c96a3-640">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="c96a3-641">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="c96a3-641">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="c96a3-642">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="c96a3-642">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="c96a3-643">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="c96a3-643">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="c96a3-644">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="c96a3-644">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c96a3-645">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-645">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="c96a3-646">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-646">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="c96a3-647">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-647">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="c96a3-648">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="c96a3-648">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="c96a3-649">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-649">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="c96a3-650">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-650">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="c96a3-651">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="c96a3-651">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="c96a3-652">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-652">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="c96a3-653">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-653">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="c96a3-654">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="c96a3-654">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="c96a3-655">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="c96a3-655">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c96a3-656">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-656">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="c96a3-657">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-657">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="c96a3-658">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="c96a3-659">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="c96a3-659">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="c96a3-660">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="c96a3-661">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="c96a3-661">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="c96a3-662">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="c96a3-662">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="c96a3-663">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="c96a3-663">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="c96a3-664">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="c96a3-664">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="c96a3-665">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="c96a3-665">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="c96a3-666">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="c96a3-666">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="c96a3-667">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-667">Access configuration during startup</span></span>

<span data-ttu-id="c96a3-668">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="c96a3-668">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c96a3-669">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="c96a3-669">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="c96a3-670">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="c96a3-670">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="c96a3-671">在 Razor Pages 页或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-671">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="c96a3-672">若要访问 Razor Pages 页或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration 命名空间](xref:Microsoft.Extensions.Configuration)添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入页面或视图。</span><span class="sxs-lookup"><span data-stu-id="c96a3-672">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="c96a3-673">在 Razor 页面页中：</span><span class="sxs-lookup"><span data-stu-id="c96a3-673">In a Razor Pages page:</span></span>

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

<span data-ttu-id="c96a3-674">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="c96a3-674">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="c96a3-675">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="c96a3-675">Add configuration from an external assembly</span></span>

<span data-ttu-id="c96a3-676">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="c96a3-676">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="c96a3-677">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="c96a3-677">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c96a3-678">其他资源</span><span class="sxs-lookup"><span data-stu-id="c96a3-678">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
