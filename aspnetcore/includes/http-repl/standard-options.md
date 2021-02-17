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

  禁用 HTTP 响应格式的标志。

* `-h|--header`

  设置 HTTP 请求标头。 支持以下两种值格式：

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  指定一个文件，HTTP 响应正文应写入该文件。 例如，`--response:body "C:\response.json"`。 如果文件不存在，则创建该文件。

* `--response:headers`

  指定一个文件，HTTP 响应标头应写入该文件。 例如，`--response:headers "C:\response.txt"`。 如果文件不存在，则创建该文件。

* `-s|--streaming`

  支持 HTTP 响应流式处理的标志。
