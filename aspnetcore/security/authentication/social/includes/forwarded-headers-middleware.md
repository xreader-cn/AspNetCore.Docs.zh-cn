## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>使用代理或负载均衡器转发请求信息

如果应用部署在代理服务器或负载均衡器后面，则可能会将某些原始请求信息转发到请求标头中的应用。 此信息通常包括安全请求方案 (`https`)、主机和客户端 IP 地址。 应用不会自动读取这些请求标头以发现和使用原始请求信息。

方案用于通过外部提供程序影响身份验证流的链接生成。 丢失安全方案 (`https`) 会导致应用生成不正确且不安全的重定向 URL。

使用转发标头中间件以使应用可以使用原始请求信息来进行请求处理。

有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。
