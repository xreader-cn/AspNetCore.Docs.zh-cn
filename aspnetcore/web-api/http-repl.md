---
title: 使用 HTTP REPL 测试 Web API
author: scottaddie
description: 了解如何使用 HTTP REPL .NET Core 全局工具来浏览和测试 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/12/2019
uid: web-api/http-repl
ms.openlocfilehash: 1774382305cc3d479291700390807d277a24bfa7
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308344"
---
# <a name="test-web-apis-with-the-http-repl"></a>使用 HTTP REPL 测试 Web API

作者：[Scott Addie](https://twitter.com/Scott_Addie)

HTTP 读取–求值–打印循环 (REPL)：

* 一种轻量级跨平台命令行工具，在所有支持的 .NET Core 的位置都可得到支持。
* 用于发出 HTTP 请求以测试 ASP.NET Core Web API 并查看其结果。

支持以下 [HTTP 谓词](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)：

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [HEAD](#test-http-head-requests)
* [OPTIONS](#test-http-options-requests)
* [PATCH](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

若要继续操作，请[查看或下载示例 ASP.NET Core Web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples)（[下载方式](xref:index#how-to-download-a-sample)）。

## <a name="prerequisites"></a>系统必备

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>安装

若要安装 HTTP REPL，运行以下命令：

```console
dotnet tool install -g dotnet-httprepl
    --version 2.2.0-*
    --add-source https://dotnet.myget.org/F/dotnet-core/api/v3/index.json
```

从 MyGet 上托管的 [dotnet-httprepl](https://dotnet.myget.org/feed/dotnet-core/package/nuget/dotnet-httprepl
) NuGet 包安装 [.NET Core 全局工具](/dotnet/core/tools/global-tools#install-a-global-tool)。

## <a name="usage"></a>用法

成功安装该工具后，运行以下命令以启动 HTTP REPL：

```console
dotnet httprepl
```

若要查看可用的 HTTP REPL 命令，请运行下面的一个命令：

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

显示以下输出：

```console
Usage: dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  --help - Show help information.

Once the REPL starts, these commands are valid:

HTTP Commands:
Use these commands to execute requests against your application.

GET            Issues a GET request.
POST           Issues a POST request.
PUT            Issues a PUT request.
DELETE         Issues a DELETE request.
PATCH          Issues a PATCH request.
HEAD           Issues a HEAD request.
OPTIONS        Issues an OPTIONS request.

set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`


Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
set swagger    Set the URI, relative to your base if set, of the Swagger document for this API. e.g. `set swagger /swagger/v1/swagger.json`
ls             Show all endpoints for the current path.
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`.

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell.
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands.
exit           Exit the shell.

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\Program Files\Microsoft VS Code\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line.
ui             Displays the Swagger UI page, if available, in the default browser.

Use help <COMMAND> to learn more details about individual commands. e.g. `help get`
```

HTTP REPL 提供命令完成。 按 Tab <kbd></kbd>键可循环访问补全所键入字符或 API 终结点的命令的列表。 以下部分概述了可用的 CLI 命令。

## <a name="connect-to-the-web-api"></a>连接到 Web API

运行以下命令，连接到 Web API：

```console
dotnet httprepl <BASE URI>
```

`<BASE URI>` 是 Web API 的基 URI。 例如:

```console
dotnet httprepl https://localhost:5001
```

或者，在 HTTP REPL 运行期间的任何时刻运行以下命令：

```console
set base <BASE URI>
```

例如:

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a>指向 Web API 的 Swagger 文档

若要正确检查 Web API，请将相对 URI 设置为 Web API 的 Swagger 文档。 运行下面的命令：

```console
set swagger <RELATIVE URI>
```

例如:

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>浏览 Web API

### <a name="view-available-endpoints"></a>查看可用的终结点

若要在 Web API 地址的当前路径中列出不同的终结点（控制器），请运行 `ls` 或 `dir` 命令：

```console
https://localhot:5001/~ ls
```

以下输出格式随即显示：

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

上述输出指示有两个控制器可用：`Fruits` 和 `People`。 这两个控制器都支持无参数 HTTP GET 和 POST 操作。

导航到特定控制器可显示更多详细信息。 例如，以下命令的输出显示 `Fruits` 控制器还支持 HTTP GET、PUT 和 DELETE 操作。 其中每个操作都需要路由中有一个 `id` 参数：

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

或者，运行 `ui` 命令，在浏览器中打开 Web API 的 Swagger UI 页。 例如:

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>导航到一个终结点

若要在 Web API 上导航到其他终结点，请运行 `cd` 命令：

```console
https://localhost:5001/~ cd people
```

`cd` 命令后的路径不区分大小写。 以下输出格式随即显示：

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>自定义 HTTP REPL

可自定义 HTTP REPL 的默认[颜色](#set-color-preferences)。 此外，还可定义[默认文本编辑器](#set-the-default-text-editor)。 HTTP REPL 首选项在当前会话中保持不变，并且也会在之后的会话中使用。 修改后，首选项存储在以下文件中：

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

%HOME%/.httpreplprefs 

# <a name="macostabmacos"></a>[macOS](#tab/macos)

%HOME%/.httpreplprefs 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

%USERPROFILE%\\.httpreplprefs 

---

.httpreplprefs  文件将在启动时加载，并且在运行时不监控对其的更改。 对文件的手动修改只会在重启该工具后生效。

### <a name="view-the-settings"></a>查看设置

若要查看可用的设置，请运行 `pref get` 命令。 例如:

```console
https://localhost:5001/~ pref get
```

上述命令显示可用的键值对：

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

### <a name="set-color-preferences"></a>设置颜色首选项

当前仅 JSON 支持响应着色。 若要自定义默认的 HTTP REPL 工具着色，请找到与要更改的颜色相对应的键。 有关如何查找键的说明，请参阅[查看设置](#view-the-settings)部分。 例如，将 `colors.json` 键值从 `Green` 更改为 `White`如下所示：

```console
https://localhost:5001/people~ pref set colors.json White
```

只能使用[允许的颜色](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)。 后续 HTTP 请求使用新着色显示输出。

如果未设置特定颜色键，则会考虑使用更通用的键。 若要演示此回退行为，请考虑使用以下示例：

* 如果 `colors.json.name` 没有值，则使用 `colors.json.string`。
* 如果 `colors.json.string` 没有值，则使用 `colors.json.literal`。
* 如果 `colors.json.literal` 没有值，则使用 `colors.json`。 
* 如果 `colors.json` 没有值，则使用命令行界面的默认文本颜色 (`AllowedColors.None`)。

### <a name="set-indentation-size"></a>设置缩进尺寸

当前，仅 JSON 支持响应缩进尺寸自定义。 默认尺寸为两个空格。 例如:

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

若要更改默认尺寸，请设置 `formatting.json.indentSize` 键。 例如，始终使用四个空格：

```console
pref set formatting.json.indentSize 4
```

后续响应遵循四个空格的设置：

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

### <a name="set-indentation-size"></a>设置缩进尺寸

当前，仅 JSON 支持响应缩进尺寸自定义。 默认尺寸为两个空格。 例如:

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

若要更改默认尺寸，请设置 `formatting.json.indentSize` 键。 例如，始终使用四个空格：

```console
pref set formatting.json.indentSize 4
```

后续响应遵循四个空格的设置：

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

### <a name="set-the-default-text-editor"></a>设置默认文本编辑器

默认情况下，HTTP REPL 未配置任何文本编辑器供使用。 若要测试需要 HTTP 请求正文的 Web API 方法，必须设置默认文本编辑器。 HTTP REPL 工具将启动已配置的文本编辑器，专门用于编写请求正文。 运行以下命令，将你青睐的文本编辑器设置为默认编辑器：

```console
pref set editor.command.default "<EXECUTABLE>"
```

在上述命令中，`<EXECUTABLE>` 是文本编辑器可执行文件的完整路径。 例如，运行以下命令，将 Visual Studio Code 设置为默认文本编辑器：

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

若要使用特定 CLI 参数启动默认文本编辑器，请设置 `editor.command.default.arguments` 键。 例如，假设 Visual Studio Code 为默认文本编辑器，并且你总是希望 HTTP REPL 在禁用了扩展的新会话中打开 Visual Studio Code。 运行下面的命令：

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a>测试 HTTP GET 请求

### <a name="synopsis"></a>摘要

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>自变量

`PARAMETER`

关联控制器操作方法所需的路由参数（如果有）。

### <a name="options"></a>选项

`get` 命令可以使用以下选项：

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>示例

发出 HTTP GET 请求：

1. 在支持 `get` 命令的终结点上运行该命令：

    ```console
    https://localhost:5001/people~ get
    ```

    上述命令显示以下输出格式：

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

1. 通过将参数传递给 `get` 命令来检索特定记录：

    ```console
    https://localhost:5001/people~ get 2
    ```

    上述命令显示以下输出格式：

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

## <a name="test-http-post-requests"></a>测试 HTTP POST 请求

### <a name="synopsis"></a>摘要

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>自变量

`PARAMETER`

关联控制器操作方法所需的路由参数（如果有）。

### <a name="options"></a>选项

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>示例

发出 HTTP POST 请求：

1. 在支持 `post` 命令的终结点上运行该命令：

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。 默认文本编辑器打开一个 .tmp  文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。 例如:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。

1. 修改 JSON 模板以满足模型验证要求：

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. 保存 .tmp  文件，并关闭文本编辑器。 以下输出显示在命令行界面中：

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

## <a name="test-http-put-requests"></a>测试 HTTP PUT 请求

### <a name="synopsis"></a>摘要

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>自变量

`PARAMETER`

关联控制器操作方法所需的路由参数（如果有）。

### <a name="options"></a>选项

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>示例

发出 HTTP PUT 请求：

1. *可选*：在修改之前，运行 `get` 命令以查看数据：

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

    在上述命令中，`Content-Type` HTTP 请求标头被设置为指示 JSON 的请求正文媒体类型。 默认文本编辑器打开一个 .tmp  文件，其中包含一个表示 HTTP 请求正文的 JSON 模板。 例如:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 若要设置默认文本编辑器，请参阅[设置默认文本编辑器](#set-the-default-text-editor)部分。

1. 修改 JSON 模板以满足模型验证要求：

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. 保存 .tmp  文件，并关闭文本编辑器。 以下输出显示在命令行界面中：

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *可选*：发出 `get` 命令以查看修改。 例如，如果在文本编辑器中键入“Cherry”，`get` 会返回以下内容：

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

## <a name="test-http-delete-requests"></a>测试 HTTP DELETE 请求

### <a name="synopsis"></a>摘要

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>自变量

`PARAMETER`

关联控制器操作方法所需的路由参数（如果有）。

### <a name="options"></a>选项

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>示例

发出 HTTP DELETE 请求：

1. *可选*：在修改之前，运行 `get` 命令以查看数据：

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

    上述命令显示以下输出格式：

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *可选*：发出 `get` 命令以查看修改。 在此示例中，`get` 返回以下内容：

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

## <a name="test-http-patch-requests"></a>测试 HTTP PATCH 请求

### <a name="synopsis"></a>摘要

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>自变量

`PARAMETER`

关联控制器操作方法所需的路由参数（如果有）。

### <a name="options"></a>选项

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>测试 HTTP HEAD 请求

### <a name="synopsis"></a>摘要

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>自变量

`PARAMETER`

关联控制器操作方法所需的路由参数（如果有）。

### <a name="options"></a>选项

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>测试 HTTP OPTIONS 请求

### <a name="synopsis"></a>摘要

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>自变量

`PARAMETER`

关联控制器操作方法所需的路由参数（如果有）。

### <a name="options"></a>选项

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>设置 HTTP 请求标头

若要设置 HTTP 请求标头，请使用下面一种方法：

1. 使用该 HTTP 请求进行内联设置。 例如:

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  若使用上述方法，每个不同的 HTTP 请求标头都需要其自己的 `-h` 选项。

1. 在发送 HTTP 请求之前进行设置。 例如:

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  在发送请求之前设置标头时，标头在命令行界面会话期间保持设置。 若要清除标头，请提供一个空值。 例如:

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a>切换 HTTP 请求显示

默认情况下，禁止显示正在发送的 HTTP 请求。 可在命令行界面会话期间更改相应的设置。

### <a name="enable-request-display"></a>启用请求显示

运行 `echo on` 命令可查看正在发送的 HTTP 请求。 例如:

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

当前会话中的后续 HTTP 请求显示请求标头。 例如:

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

### <a name="disable-request-display"></a>禁用请求显示

运行 `echo off` 命令可禁止显示正在发送的 HTTP 请求。 例如:

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>运行脚本

如果经常执行一组相同的 HTTP REPL 命令，请考虑将它们存储在一个文本文件中。 文件中的命令采用与在命令行上手动执行的命令相同的形式。 可使用 `run` 命令批量执行这些命令。 例如:

1. 创建一个文本文件，其中包含一组换行符分隔的命令。 例如，一个包含以下命令的 people-script.txt  文件：

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. 执行 `run` 命令，传入文本文件的路径。 例如:

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    将显示以下输出：

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

## <a name="clear-the-output"></a>清除输出

若要删除 HTTP REPL 工具写入命令行界面的所有输出，请运行 `clear` 或 `cls` 命令。 例如，假设命令行界面包含以下输出：

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

运行以下命令以清除输出：

```console
https://localhost:5001/~ clear
```

运行上述命令后，命令行界面仅包含以下输出：

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>其他资源

* [REST API 请求](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [HTTP REPL GitHub 存储库](https://github.com/aspnet/HttpRepl)
