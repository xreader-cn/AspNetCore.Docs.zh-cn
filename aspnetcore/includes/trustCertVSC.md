---
ms.openlocfilehash: a9bdff481b1a72a9ee19f4e51fada177530c0cbb
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59472330"
---
*  <span data-ttu-id="7de0d-101">通过运行以下命令来信任 HTTPS 开发证书：</span><span class="sxs-lookup"><span data-stu-id="7de0d-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```console
    dotnet dev-certs https --trust
    ```

    <span data-ttu-id="7de0d-102">以上命令会显示以下对话：</span><span class="sxs-lookup"><span data-stu-id="7de0d-102">The preceding command displays the following dialog:</span></span>

    ![安全警告对话](~/getting-started/_static/cert.png)

*    <span data-ttu-id="7de0d-104">如果你同意信任开发证书，请选择“是”。</span><span class="sxs-lookup"><span data-stu-id="7de0d-104">Select **Yes** if you agree to trust the development certificate.</span></span>

     <span data-ttu-id="7de0d-105">有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。</span><span class="sxs-lookup"><span data-stu-id="7de0d-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>