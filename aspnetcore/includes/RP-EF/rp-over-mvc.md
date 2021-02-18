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
ms.openlocfilehash: 6808c71b1ca43755eea4958ff9409f40e8685694
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551982"
---
<span data-ttu-id="dbafc-101">本教程介绍具有控制器和视图的 ASP.NET Core MVC 和 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="dbafc-101">This tutorial teaches ASP.NET Core MVC and Entity Framework Core with controllers and views.</span></span> <span data-ttu-id="dbafc-102">[Razor Pages](xref:razor-pages/index) 是一种备用编程模型。</span><span class="sxs-lookup"><span data-stu-id="dbafc-102">[Razor Pages](xref:razor-pages/index) is an alternative programming model.</span></span> <span data-ttu-id="dbafc-103">对于新的开发，我们建议在具有控制器和视图的 MVC 上使用 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="dbafc-103">For new development, we recommend Razor Pages over MVC with controllers and views.</span></span> <span data-ttu-id="dbafc-104">请参阅本教程的 [Razor Pages](xref:data/ef-rp/intro) 版本。</span><span class="sxs-lookup"><span data-stu-id="dbafc-104">See the [Razor Pages](xref:data/ef-rp/intro) version of this tutorial.</span></span> <span data-ttu-id="dbafc-105">每个教程涵盖其他教程没有的一些资料：</span><span class="sxs-lookup"><span data-stu-id="dbafc-105">Each tutorial covers some material the other doesn't:</span></span>

<span data-ttu-id="dbafc-106">此 MVC 教程中包含但 Razor Pages 教程中没有的某些内容：</span><span class="sxs-lookup"><span data-stu-id="dbafc-106">Some things this MVC tutorial has that the Razor Pages tutorial doesn't:</span></span>

* <span data-ttu-id="dbafc-107">在数据模型中实现继承</span><span class="sxs-lookup"><span data-stu-id="dbafc-107">Implement inheritance in the data model</span></span>
* <span data-ttu-id="dbafc-108">执行原始 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="dbafc-108">Perform raw SQL queries</span></span>
* <span data-ttu-id="dbafc-109">使用动态 LINQ 简化代码</span><span class="sxs-lookup"><span data-stu-id="dbafc-109">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="dbafc-110">Razor Pages 教程中包含但此 MVC 教程中没有的某些内容：</span><span class="sxs-lookup"><span data-stu-id="dbafc-110">Some things the Razor Pages tutorial has that this one doesn't:</span></span>

* <span data-ttu-id="dbafc-111">使用 Select 方法加载相关数据</span><span class="sxs-lookup"><span data-stu-id="dbafc-111">Use Select method to load related data</span></span>
* <span data-ttu-id="dbafc-112">EF 最佳做法。</span><span class="sxs-lookup"><span data-stu-id="dbafc-112">Best practices for EF.</span></span>