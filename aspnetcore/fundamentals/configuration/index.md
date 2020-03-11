---
title: ASP.NET Core 中的配置
author: guardrex
description: 理解如何使用配置 API 配置 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 3dcabae3f76d81e641057c346dbb9097c2da42c7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644100"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="48d19-103">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="48d19-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="48d19-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="48d19-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48d19-105">ASP.NET Core 中的应用配置基于配置提供程序  建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="48d19-106">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="48d19-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="48d19-107">Azure Key Vault</span></span>
* <span data-ttu-id="48d19-108">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="48d19-108">Azure App Configuration</span></span>
* <span data-ttu-id="48d19-109">命令行参数</span><span class="sxs-lookup"><span data-stu-id="48d19-109">Command-line arguments</span></span>
* <span data-ttu-id="48d19-110">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="48d19-111">目录文件</span><span class="sxs-lookup"><span data-stu-id="48d19-111">Directory files</span></span>
* <span data-ttu-id="48d19-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="48d19-112">Environment variables</span></span>
* <span data-ttu-id="48d19-113">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="48d19-113">In-memory .NET objects</span></span>
* <span data-ttu-id="48d19-114">设置文件</span><span class="sxs-lookup"><span data-stu-id="48d19-114">Settings files</span></span>

<span data-ttu-id="48d19-115">框架隐式包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="48d19-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

<span data-ttu-id="48d19-116">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="48d19-116">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="48d19-117">选项模式  是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="48d19-117">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="48d19-118">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="48d19-118">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="48d19-119">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="48d19-119">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="48d19-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="48d19-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="48d19-121">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="48d19-121">Host versus app configuration</span></span>

<span data-ttu-id="48d19-122">在配置并启动应用之前，配置并启动主机  。</span><span class="sxs-lookup"><span data-stu-id="48d19-122">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="48d19-123">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="48d19-123">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="48d19-124">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-124">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="48d19-125">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-125">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="48d19-126">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="48d19-126">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="48d19-127">其他配置</span><span class="sxs-lookup"><span data-stu-id="48d19-127">Other configuration</span></span>

<span data-ttu-id="48d19-128">本主题仅与应用配置相关  。</span><span class="sxs-lookup"><span data-stu-id="48d19-128">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="48d19-129">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-129">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="48d19-130">launch.json  /launchSettings.json  是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="48d19-130">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="48d19-131"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="48d19-131">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="48d19-132">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-132">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="48d19-133">web.config  是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="48d19-133">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="48d19-134">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="48d19-134">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="48d19-135">默认配置</span><span class="sxs-lookup"><span data-stu-id="48d19-135">Default configuration</span></span>

