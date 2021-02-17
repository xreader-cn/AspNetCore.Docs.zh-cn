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
ms.openlocfilehash: 1cc2a01725a2af8f3dbfe1b23ea81e99bab9ef8e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551321"
---
## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="09665-101">内置 ASP.NET Core 标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="09665-101">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="09665-102">**[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-102">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span></span>

<span data-ttu-id="09665-103">**[缓存标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-103">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span></span>

<span data-ttu-id="09665-104">**[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-104">**[Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span></span>

<span data-ttu-id="09665-105">**[分布式缓存帮助程序](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-105">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span></span>

<span data-ttu-id="09665-106">**[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-106">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span></span>

<span data-ttu-id="09665-107">**[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-107">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="09665-108">**[窗体操作标记帮助程序](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-108">**[Form Action Tag Helper](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span></span>

<span data-ttu-id="09665-109">**[图像标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-109">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span></span>

<span data-ttu-id="09665-110">**[输入标记帮助程序](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-110">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="09665-111">**[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-111">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

<span data-ttu-id="09665-112">**[链接标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-112">**[Link Tag Helper](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span></span>

<span data-ttu-id="09665-113">**[部分标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-113">**[Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span></span>

<span data-ttu-id="09665-114">**[脚本标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-114">**[Script Tag Helper](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span></span>

<span data-ttu-id="09665-115">**[选择标记帮助程序](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-115">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="09665-116">**[文本区标记帮助程序](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-116">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="09665-117">**[验证消息标记帮助程序](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-117">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="09665-118">**[验证摘要标记帮助程序](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="09665-118">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>
