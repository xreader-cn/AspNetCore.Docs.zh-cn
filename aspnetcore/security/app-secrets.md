---
title: ASP.NET Core 中的开发中安全存储应用机密
author: rick-anderson
description: 了解如何在开发 ASP.NET Core 应用过程中将敏感信息作为应用密钥存储和检索。
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056863"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="63c3c-103">ASP.NET Core 中的开发中安全存储应用机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63c3c-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)和 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="63c3c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="63c3c-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="63c3c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="63c3c-106">本文档介绍在开发计算机上开发 ASP.NET Core 应用过程中存储和检索敏感数据的方法。</span><span class="sxs-lookup"><span data-stu-id="63c3c-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="63c3c-107">切勿在源代码中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="63c3c-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="63c3c-108">生产机密不应用于开发或测试。</span><span class="sxs-lookup"><span data-stu-id="63c3c-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="63c3c-109">机密不应与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="63c3c-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="63c3c-110">相反，机密应通过受控方式（如环境变量、Azure Key Vault 等）在生产环境中可用。可以通过 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="63c3c-111">环境变量</span><span class="sxs-lookup"><span data-stu-id="63c3c-111">Environment variables</span></span>

<span data-ttu-id="63c3c-112">环境变量用于避免在代码中或在本地配置文件中存储应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="63c3c-113">环境变量会重写所有以前指定的配置源的配置值。</span><span class="sxs-lookup"><span data-stu-id="63c3c-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="63c3c-114">请考虑一个 ASP.NET Core web 应用，其中启用了 **单个用户帐户** 安全。</span><span class="sxs-lookup"><span data-stu-id="63c3c-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="63c3c-115">带有密钥的项目文件中包含默认的数据库连接字符串 *appsettings.json* `DefaultConnection` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="63c3c-116">默认连接字符串用于 LocalDB，后者在用户模式下运行，不需要密码。</span><span class="sxs-lookup"><span data-stu-id="63c3c-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="63c3c-117">在应用程序部署过程中， `DefaultConnection` 可使用环境变量的值覆盖密钥值。</span><span class="sxs-lookup"><span data-stu-id="63c3c-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="63c3c-118">环境变量可以存储具有敏感凭据的完整连接字符串。</span><span class="sxs-lookup"><span data-stu-id="63c3c-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="63c3c-119">环境变量通常以未加密的纯文本格式存储。</span><span class="sxs-lookup"><span data-stu-id="63c3c-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="63c3c-120">如果计算机或进程受到危害，则不受信任方可以访问环境变量。</span><span class="sxs-lookup"><span data-stu-id="63c3c-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="63c3c-121">可能需要其他措施来防止泄露用户机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="63c3c-122">机密管理器</span><span class="sxs-lookup"><span data-stu-id="63c3c-122">Secret Manager</span></span>

<span data-ttu-id="63c3c-123">机密管理器工具在开发 ASP.NET Core 项目的过程中存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="63c3c-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="63c3c-124">在此上下文中，一段敏感数据是应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="63c3c-125">应用密钥存储在与项目树不同的位置。</span><span class="sxs-lookup"><span data-stu-id="63c3c-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="63c3c-126">应用程序机密与特定项目关联或在多个项目之间共享。</span><span class="sxs-lookup"><span data-stu-id="63c3c-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="63c3c-127">应用密码未签入源控件。</span><span class="sxs-lookup"><span data-stu-id="63c3c-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="63c3c-128">机密管理器工具不会加密存储的机密，也不应将其视为受信任的存储区。</span><span class="sxs-lookup"><span data-stu-id="63c3c-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="63c3c-129">它仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="63c3c-129">It's for development purposes only.</span></span> <span data-ttu-id="63c3c-130">密钥和值存储在用户配置文件目录中的 JSON 配置文件中。</span><span class="sxs-lookup"><span data-stu-id="63c3c-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="63c3c-131">机密管理器工具的工作原理</span><span class="sxs-lookup"><span data-stu-id="63c3c-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="63c3c-132">机密管理器工具可以抽象出实现的详细信息，例如存储值的位置和方式。</span><span class="sxs-lookup"><span data-stu-id="63c3c-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="63c3c-133">您可以使用该工具，而无需了解这些实现细节。</span><span class="sxs-lookup"><span data-stu-id="63c3c-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="63c3c-134">这些值存储在本地计算机上受系统保护的用户配置文件文件夹中的 JSON 配置文件中：</span><span class="sxs-lookup"><span data-stu-id="63c3c-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="63c3c-135">Windows</span><span class="sxs-lookup"><span data-stu-id="63c3c-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="63c3c-136">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="63c3c-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="63c3c-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="63c3c-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="63c3c-138">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="63c3c-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="63c3c-139">在前面的文件路径中，将替换 `<user_secrets_id>` 为 `UserSecretsId` *.csproj* 文件中指定的值。</span><span class="sxs-lookup"><span data-stu-id="63c3c-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="63c3c-140">不要编写依赖于通过机密管理器工具保存的数据的位置或格式的代码。</span><span class="sxs-lookup"><span data-stu-id="63c3c-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="63c3c-141">这些实现细节可能会发生变化。</span><span class="sxs-lookup"><span data-stu-id="63c3c-141">These implementation details may change.</span></span> <span data-ttu-id="63c3c-142">例如，机密值不会加密，但可能会在将来。</span><span class="sxs-lookup"><span data-stu-id="63c3c-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="63c3c-143">启用密钥存储</span><span class="sxs-lookup"><span data-stu-id="63c3c-143">Enable secret storage</span></span>

