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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552878"
---
# <a name="visual-studio"></a>[<span data-ttu-id="8b774-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b774-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8b774-102">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="8b774-102">Create a new project.</span></span>
1. <span data-ttu-id="8b774-103">选择“辅助角色服务”。</span><span class="sxs-lookup"><span data-stu-id="8b774-103">Select **Worker Service**.</span></span> <span data-ttu-id="8b774-104">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="8b774-104">Select **Next**.</span></span>
1. <span data-ttu-id="8b774-105">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="8b774-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8b774-106">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="8b774-106">Select **Create**.</span></span>
1. <span data-ttu-id="8b774-107">在“创建辅助角色服务”对话框中，选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="8b774-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b774-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8b774-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8b774-109">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="8b774-109">Create a new project.</span></span>
1. <span data-ttu-id="8b774-110">在侧栏中的“.NET Core”下，选择“应用”。</span><span class="sxs-lookup"><span data-stu-id="8b774-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="8b774-111">在“ASP.NET Core”下，选择“辅助角色”。</span><span class="sxs-lookup"><span data-stu-id="8b774-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="8b774-112">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="8b774-112">Select **Next**.</span></span>
1. <span data-ttu-id="8b774-113">对于“目标框架”，选择“.NET Core 3.0”或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8b774-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="8b774-114">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="8b774-114">Select **Next**.</span></span>
1. <span data-ttu-id="8b774-115">在“项目名称”字段中提供名称。</span><span class="sxs-lookup"><span data-stu-id="8b774-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="8b774-116">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="8b774-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8b774-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b774-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8b774-118">将辅助角色服务 (`worker`) 模板用于命令行界面中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="8b774-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="8b774-119">下面的示例中创建了名为 `ContosoWorker` 的辅助角色服务应用。</span><span class="sxs-lookup"><span data-stu-id="8b774-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="8b774-120">执行命令时会自动为 `ContosoWorker` 应用创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="8b774-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
