---
title: ASP.NET Core 中的临时数据保护提供程序
author: rick-anderson
description: 了解 ASP.NET Core 暂时数据保护提供程序的实现详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654012"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="8a30e-103">ASP.NET Core 中的临时数据保护提供程序</span><span class="sxs-lookup"><span data-stu-id="8a30e-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="8a30e-104">在某些情况下，应用程序需要一次性 `IDataProtectionProvider`。</span><span class="sxs-lookup"><span data-stu-id="8a30e-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="8a30e-105">例如，开发人员可能只是在一次性的控制台应用程序中试验，或者应用程序本身是暂时性的（它已编写脚本或单元测试项目）。</span><span class="sxs-lookup"><span data-stu-id="8a30e-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="8a30e-106">为支持这些方案， [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/)包包括 `EphemeralDataProtectionProvider`类型。</span><span class="sxs-lookup"><span data-stu-id="8a30e-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="8a30e-107">此类型提供 `IDataProtectionProvider` 的基本实现，其密钥存储库仅保存在内存中，而不会写出到任何后备存储。</span><span class="sxs-lookup"><span data-stu-id="8a30e-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="8a30e-108">`EphemeralDataProtectionProvider` 的每个实例都使用其自己的唯一主密钥。</span><span class="sxs-lookup"><span data-stu-id="8a30e-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="8a30e-109">因此，如果 `IDataProtector` 的根 `EphemeralDataProtectionProvider` 生成受保护的负载，则该负载只能由根同一 `EphemeralDataProtectionProvider` 实例的等效 `IDataProtector` （给定相同的[用途](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes)链）进行保护。</span><span class="sxs-lookup"><span data-stu-id="8a30e-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="8a30e-110">下面的示例演示如何实例化 `EphemeralDataProtectionProvider` 并使用它来保护和取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="8a30e-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

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
