---
title: ASP.NET核心开发中应用秘密的安全存储
author: rick-anderson
description: 了解如何在开发ASP.NET核心应用期间将敏感信息存储和检索为应用机密。
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791595"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="5af85-103">ASP.NET核心开发中应用秘密的安全存储</span><span class="sxs-lookup"><span data-stu-id="5af85-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5af85-104">由[里克·安德森](https://twitter.com/RickAndMSFT)、[柯克·拉金](https://twitter.com/serpent5)、[丹尼尔·罗斯](https://github.com/danroth27)和[斯科特·阿迪](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5af85-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5af85-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5af85-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5af85-106">本文档介绍在开发计算机上开发ASP.NET酷睿应用期间存储和检索敏感数据的技术。</span><span class="sxs-lookup"><span data-stu-id="5af85-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5af85-107">切勿在源代码中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="5af85-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5af85-108">生产机密不应用于开发或测试。</span><span class="sxs-lookup"><span data-stu-id="5af85-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5af85-109">不应将机密随应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="5af85-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5af85-110">相反，应通过受控方式（如环境变量、Azure 密钥保管库等）在生产环境中提供机密。您可以使用[Azure 密钥保管库配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5af85-111">环境变量</span><span class="sxs-lookup"><span data-stu-id="5af85-111">Environment variables</span></span>

<span data-ttu-id="5af85-112">环境变量用于避免在代码或本地配置文件中存储应用机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5af85-113">环境变量覆盖所有以前指定的配置源的配置值。</span><span class="sxs-lookup"><span data-stu-id="5af85-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5af85-114">请考虑一个 ASP.NET 核心 Web 应用，其中启用**了单个用户帐户**安全性。</span><span class="sxs-lookup"><span data-stu-id="5af85-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5af85-115">默认数据库连接字符串包含在项目的*appsettings.json*文件中，其中包含密钥`DefaultConnection`。</span><span class="sxs-lookup"><span data-stu-id="5af85-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5af85-116">默认连接字符串用于 LocalDB，该字符串在用户模式下运行，不需要密码。</span><span class="sxs-lookup"><span data-stu-id="5af85-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5af85-117">在应用部署期间，`DefaultConnection`可以使用环境变量的值重写键值。</span><span class="sxs-lookup"><span data-stu-id="5af85-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5af85-118">环境变量可能将完整的连接字符串存储为具有敏感凭据。</span><span class="sxs-lookup"><span data-stu-id="5af85-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5af85-119">环境变量通常以纯、未加密的文本存储。</span><span class="sxs-lookup"><span data-stu-id="5af85-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5af85-120">如果计算机或进程遭到破坏，则不受信任的方可以访问环境变量。</span><span class="sxs-lookup"><span data-stu-id="5af85-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5af85-121">可能需要采取其他措施防止泄露用户机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5af85-122">机密管理器</span><span class="sxs-lookup"><span data-stu-id="5af85-122">Secret Manager</span></span>

<span data-ttu-id="5af85-123">在开发ASP.NET核心项目期间，秘密管理器工具存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="5af85-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5af85-124">在此上下文中，一段敏感数据是应用机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5af85-125">应用机密存储在与项目树不同的位置。</span><span class="sxs-lookup"><span data-stu-id="5af85-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5af85-126">应用机密与特定项目关联或跨多个项目共享。</span><span class="sxs-lookup"><span data-stu-id="5af85-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5af85-127">应用机密不会签入源代码管理。</span><span class="sxs-lookup"><span data-stu-id="5af85-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5af85-128">秘密管理器工具不加密存储的秘密，不应被视为受信任的存储。</span><span class="sxs-lookup"><span data-stu-id="5af85-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5af85-129">它仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="5af85-129">It's for development purposes only.</span></span> <span data-ttu-id="5af85-130">键和值存储在用户配置文件目录中的 JSON 配置文件中。</span><span class="sxs-lookup"><span data-stu-id="5af85-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5af85-131">秘密管理器工具的工作原理</span><span class="sxs-lookup"><span data-stu-id="5af85-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="5af85-132">"秘密管理器"工具会抽象出实现详细信息，例如值的存储位置和方式。</span><span class="sxs-lookup"><span data-stu-id="5af85-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5af85-133">您可以在不知道这些实现详细信息的情况下使用该工具。</span><span class="sxs-lookup"><span data-stu-id="5af85-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5af85-134">这些值存储在本地计算机上的受系统保护的用户配置文件文件夹中的 JSON 配置文件中：</span><span class="sxs-lookup"><span data-stu-id="5af85-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5af85-135">Windows</span><span class="sxs-lookup"><span data-stu-id="5af85-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="5af85-136">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="5af85-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5af85-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5af85-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5af85-138">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="5af85-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5af85-139">在前面的文件路径中，替换为`<user_secrets_id>` `UserSecretsId` *.csproj*文件中指定的值。</span><span class="sxs-lookup"><span data-stu-id="5af85-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5af85-140">不要编写依赖于使用机密管理器工具保存的数据的位置或格式的代码。</span><span class="sxs-lookup"><span data-stu-id="5af85-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5af85-141">这些实现详细信息可能会更改。</span><span class="sxs-lookup"><span data-stu-id="5af85-141">These implementation details may change.</span></span> <span data-ttu-id="5af85-142">例如，机密值未加密，但将来可能已加密。</span><span class="sxs-lookup"><span data-stu-id="5af85-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5af85-143">启用机密存储</span><span class="sxs-lookup"><span data-stu-id="5af85-143">Enable secret storage</span></span>

<span data-ttu-id="5af85-144">"机密管理器"工具可对存储在用户配置文件中的特定于项目的配置设置进行操作。</span><span class="sxs-lookup"><span data-stu-id="5af85-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5af85-145">机密管理器工具包括 .NET Core SDK 3.0.100 或更高版本中`init`的命令。</span><span class="sxs-lookup"><span data-stu-id="5af85-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="5af85-146">要使用用户机密，请运行项目目录中的以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="5af85-147">前面的命令在`UserSecretsId`*.csproj*文件中添加一个`PropertyGroup`元素。</span><span class="sxs-lookup"><span data-stu-id="5af85-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5af85-148">默认情况下，的内部`UserSecretsId`文本是 GUID。</span><span class="sxs-lookup"><span data-stu-id="5af85-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="5af85-149">内部文本是任意的，但对于项目是唯一的。</span><span class="sxs-lookup"><span data-stu-id="5af85-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="5af85-150">在 Visual Studio 中，右键单击解决方案资源管理器中的项目，然后从上下文菜单中选择 **"管理用户机密**"。</span><span class="sxs-lookup"><span data-stu-id="5af85-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5af85-151">此手势将一`UserSecretsId`个使用 GUID 填充的元素添加到 *.csproj*文件中。</span><span class="sxs-lookup"><span data-stu-id="5af85-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5af85-152">设置机密</span><span class="sxs-lookup"><span data-stu-id="5af85-152">Set a secret</span></span>

<span data-ttu-id="5af85-153">定义由键及其值组成的应用机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5af85-154">机密与项目`UserSecretsId`的值相关联。</span><span class="sxs-lookup"><span data-stu-id="5af85-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5af85-155">例如，从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5af85-156">在前面的示例中，冒号表示`Movies`是具有`ServiceApiKey`属性的对象文本。</span><span class="sxs-lookup"><span data-stu-id="5af85-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5af85-157">机密管理器工具也可以从其他目录使用。</span><span class="sxs-lookup"><span data-stu-id="5af85-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5af85-158">使用`--project`选项提供*存在 .csproj*文件的文件系统路径。</span><span class="sxs-lookup"><span data-stu-id="5af85-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5af85-159">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5af85-160">视觉工作室中的 JSON 结构拼合</span><span class="sxs-lookup"><span data-stu-id="5af85-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5af85-161">可视化工作室的 **"管理用户机密**"手势将在文本编辑器中打开*一个机密.json*文件。</span><span class="sxs-lookup"><span data-stu-id="5af85-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5af85-162">将*机密*内容替换为要存储的键值对。</span><span class="sxs-lookup"><span data-stu-id="5af85-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5af85-163">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5af85-164">通过`dotnet user-secrets remove`或`dotnet user-secrets set`进行修改后，JSON 结构将展平。</span><span class="sxs-lookup"><span data-stu-id="5af85-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5af85-165">例如，运行`dotnet user-secrets remove "Movies:ConnectionString"`将折叠`Movies`对象文本。</span><span class="sxs-lookup"><span data-stu-id="5af85-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5af85-166">修改后的文件类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="5af85-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5af85-167">设置多个机密</span><span class="sxs-lookup"><span data-stu-id="5af85-167">Set multiple secrets</span></span>

<span data-ttu-id="5af85-168">通过将 JSON 管道到`set`命令，可以设置一批机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5af85-169">在下面的示例中 *，input.json*文件的内容被传送到命令`set`。</span><span class="sxs-lookup"><span data-stu-id="5af85-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5af85-170">Windows</span><span class="sxs-lookup"><span data-stu-id="5af85-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="5af85-171">打开命令外壳，并执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5af85-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5af85-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5af85-173">打开命令外壳，并执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5af85-174">访问机密</span><span class="sxs-lookup"><span data-stu-id="5af85-174">Access a secret</span></span>

<span data-ttu-id="5af85-175">[ASP.NET核心配置 API](xref:fundamentals/configuration/index)提供对机密管理器机密的访问。</span><span class="sxs-lookup"><span data-stu-id="5af85-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5af85-176">当用户调用<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>使用预配置默认值初始化主机的新实例时，将在开发模式下自动添加用户机密配置源。</span><span class="sxs-lookup"><span data-stu-id="5af85-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5af85-177">`CreateDefaultBuilder`当<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName>为<xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>时调用 ：</span><span class="sxs-lookup"><span data-stu-id="5af85-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="5af85-178">未`CreateDefaultBuilder`调用时，请通过调用<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>显式添加用户机密配置源。</span><span class="sxs-lookup"><span data-stu-id="5af85-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="5af85-179">仅在`AddUserSecrets`应用在开发环境中运行时调用，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="5af85-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="5af85-180">可通过`Configuration`API 检索用户机密：</span><span class="sxs-lookup"><span data-stu-id="5af85-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5af85-181">将机密映射到 POCO</span><span class="sxs-lookup"><span data-stu-id="5af85-181">Map secrets to a POCO</span></span>

<span data-ttu-id="5af85-182">将整个对象文本映射到 POCO（具有属性的简单 .NET 类）可用于聚合相关属性。</span><span class="sxs-lookup"><span data-stu-id="5af85-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-183">要将上述机密映射到 POCO，请使用`Configuration`API[的对象图形绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph)功能。</span><span class="sxs-lookup"><span data-stu-id="5af85-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5af85-184">以下代码绑定到自定义`MovieSettings`POCO 并访问`ServiceApiKey`属性值：</span><span class="sxs-lookup"><span data-stu-id="5af85-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5af85-185">和`Movies:ConnectionString``Movies:ServiceApiKey`机密映射到 中的`MovieSettings`相应属性：</span><span class="sxs-lookup"><span data-stu-id="5af85-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5af85-186">字符串替换与机密</span><span class="sxs-lookup"><span data-stu-id="5af85-186">String replacement with secrets</span></span>

<span data-ttu-id="5af85-187">以纯文本形式存储密码不安全。</span><span class="sxs-lookup"><span data-stu-id="5af85-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5af85-188">例如，存储在*appsettings.json*中的数据库连接字符串可能包含指定用户的密码：</span><span class="sxs-lookup"><span data-stu-id="5af85-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5af85-189">更安全的方法是将密码存储为机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5af85-190">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5af85-191">从`Password`*appsettings.json*中的连接字符串中删除键值对。</span><span class="sxs-lookup"><span data-stu-id="5af85-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5af85-192">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5af85-193">可以在<xref:System.Data.SqlClient.SqlConnectionStringBuilder>对象<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>的属性上设置机密的值以完成连接字符串：</span><span class="sxs-lookup"><span data-stu-id="5af85-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5af85-194">列出机密</span><span class="sxs-lookup"><span data-stu-id="5af85-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-195">从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5af85-196">将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="5af85-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5af85-197">在前面的示例中，键名称中的冒号表示机密中的对象层次结构 *。*</span><span class="sxs-lookup"><span data-stu-id="5af85-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5af85-198">删除单个机密</span><span class="sxs-lookup"><span data-stu-id="5af85-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-199">从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5af85-200">应用的*机密.json*文件已修改以删除与`MoviesConnectionString`密钥关联的键值对：</span><span class="sxs-lookup"><span data-stu-id="5af85-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5af85-201">`dotnet user-secrets list`显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="5af85-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5af85-202">删除所有机密</span><span class="sxs-lookup"><span data-stu-id="5af85-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-203">从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5af85-204">应用程序的所有用户机密已从*机密.json*文件中删除：</span><span class="sxs-lookup"><span data-stu-id="5af85-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5af85-205">正在`dotnet user-secrets list`运行将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="5af85-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5af85-206">其他资源</span><span class="sxs-lookup"><span data-stu-id="5af85-206">Additional resources</span></span>

* <span data-ttu-id="5af85-207">有关从 IIS 访问机密管理器的信息，请参阅[此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328)。</span><span class="sxs-lookup"><span data-stu-id="5af85-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5af85-208">由[里克·安德森](https://twitter.com/RickAndMSFT)，[丹尼尔·罗斯](https://github.com/danroth27)和[斯科特·艾迪](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5af85-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5af85-209">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5af85-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5af85-210">本文档介绍在开发计算机上开发ASP.NET酷睿应用期间存储和检索敏感数据的技术。</span><span class="sxs-lookup"><span data-stu-id="5af85-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5af85-211">切勿在源代码中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="5af85-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5af85-212">生产机密不应用于开发或测试。</span><span class="sxs-lookup"><span data-stu-id="5af85-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5af85-213">不应将机密随应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="5af85-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5af85-214">相反，应通过受控方式（如环境变量、Azure 密钥保管库等）在生产环境中提供机密。您可以使用[Azure 密钥保管库配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5af85-215">环境变量</span><span class="sxs-lookup"><span data-stu-id="5af85-215">Environment variables</span></span>

<span data-ttu-id="5af85-216">环境变量用于避免在代码或本地配置文件中存储应用机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5af85-217">环境变量覆盖所有以前指定的配置源的配置值。</span><span class="sxs-lookup"><span data-stu-id="5af85-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5af85-218">请考虑一个 ASP.NET 核心 Web 应用，其中启用**了单个用户帐户**安全性。</span><span class="sxs-lookup"><span data-stu-id="5af85-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5af85-219">默认数据库连接字符串包含在项目的*appsettings.json*文件中，其中包含密钥`DefaultConnection`。</span><span class="sxs-lookup"><span data-stu-id="5af85-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5af85-220">默认连接字符串用于 LocalDB，该字符串在用户模式下运行，不需要密码。</span><span class="sxs-lookup"><span data-stu-id="5af85-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5af85-221">在应用部署期间，`DefaultConnection`可以使用环境变量的值重写键值。</span><span class="sxs-lookup"><span data-stu-id="5af85-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5af85-222">环境变量可能将完整的连接字符串存储为具有敏感凭据。</span><span class="sxs-lookup"><span data-stu-id="5af85-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5af85-223">环境变量通常以纯、未加密的文本存储。</span><span class="sxs-lookup"><span data-stu-id="5af85-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5af85-224">如果计算机或进程遭到破坏，则不受信任的方可以访问环境变量。</span><span class="sxs-lookup"><span data-stu-id="5af85-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5af85-225">可能需要采取其他措施防止泄露用户机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5af85-226">机密管理器</span><span class="sxs-lookup"><span data-stu-id="5af85-226">Secret Manager</span></span>

<span data-ttu-id="5af85-227">在开发ASP.NET核心项目期间，秘密管理器工具存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="5af85-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5af85-228">在此上下文中，一段敏感数据是应用机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5af85-229">应用机密存储在与项目树不同的位置。</span><span class="sxs-lookup"><span data-stu-id="5af85-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5af85-230">应用机密与特定项目关联或跨多个项目共享。</span><span class="sxs-lookup"><span data-stu-id="5af85-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5af85-231">应用机密不会签入源代码管理。</span><span class="sxs-lookup"><span data-stu-id="5af85-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5af85-232">秘密管理器工具不加密存储的秘密，不应被视为受信任的存储。</span><span class="sxs-lookup"><span data-stu-id="5af85-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5af85-233">它仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="5af85-233">It's for development purposes only.</span></span> <span data-ttu-id="5af85-234">键和值存储在用户配置文件目录中的 JSON 配置文件中。</span><span class="sxs-lookup"><span data-stu-id="5af85-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5af85-235">秘密管理器工具的工作原理</span><span class="sxs-lookup"><span data-stu-id="5af85-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="5af85-236">"秘密管理器"工具会抽象出实现详细信息，例如值的存储位置和方式。</span><span class="sxs-lookup"><span data-stu-id="5af85-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5af85-237">您可以在不知道这些实现详细信息的情况下使用该工具。</span><span class="sxs-lookup"><span data-stu-id="5af85-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5af85-238">这些值存储在本地计算机上的受系统保护的用户配置文件文件夹中的 JSON 配置文件中：</span><span class="sxs-lookup"><span data-stu-id="5af85-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5af85-239">Windows</span><span class="sxs-lookup"><span data-stu-id="5af85-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="5af85-240">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="5af85-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5af85-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5af85-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5af85-242">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="5af85-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5af85-243">在前面的文件路径中，替换为`<user_secrets_id>` `UserSecretsId` *.csproj*文件中指定的值。</span><span class="sxs-lookup"><span data-stu-id="5af85-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5af85-244">不要编写依赖于使用机密管理器工具保存的数据的位置或格式的代码。</span><span class="sxs-lookup"><span data-stu-id="5af85-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5af85-245">这些实现详细信息可能会更改。</span><span class="sxs-lookup"><span data-stu-id="5af85-245">These implementation details may change.</span></span> <span data-ttu-id="5af85-246">例如，机密值未加密，但将来可能已加密。</span><span class="sxs-lookup"><span data-stu-id="5af85-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5af85-247">启用机密存储</span><span class="sxs-lookup"><span data-stu-id="5af85-247">Enable secret storage</span></span>

<span data-ttu-id="5af85-248">"机密管理器"工具可对存储在用户配置文件中的特定于项目的配置设置进行操作。</span><span class="sxs-lookup"><span data-stu-id="5af85-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5af85-249">要使用用户机密，请定义`UserSecretsId` `PropertyGroup` *.csproj*文件中的元素。</span><span class="sxs-lookup"><span data-stu-id="5af85-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5af85-250">的内部`UserSecretsId`文本是任意的，但对于项目是唯一的。</span><span class="sxs-lookup"><span data-stu-id="5af85-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="5af85-251">开发人员通常为 生成`UserSecretsId`GUID。</span><span class="sxs-lookup"><span data-stu-id="5af85-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="5af85-252">在 Visual Studio 中，右键单击解决方案资源管理器中的项目，然后从上下文菜单中选择 **"管理用户机密**"。</span><span class="sxs-lookup"><span data-stu-id="5af85-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5af85-253">此手势将一`UserSecretsId`个使用 GUID 填充的元素添加到 *.csproj*文件中。</span><span class="sxs-lookup"><span data-stu-id="5af85-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5af85-254">设置机密</span><span class="sxs-lookup"><span data-stu-id="5af85-254">Set a secret</span></span>

<span data-ttu-id="5af85-255">定义由键及其值组成的应用机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5af85-256">机密与项目`UserSecretsId`的值相关联。</span><span class="sxs-lookup"><span data-stu-id="5af85-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5af85-257">例如，从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5af85-258">在前面的示例中，冒号表示`Movies`是具有`ServiceApiKey`属性的对象文本。</span><span class="sxs-lookup"><span data-stu-id="5af85-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5af85-259">机密管理器工具也可以从其他目录使用。</span><span class="sxs-lookup"><span data-stu-id="5af85-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5af85-260">使用`--project`选项提供*存在 .csproj*文件的文件系统路径。</span><span class="sxs-lookup"><span data-stu-id="5af85-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5af85-261">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5af85-262">视觉工作室中的 JSON 结构拼合</span><span class="sxs-lookup"><span data-stu-id="5af85-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5af85-263">可视化工作室的 **"管理用户机密**"手势将在文本编辑器中打开*一个机密.json*文件。</span><span class="sxs-lookup"><span data-stu-id="5af85-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5af85-264">将*机密*内容替换为要存储的键值对。</span><span class="sxs-lookup"><span data-stu-id="5af85-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5af85-265">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5af85-266">通过`dotnet user-secrets remove`或`dotnet user-secrets set`进行修改后，JSON 结构将展平。</span><span class="sxs-lookup"><span data-stu-id="5af85-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5af85-267">例如，运行`dotnet user-secrets remove "Movies:ConnectionString"`将折叠`Movies`对象文本。</span><span class="sxs-lookup"><span data-stu-id="5af85-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5af85-268">修改后的文件类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="5af85-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5af85-269">设置多个机密</span><span class="sxs-lookup"><span data-stu-id="5af85-269">Set multiple secrets</span></span>

<span data-ttu-id="5af85-270">通过将 JSON 管道到`set`命令，可以设置一批机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5af85-271">在下面的示例中 *，input.json*文件的内容被传送到命令`set`。</span><span class="sxs-lookup"><span data-stu-id="5af85-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5af85-272">Windows</span><span class="sxs-lookup"><span data-stu-id="5af85-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="5af85-273">打开命令外壳，并执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5af85-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5af85-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5af85-275">打开命令外壳，并执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5af85-276">访问机密</span><span class="sxs-lookup"><span data-stu-id="5af85-276">Access a secret</span></span>

<span data-ttu-id="5af85-277">[ASP.NET核心配置 API](xref:fundamentals/configuration/index)提供对机密管理器机密的访问。</span><span class="sxs-lookup"><span data-stu-id="5af85-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5af85-278">如果项目的目标是 .NET 框架，请安装[Microsoft.扩展.配置.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="5af85-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="5af85-279">在 ASP.NET Core 2.0 或更高版本中，当项目调用<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>使用预配置默认值初始化主机的新实例时，用户机密配置源将自动在开发模式下添加。</span><span class="sxs-lookup"><span data-stu-id="5af85-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5af85-280">`CreateDefaultBuilder`当<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName>为<xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>时调用 ：</span><span class="sxs-lookup"><span data-stu-id="5af85-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="5af85-281">未`CreateDefaultBuilder`调用时，通过在<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>`Startup`构造函数中调用显式添加用户机密配置源。</span><span class="sxs-lookup"><span data-stu-id="5af85-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="5af85-282">仅在`AddUserSecrets`应用在开发环境中运行时调用，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="5af85-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="5af85-283">可通过`Configuration`API 检索用户机密：</span><span class="sxs-lookup"><span data-stu-id="5af85-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5af85-284">将机密映射到 POCO</span><span class="sxs-lookup"><span data-stu-id="5af85-284">Map secrets to a POCO</span></span>

<span data-ttu-id="5af85-285">将整个对象文本映射到 POCO（具有属性的简单 .NET 类）可用于聚合相关属性。</span><span class="sxs-lookup"><span data-stu-id="5af85-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-286">要将上述机密映射到 POCO，请使用`Configuration`API[的对象图形绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph)功能。</span><span class="sxs-lookup"><span data-stu-id="5af85-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5af85-287">以下代码绑定到自定义`MovieSettings`POCO 并访问`ServiceApiKey`属性值：</span><span class="sxs-lookup"><span data-stu-id="5af85-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5af85-288">和`Movies:ConnectionString``Movies:ServiceApiKey`机密映射到 中的`MovieSettings`相应属性：</span><span class="sxs-lookup"><span data-stu-id="5af85-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5af85-289">字符串替换与机密</span><span class="sxs-lookup"><span data-stu-id="5af85-289">String replacement with secrets</span></span>

<span data-ttu-id="5af85-290">以纯文本形式存储密码不安全。</span><span class="sxs-lookup"><span data-stu-id="5af85-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5af85-291">例如，存储在*appsettings.json*中的数据库连接字符串可能包含指定用户的密码：</span><span class="sxs-lookup"><span data-stu-id="5af85-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5af85-292">更安全的方法是将密码存储为机密。</span><span class="sxs-lookup"><span data-stu-id="5af85-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5af85-293">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5af85-294">从`Password`*appsettings.json*中的连接字符串中删除键值对。</span><span class="sxs-lookup"><span data-stu-id="5af85-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5af85-295">例如：</span><span class="sxs-lookup"><span data-stu-id="5af85-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5af85-296">可以在<xref:System.Data.SqlClient.SqlConnectionStringBuilder>对象<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>的属性上设置机密的值以完成连接字符串：</span><span class="sxs-lookup"><span data-stu-id="5af85-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5af85-297">列出机密</span><span class="sxs-lookup"><span data-stu-id="5af85-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-298">从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5af85-299">将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="5af85-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5af85-300">在前面的示例中，键名称中的冒号表示机密中的对象层次结构 *。*</span><span class="sxs-lookup"><span data-stu-id="5af85-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5af85-301">删除单个机密</span><span class="sxs-lookup"><span data-stu-id="5af85-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-302">从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5af85-303">应用的*机密.json*文件已修改以删除与`MoviesConnectionString`密钥关联的键值对：</span><span class="sxs-lookup"><span data-stu-id="5af85-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5af85-304">正在`dotnet user-secrets list`运行将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="5af85-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5af85-305">删除所有机密</span><span class="sxs-lookup"><span data-stu-id="5af85-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5af85-306">从*存在 .csproj*文件的目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5af85-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5af85-307">应用程序的所有用户机密已从*机密.json*文件中删除：</span><span class="sxs-lookup"><span data-stu-id="5af85-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5af85-308">正在`dotnet user-secrets list`运行将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="5af85-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5af85-309">其他资源</span><span class="sxs-lookup"><span data-stu-id="5af85-309">Additional resources</span></span>

* <span data-ttu-id="5af85-310">有关从 IIS 访问机密管理器的信息，请参阅[此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328)。</span><span class="sxs-lookup"><span data-stu-id="5af85-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end