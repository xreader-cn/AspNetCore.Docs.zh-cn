运行标识基架：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。
* 从左窗格**添加基架**对话框中，选择**标识** > **添加**。
* 在中**ADD 标识添加**对话框中，选择所需的选项。
  * 选择现有的布局页上，或不正确的标记将被覆盖布局文件。 当现有 *\_Layout.cshtml*选择文件，它是**不**覆盖。

 例如`~/Pages/Shared/_Layout.cshtml`Razor 页面`~/Views/Shared/_Layout.cshtml`对于 MVC 项目
* 若要使用现有的数据上下文，请选择要重写的至少一个文件。 必须选择至少一个文件以添加数据上下文。
  * 选择您的数据上下文类。
  * 选择**添加**。
* 若要创建新的用户上下文，并可能对标识创建一个自定义用户类：
  * 选择 **+** 按钮以创建一个新**数据上下文类**。
  * 选择**添加**。

注意:如果要创建新的用户上下文，您无需选择要重写的文件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果以前未安装 ASP.NET Core 基架，请立即进行安装：

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

添加到包引用[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)到项目 (\*.csproj) 文件。 在项目目录中运行以下命令：

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

运行以下命令以列出标识基架选项：

```console
dotnet aspnet-codegenerator identity -h
```

在项目文件夹中，运行所需的选项标识基架。 例如，若要设置默认值 UI 标识和最小的文件数，运行以下命令。 使用数据库上下文的正确完全限定的名称：

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，转义分号分隔的文件列表中，或将文件列表放在双引号内。 例如：

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

如果未指定运行标识基架`--files`标志或`--useDefaultUI`标志，所有可用的标识 UI 页将在项目中创建。

---
