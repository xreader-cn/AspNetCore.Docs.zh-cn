---
title: ASP.NET核心开发中应用秘密的安全存储
author: rick-anderson
description: 了解如何在开发ASP.NET核心应用期间将敏感信息存储和检索为应用机密。
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661803"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET核心开发中应用秘密的安全存储

::: moniker range=">= aspnetcore-3.0"

由[里克·安德森](https://twitter.com/RickAndMSFT)、[柯克·拉金](https://twitter.com/serpent5)、[丹尼尔·罗斯](https://github.com/danroth27)和[斯科特·阿迪](https://github.com/scottaddie)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）

本文档介绍在开发计算机上开发ASP.NET酷睿应用期间存储和检索敏感数据的技术。 切勿在源代码中存储密码或其他敏感数据。 生产机密不应用于开发或测试。 不应将机密随应用一起部署。 相反，应通过受控方式（如环境变量、Azure 密钥保管库等）在生产环境中提供机密。您可以使用[Azure 密钥保管库配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。

## <a name="environment-variables"></a>环境变量

环境变量用于避免在代码或本地配置文件中存储应用机密。 环境变量覆盖所有以前指定的配置源的配置值。

请考虑一个 ASP.NET 核心 Web 应用，其中启用**了单个用户帐户**安全性。 默认数据库连接字符串包含在项目的*appsettings.json*文件中，其中包含密钥`DefaultConnection`。 默认连接字符串用于 LocalDB，该字符串在用户模式下运行，不需要密码。 在应用部署期间，`DefaultConnection`可以使用环境变量的值重写键值。 环境变量可能将完整的连接字符串存储为具有敏感凭据。

> [!WARNING]
> 环境变量通常以纯、未加密的文本存储。 如果计算机或进程遭到破坏，则不受信任的方可以访问环境变量。 可能需要采取其他措施防止泄露用户机密。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>机密管理器

在开发ASP.NET核心项目期间，秘密管理器工具存储敏感数据。 在此上下文中，一段敏感数据是应用机密。 应用机密存储在与项目树不同的位置。 应用机密与特定项目关联或跨多个项目共享。 应用机密不会签入源代码管理。

> [!WARNING]
> 秘密管理器工具不加密存储的秘密，不应被视为受信任的存储。 它仅用于开发目的。 键和值存储在用户配置文件目录中的 JSON 配置文件中。

## <a name="how-the-secret-manager-tool-works"></a>秘密管理器工具的工作原理

"秘密管理器"工具会抽象出实现详细信息，例如值的存储位置和方式。 您可以在不知道这些实现详细信息的情况下使用该工具。 这些值存储在本地计算机上的受系统保护的用户配置文件文件夹中的 JSON 配置文件中：

# <a name="windows"></a>[Windows](#tab/windows)

文件系统路径：

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

文件系统路径：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

在前面的文件路径中，替换为`<user_secrets_id>` `UserSecretsId` *.csproj*文件中指定的值。

不要编写依赖于使用机密管理器工具保存的数据的位置或格式的代码。 这些实现详细信息可能会更改。 例如，机密值未加密，但将来可能已加密。

## <a name="enable-secret-storage"></a>启用机密存储

"机密管理器"工具可对存储在用户配置文件中的特定于项目的配置设置进行操作。

机密管理器工具包括 .NET Core SDK 3.0.100 或更高版本中`init`的命令。 要使用用户机密，请运行项目目录中的以下命令：

```dotnetcli
dotnet user-secrets init
```

前面的命令在`UserSecretsId`*.csproj*文件中添加一个`PropertyGroup`元素。 默认情况下，的内部`UserSecretsId`文本是 GUID。 内部文本是任意的，但对于项目是唯一的。

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

在 Visual Studio 中，右键单击解决方案资源管理器中的项目，然后从上下文菜单中选择 **"管理用户机密**"。 此手势将一`UserSecretsId`个使用 GUID 填充的元素添加到 *.csproj*文件中。

## <a name="set-a-secret"></a>设置机密

定义由键及其值组成的应用机密。 机密与项目`UserSecretsId`的值相关联。 例如，从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

在前面的示例中，冒号表示`Movies`是具有`ServiceApiKey`属性的对象文本。

机密管理器工具也可以从其他目录使用。 使用`--project`选项提供*存在 .csproj*文件的文件系统路径。 例如：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>视觉工作室中的 JSON 结构拼合

可视化工作室的 **"管理用户机密**"手势将在文本编辑器中打开*一个机密.json*文件。 将*机密*内容替换为要存储的键值对。 例如：

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

通过`dotnet user-secrets remove`或`dotnet user-secrets set`进行修改后，JSON 结构将展平。 例如，运行`dotnet user-secrets remove "Movies:ConnectionString"`将折叠`Movies`对象文本。 修改后的文件类似于以下内容：

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>设置多个机密

通过将 JSON 管道到`set`命令，可以设置一批机密。 在下面的示例中 *，input.json*文件的内容被传送到命令`set`。

# <a name="windows"></a>[Windows](#tab/windows)

打开命令外壳，并执行以下命令：

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

打开命令外壳，并执行以下命令：

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>访问机密

[ASP.NET核心配置 API](xref:fundamentals/configuration/index)提供对机密管理器机密的访问。

在 ASP.NET Core 2.0 或更高版本中，当项目调用<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>使用预配置默认值初始化主机的新实例时，用户机密配置源将自动在开发模式下添加。 `CreateDefaultBuilder`当<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName>为<xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>时调用 ：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

未`CreateDefaultBuilder`调用时，通过在<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>`Startup`构造函数中调用显式添加用户机密配置源。 仅在`AddUserSecrets`应用在开发环境中运行时调用，如以下示例所示：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

可通过`Configuration`API 检索用户机密：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a>将机密映射到 POCO

将整个对象文本映射到 POCO（具有属性的简单 .NET 类）可用于聚合相关属性。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

要将上述机密映射到 POCO，请使用`Configuration`API[的对象图形绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph)功能。 以下代码绑定到自定义`MovieSettings`POCO 并访问`ServiceApiKey`属性值：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

和`Movies:ConnectionString``Movies:ServiceApiKey`机密映射到 中的`MovieSettings`相应属性：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>字符串替换与机密

以纯文本形式存储密码不安全。 例如，存储在*appsettings.json*中的数据库连接字符串可能包含指定用户的密码：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

更安全的方法是将密码存储为机密。 例如：

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

从`Password`*appsettings.json*中的连接字符串中删除键值对。 例如：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

可以在<xref:System.Data.SqlClient.SqlConnectionStringBuilder>对象<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>的属性上设置机密的值以完成连接字符串：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>列出机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets list
```

将显示以下输出：

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

在前面的示例中，键名称中的冒号表示机密中的对象层次结构 *。*

## <a name="remove-a-single-secret"></a>删除单个机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

应用的*机密.json*文件已修改以删除与`MoviesConnectionString`密钥关联的键值对：

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`显示以下消息：

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>删除所有机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets clear
```

应用程序的所有用户机密已从*机密.json*文件中删除：

```json
{}
```

正在`dotnet user-secrets list`运行将显示以下消息：

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>其他资源

* 有关从 IIS 访问机密管理器的信息，请参阅[此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328)。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

由[里克·安德森](https://twitter.com/RickAndMSFT)，[丹尼尔·罗斯](https://github.com/danroth27)和[斯科特·艾迪](https://github.com/scottaddie)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）

本文档介绍在开发计算机上开发ASP.NET酷睿应用期间存储和检索敏感数据的技术。 切勿在源代码中存储密码或其他敏感数据。 生产机密不应用于开发或测试。 不应将机密随应用一起部署。 相反，应通过受控方式（如环境变量、Azure 密钥保管库等）在生产环境中提供机密。您可以使用[Azure 密钥保管库配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。

## <a name="environment-variables"></a>环境变量

环境变量用于避免在代码或本地配置文件中存储应用机密。 环境变量覆盖所有以前指定的配置源的配置值。

请考虑一个 ASP.NET 核心 Web 应用，其中启用**了单个用户帐户**安全性。 默认数据库连接字符串包含在项目的*appsettings.json*文件中，其中包含密钥`DefaultConnection`。 默认连接字符串用于 LocalDB，该字符串在用户模式下运行，不需要密码。 在应用部署期间，`DefaultConnection`可以使用环境变量的值重写键值。 环境变量可能将完整的连接字符串存储为具有敏感凭据。

> [!WARNING]
> 环境变量通常以纯、未加密的文本存储。 如果计算机或进程遭到破坏，则不受信任的方可以访问环境变量。 可能需要采取其他措施防止泄露用户机密。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>机密管理器

在开发ASP.NET核心项目期间，秘密管理器工具存储敏感数据。 在此上下文中，一段敏感数据是应用机密。 应用机密存储在与项目树不同的位置。 应用机密与特定项目关联或跨多个项目共享。 应用机密不会签入源代码管理。

> [!WARNING]
> 秘密管理器工具不加密存储的秘密，不应被视为受信任的存储。 它仅用于开发目的。 键和值存储在用户配置文件目录中的 JSON 配置文件中。

## <a name="how-the-secret-manager-tool-works"></a>秘密管理器工具的工作原理

"秘密管理器"工具会抽象出实现详细信息，例如值的存储位置和方式。 您可以在不知道这些实现详细信息的情况下使用该工具。 这些值存储在本地计算机上的受系统保护的用户配置文件文件夹中的 JSON 配置文件中：

# <a name="windows"></a>[Windows](#tab/windows)

文件系统路径：

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

文件系统路径：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

在前面的文件路径中，替换为`<user_secrets_id>` `UserSecretsId` *.csproj*文件中指定的值。

不要编写依赖于使用机密管理器工具保存的数据的位置或格式的代码。 这些实现详细信息可能会更改。 例如，机密值未加密，但将来可能已加密。

## <a name="enable-secret-storage"></a>启用机密存储

"机密管理器"工具可对存储在用户配置文件中的特定于项目的配置设置进行操作。

要使用用户机密，请定义`UserSecretsId` `PropertyGroup` *.csproj*文件中的元素。 的内部`UserSecretsId`文本是任意的，但对于项目是唯一的。 开发人员通常为 生成`UserSecretsId`GUID。

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> 在 Visual Studio 中，右键单击解决方案资源管理器中的项目，然后从上下文菜单中选择 **"管理用户机密**"。 此手势将一`UserSecretsId`个使用 GUID 填充的元素添加到 *.csproj*文件中。

## <a name="set-a-secret"></a>设置机密

定义由键及其值组成的应用机密。 机密与项目`UserSecretsId`的值相关联。 例如，从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

在前面的示例中，冒号表示`Movies`是具有`ServiceApiKey`属性的对象文本。

机密管理器工具也可以从其他目录使用。 使用`--project`选项提供*存在 .csproj*文件的文件系统路径。 例如：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>视觉工作室中的 JSON 结构拼合

可视化工作室的 **"管理用户机密**"手势将在文本编辑器中打开*一个机密.json*文件。 将*机密*内容替换为要存储的键值对。 例如：

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

通过`dotnet user-secrets remove`或`dotnet user-secrets set`进行修改后，JSON 结构将展平。 例如，运行`dotnet user-secrets remove "Movies:ConnectionString"`将折叠`Movies`对象文本。 修改后的文件类似于以下内容：

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>设置多个机密

通过将 JSON 管道到`set`命令，可以设置一批机密。 在下面的示例中 *，input.json*文件的内容被传送到命令`set`。

# <a name="windows"></a>[Windows](#tab/windows)

打开命令外壳，并执行以下命令：

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

打开命令外壳，并执行以下命令：

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>访问机密

[ASP.NET核心配置 API](xref:fundamentals/configuration/index)提供对机密管理器机密的访问。

如果项目的目标是 .NET 框架，请安装[Microsoft.扩展.配置.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 包。


在 ASP.NET Core 2.0 或更高版本中，当项目调用<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>使用预配置默认值初始化主机的新实例时，用户机密配置源将自动在开发模式下添加。 `CreateDefaultBuilder`当<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName>为<xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>时调用 ：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


未`CreateDefaultBuilder`调用时，通过在<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>`Startup`构造函数中调用显式添加用户机密配置源。 仅在`AddUserSecrets`应用在开发环境中运行时调用，如以下示例所示：

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

可通过`Configuration`API 检索用户机密：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>将机密映射到 POCO

将整个对象文本映射到 POCO（具有属性的简单 .NET 类）可用于聚合相关属性。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

要将上述机密映射到 POCO，请使用`Configuration`API[的对象图形绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph)功能。 以下代码绑定到自定义`MovieSettings`POCO 并访问`ServiceApiKey`属性值：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

和`Movies:ConnectionString``Movies:ServiceApiKey`机密映射到 中的`MovieSettings`相应属性：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>字符串替换与机密

以纯文本形式存储密码不安全。 例如，存储在*appsettings.json*中的数据库连接字符串可能包含指定用户的密码：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

更安全的方法是将密码存储为机密。 例如：

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

从`Password`*appsettings.json*中的连接字符串中删除键值对。 例如：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

可以在<xref:System.Data.SqlClient.SqlConnectionStringBuilder>对象<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>的属性上设置机密的值以完成连接字符串：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>列出机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets list
```

将显示以下输出：

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

在前面的示例中，键名称中的冒号表示机密中的对象层次结构 *。*

## <a name="remove-a-single-secret"></a>删除单个机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

应用的*机密.json*文件已修改以删除与`MoviesConnectionString`密钥关联的键值对：

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

正在`dotnet user-secrets list`运行将显示以下消息：

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>删除所有机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从*存在 .csproj*文件的目录中运行以下命令：

```dotnetcli
dotnet user-secrets clear
```

应用程序的所有用户机密已从*机密.json*文件中删除：

```json
{}
```

正在`dotnet user-secrets list`运行将显示以下消息：

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>其他资源

* 有关从 IIS 访问机密管理器的信息，请参阅[此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328)。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end