---
title: 带有 Swagger/OpenAPI 的 ASP.NET Core Web API 帮助页
author: RicoSuter
description: 本教程提供添加 Swagger 以生成文档的演练和针对 Web API 应用的帮助页。
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 66b8278e84df5ee56582254ebe2dc99ada98a9dc
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060301"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="41540-103">带有 Swagger/OpenAPI 的 ASP.NET Core Web API 帮助页</span><span class="sxs-lookup"><span data-stu-id="41540-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="41540-104">作者：[Christoph Nienaber](https://twitter.com/zuckerthoben) 和 [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="41540-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="41540-105">使用 Web API 时，了解其各种方法对开发人员来说可能是一项挑战。</span><span class="sxs-lookup"><span data-stu-id="41540-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="41540-106">[Swagger](https://swagger.io/) 也称为 [OpenAPI](https://www.openapis.org/)，解决了为 Web API 生成有用文档和帮助页的问题。</span><span class="sxs-lookup"><span data-stu-id="41540-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="41540-107">它具有诸如交互式文档、客户端 SDK 生成和 API 可发现性等优点。</span><span class="sxs-lookup"><span data-stu-id="41540-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="41540-108">本文展示了 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 和 [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger 实现：</span><span class="sxs-lookup"><span data-stu-id="41540-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="41540-109">**Swashbuckle.AspNetCore** 是一个开源项目，用于生成 ASP.NET Core Web API 的 Swagger 文档。</span><span class="sxs-lookup"><span data-stu-id="41540-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="41540-110">**NSwag** 是另一个用于生成 Swagger 文档并将 [Swagger UI](https://swagger.io/swagger-ui/) 或 [ReDoc](https://github.com/Rebilly/ReDoc) 集成到 ASP.NET Core Web API 中的开源项目。</span><span class="sxs-lookup"><span data-stu-id="41540-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="41540-111">此外，NSwag 还提供了为 API 生成 C# 和 TypeScript 客户端代码的方法。</span><span class="sxs-lookup"><span data-stu-id="41540-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="41540-112">什么是 Swagger/OpenAPI？</span><span class="sxs-lookup"><span data-stu-id="41540-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="41540-113">Swagger 是一个与语言无关的规范，用于描述 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API。</span><span class="sxs-lookup"><span data-stu-id="41540-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="41540-114">Swagger 项目已捐赠给 [OpenAPI 计划](https://www.openapis.org/)，现在它被称为开放 API。</span><span class="sxs-lookup"><span data-stu-id="41540-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="41540-115">这两个名称可互换使用，但 OpenAPI 是首选。</span><span class="sxs-lookup"><span data-stu-id="41540-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="41540-116">它允许计算机和人员了解服务的功能，而无需直接访问实现（源代码、网络访问、文档）。</span><span class="sxs-lookup"><span data-stu-id="41540-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="41540-117">其中一个目标是尽量减少连接取消关联的服务所需的工作量。</span><span class="sxs-lookup"><span data-stu-id="41540-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="41540-118">另一个目标是减少准确记录服务所需的时间。</span><span class="sxs-lookup"><span data-stu-id="41540-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="41540-119">OpenAPI 规范 (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="41540-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="41540-120">OpenAPI 流的核心是规范，默认情况下是名为 openapi.json 的文档。</span><span class="sxs-lookup"><span data-stu-id="41540-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json*.</span></span> <span data-ttu-id="41540-121">它由 OpenAPI 工具链（或其第三方实现）根据你的服务生成。</span><span class="sxs-lookup"><span data-stu-id="41540-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="41540-122">它描述了 API 的功能以及使用 HTTP 对其进行访问的方式。</span><span class="sxs-lookup"><span data-stu-id="41540-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="41540-123">它驱动 Swagger UI，并由工具链用来启用发现和客户端代码生成。</span><span class="sxs-lookup"><span data-stu-id="41540-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="41540-124">下面是为简洁起见而缩减的 OpenAPI 规范的示例：</span><span class="sxs-lookup"><span data-stu-id="41540-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="41540-125">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="41540-125">Swagger UI</span></span>

<span data-ttu-id="41540-126">[Swagger UI](https://swagger.io/swagger-ui/) 提供了基于 Web 的 UI，它使用生成的 OpenAPI 规范提供有关服务的信息。</span><span class="sxs-lookup"><span data-stu-id="41540-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="41540-127">Swashbuckle 和 NSwag 均包含 Swagger UI 的嵌入式版本，因此可使用中间件注册调用将该嵌入式版本托管在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="41540-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="41540-128">Web UI 如下所示：</span><span class="sxs-lookup"><span data-stu-id="41540-128">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="41540-130">控制器中的每个公共操作方法都可以从 UI 中进行测试。</span><span class="sxs-lookup"><span data-stu-id="41540-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="41540-131">单击方法名称可以展开该部分。</span><span class="sxs-lookup"><span data-stu-id="41540-131">Click a method name to expand the section.</span></span> <span data-ttu-id="41540-132">添加所有必要的参数，然后单击“试试看!”。</span><span class="sxs-lookup"><span data-stu-id="41540-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![示例 Swagger GET 测试](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="41540-134">用于屏幕截图的 Swagger UI 版本是版本 2。</span><span class="sxs-lookup"><span data-stu-id="41540-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="41540-135">有关版本 3 的示例，请参阅 [Petstore 示例](https://petstore.swagger.io/)。</span><span class="sxs-lookup"><span data-stu-id="41540-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="41540-136">后续步骤</span><span class="sxs-lookup"><span data-stu-id="41540-136">Next steps</span></span>

* [<span data-ttu-id="41540-137">Swashbuckle 入门</span><span class="sxs-lookup"><span data-stu-id="41540-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="41540-138">NSwag 入门</span><span class="sxs-lookup"><span data-stu-id="41540-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
