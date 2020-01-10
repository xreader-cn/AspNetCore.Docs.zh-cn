---
title: 版本 gRPC 服务
author: jamesnk
description: 了解如何版本 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 9bd76009ba28a1abef25a98686afea6753d4a8f4
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828511"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="ae519-103">版本 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="ae519-103">Versioning gRPC services</span></span>

<span data-ttu-id="ae519-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="ae519-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="ae519-105">添加到应用中的新功能可能要求提供给客户端的 gRPC 服务更改，有时会出现意外和中断的方式。</span><span class="sxs-lookup"><span data-stu-id="ae519-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="ae519-106">当 gRPC services 更改时：</span><span class="sxs-lookup"><span data-stu-id="ae519-106">When gRPC services change:</span></span>

* <span data-ttu-id="ae519-107">应考虑对客户端的更改的影响。</span><span class="sxs-lookup"><span data-stu-id="ae519-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="ae519-108">应该实现支持更改的版本控制策略。</span><span class="sxs-lookup"><span data-stu-id="ae519-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="ae519-109">向后兼容性</span><span class="sxs-lookup"><span data-stu-id="ae519-109">Backwards compatibility</span></span>

<span data-ttu-id="ae519-110">GRPC 协议旨在支持随时间变化的服务。</span><span class="sxs-lookup"><span data-stu-id="ae519-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="ae519-111">通常，对 gRPC 服务和方法的添加是不间断的。</span><span class="sxs-lookup"><span data-stu-id="ae519-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="ae519-112">非重大更改允许现有的客户端无需更改即可继续工作。</span><span class="sxs-lookup"><span data-stu-id="ae519-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="ae519-113">更改或删除 gRPC 服务是重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="ae519-114">当 gRPC 服务有重大更改时，必须更新和重新部署使用该服务的客户端。</span><span class="sxs-lookup"><span data-stu-id="ae519-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="ae519-115">对服务进行非重大更改有很多好处：</span><span class="sxs-lookup"><span data-stu-id="ae519-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="ae519-116">现有客户端将继续运行。</span><span class="sxs-lookup"><span data-stu-id="ae519-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="ae519-117">避免向客户端通知重大更改并进行更新。</span><span class="sxs-lookup"><span data-stu-id="ae519-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="ae519-118">只需记录并维护服务的一个版本。</span><span class="sxs-lookup"><span data-stu-id="ae519-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="ae519-119">非重大变化</span><span class="sxs-lookup"><span data-stu-id="ae519-119">Non-breaking changes</span></span>

<span data-ttu-id="ae519-120">在 gRPC 协议级别和 .NET 二进制级别，这些更改不会中断。</span><span class="sxs-lookup"><span data-stu-id="ae519-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="ae519-121">**添加新服务**</span><span class="sxs-lookup"><span data-stu-id="ae519-121">**Adding a new service**</span></span>
* <span data-ttu-id="ae519-122">**向服务中添加新方法**</span><span class="sxs-lookup"><span data-stu-id="ae519-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="ae519-123">如果未设置，则在请求消息中添加**字段**时，将使用服务器上的[默认值](https://developers.google.com/protocol-buffers/docs/proto3#default)反序列化添加到请求消息的字段。</span><span class="sxs-lookup"><span data-stu-id="ae519-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="ae519-124">若要进行非重大更改，当旧客户端不设置新字段时，服务必须成功。</span><span class="sxs-lookup"><span data-stu-id="ae519-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="ae519-125">向**响应消息添加字段**-添加到响应消息的字段将反序列化到消息的客户端上的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)集合中。</span><span class="sxs-lookup"><span data-stu-id="ae519-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="ae519-126">**向枚举枚举添加值**将被序列化为数值。</span><span class="sxs-lookup"><span data-stu-id="ae519-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="ae519-127">新的枚举值将在客户端上反序列化到没有枚举名称的枚举值。</span><span class="sxs-lookup"><span data-stu-id="ae519-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="ae519-128">若要进行非重大更改，较旧的客户端必须在接收新枚举值时正确运行。</span><span class="sxs-lookup"><span data-stu-id="ae519-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="ae519-129">二进制重大更改</span><span class="sxs-lookup"><span data-stu-id="ae519-129">Binary breaking changes</span></span>

