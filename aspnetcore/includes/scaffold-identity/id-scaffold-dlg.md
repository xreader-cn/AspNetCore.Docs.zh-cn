运行标识 scaffolder：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。
* 从左窗格**添加基架**对话框中，选择**标识** > **添加**。
* 在 "**添加标识**" 对话框中，选择所需的选项。
  * 选择现有的布局页，否则将用错误的标记覆盖你的布局文件。 例如 `~/Pages/Shared/_Layout.cshtml` Razor Pages MVC 项目的 `~/Views/Shared/_Layout.cshtml`
  * 选择 **+** 按钮以创建一个新**数据上下文类**。
* 选择**添加**。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果以前未安装 ASP.NET Core 基架，请立即进行安装：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

将所需的 NuGet 包引用添加到项目（\*.csproj）文件中。 在项目目录中运行以下命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

运行以下命令以列出标识基架选项：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

在项目文件夹中，运行具有所需选项的标识 scaffolder。 例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令：

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