<span data-ttu-id="48d19-136">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="48d19-136">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="48d19-137">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-137">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="48d19-138">以下内容适用于使用[通用主机](xref:fundamentals/host/generic-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-138">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="48d19-139">有关使用 [Web 主机](xref:fundamentals/host/web-host)时默认配置的详细信息，请参阅[本主题的 ASP.NET Core 2.2 版本](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="48d19-139">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="48d19-140">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="48d19-140">Host configuration is provided from:</span></span>
  * <span data-ttu-id="48d19-141">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `DOTNET_`（例如，`DOTNET_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-141">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="48d19-142">在配置键值对加载后，前缀 (`DOTNET_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="48d19-142">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="48d19-143">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-143">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="48d19-144">已建立 Web 主机默认配置 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="48d19-144">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="48d19-145">Kestrel 用作 Web 服务器，并使用应用的配置提供程序对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-145">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="48d19-146">添加主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="48d19-146">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="48d19-147">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则添加转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="48d19-147">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="48d19-148">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="48d19-148">Enable IIS integration.</span></span>
* <span data-ttu-id="48d19-149">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="48d19-149">App configuration is provided from:</span></span>
  * <span data-ttu-id="48d19-150">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json  提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-150">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="48d19-151">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json  提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-151">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="48d19-152">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="48d19-152">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="48d19-153">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-153">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="48d19-154">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-154">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="48d19-155">安全性</span><span class="sxs-lookup"><span data-stu-id="48d19-155">Security</span></span>

<span data-ttu-id="48d19-156">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="48d19-156">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="48d19-157">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-157">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="48d19-158">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="48d19-158">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="48d19-159">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="48d19-159">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="48d19-160">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="48d19-160">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="48d19-161"><xref:security/app-secrets> &ndash; 包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="48d19-161"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="48d19-162">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="48d19-162">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="48d19-163">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-163">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="48d19-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="48d19-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="48d19-165">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="48d19-165">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="48d19-166">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="48d19-166">Hierarchical configuration data</span></span>

<span data-ttu-id="48d19-167">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-167">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="48d19-168">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="48d19-168">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="48d19-169">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="48d19-169">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="48d19-170">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="48d19-170">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="48d19-171">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-171">section0:key0</span></span>
* <span data-ttu-id="48d19-172">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-172">section0:key1</span></span>
* <span data-ttu-id="48d19-173">section1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-173">section1:key0</span></span>
* <span data-ttu-id="48d19-174">section1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-174">section1:key1</span></span>

<span data-ttu-id="48d19-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="48d19-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="48d19-176">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-176">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="48d19-177">约定</span><span class="sxs-lookup"><span data-stu-id="48d19-177">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="48d19-178">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-178">Sources and providers</span></span>

<span data-ttu-id="48d19-179">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="48d19-179">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="48d19-180">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-180">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="48d19-181">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="48d19-181">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="48d19-182">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="48d19-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="48d19-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 来获取以下类的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="48d19-184">在下面的示例中，使用 `_config` 字段来访问配置值：</span><span class="sxs-lookup"><span data-stu-id="48d19-184">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="48d19-185">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="48d19-185">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="48d19-186">键</span><span class="sxs-lookup"><span data-stu-id="48d19-186">Keys</span></span>

<span data-ttu-id="48d19-187">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="48d19-187">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="48d19-188">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="48d19-188">Keys are case-insensitive.</span></span> <span data-ttu-id="48d19-189">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="48d19-189">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="48d19-190">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-190">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="48d19-191">分层键</span><span class="sxs-lookup"><span data-stu-id="48d19-191">Hierarchical keys</span></span>
  * <span data-ttu-id="48d19-192">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="48d19-192">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="48d19-193">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="48d19-193">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="48d19-194">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="48d19-194">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="48d19-195">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="48d19-195">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="48d19-196">将机密加载到应用的配置中时，用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="48d19-196">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="48d19-197"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-197">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="48d19-198">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="48d19-198">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="48d19-199">值</span><span class="sxs-lookup"><span data-stu-id="48d19-199">Values</span></span>

<span data-ttu-id="48d19-200">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="48d19-200">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="48d19-201">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="48d19-201">Values are strings.</span></span>
* <span data-ttu-id="48d19-202">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-202">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="48d19-203">提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-203">Providers</span></span>

<span data-ttu-id="48d19-204">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-204">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="48d19-205">提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-205">Provider</span></span> | <span data-ttu-id="48d19-206">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="48d19-206">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="48d19-207">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="48d19-207">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="48d19-208">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="48d19-208">Azure Key Vault</span></span> |
| <span data-ttu-id="48d19-209">[Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="48d19-209">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="48d19-210">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="48d19-210">Azure App Configuration</span></span> |
| [<span data-ttu-id="48d19-211">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="48d19-212">命令行参数</span><span class="sxs-lookup"><span data-stu-id="48d19-212">Command-line parameters</span></span> |
| [<span data-ttu-id="48d19-213">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="48d19-214">自定义源</span><span class="sxs-lookup"><span data-stu-id="48d19-214">Custom source</span></span> |
| [<span data-ttu-id="48d19-215">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="48d19-216">环境变量</span><span class="sxs-lookup"><span data-stu-id="48d19-216">Environment variables</span></span> |
| [<span data-ttu-id="48d19-217">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="48d19-218">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="48d19-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="48d19-219">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-219">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="48d19-220">目录文件</span><span class="sxs-lookup"><span data-stu-id="48d19-220">Directory files</span></span> |
| [<span data-ttu-id="48d19-221">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-221">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="48d19-222">内存中集合</span><span class="sxs-lookup"><span data-stu-id="48d19-222">In-memory collections</span></span> |
| <span data-ttu-id="48d19-223">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="48d19-223">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="48d19-224">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="48d19-224">File in the user profile directory</span></span> |

<span data-ttu-id="48d19-225">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="48d19-225">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="48d19-226">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="48d19-226">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="48d19-227">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="48d19-227">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="48d19-228">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="48d19-228">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="48d19-229">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境）  </span><span class="sxs-lookup"><span data-stu-id="48d19-229">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="48d19-230">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="48d19-230">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="48d19-231">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="48d19-231">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="48d19-232">环境变量</span><span class="sxs-lookup"><span data-stu-id="48d19-232">Environment variables</span></span>
1. <span data-ttu-id="48d19-233">命令行参数</span><span class="sxs-lookup"><span data-stu-id="48d19-233">Command-line arguments</span></span>

<span data-ttu-id="48d19-234">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-234">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="48d19-235">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="48d19-235">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-236">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-236">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="48d19-237">用 ConfigureHostConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="48d19-237">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="48d19-238">若要配置主机生成器，请调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 并提供配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-238">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="48d19-239">用 `ConfigureHostConfiguration` 初始化 <xref:Microsoft.Extensions.Hosting.IHostEnvironment>，以便稍后在生成过程中使用。</span><span class="sxs-lookup"><span data-stu-id="48d19-239">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="48d19-240">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="48d19-240">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="48d19-241">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="48d19-241">ConfigureAppConfiguration</span></span>

<span data-ttu-id="48d19-242">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="48d19-242">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="48d19-243">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="48d19-243">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="48d19-244">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="48d19-244">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="48d19-245">删除 CreateDefaultBuilder 添加的提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-245">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="48d19-246">要删除 `CreateDefaultBuilder` 添加的提供程序，请先对 [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) 调用 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)：</span><span class="sxs-lookup"><span data-stu-id="48d19-246">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="48d19-247">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="48d19-247">Consume configuration during app startup</span></span>

<span data-ttu-id="48d19-248">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="48d19-248">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="48d19-249">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-249">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="48d19-250">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-250">Command-line Configuration Provider</span></span>

<span data-ttu-id="48d19-251"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-251">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-252">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-252">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-253">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="48d19-253">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="48d19-254">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-254">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-255">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="48d19-255">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="48d19-256">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="48d19-256">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="48d19-257">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="48d19-257">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="48d19-258">环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-258">Environment variables.</span></span>

<span data-ttu-id="48d19-259">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-259">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="48d19-260">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-260">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="48d19-261">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="48d19-261">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="48d19-262">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="48d19-262">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="48d19-263">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="48d19-263">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-264">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="48d19-264">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="48d19-265">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-265">**Example**</span></span>

<span data-ttu-id="48d19-266">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="48d19-266">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="48d19-267">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="48d19-267">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="48d19-268">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="48d19-268">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="48d19-269">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="48d19-269">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="48d19-270">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-270">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="48d19-271">自变量</span><span class="sxs-lookup"><span data-stu-id="48d19-271">Arguments</span></span>

<span data-ttu-id="48d19-272">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="48d19-272">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="48d19-273">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="48d19-273">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="48d19-274">键前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-274">Key prefix</span></span>               | <span data-ttu-id="48d19-275">示例</span><span class="sxs-lookup"><span data-stu-id="48d19-275">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="48d19-276">无前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-276">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="48d19-277">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="48d19-277">Two dashes (`--`)</span></span>        | <span data-ttu-id="48d19-278">`--CommandLineKey2=value2`，`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="48d19-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="48d19-279">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="48d19-279">Forward slash (`/`)</span></span>      | <span data-ttu-id="48d19-280">`/CommandLineKey3=value3`，`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="48d19-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="48d19-281">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="48d19-281">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="48d19-282">示例命令：</span><span class="sxs-lookup"><span data-stu-id="48d19-282">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="48d19-283">交换映射</span><span class="sxs-lookup"><span data-stu-id="48d19-283">Switch mappings</span></span>

<span data-ttu-id="48d19-284">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="48d19-284">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="48d19-285">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，需要为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="48d19-285">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="48d19-286">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="48d19-286">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="48d19-287">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-287">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="48d19-288">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="48d19-288">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="48d19-289">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="48d19-289">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="48d19-290">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="48d19-290">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="48d19-291">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="48d19-291">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="48d19-292">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="48d19-292">Create a switch mappings dictionary.</span></span> <span data-ttu-id="48d19-293">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="48d19-293">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="48d19-294">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="48d19-294">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="48d19-295">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="48d19-295">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="48d19-296">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="48d19-296">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-297">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="48d19-297">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="48d19-298">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-298">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="48d19-299">键</span><span class="sxs-lookup"><span data-stu-id="48d19-299">Key</span></span>       | <span data-ttu-id="48d19-300">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-300">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="48d19-301">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="48d19-301">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="48d19-302">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-302">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="48d19-303">键</span><span class="sxs-lookup"><span data-stu-id="48d19-303">Key</span></span>               | <span data-ttu-id="48d19-304">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-304">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="48d19-305">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-305">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="48d19-306"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-306">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-307">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-307">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="48d19-308">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="48d19-309">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="48d19-309">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="48d19-310">如果使用[通用主机](xref:fundamentals/host/generic-host)初始化新的主机生成器，且调用了 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-310">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="48d19-311">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-311">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-312">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="48d19-312">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="48d19-313">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="48d19-313">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="48d19-314">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="48d19-314">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="48d19-315">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="48d19-315">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="48d19-316">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="48d19-316">Command-line arguments.</span></span>

<span data-ttu-id="48d19-317">环境变量配置提供程序是在配置已根据用户机密和 appsettings  文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="48d19-317">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="48d19-318">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-318">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="48d19-319">要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`：</span><span class="sxs-lookup"><span data-stu-id="48d19-319">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="48d19-320">最后调用 `AddEnvironmentVariables`让带给定前缀的环境变量可替代其他提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-320">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="48d19-321">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-321">**Example**</span></span>

<span data-ttu-id="48d19-322">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="48d19-322">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="48d19-323">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-323">Run the sample app.</span></span> <span data-ttu-id="48d19-324">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="48d19-324">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="48d19-325">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-325">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="48d19-326">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="48d19-326">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="48d19-327">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-327">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="48d19-328">请参阅示例应用的“Pages/Index.cshtml.cs”文件  。</span><span class="sxs-lookup"><span data-stu-id="48d19-328">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="48d19-329">要公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-329">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="48d19-330">前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-330">Prefixes</span></span>

<span data-ttu-id="48d19-331">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-331">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="48d19-332">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="48d19-332">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="48d19-333">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="48d19-333">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="48d19-334">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-334">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="48d19-335">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-335">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-336">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="48d19-336">**Connection string prefixes**</span></span>

<span data-ttu-id="48d19-337">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="48d19-337">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="48d19-338">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="48d19-338">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="48d19-339">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-339">Connection string prefix</span></span> | <span data-ttu-id="48d19-340">提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-340">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="48d19-341">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-341">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="48d19-342">MySQL</span><span class="sxs-lookup"><span data-stu-id="48d19-342">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="48d19-343">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="48d19-343">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="48d19-344">SQL Server</span><span class="sxs-lookup"><span data-stu-id="48d19-344">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="48d19-345">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="48d19-345">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="48d19-346">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="48d19-346">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="48d19-347">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="48d19-347">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="48d19-348">环境变量键</span><span class="sxs-lookup"><span data-stu-id="48d19-348">Environment variable key</span></span> | <span data-ttu-id="48d19-349">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="48d19-349">Converted configuration key</span></span> | <span data-ttu-id="48d19-350">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="48d19-350">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-351">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="48d19-351">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-352">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="48d19-352">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="48d19-353">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="48d19-353">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-354">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="48d19-354">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="48d19-355">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="48d19-355">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-356">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="48d19-356">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="48d19-357">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="48d19-357">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="48d19-358">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-358">**Example**</span></span>

<span data-ttu-id="48d19-359">在服务器上创建了一个自定义连接字符串环境变量：</span><span class="sxs-lookup"><span data-stu-id="48d19-359">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="48d19-360">名称 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="48d19-360">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="48d19-361">值 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="48d19-361">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="48d19-362">如果 `IConfiguration` 已引入并分配给名为 `_config` 的字段，请读取值：</span><span class="sxs-lookup"><span data-stu-id="48d19-362">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="48d19-363">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-363">File Configuration Provider</span></span>

<span data-ttu-id="48d19-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="48d19-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="48d19-365">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="48d19-365">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="48d19-366">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-366">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="48d19-367">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-367">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="48d19-368">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-368">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="48d19-369">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-369">INI Configuration Provider</span></span>

<span data-ttu-id="48d19-370"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-370">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-371">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-371">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-372">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="48d19-372">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="48d19-373">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-373">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-374">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="48d19-374">Whether the file is optional.</span></span>
* <span data-ttu-id="48d19-375">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-375">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="48d19-376"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-376">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="48d19-377">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-377">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="48d19-378">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="48d19-378">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="48d19-379">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-379">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-380">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-380">section0:key0</span></span>
* <span data-ttu-id="48d19-381">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-381">section0:key1</span></span>
* <span data-ttu-id="48d19-382">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="48d19-382">section1:subsection:key</span></span>
* <span data-ttu-id="48d19-383">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="48d19-383">section2:subsection0:key</span></span>
* <span data-ttu-id="48d19-384">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="48d19-384">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="48d19-385">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-385">JSON Configuration Provider</span></span>

<span data-ttu-id="48d19-386"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-386">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="48d19-387">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-387">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-388">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-388">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-389">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="48d19-389">Whether the file is optional.</span></span>
* <span data-ttu-id="48d19-390">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-390">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="48d19-391"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-391">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="48d19-392">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，会自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="48d19-392">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-393">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-393">The method is called to load configuration from:</span></span>

* <span data-ttu-id="48d19-394">appsettings.json &ndash; 首先读取此文件  。</span><span class="sxs-lookup"><span data-stu-id="48d19-394">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="48d19-395">该文件的环境版本可以替代 appsettings.json  文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-395">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="48d19-396">appsettings.{Environment}.json &ndash; 根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载文件的环境版本  。</span><span class="sxs-lookup"><span data-stu-id="48d19-396">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="48d19-397">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-397">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-398">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="48d19-398">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="48d19-399">环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-399">Environment variables.</span></span>
* <span data-ttu-id="48d19-400">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="48d19-400">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="48d19-401">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="48d19-401">Command-line arguments.</span></span>

<span data-ttu-id="48d19-402">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-402">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="48d19-403">因此，用户机密、环境变量和命令行参数会替代由 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-403">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="48d19-404">构建主机时调用 `ConfigureAppConfiguration` 以指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置：  </span><span class="sxs-lookup"><span data-stu-id="48d19-404">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="48d19-405">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-405">**Example**</span></span>

<span data-ttu-id="48d19-406">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用：</span><span class="sxs-lookup"><span data-stu-id="48d19-406">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="48d19-407">第一次调用 `AddJsonFile` 会从 appsettings 加载配置  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-407">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="48d19-408">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置  。</span><span class="sxs-lookup"><span data-stu-id="48d19-408">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="48d19-409">对于示例应用中的 appsettings.Development.json，将加载以下文件  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-409">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="48d19-410">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-410">Run the sample app.</span></span> <span data-ttu-id="48d19-411">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="48d19-411">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="48d19-412">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="48d19-412">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="48d19-413">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="48d19-413">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="48d19-414">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="48d19-414">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="48d19-415">打开 Properties/launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="48d19-415">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="48d19-416">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="48d19-416">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="48d19-417">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-417">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="48d19-418">appsettings.Development.json 中的设置不再替代 appsettings.json 中的设置   。</span><span class="sxs-lookup"><span data-stu-id="48d19-418">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="48d19-419">键 `Logging:LogLevel:Default` 的日志级别为 `Information`。</span><span class="sxs-lookup"><span data-stu-id="48d19-419">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="48d19-420">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-420">XML Configuration Provider</span></span>

<span data-ttu-id="48d19-421"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-421">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-422">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-422">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-423">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-423">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-424">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="48d19-424">Whether the file is optional.</span></span>
* <span data-ttu-id="48d19-425">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-425">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="48d19-426"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-426">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="48d19-427">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="48d19-427">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="48d19-428">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="48d19-428">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="48d19-429">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-429">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="48d19-430">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="48d19-430">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="48d19-431">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-431">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-432">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-432">section0:key0</span></span>
* <span data-ttu-id="48d19-433">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-433">section0:key1</span></span>
* <span data-ttu-id="48d19-434">section1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-434">section1:key0</span></span>
* <span data-ttu-id="48d19-435">section1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-435">section1:key1</span></span>

<span data-ttu-id="48d19-436">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="48d19-436">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="48d19-437">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-437">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-438">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-438">section:section0:key:key0</span></span>
* <span data-ttu-id="48d19-439">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-439">section:section0:key:key1</span></span>
* <span data-ttu-id="48d19-440">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-440">section:section1:key:key0</span></span>
* <span data-ttu-id="48d19-441">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-441">section:section1:key:key1</span></span>

<span data-ttu-id="48d19-442">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="48d19-442">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="48d19-443">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-443">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-444">key:attribute</span><span class="sxs-lookup"><span data-stu-id="48d19-444">key:attribute</span></span>
* <span data-ttu-id="48d19-445">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="48d19-445">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="48d19-446">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-446">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="48d19-447"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-447">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="48d19-448">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="48d19-448">The key is the file name.</span></span> <span data-ttu-id="48d19-449">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="48d19-449">The value contains the file's contents.</span></span> <span data-ttu-id="48d19-450">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="48d19-450">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="48d19-451">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-451">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="48d19-452">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="48d19-452">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="48d19-453">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-453">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-454">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="48d19-454">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="48d19-455">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="48d19-455">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="48d19-456">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="48d19-456">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="48d19-457">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="48d19-457">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="48d19-458">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-458">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="48d19-459">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-459">Memory Configuration Provider</span></span>

<span data-ttu-id="48d19-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="48d19-461">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-462">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="48d19-463">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-463">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="48d19-464">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="48d19-464">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="48d19-465">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-465">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="48d19-466">GetValue</span><span class="sxs-lookup"><span data-stu-id="48d19-466">GetValue</span></span>

<span data-ttu-id="48d19-467">[ConfigurationBinder.GetValueT\<>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将其转换为指定的非集合类型。</span><span class="sxs-lookup"><span data-stu-id="48d19-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="48d19-468">重载接受默认值。</span><span class="sxs-lookup"><span data-stu-id="48d19-468">An overload accepts a default value.</span></span>

<span data-ttu-id="48d19-469">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="48d19-469">The following example:</span></span>

* <span data-ttu-id="48d19-470">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="48d19-470">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="48d19-471">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="48d19-471">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="48d19-472">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="48d19-472">Types the value as an `int`.</span></span>
* <span data-ttu-id="48d19-473">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="48d19-473">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="48d19-474">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="48d19-474">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="48d19-475">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-475">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="48d19-476">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="48d19-476">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="48d19-477">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="48d19-477">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="48d19-478">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-478">section0:key0</span></span>
* <span data-ttu-id="48d19-479">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-479">section0:key1</span></span>
* <span data-ttu-id="48d19-480">section1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-480">section1:key0</span></span>
* <span data-ttu-id="48d19-481">section1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-481">section1:key1</span></span>
* <span data-ttu-id="48d19-482">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-482">section2:subsection0:key0</span></span>
* <span data-ttu-id="48d19-483">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-483">section2:subsection0:key1</span></span>
* <span data-ttu-id="48d19-484">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-484">section2:subsection1:key0</span></span>
* <span data-ttu-id="48d19-485">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-485">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="48d19-486">GetSection</span><span class="sxs-lookup"><span data-stu-id="48d19-486">GetSection</span></span>

<span data-ttu-id="48d19-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="48d19-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="48d19-488">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="48d19-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="48d19-489">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="48d19-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="48d19-490">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="48d19-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="48d19-491">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="48d19-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="48d19-492">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="48d19-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="48d19-493">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="48d19-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="48d19-494">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="48d19-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="48d19-495">GetChildren</span></span>

<span data-ttu-id="48d19-496">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="48d19-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="48d19-497">存在</span><span class="sxs-lookup"><span data-stu-id="48d19-497">Exists</span></span>

<span data-ttu-id="48d19-498">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="48d19-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="48d19-499">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="48d19-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="48d19-500">绑定至类</span><span class="sxs-lookup"><span data-stu-id="48d19-500">Bind to a class</span></span>

<span data-ttu-id="48d19-501">可以使用选项模式  将配置绑定到表示相关设置组的类。</span><span class="sxs-lookup"><span data-stu-id="48d19-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="48d19-502">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="48d19-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="48d19-503">配置值作为字符串返回，但调用 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以构造 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="48d19-504">绑定器将值绑定到所提供类型的所有公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="48d19-504">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="48d19-505">不会绑定字段  。</span><span class="sxs-lookup"><span data-stu-id="48d19-505">Fields are **not** bound.</span></span>

<span data-ttu-id="48d19-506">示例应用包含 `Starship` 模型 (Models/Starship.cs  )：</span><span class="sxs-lookup"><span data-stu-id="48d19-506">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="48d19-507">当示例应用使用 JSON 配置提供程序加载配置时，starship.json  文件的 `starship` 节会创建配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-507">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

<span data-ttu-id="48d19-508">创建以下配置键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-508">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="48d19-509">键</span><span class="sxs-lookup"><span data-stu-id="48d19-509">Key</span></span>                   | <span data-ttu-id="48d19-510">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-510">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="48d19-511">starship:name</span><span class="sxs-lookup"><span data-stu-id="48d19-511">starship:name</span></span>         | <span data-ttu-id="48d19-512">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="48d19-512">USS Enterprise</span></span>                                    |
| <span data-ttu-id="48d19-513">starship:registry</span><span class="sxs-lookup"><span data-stu-id="48d19-513">starship:registry</span></span>     | <span data-ttu-id="48d19-514">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="48d19-514">NCC-1701</span></span>                                          |
| <span data-ttu-id="48d19-515">starship:class</span><span class="sxs-lookup"><span data-stu-id="48d19-515">starship:class</span></span>        | <span data-ttu-id="48d19-516">Constitution</span><span class="sxs-lookup"><span data-stu-id="48d19-516">Constitution</span></span>                                      |
| <span data-ttu-id="48d19-517">starship:length</span><span class="sxs-lookup"><span data-stu-id="48d19-517">starship:length</span></span>       | <span data-ttu-id="48d19-518">304.8</span><span class="sxs-lookup"><span data-stu-id="48d19-518">304.8</span></span>                                             |
| <span data-ttu-id="48d19-519">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="48d19-519">starship:commissioned</span></span> | <span data-ttu-id="48d19-520">False</span><span class="sxs-lookup"><span data-stu-id="48d19-520">False</span></span>                                             |
| <span data-ttu-id="48d19-521">trademark</span><span class="sxs-lookup"><span data-stu-id="48d19-521">trademark</span></span>             | <span data-ttu-id="48d19-522">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="48d19-522">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="48d19-523">示例应用使用 `starship` 键调用 `GetSection`。</span><span class="sxs-lookup"><span data-stu-id="48d19-523">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="48d19-524">`starship` 键值对是独立的。</span><span class="sxs-lookup"><span data-stu-id="48d19-524">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="48d19-525">在子节传入 `Starship` 类的实例时调用 `Bind` 方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-525">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="48d19-526">绑定实例值后，将实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="48d19-526">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="48d19-527">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="48d19-527">Bind to an object graph</span></span>

<span data-ttu-id="48d19-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="48d19-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="48d19-529">与绑定简单对象一样，只绑定公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="48d19-529">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="48d19-530">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs  )：</span><span class="sxs-lookup"><span data-stu-id="48d19-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="48d19-531">示例应用有一个包含配置数据的 tvshow.xml  文件：</span><span class="sxs-lookup"><span data-stu-id="48d19-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="48d19-532">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="48d19-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="48d19-533">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="48d19-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="48d19-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定类型。</span><span class="sxs-lookup"><span data-stu-id="48d19-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="48d19-535">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="48d19-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="48d19-536">以下代码显示如何将 `Get<T>` 与前面的示例一起使用，该示例允许将绑定实例直接分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="48d19-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="48d19-537">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="48d19-537">Bind an array to a class</span></span>

<span data-ttu-id="48d19-538">示例应用演示了本部分中介绍的概念。 </span><span class="sxs-lookup"><span data-stu-id="48d19-538">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="48d19-539"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-539">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="48d19-540">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="48d19-540">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="48d19-541">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="48d19-541">Binding is provided by convention.</span></span> <span data-ttu-id="48d19-542">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="48d19-542">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="48d19-543">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="48d19-543">**In-memory array processing**</span></span>

<span data-ttu-id="48d19-544">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="48d19-544">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="48d19-545">键</span><span class="sxs-lookup"><span data-stu-id="48d19-545">Key</span></span>             | <span data-ttu-id="48d19-546">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-546">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="48d19-547">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="48d19-547">array:entries:0</span></span> | <span data-ttu-id="48d19-548">value0</span><span class="sxs-lookup"><span data-stu-id="48d19-548">value0</span></span> |
| <span data-ttu-id="48d19-549">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="48d19-549">array:entries:1</span></span> | <span data-ttu-id="48d19-550">value1</span><span class="sxs-lookup"><span data-stu-id="48d19-550">value1</span></span> |
| <span data-ttu-id="48d19-551">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="48d19-551">array:entries:2</span></span> | <span data-ttu-id="48d19-552">value2</span><span class="sxs-lookup"><span data-stu-id="48d19-552">value2</span></span> |
| <span data-ttu-id="48d19-553">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="48d19-553">array:entries:4</span></span> | <span data-ttu-id="48d19-554">value4</span><span class="sxs-lookup"><span data-stu-id="48d19-554">value4</span></span> |
| <span data-ttu-id="48d19-555">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="48d19-555">array:entries:5</span></span> | <span data-ttu-id="48d19-556">value5</span><span class="sxs-lookup"><span data-stu-id="48d19-556">value5</span></span> |

<span data-ttu-id="48d19-557">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="48d19-557">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="48d19-558">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-558">The array skips a value for index &num;3.</span></span> <span data-ttu-id="48d19-559">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="48d19-559">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="48d19-560">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="48d19-560">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="48d19-561">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="48d19-561">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="48d19-562">还可以使用 [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="48d19-562">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="48d19-563">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-563">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="48d19-564">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="48d19-564">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="48d19-565">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="48d19-565">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="48d19-566">0</span><span class="sxs-lookup"><span data-stu-id="48d19-566">0</span></span>                            | <span data-ttu-id="48d19-567">value0</span><span class="sxs-lookup"><span data-stu-id="48d19-567">value0</span></span>                       |
| <span data-ttu-id="48d19-568">1</span><span class="sxs-lookup"><span data-stu-id="48d19-568">1</span></span>                            | <span data-ttu-id="48d19-569">value1</span><span class="sxs-lookup"><span data-stu-id="48d19-569">value1</span></span>                       |
| <span data-ttu-id="48d19-570">2</span><span class="sxs-lookup"><span data-stu-id="48d19-570">2</span></span>                            | <span data-ttu-id="48d19-571">value2</span><span class="sxs-lookup"><span data-stu-id="48d19-571">value2</span></span>                       |
| <span data-ttu-id="48d19-572">3</span><span class="sxs-lookup"><span data-stu-id="48d19-572">3</span></span>                            | <span data-ttu-id="48d19-573">value4</span><span class="sxs-lookup"><span data-stu-id="48d19-573">value4</span></span>                       |
| <span data-ttu-id="48d19-574">4</span><span class="sxs-lookup"><span data-stu-id="48d19-574">4</span></span>                            | <span data-ttu-id="48d19-575">value5</span><span class="sxs-lookup"><span data-stu-id="48d19-575">value5</span></span>                       |

<span data-ttu-id="48d19-576">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="48d19-576">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="48d19-577">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-577">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="48d19-578">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="48d19-578">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="48d19-579">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="48d19-579">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="48d19-580">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="48d19-580">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="48d19-581">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="48d19-581">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="48d19-582">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="48d19-582">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="48d19-583">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="48d19-583">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="48d19-584">键</span><span class="sxs-lookup"><span data-stu-id="48d19-584">Key</span></span>             | <span data-ttu-id="48d19-585">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-585">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="48d19-586">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="48d19-586">array:entries:3</span></span> | <span data-ttu-id="48d19-587">value3</span><span class="sxs-lookup"><span data-stu-id="48d19-587">value3</span></span> |

<span data-ttu-id="48d19-588">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="48d19-588">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="48d19-589">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="48d19-589">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="48d19-590">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="48d19-590">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="48d19-591">0</span><span class="sxs-lookup"><span data-stu-id="48d19-591">0</span></span>                            | <span data-ttu-id="48d19-592">value0</span><span class="sxs-lookup"><span data-stu-id="48d19-592">value0</span></span>                       |
| <span data-ttu-id="48d19-593">1</span><span class="sxs-lookup"><span data-stu-id="48d19-593">1</span></span>                            | <span data-ttu-id="48d19-594">value1</span><span class="sxs-lookup"><span data-stu-id="48d19-594">value1</span></span>                       |
| <span data-ttu-id="48d19-595">2</span><span class="sxs-lookup"><span data-stu-id="48d19-595">2</span></span>                            | <span data-ttu-id="48d19-596">value2</span><span class="sxs-lookup"><span data-stu-id="48d19-596">value2</span></span>                       |
| <span data-ttu-id="48d19-597">3</span><span class="sxs-lookup"><span data-stu-id="48d19-597">3</span></span>                            | <span data-ttu-id="48d19-598">value3</span><span class="sxs-lookup"><span data-stu-id="48d19-598">value3</span></span>                       |
| <span data-ttu-id="48d19-599">4</span><span class="sxs-lookup"><span data-stu-id="48d19-599">4</span></span>                            | <span data-ttu-id="48d19-600">value4</span><span class="sxs-lookup"><span data-stu-id="48d19-600">value4</span></span>                       |
| <span data-ttu-id="48d19-601">5</span><span class="sxs-lookup"><span data-stu-id="48d19-601">5</span></span>                            | <span data-ttu-id="48d19-602">value5</span><span class="sxs-lookup"><span data-stu-id="48d19-602">value5</span></span>                       |

<span data-ttu-id="48d19-603">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="48d19-603">**JSON array processing**</span></span>

<span data-ttu-id="48d19-604">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="48d19-604">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="48d19-605">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="48d19-605">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="48d19-606">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-606">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="48d19-607">键</span><span class="sxs-lookup"><span data-stu-id="48d19-607">Key</span></span>                     | <span data-ttu-id="48d19-608">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-608">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="48d19-609">json_array:key</span><span class="sxs-lookup"><span data-stu-id="48d19-609">json_array:key</span></span>          | <span data-ttu-id="48d19-610">valueA</span><span class="sxs-lookup"><span data-stu-id="48d19-610">valueA</span></span> |
| <span data-ttu-id="48d19-611">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="48d19-611">json_array:subsection:0</span></span> | <span data-ttu-id="48d19-612">valueB</span><span class="sxs-lookup"><span data-stu-id="48d19-612">valueB</span></span> |
| <span data-ttu-id="48d19-613">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="48d19-613">json_array:subsection:1</span></span> | <span data-ttu-id="48d19-614">valueC</span><span class="sxs-lookup"><span data-stu-id="48d19-614">valueC</span></span> |
| <span data-ttu-id="48d19-615">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="48d19-615">json_array:subsection:2</span></span> | <span data-ttu-id="48d19-616">valueD</span><span class="sxs-lookup"><span data-stu-id="48d19-616">valueD</span></span> |

<span data-ttu-id="48d19-617">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-617">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="48d19-618">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="48d19-618">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="48d19-619">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="48d19-619">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="48d19-620">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="48d19-620">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="48d19-621">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="48d19-621">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="48d19-622">0</span><span class="sxs-lookup"><span data-stu-id="48d19-622">0</span></span>                                   | <span data-ttu-id="48d19-623">valueB</span><span class="sxs-lookup"><span data-stu-id="48d19-623">valueB</span></span>                              |
| <span data-ttu-id="48d19-624">1</span><span class="sxs-lookup"><span data-stu-id="48d19-624">1</span></span>                                   | <span data-ttu-id="48d19-625">valueC</span><span class="sxs-lookup"><span data-stu-id="48d19-625">valueC</span></span>                              |
| <span data-ttu-id="48d19-626">2</span><span class="sxs-lookup"><span data-stu-id="48d19-626">2</span></span>                                   | <span data-ttu-id="48d19-627">valueD</span><span class="sxs-lookup"><span data-stu-id="48d19-627">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="48d19-628">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-628">Custom configuration provider</span></span>

<span data-ttu-id="48d19-629">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-629">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="48d19-630">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="48d19-630">The provider has the following characteristics:</span></span>

* <span data-ttu-id="48d19-631">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="48d19-631">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="48d19-632">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="48d19-632">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="48d19-633">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-633">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="48d19-634">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="48d19-634">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="48d19-635">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="48d19-635">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="48d19-636">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="48d19-636">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="48d19-637">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-637">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="48d19-638">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-638">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="48d19-639">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="48d19-640">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="48d19-640">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="48d19-641">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="48d19-642">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-642">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="48d19-643">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="48d19-643">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="48d19-644">由于[配置密钥不区分大小写](#keys)，因此用来初始化数据库的字典是用不区分大小写的比较程序 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 创建的。</span><span class="sxs-lookup"><span data-stu-id="48d19-644">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="48d19-645">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="48d19-646">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="48d19-646">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="48d19-647">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-647">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="48d19-648">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="48d19-648">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="48d19-649">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="48d19-649">Access configuration during startup</span></span>

<span data-ttu-id="48d19-650">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="48d19-650">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="48d19-651">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="48d19-651">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="48d19-652">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="48d19-652">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="48d19-653">在 Razor Pages 页或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="48d19-653">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="48d19-654">若要访问 Razor Pages 页或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration 命名空间](xref:Microsoft.Extensions.Configuration)添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入页面或视图。</span><span class="sxs-lookup"><span data-stu-id="48d19-654">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="48d19-655">在 Razor 页面页中：</span><span class="sxs-lookup"><span data-stu-id="48d19-655">In a Razor Pages page:</span></span>

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

<span data-ttu-id="48d19-656">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="48d19-656">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="48d19-657">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="48d19-657">Add configuration from an external assembly</span></span>

<span data-ttu-id="48d19-658">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="48d19-658">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="48d19-659">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="48d19-659">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48d19-660">其他资源</span><span class="sxs-lookup"><span data-stu-id="48d19-660">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="48d19-661">ASP.NET Core 中的应用配置基于配置提供程序  建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-661">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="48d19-662">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-662">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="48d19-663">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="48d19-663">Azure Key Vault</span></span>
* <span data-ttu-id="48d19-664">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="48d19-664">Azure App Configuration</span></span>
* <span data-ttu-id="48d19-665">命令行参数</span><span class="sxs-lookup"><span data-stu-id="48d19-665">Command-line arguments</span></span>
* <span data-ttu-id="48d19-666">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-666">Custom providers (installed or created)</span></span>
* <span data-ttu-id="48d19-667">目录文件</span><span class="sxs-lookup"><span data-stu-id="48d19-667">Directory files</span></span>
* <span data-ttu-id="48d19-668">环境变量</span><span class="sxs-lookup"><span data-stu-id="48d19-668">Environment variables</span></span>
* <span data-ttu-id="48d19-669">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="48d19-669">In-memory .NET objects</span></span>
* <span data-ttu-id="48d19-670">设置文件</span><span class="sxs-lookup"><span data-stu-id="48d19-670">Settings files</span></span>

<span data-ttu-id="48d19-671">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="48d19-671">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="48d19-672">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="48d19-672">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="48d19-673">选项模式  是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="48d19-673">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="48d19-674">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="48d19-674">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="48d19-675">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="48d19-675">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="48d19-676">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="48d19-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="48d19-677">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="48d19-677">Host versus app configuration</span></span>

<span data-ttu-id="48d19-678">在配置并启动应用之前，配置并启动主机  。</span><span class="sxs-lookup"><span data-stu-id="48d19-678">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="48d19-679">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="48d19-679">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="48d19-680">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-680">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="48d19-681">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-681">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="48d19-682">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="48d19-682">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="48d19-683">其他配置</span><span class="sxs-lookup"><span data-stu-id="48d19-683">Other configuration</span></span>

<span data-ttu-id="48d19-684">本主题仅与应用配置相关  。</span><span class="sxs-lookup"><span data-stu-id="48d19-684">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="48d19-685">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-685">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="48d19-686">launch.json  /launchSettings.json  是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="48d19-686">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="48d19-687"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="48d19-687">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="48d19-688">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-688">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="48d19-689">web.config  是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="48d19-689">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="48d19-690">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="48d19-690">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="48d19-691">默认配置</span><span class="sxs-lookup"><span data-stu-id="48d19-691">Default configuration</span></span>

<span data-ttu-id="48d19-692">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="48d19-692">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="48d19-693">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-693">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="48d19-694">以下内容适用于使用 [Web 主机](xref:fundamentals/host/web-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-694">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="48d19-695">有关使用[通用主机](xref:fundamentals/host/generic-host)时默认配置的详细信息，请参阅[本主题的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="48d19-695">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="48d19-696">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="48d19-696">Host configuration is provided from:</span></span>
  * <span data-ttu-id="48d19-697">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-697">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="48d19-698">在配置键值对加载后，前缀 (`ASPNETCORE_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="48d19-698">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="48d19-699">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-699">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="48d19-700">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="48d19-700">App configuration is provided from:</span></span>
  * <span data-ttu-id="48d19-701">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.json  提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-701">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="48d19-702">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json  提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-702">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="48d19-703">应用在使用入口程序集的 `Development` 环境中运行时的[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="48d19-703">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="48d19-704">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-704">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="48d19-705">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-705">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="48d19-706">安全性</span><span class="sxs-lookup"><span data-stu-id="48d19-706">Security</span></span>

<span data-ttu-id="48d19-707">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="48d19-707">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="48d19-708">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-708">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="48d19-709">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="48d19-709">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="48d19-710">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="48d19-710">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="48d19-711">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="48d19-711">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="48d19-712"><xref:security/app-secrets> &ndash; 包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="48d19-712"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="48d19-713">Secret Manager 使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="48d19-713">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="48d19-714">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-714">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="48d19-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="48d19-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="48d19-716">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="48d19-716">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="48d19-717">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="48d19-717">Hierarchical configuration data</span></span>

<span data-ttu-id="48d19-718">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-718">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="48d19-719">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="48d19-719">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="48d19-720">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="48d19-720">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="48d19-721">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="48d19-721">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="48d19-722">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-722">section0:key0</span></span>
* <span data-ttu-id="48d19-723">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-723">section0:key1</span></span>
* <span data-ttu-id="48d19-724">section1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-724">section1:key0</span></span>
* <span data-ttu-id="48d19-725">section1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-725">section1:key1</span></span>

<span data-ttu-id="48d19-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="48d19-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="48d19-727">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-727">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="48d19-728">约定</span><span class="sxs-lookup"><span data-stu-id="48d19-728">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="48d19-729">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-729">Sources and providers</span></span>

<span data-ttu-id="48d19-730">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="48d19-730">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="48d19-731">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-731">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="48d19-732">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="48d19-732">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="48d19-733">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="48d19-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="48d19-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 来获取以下类的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="48d19-735">在下面的示例中，使用 `_config` 字段来访问配置值：</span><span class="sxs-lookup"><span data-stu-id="48d19-735">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="48d19-736">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="48d19-736">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="48d19-737">键</span><span class="sxs-lookup"><span data-stu-id="48d19-737">Keys</span></span>

<span data-ttu-id="48d19-738">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="48d19-738">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="48d19-739">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="48d19-739">Keys are case-insensitive.</span></span> <span data-ttu-id="48d19-740">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="48d19-740">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="48d19-741">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-741">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="48d19-742">分层键</span><span class="sxs-lookup"><span data-stu-id="48d19-742">Hierarchical keys</span></span>
  * <span data-ttu-id="48d19-743">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="48d19-743">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="48d19-744">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="48d19-744">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="48d19-745">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="48d19-745">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="48d19-746">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="48d19-746">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="48d19-747">将机密加载到应用的配置中时，用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="48d19-747">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="48d19-748"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-748">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="48d19-749">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="48d19-749">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="48d19-750">值</span><span class="sxs-lookup"><span data-stu-id="48d19-750">Values</span></span>

<span data-ttu-id="48d19-751">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="48d19-751">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="48d19-752">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="48d19-752">Values are strings.</span></span>
* <span data-ttu-id="48d19-753">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-753">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="48d19-754">提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-754">Providers</span></span>

<span data-ttu-id="48d19-755">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-755">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="48d19-756">提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-756">Provider</span></span> | <span data-ttu-id="48d19-757">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="48d19-757">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="48d19-758">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="48d19-758">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="48d19-759">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="48d19-759">Azure Key Vault</span></span> |
| <span data-ttu-id="48d19-760">[Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="48d19-760">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="48d19-761">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="48d19-761">Azure App Configuration</span></span> |
| [<span data-ttu-id="48d19-762">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-762">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="48d19-763">命令行参数</span><span class="sxs-lookup"><span data-stu-id="48d19-763">Command-line parameters</span></span> |
| [<span data-ttu-id="48d19-764">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-764">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="48d19-765">自定义源</span><span class="sxs-lookup"><span data-stu-id="48d19-765">Custom source</span></span> |
| [<span data-ttu-id="48d19-766">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-766">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="48d19-767">环境变量</span><span class="sxs-lookup"><span data-stu-id="48d19-767">Environment variables</span></span> |
| [<span data-ttu-id="48d19-768">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-768">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="48d19-769">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="48d19-769">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="48d19-770">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-770">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="48d19-771">目录文件</span><span class="sxs-lookup"><span data-stu-id="48d19-771">Directory files</span></span> |
| [<span data-ttu-id="48d19-772">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-772">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="48d19-773">内存中集合</span><span class="sxs-lookup"><span data-stu-id="48d19-773">In-memory collections</span></span> |
| <span data-ttu-id="48d19-774">[用户机密 (Secret Manager)](xref:security/app-secrets)（安全  主题）</span><span class="sxs-lookup"><span data-stu-id="48d19-774">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="48d19-775">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="48d19-775">File in the user profile directory</span></span> |

<span data-ttu-id="48d19-776">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="48d19-776">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="48d19-777">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="48d19-777">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="48d19-778">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="48d19-778">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="48d19-779">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="48d19-779">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="48d19-780">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境）  </span><span class="sxs-lookup"><span data-stu-id="48d19-780">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="48d19-781">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="48d19-781">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="48d19-782">[用户机密 (Secret Manager)](xref:security/app-secrets)（仅限开发环境中）</span><span class="sxs-lookup"><span data-stu-id="48d19-782">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="48d19-783">环境变量</span><span class="sxs-lookup"><span data-stu-id="48d19-783">Environment variables</span></span>
1. <span data-ttu-id="48d19-784">命令行参数</span><span class="sxs-lookup"><span data-stu-id="48d19-784">Command-line arguments</span></span>

<span data-ttu-id="48d19-785">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-785">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="48d19-786">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="48d19-786">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-787">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-787">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="48d19-788">用 UseConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="48d19-788">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="48d19-789">若要配置主机生成器，请使用配置在主机生成器上调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="48d19-789">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="48d19-790">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="48d19-790">ConfigureAppConfiguration</span></span>

<span data-ttu-id="48d19-791">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="48d19-791">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="48d19-792">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="48d19-792">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="48d19-793">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="48d19-793">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="48d19-794">删除 CreateDefaultBuilder 添加的提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-794">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="48d19-795">要删除 `CreateDefaultBuilder` 添加的提供程序，请先对 [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) 调用 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)：</span><span class="sxs-lookup"><span data-stu-id="48d19-795">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="48d19-796">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="48d19-796">Consume configuration during app startup</span></span>

<span data-ttu-id="48d19-797">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="48d19-797">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="48d19-798">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-798">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="48d19-799">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-799">Command-line Configuration Provider</span></span>

<span data-ttu-id="48d19-800"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-800">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-801">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-801">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-802">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="48d19-802">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="48d19-803">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-803">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-804">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="48d19-804">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="48d19-805">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="48d19-805">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="48d19-806">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="48d19-806">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="48d19-807">环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-807">Environment variables.</span></span>

<span data-ttu-id="48d19-808">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-808">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="48d19-809">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-809">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="48d19-810">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="48d19-810">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="48d19-811">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="48d19-811">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="48d19-812">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="48d19-812">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-813">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="48d19-813">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="48d19-814">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-814">**Example**</span></span>

<span data-ttu-id="48d19-815">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="48d19-815">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="48d19-816">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="48d19-816">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="48d19-817">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="48d19-817">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="48d19-818">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="48d19-818">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="48d19-819">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-819">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="48d19-820">自变量</span><span class="sxs-lookup"><span data-stu-id="48d19-820">Arguments</span></span>

<span data-ttu-id="48d19-821">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="48d19-821">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="48d19-822">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="48d19-822">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="48d19-823">键前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-823">Key prefix</span></span>               | <span data-ttu-id="48d19-824">示例</span><span class="sxs-lookup"><span data-stu-id="48d19-824">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="48d19-825">无前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-825">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="48d19-826">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="48d19-826">Two dashes (`--`)</span></span>        | <span data-ttu-id="48d19-827">`--CommandLineKey2=value2`，`--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="48d19-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="48d19-828">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="48d19-828">Forward slash (`/`)</span></span>      | <span data-ttu-id="48d19-829">`/CommandLineKey3=value3`，`/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="48d19-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="48d19-830">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="48d19-830">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="48d19-831">示例命令：</span><span class="sxs-lookup"><span data-stu-id="48d19-831">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="48d19-832">交换映射</span><span class="sxs-lookup"><span data-stu-id="48d19-832">Switch mappings</span></span>

<span data-ttu-id="48d19-833">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="48d19-833">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="48d19-834">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，需要为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="48d19-834">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="48d19-835">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="48d19-835">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="48d19-836">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-836">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="48d19-837">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="48d19-837">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="48d19-838">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="48d19-838">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="48d19-839">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="48d19-839">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="48d19-840">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="48d19-840">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="48d19-841">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="48d19-841">Create a switch mappings dictionary.</span></span> <span data-ttu-id="48d19-842">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="48d19-842">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="48d19-843">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="48d19-843">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="48d19-844">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="48d19-844">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="48d19-845">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="48d19-845">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-846">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="48d19-846">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="48d19-847">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-847">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="48d19-848">键</span><span class="sxs-lookup"><span data-stu-id="48d19-848">Key</span></span>       | <span data-ttu-id="48d19-849">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-849">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="48d19-850">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="48d19-850">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="48d19-851">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-851">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="48d19-852">键</span><span class="sxs-lookup"><span data-stu-id="48d19-852">Key</span></span>               | <span data-ttu-id="48d19-853">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-853">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="48d19-854">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-854">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="48d19-855"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-855">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-856">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-856">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="48d19-857">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="48d19-858">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="48d19-858">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="48d19-859">如果使用 [Web 主机](xref:fundamentals/host/web-host)初始化新的主机生成器，且调用 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-859">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="48d19-860">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-860">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-861">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="48d19-861">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="48d19-862">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="48d19-862">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="48d19-863">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置   。</span><span class="sxs-lookup"><span data-stu-id="48d19-863">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="48d19-864">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="48d19-864">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="48d19-865">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="48d19-865">Command-line arguments.</span></span>

<span data-ttu-id="48d19-866">环境变量配置提供程序是在配置已根据用户机密和 appsettings  文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="48d19-866">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="48d19-867">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-867">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="48d19-868">要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`：</span><span class="sxs-lookup"><span data-stu-id="48d19-868">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="48d19-869">最后调用 `AddEnvironmentVariables`让带给定前缀的环境变量可替代其他提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-869">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="48d19-870">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-870">**Example**</span></span>

<span data-ttu-id="48d19-871">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="48d19-871">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="48d19-872">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-872">Run the sample app.</span></span> <span data-ttu-id="48d19-873">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="48d19-873">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="48d19-874">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-874">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="48d19-875">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="48d19-875">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="48d19-876">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-876">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="48d19-877">请参阅示例应用的“Pages/Index.cshtml.cs”文件  。</span><span class="sxs-lookup"><span data-stu-id="48d19-877">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="48d19-878">要公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-878">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="48d19-879">前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-879">Prefixes</span></span>

<span data-ttu-id="48d19-880">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-880">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="48d19-881">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="48d19-881">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="48d19-882">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="48d19-882">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="48d19-883">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="48d19-883">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="48d19-884">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-884">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-885">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="48d19-885">**Connection string prefixes**</span></span>

<span data-ttu-id="48d19-886">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="48d19-886">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="48d19-887">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="48d19-887">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="48d19-888">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="48d19-888">Connection string prefix</span></span> | <span data-ttu-id="48d19-889">提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-889">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="48d19-890">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-890">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="48d19-891">MySQL</span><span class="sxs-lookup"><span data-stu-id="48d19-891">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="48d19-892">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="48d19-892">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="48d19-893">SQL Server</span><span class="sxs-lookup"><span data-stu-id="48d19-893">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="48d19-894">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="48d19-894">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="48d19-895">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="48d19-895">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="48d19-896">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="48d19-896">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="48d19-897">环境变量键</span><span class="sxs-lookup"><span data-stu-id="48d19-897">Environment variable key</span></span> | <span data-ttu-id="48d19-898">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="48d19-898">Converted configuration key</span></span> | <span data-ttu-id="48d19-899">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="48d19-899">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-900">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="48d19-900">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-901">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="48d19-901">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="48d19-902">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="48d19-902">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-903">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="48d19-903">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="48d19-904">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="48d19-904">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="48d19-905">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="48d19-905">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="48d19-906">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="48d19-906">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="48d19-907">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-907">**Example**</span></span>

<span data-ttu-id="48d19-908">在服务器上创建了一个自定义连接字符串环境变量：</span><span class="sxs-lookup"><span data-stu-id="48d19-908">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="48d19-909">名称 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="48d19-909">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="48d19-910">值 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="48d19-910">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="48d19-911">如果 `IConfiguration` 已引入并分配给名为 `_config` 的字段，请读取值：</span><span class="sxs-lookup"><span data-stu-id="48d19-911">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="48d19-912">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-912">File Configuration Provider</span></span>

<span data-ttu-id="48d19-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="48d19-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="48d19-914">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="48d19-914">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="48d19-915">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-915">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="48d19-916">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-916">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="48d19-917">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-917">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="48d19-918">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-918">INI Configuration Provider</span></span>

<span data-ttu-id="48d19-919"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-919">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-920">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-920">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-921">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="48d19-921">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="48d19-922">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-922">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-923">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="48d19-923">Whether the file is optional.</span></span>
* <span data-ttu-id="48d19-924">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-924">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="48d19-925"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-925">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="48d19-926">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-926">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="48d19-927">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="48d19-927">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="48d19-928">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-928">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-929">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-929">section0:key0</span></span>
* <span data-ttu-id="48d19-930">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-930">section0:key1</span></span>
* <span data-ttu-id="48d19-931">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="48d19-931">section1:subsection:key</span></span>
* <span data-ttu-id="48d19-932">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="48d19-932">section2:subsection0:key</span></span>
* <span data-ttu-id="48d19-933">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="48d19-933">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="48d19-934">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-934">JSON Configuration Provider</span></span>

<span data-ttu-id="48d19-935"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-935">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="48d19-936">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-936">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-937">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-937">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-938">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="48d19-938">Whether the file is optional.</span></span>
* <span data-ttu-id="48d19-939">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-939">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="48d19-940"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-940">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="48d19-941">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，会自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="48d19-941">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="48d19-942">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-942">The method is called to load configuration from:</span></span>

* <span data-ttu-id="48d19-943">appsettings.json &ndash; 首先读取此文件  。</span><span class="sxs-lookup"><span data-stu-id="48d19-943">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="48d19-944">该文件的环境版本可以替代 appsettings.json  文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-944">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="48d19-945">appsettings.{Environment}.json &ndash; 根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载文件的环境版本  。</span><span class="sxs-lookup"><span data-stu-id="48d19-945">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="48d19-946">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-946">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="48d19-947">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="48d19-947">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="48d19-948">环境变量。</span><span class="sxs-lookup"><span data-stu-id="48d19-948">Environment variables.</span></span>
* <span data-ttu-id="48d19-949">[用户机密 (Secret Manager)](xref:security/app-secrets)（在开发环境中）。</span><span class="sxs-lookup"><span data-stu-id="48d19-949">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="48d19-950">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="48d19-950">Command-line arguments.</span></span>

<span data-ttu-id="48d19-951">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-951">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="48d19-952">因此，用户机密、环境变量和命令行参数会替代由 appsettings  文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-952">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="48d19-953">构建主机时调用 `ConfigureAppConfiguration` 以指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置：  </span><span class="sxs-lookup"><span data-stu-id="48d19-953">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="48d19-954">**示例**</span><span class="sxs-lookup"><span data-stu-id="48d19-954">**Example**</span></span>

<span data-ttu-id="48d19-955">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用：</span><span class="sxs-lookup"><span data-stu-id="48d19-955">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="48d19-956">第一次调用 `AddJsonFile` 会从 appsettings 加载配置  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-956">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="48d19-957">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置  。</span><span class="sxs-lookup"><span data-stu-id="48d19-957">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="48d19-958">对于示例应用中的 appsettings.Development.json，将加载以下文件  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-958">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="48d19-959">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-959">Run the sample app.</span></span> <span data-ttu-id="48d19-960">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="48d19-960">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="48d19-961">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="48d19-961">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="48d19-962">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="48d19-962">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="48d19-963">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="48d19-963">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="48d19-964">打开 Properties/launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="48d19-964">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="48d19-965">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="48d19-965">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="48d19-966">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="48d19-966">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="48d19-967">appsettings.Development.json 中的设置不再替代 appsettings.json 中的设置   。</span><span class="sxs-lookup"><span data-stu-id="48d19-967">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="48d19-968">键 `Logging:LogLevel:Default` 的日志级别为 `Warning`。</span><span class="sxs-lookup"><span data-stu-id="48d19-968">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="48d19-969">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-969">XML Configuration Provider</span></span>

<span data-ttu-id="48d19-970"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-970">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="48d19-971">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-971">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-972">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-972">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-973">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="48d19-973">Whether the file is optional.</span></span>
* <span data-ttu-id="48d19-974">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-974">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="48d19-975"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-975">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="48d19-976">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="48d19-976">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="48d19-977">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="48d19-977">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="48d19-978">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-978">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="48d19-979">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="48d19-979">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="48d19-980">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-980">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-981">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-981">section0:key0</span></span>
* <span data-ttu-id="48d19-982">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-982">section0:key1</span></span>
* <span data-ttu-id="48d19-983">section1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-983">section1:key0</span></span>
* <span data-ttu-id="48d19-984">section1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-984">section1:key1</span></span>

<span data-ttu-id="48d19-985">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="48d19-985">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="48d19-986">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-986">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-987">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-987">section:section0:key:key0</span></span>
* <span data-ttu-id="48d19-988">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-988">section:section0:key:key1</span></span>
* <span data-ttu-id="48d19-989">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-989">section:section1:key:key0</span></span>
* <span data-ttu-id="48d19-990">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-990">section:section1:key:key1</span></span>

<span data-ttu-id="48d19-991">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="48d19-991">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="48d19-992">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="48d19-992">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="48d19-993">key:attribute</span><span class="sxs-lookup"><span data-stu-id="48d19-993">key:attribute</span></span>
* <span data-ttu-id="48d19-994">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="48d19-994">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="48d19-995">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-995">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="48d19-996"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-996">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="48d19-997">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="48d19-997">The key is the file name.</span></span> <span data-ttu-id="48d19-998">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="48d19-998">The value contains the file's contents.</span></span> <span data-ttu-id="48d19-999">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="48d19-999">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="48d19-1000">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-1000">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="48d19-1001">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="48d19-1001">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="48d19-1002">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="48d19-1002">Overloads permit specifying:</span></span>

* <span data-ttu-id="48d19-1003">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="48d19-1003">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="48d19-1004">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="48d19-1004">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="48d19-1005">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="48d19-1005">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="48d19-1006">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1006">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="48d19-1007">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-1007">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="48d19-1008">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-1008">Memory Configuration Provider</span></span>

<span data-ttu-id="48d19-1009"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="48d19-1009">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="48d19-1010">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-1010">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="48d19-1011">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-1011">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="48d19-1012">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-1012">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="48d19-1013">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="48d19-1013">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="48d19-1014">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-1014">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="48d19-1015">GetValue</span><span class="sxs-lookup"><span data-stu-id="48d19-1015">GetValue</span></span>

<span data-ttu-id="48d19-1016">[ConfigurationBinder.GetValueT\<>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将其转换为指定的非集合类型。</span><span class="sxs-lookup"><span data-stu-id="48d19-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="48d19-1017">重载接受默认值。</span><span class="sxs-lookup"><span data-stu-id="48d19-1017">An overload accepts a default value.</span></span>

<span data-ttu-id="48d19-1018">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="48d19-1018">The following example:</span></span>

* <span data-ttu-id="48d19-1019">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="48d19-1019">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="48d19-1020">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1020">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="48d19-1021">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1021">Types the value as an `int`.</span></span>
* <span data-ttu-id="48d19-1022">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="48d19-1022">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="48d19-1023">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="48d19-1023">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="48d19-1024">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="48d19-1024">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="48d19-1025">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="48d19-1025">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="48d19-1026">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="48d19-1026">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="48d19-1027">section0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-1027">section0:key0</span></span>
* <span data-ttu-id="48d19-1028">section0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-1028">section0:key1</span></span>
* <span data-ttu-id="48d19-1029">section1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-1029">section1:key0</span></span>
* <span data-ttu-id="48d19-1030">section1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-1030">section1:key1</span></span>
* <span data-ttu-id="48d19-1031">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-1031">section2:subsection0:key0</span></span>
* <span data-ttu-id="48d19-1032">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-1032">section2:subsection0:key1</span></span>
* <span data-ttu-id="48d19-1033">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="48d19-1033">section2:subsection1:key0</span></span>
* <span data-ttu-id="48d19-1034">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="48d19-1034">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="48d19-1035">GetSection</span><span class="sxs-lookup"><span data-stu-id="48d19-1035">GetSection</span></span>

<span data-ttu-id="48d19-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="48d19-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="48d19-1037">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="48d19-1037">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="48d19-1038">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="48d19-1038">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="48d19-1039">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="48d19-1039">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="48d19-1040">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1040">`GetSection` never returns `null`.</span></span> <span data-ttu-id="48d19-1041">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1041">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="48d19-1042">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="48d19-1042">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="48d19-1043">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="48d19-1043">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="48d19-1044">GetChildren</span><span class="sxs-lookup"><span data-stu-id="48d19-1044">GetChildren</span></span>

<span data-ttu-id="48d19-1045">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="48d19-1045">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="48d19-1046">存在</span><span class="sxs-lookup"><span data-stu-id="48d19-1046">Exists</span></span>

<span data-ttu-id="48d19-1047">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="48d19-1047">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="48d19-1048">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="48d19-1048">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="48d19-1049">绑定至类</span><span class="sxs-lookup"><span data-stu-id="48d19-1049">Bind to a class</span></span>

<span data-ttu-id="48d19-1050">可以使用选项模式  将配置绑定到表示相关设置组的类。</span><span class="sxs-lookup"><span data-stu-id="48d19-1050">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="48d19-1051">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="48d19-1051">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="48d19-1052">配置值作为字符串返回，但调用 <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 可以构造 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-1052">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="48d19-1053">绑定器将值绑定到所提供类型的所有公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="48d19-1053">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="48d19-1054">不会绑定字段  。</span><span class="sxs-lookup"><span data-stu-id="48d19-1054">Fields are **not** bound.</span></span>

<span data-ttu-id="48d19-1055">示例应用包含 `Starship` 模型 (Models/Starship.cs  )：</span><span class="sxs-lookup"><span data-stu-id="48d19-1055">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="48d19-1056">当示例应用使用 JSON 配置提供程序加载配置时，starship.json  文件的 `starship` 节会创建配置：</span><span class="sxs-lookup"><span data-stu-id="48d19-1056">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="48d19-1057">创建以下配置键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-1057">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="48d19-1058">键</span><span class="sxs-lookup"><span data-stu-id="48d19-1058">Key</span></span>                   | <span data-ttu-id="48d19-1059">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-1059">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="48d19-1060">starship:name</span><span class="sxs-lookup"><span data-stu-id="48d19-1060">starship:name</span></span>         | <span data-ttu-id="48d19-1061">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="48d19-1061">USS Enterprise</span></span>                                    |
| <span data-ttu-id="48d19-1062">starship:registry</span><span class="sxs-lookup"><span data-stu-id="48d19-1062">starship:registry</span></span>     | <span data-ttu-id="48d19-1063">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="48d19-1063">NCC-1701</span></span>                                          |
| <span data-ttu-id="48d19-1064">starship:class</span><span class="sxs-lookup"><span data-stu-id="48d19-1064">starship:class</span></span>        | <span data-ttu-id="48d19-1065">Constitution</span><span class="sxs-lookup"><span data-stu-id="48d19-1065">Constitution</span></span>                                      |
| <span data-ttu-id="48d19-1066">starship:length</span><span class="sxs-lookup"><span data-stu-id="48d19-1066">starship:length</span></span>       | <span data-ttu-id="48d19-1067">304.8</span><span class="sxs-lookup"><span data-stu-id="48d19-1067">304.8</span></span>                                             |
| <span data-ttu-id="48d19-1068">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="48d19-1068">starship:commissioned</span></span> | <span data-ttu-id="48d19-1069">False</span><span class="sxs-lookup"><span data-stu-id="48d19-1069">False</span></span>                                             |
| <span data-ttu-id="48d19-1070">trademark</span><span class="sxs-lookup"><span data-stu-id="48d19-1070">trademark</span></span>             | <span data-ttu-id="48d19-1071">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="48d19-1071">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="48d19-1072">示例应用使用 `starship` 键调用 `GetSection`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1072">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="48d19-1073">`starship` 键值对是独立的。</span><span class="sxs-lookup"><span data-stu-id="48d19-1073">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="48d19-1074">在子节传入 `Starship` 类的实例时调用 `Bind` 方法。</span><span class="sxs-lookup"><span data-stu-id="48d19-1074">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="48d19-1075">绑定实例值后，将实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="48d19-1075">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="48d19-1076">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="48d19-1076">Bind to an object graph</span></span>

<span data-ttu-id="48d19-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="48d19-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="48d19-1078">与绑定简单对象一样，只绑定公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="48d19-1078">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="48d19-1079">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs  )：</span><span class="sxs-lookup"><span data-stu-id="48d19-1079">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="48d19-1080">示例应用有一个包含配置数据的 tvshow.xml  文件：</span><span class="sxs-lookup"><span data-stu-id="48d19-1080">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="48d19-1081">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="48d19-1081">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="48d19-1082">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="48d19-1082">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="48d19-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定类型。</span><span class="sxs-lookup"><span data-stu-id="48d19-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="48d19-1084">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="48d19-1084">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="48d19-1085">以下代码显示如何将 `Get<T>` 与前面的示例一起使用，该示例允许将绑定实例直接分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="48d19-1085">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="48d19-1086">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="48d19-1086">Bind an array to a class</span></span>

<span data-ttu-id="48d19-1087">示例应用演示了本部分中介绍的概念。 </span><span class="sxs-lookup"><span data-stu-id="48d19-1087">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="48d19-1088"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="48d19-1088">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="48d19-1089">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="48d19-1089">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="48d19-1090">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="48d19-1090">Binding is provided by convention.</span></span> <span data-ttu-id="48d19-1091">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="48d19-1091">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="48d19-1092">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="48d19-1092">**In-memory array processing**</span></span>

<span data-ttu-id="48d19-1093">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="48d19-1093">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="48d19-1094">键</span><span class="sxs-lookup"><span data-stu-id="48d19-1094">Key</span></span>             | <span data-ttu-id="48d19-1095">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-1095">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="48d19-1096">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="48d19-1096">array:entries:0</span></span> | <span data-ttu-id="48d19-1097">value0</span><span class="sxs-lookup"><span data-stu-id="48d19-1097">value0</span></span> |
| <span data-ttu-id="48d19-1098">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="48d19-1098">array:entries:1</span></span> | <span data-ttu-id="48d19-1099">value1</span><span class="sxs-lookup"><span data-stu-id="48d19-1099">value1</span></span> |
| <span data-ttu-id="48d19-1100">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="48d19-1100">array:entries:2</span></span> | <span data-ttu-id="48d19-1101">value2</span><span class="sxs-lookup"><span data-stu-id="48d19-1101">value2</span></span> |
| <span data-ttu-id="48d19-1102">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="48d19-1102">array:entries:4</span></span> | <span data-ttu-id="48d19-1103">value4</span><span class="sxs-lookup"><span data-stu-id="48d19-1103">value4</span></span> |
| <span data-ttu-id="48d19-1104">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="48d19-1104">array:entries:5</span></span> | <span data-ttu-id="48d19-1105">value5</span><span class="sxs-lookup"><span data-stu-id="48d19-1105">value5</span></span> |

<span data-ttu-id="48d19-1106">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="48d19-1106">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="48d19-1107">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-1107">The array skips a value for index &num;3.</span></span> <span data-ttu-id="48d19-1108">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="48d19-1108">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="48d19-1109">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="48d19-1109">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="48d19-1110">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="48d19-1110">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="48d19-1111">还可以使用 [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="48d19-1111">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="48d19-1112">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-1112">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="48d19-1113">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="48d19-1113">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="48d19-1114">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="48d19-1114">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="48d19-1115">0</span><span class="sxs-lookup"><span data-stu-id="48d19-1115">0</span></span>                            | <span data-ttu-id="48d19-1116">value0</span><span class="sxs-lookup"><span data-stu-id="48d19-1116">value0</span></span>                       |
| <span data-ttu-id="48d19-1117">1</span><span class="sxs-lookup"><span data-stu-id="48d19-1117">1</span></span>                            | <span data-ttu-id="48d19-1118">value1</span><span class="sxs-lookup"><span data-stu-id="48d19-1118">value1</span></span>                       |
| <span data-ttu-id="48d19-1119">2</span><span class="sxs-lookup"><span data-stu-id="48d19-1119">2</span></span>                            | <span data-ttu-id="48d19-1120">value2</span><span class="sxs-lookup"><span data-stu-id="48d19-1120">value2</span></span>                       |
| <span data-ttu-id="48d19-1121">3</span><span class="sxs-lookup"><span data-stu-id="48d19-1121">3</span></span>                            | <span data-ttu-id="48d19-1122">value4</span><span class="sxs-lookup"><span data-stu-id="48d19-1122">value4</span></span>                       |
| <span data-ttu-id="48d19-1123">4</span><span class="sxs-lookup"><span data-stu-id="48d19-1123">4</span></span>                            | <span data-ttu-id="48d19-1124">value5</span><span class="sxs-lookup"><span data-stu-id="48d19-1124">value5</span></span>                       |

<span data-ttu-id="48d19-1125">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1125">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="48d19-1126">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="48d19-1126">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="48d19-1127">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="48d19-1127">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="48d19-1128">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="48d19-1128">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="48d19-1129">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="48d19-1129">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="48d19-1130">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="48d19-1130">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="48d19-1131">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="48d19-1131">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="48d19-1132">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="48d19-1132">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="48d19-1133">键</span><span class="sxs-lookup"><span data-stu-id="48d19-1133">Key</span></span>             | <span data-ttu-id="48d19-1134">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-1134">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="48d19-1135">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="48d19-1135">array:entries:3</span></span> | <span data-ttu-id="48d19-1136">value3</span><span class="sxs-lookup"><span data-stu-id="48d19-1136">value3</span></span> |

<span data-ttu-id="48d19-1137">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="48d19-1137">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="48d19-1138">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="48d19-1138">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="48d19-1139">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="48d19-1139">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="48d19-1140">0</span><span class="sxs-lookup"><span data-stu-id="48d19-1140">0</span></span>                            | <span data-ttu-id="48d19-1141">value0</span><span class="sxs-lookup"><span data-stu-id="48d19-1141">value0</span></span>                       |
| <span data-ttu-id="48d19-1142">1</span><span class="sxs-lookup"><span data-stu-id="48d19-1142">1</span></span>                            | <span data-ttu-id="48d19-1143">value1</span><span class="sxs-lookup"><span data-stu-id="48d19-1143">value1</span></span>                       |
| <span data-ttu-id="48d19-1144">2</span><span class="sxs-lookup"><span data-stu-id="48d19-1144">2</span></span>                            | <span data-ttu-id="48d19-1145">value2</span><span class="sxs-lookup"><span data-stu-id="48d19-1145">value2</span></span>                       |
| <span data-ttu-id="48d19-1146">3</span><span class="sxs-lookup"><span data-stu-id="48d19-1146">3</span></span>                            | <span data-ttu-id="48d19-1147">value3</span><span class="sxs-lookup"><span data-stu-id="48d19-1147">value3</span></span>                       |
| <span data-ttu-id="48d19-1148">4</span><span class="sxs-lookup"><span data-stu-id="48d19-1148">4</span></span>                            | <span data-ttu-id="48d19-1149">value4</span><span class="sxs-lookup"><span data-stu-id="48d19-1149">value4</span></span>                       |
| <span data-ttu-id="48d19-1150">5</span><span class="sxs-lookup"><span data-stu-id="48d19-1150">5</span></span>                            | <span data-ttu-id="48d19-1151">value5</span><span class="sxs-lookup"><span data-stu-id="48d19-1151">value5</span></span>                       |

<span data-ttu-id="48d19-1152">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="48d19-1152">**JSON array processing**</span></span>

<span data-ttu-id="48d19-1153">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="48d19-1153">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="48d19-1154">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="48d19-1154">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="48d19-1155">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-1155">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="48d19-1156">键</span><span class="sxs-lookup"><span data-stu-id="48d19-1156">Key</span></span>                     | <span data-ttu-id="48d19-1157">“值”</span><span class="sxs-lookup"><span data-stu-id="48d19-1157">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="48d19-1158">json_array:key</span><span class="sxs-lookup"><span data-stu-id="48d19-1158">json_array:key</span></span>          | <span data-ttu-id="48d19-1159">valueA</span><span class="sxs-lookup"><span data-stu-id="48d19-1159">valueA</span></span> |
| <span data-ttu-id="48d19-1160">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="48d19-1160">json_array:subsection:0</span></span> | <span data-ttu-id="48d19-1161">valueB</span><span class="sxs-lookup"><span data-stu-id="48d19-1161">valueB</span></span> |
| <span data-ttu-id="48d19-1162">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="48d19-1162">json_array:subsection:1</span></span> | <span data-ttu-id="48d19-1163">valueC</span><span class="sxs-lookup"><span data-stu-id="48d19-1163">valueC</span></span> |
| <span data-ttu-id="48d19-1164">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="48d19-1164">json_array:subsection:2</span></span> | <span data-ttu-id="48d19-1165">valueD</span><span class="sxs-lookup"><span data-stu-id="48d19-1165">valueD</span></span> |

<span data-ttu-id="48d19-1166">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="48d19-1166">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="48d19-1167">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1167">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="48d19-1168">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="48d19-1168">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="48d19-1169">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="48d19-1169">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="48d19-1170">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="48d19-1170">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="48d19-1171">0</span><span class="sxs-lookup"><span data-stu-id="48d19-1171">0</span></span>                                   | <span data-ttu-id="48d19-1172">valueB</span><span class="sxs-lookup"><span data-stu-id="48d19-1172">valueB</span></span>                              |
| <span data-ttu-id="48d19-1173">1</span><span class="sxs-lookup"><span data-stu-id="48d19-1173">1</span></span>                                   | <span data-ttu-id="48d19-1174">valueC</span><span class="sxs-lookup"><span data-stu-id="48d19-1174">valueC</span></span>                              |
| <span data-ttu-id="48d19-1175">2</span><span class="sxs-lookup"><span data-stu-id="48d19-1175">2</span></span>                                   | <span data-ttu-id="48d19-1176">valueD</span><span class="sxs-lookup"><span data-stu-id="48d19-1176">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="48d19-1177">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="48d19-1177">Custom configuration provider</span></span>

<span data-ttu-id="48d19-1178">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-1178">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="48d19-1179">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="48d19-1179">The provider has the following characteristics:</span></span>

* <span data-ttu-id="48d19-1180">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="48d19-1180">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="48d19-1181">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="48d19-1181">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="48d19-1182">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="48d19-1182">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="48d19-1183">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="48d19-1183">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="48d19-1184">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="48d19-1184">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="48d19-1185">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="48d19-1185">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="48d19-1186">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-1186">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="48d19-1187">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="48d19-1187">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="48d19-1188">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="48d19-1189">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="48d19-1189">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="48d19-1190">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="48d19-1191">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="48d19-1191">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="48d19-1192">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="48d19-1192">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="48d19-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="48d19-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="48d19-1194">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="48d19-1194">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="48d19-1195">Extensions/EntityFrameworkExtensions.cs  ：</span><span class="sxs-lookup"><span data-stu-id="48d19-1195">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="48d19-1196">下面的代码演示如何在 Program.cs  中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="48d19-1196">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="48d19-1197">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="48d19-1197">Access configuration during startup</span></span>

<span data-ttu-id="48d19-1198">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="48d19-1198">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="48d19-1199">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="48d19-1199">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="48d19-1200">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="48d19-1200">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="48d19-1201">在 Razor Pages 页或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="48d19-1201">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="48d19-1202">若要访问 Razor Pages 页或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration 命名空间](xref:Microsoft.Extensions.Configuration)添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入页面或视图。</span><span class="sxs-lookup"><span data-stu-id="48d19-1202">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="48d19-1203">在 Razor 页面页中：</span><span class="sxs-lookup"><span data-stu-id="48d19-1203">In a Razor Pages page:</span></span>

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

<span data-ttu-id="48d19-1204">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="48d19-1204">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="48d19-1205">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="48d19-1205">Add configuration from an external assembly</span></span>

<span data-ttu-id="48d19-1206">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="48d19-1206">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="48d19-1207">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="48d19-1207">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48d19-1208">其他资源</span><span class="sxs-lookup"><span data-stu-id="48d19-1208">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
