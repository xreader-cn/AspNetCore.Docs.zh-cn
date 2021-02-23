---
title: 配置适用于 ASP.NET Core Blazor 的裁边器
author: guardrex
description: 了解在构建 Blazor 应用时如何控制中间语言 (IL) 链接器（裁边器）。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975205"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="d11c2-103">配置适用于 ASP.NET Core Blazor 的裁边器</span><span class="sxs-lookup"><span data-stu-id="d11c2-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="d11c2-104">Blazor WebAssembly 执行[中间语言 (IL)](/dotnet/standard/managed-code#intermediate-language--execution) 剪裁以减小发布输出的大小。</span><span class="sxs-lookup"><span data-stu-id="d11c2-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="d11c2-105">默认情况下，在发布应用时进行剪裁。</span><span class="sxs-lookup"><span data-stu-id="d11c2-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="d11c2-106">剪裁可能会造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="d11c2-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="d11c2-107">在使用反射的应用中，剪裁器通常无法确定在运行时反射所需的类型。</span><span class="sxs-lookup"><span data-stu-id="d11c2-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="d11c2-108">若要剪裁使用反射的应用，必须通知剪裁器应用所依赖的应用代码和包或框架中的反射所需的类型。</span><span class="sxs-lookup"><span data-stu-id="d11c2-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="d11c2-109">剪裁器也无法在运行时对应用的动态行为作出响应。</span><span class="sxs-lookup"><span data-stu-id="d11c2-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="d11c2-110">若要确保剪裁后的应用在部署后正常工作，请在开发时经常对已发布的输出进行测试。</span><span class="sxs-lookup"><span data-stu-id="d11c2-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="d11c2-111">若要配置剪裁器，请参阅 .NET 基础知识文档中的[剪裁选项](/dotnet/core/deploying/trimming-options)一文，其中包括有关以下主题的指导：</span><span class="sxs-lookup"><span data-stu-id="d11c2-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="d11c2-112">通过项目文件中的 `<PublishTrimmed>` 属性对整个应用禁用剪裁。</span><span class="sxs-lookup"><span data-stu-id="d11c2-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="d11c2-113">控制剪裁器如何放弃未充分利用的 IL。</span><span class="sxs-lookup"><span data-stu-id="d11c2-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="d11c2-114">阻止剪裁器剪裁特定程序集。</span><span class="sxs-lookup"><span data-stu-id="d11c2-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="d11c2-115">要剪裁的“根”程序集。</span><span class="sxs-lookup"><span data-stu-id="d11c2-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="d11c2-116">通过在项目文件中将 `<SuppressTrimAnalysisWarnings>` 属性设置为 `false` 来显示关于反射的类型的警告。</span><span class="sxs-lookup"><span data-stu-id="d11c2-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="d11c2-117">控制符号剪裁和调试程序支持。</span><span class="sxs-lookup"><span data-stu-id="d11c2-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="d11c2-118">设置剪裁器功能以剪裁框架库功能。</span><span class="sxs-lookup"><span data-stu-id="d11c2-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d11c2-119">其他资源</span><span class="sxs-lookup"><span data-stu-id="d11c2-119">Additional resources</span></span>

* [<span data-ttu-id="d11c2-120">裁剪自包含部署和可执行文件</span><span class="sxs-lookup"><span data-stu-id="d11c2-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
