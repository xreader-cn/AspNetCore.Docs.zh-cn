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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552158"
---
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="95b1f-101">在类库中共享互操作代码</span><span class="sxs-lookup"><span data-stu-id="95b1f-101">Share interop code in a class library</span></span>

<span data-ttu-id="95b1f-102">可在类库中包含 JS 互操作代码，以便能在 NuGet 包中共享代码。</span><span class="sxs-lookup"><span data-stu-id="95b1f-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="95b1f-103">类库会处理在生成的程序集中嵌入 JavaScript 资源的操作。</span><span class="sxs-lookup"><span data-stu-id="95b1f-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="95b1f-104">JavaScript 文件位于 `wwwroot` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="95b1f-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="95b1f-105">工具负责在生成库时嵌入资源。</span><span class="sxs-lookup"><span data-stu-id="95b1f-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="95b1f-106">按引用任何其他 NuGet 包的方式在应用的项目文件中引用生成的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="95b1f-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="95b1f-107">包还原后，应用代码可如同是 C# 一样调入 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="95b1f-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="95b1f-108">有关详细信息，请参阅 <xref:blazor/components/class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="95b1f-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>
