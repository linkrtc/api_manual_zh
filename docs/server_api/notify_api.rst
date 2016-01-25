.. _label-notify-api:

通知 API
##########

:term:`LinkRTC` 后台服务通过通知 API 将通知类型的消息数据 :http:method:`POST` 到用户的应用服务程序。

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

.. sourcecode:: json

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

在本节文档中，使用类似 `Python` 语言类型定义的形式，描述通知消息的 `data` 部分。

.. module:: sapi.event

呼叫状态变化
==============

:type: ``event.CallStateChanged``

.. class:: CallStateChanged

    .. attribute:: call

        状态发生变化的呼叫

        :datatype: :class:`sapi.Call`               

.. rubric:: 举例:

`ID` 为 `2341` 的入方向呼叫产生：

.. sourcecode:: http

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

:term:`WebRTC` 客户端连接状态变化
===================================

:term:`WebRTC` 客户端连接建立或者连接断开

:type: ``event.WebRtcClientConnectStateChanged``

.. class:: WebRtcClientConnected

    .. attribute:: connected

        * ``true``: 连接建立
        * ``false``: 连接断开

        :datatype: ``bool``

    .. attribute:: client

        连接状态发生变化的客户端

        :datatype: :class:`sapi.WebRtcClient`

.. rubric:: 举例:

`ID` 为 `sx3kerjs` 的 :term:`WebRTC` 客户端建立连接:

.. sourcecode:: http

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