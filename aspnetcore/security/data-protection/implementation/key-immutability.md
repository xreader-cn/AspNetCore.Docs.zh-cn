---
title: 密钥不可变性和 ASP.NET Core 中的密钥设置
author: rick-anderson
description: 了解可变性 ASP.NET Core 数据保护 Api 的实现详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 7796cb102c0f6f03809704016fd36b28c7a82438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892284"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="f2e69-103">密钥不可变性和 ASP.NET Core 中的密钥设置</span><span class="sxs-lookup"><span data-stu-id="f2e69-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="f2e69-104">后一个对象被保存到后备存储，它的表示形式是永久固定的。</span><span class="sxs-lookup"><span data-stu-id="f2e69-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="f2e69-105">新的数据可以添加到后备存储，但可以永远不会改变现有数据。</span><span class="sxs-lookup"><span data-stu-id="f2e69-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="f2e69-106">此行为的主要目的是为了防止数据损坏。</span><span class="sxs-lookup"><span data-stu-id="f2e69-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="f2e69-107">此行为的一个后果是，一旦密钥将写入后备存储，它是不可变。</span><span class="sxs-lookup"><span data-stu-id="f2e69-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="f2e69-108">其创建、 激活和到期日期可以永远不会更改，但它可以通过使用吊销`IKeyManager`。</span><span class="sxs-lookup"><span data-stu-id="f2e69-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="f2e69-109">此外，其基础的算法信息、 主密钥密钥材料和 rest 属性静态加密也是不可变。</span><span class="sxs-lookup"><span data-stu-id="f2e69-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="f2e69-110">如果开发人员更改会影响密钥持久性的任何设置，这些更改不会起开始下一次生成一个密钥，通过显式调用之前`IKeyManager.CreateNewKey`或通过数据保护系统的自己[自动密钥生成](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)行为。</span><span class="sxs-lookup"><span data-stu-id="f2e69-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="f2e69-111">影响密钥持久性的设置如下所示：</span><span class="sxs-lookup"><span data-stu-id="f2e69-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="f2e69-112">默认密钥生存期</span><span class="sxs-lookup"><span data-stu-id="f2e69-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="f2e69-113">在 rest 机制密钥加密</span><span class="sxs-lookup"><span data-stu-id="f2e69-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="f2e69-114">算法的信息包含在项</span><span class="sxs-lookup"><span data-stu-id="f2e69-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="f2e69-115">如果需要这些设置，以开始执行下一步的自动密钥滚动时间之前，请考虑进行显式调用`IKeyManager.CreateNewKey`强制创建新密钥。</span><span class="sxs-lookup"><span data-stu-id="f2e69-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="f2e69-116">请记住，若要提供显式激活日期 （{现在 + 2 天} 是一个很好的经验以留出时间进行传播的更改） 的调用中的有效期范围之内。</span><span class="sxs-lookup"><span data-stu-id="f2e69-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="f2e69-117">触摸存储库中的所有应用程序应指定相同的设置`IDataProtectionBuilder`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="f2e69-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="f2e69-118">否则，持久化密钥的属性将取决于特定调用密钥生成例程的应用程序。</span><span class="sxs-lookup"><span data-stu-id="f2e69-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
