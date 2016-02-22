服务器提供的远程方法
####################

.. module:: capi.webrtcclient

发起出方向呼叫
----------------

.. function:: make_call(sdp, from, to)

    :param str sdp: 客户端的 :term:`SDP` 字符串。
    :param str from: 呼叫发起者，格式为 :term:`SIP` URL。
    :param str to: 呼叫接收者，格式为 :term:`SIP` URL。
    :return: 呼叫 `ID`，对应 :attr:`sapi.Call.id` 属性
    :rtype: str

.. rubric:: 举例

**客户端调用：**

.. code-block:: json

    {
        "jsonrpc": "2.0",
        "id": "2372",
        "method": "capi.webrtcclient.make_call",
        "params": {
            "sdp": "v=0\r\no=user2 1413 2673 IN IP4 192.168.100.105\r\ns=Talk\r\nc=IN IP4 192.168.100.105\r\nt=0 0\r\na=ice-pwd:37605d4ae30266e29a893f3f\r\na=ice-ufrag:55cf1b68\r\na=rtcp-xr:rcvr-rtt=all:10000 stat-summary=loss,dup,jitt,TTL voip-metrics\r\nm=audio 7078 UDP/TLS/RTP/SAVPF 96 97 98 99 0 8 100 101 102 103\r\nc=IN IP4 119.32.243.210\r\na=rtpmap:96 opus/48000/2\r\na=fmtp:96 useinbandfec=1\r\na=rtpmap:97 SILK/16000\r\na=rtpmap:98 speex/16000\r\na=fmtp:98 vbr=on\r\na=rtpmap:99 speex/8000\r\na=fmtp:99 vbr=on\r\na=rtpmap:100 iLBC/8000\r\na=fmtp:100 mode=30\r\na=rtpmap:101 telephone-event/48000\r\na=rtpmap:102 telephone-event/16000\r\na=rtpmap:103 telephone-event/8000\r\na=setup:actpass\r\na=fingerprint:SHA-256 AC:4C:AA:6D:50:AF:CF:BA:31:4D:A0:22:50:DF:CA:E0:67:1B:D4:55:B2:1F:B9:7C:92:9F:DB:F6:78:90:53:AA\r\na=ssrc:4121415948 cname:sip:user2@sip.web2sip.hes86.net\r\na=candidate:1 1 UDP 2130706431 192.168.100.105 7078 typ host\r\na=candidate:1 2 UDP 2130706430 192.168.100.105 7079 typ host\r\na=candidate:2 1 UDP 1694498815 119.32.243.210 7078 typ srflx raddr 192.168.100.105 rport 7078\r\na=candidate:2 2 UDP 1694498814 119.32.243.210 7079 typ srflx raddr 192.168.100.105 rport 7079\r\na=rtcp-fb:* trr-int 5000",
            "from": "sip:40099998888@project1.sip.linkrtc.com",
            "to": "sip:13899922288@your_sip_provider.com"
        }
    }

**服务器回复：**

.. code-block:: json

    {
        "jsonrpc": "2.0",
        "id": "2372",
        "result": "f3kjfee"
    }

接受入方向呼叫
-----------------

.. function:: accept_call(call_id)

    :param str call_id: 要接受的呼叫 `ID`,对应 :attr:`sapi.Call.id` 属性

拒绝入方向呼叫
-----------------

.. function:: reject_call(call_id)

    :param str call_id: 要拒绝的呼叫 `ID`,对应 :attr:`sapi.Call.id` 属性

结束呼叫
-----------

由该客户端发起，或者交换到该客户端的任何呼叫，无论方向、状态，都被断开。

.. function:: drop_call(call_id)

    :param str call_id: 要结束的呼叫 `ID`,对应 :attr:`sapi.Call.id` 属性
