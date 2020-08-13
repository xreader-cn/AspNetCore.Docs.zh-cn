---
title: 版本控制 gRPC 服务
author: jamesnk
description: 了解如何对 gRPC 服务进行版本控制。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/versioning
ms.openlocfilehash: 087ce449b0d8ab1eaf3434ad64feeff78c1fee22
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015954"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="61a5e-103">版本控制 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="61a5e-103">Versioning gRPC services</span></span>

<span data-ttu-id="61a5e-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="61a5e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="61a5e-105">添加到应用的新功能可能要求提供给客户端的 gRPC 服务进行更改（有时要求该服务以意想不到、打破常规的方式进行更改）。</span><span class="sxs-lookup"><span data-stu-id="61a5e-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="61a5e-106">gRPC 服务更改时：</span><span class="sxs-lookup"><span data-stu-id="61a5e-106">When gRPC services change:</span></span>

* <span data-ttu-id="61a5e-107">应考虑更改会如何影响客户端。</span><span class="sxs-lookup"><span data-stu-id="61a5e-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="61a5e-108">应实现支持更改的版本控制策略。</span><span class="sxs-lookup"><span data-stu-id="61a5e-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="61a5e-109">向后兼容性</span><span class="sxs-lookup"><span data-stu-id="61a5e-109">Backwards compatibility</span></span>

<span data-ttu-id="61a5e-110">gRPC 协议旨在支持随时间变化的服务。</span><span class="sxs-lookup"><span data-stu-id="61a5e-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="61a5e-111">通常，gRPC 服务和方法会不中断地新增内容。</span><span class="sxs-lookup"><span data-stu-id="61a5e-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="61a5e-112">非中断性变更允许现有客户端继续工作而不做任何变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="61a5e-113">更改或删除 gRPC 服务是中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="61a5e-114">gRPC 服务发生中断性变更时，必须更新和重新部署使用该服务的客户端。</span><span class="sxs-lookup"><span data-stu-id="61a5e-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="61a5e-115">对服务进行非中断性变更有许多好处：</span><span class="sxs-lookup"><span data-stu-id="61a5e-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="61a5e-116">现有客户端可继续运行。</span><span class="sxs-lookup"><span data-stu-id="61a5e-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="61a5e-117">避免向客户端通知中断性变更并进行更新。</span><span class="sxs-lookup"><span data-stu-id="61a5e-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="61a5e-118">只需要记录和维护服务的一个版本。</span><span class="sxs-lookup"><span data-stu-id="61a5e-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="61a5e-119">非重大变化</span><span class="sxs-lookup"><span data-stu-id="61a5e-119">Non-breaking changes</span></span>

<span data-ttu-id="61a5e-120">在 gRPC 协议级别和 .NET 二进制级别，这些变更不会中断。</span><span class="sxs-lookup"><span data-stu-id="61a5e-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="61a5e-121">添加新服务</span><span class="sxs-lookup"><span data-stu-id="61a5e-121">**Adding a new service**</span></span>
* <span data-ttu-id="61a5e-122">向服务中添加新方法</span><span class="sxs-lookup"><span data-stu-id="61a5e-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="61a5e-123">将字段添加到请求消息 - 添加到请求消息的字段将在服务器上通过[默认值](https://developers.google.com/protocol-buffers/docs/proto3#default)（若未设置）进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="61a5e-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="61a5e-124">若要实现非中断性变更，当新字段不是由旧客户端设置时，服务必须成功。</span><span class="sxs-lookup"><span data-stu-id="61a5e-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="61a5e-125">将字段添加到响应消息 - 添加到响应消息的字段将反序列化到客户端上消息的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)集合中。</span><span class="sxs-lookup"><span data-stu-id="61a5e-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="61a5e-126">向枚举添加值 - 枚举被序列化为数值。</span><span class="sxs-lookup"><span data-stu-id="61a5e-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="61a5e-127">新的枚举值在客户端反序列化为没有枚举名的枚举值。</span><span class="sxs-lookup"><span data-stu-id="61a5e-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="61a5e-128">若要实现非中断性变更，旧客户端在接收新枚举值时必须正确运行。</span><span class="sxs-lookup"><span data-stu-id="61a5e-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="61a5e-129">二进制中断性变更</span><span class="sxs-lookup"><span data-stu-id="61a5e-129">Binary breaking changes</span></span>

