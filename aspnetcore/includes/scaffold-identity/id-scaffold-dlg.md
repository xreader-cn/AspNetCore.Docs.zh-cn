运行标识 scaffolder：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在**解决方案资源管理器**中，右键单击项目 > "**添加**  >  **新的基架项**"。
* 从 "**添加新的基架项**" 对话框的左窗格中，选择 "**标识**" "  >  **添加**"。
* 在 "**添加标识**" 对话框中，选择所需的选项。
  * 选择现有的布局页，或将用错误的标记覆盖布局文件：
    * `~/Pages/Shared/_Layout.cshtml`对于 Razor Pages
    * `~/Views/Shared/_Layout.cshtml`对于 MVC 项目
    * `blazorserver`默认情况下，不会为 Razor Pages 或 MVC 为 Blazor 服务器模板（）中创建的 Blazor 服务器应用配置。 将 "布局页" 项留空。
  * 选择 **+** 按钮以创建新的**数据上下文类**。 接受默认值或指定类（例如 `MyApplication.Data.ApplicationDbContext` ）。
* 选择 **添加** 。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果你之前未安装 ASP.NET Core scaffolder，请立即安装：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

将所需的 NuGet 包引用添加到项目文件（*.csproj*）。 在项目目录中运行以下命令：

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

在项目文件夹中，运行具有所需选项的标识 scaffolder。 例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令：

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