<span data-ttu-id="ae519-130">以下更改在 gRPC 协议级别不会中断，但当客户端升级到最新*的 proto*协定或客户端 .net 程序集时，需要对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="ae519-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="ae519-131">如果计划将 gRPC 库发布到 NuGet，则二进制兼容性非常重要。</span><span class="sxs-lookup"><span data-stu-id="ae519-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="ae519-132">**删除**字段中的字段值将反序列化为消息的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)。</span><span class="sxs-lookup"><span data-stu-id="ae519-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="ae519-133">这不是一种 gRPC 的协议重大更改，但如果要升级到最新的合同，则需要更新客户端。</span><span class="sxs-lookup"><span data-stu-id="ae519-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="ae519-134">很重要的一点是，以后删除的字段编号不会意外重复使用。</span><span class="sxs-lookup"><span data-stu-id="ae519-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="ae519-135">若要确保不会发生这种情况，请使用 Protobuf 的[保留](https://developers.google.com/protocol-buffers/docs/proto3#reserved)关键字在消息上指定已删除的字段编号和名称。</span><span class="sxs-lookup"><span data-stu-id="ae519-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="ae519-136">**重命名消息**-消息名称通常不会发送到网络上，因此这不是 gRPC 的协议重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="ae519-137">如果客户端升级到最新的合同，则需要更新客户端。</span><span class="sxs-lookup"><span data-stu-id="ae519-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="ae519-138">在网络上发送消息[名称的一](https://developers.google.com/protocol-buffers/docs/proto3#any)种情况是，使用消息名称标识消息类型时，会在网络**上发送消息名称。**</span><span class="sxs-lookup"><span data-stu-id="ae519-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="ae519-139">更改**csharp_namespace**更改的 `csharp_namespace` 将更改所生成的 .net 类型的命名空间。</span><span class="sxs-lookup"><span data-stu-id="ae519-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="ae519-140">这不是一种 gRPC 的协议重大更改，但如果要升级到最新的合同，则需要更新客户端。</span><span class="sxs-lookup"><span data-stu-id="ae519-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="ae519-141">协议重大更改</span><span class="sxs-lookup"><span data-stu-id="ae519-141">Protocol breaking changes</span></span>

<span data-ttu-id="ae519-142">以下各项是协议和二进制的重大更改：</span><span class="sxs-lookup"><span data-stu-id="ae519-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="ae519-143">**重命名字段**-使用 Protobuf 内容时，字段名称仅用于生成的代码中。</span><span class="sxs-lookup"><span data-stu-id="ae519-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="ae519-144">此字段编号用于标识网络上的字段。</span><span class="sxs-lookup"><span data-stu-id="ae519-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="ae519-145">重命名字段不是 Protobuf 的协议重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="ae519-146">但是，如果服务器使用 JSON 内容，则重命名字段是一项重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="ae519-147">将**字段数据类型**更改为[不兼容类型](https://developers.google.com/protocol-buffers/docs/proto3#updating)会在反序列化消息时导致错误。</span><span class="sxs-lookup"><span data-stu-id="ae519-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="ae519-148">即使新的数据类型是兼容的，也可能需要更新客户端以支持新类型（如果它升级到最新的协定）。</span><span class="sxs-lookup"><span data-stu-id="ae519-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="ae519-149">**更改字段编号**-使用 Protobuf 有效负载时，字段编号用于标识网络上的字段。</span><span class="sxs-lookup"><span data-stu-id="ae519-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="ae519-150">**重命名包、服务或方法**-gRPC 使用包名称、服务名称和方法名称来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="ae519-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="ae519-151">客户端从服务器获取未*实现*的状态。</span><span class="sxs-lookup"><span data-stu-id="ae519-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="ae519-152">**删除服务或方法**-当调用已删除的方法时，客户端从服务器获取未*实现*的状态。</span><span class="sxs-lookup"><span data-stu-id="ae519-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="ae519-153">行为重大更改</span><span class="sxs-lookup"><span data-stu-id="ae519-153">Behavior breaking changes</span></span>