<span data-ttu-id="61a5e-130">以下变更在 gRPC 协议级别是非中断性变更，但如果客户端升级到最新的 .proto 协定或客户端 .NET 程序集，则需要对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="61a5e-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="61a5e-131">如果你计划将 gRPC 库发布到 NuGet，二进制兼容性很重要。</span><span class="sxs-lookup"><span data-stu-id="61a5e-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="61a5e-132">删除字段 - 已删除字段中的值被反序列化为消息的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)。</span><span class="sxs-lookup"><span data-stu-id="61a5e-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="61a5e-133">这并不是 gRPC 协议中断性变更，但如果客户端升级到最新的协定，则需要对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="61a5e-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="61a5e-134">删除的字段编号不会在将来被意外重用，这点很重要。</span><span class="sxs-lookup"><span data-stu-id="61a5e-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="61a5e-135">若要确保不会发生这种情况，请使用 Protobuf 的[保留](https://developers.google.com/protocol-buffers/docs/proto3#reserved)关键字指定邮件上已删除的字段编号和名称。</span><span class="sxs-lookup"><span data-stu-id="61a5e-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="61a5e-136">重命名消息 - 消息名称通常不会在网络上发送，因此这不是 gRPC 协议中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="61a5e-137">如果客户端升级到最新的协定，则需要对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="61a5e-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="61a5e-138">当消息名称用于标识消息类型时，[任何](https://developers.google.com/protocol-buffers/docs/proto3#any)字段都会出现消息名称在网络上发送的情况。</span><span class="sxs-lookup"><span data-stu-id="61a5e-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="61a5e-139">更改 csharp_namespace - 更改 `csharp_namespace` 将更改所生成的 .NET 类型的命名空间。</span><span class="sxs-lookup"><span data-stu-id="61a5e-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="61a5e-140">这并不是 gRPC 协议中断性变更，但如果客户端升级到最新的协定，则需要对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="61a5e-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="61a5e-141">协议中断性变更</span><span class="sxs-lookup"><span data-stu-id="61a5e-141">Protocol breaking changes</span></span>

<span data-ttu-id="61a5e-142">以下各项是协议和二进制的中断性变更：</span><span class="sxs-lookup"><span data-stu-id="61a5e-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="61a5e-143">重命名字段 - 对于 Protobuf 内容，字段名只在生成的代码中使用。</span><span class="sxs-lookup"><span data-stu-id="61a5e-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="61a5e-144">字段编号用于标识网络上的字段。</span><span class="sxs-lookup"><span data-stu-id="61a5e-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="61a5e-145">对 Protobuf 来说，重命名字段不是协议中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="61a5e-146">但是，如果服务器正在使用 JSON 内容，则重命名字段是一个中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="61a5e-147">更改字段数据类型 - 将字段的数据类型更改为[不兼容类型](https://developers.google.com/protocol-buffers/docs/proto3#updating)将在反序列化消息时导致错误。</span><span class="sxs-lookup"><span data-stu-id="61a5e-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="61a5e-148">即使新的数据类型是兼容的，但如果客户端升级到最新的协定，它也可能需要更新以支持新的类型。</span><span class="sxs-lookup"><span data-stu-id="61a5e-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="61a5e-149">更改字段编号 - 对于 Protobuf 有效负载，字段编号用于标识网络上的字段。</span><span class="sxs-lookup"><span data-stu-id="61a5e-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="61a5e-150">重命名包、服务或方法 - gRPC 使用包名、服务名和方法名来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="61a5e-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="61a5e-151">客户端从服务器获取 UNIMPLEMENTED 状态。</span><span class="sxs-lookup"><span data-stu-id="61a5e-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="61a5e-152">删除服务或方法 - 客户端在调用已删除的方法时从服务器获取 UNIMPLEMENTED 状态。</span><span class="sxs-lookup"><span data-stu-id="61a5e-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="61a5e-153">行为中断性变更</span><span class="sxs-lookup"><span data-stu-id="61a5e-153">Behavior breaking changes</span></span>

