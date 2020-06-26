* `-F|--no-formatting`

  <span data-ttu-id="27bec-101">禁用 HTTP 响应格式的标志。</span><span class="sxs-lookup"><span data-stu-id="27bec-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="27bec-102">设置 HTTP 请求标头。</span><span class="sxs-lookup"><span data-stu-id="27bec-102">Sets an HTTP request header.</span></span> <span data-ttu-id="27bec-103">支持以下两种值格式：</span><span class="sxs-lookup"><span data-stu-id="27bec-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="27bec-104">指定一个文件，整个 HTTP 响应（包括标头和正文）应写入该文件。</span><span class="sxs-lookup"><span data-stu-id="27bec-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="27bec-105">例如 `--response "C:\response.txt"`。</span><span class="sxs-lookup"><span data-stu-id="27bec-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="27bec-106">如果文件不存在，则创建该文件。</span><span class="sxs-lookup"><span data-stu-id="27bec-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="27bec-107">指定一个文件，HTTP 响应正文应写入该文件。</span><span class="sxs-lookup"><span data-stu-id="27bec-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="27bec-108">例如 `--response:body "C:\response.json"`。</span><span class="sxs-lookup"><span data-stu-id="27bec-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="27bec-109">如果文件不存在，则创建该文件。</span><span class="sxs-lookup"><span data-stu-id="27bec-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="27bec-110">指定一个文件，HTTP 响应标头应写入该文件。</span><span class="sxs-lookup"><span data-stu-id="27bec-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="27bec-111">例如 `--response:headers "C:\response.txt"`。</span><span class="sxs-lookup"><span data-stu-id="27bec-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="27bec-112">如果文件不存在，则创建该文件。</span><span class="sxs-lookup"><span data-stu-id="27bec-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="27bec-113">支持 HTTP 响应流式处理的标志。</span><span class="sxs-lookup"><span data-stu-id="27bec-113">A flag whose presence enables streaming of the HTTP response.</span></span>
