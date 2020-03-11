---
title: 密钥不可变性和 ASP.NET Core 中的密钥设置
author: rick-anderson
description: 了解可变性 ASP.NET Core 数据保护 Api 的实现详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 7796cb102c0f6f03809704016fd36b28c7a82438
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653994"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>密钥不可变性和 ASP.NET Core 中的密钥设置

一旦将对象保存到后备存储，它的表示形式就会永远固定。 可以将新数据添加到后备存储，但不能转变现有数据。 此行为的主要目的是防止数据损坏。

此行为的一个结果是，将密钥写入后备存储后，它是不可变的。 它的创建、激活和到期日期永远无法更改，但可以通过使用 `IKeyManager`进行吊销。 此外，其基础算法信息、主密钥材料和静态加密属性也是不可变的。

如果开发人员更改了任何影响密钥持久性的设置，则在下一次生成密钥之前，这些更改不会生效，无论是通过显式调用 `IKeyManager.CreateNewKey` 还是通过数据保护系统自己的[自动密钥生成](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)行为来实现的。 影响密钥持久性的设置如下所示：

* [默认密钥生存期](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [静态密钥加密机制](xref:security/data-protection/implementation/key-encryption-at-rest)

* [密钥内包含的算法信息](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

如果需要在下一次自动密钥滚动时间之前启动这些设置，请考虑对 `IKeyManager.CreateNewKey` 进行显式调用，以强制创建新密钥。 请记住，提供明确的激活日期（{now + 2 days} 是合理的经验法则，以允许更改传播的时间）以及调用中的到期日期。

>[!TIP]
> 接触存储库的所有应用程序都应与 `IDataProtectionBuilder` 扩展方法指定相同的设置。 否则，持久化密钥的属性将依赖于调用密钥生成例程的特定应用程序。
