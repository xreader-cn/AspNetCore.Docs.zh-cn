---
title: 在 ASP.NET Core 目的字符串
author: rick-anderson
description: 了解如何在 ASP.NET Core 数据保护 Api 中使用目的字符串。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654000"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="3cbf7-103">在 ASP.NET Core 目的字符串</span><span class="sxs-lookup"><span data-stu-id="3cbf7-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="3cbf7-104">使用 `IDataProtectionProvider` 的组件必须向 `CreateProtector` 方法传递唯一的*用途*参数。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="3cbf7-105">目的*参数*对于数据保护系统的安全性是固有的，因为它在加密使用者之间提供隔离，即使根加密密钥相同也是如此。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="3cbf7-106">当使用者指定目的时，会将目的字符串与根加密密钥一起使用，以派生该使用者独有的加密子项。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="3cbf7-107">这会将使用者与应用程序中的所有其他加密使用者隔离：其他组件不能读取其有效负载，也不能读取任何其他组件的负载。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="3cbf7-108">此隔离还会针对组件呈现不可行的整个攻击类别。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![用途关系图示例](purpose-strings/_static/purposes.png)

<span data-ttu-id="3cbf7-110">在上面的关系图中，`IDataProtector` 实例 A 和 B**无法**读取彼此各自的负载。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="3cbf7-111">用途字符串不必是机密。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="3cbf7-112">它应该是唯一的，因为任何其他正常行为的组件都不能提供相同的目的字符串。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="3cbf7-113">使用数据保护 Api 的组件命名空间和类型名称是一个很好的经验法则，因为在实践中，此信息永远不会发生冲突。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="3cbf7-114">负责 minting 持有者令牌的 Contoso 创作组件可能会使用 BearerToken 作为其目的字符串。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="3cbf7-115">甚至更好的是，它可能使用 BearerToken 作为其目的字符串。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="3cbf7-116">追加版本号后，将来的版本将使用 BearerToken 作为其用途，而不同的版本将在负载开始时彼此之间完全隔离。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="3cbf7-117">由于 `CreateProtector` 的目的参数是一个字符串数组，因此可以改为将上面的指定为 `[ "Contoso.Security.BearerToken", "v1" ]`。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="3cbf7-118">这允许建立一个目的层次结构，并使用数据保护系统开启多租户方案。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="3cbf7-119">组件不应允许不受信任的用户输入成为目的链的唯一输入源。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="3cbf7-120">例如，假设有一个 SecureMessage，该组件负责存储安全消息。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="3cbf7-121">如果安全消息传送组件调用 `CreateProtector([ username ])`，则恶意用户可能会创建用户名为 "BearerToken" 的帐户，以尝试获取该组件以便调用 `CreateProtector([ "Contoso.Security.BearerToken" ])`，从而导致安全消息传送系统 mint 负载，这可能被视为身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="3cbf7-122">消息传送组件的更好的目的链将 `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`，这提供了适当的隔离。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="3cbf7-123">提供的隔离和 `IDataProtectionProvider`、`IDataProtector`和用途的行为如下所示：</span><span class="sxs-lookup"><span data-stu-id="3cbf7-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="3cbf7-124">对于给定的 `IDataProtectionProvider` 对象，`CreateProtector` 方法将创建一个唯一绑定到创建它的 `IDataProtectionProvider` 对象和传递到该方法的用途参数的 `IDataProtector` 对象。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="3cbf7-125">用途参数不得为 null。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="3cbf7-126">（如果将目的指定为数组，这意味着数组的长度不能为零，且数组的所有元素都必须为非 null。）在技术上允许空字符串目的，但不建议使用。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="3cbf7-127">当且仅当它们包含相同的字符串（使用序号比较器）时，两个用途参数等效。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="3cbf7-128">单个用途参数等效于相应的单元素用途数组。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="3cbf7-129">当且仅当两个 `IDataProtector` 对象是从具有等效目的参数的等效 `IDataProtectionProvider` 对象创建的时，它们是等效的。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="3cbf7-130">对于给定的 `IDataProtector` 对象，当且仅当等效 `IDataProtector` 对象 `protectedData := Protect(unprotectedData)` 时，对 `Unprotect(protectedData)` 的调用将返回原始的 `unprotectedData`。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="3cbf7-131">我们不考虑这样的情况：某些组件有意选择的目的字符串与另一个组件冲突。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="3cbf7-132">此类组件实质上被视为恶意组件，并且此系统不会在恶意代码已在工作进程内部运行的情况下提供安全保证。</span><span class="sxs-lookup"><span data-stu-id="3cbf7-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