<span data-ttu-id="63c3c-144">机密管理器工具对存储在用户配置文件中的特定于项目的配置设置进行操作。</span><span class="sxs-lookup"><span data-stu-id="63c3c-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="63c3c-145">机密管理器工具 `init` 在 .NET Core SDK 3.0.100 或更高版本中包含命令。</span><span class="sxs-lookup"><span data-stu-id="63c3c-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="63c3c-146">若要使用用户机密，请在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="63c3c-147">前面的命令将在 `UserSecretsId` .csproj 文件的中添加一个元素 `PropertyGroup` 。 *.csproj*</span><span class="sxs-lookup"><span data-stu-id="63c3c-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="63c3c-148">默认情况下，的内部文本 `UserSecretsId` 是 GUID。</span><span class="sxs-lookup"><span data-stu-id="63c3c-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="63c3c-149">内部文本是任意的，但对项目是唯一的。</span><span class="sxs-lookup"><span data-stu-id="63c3c-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="63c3c-150">在 Visual Studio 中，右键单击 "解决方案资源管理器中的项目，然后从上下文菜单中选择" **管理用户机密** "。</span><span class="sxs-lookup"><span data-stu-id="63c3c-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="63c3c-151">此笔势将 `UserSecretsId` 使用 GUID 填充的元素添加到 *.csproj* 文件。</span><span class="sxs-lookup"><span data-stu-id="63c3c-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="63c3c-152">设置机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-152">Set a secret</span></span>

<span data-ttu-id="63c3c-153">定义包含密钥及其值的应用密码。</span><span class="sxs-lookup"><span data-stu-id="63c3c-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="63c3c-154">机密与项目的值相关联 `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="63c3c-155">例如，从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="63c3c-156">在前面的示例中，冒号表示 `Movies` 是具有属性的对象文本 `ServiceApiKey` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="63c3c-157">机密管理器工具也可用于其他目录。</span><span class="sxs-lookup"><span data-stu-id="63c3c-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="63c3c-158">使用 `--project` 选项可提供 *.csproj* 文件所在的文件系统路径。</span><span class="sxs-lookup"><span data-stu-id="63c3c-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="63c3c-159">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="63c3c-160">Visual Studio 中的 JSON 结构平展</span><span class="sxs-lookup"><span data-stu-id="63c3c-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="63c3c-161">Visual Studio 的 " **管理用户机密** " 手势在文本编辑器中打开文件的 *secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="63c3c-162">将 *上secrets.js* 的内容替换为要存储的键值对。</span><span class="sxs-lookup"><span data-stu-id="63c3c-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="63c3c-163">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="63c3c-164">JSON 结构是通过或进行修改后平展的 `dotnet user-secrets remove` `dotnet user-secrets set` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="63c3c-165">例如，运行会 `dotnet user-secrets remove "Movies:ConnectionString"` 折叠 `Movies` 对象文本。</span><span class="sxs-lookup"><span data-stu-id="63c3c-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="63c3c-166">修改后的文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="63c3c-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="63c3c-167">设置多个机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-167">Set multiple secrets</span></span>

