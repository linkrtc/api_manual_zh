:term:`WebRTC` 客户端远提供的程方法
####################################

.. module:: user.webrtcclient

呼叫状态变化通知
----------------

.. function:: on_call_state_changed(call)

    :param sapi.Call call: 状态变化后的 `Call` 对象。

.. attention:: 该 `RPC` 是通知性质的方法。如果服务器发送的调用数据中没有提供 `id` 属性，则客户端不必进行回复。

.. rubric:: 举例

**服务器调用：**

.. code-block:: json

    {
        "jsonrpc": "2.0",
        "method": "user.webrtcclient.on_call_state_changed",
        "params": [{
        	"id": "f3kjfee",
        	"dir": "outgoing",
        	"prior_state": "pending",
        	"curr_state": "calling",
        	"...": "... ..."
        }]
    }
