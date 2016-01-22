##################
服务器 API 介绍
##################

服务器 API 用于 :term:`LinkRTC` 后台与用户的应用服务程序之间的通信。

它们之间的通信以 `HTTP` 的形式在互联网上进行，它们

:term:`LinkRTC` 与 用户的应用服务程序 **既是HTTP服务器又是客户端** ：
以客户端的身份向对方发送请求，同时也以服务器的身份接受对方的请求。

.. digraph:: server_http

  linkrtc [label="api.linkrtc.com"]
  userapp [label="用户应用程序服务"]

  linkrtc -> userapp [label="HTTP or HTTPS" color=blue fontcolor=blue]
  userapp -> linkrtc [label="force HTTPS" color=green fontcolor=green]

==========
设计原则
==========

1. 基于 :term:`HTTP 1.1`。
2. 双方互为 `HTTP` 服务器/客户端，双向访问。
3. 采用 :term:`Restful` 形态的 `Web API`。
4. 消息提使用 :term:`UTF-8` 编码的 :term:`JSON` 格式文本记录结构化数据。
5. :term:`LinkRTC` 只接受 :term:`HTTPS` 加密连接； :term:`LinkRTC` 向用户应用程序服务发送请求时允许使用不加密的 `HTTP` 连接。
6. 对请求消息进行签名验证。

==========
格式规范
==========

--------------
HTTP 头和内容
--------------

```````````````
普通请求与回复
```````````````
`POST` 或 `PUT` 请求、以及回复的正文部分 **必须** 是
采用 :term:`UTF-8` 编码的 :term:`JSON` 格式字符串，
`Content-Type` 头域的值应是 `application/json`。

例如：

用户应用程序服务发出的请求：

.. code-block:: http

  POST /api/account/tom/app/tomsapp/client HTTP/1.1
  Host: api.linkrtc.com
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {
    "capability": {
      "audio": true,
      "video": false
    },
    "userData": "client-01"
  }

`api.linkrtc.com` 的回复：

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {
    "id": "5",
    "token": "fg430mu3ojfg398u4",
    "expires": 3600
  }

`````````````
空请求与回复
`````````````
如果 `POST` 、 `PUT` 请求不包含内容，或回复不包含内容，其 `Content-Length` 头域的值应为 `0`，例如：

请求：

.. code-block:: http

  POST /api/account/tom/app/tomsapp/ping HTTP/1.1
  Host: api.linkrtc.com
  Content-Length: 0

回复：

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Length: 0

--------------
HTTP 状态码
--------------

`````````````
200 执行成功
`````````````
如果API调用成功，被调用方应返回状态码 `200 OK` 。

`````````````
500 执行失败
`````````````
如果服务器在响应API调用期间出现错误，或者出现意料之外的情况，应返回 `500 Internal Server Error`。

:term:`LinkRTC` 在许多情况下，会提供具体的错误编码以及错误信息，这些错误信息用 :term:`JSON` 对象格式存放在回复数据的内容部分。
其中 ``code`` 属性记录错误编码， ``text`` 属性记录错误文本信息。

如：

.. code-block:: http

  HTTP/1.1 500 Internal Server Error
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {"code": 10013, "text": "calee not allowed"}

.. attention::
  :term:`LinkRTC` 后台服务无法在所有情况下都提供 :term:`JSON` 格式错误信息，调用方可以根据 `Content-Type` 进行判断。

`````````````
其它
`````````````
其它 `Status Code` 均遵照 `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html>`_ 的定义
