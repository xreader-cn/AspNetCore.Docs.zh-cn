# <a name="visual-studio"></a>[<span data-ttu-id="d4726-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4726-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d4726-102">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="d4726-102">Create a new project.</span></span>
1. <span data-ttu-id="d4726-103">选择“辅助角色服务”  。</span><span class="sxs-lookup"><span data-stu-id="d4726-103">Select **Worker Service**.</span></span> <span data-ttu-id="d4726-104">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="d4726-104">Select **Next**.</span></span>
1. <span data-ttu-id="d4726-105">在“项目名称”字段提供项目名称，或接受默认项目名称  。</span><span class="sxs-lookup"><span data-stu-id="d4726-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d4726-106">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="d4726-106">Select **Create**.</span></span>
1. <span data-ttu-id="d4726-107">在“创建辅助角色服务”对话框中，选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="d4726-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d4726-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d4726-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d4726-109">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="d4726-109">Create a new project.</span></span>
1. <span data-ttu-id="d4726-110">在侧栏中的“.NET Core”下，选择“应用”。</span><span class="sxs-lookup"><span data-stu-id="d4726-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="d4726-111">在“ASP.NET Core”下，选择“辅助角色”。</span><span class="sxs-lookup"><span data-stu-id="d4726-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="d4726-112">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="d4726-112">Select **Next**.</span></span>
1. <span data-ttu-id="d4726-113">对于“目标框架”，选择“.NET Core 3.0”或更高版本   。</span><span class="sxs-lookup"><span data-stu-id="d4726-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="d4726-114">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="d4726-114">Select **Next**.</span></span>
1. <span data-ttu-id="d4726-115">在“项目名称”  字段中提供名称。</span><span class="sxs-lookup"><span data-stu-id="d4726-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="d4726-116">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="d4726-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d4726-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d4726-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d4726-118">将辅助角色服务 (`worker`) 模板用于命令行界面中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="d4726-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="d4726-119">下面的示例中创建了名为 `ContosoWorker` 的辅助角色服务应用。</span><span class="sxs-lookup"><span data-stu-id="d4726-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="d4726-120">执行命令时会自动为 `ContosoWorker` 应用创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="d4726-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