<span data-ttu-id="63c3c-168">可以通过管道 JSON 将密码批设置为 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="63c3c-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="63c3c-169">在下面的示例中，将文件的内容的 *input.js* 传递给 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="63c3c-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="63c3c-170">Windows</span><span class="sxs-lookup"><span data-stu-id="63c3c-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="63c3c-171">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="63c3c-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="63c3c-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="63c3c-173">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="63c3c-174">访问机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-174">Access a secret</span></span>

<span data-ttu-id="63c3c-175">[ASP.NET Core 配置 API](xref:fundamentals/configuration/index)提供对机密管理器密码的访问权限。</span><span class="sxs-lookup"><span data-stu-id="63c3c-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="63c3c-176">当项目调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 使用预先配置的默认值初始化主机的新实例时，用户机密配置源将在开发模式下自动添加。</span><span class="sxs-lookup"><span data-stu-id="63c3c-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="63c3c-177">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>当为时 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> 调用 <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="63c3c-178">如果 `CreateDefaultBuilder` 未调用，请通过调用显式添加用户机密配置源 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="63c3c-179">`AddUserSecrets`仅在开发环境中运行应用时调用，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="63c3c-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="63c3c-180">可以通过 API 检索用户机密 `Configuration` ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="63c3c-181">将机密映射到 POCO</span><span class="sxs-lookup"><span data-stu-id="63c3c-181">Map secrets to a POCO</span></span>

<span data-ttu-id="63c3c-182">将整个对象文本映射到 POCO (具有属性) 的简单 .NET 类对于聚合相关属性十分有用。</span><span class="sxs-lookup"><span data-stu-id="63c3c-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-183">若要将上述机密映射到 POCO，请使用 `Configuration` API 的 [对象关系图绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph) 功能。</span><span class="sxs-lookup"><span data-stu-id="63c3c-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="63c3c-184">下面的代码绑定到自定义 `MovieSettings` POCO 并访问 `ServiceApiKey` 属性值：</span><span class="sxs-lookup"><span data-stu-id="63c3c-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="63c3c-185">`Movies:ConnectionString`和 `Movies:ServiceApiKey` 机密映射到中的相应属性 `MovieSettings` ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="63c3c-186">用机密替换字符串</span><span class="sxs-lookup"><span data-stu-id="63c3c-186">String replacement with secrets</span></span>

<span data-ttu-id="63c3c-187">以纯文本形式存储密码是不安全的。</span><span class="sxs-lookup"><span data-stu-id="63c3c-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="63c3c-188">例如，存储在中的数据库连接字符串 *appsettings.json* 可能包含指定用户的密码：</span><span class="sxs-lookup"><span data-stu-id="63c3c-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="63c3c-189">更安全的方法是将密码存储为机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="63c3c-190">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="63c3c-191">`Password`从中的连接字符串中移除键值对 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-191">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="63c3c-192">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="63c3c-193">可以对对象的属性设置机密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成连接字符串：</span><span class="sxs-lookup"><span data-stu-id="63c3c-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="63c3c-194">列出机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-195">从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="63c3c-196">随即显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="63c3c-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="63c3c-197">在前面的示例中，键名称中的冒号表示 *secrets.js上* 的中的对象层次结构。</span><span class="sxs-lookup"><span data-stu-id="63c3c-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="63c3c-198">删除单个机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-199">从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="63c3c-200">已修改文件 *上* 应用的secrets.js，以删除与密钥关联的键值对 `MoviesConnectionString` ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="63c3c-201">`dotnet user-secrets list` 显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="63c3c-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="63c3c-202">删除所有机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-203">从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="63c3c-204">此应用的所有用户机密已从文件中的 *secrets.js* 删除：</span><span class="sxs-lookup"><span data-stu-id="63c3c-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="63c3c-205">运行 `dotnet user-secrets list` 将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="63c3c-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="63c3c-206">其他资源</span><span class="sxs-lookup"><span data-stu-id="63c3c-206">Additional resources</span></span>

