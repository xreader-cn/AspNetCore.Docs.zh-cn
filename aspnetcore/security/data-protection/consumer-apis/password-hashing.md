---
title: 在 ASP.NET Core中的哈希密码
author: rick-anderson
description: 了解如何使用 ASP.NET Core数据保护 Api 的密码执行哈希。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: bd4b8fcf6a5a16a86ada97bbd3519f872d1417b7
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602450"
---
# <a name="hash-passwords-in-aspnet-core"></a>在 ASP.NET Core中的哈希密码

数据保护基本代码包括一个包含加密密钥派生函数的*AspNetCore 密钥派生*。 此包是一个独立的组件, 与数据保护系统的其余部分没有任何依赖关系。 它可以完全独立地使用。 为了方便起见, 源与数据保护代码库并存。

包当前提供了一个方法`KeyDerivation.Pbkdf2` , 该方法允许使用[PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)对密码进行哈希处理。 此 API 与 .NET Framework 现有的[Rfc2898DeriveBytes 类型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)非常相似, 但有三个重要的区别:

1. `HMACSHA512` `HMACSHA256` `HMACSHA1` `Rfc2898DeriveBytes`方法支持使用多个 PRFs (目前为、和), 而类型仅支持`HMACSHA1`。 `KeyDerivation.Pbkdf2`

2. 方法`KeyDerivation.Pbkdf2`将检测当前操作系统, 并尝试选择最适合的例程实现, 在某些情况下提供更好的性能。 (在 Windows 8 上, 它提供的吞吐量`Rfc2898DeriveBytes`大约为10倍。)

3. `KeyDerivation.Pbkdf2`方法要求调用方指定所有参数 (salt、PRF 和迭代计数)。 `Rfc2898DeriveBytes`类型为这些提供默认值。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

有关实际用例, 请参阅 ASP.NET Core `PasswordHasher`标识的[源代码](https://github.com/aspnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs)。
