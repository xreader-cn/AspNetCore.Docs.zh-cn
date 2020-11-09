---
title: 通过 LibMan 在 ASP.NET Core 中获取客户端库
author: scottaddie
description: 了解如何使用库管理器 (LibMan) 在 ASP.NET Core 项目中安装客户端库资产。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: client-side/libman/index
ms.openlocfilehash: 62b6859b0a8ad0f98a2684f21c0f68dbbd67c4c1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054653"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a><span data-ttu-id="81d18-103">通过 LibMan 在 ASP.NET Core 中获取客户端库</span><span class="sxs-lookup"><span data-stu-id="81d18-103">Client-side library acquisition in ASP.NET Core with LibMan</span></span>

<span data-ttu-id="81d18-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="81d18-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="81d18-105">库管理器 (LibMan) 是一个轻量型客户端库获取工具。</span><span class="sxs-lookup"><span data-stu-id="81d18-105">Library Manager (LibMan) is a lightweight, client-side library acquisition tool.</span></span> <span data-ttu-id="81d18-106">LibMan 可从文件系统或从[内容分发网络 (CDN)](https://wikipedia.org/wiki/Content_delivery_network) 下载库和框架。</span><span class="sxs-lookup"><span data-stu-id="81d18-106">LibMan downloads popular libraries and frameworks from the file system or from a [content delivery network (CDN)](https://wikipedia.org/wiki/Content_delivery_network).</span></span> <span data-ttu-id="81d18-107">支持的 CDN 包括 [CDNJS](https://cdnjs.com/)、[jsDelivr](https://www.jsdelivr.com/) 和 [unpkg](https://unpkg.com/#/)。</span><span class="sxs-lookup"><span data-stu-id="81d18-107">The supported CDNs include [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/), and [unpkg](https://unpkg.com/#/).</span></span> <span data-ttu-id="81d18-108">将提取所选库文件，并将其置于 ASP.NET Core 项目中的相应位置。</span><span class="sxs-lookup"><span data-stu-id="81d18-108">The selected library files are fetched and placed in the appropriate location within the ASP.NET Core project.</span></span>

## <a name="libman-use-cases"></a><span data-ttu-id="81d18-109">LibMan 用例</span><span class="sxs-lookup"><span data-stu-id="81d18-109">LibMan use cases</span></span>

<span data-ttu-id="81d18-110">LibMan 提供以下优势：</span><span class="sxs-lookup"><span data-stu-id="81d18-110">LibMan offers the following benefits:</span></span>

* <span data-ttu-id="81d18-111">只会下载所需的库文件。</span><span class="sxs-lookup"><span data-stu-id="81d18-111">Only the library files you need are downloaded.</span></span>
* <span data-ttu-id="81d18-112">无需使用其他工具（例如 [Node.js](https://nodejs.org)、[npm](https://www.npmjs.com) 和 [ WebPack](https://webpack.js.org)），即可获取库中文件的子集。</span><span class="sxs-lookup"><span data-stu-id="81d18-112">Additional tooling, such as [Node.js](https://nodejs.org), [npm](https://www.npmjs.com), and [WebPack](https://webpack.js.org), isn't necessary to acquire a subset of files in a library.</span></span>
* <span data-ttu-id="81d18-113">可将文件放置在特定位置，无需执行生成任务，也不需手动进行文件复制。</span><span class="sxs-lookup"><span data-stu-id="81d18-113">Files can be placed in a specific location without resorting to build tasks or manual file copying.</span></span>

<span data-ttu-id="81d18-114">有关 LibMan 优势的详细信息，请观看 [Visual Studio 2017 中的新式前端 Web 开发：LibMan 段](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)。</span><span class="sxs-lookup"><span data-stu-id="81d18-114">For more information about LibMan's benefits, watch [Modern front-end web development in Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).</span></span>

<span data-ttu-id="81d18-115">LibMan 不是程序包管理系统。</span><span class="sxs-lookup"><span data-stu-id="81d18-115">LibMan isn't a package management system.</span></span> <span data-ttu-id="81d18-116">如果已在使用包管理器（例如 npm 或[yarn](https://yarnpkg.com)），请继续使用它们。</span><span class="sxs-lookup"><span data-stu-id="81d18-116">If you're already using a package manager, such as npm or [yarn](https://yarnpkg.com), continue doing so.</span></span> <span data-ttu-id="81d18-117">LibMan 不是为取代这些工具而开发的。</span><span class="sxs-lookup"><span data-stu-id="81d18-117">LibMan wasn't developed to replace those tools.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81d18-118">其他资源</span><span class="sxs-lookup"><span data-stu-id="81d18-118">Additional resources</span></span>

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="81d18-119">LibMan GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="81d18-119">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
