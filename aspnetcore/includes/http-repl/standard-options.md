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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552417"
---
* `-F|--no-formatting`

  <span data-ttu-id="df4ee-101">禁用 HTTP 响应格式的标志。</span><span class="sxs-lookup"><span data-stu-id="df4ee-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="df4ee-102">设置 HTTP 请求标头。</span><span class="sxs-lookup"><span data-stu-id="df4ee-102">Sets an HTTP request header.</span></span> <span data-ttu-id="df4ee-103">支持以下两种值格式：</span><span class="sxs-lookup"><span data-stu-id="df4ee-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="df4ee-104">指定一个文件，HTTP 响应正文应写入该文件。</span><span class="sxs-lookup"><span data-stu-id="df4ee-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="df4ee-105">例如，`--response:body "C:\response.json"`。</span><span class="sxs-lookup"><span data-stu-id="df4ee-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="df4ee-106">如果文件不存在，则创建该文件。</span><span class="sxs-lookup"><span data-stu-id="df4ee-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="df4ee-107">指定一个文件，HTTP 响应标头应写入该文件。</span><span class="sxs-lookup"><span data-stu-id="df4ee-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="df4ee-108">例如，`--response:headers "C:\response.txt"`。</span><span class="sxs-lookup"><span data-stu-id="df4ee-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="df4ee-109">如果文件不存在，则创建该文件。</span><span class="sxs-lookup"><span data-stu-id="df4ee-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="df4ee-110">支持 HTTP 响应流式处理的标志。</span><span class="sxs-lookup"><span data-stu-id="df4ee-110">A flag whose presence enables streaming of the HTTP response.</span></span>
