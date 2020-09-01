---
title: 为 .NET 应用创建 Protobuf 消息
author: jamesnk
description: 了解如何为 .NET 应用创建 Protobuf 消息。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/protobuf
ms.openlocfilehash: b8149b79c1e7b204e52cc8595d1193b623bb0008
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945456"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="0f646-103">为 .NET 应用创建 Protobuf 消息</span><span class="sxs-lookup"><span data-stu-id="0f646-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="0f646-104">作者：[James Newton-King](https://twitter.com/jamesnk) 和 [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="0f646-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="0f646-105">gRPC 使用 [Protobuf](https://developers.google.com/protocol-buffers) 作为其接口定义语言 (IDL)。</span><span class="sxs-lookup"><span data-stu-id="0f646-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="0f646-106">Protobuf IDL 是一种中性语言格式，用于指定 gRPC 服务发送和接收的消息。</span><span class="sxs-lookup"><span data-stu-id="0f646-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="0f646-107">Protobuf 消息在 .proto 文件中定义。</span><span class="sxs-lookup"><span data-stu-id="0f646-107">Protobuf messages are defined in *.proto* files.</span></span> <span data-ttu-id="0f646-108">本文档介绍如何将 Protobuf 概念映射到 .NET。</span><span class="sxs-lookup"><span data-stu-id="0f646-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="0f646-109">Protobuf 消息</span><span class="sxs-lookup"><span data-stu-id="0f646-109">Protobuf messages</span></span>

<span data-ttu-id="0f646-110">消息是 Protobuf 中的主要数据传输对象。</span><span class="sxs-lookup"><span data-stu-id="0f646-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="0f646-111">它们在概念上类似于 .NET 类。</span><span class="sxs-lookup"><span data-stu-id="0f646-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="0f646-112">前面的消息定义将三个字段指定为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="0f646-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="0f646-113">与 .NET 类型上的属性类似，每个字段都有名称和类型。</span><span class="sxs-lookup"><span data-stu-id="0f646-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="0f646-114">字段类型可以是 [Protobuf 标量值类型](#scalar-value-types)（如 `int32`），也可以是其他消息。</span><span class="sxs-lookup"><span data-stu-id="0f646-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="0f646-115">包括名称，消息定义中的每个字段都有一个唯一的编号。</span><span class="sxs-lookup"><span data-stu-id="0f646-115">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="0f646-116">消息序列化为 Protobuf 时，字段编号用于标识字段。</span><span class="sxs-lookup"><span data-stu-id="0f646-116">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="0f646-117">序列化一个小编号比序列化整个字段名称要快。</span><span class="sxs-lookup"><span data-stu-id="0f646-117">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="0f646-118">因为字段编号标识字段，所以在更改编号时务必小心。</span><span class="sxs-lookup"><span data-stu-id="0f646-118">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="0f646-119">有关更改 Protobuf 消息的详细信息，请参阅 <xref:grpc/versioning>。</span><span class="sxs-lookup"><span data-stu-id="0f646-119">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="0f646-120">生成应用时，Protobuf 工具将从 .proto 文件生成 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="0f646-120">When an app is built the Protobuf tooling generates .NET types from *.proto* files.</span></span> <span data-ttu-id="0f646-121">`Person` 消息生成 .NET 类：</span><span class="sxs-lookup"><span data-stu-id="0f646-121">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="0f646-122">有关 Protobuf 消息的详细信息，请参阅 [Protobuf 语言指南](https://developers.google.com/protocol-buffers/docs/proto3#simple)。</span><span class="sxs-lookup"><span data-stu-id="0f646-122">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="0f646-123">标量值类型</span><span class="sxs-lookup"><span data-stu-id="0f646-123">Scalar Value Types</span></span>

<span data-ttu-id="0f646-124">Protobuf 支持一系列本机标量值类型。</span><span class="sxs-lookup"><span data-stu-id="0f646-124">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="0f646-125">下表列出了全部本机标量值类型及其等效 C# 类型：</span><span class="sxs-lookup"><span data-stu-id="0f646-125">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="0f646-126">Protobuf 类型</span><span class="sxs-lookup"><span data-stu-id="0f646-126">Protobuf type</span></span> | <span data-ttu-id="0f646-127">C# 类型</span><span class="sxs-lookup"><span data-stu-id="0f646-127">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

### <a name="dates-and-times"></a><span data-ttu-id="0f646-128">日期和时间</span><span class="sxs-lookup"><span data-stu-id="0f646-128">Dates and times</span></span>

<span data-ttu-id="0f646-129">本机标量类型不提供与 .NET 的 <xref:System.DateTimeOffset>、<xref:System.DateTime> 和 <xref:System.TimeSpan> 等效的日期和时间值。</span><span class="sxs-lookup"><span data-stu-id="0f646-129">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="0f646-130">可使用 Protobuf 的一些“已知类型”扩展来指定这些类型。</span><span class="sxs-lookup"><span data-stu-id="0f646-130">These types can be specified by using some of Protobuf's "Well Known Types" extensions.</span></span> <span data-ttu-id="0f646-131">这些扩展为受支持平台中的复杂字段类型提供代码生成和运行时支持。</span><span class="sxs-lookup"><span data-stu-id="0f646-131">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="0f646-132">下表显示日期和时间类型：</span><span class="sxs-lookup"><span data-stu-id="0f646-132">The following table shows the date and time types:</span></span>

| <span data-ttu-id="0f646-133">.NET 类型</span><span class="sxs-lookup"><span data-stu-id="0f646-133">.NET type</span></span> | <span data-ttu-id="0f646-134">Protobuf 已知类型</span><span class="sxs-lookup"><span data-stu-id="0f646-134">Protobuf well-known type</span></span> |
| ------- | ------------------------ |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime` | `google.protobuf.Timestamp` |
| `TimeSpan` | `google.protobuf.Duration` |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="0f646-135">C# 类中生成的属性不是 .NET 日期和时间类型。</span><span class="sxs-lookup"><span data-stu-id="0f646-135">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="0f646-136">属性使用 `Google.Protobuf.WellKnownTypes` 命名空间中的 `Timestamp` 和 `Duration` 类。</span><span class="sxs-lookup"><span data-stu-id="0f646-136">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="0f646-137">这些类提供在 `DateTimeOffset`、`DateTime` 和 `TimeSpan` 之间进行转换的方法。</span><span class="sxs-lookup"><span data-stu-id="0f646-137">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="0f646-138">`Timestamp` 类型适用于 UTC 时间。</span><span class="sxs-lookup"><span data-stu-id="0f646-138">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="0f646-139">`DateTimeOffset` 值的偏移量始终为零，并且 `DateTime.Kind` 属性始终为 `DateTimeKind.Utc`。</span><span class="sxs-lookup"><span data-stu-id="0f646-139">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="0f646-140">可为 null 的类型</span><span class="sxs-lookup"><span data-stu-id="0f646-140">Nullable types</span></span>

<span data-ttu-id="0f646-141">C# 的 Protobuf 代码生成使用本机类型，如 `int` 表示 `int32`。</span><span class="sxs-lookup"><span data-stu-id="0f646-141">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="0f646-142">因此这些值始终包括在内，不能为 `null`。</span><span class="sxs-lookup"><span data-stu-id="0f646-142">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="0f646-143">对于需要显式 `null` 的值（例如在 C# 代码中使用 `int?`），Protobuf 的“已知类型”包括编译为可以为 null 的 C# 类型的包装器。</span><span class="sxs-lookup"><span data-stu-id="0f646-143">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's "Well Known Types" include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="0f646-144">若要使用它们，请将 `wrappers.proto` 导入到 `.proto` 文件中，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="0f646-144">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="0f646-145">对于生成的消息属性，Protobuf 使用可为 null 的 .NET 类型，例如 `int?`。</span><span class="sxs-lookup"><span data-stu-id="0f646-145">Protobuf uses .NET nullable types, for example, `int?`, for the generated message property.</span></span>

<span data-ttu-id="0f646-146">下表完整列出了包装器类型以及它们的等效 C# 类型：</span><span class="sxs-lookup"><span data-stu-id="0f646-146">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="0f646-147">C# 类型</span><span class="sxs-lookup"><span data-stu-id="0f646-147">C# type</span></span>   | <span data-ttu-id="0f646-148">已知类型包装器</span><span class="sxs-lookup"><span data-stu-id="0f646-148">Well Known Type wrapper</span></span>       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a><span data-ttu-id="0f646-149">小数</span><span class="sxs-lookup"><span data-stu-id="0f646-149">Decimals</span></span>

<span data-ttu-id="0f646-150">Protobuf 本身不支持 .NET `decimal` 类型，只支持 `double` 和 `float`。</span><span class="sxs-lookup"><span data-stu-id="0f646-150">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="0f646-151">在 Protobuf 项目中，我们正在探讨这样一种可能性：将标准 decimal 类型添加到已知类型，并为支持它的语言和框架添加平台支持。</span><span class="sxs-lookup"><span data-stu-id="0f646-151">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the well-known types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="0f646-152">尚未实现任何内容。</span><span class="sxs-lookup"><span data-stu-id="0f646-152">Nothing has been implemented yet.</span></span>

<span data-ttu-id="0f646-153">可以创建消息定义来表示 `decimal` 类型，以便在 .NET 客户端和服务器之间实现安全序列化。</span><span class="sxs-lookup"><span data-stu-id="0f646-153">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="0f646-154">但其他平台上的开发人员必须了解所使用的格式，并能够实现自己对其的处理。</span><span class="sxs-lookup"><span data-stu-id="0f646-154">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="0f646-155">为 Protobuf 创建自定义 decimal 类型</span><span class="sxs-lookup"><span data-stu-id="0f646-155">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="0f646-156">`nanos` 字段表示从 `0.999_999_999` 到 `-0.999_999_999` 的值。</span><span class="sxs-lookup"><span data-stu-id="0f646-156">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="0f646-157">例如，`decimal` 值 `1.5m` 将表示为 `{ units = 1, nanos = 500_000_000 }`。</span><span class="sxs-lookup"><span data-stu-id="0f646-157">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="0f646-158">这就是此示例中的 `nanos` 字段使用 `sfixed32` 类型的原因：对于较大的值，其编码效率比 `int32` 更高。</span><span class="sxs-lookup"><span data-stu-id="0f646-158">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="0f646-159">如果 `units` 字段为负，则 `nanos` 字段也应为负。</span><span class="sxs-lookup"><span data-stu-id="0f646-159">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="0f646-160">还有多个其他算法可将 `decimal` 值编码为字节字符串，但此消息比其他任何字符串都易于理解。</span><span class="sxs-lookup"><span data-stu-id="0f646-160">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="0f646-161">在不同平台上，这些值不受 big-endian 或 little-endian 影响。</span><span class="sxs-lookup"><span data-stu-id="0f646-161">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="0f646-162">此类型与 BCL `decimal` 类型之间的转换可能会在 C# 中实现，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0f646-162">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="0f646-163">集合</span><span class="sxs-lookup"><span data-stu-id="0f646-163">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="0f646-164">列表</span><span class="sxs-lookup"><span data-stu-id="0f646-164">Lists</span></span>

<span data-ttu-id="0f646-165">Protobuf 中，在字段上使用 `repeated` 前缀关键字指定列表。</span><span class="sxs-lookup"><span data-stu-id="0f646-165">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="0f646-166">以下示例演示如何创建列表：</span><span class="sxs-lookup"><span data-stu-id="0f646-166">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="0f646-167">在生成的代码中，`repeated` 字段由 `Google.Protobuf.Collections.RepeatedField<T>` 泛型类型表示。</span><span class="sxs-lookup"><span data-stu-id="0f646-167">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="0f646-168">`RepeatedField<T>` 可实现 <xref:System.Collections.Generic.IList%601>。</span><span class="sxs-lookup"><span data-stu-id="0f646-168">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="0f646-169">因此你可使用 LINQ 查询，或者将其转换为数组或列表。</span><span class="sxs-lookup"><span data-stu-id="0f646-169">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="0f646-170">`RepeatedField<T>` 属性没有公共 setter。</span><span class="sxs-lookup"><span data-stu-id="0f646-170">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="0f646-171">项应添加到现有集合中。</span><span class="sxs-lookup"><span data-stu-id="0f646-171">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="0f646-172">字典</span><span class="sxs-lookup"><span data-stu-id="0f646-172">Dictionaries</span></span>

<span data-ttu-id="0f646-173">.NET <xref:System.Collections.Generic.IDictionary%602> 类型在 Protobuf 中使用 `map<key_type, value_type>` 表示。</span><span class="sxs-lookup"><span data-stu-id="0f646-173">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="0f646-174">在生成的 .NET 代码中，`map` 字段由 `Google.Protobuf.Collections.MapField<TKey, TValue>` 泛型类型表示。</span><span class="sxs-lookup"><span data-stu-id="0f646-174">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="0f646-175">`MapField<TKey, TValue>` 可实现 <xref:System.Collections.Generic.IDictionary%602>。</span><span class="sxs-lookup"><span data-stu-id="0f646-175">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="0f646-176">与 `repeated` 属性一样，`map` 属性没有公共 setter。</span><span class="sxs-lookup"><span data-stu-id="0f646-176">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="0f646-177">项应添加到现有集合中。</span><span class="sxs-lookup"><span data-stu-id="0f646-177">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="0f646-178">无结构的条件消息</span><span class="sxs-lookup"><span data-stu-id="0f646-178">Unstructured and conditional messages</span></span>

<span data-ttu-id="0f646-179">Protobuf 是协定优先的消息格式，在生成应用时，需要在 .proto 文件中指定应用消息。</span><span class="sxs-lookup"><span data-stu-id="0f646-179">Protobuf is a contract-first messaging format, and an apps messages need to be specified in *.proto* files when the app is built.</span></span> <span data-ttu-id="0f646-180">对于高级方案，Protobuf 提供语言功能和已知类型，以支持条件消息和未知消息。</span><span class="sxs-lookup"><span data-stu-id="0f646-180">For advanced scenarios, Protobuf offers language features and well known types to support conditional and unknown messages.</span></span>

### <a name="any"></a><span data-ttu-id="0f646-181">任意</span><span class="sxs-lookup"><span data-stu-id="0f646-181">Any</span></span>

<span data-ttu-id="0f646-182">利用 `Any` 类型，可以将消息作为嵌入类型使用，而无需 .proto 定义。</span><span class="sxs-lookup"><span data-stu-id="0f646-182">The `Any` type lets you use messages as embedded types without having their *.proto* definition.</span></span> <span data-ttu-id="0f646-183">若要使用 `Any` 类型，请导入 `any.proto`。</span><span class="sxs-lookup"><span data-stu-id="0f646-183">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="0f646-184">Oneof</span><span class="sxs-lookup"><span data-stu-id="0f646-184">Oneof</span></span>

<span data-ttu-id="0f646-185">`oneof` 字段是一种语言特性。</span><span class="sxs-lookup"><span data-stu-id="0f646-185">`oneof` fields are a language feature.</span></span> <span data-ttu-id="0f646-186">编译器在生成消息类时处理 `oneof` 关键字。</span><span class="sxs-lookup"><span data-stu-id="0f646-186">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="0f646-187">使用 `oneof` 指定可能返回 `Person` 或 `Error` 的响应消息可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="0f646-187">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="0f646-188">在整个消息声明中，`oneof` 集内的字段必须具有唯一的字段编号。</span><span class="sxs-lookup"><span data-stu-id="0f646-188">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="0f646-189">使用 `oneof` 时，生成的 C# 代码包括一个枚举，用于指定哪些字段已设置。</span><span class="sxs-lookup"><span data-stu-id="0f646-189">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="0f646-190">可以测试枚举来查找已设置的字段。</span><span class="sxs-lookup"><span data-stu-id="0f646-190">You can test the enum to find which field is set.</span></span> <span data-ttu-id="0f646-191">未设置的字段将返回 `null` 或默认值，而不是引发异常。</span><span class="sxs-lookup"><span data-stu-id="0f646-191">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="0f646-192">“值”</span><span class="sxs-lookup"><span data-stu-id="0f646-192">Value</span></span>

<span data-ttu-id="0f646-193">`Value` 类型表示动态类型的值。</span><span class="sxs-lookup"><span data-stu-id="0f646-193">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="0f646-194">它可以是 `null`、数字、字符串、布尔值、值字典 (`Struct`) 或值列表 (`ValueList`)。</span><span class="sxs-lookup"><span data-stu-id="0f646-194">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="0f646-195">`Value` 是一个已知类型，它使用前面讨论的 `oneof` 功能。</span><span class="sxs-lookup"><span data-stu-id="0f646-195">`Value` is a well known type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="0f646-196">若要使用 `Value` 类型，请导入 `struct.proto`。</span><span class="sxs-lookup"><span data-stu-id="0f646-196">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="0f646-197">直接使用 `Value` 可能很冗长。</span><span class="sxs-lookup"><span data-stu-id="0f646-197">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="0f646-198">使用 `Value` 的替代方法是通过 Protobuf 的内置支持，将消息映射到 JSON。</span><span class="sxs-lookup"><span data-stu-id="0f646-198">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="0f646-199">Protobuf 的 `JsonFormatter` 和 `JsonWriter` 类型可用于任何 Protobuf 消息。</span><span class="sxs-lookup"><span data-stu-id="0f646-199">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="0f646-200">`Value` 特别适用于与 JSON 进行转换。</span><span class="sxs-lookup"><span data-stu-id="0f646-200">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="0f646-201">以下是与之前的代码等效的 JSON：</span><span class="sxs-lookup"><span data-stu-id="0f646-201">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="0f646-202">其他资源</span><span class="sxs-lookup"><span data-stu-id="0f646-202">Additional resources</span></span>

* [<span data-ttu-id="0f646-203">Protobuf 语言指南</span><span class="sxs-lookup"><span data-stu-id="0f646-203">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
