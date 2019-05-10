---
title: 在 ASP.NET Core中的哈希密码
author: rick-anderson
description: 了解如何使用 ASP.NET Core数据保护 Api 的密码执行哈希。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 70301ffffbaaf3c5ff0642b19b80e40be83aa438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896644"
---
# <a name="hash-passwords-in-aspnet-core"></a>在 ASP.NET Core中的哈希密码

数据保护代码库包括包*Microsoft.AspNetCore.Cryptography.KeyDerivation*其中包含加密密钥派生函数。 此包是一个独立组件，并不依赖于数据保护系统的其余部分。 可以完全独立地使用它。 源并存的数据保护代码库为方便起见。

包目前提供一种方法`KeyDerivation.Pbkdf2`允许使用密码哈希[PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)。 此 API 是非常类似于.NET Framework 的现有[Rfc2898DeriveBytes 类型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)，但具有三个重要的区别：

1. `KeyDerivation.Pbkdf2`方法支持使用多个 PRFs (目前`HMACSHA1`， `HMACSHA256`，和`HMACSHA512`)，而`Rfc2898DeriveBytes`类型仅支持`HMACSHA1`。

2. `KeyDerivation.Pbkdf2`方法检测到当前操作系统，并尝试选择提供更好的性能，在某些情况下的例程的经过最完美优化的实现。 (在 Windows 8 中，它提供了大约 10 倍的吞吐量`Rfc2898DeriveBytes`。)

3. `KeyDerivation.Pbkdf2`方法需要调用方指定所有参数 （给加盐，PRF 和迭代次数）。 `Rfc2898DeriveBytes`类型提供的这些默认值。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

请参阅[源代码](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs)有关 ASP.NET Core 标识`PasswordHasher`类型实际用例。
