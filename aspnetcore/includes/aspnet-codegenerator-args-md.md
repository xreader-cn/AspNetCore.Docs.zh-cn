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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551927"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="5cffd-101">选项</span><span class="sxs-lookup"><span data-stu-id="5cffd-101">Option</span></span>               | <span data-ttu-id="5cffd-102">说明</span><span class="sxs-lookup"><span data-stu-id="5cffd-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="5cffd-103">--model 或 -m</span><span class="sxs-lookup"><span data-stu-id="5cffd-103">--model or -m</span></span>  | <span data-ttu-id="5cffd-104">要使用的模型类。</span><span class="sxs-lookup"><span data-stu-id="5cffd-104">Model class to use.</span></span> |
| <span data-ttu-id="5cffd-105">--dataContext 或 -dc</span><span class="sxs-lookup"><span data-stu-id="5cffd-105">--dataContext or -dc</span></span>  | <span data-ttu-id="5cffd-106">要使用的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="5cffd-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="5cffd-107">--bootstrapVersion 或 -b</span><span class="sxs-lookup"><span data-stu-id="5cffd-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="5cffd-108">指定启动版本。</span><span class="sxs-lookup"><span data-stu-id="5cffd-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="5cffd-109">有效值为 `3` or `4`进行求值的基于 SQL 语言的筛选器表达式。</span><span class="sxs-lookup"><span data-stu-id="5cffd-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="5cffd-110">默认值为 `4`。</span><span class="sxs-lookup"><span data-stu-id="5cffd-110">Default is `4`.</span></span> <span data-ttu-id="5cffd-111">如果需要一个 wwwroot 目录而当前不存在，则创建一个，其中包含指定版本的启动文件。</span><span class="sxs-lookup"><span data-stu-id="5cffd-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="5cffd-112">--referenceScriptLibraries 或 -scripts</span><span class="sxs-lookup"><span data-stu-id="5cffd-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="5cffd-113">在生成的视图中引用脚本库。</span><span class="sxs-lookup"><span data-stu-id="5cffd-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="5cffd-114">向“编辑”和“创建”页面添加 `_ValidationScriptsPartial`。</span><span class="sxs-lookup"><span data-stu-id="5cffd-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="5cffd-115">--layout 或 -l</span><span class="sxs-lookup"><span data-stu-id="5cffd-115">--layout or -l</span></span> | <span data-ttu-id="5cffd-116">要使用的自定义布局页。</span><span class="sxs-lookup"><span data-stu-id="5cffd-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="5cffd-117">--useDefaultLayout 或 -udl</span><span class="sxs-lookup"><span data-stu-id="5cffd-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="5cffd-118">使用视图的默认布局。</span><span class="sxs-lookup"><span data-stu-id="5cffd-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="5cffd-119">--force 或 -f</span><span class="sxs-lookup"><span data-stu-id="5cffd-119">--force or -f</span></span> | <span data-ttu-id="5cffd-120">覆盖现有文件。</span><span class="sxs-lookup"><span data-stu-id="5cffd-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="5cffd-121">--relativeFolderPath 或 -outDir</span><span class="sxs-lookup"><span data-stu-id="5cffd-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="5cffd-122">生成文件的项目的相对输出文件夹路径。</span><span class="sxs-lookup"><span data-stu-id="5cffd-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="5cffd-123">如果未指定，则会在项目文件夹中生成文件。</span><span class="sxs-lookup"><span data-stu-id="5cffd-123">If not specified, files are generated in the project folder.</span></span> |