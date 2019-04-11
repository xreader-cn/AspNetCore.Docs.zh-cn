---
ms.openlocfilehash: a9bdff481b1a72a9ee19f4e51fada177530c0cbb
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472330"
---
*  通过运行以下命令来信任 HTTPS 开发证书：

    ```console
    dotnet dev-certs https --trust
    ```

    以上命令会显示以下对话：

    ![安全警告对话](~/getting-started/_static/cert.png)

*    如果你同意信任开发证书，请选择“是”。

     有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。