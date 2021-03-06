.. _label-notify-api:

通知 API
##########

:term:`LinkRTC` 后台服务通过通知 API 将通知类型的消息数据 :http:method:`POST` 到用户的应用服务程序。

.. digraph:: notify_api

    a [label="api.linkrtc.com"]
    b [label="http://your.app.com/your/script.php"]

    a -> b [label="POST"]

用户应用服务程序通过这些通知数据判断呼叫或者客户端的状态，以便对其进行控制。

接收通知消息的 `URL` 通过 `用户控制台 <http://console.linkrtc.com/>`_ 设置。

.. attention::

    在通知 API 中， :term:`LinkRTC` 作为 `HTTP` 客户端，用户应用服务程序作为 `HTTP` 服务器。

    所以： **开发者的 Web 服务程序需要向**
    :term:`LinkRTC`
    **提供访问地址！**

通知消息的内容是 :term:`JSON` 对象格式。

该 :term:`JSON` 对象的最外层包括两个属性：
    * `type`: 消息类型字符串。
    * `data`: 消息参数，其数据类型随事件类型不同而各异。

例如：

.. code-block:: json

    {
        "type": "call.CallStateChanged",
        "data": {
            "call": {
                "id": "2341",
                "dir": "incoming",
                "from": "13283484795",
                "to": "400666255343",
                "...": "... ..."
            }
        }
    }

本节文档使用面向对象语言类型定义的伪代码形式，描述通知消息 `data` 部分的数据结构。

.. module:: sapi.event

呼叫状态变化
==============

:type: ``event.CallStateChanged``

.. class:: CallStateChanged

  .. attribute:: call

    状态发生变化的呼叫

    :rtype: sapi.Call

.. rubric:: 举例:

`ID` 为 `2341` 的入方向呼叫产生：

.. code-block:: http

    POST /your/script.php HTTP/1.1
    Host: your.company.com
    Content-Type: application/json; charset=utf-8
    Content-Length: xxx
    X-LinkRTC-Timestamp: 1453543759
    X-LinkRTC-Signature: E6E157A9FA805921DA12A86A40CC2A15

    {
        "type": "event.CallStateChanged",
        "data": {
            "call": {
                "id": "2341",
                "dir": "incoming",
                "current_state": "pending",
                "...": "... ..."
            }
        }
    }

.. warning::

    当用户应用服务程序收到呼叫状态变化事件通知中，
    `data` 部分的 :class:`sapi.Call` 对象的当前状态属性 :attr:`sapi.Call.current_state` 值为 `待定` (``pending``) ，
    且呼叫方向属性 :attr:`sapi.Call.dir` 值为 `出方向` (``outgoing``) 时，
    用户应用服务程序需要在呼叫超时或者被放弃之前调用
    :http:post:`/v0.1/sapi/call/(str:call_id)/allow`
    允许此次呼叫，方可使出方向呼叫继续进行。


    同理，当 `data` 部分的 :class:`sapi.Call` 对象的当前状态属性 :attr:`sapi.Call.current_state` 值为 `待定` (``pending``) ，
    且呼叫方向属性 :attr:`sapi.Call.dir` 值为 `入方向` (``incoming``) 时，
    用户应用服务程序需要在呼叫超时或者被放弃之前调用
    :http:post:`/v0.1/sapi/call/(str:call_id)/switch`
    允许此次呼叫，方可使入方向呼叫继续进行。



:term:`WebRTC` 客户端连接状态变化
===================================

:term:`WebRTC` 客户端连接建立或者连接断开

:type: ``event.WebRtcClientConnectStateChanged``

.. class:: WebRtcClientConnected

    .. attribute:: connected

        * ``true``: 连接建立
        * ``false``: 连接断开

        :rtype: bool

    .. attribute:: client

        连接状态发生变化的客户端

        :rtype: sapi.WebRtcClient

.. rubric:: 举例:

`ID` 为 `sx3kerjs` 的 :term:`WebRTC` 客户端建立连接:

.. code-block:: http

    POST /your/script.php HTTP/1.1
    Host: your.company.com
    Content-Type: application/json; charset=utf-8
    Content-Length: xxx
    X-LinkRTC-Timestamp: 1453543759
    X-LinkRTC-Signature: E6E157A9FA805921DA12A86A40CC2A15

    {
        "type": "event.WebRtcClientConnectStateChanged",
        "data": {
            "connected": true,
            "client": {
                "id": "sx3kerjs",
                "..": "....",
                "...": "... ..."
            }
        }
    }
