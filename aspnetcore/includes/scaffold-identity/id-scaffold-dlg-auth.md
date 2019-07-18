<span data-ttu-id="39dca-101">运行标识 scaffolder:</span><span class="sxs-lookup"><span data-stu-id="39dca-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="39dca-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39dca-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="39dca-103">从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。</span><span class="sxs-lookup"><span data-stu-id="39dca-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="39dca-104">在 "**添加基架**" 对话框的左窗格中, 选择 "**标识** > " "**添加**"。</span><span class="sxs-lookup"><span data-stu-id="39dca-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="39dca-105">在 "**添加标识**" 对话框中, 选择所需的选项。</span><span class="sxs-lookup"><span data-stu-id="39dca-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="39dca-106">选择现有的布局页, 否则将用错误的标记覆盖你的布局文件。</span><span class="sxs-lookup"><span data-stu-id="39dca-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="39dca-107">如果选择了现有 *\_的布局 cshtml*文件, 则**不**会覆盖它。</span><span class="sxs-lookup"><span data-stu-id="39dca-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span>

 <span data-ttu-id="39dca-108">例如: `~/Pages/Shared/_Layout.cshtml`对于 MVC 项目`~/Views/Shared/_Layout.cshtml` Razor Pages</span><span class="sxs-lookup"><span data-stu-id="39dca-108">For example: `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="39dca-109">若要使用现有的数据上下文, 请至少选择一个要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="39dca-109">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="39dca-110">必须至少选择一个文件以添加数据上下文。</span><span class="sxs-lookup"><span data-stu-id="39dca-110">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="39dca-111">选择数据上下文类。</span><span class="sxs-lookup"><span data-stu-id="39dca-111">Select your data context class.</span></span>
  * <span data-ttu-id="39dca-112">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="39dca-112">Select **Add**.</span></span>
* <span data-ttu-id="39dca-113">创建新的用户上下文, 并可能为标识创建自定义用户类:</span><span class="sxs-lookup"><span data-stu-id="39dca-113">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="39dca-114">选择 **+** 按钮以创建一个新**数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="39dca-114">Select the **+** button to create a new **Data context class**.</span></span>
  * <span data-ttu-id="39dca-115">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="39dca-115">Select **Add**.</span></span>

<span data-ttu-id="39dca-116">注意:如果要创建新的用户上下文, 则无需选择要重写的文件。</span><span class="sxs-lookup"><span data-stu-id="39dca-116">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="39dca-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="39dca-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="39dca-118">如果以前未安装 ASP.NET Core 基架，请立即进行安装：</span><span class="sxs-lookup"><span data-stu-id="39dca-118">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="39dca-119">将对[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)的包引用添加到项目 (\*.csproj) 文件中。</span><span class="sxs-lookup"><span data-stu-id="39dca-119">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="39dca-120">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="39dca-120">Run the following command in the project directory:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="39dca-121">运行以下命令以列出标识基架选项：</span><span class="sxs-lookup"><span data-stu-id="39dca-121">Run the following command to list the Identity scaffolder options:</span></span>

```console
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="39dca-122">在项目文件夹中, 运行具有所需选项的标识 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="39dca-122">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="39dca-123">例如, 若要设置默认 UI 和最小文件数的标识, 请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="39dca-123">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="39dca-124">为数据库上下文使用正确的完全限定名:</span><span class="sxs-lookup"><span data-stu-id="39dca-124">Use the correct fully qualified name for your DB context:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files Account.Register
```

<span data-ttu-id="39dca-125">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="39dca-125">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="39dca-126">使用 PowerShell 时, 请在文件列表中转义分号, 或将文件列表置于双引号中。</span><span class="sxs-lookup"><span data-stu-id="39dca-126">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="39dca-127">例如:</span><span class="sxs-lookup"><span data-stu-id="39dca-127">For example:</span></span>

```console
dotnet aspnet-codegenerator identity -dc MyWeb.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="39dca-128">如果在未指定`--files`标志`--useDefaultUI`或标志的情况下运行标识 scaffolder, 则会在项目中创建所有可用的标识 UI 页。</span><span class="sxs-lookup"><span data-stu-id="39dca-128">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---
