---
title: HTTP 复制遥测
author: scottaddie
description: 了解 HTTP 复制收集的遥测数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502001"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="be742-103">HTTP 复制遥测</span><span class="sxs-lookup"><span data-stu-id="be742-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="be742-104">[ (复制) 的 HTTP 读取-Eval 打印循环](xref:web-api/http-repl)包含收集使用情况数据的遥测功能。</span><span class="sxs-lookup"><span data-stu-id="be742-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="be742-105">HTTP 复制团队了解如何使用该工具是非常重要的，因此可以对其进行改进。</span><span class="sxs-lookup"><span data-stu-id="be742-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="be742-106">如何选择退出</span><span class="sxs-lookup"><span data-stu-id="be742-106">How to opt out</span></span>

<span data-ttu-id="be742-107">默认情况下，已启用 HTTP 复制遥测功能。</span><span class="sxs-lookup"><span data-stu-id="be742-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="be742-108">要选择退出遥测功能，请将 `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 环境变量设置为 `1` 或 `true`。</span><span class="sxs-lookup"><span data-stu-id="be742-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="be742-109">公开</span><span class="sxs-lookup"><span data-stu-id="be742-109">Disclosure</span></span>

<span data-ttu-id="be742-110">首次运行该工具时，HttpRepl 显示类似于以下内容的文本。</span><span class="sxs-lookup"><span data-stu-id="be742-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="be742-111">文本可能会略有不同，具体取决于所运行的工具的版本。</span><span class="sxs-lookup"><span data-stu-id="be742-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="be742-112">此“首次运行”体验是 Microsoft 通知用户有关数据收集信息的方式。</span><span class="sxs-lookup"><span data-stu-id="be742-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="be742-113">数据点</span><span class="sxs-lookup"><span data-stu-id="be742-113">Data points</span></span>

