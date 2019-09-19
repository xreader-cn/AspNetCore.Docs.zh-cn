* <span data-ttu-id="a6f27-101">通过运行以下命令来信任 HTTPS 开发证书：</span><span class="sxs-lookup"><span data-stu-id="a6f27-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```dotnetcli
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="a6f27-102">上面的命令显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="a6f27-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="a6f27-103">在系统提示时输入管理员用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="a6f27-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="a6f27-104">此时会安装并信任证书。</span><span class="sxs-lookup"><span data-stu-id="a6f27-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="a6f27-105">有关详细信息，请参阅[信任 ASP.NET Core HTTPS 开发证书](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。</span><span class="sxs-lookup"><span data-stu-id="a6f27-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>