* <span data-ttu-id="63c3c-207">有关从 IIS 访问密钥管理器的信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="63c3c-208">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)和 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="63c3c-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="63c3c-209">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="63c3c-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="63c3c-210">本文档介绍在开发计算机上开发 ASP.NET Core 应用过程中存储和检索敏感数据的方法。</span><span class="sxs-lookup"><span data-stu-id="63c3c-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="63c3c-211">切勿在源代码中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="63c3c-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="63c3c-212">生产机密不应用于开发或测试。</span><span class="sxs-lookup"><span data-stu-id="63c3c-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="63c3c-213">机密不应与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="63c3c-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="63c3c-214">相反，机密应通过受控方式（如环境变量、Azure Key Vault 等）在生产环境中可用。可以通过 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="63c3c-215">环境变量</span><span class="sxs-lookup"><span data-stu-id="63c3c-215">Environment variables</span></span>

<span data-ttu-id="63c3c-216">环境变量用于避免在代码中或在本地配置文件中存储应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="63c3c-217">环境变量会重写所有以前指定的配置源的配置值。</span><span class="sxs-lookup"><span data-stu-id="63c3c-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="63c3c-218">请考虑一个 ASP.NET Core web 应用，其中启用了 **单个用户帐户** 安全。</span><span class="sxs-lookup"><span data-stu-id="63c3c-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="63c3c-219">带有密钥的项目文件中包含默认的数据库连接字符串 *appsettings.json* `DefaultConnection` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="63c3c-220">默认连接字符串用于 LocalDB，后者在用户模式下运行，不需要密码。</span><span class="sxs-lookup"><span data-stu-id="63c3c-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="63c3c-221">在应用程序部署过程中， `DefaultConnection` 可使用环境变量的值覆盖密钥值。</span><span class="sxs-lookup"><span data-stu-id="63c3c-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="63c3c-222">环境变量可以存储具有敏感凭据的完整连接字符串。</span><span class="sxs-lookup"><span data-stu-id="63c3c-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="63c3c-223">环境变量通常以未加密的纯文本格式存储。</span><span class="sxs-lookup"><span data-stu-id="63c3c-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="63c3c-224">如果计算机或进程受到危害，则不受信任方可以访问环境变量。</span><span class="sxs-lookup"><span data-stu-id="63c3c-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="63c3c-225">可能需要其他措施来防止泄露用户机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="63c3c-226">机密管理器</span><span class="sxs-lookup"><span data-stu-id="63c3c-226">Secret Manager</span></span>

<span data-ttu-id="63c3c-227">机密管理器工具在开发 ASP.NET Core 项目的过程中存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="63c3c-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="63c3c-228">在此上下文中，一段敏感数据是应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="63c3c-229">应用密钥存储在与项目树不同的位置。</span><span class="sxs-lookup"><span data-stu-id="63c3c-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="63c3c-230">应用程序机密与特定项目关联或在多个项目之间共享。</span><span class="sxs-lookup"><span data-stu-id="63c3c-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="63c3c-231">应用密码未签入源控件。</span><span class="sxs-lookup"><span data-stu-id="63c3c-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="63c3c-232">机密管理器工具不会加密存储的机密，也不应将其视为受信任的存储区。</span><span class="sxs-lookup"><span data-stu-id="63c3c-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="63c3c-233">它仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="63c3c-233">It's for development purposes only.</span></span> <span data-ttu-id="63c3c-234">密钥和值存储在用户配置文件目录中的 JSON 配置文件中。</span><span class="sxs-lookup"><span data-stu-id="63c3c-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="63c3c-235">机密管理器工具的工作原理</span><span class="sxs-lookup"><span data-stu-id="63c3c-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="63c3c-236">机密管理器工具可以抽象出实现的详细信息，例如存储值的位置和方式。</span><span class="sxs-lookup"><span data-stu-id="63c3c-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="63c3c-237">您可以使用该工具，而无需了解这些实现细节。</span><span class="sxs-lookup"><span data-stu-id="63c3c-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="63c3c-238">这些值存储在本地计算机上受系统保护的用户配置文件文件夹中的 JSON 配置文件中：</span><span class="sxs-lookup"><span data-stu-id="63c3c-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="63c3c-239">Windows</span><span class="sxs-lookup"><span data-stu-id="63c3c-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="63c3c-240">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="63c3c-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="63c3c-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="63c3c-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="63c3c-242">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="63c3c-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="63c3c-243">在前面的文件路径中，将替换 `<user_secrets_id>` 为 `UserSecretsId` *.csproj* 文件中指定的值。</span><span class="sxs-lookup"><span data-stu-id="63c3c-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="63c3c-244">不要编写依赖于通过机密管理器工具保存的数据的位置或格式的代码。</span><span class="sxs-lookup"><span data-stu-id="63c3c-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="63c3c-245">这些实现细节可能会发生变化。</span><span class="sxs-lookup"><span data-stu-id="63c3c-245">These implementation details may change.</span></span> <span data-ttu-id="63c3c-246">例如，机密值不会加密，但可能会在将来。</span><span class="sxs-lookup"><span data-stu-id="63c3c-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="63c3c-247">启用密钥存储</span><span class="sxs-lookup"><span data-stu-id="63c3c-247">Enable secret storage</span></span>

