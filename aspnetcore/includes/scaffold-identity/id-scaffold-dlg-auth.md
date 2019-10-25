运行标识 scaffolder：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在**解决方案资源管理器**中，右键单击项目 >**添加** > 新的**基架项**。
* 在 "**添加基架**" 对话框的左窗格中，选择 "**标识**" > "**添加**"。
* 在 "**添加标识**" 对话框中，选择所需的选项。
  * 选择现有的布局页，否则将用错误的标记覆盖你的布局文件。 选择现有 *\_布局 cshtml*文件时，**不**会覆盖它。

 例如： MVC 项目的 Razor Pages `~/Views/Shared/_Layout.cshtml` 的 `~/Pages/Shared/_Layout.cshtml`
* 若要使用现有的数据上下文，请至少选择一个要重写的文件。 必须至少选择一个文件以添加数据上下文。
  * 选择数据上下文类。
  * 选择“添加”。
* 创建新的用户上下文，并可能为标识创建自定义用户类：
  * 选择 " **+** " 按钮以创建新的**数据上下文类**。
  * 选择“添加”。

注意：如果要创建新的用户上下文，则无需选择要重写的文件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果你之前未安装 ASP.NET Core scaffolder，请立即安装：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

将对[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)的包引用添加到项目（\*.csproj）文件中。 在项目目录中运行以下命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

运行以下命令以列出 Identity scaffolder 选项：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

在项目文件夹中，运行具有所需选项的标识 scaffolder。 例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令。 为数据库上下文使用正确的完全限定名：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，请在文件列表中转义分号，或将文件列表置于双引号中。 例如:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

如果在未指定 `--files` 标志或 `--useDefaultUI` 标志的情况下运行标识 scaffolder，则会在项目中创建所有可用的标识 UI 页。

---
