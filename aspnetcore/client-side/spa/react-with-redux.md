---
title: 通过 ASP.NET Core 使用带 Redux 的 React 项目模板
author: SteveSandersonMS
description: 了解如何开始使用适用于带 Redux 的 React 和 create-react-app 的 ASP.NET Core 单页应用程序 (SPA) 项目模板。
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react-with-redux
ms.openlocfilehash: c1aedcf1e14a9e7b339b60dd02c4267cd5945a49
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341603"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a>通过 ASP.NET Core 使用带 Redux 的 React 项目模板

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> 本文档不适用于 ASP.NET Core 2.0 中包含的 React 与 Redux 项目模板。 它与较新的 React 与 Redux 模板，则可以手动更新。 该模板是适用于 ASP.NET Core 2.1 或更高版本。

::: moniker-end

更新的带 Redux 的 React 项目模板为使用 React、Redux 和 [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) 约定实现丰富的客户端用户界面 (UI) 的 ASP.NET Core 应用程序提供了便捷起点。

除了项目创建命令外，关于带 Redux 的 React 模板的所有信息都与 React 模板相同。 要创建此项目类型，请运行 `dotnet new reactredux` 而不是 `dotnet new react`。 有关这两个基于 React 的模板的通用功能的详细信息，请参阅 [React 模板文档](xref:spa/react)。

有关在 IIS 中配置 React 与 Redux 子应用程序的信息，请参阅[ReactRedux 模板 2.1:无法在 IIS 上使用 SPA (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555)。
