---
title: 带有 Swagger/OpenAPI 的 ASP.NET Core Web API 帮助页
author: rsuter
description: 本教程提供添加 Swagger 以生成文档的演练和针对 Web API 应用的帮助页。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/20/2018
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: fb65b84b781b5c19040b559f0d7635ceb00cf4a0
ms.sourcegitcommit: 06a455d63ff7d6b571ca832e8117f4ac9d646baf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316568"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>带有 Swagger/OpenAPI 的 ASP.NET Core Web API 帮助页

作者：[Christoph Nienaber](https://twitter.com/zuckerthoben) 和 [Rico Suter](http://rsuter.com)

使用 Web API 时，了解其各种方法对开发人员来说可能是一项挑战。 [Swagger](https://swagger.io/) 也称为[OpenAPI](https://www.openapis.org/)，解决了为 Web API 生成有用文档和帮助页的问题。 它具有诸如交互式文档、客户端 SDK 生成和 API 可发现性等优点。

本文展示了 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 和 [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger 实现：

* **Swashbuckle.AspNetCore** 是一个开源项目，用于生成 ASP.NET Core Web API 的 Swagger 文档。

* **NSwag** 是另一个用于生成 Swagger 文档并将 [Swagger UI](https://swagger.io/swagger-ui/) 或 [ReDoc](https://github.com/Rebilly/ReDoc) 集成到 ASP.NET Core Web API 中的开源项目。 此外，NSwag 还提供了为 API 生成 C# 和 TypeScript 客户端代码的方法。

## <a name="what-is-swagger--openapi"></a>什么是 Swagger/OpenAPI？

Swagger 是一个与语言无关的规范，用于描述 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API。 Swagger 项目已捐赠给 [OpenAPI 计划](https://www.openapis.org/)，现在它被称为开放 API。 这两个名称可互换使用，但 OpenAPI 是首选。 它允许计算机和人员了解服务的功能，而无需直接访问实现（源代码、网络访问、文档）。 其中一个目标是尽量减少连接取消关联的服务所需的工作量。 另一个目标是减少准确记录服务所需的时间。

## <a name="swagger-specification-swaggerjson"></a>Swagger 规范 (swagger.json)

Swagger 流的核心是 Swagger 规范，默认情况下是名为 swagger.json 的文档  。 它由 Swagger 工具链（或其第三方实现）根据你的服务生成。 它描述了 API 的功能以及使用 HTTP 对其进行访问的方式。 它驱动 Swagger UI，并由工具链用来启用发现和客户端代码生成。 下面是为简洁起见而缩减的 Swagger 规范的示例：

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a>Swagger UI

[Swagger UI](https://swagger.io/swagger-ui/) 提供了基于 Web 的 UI，它使用生成的 Swagger 规范提供有关服务的信息。 Swashbuckle 和 NSwag 均包含 Swagger UI 的嵌入式版本，因此可使用中间件注册调用将该嵌入式版本托管在 ASP.NET Core 应用中。 Web UI 如下所示：

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

控制器中的每个公共操作方法都可以从 UI 中进行测试。 单击方法名称可以展开该部分。 添加所有必要的参数，然后单击“试试看!”  。

![示例 Swagger GET 测试](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> 用于屏幕截图的 Swagger UI 版本是版本 2。 有关版本 3 的示例，请参阅 [Petstore 示例](http://petstore.swagger.io/)。

## <a name="next-steps"></a>后续步骤

* [Swashbuckle 入门](xref:tutorials/get-started-with-swashbuckle)
* [NSwag 入门](xref:tutorials/get-started-with-nswag)
