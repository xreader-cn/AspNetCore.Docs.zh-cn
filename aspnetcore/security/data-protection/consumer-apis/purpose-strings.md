---
title: ASP.NET Core 中的用途字符串
author: rick-anderson
description: 了解如何在 ASP.NET Core 数据保护 Api 中使用目的字符串。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060802"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="43194-103">ASP.NET Core 中的用途字符串</span><span class="sxs-lookup"><span data-stu-id="43194-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="43194-104">使用的组件 `IDataProtectionProvider` 必须将唯一的 *用途* 参数传递给 `CreateProtector` 方法。</span><span class="sxs-lookup"><span data-stu-id="43194-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="43194-105">目的 *参数* 对于数据保护系统的安全性是固有的，因为它在加密使用者之间提供隔离，即使根加密密钥相同也是如此。</span><span class="sxs-lookup"><span data-stu-id="43194-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="43194-106">当使用者指定目的时，会将目的字符串与根加密密钥一起使用，以派生该使用者独有的加密子项。</span><span class="sxs-lookup"><span data-stu-id="43194-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="43194-107">这会将使用者与应用程序中的所有其他加密使用者隔离：其他组件不能读取其有效负载，也不能读取任何其他组件的负载。</span><span class="sxs-lookup"><span data-stu-id="43194-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="43194-108">此隔离还会针对组件呈现不可行的整个攻击类别。</span><span class="sxs-lookup"><span data-stu-id="43194-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![用途关系图示例](purpose-strings/_static/purposes.png)

<span data-ttu-id="43194-110">在上图中， `IDataProtector` 实例 A 和 B **无法** 读取彼此各自的负载。</span><span class="sxs-lookup"><span data-stu-id="43194-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="43194-111">用途字符串不必是机密。</span><span class="sxs-lookup"><span data-stu-id="43194-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="43194-112">它应该是唯一的，因为任何其他正常行为的组件都不能提供相同的目的字符串。</span><span class="sxs-lookup"><span data-stu-id="43194-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="43194-113">使用数据保护 Api 的组件命名空间和类型名称是一个很好的经验法则，因为在实践中，此信息永远不会发生冲突。</span><span class="sxs-lookup"><span data-stu-id="43194-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="43194-114">负责 minting 持有者令牌的 Contoso 创作组件可能会使用 BearerToken 作为其目的字符串。</span><span class="sxs-lookup"><span data-stu-id="43194-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="43194-115">甚至更好的是，它可能使用 BearerToken 作为其目的字符串。</span><span class="sxs-lookup"><span data-stu-id="43194-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="43194-116">追加版本号后，将来的版本将使用 BearerToken 作为其用途，而不同的版本将在负载开始时彼此之间完全隔离。</span><span class="sxs-lookup"><span data-stu-id="43194-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="43194-117">由于的用途参数 `CreateProtector` 是一个字符串数组，因此可以改为将上面的指定为 `[ "Contoso.Security.BearerToken", "v1" ]` 。</span><span class="sxs-lookup"><span data-stu-id="43194-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="43194-118">这允许建立一个目的层次结构，并使用数据保护系统开启多租户方案。</span><span class="sxs-lookup"><span data-stu-id="43194-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="43194-119">组件不应允许不受信任的用户输入成为目的链的唯一输入源。</span><span class="sxs-lookup"><span data-stu-id="43194-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="43194-120">例如，假设有一个 SecureMessage，该组件负责存储安全消息。</span><span class="sxs-lookup"><span data-stu-id="43194-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="43194-121">如果要调用安全消息传送组件 `CreateProtector([ username ])` ，则恶意用户可能会在尝试获取要调用的组件时创建用户名为 "BearerToken" 的帐户 `CreateProtector([ "Contoso.Security.BearerToken" ])` ，从而无意中导致安全消息传送系统 mint 可视为身份验证令牌的负载。</span><span class="sxs-lookup"><span data-stu-id="43194-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="43194-122">消息传送组件的更好的目的链是 `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` ，它提供了适当的隔离。</span><span class="sxs-lookup"><span data-stu-id="43194-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="43194-123">由提供的隔离和 `IDataProtectionProvider` 、和目的的行为如下所示 `IDataProtector` ：</span><span class="sxs-lookup"><span data-stu-id="43194-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="43194-124">对于给定的 `IDataProtectionProvider` 对象， `CreateProtector` 方法会创建一个 `IDataProtector` 唯一绑定到 `IDataProtectionProvider` 创建它的对象和传递到该方法的用途参数的对象。</span><span class="sxs-lookup"><span data-stu-id="43194-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="43194-125">用途参数不得为 null。</span><span class="sxs-lookup"><span data-stu-id="43194-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="43194-126"> (如果将目的指定为数组，这意味着数组的长度不能为零，且数组的所有元素都必须为非 null。 ) 在技术上允许空字符串目的，但不鼓励这样做。</span><span class="sxs-lookup"><span data-stu-id="43194-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="43194-127">当且仅当它们包含相同的字符串 (按相同顺序) 使用序号比较器时，两个用途参数相等。</span><span class="sxs-lookup"><span data-stu-id="43194-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="43194-128">单个用途参数等效于相应的单元素用途数组。</span><span class="sxs-lookup"><span data-stu-id="43194-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="43194-129">`IDataProtector`如果和的对象是从 `IDataProtectionProvider` 具有等效目的参数的等效对象创建的，则这两个对象是等效的。</span><span class="sxs-lookup"><span data-stu-id="43194-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="43194-130">对于给定的 `IDataProtector` 对象， `Unprotect(protectedData)` `unprotectedData` 当且仅当 `protectedData := Protect(unprotectedData)` 对于等效对象时，对的调用将返回原始 `IDataProtector` 。</span><span class="sxs-lookup"><span data-stu-id="43194-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="43194-131">我们不考虑这样的情况：某些组件有意选择的目的字符串与另一个组件冲突。</span><span class="sxs-lookup"><span data-stu-id="43194-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="43194-132">此类组件实质上被视为恶意组件，并且此系统不会在恶意代码已在工作进程内部运行的情况下提供安全保证。</span><span class="sxs-lookup"><span data-stu-id="43194-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