<span data-ttu-id="be742-114">遥测功能不会：</span><span class="sxs-lookup"><span data-stu-id="be742-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="be742-115">收集个人数据，如用户名、电子邮件地址或 Url。</span><span class="sxs-lookup"><span data-stu-id="be742-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="be742-116">扫描 HTTP 请求或响应。</span><span class="sxs-lookup"><span data-stu-id="be742-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="be742-117">数据将安全地发送到 Microsoft 服务器，并以受限访问权限保存。</span><span class="sxs-lookup"><span data-stu-id="be742-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="be742-118">保护你的隐私对我们很重要。</span><span class="sxs-lookup"><span data-stu-id="be742-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="be742-119">如果你怀疑遥测功能正在收集敏感数据，或者数据被不安全地或未恰当地处理，请执行以下操作之一：</span><span class="sxs-lookup"><span data-stu-id="be742-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="be742-120">在 [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) 存储库中发布问题。</span><span class="sxs-lookup"><span data-stu-id="be742-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="be742-121">向发送电子邮件以 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) 进行调查。</span><span class="sxs-lookup"><span data-stu-id="be742-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="be742-122">遥测功能收集以下数据。</span><span class="sxs-lookup"><span data-stu-id="be742-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="be742-123">.NET SDK 版本</span><span class="sxs-lookup"><span data-stu-id="be742-123">.NET SDK versions</span></span> | <span data-ttu-id="be742-124">数据</span><span class="sxs-lookup"><span data-stu-id="be742-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="be742-125">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-125">>=5.0</span></span>        | <span data-ttu-id="be742-126">调用时间戳。</span><span class="sxs-lookup"><span data-stu-id="be742-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="be742-127">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-127">>=5.0</span></span>        | <span data-ttu-id="be742-128">用于确定地理位置的三个八位字节的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="be742-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="be742-129">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-129">>=5.0</span></span>        | <span data-ttu-id="be742-130">操作系统和版本。</span><span class="sxs-lookup"><span data-stu-id="be742-130">Operating system and version.</span></span> |
| <span data-ttu-id="be742-131">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-131">>=5.0</span></span>        | <span data-ttu-id="be742-132">运行此工具)  (RID 的运行时 ID。</span><span class="sxs-lookup"><span data-stu-id="be742-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="be742-133">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-133">>=5.0</span></span>        | <span data-ttu-id="be742-134">该工具是否在容器中运行。</span><span class="sxs-lookup"><span data-stu-id="be742-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="be742-135">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-135">>=5.0</span></span>        | <span data-ttu-id="be742-136"> (MAC) 地址的哈希媒体访问控制：加密 (SHA256) 对计算机进行哈希处理和唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="be742-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="be742-137">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-137">>=5.0</span></span>        | <span data-ttu-id="be742-138">内核版本。</span><span class="sxs-lookup"><span data-stu-id="be742-138">Kernel version.</span></span> |
| <span data-ttu-id="be742-139">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-139">>=5.0</span></span>        | <span data-ttu-id="be742-140">HTTP 复制版本。</span><span class="sxs-lookup"><span data-stu-id="be742-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="be742-141">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-141">>=5.0</span></span>        | <span data-ttu-id="be742-142">该工具是通过 `help` 、 `run` 或参数启动的 `connect` 。</span><span class="sxs-lookup"><span data-stu-id="be742-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="be742-143">不收集实际参数值。</span><span class="sxs-lookup"><span data-stu-id="be742-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="be742-144">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-144">>=5.0</span></span>        | <span data-ttu-id="be742-145">调用的命令 (例如， `get`) 以及是否成功。</span><span class="sxs-lookup"><span data-stu-id="be742-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="be742-146">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-146">>=5.0</span></span>        | <span data-ttu-id="be742-147">对于 `connect` 命令，是 `root` `base` 提供、还是 `openapi` 参数。</span><span class="sxs-lookup"><span data-stu-id="be742-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="be742-148">不收集实际参数值。</span><span class="sxs-lookup"><span data-stu-id="be742-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="be742-149">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-149">>=5.0</span></span>        | <span data-ttu-id="be742-150">对于 `pref` 命令，无论是 `get` 已发出的，还是 `set` 已访问的首选项。</span><span class="sxs-lookup"><span data-stu-id="be742-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="be742-151">如果不是众所周知的首选项，则该名称将进行哈希处理。</span><span class="sxs-lookup"><span data-stu-id="be742-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="be742-152">不收集该值。</span><span class="sxs-lookup"><span data-stu-id="be742-152">The value isn't collected.</span></span> |
| <span data-ttu-id="be742-153">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-153">>=5.0</span></span>        | <span data-ttu-id="be742-154">对于 `set header` 命令，为正在设置的标头名称。</span><span class="sxs-lookup"><span data-stu-id="be742-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="be742-155">如果不是众所周知的标头，则对名称进行哈希处理。</span><span class="sxs-lookup"><span data-stu-id="be742-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="be742-156">不收集该值。</span><span class="sxs-lookup"><span data-stu-id="be742-156">The value isn't collected.</span></span> |
| <span data-ttu-id="be742-157">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-157">>=5.0</span></span>        | <span data-ttu-id="be742-158">对于 `connect` 命令，是否使用了的特殊用例 `dotnet new webapi` ，以及是否通过首选项跳过它。</span><span class="sxs-lookup"><span data-stu-id="be742-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="be742-159">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="be742-159">>=5.0</span></span>        | <span data-ttu-id="be742-160">对于所有 HTTP 命令 (例如 GET、POST、PUT) ，无论是否指定了每个选项。</span><span class="sxs-lookup"><span data-stu-id="be742-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="be742-161">不收集选项的值。</span><span class="sxs-lookup"><span data-stu-id="be742-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="be742-162">其他资源</span><span class="sxs-lookup"><span data-stu-id="be742-162">Additional resources</span></span>

* [<span data-ttu-id="be742-163">.NET Core SDK 遥测</span><span class="sxs-lookup"><span data-stu-id="be742-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="be742-164">.NET Core CLI 遥测数据</span><span class="sxs-lookup"><span data-stu-id="be742-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
