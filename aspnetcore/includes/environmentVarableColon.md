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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536271"
---
所有平台上的环境变量分层键都不支持 `:` 分隔符。 `__`（双下划线）：

* 受所有平台支持。 例如，[Bash](https://linuxhint.com/bash-environment-variables/) 不支持 `:` 分隔符，但支持 `__`。
* 自动替换为 `:`