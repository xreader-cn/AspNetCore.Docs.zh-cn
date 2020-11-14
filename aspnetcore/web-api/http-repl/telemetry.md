---
title: HttpRepl 遥测
author: scottaddie
description: 了解 HttpRepl 收集的遥测数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550603"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="85890-103">HttpRepl 遥测</span><span class="sxs-lookup"><span data-stu-id="85890-103">HttpRepl telemetry</span></span>

<span data-ttu-id="85890-104">[HttpRepl](xref:web-api/http-repl)包含收集使用情况数据的遥测功能。</span><span class="sxs-lookup"><span data-stu-id="85890-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="85890-105">重要的是，HttpRepl 团队了解如何使用该工具，以便能够改进。</span><span class="sxs-lookup"><span data-stu-id="85890-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="85890-106">如何选择退出</span><span class="sxs-lookup"><span data-stu-id="85890-106">How to opt out</span></span>

<span data-ttu-id="85890-107">默认情况下，HttpRepl 遥测功能处于启用状态。</span><span class="sxs-lookup"><span data-stu-id="85890-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="85890-108">要选择退出遥测功能，请将 `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 环境变量设置为 `1` 或 `true`。</span><span class="sxs-lookup"><span data-stu-id="85890-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="85890-109">公开</span><span class="sxs-lookup"><span data-stu-id="85890-109">Disclosure</span></span>

<span data-ttu-id="85890-110">首次运行该工具时，HttpRepl 显示类似于以下内容的文本。</span><span class="sxs-lookup"><span data-stu-id="85890-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="85890-111">文本可能会略有不同，具体取决于所运行的工具的版本。</span><span class="sxs-lookup"><span data-stu-id="85890-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="85890-112">此“首次运行”体验是 Microsoft 通知用户有关数据收集信息的方式。</span><span class="sxs-lookup"><span data-stu-id="85890-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="85890-113">若要禁止显示 "首次运行" 体验文本，请将 `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` 环境变量设置为 `1` 或 `true` 。</span><span class="sxs-lookup"><span data-stu-id="85890-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="85890-114">数据点</span><span class="sxs-lookup"><span data-stu-id="85890-114">Data points</span></span>

