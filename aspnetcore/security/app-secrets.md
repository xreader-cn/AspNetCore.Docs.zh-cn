---
title: 安全存储中 ASP.NET Core 中开发的应用程序机密
author: rick-anderson
description: 了解如何存储和检索在 ASP.NET Core 应用程序开发期间为应用程序机密的敏感信息。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/31/2019
uid: security/app-secrets
ms.openlocfilehash: eaa2e9d1ba98d391a29a9ff55872d062df016b87
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667773"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>安全存储中 ASP.NET Core 中开发的应用程序机密

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Daniel Roth](https://github.com/danroth27)，和[Scott Addie](https://github.com/scottaddie)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）

本文档介绍用于存储和检索敏感数据的 ASP.NET Core 应用程序开发过程的技术。 永远不要将密码或其他敏感数据存储在源代码中。 不应使用生产机密进行开发或测试。 可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。

## <a name="environment-variables"></a>环境变量

使用环境变量以避免在代码中或在本地配置文件中的应用机密的存储。 环境变量重写所有以前指定的配置源的配置的值。

::: moniker range="<= aspnetcore-1.1"

通过调用配置的环境变量值的读取[AddEnvironmentVariables](/dotnet/api/microsoft.extensions.configuration.environmentvariablesextensions.addenvironmentvariables)中`Startup`构造函数：

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

请考虑在其中一个 ASP.NET Core web 应用**单个用户帐户**已启用安全性。 默认数据库连接字符串包含在项目的*appsettings.json*文件具有键`DefaultConnection`。 默认连接字符串是 localdb，这在用户模式下运行，不需要密码。 应用程序在部署期间，`DefaultConnection`使用环境变量的值可以重写密钥值。 环境变量可以存储敏感凭据与完整的连接字符串。

> [!WARNING]
> 环境变量通常存储在普通的未加密的文本。 如果计算机或进程受到攻击，可以由不受信任方访问环境变量。 可能需要更多措施，防止用户机密泄露。

## <a name="secret-manager"></a>机密管理器

密码管理器工具在 ASP.NET Core 项目的开发过程中存储敏感数据。 在此上下文中，一种敏感数据是应用程序密码。 应用程序机密存储在项目树不同的位置。 与特定项目关联或在多个项目之间共享的应用程序机密。 应用机密不会签入源代码管理。

> [!WARNING]
> 机密管理器工具不会加密存储的机密，不应被视为受信任存储区。 它是仅限开发目的。 在用户配置文件目录中的 JSON 配置文件中存储的键和值。

## <a name="how-the-secret-manager-tool-works"></a>机密管理器工具的工作原理

机密管理器工具抽象化实现详细信息，例如位置和方式存储的值。 如果不知道这些实现的详细信息，可以使用该工具。 值存储在本地计算机上 JSON 配置文件中的系统保护的用户配置文件文件夹中：

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

文件系统路径：

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="macostabmacos"></a>[macOS](#tab/macos)

文件系统路径：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

文件系统路径：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

在前面文件路径中，替换`<user_secrets_id>`与`UserSecretsId`中指定值 *.csproj*文件。

不编写代码，取决于使用机密管理器工具中保存的数据的格式的位置。 这些实现细节可能会更改。 例如，机密的值不会加密，但可能在将来。

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>安装机密管理器工具

机密管理器工具是可与.NET Core CLI，在.NET Core SDK 2.1.300 捆绑或更高版本。 有关.NET Core SDK 2.1.300 之前的版本中，工具安装是必需的。

> [!TIP]
> 运行`dotnet --version`从命令行界面中，若要查看已安装的.NET Core SDK 版本号。

如果正在使用的.NET Core SDK 包括的工具，将显示警告：

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

安装[Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) ASP.NET Core 项目中的 NuGet 包。 例如：

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

在验证工具的安装命令行界面中执行以下命令：

```console
dotnet user-secrets -h
```

机密管理器工具会显示示例用法、 选项和命令帮助：

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> 您必须位于与相同的目录 *.csproj*文件中定义的工具在运行 *.csproj*文件的`DotNetCliToolReference`元素。

::: moniker-end

## <a name="set-a-secret"></a>设置的机密

机密管理器工具对存储在用户配置文件中的特定于项目的配置设置进行操作。 若要使用用户机密，定义`UserSecretsId`中的元素`PropertyGroup`的 *.csproj*文件。 值`UserSecretsId`是任意的但仅适用于该项目。 开发人员通常会生成的 GUID `UserSecretsId`。

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> 在 Visual Studio 中，右键单击解决方案资源管理器中的项目并选择**管理用户机密**从上下文菜单。 添加了此笔势`UserSecretsId`元素中，为填充 guid *.csproj*文件。 Visual Studio 将打开*secrets.json*在文本编辑器中的文件。 内容替换为*secrets.json*与要存储的键 / 值对。 例如：
> ```json
> {
>   "Movies": {
>     "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
>     "ServiceApiKey": "12345"
>   }
> }
> ```
> JSON 结构平展后通过修改`dotnet user-secrets remove`或`dotnet user-secrets set`。 例如，运行`dotnet user-secrets remove "Movies:ConnectionString"`折叠`Movies`对象文字。 修改后的文件如下所示：
> ```json
> {
>   "Movies:ServiceApiKey": "12345"
> }
> ```

定义由一个键和其值组成的应用程序密码。 密钥是与项目相关联`UserSecretsId`值。 例如，从在其中的目录运行以下命令 *.csproj*文件是否存在：

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

在前面的示例中，冒号表示`Movies`是一个对象文字与`ServiceApiKey`属性。

机密管理器工具可以过使用从其他目录。 使用`--project`选项可提供文件系统路径，在 *.csproj*文件存在。 例如：

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

## <a name="set-multiple-secrets"></a>设置多个机密

可以通过管道传递到 JSON 设置机密一批`set`命令。 在以下示例中， *input.json*文件的内容通过管道传递给`set`命令。

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

打开命令外壳中，并执行以下命令：

  ```console
  type .\input.json | dotnet user-secrets set
  ```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

打开命令外壳中，并执行以下命令：

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

打开命令外壳中，并执行以下命令：

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>访问机密

::: moniker range=">= aspnetcore-2.0"

[ASP.NET Core 配置 API](xref:fundamentals/configuration/index)提供对机密 Manager 机密的访问。 如果项目面向.NET Framework，安装[Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 包。

在 ASP.NET Core 2.0 或更高版本，用户机密配置源时，自动添加在开发模式下项目调用<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>来初始化与预配置的默认主机的新实例。 `CreateDefaultBuilder` 调用<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*>时<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName>是<xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

当`CreateDefaultBuilder`不是调用，通过调用显式添加的用户机密配置源<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*>中`Startup`构造函数。 调用`AddUserSecrets`仅运行的应用在开发环境中，如下面的示例中所示：

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[ASP.NET Core 配置 API](xref:fundamentals/configuration/index)提供对机密 Manager 机密的访问。 安装[Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 包。

添加用户机密配置源通过调用[AddUserSecrets](/dotnet/api/microsoft.extensions.configuration.usersecretsconfigurationextensions.addusersecrets)中`Startup`构造函数：

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

可以通过检索用户机密`Configuration`API:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>映射到 POCO 的机密

将整个对象文字映射到 POCO （具有属性的简单.NET 类） 可用于聚合相关的属性。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

若要映射到 POCO，前面的机密，使用`Configuration`API 的[对象 graph 绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph)功能。 以下代码将绑定到自定义`MovieSettings`POCO 和访问`ServiceApiKey`属性值：

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

`Movies:ConnectionString`并`Movies:ServiceApiKey`机密映射到相应的属性中`MovieSettings`:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>包含机密的字符串替换

以纯文本形式存储密码是不安全。 例如，数据库连接字符串存储在*appsettings.json*可能包括指定用户的密码：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

更安全的方法是将密码存储为机密。 例如：

```console
dotnet user-secrets set "DbPassword" "pass123"
```

删除`Password`中的连接字符串中的键 / 值对*appsettings.json*。 例如：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

在上设置的机密的值[SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder)对象的[密码](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.password)属性来完成的连接字符串：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>列出机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从在其中的目录运行以下命令 *.csproj*文件是否存在：

```console
dotnet user-secrets list
```

将显示以下输出：

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

在前面的示例中，在项名称中的冒号表示对象层次结构内的*secrets.json*。

## <a name="remove-a-single-secret"></a>删除单个机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从在其中的目录运行以下命令 *.csproj*文件是否存在：

```console
dotnet user-secrets remove "Movies:ConnectionString"
```

应用程序的*secrets.json*已修改文件，以删除与关联的键 / 值对`MoviesConnectionString`密钥：

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

运行`dotnet user-secrets list`会显示以下消息：

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>删除所有机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从在其中的目录运行以下命令 *.csproj*文件是否存在：

```console
dotnet user-secrets clear
```

从已删除应用程序的所有用户机密*secrets.json*文件：

```json
{}
```

运行`dotnet user-secrets list`会显示以下消息：

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
