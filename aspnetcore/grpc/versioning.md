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
# <a name="versioning-grpc-services"></a>版本 gRPC 服务

按[James 牛顿-k](https://twitter.com/jamesnk)

添加到应用中的新功能可能要求提供给客户端的 gRPC 服务更改，有时会出现意外和中断的方式。 当 gRPC services 更改时：

* 应考虑对客户端的更改的影响。
* 应该实现支持更改的版本控制策略。

## <a name="backwards-compatibility"></a>向后兼容性

GRPC 协议旨在支持随时间变化的服务。 通常，对 gRPC 服务和方法的添加是不间断的。 非重大更改允许现有的客户端无需更改即可继续工作。 更改或删除 gRPC 服务是重大更改。 当 gRPC 服务有重大更改时，必须更新和重新部署使用该服务的客户端。

对服务进行非重大更改有很多好处：

* 现有客户端将继续运行。
* 避免向客户端通知重大更改并进行更新。
* 只需记录并维护服务的一个版本。

### <a name="non-breaking-changes"></a>非重大变化

在 gRPC 协议级别和 .NET 二进制级别，这些更改不会中断。

* **添加新服务**
* **向服务中添加新方法**
* 如果未设置，则在请求消息中添加**字段**时，将使用服务器上的[默认值](https://developers.google.com/protocol-buffers/docs/proto3#default)反序列化添加到请求消息的字段。 若要进行非重大更改，当旧客户端不设置新字段时，服务必须成功。
* 向**响应消息添加字段**-添加到响应消息的字段将反序列化到消息的客户端上的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)集合中。
* **向枚举枚举添加值**将被序列化为数值。 新的枚举值将在客户端上反序列化到没有枚举名称的枚举值。 若要进行非重大更改，较旧的客户端必须在接收新枚举值时正确运行。

### <a name="binary-breaking-changes"></a>二进制重大更改

以下更改在 gRPC 协议级别不会中断，但当客户端升级到最新*的 proto*协定或客户端 .net 程序集时，需要对其进行更新。 如果计划将 gRPC 库发布到 NuGet，则二进制兼容性非常重要。

* **删除**字段中的字段值将反序列化为消息的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)。 这不是一种 gRPC 的协议重大更改，但如果要升级到最新的合同，则需要更新客户端。 很重要的一点是，以后删除的字段编号不会意外重复使用。 若要确保不会发生这种情况，请使用 Protobuf 的[保留](https://developers.google.com/protocol-buffers/docs/proto3#reserved)关键字在消息上指定已删除的字段编号和名称。
* **重命名消息**-消息名称通常不会发送到网络上，因此这不是 gRPC 的协议重大更改。 如果客户端升级到最新的合同，则需要更新客户端。 在网络上发送消息[名称的一](https://developers.google.com/protocol-buffers/docs/proto3#any)种情况是，使用消息名称标识消息类型时，会在网络**上发送消息名称。**
* 更改**csharp_namespace**更改的 `csharp_namespace` 将更改所生成的 .net 类型的命名空间。 这不是一种 gRPC 的协议重大更改，但如果要升级到最新的合同，则需要更新客户端。

### <a name="protocol-breaking-changes"></a>协议重大更改

以下各项是协议和二进制的重大更改：

* **重命名字段**-使用 Protobuf 内容时，字段名称仅用于生成的代码中。 此字段编号用于标识网络上的字段。 重命名字段不是 Protobuf 的协议重大更改。 但是，如果服务器使用 JSON 内容，则重命名字段是一项重大更改。
* 将**字段数据类型**更改为[不兼容类型](https://developers.google.com/protocol-buffers/docs/proto3#updating)会在反序列化消息时导致错误。 即使新的数据类型是兼容的，也可能需要更新客户端以支持新类型（如果它升级到最新的协定）。
* **更改字段编号**-使用 Protobuf 有效负载时，字段编号用于标识网络上的字段。
* **重命名包、服务或方法**-gRPC 使用包名称、服务名称和方法名称来生成 URL。 客户端从服务器获取未*实现*的状态。
* **删除服务或方法**-当调用已删除的方法时，客户端从服务器获取未*实现*的状态。

### <a name="behavior-breaking-changes"></a>行为重大更改

在进行非重大更改时，您还必须考虑到较旧的客户端是否可以继续使用新的服务行为。 例如，将新字段添加到请求消息：

* 不是协议的重大更改。
* 如果新字段未设置，则在服务器上返回错误状态，从而使其成为旧客户端的重大更改。

行为兼容性由应用程序特定的代码确定。

## <a name="version-number-services"></a>版本号服务

服务应尽量保持与旧客户端的向后兼容。 最终，对应用所做的更改可能需要进行重大更改。 打破旧客户端，并强制将它们与服务一起更新并不是一种很好的用户体验。 在进行重大更改时保持向后兼容性的一种方法是发布服务的多个版本。

gRPC 支持可选的[包](https://developers.google.com/protocol-buffers/docs/proto3#packages)说明符，其功能与 .net 命名空间非常类似。 事实上，如果未在*proto*文件中设置 `option csharp_namespace`，则 `package` 将用作生成的 .net 类型的 .net 命名空间。 包可用于指定服务及其消息的版本号：

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

包名称与服务名称组合在一起，以标识服务地址。 服务地址允许并行承载服务的多个版本：

* `greet.v1.Greeter`
* `greet.v2.Greeter`

版本控制服务的实现在*Startup.cs*中注册：

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

如果在包名称中包含版本号，则可以通过重大更改发布服务的*v2*版本，同时继续支持调用*v1*版本的旧客户端。 当客户端更新为使用*v2*服务后，您可以选择删除旧版本。 计划发布服务的多个版本时：

* 如果合理，请避免重大更改。
* 除非进行重大更改，否则不要更新版本号。
* 进行重大更改时，请更新版本号。

发布服务的多个版本会将其重复。 若要减少重复，请考虑将业务逻辑从服务实现移动到可供旧实现和新实现重复使用的集中位置：

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

用不同的包名称生成的服务和消息是**不同的 .net 类型**。 将业务逻辑移动到集中位置需要将消息映射到常见类型。
