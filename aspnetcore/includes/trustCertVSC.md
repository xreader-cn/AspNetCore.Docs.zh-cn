* <span data-ttu-id="a7a1f-101">通过运行以下命令来信任 HTTPS 开发证书：</span><span class="sxs-lookup"><span data-stu-id="a7a1f-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```console
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="a7a1f-102">上述命令在 Linux 上无效。</span><span class="sxs-lookup"><span data-stu-id="a7a1f-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="a7a1f-103">有关信任证书的详细信息，请参阅 Linux 发行版的文档。</span><span class="sxs-lookup"><span data-stu-id="a7a1f-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="a7a1f-104">以上命令会显示以下对话：</span><span class="sxs-lookup"><span data-stu-id="a7a1f-104">The preceding command displays the following dialog:</span></span>

  ![安全警告对话](~/getting-started/_static/cert.png)

* <span data-ttu-id="a7a1f-106">如果你同意信任开发证书，请选择“是”。 </span><span class="sxs-lookup"><span data-stu-id="a7a1f-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="a7a1f-107">有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。</span><span class="sxs-lookup"><span data-stu-id="a7a1f-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
