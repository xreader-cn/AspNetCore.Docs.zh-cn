---
title: 通过 ASP.NET Core 使用带 Redux 的 React 项目模板
author: SteveSandersonMS
description: 了解如何开始使用适用于带 Redux 的 React 和 create-react-app 的 ASP.NET Core 单页应用程序 (SPA) 项目模板。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
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
uid: spa/react-with-redux
ms.openlocfilehash: 9ae96b14f3f50d4079933bbd893ff95fa677d273
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054497"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="97060-103">通过 ASP.NET Core 使用带 Redux 的 React 项目模板</span><span class="sxs-lookup"><span data-stu-id="97060-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="97060-104">更新的带 Redux 的 React 项目模板为使用 React、Redux 和 [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) 约定实现丰富的客户端用户界面 (UI) 的 ASP.NET Core 应用程序提供了便捷起点。</span><span class="sxs-lookup"><span data-stu-id="97060-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="97060-105">除了项目创建命令外，关于带 Redux 的 React 模板的所有信息都与 React 模板相同。</span><span class="sxs-lookup"><span data-stu-id="97060-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="97060-106">要创建此项目类型，请运行 `dotnet new reactredux` 而不是 `dotnet new react`。</span><span class="sxs-lookup"><span data-stu-id="97060-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="97060-107">有关这两个基于 React 的模板的通用功能的详细信息，请参阅 [React 模板文档](xref:spa/react)。</span><span class="sxs-lookup"><span data-stu-id="97060-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="97060-108">有关在 IIS 中配置带 Redux 的 React 子应用程序的信息，请参阅 [ReactRedux Template 2.1:Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555)（ReactRedux 模板 2.1：无法在 IIS 上使用 SPA (aspnet/Templating #555)）。</span><span class="sxs-lookup"><span data-stu-id="97060-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
