---
title: 通过 LibMan 在 ASP.NET Core 中获取客户端库
author: scottaddie
description: 了解如何使用库管理器 (LibMan) 在 ASP.NET Core 项目中安装客户端库资产。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644574"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>通过 LibMan 在 ASP.NET Core 中获取客户端库

作者：[Scott Addie](https://twitter.com/Scott_Addie)

库管理器 (LibMan) 是一个轻量型客户端库获取工具。 LibMan 可从文件系统或从[内容分发网络 (CDN)](https://wikipedia.org/wiki/Content_delivery_network) 下载库和框架。 支持的 CDN 包括 [CDNJS](https://cdnjs.com/)、[jsDelivr](https://www.jsdelivr.com/) 和 [unpkg](https://unpkg.com/#/)。 将提取所选库文件，并将其置于 ASP.NET Core 项目中的相应位置。

## <a name="libman-use-cases"></a>LibMan 用例

LibMan 提供以下优势：

* 只会下载所需的库文件。
* 无需使用其他工具（例如 [Node.js](https://nodejs.org)、[npm](https://www.npmjs.com) 和 [ WebPack](https://webpack.js.org)），即可获取库中文件的子集。
* 可将文件放置在特定位置，无需执行生成任务，也不需手动进行文件复制。

有关 LibMan 优势的详细信息，请观看 [Visual Studio 2017 中的新式前端 Web 开发：LibMan 段](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s)。

LibMan 不是程序包管理系统。 如果已在使用包管理器（例如 npm 或[yarn](https://yarnpkg.com)），请继续使用它们。 LibMan 不是为取代这些工具而开发的。

## <a name="additional-resources"></a>其他资源

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [LibMan GitHub 存储库](https://github.com/aspnet/LibraryManager)