<span data-ttu-id="61a5e-154">在进行非中断性更改时，还必须考虑旧客户端是否可以继续使用新的服务行为。</span><span class="sxs-lookup"><span data-stu-id="61a5e-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="61a5e-155">例如，向请求消息添加新字段：</span><span class="sxs-lookup"><span data-stu-id="61a5e-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="61a5e-156">它不是协议中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="61a5e-157">如果未设置新字段，则在服务器上返回错误状态对于旧客户端来说是一个中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="61a5e-158">行为兼容性由应用特定的代码决定。</span><span class="sxs-lookup"><span data-stu-id="61a5e-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="61a5e-159">版本号服务</span><span class="sxs-lookup"><span data-stu-id="61a5e-159">Version number services</span></span>

<span data-ttu-id="61a5e-160">服务应尽量保持与旧客户端的后向兼容。</span><span class="sxs-lookup"><span data-stu-id="61a5e-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="61a5e-161">最终对应用的更改可能需要进行中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="61a5e-162">中断旧客户端并强制其随服务一起更新不是一种好的用户体验。</span><span class="sxs-lookup"><span data-stu-id="61a5e-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="61a5e-163">若要在进行中断性变更的同时保持后向兼容性，一种方法是发布服务的多个版本。</span><span class="sxs-lookup"><span data-stu-id="61a5e-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="61a5e-164">gRPC 支持可选的[包](https://developers.google.com/protocol-buffers/docs/proto3#packages)说明符，它的功能非常类似于 .NET 命名空间。</span><span class="sxs-lookup"><span data-stu-id="61a5e-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="61a5e-165">实际上，如果 .proto 文件中未设置 `option csharp_namespace`，则 `package` 将用作生成的 .NET 类型的 .NET 命名空间。</span><span class="sxs-lookup"><span data-stu-id="61a5e-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="61a5e-166">该包可用于指定服务的版本号及其消息：</span><span class="sxs-lookup"><span data-stu-id="61a5e-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="61a5e-167">包名称与服务名称相结合以标识服务地址。</span><span class="sxs-lookup"><span data-stu-id="61a5e-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="61a5e-168">服务地址允许并行托管服务的多个版本：</span><span class="sxs-lookup"><span data-stu-id="61a5e-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="61a5e-169">已进行版本控制的服务的实现在 Startup.cs 中注册：</span><span class="sxs-lookup"><span data-stu-id="61a5e-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="61a5e-170">通过在包名称中包含版本号，你可发布具有中断性变更的服务 v2 版本，同时继续支持调用 v1 版本的旧客户端 。</span><span class="sxs-lookup"><span data-stu-id="61a5e-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="61a5e-171">更新客户端以使用 v2 服务后，你可选择删除旧版本。</span><span class="sxs-lookup"><span data-stu-id="61a5e-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="61a5e-172">计划发布服务的多个版本时：</span><span class="sxs-lookup"><span data-stu-id="61a5e-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="61a5e-173">如果合理，请避免中断性变更。</span><span class="sxs-lookup"><span data-stu-id="61a5e-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="61a5e-174">除非进行中断性更改，否则请勿更新版本号。</span><span class="sxs-lookup"><span data-stu-id="61a5e-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="61a5e-175">进行中断性变更时，请务必更新版本号。</span><span class="sxs-lookup"><span data-stu-id="61a5e-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="61a5e-176">发布多个服务版本会使其重复。</span><span class="sxs-lookup"><span data-stu-id="61a5e-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="61a5e-177">若要减少重复，请考虑将业务逻辑从服务实现移动到可由新旧实现重用的集中位置：</span><span class="sxs-lookup"><span data-stu-id="61a5e-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="61a5e-178">使用不同包名称生成的服务和消息属于不同的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="61a5e-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="61a5e-179">将业务逻辑移动到集中位置需要将消息映射到常见类型。</span><span class="sxs-lookup"><span data-stu-id="61a5e-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
