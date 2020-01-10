---
title: 在 ASP.NET Core中的哈希密码
author: rick-anderson
description: 了解如何使用 ASP.NET Core数据保护 Api 的密码执行哈希。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828563"
---
# <a name="hash-passwords-in-aspnet-core"></a>在 ASP.NET Core中的哈希密码

数据保护基本代码包括一个包含加密密钥派生函数的*AspNetCore 密钥派生*。 此包是一个独立的组件，与数据保护系统的其余部分没有任何依赖关系。 它可以完全独立地使用。 为了方便起见，源与数据保护代码库并存。

包当前提供了一个允许使用[PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)对密码进行哈希处理 `KeyDerivation.Pbkdf2` 方法。 此 API 与 .NET Framework 现有的[Rfc2898DeriveBytes 类型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)非常相似，但有三个重要的区别：

1. `KeyDerivation.Pbkdf2` 方法支持使用多个 PRFs （当前 `HMACSHA1`、`HMACSHA256`和 `HMACSHA512`），而 `Rfc2898DeriveBytes` 类型仅支持 `HMACSHA1`。

2. `KeyDerivation.Pbkdf2` 方法将检测当前操作系统，并尝试选择最适合的例程实现，在某些情况下提供更好的性能。 （在 Windows 8 中，它提供 `Rfc2898DeriveBytes`吞吐量的10倍。）

3. `KeyDerivation.Pbkdf2` 方法要求调用方指定所有参数（salt、PRF 和迭代计数）。 `Rfc2898DeriveBytes` 类型为这些值提供默认值。

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

有关实际用例, 请参阅 ASP.NET Core `PasswordHasher`标识的[源代码](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs)。
