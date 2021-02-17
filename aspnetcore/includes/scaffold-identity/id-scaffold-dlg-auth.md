---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551857"
---
::: moniker range=">= aspnetcore-3.0"

运行 Identity scaffolder：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **解决方案资源管理器** 中，右键单击项目 > " **添加** > **新的基架项**"。
* 在 " **添加基架** " 对话框的左窗格中，选择 " **Identity** > **添加**"。
* 在 "**添加 Identity** " 对话框中，选择所需的选项。
  * 选择现有的布局页，以便不会用错误的标记覆盖布局文件。 如果选择了现有的 *\_ 布局 cshtml* 文件，则 **不** 会覆盖它。 例如：
    * `~/Pages/Shared/_Layout.cshtml` 对于 Razor Blazor Server 包含现有 Razor 页面的基础结构的页面或项目
    * `~/Views/Shared/_Layout.cshtml` 对于 Blazor Server 包含现有 mvc 基础结构的 mvc 项目或项目
* 若要使用现有的数据上下文，请至少选择一个要重写的文件。 必须至少选择一个文件以添加数据上下文。
  * 选择数据上下文类。
  * 选择 **添加** 。
* 若要创建新的用户上下文，并可能创建的自定义用户类 Identity ：
  * 选择 **+** 按钮以创建新的 **数据上下文类**。 接受默认值或指定类 (例如 `MyApplication.Data.ApplicationDbContext`) 。
  * 选择 **添加** 。

注意：如果要创建新的用户上下文，则无需选择要重写的文件。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果你之前未安装 ASP.NET Core scaffolder，请立即安装：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

将所需的 NuGet 包引用添加到 (*.csproj*) 的项目文件。 在项目目录中运行以下命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

运行以下命令以列出 Identity scaffolder 选项：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

在项目文件夹中，运行 Identity 具有所需选项的 scaffolder。 例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令。 为数据库上下文使用正确的完全限定名：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，请在文件列表中转义分号，或将文件列表置于双引号中。 例如：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

如果在 Identity 未指定标志或标志的情况下运行 scaffolder `--files` `--useDefaultUI` ，则 Identity 会在项目中创建所有可用的 UI 页。

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

运行 Identity scaffolder：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **解决方案资源管理器** 中，右键单击项目 > " **添加** > **新的基架项**"。
* 在 " **添加基架** " 对话框的左窗格中，选择 " **Identity** > **添加**"。
* 在 "**添加 Identity** " 对话框中，选择所需的选项。
  * 选择现有的布局页，否则将用错误的标记覆盖你的布局文件。 如果选择了现有的 *\_ 布局 cshtml* 文件，则 **不** 会覆盖它。 例如：
    * `~/Pages/Shared/_Layout.cshtml` 对于 Razor 页面
    * `~/Views/Shared/_Layout.cshtml` 对于 MVC 项目
* 若要使用现有的数据上下文，请至少选择一个要重写的文件。 必须至少选择一个文件以添加数据上下文。
  * 选择数据上下文类。
  * 选择 **添加** 。
* 若要创建新的用户上下文，并可能创建的自定义用户类 Identity ：
  * 选择 **+** 按钮以创建新的 **数据上下文类**。 接受默认值或指定类 (例如 `MyApplication.Data.ApplicationDbContext`) 。
  * 选择 **添加** 。

注意：如果要创建新的用户上下文，则无需选择要重写的文件。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果你之前未安装 ASP.NET Core scaffolder，请立即安装：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

将对 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 的包引用添加到项目文件 (*.csproj*) 。 在项目目录中运行以下命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

运行以下命令以列出 Identity scaffolder 选项：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

在项目文件夹中，运行 Identity 具有所需选项的 scaffolder。 例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令。 为数据库上下文使用正确的完全限定名：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，请在文件列表中转义分号，或将文件列表置于双引号中。 例如：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

如果在 Identity 未指定标志或标志的情况下运行 scaffolder `--files` `--useDefaultUI` ，则 Identity 会在项目中创建所有可用的 UI 页。

---

::: moniker-end
