---
title: 使用 HTTP REPL 测试 Web API
author: scottaddie
description: 了解如何使用 HTTP REPL .NET Core 全局工具来浏览和测试 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2019
uid: web-api/http-repl
ms.openlocfilehash: 1ceda6182c62bb1be06cd95f14e6a46a1809253e
ms.sourcegitcommit: 059ab380744fa3be3b69aa90d431b563c57092cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68410885"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="9c382-103">使用 HTTP REPL 测试 Web API</span><span class="sxs-lookup"><span data-stu-id="9c382-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="9c382-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9c382-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9c382-105">HTTP 读取–求值–打印循环 (REPL)：</span><span class="sxs-lookup"><span data-stu-id="9c382-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="9c382-106">一种轻量级跨平台命令行工具，在所有支持的 .NET Core 的位置都可得到支持。</span><span class="sxs-lookup"><span data-stu-id="9c382-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="9c382-107">用于发出 HTTP 请求以测试 ASP.NET Core Web API 并查看其结果。</span><span class="sxs-lookup"><span data-stu-id="9c382-107">Used for making HTTP requests to test ASP.NET Core web APIs and view their results.</span></span>

<span data-ttu-id="9c382-108">支持以下 [HTTP 谓词](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)：</span><span class="sxs-lookup"><span data-stu-id="9c382-108">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="9c382-109">DELETE</span><span class="sxs-lookup"><span data-stu-id="9c382-109">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="9c382-110">GET</span><span class="sxs-lookup"><span data-stu-id="9c382-110">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="9c382-111">HEAD</span><span class="sxs-lookup"><span data-stu-id="9c382-111">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="9c382-112">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="9c382-112">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="9c382-113">PATCH</span><span class="sxs-lookup"><span data-stu-id="9c382-113">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="9c382-114">POST</span><span class="sxs-lookup"><span data-stu-id="9c382-114">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="9c382-115">PUT</span><span class="sxs-lookup"><span data-stu-id="9c382-115">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="9c382-116">若要继续操作，请[查看或下载示例 ASP.NET Core Web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples)（[下载方式](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="9c382-116">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9c382-117">系统必备</span><span class="sxs-lookup"><span data-stu-id="9c382-117">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="9c382-118">安装</span><span class="sxs-lookup"><span data-stu-id="9c382-118">Installation</span></span>

<span data-ttu-id="9c382-119">若要安装 HTTP REPL，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-119">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version 3.0.0-*
```

<span data-ttu-id="9c382-120">从 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet 包安装 [.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)。</span><span class="sxs-lookup"><span data-stu-id="9c382-120">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="9c382-121">用法</span><span class="sxs-lookup"><span data-stu-id="9c382-121">Usage</span></span>

<span data-ttu-id="9c382-122">成功安装该工具后，运行以下命令以启动 HTTP REPL：</span><span class="sxs-lookup"><span data-stu-id="9c382-122">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="9c382-123">若要查看可用的 HTTP REPL 命令，请运行下面的一个命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-123">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="9c382-124">显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="9c382-124">The following output is displayed:</span></span>

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
set swagger    Sets the swagger document to use for information about the current server
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="9c382-125">HTTP REPL 提供命令完成。</span><span class="sxs-lookup"><span data-stu-id="9c382-125">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="9c382-126">按 Tab <kbd></kbd>键可循环访问补全所键入字符或 API 终结点的命令的列表。</span><span class="sxs-lookup"><span data-stu-id="9c382-126">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="9c382-127">以下部分概述了可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="9c382-127">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="9c382-128">连接到 Web API</span><span class="sxs-lookup"><span data-stu-id="9c382-128">Connect to the web API</span></span>

<span data-ttu-id="9c382-129">运行以下命令，连接到 Web API：</span><span class="sxs-lookup"><span data-stu-id="9c382-129">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="9c382-130">`<BASE URI>` 是 Web API 的基 URI。</span><span class="sxs-lookup"><span data-stu-id="9c382-130">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="9c382-131">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-131">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="9c382-132">或者，在 HTTP REPL 运行期间的任何时刻运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-132">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="9c382-133">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-133">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="9c382-134">指向 Web API 的 Swagger 文档</span><span class="sxs-lookup"><span data-stu-id="9c382-134">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="9c382-135">若要正确检查 Web API，请将相对 URI 设置为 Web API 的 Swagger 文档。</span><span class="sxs-lookup"><span data-stu-id="9c382-135">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="9c382-136">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-136">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="9c382-137">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-137">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="9c382-138">浏览 Web API</span><span class="sxs-lookup"><span data-stu-id="9c382-138">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="9c382-139">查看可用的终结点</span><span class="sxs-lookup"><span data-stu-id="9c382-139">View available endpoints</span></span>

<span data-ttu-id="9c382-140">若要在 Web API 地址的当前路径中列出不同的终结点（控制器），请运行 `ls` 或 `dir` 命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-140">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="9c382-141">以下输出格式随即显示：</span><span class="sxs-lookup"><span data-stu-id="9c382-141">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="9c382-142">上述输出指示有两个控制器可用：`Fruits` 和 `People`。</span><span class="sxs-lookup"><span data-stu-id="9c382-142">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="9c382-143">这两个控制器都支持无参数 HTTP GET 和 POST 操作。</span><span class="sxs-lookup"><span data-stu-id="9c382-143">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="9c382-144">导航到特定控制器可显示更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="9c382-144">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="9c382-145">例如，以下命令的输出显示 `Fruits` 控制器还支持 HTTP GET、PUT 和 DELETE 操作。</span><span class="sxs-lookup"><span data-stu-id="9c382-145">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="9c382-146">其中每个操作都需要路由中有一个 `id` 参数：</span><span class="sxs-lookup"><span data-stu-id="9c382-146">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="9c382-147">或者，运行 `ui` 命令，在浏览器中打开 Web API 的 Swagger UI 页。</span><span class="sxs-lookup"><span data-stu-id="9c382-147">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="9c382-148">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-148">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="9c382-149">导航到一个终结点</span><span class="sxs-lookup"><span data-stu-id="9c382-149">Navigate to an endpoint</span></span>

<span data-ttu-id="9c382-150">若要在 Web API 上导航到其他终结点，请运行 `cd` 命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-150">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="9c382-151">`cd` 命令后的路径不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="9c382-151">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="9c382-152">以下输出格式随即显示：</span><span class="sxs-lookup"><span data-stu-id="9c382-152">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="9c382-153">自定义 HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="9c382-153">Customize the HTTP REPL</span></span>

<span data-ttu-id="9c382-154">可自定义 HTTP REPL 的默认[颜色](#set-color-preferences)。</span><span class="sxs-lookup"><span data-stu-id="9c382-154">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="9c382-155">此外，还可定义[默认文本编辑器](#set-the-default-text-editor)。</span><span class="sxs-lookup"><span data-stu-id="9c382-155">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="9c382-156">HTTP REPL 首选项在当前会话中保持不变，并且也会在之后的会话中使用。</span><span class="sxs-lookup"><span data-stu-id="9c382-156">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="9c382-157">修改后，首选项存储在以下文件中：</span><span class="sxs-lookup"><span data-stu-id="9c382-157">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="9c382-158">Linux</span><span class="sxs-lookup"><span data-stu-id="9c382-158">Linux</span></span>](#tab/linux)

<span data-ttu-id="9c382-159">%HOME%/.httpreplprefs </span><span class="sxs-lookup"><span data-stu-id="9c382-159">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="9c382-160">macOS</span><span class="sxs-lookup"><span data-stu-id="9c382-160">macOS</span></span>](#tab/macos)

<span data-ttu-id="9c382-161">%HOME%/.httpreplprefs </span><span class="sxs-lookup"><span data-stu-id="9c382-161">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="9c382-162">Windows</span><span class="sxs-lookup"><span data-stu-id="9c382-162">Windows</span></span>](#tab/windows)

<span data-ttu-id="9c382-163">%USERPROFILE%\\.httpreplprefs </span><span class="sxs-lookup"><span data-stu-id="9c382-163">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="9c382-164">.httpreplprefs  文件将在启动时加载，并且在运行时不监控对其的更改。</span><span class="sxs-lookup"><span data-stu-id="9c382-164">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="9c382-165">对文件的手动修改只会在重启该工具后生效。</span><span class="sxs-lookup"><span data-stu-id="9c382-165">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="9c382-166">查看设置</span><span class="sxs-lookup"><span data-stu-id="9c382-166">View the settings</span></span>

<span data-ttu-id="9c382-167">若要查看可用的设置，请运行 `pref get` 命令。</span><span class="sxs-lookup"><span data-stu-id="9c382-167">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="9c382-168">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-168">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="9c382-169">上述命令显示可用的键值对：</span><span class="sxs-lookup"><span data-stu-id="9c382-169">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="9c382-170">设置颜色首选项</span><span class="sxs-lookup"><span data-stu-id="9c382-170">Set color preferences</span></span>

<span data-ttu-id="9c382-171">当前仅 JSON 支持响应着色。</span><span class="sxs-lookup"><span data-stu-id="9c382-171">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="9c382-172">若要自定义默认的 HTTP REPL 工具着色，请找到与要更改的颜色相对应的键。</span><span class="sxs-lookup"><span data-stu-id="9c382-172">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="9c382-173">有关如何查找键的说明，请参阅[查看设置](#view-the-settings)部分。</span><span class="sxs-lookup"><span data-stu-id="9c382-173">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="9c382-174">例如，将 `colors.json` 键值从 `Green` 更改为 `White`如下所示：</span><span class="sxs-lookup"><span data-stu-id="9c382-174">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="9c382-175">只能使用[允许的颜色](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)。</span><span class="sxs-lookup"><span data-stu-id="9c382-175">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="9c382-176">后续 HTTP 请求使用新着色显示输出。</span><span class="sxs-lookup"><span data-stu-id="9c382-176">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="9c382-177">如果未设置特定颜色键，则会考虑使用更通用的键。</span><span class="sxs-lookup"><span data-stu-id="9c382-177">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="9c382-178">若要演示此回退行为，请考虑使用以下示例：</span><span class="sxs-lookup"><span data-stu-id="9c382-178">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="9c382-179">如果 `colors.json.name` 没有值，则使用 `colors.json.string`。</span><span class="sxs-lookup"><span data-stu-id="9c382-179">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="9c382-180">如果 `colors.json.string` 没有值，则使用 `colors.json.literal`。</span><span class="sxs-lookup"><span data-stu-id="9c382-180">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="9c382-181">如果 `colors.json.literal` 没有值，则使用 `colors.json`。</span><span class="sxs-lookup"><span data-stu-id="9c382-181">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="9c382-182">如果 `colors.json` 没有值，则使用命令行界面的默认文本颜色 (`AllowedColors.None`)。</span><span class="sxs-lookup"><span data-stu-id="9c382-182">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="9c382-183">设置缩进尺寸</span><span class="sxs-lookup"><span data-stu-id="9c382-183">Set indentation size</span></span>

<span data-ttu-id="9c382-184">当前，仅 JSON 支持响应缩进尺寸自定义。</span><span class="sxs-lookup"><span data-stu-id="9c382-184">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="9c382-185">默认尺寸为两个空格。</span><span class="sxs-lookup"><span data-stu-id="9c382-185">The default size is two spaces.</span></span> <span data-ttu-id="9c382-186">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-186">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="9c382-187">若要更改默认尺寸，请设置 `formatting.json.indentSize` 键。</span><span class="sxs-lookup"><span data-stu-id="9c382-187">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="9c382-188">例如，始终使用四个空格：</span><span class="sxs-lookup"><span data-stu-id="9c382-188">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="9c382-189">后续响应遵循四个空格的设置：</span><span class="sxs-lookup"><span data-stu-id="9c382-189">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-indentation-size"></a><span data-ttu-id="9c382-190">设置缩进尺寸</span><span class="sxs-lookup"><span data-stu-id="9c382-190">Set indentation size</span></span>

<span data-ttu-id="9c382-191">当前，仅 JSON 支持响应缩进尺寸自定义。</span><span class="sxs-lookup"><span data-stu-id="9c382-191">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="9c382-192">默认尺寸为两个空格。</span><span class="sxs-lookup"><span data-stu-id="9c382-192">The default size is two spaces.</span></span> <span data-ttu-id="9c382-193">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-193">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="9c382-194">若要更改默认尺寸，请设置 `formatting.json.indentSize` 键。</span><span class="sxs-lookup"><span data-stu-id="9c382-194">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="9c382-195">例如，始终使用四个空格：</span><span class="sxs-lookup"><span data-stu-id="9c382-195">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="9c382-196">后续响应遵循四个空格的设置：</span><span class="sxs-lookup"><span data-stu-id="9c382-196">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="9c382-197">设置默认文本编辑器</span><span class="sxs-lookup"><span data-stu-id="9c382-197">Set the default text editor</span></span>

<span data-ttu-id="9c382-198">默认情况下，HTTP REPL 未配置任何文本编辑器供使用。</span><span class="sxs-lookup"><span data-stu-id="9c382-198">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="9c382-199">若要测试需要 HTTP 请求正文的 Web API 方法，必须设置默认文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="9c382-199">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="9c382-200">HTTP REPL 工具将启动已配置的文本编辑器，专门用于编写请求正文。</span><span class="sxs-lookup"><span data-stu-id="9c382-200">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="9c382-201">运行以下命令，将你青睐的文本编辑器设置为默认编辑器：</span><span class="sxs-lookup"><span data-stu-id="9c382-201">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="9c382-202">在上述命令中，`<EXECUTABLE>` 是文本编辑器可执行文件的完整路径。</span><span class="sxs-lookup"><span data-stu-id="9c382-202">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="9c382-203">例如，运行以下命令，将 Visual Studio Code 设置为默认文本编辑器：</span><span class="sxs-lookup"><span data-stu-id="9c382-203">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="9c382-204">Linux</span><span class="sxs-lookup"><span data-stu-id="9c382-204">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="9c382-205">macOS</span><span class="sxs-lookup"><span data-stu-id="9c382-205">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="9c382-206">Windows</span><span class="sxs-lookup"><span data-stu-id="9c382-206">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="9c382-207">若要使用特定 CLI 参数启动默认文本编辑器，请设置 `editor.command.default.arguments` 键。</span><span class="sxs-lookup"><span data-stu-id="9c382-207">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="9c382-208">例如，假设 Visual Studio Code 为默认文本编辑器，并且你总是希望 HTTP REPL 在禁用了扩展的新会话中打开 Visual Studio Code。</span><span class="sxs-lookup"><span data-stu-id="9c382-208">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="9c382-209">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-209">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="9c382-210">测试 HTTP GET 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-210">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9c382-211">摘要</span><span class="sxs-lookup"><span data-stu-id="9c382-211">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9c382-212">自变量</span><span class="sxs-lookup"><span data-stu-id="9c382-212">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9c382-213">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="9c382-213">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9c382-214">选项</span><span class="sxs-lookup"><span data-stu-id="9c382-214">Options</span></span>

<span data-ttu-id="9c382-215">`get` 命令可以使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="9c382-215">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="9c382-216">示例</span><span class="sxs-lookup"><span data-stu-id="9c382-216">Example</span></span>

<span data-ttu-id="9c382-217">发出 HTTP GET 请求：</span><span class="sxs-lookup"><span data-stu-id="9c382-217">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="9c382-218">在支持 `get` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-218">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="9c382-219">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="9c382-219">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="9c382-220">通过将参数传递给 `get` 命令来检索特定记录：</span><span class="sxs-lookup"><span data-stu-id="9c382-220">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="9c382-221">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="9c382-221">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="9c382-222">测试 HTTP POST 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-222">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9c382-223">摘要</span><span class="sxs-lookup"><span data-stu-id="9c382-223">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9c382-224">自变量</span><span class="sxs-lookup"><span data-stu-id="9c382-224">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9c382-225">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="9c382-225">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9c382-226">选项</span><span class="sxs-lookup"><span data-stu-id="9c382-226">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="9c382-227">示例</span><span class="sxs-lookup"><span data-stu-id="9c382-227">Example</span></span>

<span data-ttu-id="9c382-228">发出 HTTP POST 请求：</span><span class="sxs-lookup"><span data-stu-id="9c382-228">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="9c382-229">在支持 `post` 命令的终结点上运行该命令：</span><span class="sxs-lookup"><span data-stu-id="9c382-229">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="9c382-230">在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。</span><span class="sxs-lookup"><span data-stu-id="9c382-230">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="9c382-231">默认文本编辑器打开一个 .tmp  文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。</span><span class="sxs-lookup"><span data-stu-id="9c382-231">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="9c382-232">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-232">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="9c382-233">若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。</span><span class="sxs-lookup"><span data-stu-id="9c382-233">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="9c382-234">修改 JSON 模板以满足模型验证要求：</span><span class="sxs-lookup"><span data-stu-id="9c382-234">Modify the JSON template to satisfy model validation requirements:</span></span>

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. <span data-ttu-id="9c382-235">保存 .tmp  文件，并关闭文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="9c382-235">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="9c382-236">以下输出显示在命令行界面中：</span><span class="sxs-lookup"><span data-stu-id="9c382-236">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="9c382-237">测试 HTTP PUT 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-237">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9c382-238">摘要</span><span class="sxs-lookup"><span data-stu-id="9c382-238">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9c382-239">自变量</span><span class="sxs-lookup"><span data-stu-id="9c382-239">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9c382-240">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="9c382-240">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9c382-241">选项</span><span class="sxs-lookup"><span data-stu-id="9c382-241">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="9c382-242">示例</span><span class="sxs-lookup"><span data-stu-id="9c382-242">Example</span></span>

<span data-ttu-id="9c382-243">发出 HTTP PUT 请求：</span><span class="sxs-lookup"><span data-stu-id="9c382-243">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="9c382-244">*可选*：在修改之前，运行 `get` 命令以查看数据：</span><span class="sxs-lookup"><span data-stu-id="9c382-244">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="9c382-245">在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。</span><span class="sxs-lookup"><span data-stu-id="9c382-245">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="9c382-246">默认文本编辑器打开一个 .tmp  文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。</span><span class="sxs-lookup"><span data-stu-id="9c382-246">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="9c382-247">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-247">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="9c382-248">若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。</span><span class="sxs-lookup"><span data-stu-id="9c382-248">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="9c382-249">修改 JSON 模板以满足模型验证要求：</span><span class="sxs-lookup"><span data-stu-id="9c382-249">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="9c382-250">保存 .tmp  文件，并关闭文本编辑器。</span><span class="sxs-lookup"><span data-stu-id="9c382-250">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="9c382-251">以下输出显示在命令行界面中：</span><span class="sxs-lookup"><span data-stu-id="9c382-251">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="9c382-252">*可选*：发出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="9c382-252">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="9c382-253">例如，如果在文本编辑器中键入“Cherry”，`get` 会返回以下内容：</span><span class="sxs-lookup"><span data-stu-id="9c382-253">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="9c382-254">测试 HTTP DELETE 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-254">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9c382-255">摘要</span><span class="sxs-lookup"><span data-stu-id="9c382-255">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9c382-256">自变量</span><span class="sxs-lookup"><span data-stu-id="9c382-256">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9c382-257">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="9c382-257">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9c382-258">选项</span><span class="sxs-lookup"><span data-stu-id="9c382-258">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="9c382-259">示例</span><span class="sxs-lookup"><span data-stu-id="9c382-259">Example</span></span>

<span data-ttu-id="9c382-260">发出 HTTP DELETE 请求：</span><span class="sxs-lookup"><span data-stu-id="9c382-260">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="9c382-261">*可选*：在修改之前，运行 `get` 命令以查看数据：</span><span class="sxs-lookup"><span data-stu-id="9c382-261">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="9c382-262">上述命令显示以下输出格式：</span><span class="sxs-lookup"><span data-stu-id="9c382-262">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="9c382-263">*可选*：发出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="9c382-263">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="9c382-264">在此示例中，`get` 返回以下内容：</span><span class="sxs-lookup"><span data-stu-id="9c382-264">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="9c382-265">测试 HTTP PATCH 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-265">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9c382-266">摘要</span><span class="sxs-lookup"><span data-stu-id="9c382-266">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9c382-267">自变量</span><span class="sxs-lookup"><span data-stu-id="9c382-267">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9c382-268">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="9c382-268">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9c382-269">选项</span><span class="sxs-lookup"><span data-stu-id="9c382-269">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="9c382-270">测试 HTTP HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-270">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9c382-271">摘要</span><span class="sxs-lookup"><span data-stu-id="9c382-271">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9c382-272">自变量</span><span class="sxs-lookup"><span data-stu-id="9c382-272">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9c382-273">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="9c382-273">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9c382-274">选项</span><span class="sxs-lookup"><span data-stu-id="9c382-274">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="9c382-275">测试 HTTP OPTIONS 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-275">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="9c382-276">摘要</span><span class="sxs-lookup"><span data-stu-id="9c382-276">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="9c382-277">自变量</span><span class="sxs-lookup"><span data-stu-id="9c382-277">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="9c382-278">关联控制器操作方法所需的路由参数（如果有）。</span><span class="sxs-lookup"><span data-stu-id="9c382-278">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="9c382-279">选项</span><span class="sxs-lookup"><span data-stu-id="9c382-279">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="9c382-280">设置 HTTP 请求标头</span><span class="sxs-lookup"><span data-stu-id="9c382-280">Set HTTP request headers</span></span>

<span data-ttu-id="9c382-281">若要设置 HTTP 请求标头，请使用下面一种方法：</span><span class="sxs-lookup"><span data-stu-id="9c382-281">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="9c382-282">使用该 HTTP 请求进行内联设置。</span><span class="sxs-lookup"><span data-stu-id="9c382-282">Set inline with the HTTP request.</span></span> <span data-ttu-id="9c382-283">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-283">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="9c382-284">若使用上述方法，每个不同的 HTTP 请求标头都需要其自己的 `-h` 选项。</span><span class="sxs-lookup"><span data-stu-id="9c382-284">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="9c382-285">在发送 HTTP 请求之前进行设置。</span><span class="sxs-lookup"><span data-stu-id="9c382-285">Set before sending the HTTP request.</span></span> <span data-ttu-id="9c382-286">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-286">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="9c382-287">在发送请求之前设置标头时，标头在命令行界面会话期间保持设置。</span><span class="sxs-lookup"><span data-stu-id="9c382-287">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="9c382-288">若要清除标头，请提供一个空值。</span><span class="sxs-lookup"><span data-stu-id="9c382-288">To clear the header, provide an empty value.</span></span> <span data-ttu-id="9c382-289">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-289">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="9c382-290">切换 HTTP 请求显示</span><span class="sxs-lookup"><span data-stu-id="9c382-290">Toggle HTTP request display</span></span>

<span data-ttu-id="9c382-291">默认情况下，禁止显示正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="9c382-291">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="9c382-292">可在命令行界面会话期间更改相应的设置。</span><span class="sxs-lookup"><span data-stu-id="9c382-292">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="9c382-293">启用请求显示</span><span class="sxs-lookup"><span data-stu-id="9c382-293">Enable request display</span></span>

<span data-ttu-id="9c382-294">运行 `echo on` 命令可查看正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="9c382-294">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="9c382-295">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-295">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="9c382-296">当前会话中的后续 HTTP 请求显示请求标头。</span><span class="sxs-lookup"><span data-stu-id="9c382-296">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="9c382-297">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-297">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="9c382-298">禁用请求显示</span><span class="sxs-lookup"><span data-stu-id="9c382-298">Disable request display</span></span>

<span data-ttu-id="9c382-299">运行 `echo off` 命令可禁止显示正在发送的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="9c382-299">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="9c382-300">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-300">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="9c382-301">运行脚本</span><span class="sxs-lookup"><span data-stu-id="9c382-301">Run a script</span></span>

<span data-ttu-id="9c382-302">如果经常执行一组相同的 HTTP REPL 命令，请考虑将它们存储在一个文本文件中。</span><span class="sxs-lookup"><span data-stu-id="9c382-302">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="9c382-303">文件中的命令采用与在命令行上手动执行的命令相同的形式。</span><span class="sxs-lookup"><span data-stu-id="9c382-303">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="9c382-304">可使用 `run` 命令批量执行这些命令。</span><span class="sxs-lookup"><span data-stu-id="9c382-304">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="9c382-305">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-305">For example:</span></span>

1. <span data-ttu-id="9c382-306">创建一个文本文件，其中包含一组换行符分隔的命令。</span><span class="sxs-lookup"><span data-stu-id="9c382-306">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="9c382-307">例如，一个包含以下命令的 people-script.txt  文件：</span><span class="sxs-lookup"><span data-stu-id="9c382-307">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="9c382-308">执行 `run` 命令，传入文本文件的路径。</span><span class="sxs-lookup"><span data-stu-id="9c382-308">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="9c382-309">例如:</span><span class="sxs-lookup"><span data-stu-id="9c382-309">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="9c382-310">将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="9c382-310">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="9c382-311">清除输出</span><span class="sxs-lookup"><span data-stu-id="9c382-311">Clear the output</span></span>

<span data-ttu-id="9c382-312">若要删除 HTTP REPL 工具写入命令行界面的所有输出，请运行 `clear` 或 `cls` 命令。</span><span class="sxs-lookup"><span data-stu-id="9c382-312">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="9c382-313">例如，假设命令行界面包含以下输出：</span><span class="sxs-lookup"><span data-stu-id="9c382-313">To illustrate, imagine the command shell contains the following output:</span></span>

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="9c382-314">运行以下命令以清除输出：</span><span class="sxs-lookup"><span data-stu-id="9c382-314">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="9c382-315">运行上述命令后，命令行界面仅包含以下输出：</span><span class="sxs-lookup"><span data-stu-id="9c382-315">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="9c382-316">其他资源</span><span class="sxs-lookup"><span data-stu-id="9c382-316">Additional resources</span></span>

* [<span data-ttu-id="9c382-317">REST API 请求</span><span class="sxs-lookup"><span data-stu-id="9c382-317">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="9c382-318">HTTP REPL GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="9c382-318">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
