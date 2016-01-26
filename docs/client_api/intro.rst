介绍
###########

客户端 API 用于 :term:`LinkRTC` 后台与具有 :term:`WebRTC` 功能的浏览器/客户端之间的通信。

它们之间的通信基于 :term:`WebSocket` ，遵照 :term:`JSON-RPC` 2.0 标准。

在浏览器上，开发者可以通过 `JavaScript` 脚本使用这些 API 。

:term:`LinkRTC` 与 浏览器/客户端 **既充当调用者，用充当被调用者** ，所以，它们之间的通信是双向的。

设计原则
==========

1. 使用经过SSL/TLS加密的 :term:`WebRTC` (即 `wss`)建立连接。
2. 采用远程方法调用通信模式，遵照 :term:`JSON-RPC` 2.0 标准。
3. 双方均可双向调用。

格式规范
==========

URL
-----

:term:`WebRTC` 客户端访问这个 URL 建立 :term:`WebSocket` 连接：

.. http:get:: /capi/project/(str:project_id)/rpc

    `project_id` :term:`WebRTC` 所属 :ref:`项目<label-account-project>` 的 `SID`

    :query str key: 客户端连接密钥。
        用户应用程序服务器通过 :term:`WebRTC` 客户端对象 :attr:`sapi.WebRtcClient.wskey` 属性获取该密钥。

.. rubric:: 举例

在浏览器上使用 `JavaScript` 建立 :term:`WebSocket` 连接：

.. code-block:: js

    var projId = "your_project_sid";
    var wsKey = "your_client_wskey";
    var url = `wss://api.linkrtc.com/capi/project/${projId}/rpc?key=${wsKey}`;
    var wsRpc = new WebSocket(url);
    wsRpc.addEventListener("readyState", (state) => {
        if (state == 0) {
            console.log("CONNECTING");
        } else if (state == 1) {
            console.log("OPEN");
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
