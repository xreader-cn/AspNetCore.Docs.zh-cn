<span data-ttu-id="977e2-101">运行标识 scaffolder：</span><span class="sxs-lookup"><span data-stu-id="977e2-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="977e2-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="977e2-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="977e2-103">在**解决方案资源管理器**中，右键单击项目 >**添加** > 新的**基架项**。</span><span class="sxs-lookup"><span data-stu-id="977e2-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="977e2-104">在 "**添加基架**" 对话框的左窗格中，选择 "**标识**" > "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="977e2-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="977e2-105">在 "**添加标识**" 对话框中，选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="977e2-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="977e2-106">选择现有的布局页，否则将用错误的标记覆盖你的布局文件。</span><span class="sxs-lookup"><span data-stu-id="977e2-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="977e2-107">选择现有 *\_布局 cshtml*文件时，**不**会覆盖它。</span><span class="sxs-lookup"><span data-stu-id="977e2-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="977e2-108">例如： MVC 项目的 Razor Pages `~/Views/Shared/_Layout.cshtml` 的 `~/Pages/Shared/_Layout.cshtml`</span><span class="sxs-lookup"><span data-stu-id="977e2-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="977e2-109">若要使用现有的数据上下文，请至少选择一个要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="977e2-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="977e2-110">必须至少选择一个文件以添加数据上下文。</span><span class="sxs-lookup"><span data-stu-id="977e2-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="977e2-111">选择数据上下文类。</span><span class="sxs-lookup"><span data-stu-id="977e2-111">Select your data context class.</span></span>
  * <span data-ttu-id="977e2-112">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="977e2-112">Select **Add**.</span></span>
* <span data-ttu-id="977e2-113">创建新的用户上下文，并可能为标识创建自定义用户类：</span><span class="sxs-lookup"><span data-stu-id="977e2-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="977e2-114">选择 " **+** " 按钮以创建新的**数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="977e2-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="977e2-115">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="977e2-115">Select **Add**.</span></span>

<span data-ttu-id="977e2-116">注意：如果要创建新的用户上下文，则无需选择要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="977e2-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="977e2-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="977e2-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="977e2-118">如果你之前未安装 ASP.NET Core scaffolder，请立即安装：</span><span class="sxs-lookup"><span data-stu-id="977e2-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="977e2-119">将对[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)的包引用添加到项目（\*.csproj）文件中。</span><span class="sxs-lookup"><span data-stu-id="977e2-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="977e2-120">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="977e2-120">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="977e2-121">运行以下命令以列出 Identity scaffolder 选项：</span><span class="sxs-lookup"><span data-stu-id="977e2-121">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="977e2-122">在项目文件夹中，运行具有所需选项的标识 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="977e2-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="977e2-123">例如，若要设置默认 UI 和最小文件数的标识，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="977e2-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="977e2-124">为数据库上下文使用正确的完全限定名：</span><span class="sxs-lookup"><span data-stu-id="977e2-124">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="977e2-125">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="977e2-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="977e2-126">使用 PowerShell 时，请在文件列表中转义分号，或将文件列表置于双引号中。</span><span class="sxs-lookup"><span data-stu-id="977e2-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="977e2-127">例如:</span><span class="sxs-lookup"><span data-stu-id="977e2-127">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="977e2-128">如果在未指定 `--files` 标志或 `--useDefaultUI` 标志的情况下运行标识 scaffolder，则会在项目中创建所有可用的标识 UI 页。</span><span class="sxs-lookup"><span data-stu-id="977e2-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