<span data-ttu-id="ae519-154">在进行非重大更改时，您还必须考虑到较旧的客户端是否可以继续使用新的服务行为。</span><span class="sxs-lookup"><span data-stu-id="ae519-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="ae519-155">例如，将新字段添加到请求消息：</span><span class="sxs-lookup"><span data-stu-id="ae519-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="ae519-156">不是协议的重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="ae519-157">如果新字段未设置，则在服务器上返回错误状态，从而使其成为旧客户端的重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="ae519-158">行为兼容性由应用程序特定的代码确定。</span><span class="sxs-lookup"><span data-stu-id="ae519-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="ae519-159">版本号服务</span><span class="sxs-lookup"><span data-stu-id="ae519-159">Version number services</span></span>

<span data-ttu-id="ae519-160">服务应尽量保持与旧客户端的向后兼容。</span><span class="sxs-lookup"><span data-stu-id="ae519-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="ae519-161">最终，对应用所做的更改可能需要进行重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="ae519-162">打破旧客户端，并强制将它们与服务一起更新并不是一种很好的用户体验。</span><span class="sxs-lookup"><span data-stu-id="ae519-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="ae519-163">在进行重大更改时保持向后兼容性的一种方法是发布服务的多个版本。</span><span class="sxs-lookup"><span data-stu-id="ae519-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="ae519-164">gRPC 支持可选的[包](https://developers.google.com/protocol-buffers/docs/proto3#packages)说明符，其功能与 .net 命名空间非常类似。</span><span class="sxs-lookup"><span data-stu-id="ae519-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="ae519-165">事实上，如果未在*proto*文件中设置 `option csharp_namespace`，则 `package` 将用作生成的 .net 类型的 .net 命名空间。</span><span class="sxs-lookup"><span data-stu-id="ae519-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="ae519-166">包可用于指定服务及其消息的版本号：</span><span class="sxs-lookup"><span data-stu-id="ae519-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="ae519-167">包名称与服务名称组合在一起，以标识服务地址。</span><span class="sxs-lookup"><span data-stu-id="ae519-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="ae519-168">服务地址允许并行承载服务的多个版本：</span><span class="sxs-lookup"><span data-stu-id="ae519-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="ae519-169">版本控制服务的实现在*Startup.cs*中注册：</span><span class="sxs-lookup"><span data-stu-id="ae519-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="ae519-170">如果在包名称中包含版本号，则可以通过重大更改发布服务的*v2*版本，同时继续支持调用*v1*版本的旧客户端。</span><span class="sxs-lookup"><span data-stu-id="ae519-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="ae519-171">当客户端更新为使用*v2*服务后，您可以选择删除旧版本。</span><span class="sxs-lookup"><span data-stu-id="ae519-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="ae519-172">计划发布服务的多个版本时：</span><span class="sxs-lookup"><span data-stu-id="ae519-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="ae519-173">如果合理，请避免重大更改。</span><span class="sxs-lookup"><span data-stu-id="ae519-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="ae519-174">除非进行重大更改，否则不要更新版本号。</span><span class="sxs-lookup"><span data-stu-id="ae519-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="ae519-175">进行重大更改时，请更新版本号。</span><span class="sxs-lookup"><span data-stu-id="ae519-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="ae519-176">发布服务的多个版本会将其重复。</span><span class="sxs-lookup"><span data-stu-id="ae519-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="ae519-177">若要减少重复，请考虑将业务逻辑从服务实现移动到可供旧实现和新实现重复使用的集中位置：</span><span class="sxs-lookup"><span data-stu-id="ae519-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="ae519-178">用不同的包名称生成的服务和消息是**不同的 .net 类型**。</span><span class="sxs-lookup"><span data-stu-id="ae519-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="ae519-179">将业务逻辑移动到集中位置需要将消息映射到常见类型。</span><span class="sxs-lookup"><span data-stu-id="ae519-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