<span data-ttu-id="63c3c-248">机密管理器工具对存储在用户配置文件中的特定于项目的配置设置进行操作。</span><span class="sxs-lookup"><span data-stu-id="63c3c-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="63c3c-249">若要使用用户机密，请在 .csproj 文件的中定义一个 `UserSecretsId` 元素 `PropertyGroup` 。 *.csproj*</span><span class="sxs-lookup"><span data-stu-id="63c3c-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="63c3c-250">的内部文本 `UserSecretsId` 是任意的，但对项目是唯一的。</span><span class="sxs-lookup"><span data-stu-id="63c3c-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="63c3c-251">开发人员通常会生成的 GUID `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="63c3c-252">在 Visual Studio 中，右键单击 "解决方案资源管理器中的项目，然后从上下文菜单中选择" **管理用户机密** "。</span><span class="sxs-lookup"><span data-stu-id="63c3c-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="63c3c-253">此笔势将 `UserSecretsId` 使用 GUID 填充的元素添加到 *.csproj* 文件。</span><span class="sxs-lookup"><span data-stu-id="63c3c-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="63c3c-254">设置机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-254">Set a secret</span></span>

<span data-ttu-id="63c3c-255">定义包含密钥及其值的应用密码。</span><span class="sxs-lookup"><span data-stu-id="63c3c-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="63c3c-256">机密与项目的值相关联 `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="63c3c-257">例如，从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="63c3c-258">在前面的示例中，冒号表示 `Movies` 是具有属性的对象文本 `ServiceApiKey` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="63c3c-259">机密管理器工具也可用于其他目录。</span><span class="sxs-lookup"><span data-stu-id="63c3c-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="63c3c-260">使用 `--project` 选项可提供 *.csproj* 文件所在的文件系统路径。</span><span class="sxs-lookup"><span data-stu-id="63c3c-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="63c3c-261">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="63c3c-262">Visual Studio 中的 JSON 结构平展</span><span class="sxs-lookup"><span data-stu-id="63c3c-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="63c3c-263">Visual Studio 的 " **管理用户机密** " 手势在文本编辑器中打开文件的 *secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="63c3c-264">将 *上secrets.js* 的内容替换为要存储的键值对。</span><span class="sxs-lookup"><span data-stu-id="63c3c-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="63c3c-265">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="63c3c-266">JSON 结构是通过或进行修改后平展的 `dotnet user-secrets remove` `dotnet user-secrets set` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="63c3c-267">例如，运行会 `dotnet user-secrets remove "Movies:ConnectionString"` 折叠 `Movies` 对象文本。</span><span class="sxs-lookup"><span data-stu-id="63c3c-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="63c3c-268">修改后的文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="63c3c-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="63c3c-269">设置多个机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-269">Set multiple secrets</span></span>

<span data-ttu-id="63c3c-270">可以通过管道 JSON 将密码批设置为 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="63c3c-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="63c3c-271">在下面的示例中，将文件的内容的 *input.js* 传递给 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="63c3c-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="63c3c-272">Windows</span><span class="sxs-lookup"><span data-stu-id="63c3c-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="63c3c-273">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="63c3c-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="63c3c-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="63c3c-275">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="63c3c-276">访问机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-276">Access a secret</span></span>

