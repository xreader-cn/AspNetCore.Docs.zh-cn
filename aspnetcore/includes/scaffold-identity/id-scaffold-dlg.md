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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552337"
---
运行 Identity scaffolder：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **解决方案资源管理器** 中，右键单击项目 > "**添加**  >  **新的基架项**"。
* 从 "**添加新的基架项**" 对话框的左窗格中，选择 " **Identity**  >  **添加**"。
* 在 "**添加 Identity** " 对话框中，选择所需的选项。
  * 选择现有的布局页，或将用错误的标记覆盖布局文件：
    * `~/Pages/Shared/_Layout.cshtml` 对于 Razor 页面
    * `~/Views/Shared/_Layout.cshtml` 对于 MVC 项目
    * Blazor ServerBlazor Server `blazorserver` 默认情况下，不会为默认情况下通过模板创建的应用 () 配置为 Razor 页面或 MVC。 将 "布局页" 项留空。
  * 选择 **+** 按钮以创建新的 **数据上下文类**。 接受默认值或指定类 (例如 `MyApplication.Data.ApplicationDbContext`) 。
* 选择 **添加** 。

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

在项目文件夹中，运行 Identity 具有所需选项的 scaffolder。 例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令：

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
