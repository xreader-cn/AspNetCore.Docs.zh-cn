---
title: 开始使用 ASP.NET Core 中的数据保护 Api
author: rick-anderson
description: 了解如何使用 ASP.NET Core 数据保护 Api 用于保护和取消保护的应用程序中的数据。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 25bf099a3d9edd7e6e0872725cbc3707750314e6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087641"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="a5efc-103">开始使用 ASP.NET Core 中的数据保护 Api</span><span class="sxs-lookup"><span data-stu-id="a5efc-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="a5efc-104">在其最简单、 保护数据包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="a5efc-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="a5efc-105">从数据保护提供程序创建数据保护程序。</span><span class="sxs-lookup"><span data-stu-id="a5efc-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="a5efc-106">调用`Protect`你想要保护的数据的方法。</span><span class="sxs-lookup"><span data-stu-id="a5efc-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="a5efc-107">调用`Unprotect`想要将返回转换为纯文本格式的数据的方法。</span><span class="sxs-lookup"><span data-stu-id="a5efc-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="a5efc-108">大多数框架和应用模型，如 ASP.NET Core 或 SignalR 中，已配置数据保护系统，并将其添加到服务容器还通过依赖关系注入来访问。</span><span class="sxs-lookup"><span data-stu-id="a5efc-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="a5efc-109">下面的示例演示如何配置依赖关系注入的服务容器和注册数据保护堆栈、 接收通过 DI 的数据保护提供程序、 创建保护程序和数据保护，然后取消。</span><span class="sxs-lookup"><span data-stu-id="a5efc-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="a5efc-110">创建一个保护程序时必须提供一个或多个[目标字符串](xref:security/data-protection/consumer-apis/purpose-strings)。</span><span class="sxs-lookup"><span data-stu-id="a5efc-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="a5efc-111">一个字符串，目的提供了使用者之间的隔离。</span><span class="sxs-lookup"><span data-stu-id="a5efc-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="a5efc-112">例如，使用"green"的目的字符串创建的保护程序将无法取消保护数据的"紫色"目的提供的保护程序。</span><span class="sxs-lookup"><span data-stu-id="a5efc-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="a5efc-113">实例`IDataProtectionProvider`和`IDataProtector`是线程安全的多个调用方。</span><span class="sxs-lookup"><span data-stu-id="a5efc-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="a5efc-114">它可用于的一个组件，获取对的引用后`IDataProtector`通过调用`CreateProtector`，它将该引用用于对多个调用`Protect`和`Unprotect`。</span><span class="sxs-lookup"><span data-stu-id="a5efc-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="a5efc-115">调用`Unprotect`将引发 CryptographicException，如果受保护的有效负载不能验证或中译解出来。</span><span class="sxs-lookup"><span data-stu-id="a5efc-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="a5efc-116">某些组件可能想要忽略错误期间取消保护操作;一个组件，它读取身份验证 cookie 可能处理此错误，并将请求视为如同它在所有具有任何 cookie 而无法完全是请求。</span><span class="sxs-lookup"><span data-stu-id="a5efc-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="a5efc-117">需要此行为的组件应专门捕获 CryptographicException，而不是抑制所有异常。</span><span class="sxs-lookup"><span data-stu-id="a5efc-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
