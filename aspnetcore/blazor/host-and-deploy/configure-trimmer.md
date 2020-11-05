---
title: '配置适用于 ASP.NET Core :::no-loc(Blazor)::: 的裁边器'
author: guardrex
description: '了解在构建 :::no-loc(Blazor)::: 应用时如何控制中间语言 (IL) 链接器（裁边器）。'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055797"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="2cdde-103">配置适用于 ASP.NET Core :::no-loc(Blazor)::: 的裁边器</span><span class="sxs-lookup"><span data-stu-id="2cdde-103">Configure the Trimmer for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="2cdde-104">作者：[Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="2cdde-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="2cdde-105">:::no-loc(Blazor WebAssembly)::: 执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 剪裁以减小发布输出的大小。</span><span class="sxs-lookup"><span data-stu-id="2cdde-105">:::no-loc(Blazor WebAssembly)::: performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="2cdde-106">剪裁应用可以优化大小，但可能会造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="2cdde-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="2cdde-107">使用反射或相关动态功能的应用可能会在剪裁时中断，因为链接器不知道此动态行为，而且通常无法确定在运行时反射所需的类型。</span><span class="sxs-lookup"><span data-stu-id="2cdde-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="2cdde-108">若要剪裁此类应用，必须通知链接器应用所依赖的代码和包或框架中的反射所需的任何类型。</span><span class="sxs-lookup"><span data-stu-id="2cdde-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="2cdde-109">若要确保剪裁后的应用在部署后正常工作，请务必在开发时经常对已发布的输出进行测试。</span><span class="sxs-lookup"><span data-stu-id="2cdde-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="2cdde-110">可以通过在应用的项目文件中将 `PublishTrimmed` MSBuild 属性设置为 `false` 来禁用对 .NET 应用的剪裁：</span><span class="sxs-lookup"><span data-stu-id="2cdde-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="2cdde-111">配置剪裁的其他选项可以在[剪裁选项](/dotnet/core/deploying/trimming-options)中找到。</span><span class="sxs-lookup"><span data-stu-id="2cdde-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2cdde-112">其他资源</span><span class="sxs-lookup"><span data-stu-id="2cdde-112">Additional resources</span></span>

* [<span data-ttu-id="2cdde-113">裁剪自包含部署和可执行文件</span><span class="sxs-lookup"><span data-stu-id="2cdde-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
