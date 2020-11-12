---
title: HttpRepl 遥测
author: scottaddie
description: 了解 HttpRepl 收集的遥测数据。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550603"
---
# <a name="httprepl-telemetry"></a>HttpRepl 遥测

[HttpRepl](xref:web-api/http-repl)包含收集使用情况数据的遥测功能。 重要的是，HttpRepl 团队了解如何使用该工具，以便能够改进。

## <a name="how-to-opt-out"></a>如何选择退出

默认情况下，HttpRepl 遥测功能处于启用状态。 要选择退出遥测功能，请将 `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 环境变量设置为 `1` 或 `true`。

## <a name="disclosure"></a>公开

首次运行该工具时，HttpRepl 显示类似于以下内容的文本。 文本可能会略有不同，具体取决于所运行的工具的版本。 此“首次运行”体验是 Microsoft 通知用户有关数据收集信息的方式。

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

若要禁止显示 "首次运行" 体验文本，请将 `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` 环境变量设置为 `1` 或 `true` 。

## <a name="data-points"></a>数据点

遥测功能不会：

* 收集个人数据，如用户名、电子邮件地址或 Url。
* 扫描 HTTP 请求或响应。

数据将安全地发送到 Microsoft 服务器，并以受限访问权限保存。

保护你的隐私对我们很重要。 如果你怀疑遥测功能正在收集敏感数据，或者数据被不安全地或未恰当地处理，请执行以下操作之一：

* 在 [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) 存储库中发布问题。
* 向发送电子邮件以 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) 进行调查。

遥测功能收集以下数据。

| .NET SDK 版本 | 数据 |
|--------------|------|
| >= 5。0        | 调用时间戳。 |
| >= 5。0        | 用于确定地理位置的三个八位字节的 IP 地址。 |
| >= 5。0        | 操作系统和版本。 |
| >= 5。0        | 运行此工具)  (RID 的运行时 ID。 |
| >= 5。0        | 该工具是否在容器中运行。 |
| >= 5。0        |  (MAC) 地址的哈希媒体访问控制：加密 (SHA256) 对计算机进行哈希处理和唯一 ID。 |
| >= 5。0        | 内核版本。 |
| >= 5。0        | HttpRepl 版本。 |
| >= 5。0        | 该工具是通过 `help` 、 `run` 或参数启动的 `connect` 。 不收集实际参数值。 |
| >= 5。0        | 调用的命令 (例如， `get`) 以及是否成功。 |
| >= 5。0        | 对于 `connect` 命令，是 `root` `base` 提供、还是 `openapi` 参数。 不收集实际参数值。 |
| >= 5。0        | 对于 `pref` 命令，无论是 `get` 已发出的，还是 `set` 已访问的首选项。 如果不是众所周知的首选项，则该名称将进行哈希处理。 不收集该值。 |
| >= 5。0        | 对于 `set header` 命令，为正在设置的标头名称。 如果不是众所周知的标头，则对名称进行哈希处理。 不收集该值。 |
| >= 5。0        | 对于 `connect` 命令，是否使用了的特殊用例 `dotnet new webapi` ，以及是否通过首选项跳过它。 |
| >= 5。0        | 对于所有 HTTP 命令 (例如 GET、POST、PUT) ，无论是否指定了每个选项。 不收集选项的值。 |

## <a name="additional-resources"></a>其他资源

* [.NET Core SDK 遥测](/dotnet/core/tools/telemetry)
* [.NET Core CLI 遥测数据](https://dotnet.microsoft.com/platform/telemetry)
