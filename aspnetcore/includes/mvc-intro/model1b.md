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
ms.openlocfilehash: 3c21d2a5604c2dfc96624fb5d161537797925fef
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552121"
---
<span data-ttu-id="036c4-101">向 `Movie` 类添加以下属性：</span><span class="sxs-lookup"><span data-stu-id="036c4-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="036c4-102">`Movie` 类包含：</span><span class="sxs-lookup"><span data-stu-id="036c4-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="036c4-103">数据库需要 `Id` 字段以获取主键。</span><span class="sxs-lookup"><span data-stu-id="036c4-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="036c4-104">`[DataType(DataType.Date)]`：[DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) 属性指定数据的类型 (`Date`)。</span><span class="sxs-lookup"><span data-stu-id="036c4-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="036c4-105">通过此特性：</span><span class="sxs-lookup"><span data-stu-id="036c4-105">With this attribute:</span></span>

  * <span data-ttu-id="036c4-106">用户无需在数据字段中输入时间信息。</span><span class="sxs-lookup"><span data-stu-id="036c4-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="036c4-107">仅显示日期，而非时间信息。</span><span class="sxs-lookup"><span data-stu-id="036c4-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="036c4-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 会在后续教程中介绍。</span><span class="sxs-lookup"><span data-stu-id="036c4-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>