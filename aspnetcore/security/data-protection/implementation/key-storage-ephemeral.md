---
title: ASP.NET Core 中的临时数据保护提供程序
author: rick-anderson
description: 了解 ASP.NET Core 暂时数据保护提供程序的实现详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895174"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="e7da7-103">ASP.NET Core 中的临时数据保护提供程序</span><span class="sxs-lookup"><span data-stu-id="e7da7-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="e7da7-104">情况下，应用程序需要 throwaway `IDataProtectionProvider`。</span><span class="sxs-lookup"><span data-stu-id="e7da7-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="e7da7-105">例如，开发人员可能只试验在一次性控制台应用程序，或应用程序本身是暂时性 （编写的脚本或单元测试项目）。</span><span class="sxs-lookup"><span data-stu-id="e7da7-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="e7da7-106">若要支持这些方案[Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/)包包括一种类型`EphemeralDataProtectionProvider`。</span><span class="sxs-lookup"><span data-stu-id="e7da7-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="e7da7-107">此类型提供的基本实现`IDataProtectionProvider`其密钥的存储库保留仅在内存中并不写出到任何后备存储。</span><span class="sxs-lookup"><span data-stu-id="e7da7-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="e7da7-108">每个实例`EphemeralDataProtectionProvider`使用其自己唯一的主密钥。</span><span class="sxs-lookup"><span data-stu-id="e7da7-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="e7da7-109">因此，如果`IDataProtector`根节点的`EphemeralDataProtectionProvider`生成的受保护的有效负载，该负载仅受等效`IDataProtector`(给定相同[目的](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes)链) 根节点的相同`EphemeralDataProtectionProvider`实例。</span><span class="sxs-lookup"><span data-stu-id="e7da7-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="e7da7-110">下面的示例演示如何实例化`EphemeralDataProtectionProvider`并使用它来保护和取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="e7da7-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
