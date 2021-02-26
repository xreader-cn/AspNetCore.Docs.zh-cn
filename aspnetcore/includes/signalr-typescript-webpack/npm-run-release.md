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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552342"
---
```console
npm run release
```

此命令在运行应用时生成要提供的客户端资产。 资产位于 wwwroot 文件夹  。

Webpack 完成了以下任务：

* 清除了 wwwroot 目录的内容  。
* 将 TypeScript 转换为 JavaScript，该过程称为“转译”  。
* 破坏生成的 JavaScript 以降低文件大小，该过程称为“缩小”  。
* 将已处理的 JavaScript、CSS 和 HTML 文件从 src 复制到 wwwroot 目录   。
* 将以下元素注入 wwwroot/index.html 文件  ：
  * 一个引用 wwwroot/main.\<hash\>.css 文件的 `<link>` 标记。 此标记紧挨着 `</head>` 结束标记之前。
  * 一个引用缩小后的 wwwroot/main.\<hash\>.js 文件的 `<script>` 标记。 此标记紧挨着 `</body>` 结束标记之前。