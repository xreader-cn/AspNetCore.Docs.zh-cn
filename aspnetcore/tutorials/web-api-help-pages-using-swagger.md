---
title: 带有 Swagger/OpenAPI 的 ASP.NET Core Web API 文档
author: RicoSuter
description: 本教程提供添加 Swagger 以生成文档的演练和针对 Web API 应用的帮助页。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062449"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="20d8b-103">带有 Swagger/OpenAPI 的 ASP.NET Core Web API 文档</span><span class="sxs-lookup"><span data-stu-id="20d8b-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="20d8b-104">作者：[Christoph Nienaber](https://twitter.com/zuckerthoben) 和 [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="20d8b-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="20d8b-105">Swagger (OpenAPI) 是一个与语言无关的规范，用于描述 REST API。</span><span class="sxs-lookup"><span data-stu-id="20d8b-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="20d8b-106">它使计算机和用户无需直接访问源代码即可了解 REST API 的功能。</span><span class="sxs-lookup"><span data-stu-id="20d8b-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="20d8b-107">其主要目标是：</span><span class="sxs-lookup"><span data-stu-id="20d8b-107">Its main goals are to:</span></span>

* <span data-ttu-id="20d8b-108">尽量减少连接分离的服务所需的工作量。</span><span class="sxs-lookup"><span data-stu-id="20d8b-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="20d8b-109">减少准确记录服务所需的时间。</span><span class="sxs-lookup"><span data-stu-id="20d8b-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="20d8b-110">.NET 的两个主要 OpenAPI 实现是 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 和 [NSwag](https://github.com/RicoSuter/NSwag)，请参阅：</span><span class="sxs-lookup"><span data-stu-id="20d8b-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="20d8b-111">Swashbuckle 入门</span><span class="sxs-lookup"><span data-stu-id="20d8b-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="20d8b-112">NSwag 入门</span><span class="sxs-lookup"><span data-stu-id="20d8b-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="20d8b-113">OpenAPI 与Swagger</span><span class="sxs-lookup"><span data-stu-id="20d8b-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="20d8b-114">Swagger 项目已于 2015 年捐赠给 OpenAPI 计划，自此它被称为 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="20d8b-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="20d8b-115">这两个名称可互换使用。</span><span class="sxs-lookup"><span data-stu-id="20d8b-115">Both names are used interchangeably.</span></span> <span data-ttu-id="20d8b-116">不过，“OpenAPI”指的是规范。</span><span class="sxs-lookup"><span data-stu-id="20d8b-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="20d8b-117">“Swagger”指的是来自使用 OpenAPI 规范的 SmartBear 的开放源代码和商业产品系列。</span><span class="sxs-lookup"><span data-stu-id="20d8b-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="20d8b-118">后续开放源代码产品（如 [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator)）也属于 Swagger 系列名称，尽管 SmartBear 未发布也是如此。</span><span class="sxs-lookup"><span data-stu-id="20d8b-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="20d8b-119">简而言之：</span><span class="sxs-lookup"><span data-stu-id="20d8b-119">In short:</span></span>

* <span data-ttu-id="20d8b-120">OpenAPI 是一种规范。</span><span class="sxs-lookup"><span data-stu-id="20d8b-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="20d8b-121">Swagger 是一种使用 OpenAPI 规范的工具。</span><span class="sxs-lookup"><span data-stu-id="20d8b-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="20d8b-122">例如，OpenAPIGenerator 和 SwaggerUI。</span><span class="sxs-lookup"><span data-stu-id="20d8b-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="20d8b-123">OpenAPI 规范 (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="20d8b-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="20d8b-124">OpenAPI 规范是描述 API 功能的文档。</span><span class="sxs-lookup"><span data-stu-id="20d8b-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="20d8b-125">该文档基于控制器和模型中的 XML 和属性注释。</span><span class="sxs-lookup"><span data-stu-id="20d8b-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="20d8b-126">它是 OpenAPI 流的核心部分，用于驱动诸如 SwaggerUI 之类的工具。</span><span class="sxs-lookup"><span data-stu-id="20d8b-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="20d8b-127">默认情况下，它命名为 openapi.json。</span><span class="sxs-lookup"><span data-stu-id="20d8b-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="20d8b-128">下面是为简洁起见而缩减的 OpenAPI 规范的示例：</span><span class="sxs-lookup"><span data-stu-id="20d8b-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="20d8b-129">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="20d8b-129">Swagger UI</span></span>

<span data-ttu-id="20d8b-130">[Swagger UI](https://swagger.io/swagger-ui/) 提供了基于 Web 的 UI，它使用生成的 OpenAPI 规范提供有关服务的信息。</span><span class="sxs-lookup"><span data-stu-id="20d8b-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="20d8b-131">Swashbuckle 和 NSwag 均包含 Swagger UI 的嵌入式版本，因此可使用中间件注册调用将该嵌入式版本托管在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="20d8b-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="20d8b-132">Web UI 如下所示：</span><span class="sxs-lookup"><span data-stu-id="20d8b-132">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="20d8b-134">控制器中的每个公共操作方法都可以从 UI 中进行测试。</span><span class="sxs-lookup"><span data-stu-id="20d8b-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="20d8b-135">选择方法名称可以展开该部分。</span><span class="sxs-lookup"><span data-stu-id="20d8b-135">Select a method name to expand the section.</span></span> <span data-ttu-id="20d8b-136">添加所有必要的参数，然后选择“试试看!”。</span><span class="sxs-lookup"><span data-stu-id="20d8b-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![示例 Swagger GET 测试](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="20d8b-138">用于屏幕截图的 Swagger UI 版本是版本 2。</span><span class="sxs-lookup"><span data-stu-id="20d8b-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="20d8b-139">有关版本 3 的示例，请参阅 [Petstore 示例](https://petstore.swagger.io/)。</span><span class="sxs-lookup"><span data-stu-id="20d8b-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="20d8b-140">后续步骤</span><span class="sxs-lookup"><span data-stu-id="20d8b-140">Next steps</span></span>

* [<span data-ttu-id="20d8b-141">Swashbuckle 入门</span><span class="sxs-lookup"><span data-stu-id="20d8b-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="20d8b-142">NSwag 入门</span><span class="sxs-lookup"><span data-stu-id="20d8b-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
