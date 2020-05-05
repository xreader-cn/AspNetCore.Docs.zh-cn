---
title: ASP.NET Core 中的临时数据保护提供程序
author: rick-anderson
description: 了解 ASP.NET Core 临时数据保护提供程序的实现细节。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: 22a332230e15256dc33fd1d06f2da3ea8d34d3bc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776885"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>ASP.NET Core 中的临时数据保护提供程序

<a name="data-protection-implementation-key-storage-ephemeral"></a>

在某些情况下，应用程序需要一次性`IDataProtectionProvider`。 例如，开发人员可能只是在一次性的控制台应用程序中试验，或者应用程序本身是暂时性的（它已编写脚本或单元测试项目）。 为支持这些方案， [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/)包包含类型`EphemeralDataProtectionProvider`。 此类型提供其密钥存储库`IDataProtectionProvider`仅保存在内存中且不会写出到任何后备存储的基本实现。

每个实例`EphemeralDataProtectionProvider`都使用其自己的唯一主密钥。 因此，如果中`IDataProtector`的根为`EphemeralDataProtectionProvider`的生成受保护的负载，则该负载只能由根在`IDataProtector`同一`EphemeralDataProtectionProvider`实例上的等效（给定相同的[用途](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes)链）进行保护。

下面的示例演示如何实例`EphemeralDataProtectionProvider`化并使用它来保护数据并对其取消保护。

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
