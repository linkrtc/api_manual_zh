介绍
###########

客户端 API 用于 :term:`LinkRTC` 后台与具有 :term:`WebRTC` 功能的浏览器/客户端之间的通信。

它们之间的通信基于 :term:`WebSocket` ，遵照 :term:`JSON-RPC` 2.0 标准。

在浏览器上，开发者可以通过 `JavaScript` 脚本使用这些 API 。

:term:`LinkRTC` 与 浏览器/客户端 **既充当调用者，用充当被调用者** ，所以，它们之间的通信是双向的。

设计原则
==========

1. 使用经过SSL/TLS加密的 :term:`WebRTC` (即 `wss`)建立连接。
2. 采用远程方法调用通信模式，遵照 :term:`JSON-RPC` 2.0 标准，但是不支持批处理模式。
3. 双方均可双向调用。
4. 服务器同一时间只响应一个客户端的一个调用。

格式规范
==========

URL
-----

:term:`WebRTC` 客户端访问这个 URL 建立 :term:`WebSocket` 连接：

.. http:get:: /v0.1/capi/(str:client_id)

    为名为 `ID` 为 `client_id` 的客户端建立基于 :term:`WebSocket` 的 :term:`JSON-RPC` 连接。

    用户应用程序服务器通过 :term:`WebRTC` 客户端对象 :attr:`sapi.WebRtcClient.id` 属性获取该 `ID`。

.. note::
  * 路径的第一部分 `v0.1` 是 :term:`LinkRTC` API 的版本，跨版本访问可能有兼容问题。
  * `capi` 表示客户端程序接口（ `Client Application Program Interface` ）。

.. rubric:: 举例

在浏览器上使用 `JavaScript` 建立 :term:`WebSocket` 连接：

.. code-block:: js

    var projId = "your_project_sid";
    var wsKey = "your_client_wskey";
    var url = `wss://api.linkrtc.com/v0.1/capi/${projId}?id=${wsKey}`;
    var wsRpc = new WebSocket(url);
    /**
    * RPC's response
    */
    wsRpc.addEventListener('onmessage', (event) => {
        var data = JSON.parse(event.data);
        /// "id" is RPC's id
        id = data.id;
        /// "result" is RPC's returned result
        result = data.result;
    });
    wsRpc.addEventListener("readyState", (state) => {
        if (state == 0) {
            console.log("CONNECTING");
        } else if (state == 1) {
            console.log("OPEN");
            /**
            * Send JSON-RPC
            */
            wsRpc.send(JSON.stringify({
              jsonrpc: '2.0',
              id: 'Unique-RPC-ID',
              method: 'name_of_method',
              params: ['param0', 'param1', 'param2', 'param3'],
            }))
        } else if (state == 2) {
            console.log("CLOSING");
        } else if (state == 3) {
            console.log("CLOSED");
        }
    });

内容
-----
远程方法的调用和回复内容以 :term:`JSON` 格式在 :term:`WebSocket` 通道上传输，
具体内容请参考 :term:`JSON-RPC` 2.0 标准。

定义
-----
在下面的章节中，以函数定义的形式描述 :term:`JSON-RPC` 的接口定义。