<span data-ttu-id="63c3c-277">[ASP.NET Core 配置 API](xref:fundamentals/configuration/index)提供对机密管理器密码的访问权限。</span><span class="sxs-lookup"><span data-stu-id="63c3c-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="63c3c-278">如果项目以 .NET Framework 为目标，请安装 [Microsoft.Extensions.Configu。UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="63c3c-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="63c3c-279">在 ASP.NET Core 2.0 或更高版本中，当项目调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 来初始化具有预先配置默认值的主机的新实例时，用户机密配置源将在开发模式下自动添加。</span><span class="sxs-lookup"><span data-stu-id="63c3c-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="63c3c-280">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>当为时 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 调用 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="63c3c-281">`CreateDefaultBuilder`不调用时，通过 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 在构造函数中调用来显式添加用户机密配置源 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="63c3c-282">`AddUserSecrets`仅在开发环境中运行应用时调用，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="63c3c-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="63c3c-283">可以通过 API 检索用户机密 `Configuration` ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="63c3c-284">将机密映射到 POCO</span><span class="sxs-lookup"><span data-stu-id="63c3c-284">Map secrets to a POCO</span></span>

<span data-ttu-id="63c3c-285">将整个对象文本映射到 POCO (具有属性) 的简单 .NET 类对于聚合相关属性十分有用。</span><span class="sxs-lookup"><span data-stu-id="63c3c-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-286">若要将上述机密映射到 POCO，请使用 `Configuration` API 的 [对象关系图绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph) 功能。</span><span class="sxs-lookup"><span data-stu-id="63c3c-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="63c3c-287">下面的代码绑定到自定义 `MovieSettings` POCO 并访问 `ServiceApiKey` 属性值：</span><span class="sxs-lookup"><span data-stu-id="63c3c-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="63c3c-288">`Movies:ConnectionString`和 `Movies:ServiceApiKey` 机密映射到中的相应属性 `MovieSettings` ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="63c3c-289">用机密替换字符串</span><span class="sxs-lookup"><span data-stu-id="63c3c-289">String replacement with secrets</span></span>

<span data-ttu-id="63c3c-290">以纯文本形式存储密码是不安全的。</span><span class="sxs-lookup"><span data-stu-id="63c3c-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="63c3c-291">例如，存储在中的数据库连接字符串 *appsettings.json* 可能包含指定用户的密码：</span><span class="sxs-lookup"><span data-stu-id="63c3c-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="63c3c-292">更安全的方法是将密码存储为机密。</span><span class="sxs-lookup"><span data-stu-id="63c3c-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="63c3c-293">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="63c3c-294">`Password`从中的连接字符串中移除键值对 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-294">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="63c3c-295">例如： 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="63c3c-296">可以对对象的属性设置机密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成连接字符串：</span><span class="sxs-lookup"><span data-stu-id="63c3c-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="63c3c-297">列出机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-298">从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="63c3c-299">随即显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="63c3c-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="63c3c-300">在前面的示例中，键名称中的冒号表示 *secrets.js上* 的中的对象层次结构。</span><span class="sxs-lookup"><span data-stu-id="63c3c-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="63c3c-301">删除单个机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-302">从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="63c3c-303">已修改文件 *上* 应用的secrets.js，以删除与密钥关联的键值对 `MoviesConnectionString` ：</span><span class="sxs-lookup"><span data-stu-id="63c3c-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="63c3c-304">运行 `dotnet user-secrets list` 将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="63c3c-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="63c3c-305">删除所有机密</span><span class="sxs-lookup"><span data-stu-id="63c3c-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="63c3c-306">从 *.csproj* 文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="63c3c-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="63c3c-307">此应用的所有用户机密已从文件中的 *secrets.js* 删除：</span><span class="sxs-lookup"><span data-stu-id="63c3c-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="63c3c-308">运行 `dotnet user-secrets list` 将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="63c3c-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="63c3c-309">其他资源</span><span class="sxs-lookup"><span data-stu-id="63c3c-309">Additional resources</span></span>

* <span data-ttu-id="63c3c-310">有关从 IIS 访问密钥管理器的信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 。</span><span class="sxs-lookup"><span data-stu-id="63c3c-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end