---
title: 带有 Swagger/OpenAPI 的 ASP.NET Core Web API 文档
author: RicoSuter
description: 本教程提供添加 Swagger 以生成文档的演练和针对 Web API 应用的帮助页。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- appsettings.json
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062449"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>带有 Swagger/OpenAPI 的 ASP.NET Core Web API 文档

作者：[Christoph Nienaber](https://twitter.com/zuckerthoben) 和 [Rico Suter](https://blog.rsuter.com/)

Swagger (OpenAPI) 是一个与语言无关的规范，用于描述 REST API。 它使计算机和用户无需直接访问源代码即可了解 REST API 的功能。 其主要目标是：

* 尽量减少连接分离的服务所需的工作量。
* 减少准确记录服务所需的时间。

.NET 的两个主要 OpenAPI 实现是 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 和 [NSwag](https://github.com/RicoSuter/NSwag)，请参阅：

* [Swashbuckle 入门](xref:tutorials/get-started-with-swashbuckle)
* [NSwag 入门](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenAPI 与Swagger

Swagger 项目已于 2015 年捐赠给 OpenAPI 计划，自此它被称为 OpenAPI。 这两个名称可互换使用。 不过，“OpenAPI”指的是规范。 “Swagger”指的是来自使用 OpenAPI 规范的 SmartBear 的开放源代码和商业产品系列。 后续开放源代码产品（如 [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator)）也属于 Swagger 系列名称，尽管 SmartBear 未发布也是如此。

简而言之：

* OpenAPI 是一种规范。
* Swagger 是一种使用 OpenAPI 规范的工具。 例如，OpenAPIGenerator 和 SwaggerUI。

## <a name="openapi-specification-openapijson"></a>OpenAPI 规范 (openapi.json)

OpenAPI 规范是描述 API 功能的文档。 该文档基于控制器和模型中的 XML 和属性注释。 它是 OpenAPI 流的核心部分，用于驱动诸如 SwaggerUI 之类的工具。 默认情况下，它命名为 openapi.json。 下面是为简洁起见而缩减的 OpenAPI 规范的示例：

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

## <a name="swagger-ui"></a>Swagger UI

[Swagger UI](https://swagger.io/swagger-ui/) 提供了基于 Web 的 UI，它使用生成的 OpenAPI 规范提供有关服务的信息。 Swashbuckle 和 NSwag 均包含 Swagger UI 的嵌入式版本，因此可使用中间件注册调用将该嵌入式版本托管在 ASP.NET Core 应用中。 Web UI 如下所示：

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

控制器中的每个公共操作方法都可以从 UI 中进行测试。 选择方法名称可以展开该部分。 添加所有必要的参数，然后选择“试试看!”。

![示例 Swagger GET 测试](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> 用于屏幕截图的 Swagger UI 版本是版本 2。 有关版本 3 的示例，请参阅 [Petstore 示例](https://petstore.swagger.io/)。

## <a name="next-steps"></a>后续步骤

* [Swashbuckle 入门](xref:tutorials/get-started-with-swashbuckle)
* [NSwag 入门](xref:tutorials/get-started-with-nswag)