<span data-ttu-id="85890-115">遥测功能不会：</span><span class="sxs-lookup"><span data-stu-id="85890-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="85890-116">收集个人数据，如用户名、电子邮件地址或 Url。</span><span class="sxs-lookup"><span data-stu-id="85890-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="85890-117">扫描 HTTP 请求或响应。</span><span class="sxs-lookup"><span data-stu-id="85890-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="85890-118">数据将安全地发送到 Microsoft 服务器，并以受限访问权限保存。</span><span class="sxs-lookup"><span data-stu-id="85890-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="85890-119">保护你的隐私对我们很重要。</span><span class="sxs-lookup"><span data-stu-id="85890-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="85890-120">如果你怀疑遥测功能正在收集敏感数据，或者数据被不安全地或未恰当地处理，请执行以下操作之一：</span><span class="sxs-lookup"><span data-stu-id="85890-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="85890-121">在 [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) 存储库中发布问题。</span><span class="sxs-lookup"><span data-stu-id="85890-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="85890-122">向发送电子邮件以 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) 进行调查。</span><span class="sxs-lookup"><span data-stu-id="85890-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="85890-123">遥测功能收集以下数据。</span><span class="sxs-lookup"><span data-stu-id="85890-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="85890-124">.NET SDK 版本</span><span class="sxs-lookup"><span data-stu-id="85890-124">.NET SDK versions</span></span> | <span data-ttu-id="85890-125">数据</span><span class="sxs-lookup"><span data-stu-id="85890-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="85890-126">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-126">>=5.0</span></span>        | <span data-ttu-id="85890-127">调用时间戳。</span><span class="sxs-lookup"><span data-stu-id="85890-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="85890-128">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-128">>=5.0</span></span>        | <span data-ttu-id="85890-129">用于确定地理位置的三个八位字节的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="85890-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="85890-130">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-130">>=5.0</span></span>        | <span data-ttu-id="85890-131">操作系统和版本。</span><span class="sxs-lookup"><span data-stu-id="85890-131">Operating system and version.</span></span> |
| <span data-ttu-id="85890-132">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-132">>=5.0</span></span>        | <span data-ttu-id="85890-133">运行此工具)  (RID 的运行时 ID。</span><span class="sxs-lookup"><span data-stu-id="85890-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="85890-134">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-134">>=5.0</span></span>        | <span data-ttu-id="85890-135">该工具是否在容器中运行。</span><span class="sxs-lookup"><span data-stu-id="85890-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="85890-136">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-136">>=5.0</span></span>        | <span data-ttu-id="85890-137"> (MAC) 地址的哈希媒体访问控制：加密 (SHA256) 对计算机进行哈希处理和唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="85890-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="85890-138">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-138">>=5.0</span></span>        | <span data-ttu-id="85890-139">内核版本。</span><span class="sxs-lookup"><span data-stu-id="85890-139">Kernel version.</span></span> |
| <span data-ttu-id="85890-140">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-140">>=5.0</span></span>        | <span data-ttu-id="85890-141">HttpRepl 版本。</span><span class="sxs-lookup"><span data-stu-id="85890-141">HttpRepl version.</span></span> |
| <span data-ttu-id="85890-142">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-142">>=5.0</span></span>        | <span data-ttu-id="85890-143">该工具是通过 `help` 、 `run` 或参数启动的 `connect` 。</span><span class="sxs-lookup"><span data-stu-id="85890-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="85890-144">不收集实际参数值。</span><span class="sxs-lookup"><span data-stu-id="85890-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="85890-145">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-145">>=5.0</span></span>        | <span data-ttu-id="85890-146">调用的命令 (例如， `get`) 以及是否成功。</span><span class="sxs-lookup"><span data-stu-id="85890-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="85890-147">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-147">>=5.0</span></span>        | <span data-ttu-id="85890-148">对于 `connect` 命令，是 `root` `base` 提供、还是 `openapi` 参数。</span><span class="sxs-lookup"><span data-stu-id="85890-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="85890-149">不收集实际参数值。</span><span class="sxs-lookup"><span data-stu-id="85890-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="85890-150">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-150">>=5.0</span></span>        | <span data-ttu-id="85890-151">对于 `pref` 命令，无论是 `get` 已发出的，还是 `set` 已访问的首选项。</span><span class="sxs-lookup"><span data-stu-id="85890-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="85890-152">如果不是众所周知的首选项，则该名称将进行哈希处理。</span><span class="sxs-lookup"><span data-stu-id="85890-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="85890-153">不收集该值。</span><span class="sxs-lookup"><span data-stu-id="85890-153">The value isn't collected.</span></span> |
| <span data-ttu-id="85890-154">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-154">>=5.0</span></span>        | <span data-ttu-id="85890-155">对于 `set header` 命令，为正在设置的标头名称。</span><span class="sxs-lookup"><span data-stu-id="85890-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="85890-156">如果不是众所周知的标头，则对名称进行哈希处理。</span><span class="sxs-lookup"><span data-stu-id="85890-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="85890-157">不收集该值。</span><span class="sxs-lookup"><span data-stu-id="85890-157">The value isn't collected.</span></span> |
| <span data-ttu-id="85890-158">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-158">>=5.0</span></span>        | <span data-ttu-id="85890-159">对于 `connect` 命令，是否使用了的特殊用例 `dotnet new webapi` ，以及是否通过首选项跳过它。</span><span class="sxs-lookup"><span data-stu-id="85890-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="85890-160">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="85890-160">>=5.0</span></span>        | <span data-ttu-id="85890-161">对于所有 HTTP 命令 (例如 GET、POST、PUT) ，无论是否指定了每个选项。</span><span class="sxs-lookup"><span data-stu-id="85890-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="85890-162">不收集选项的值。</span><span class="sxs-lookup"><span data-stu-id="85890-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="85890-163">其他资源</span><span class="sxs-lookup"><span data-stu-id="85890-163">Additional resources</span></span>

* [<span data-ttu-id="85890-164">.NET Core SDK 遥测</span><span class="sxs-lookup"><span data-stu-id="85890-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="85890-165">.NET Core CLI 遥测数据</span><span class="sxs-lookup"><span data-stu-id="85890-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
