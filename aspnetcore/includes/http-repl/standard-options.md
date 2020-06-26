* `-F|--no-formatting`

  禁用 HTTP 响应格式的标志。

* `-h|--header`

  设置 HTTP 请求标头。 支持以下两种值格式：

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  指定一个文件，整个 HTTP 响应（包括标头和正文）应写入该文件。 例如 `--response "C:\response.txt"`。 如果文件不存在，则创建该文件。

* `--response:body`

  指定一个文件，HTTP 响应正文应写入该文件。 例如 `--response:body "C:\response.json"`。 如果文件不存在，则创建该文件。

* `--response:headers`

  指定一个文件，HTTP 响应标头应写入该文件。 例如 `--response:headers "C:\response.txt"`。 如果文件不存在，则创建该文件。

* `-s|--streaming`

  支持 HTTP 响应流式处理的标志。
