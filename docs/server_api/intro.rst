服务器 API 介绍
##################

服务器 API 用于 :term:`LinkRTC` 后台与用户的应用服务程序之间的通信。

它们之间的通信以 `HTTP` 的形式在互联网上进行。

:term:`LinkRTC` 与 用户的应用服务程序 **既充当 HTTP 服务器又充当 HTTP 客户端** ：
以客户端的身份向对方发送请求，同时也以服务器的身份接受对方的请求。

.. digraph:: server_http

  linkrtc [label="api.linkrtc.com"]
  userapp [label="用户应用程序服务"]

  linkrtc -> userapp [label="HTTP or HTTPS" color=blue fontcolor=blue]
  userapp -> linkrtc [label="force HTTPS" color=green fontcolor=green]

设计原则
==========

1. 基于 :term:`HTTP 1.1`。
2. 双方互为 `HTTP` 服务器/客户端，双向访问。
3. 采用 :term:`Restful` 形态的 `Web API`。
4. 消息提使用 :term:`UTF-8` 编码的 :term:`JSON` 格式文本记录结构化数据。
5. :term:`LinkRTC` 只接受 :term:`HTTPS` 加密连接； :term:`LinkRTC` 向用户应用程序服务发送请求时允许使用不加密的 `HTTP` 连接。
6. 用户应用服务程序向 :term:`LinkRTC` 发送的请求需要通过 :term:`HTTP Basic Authentication` （`HTTP` 基本认证）。
7. :term:`LinkRTC` 向用户应用服务程序发送的请求带有消息签名。

格式规范
==========

HTTP 地址
--------------
用户应用服务程序调用 :term:`LinkRTC` 的 API 时候，URL 的路径部分的格式是::

  /<api_version>/sapi/*

其中 `api_version` 是 :term:`LinkRTC` API 的版本，跨版本访问可能有兼容问题。

`sapi` 表示服务应用程序接口（ `Service Application Program Interface` ），所有的服务器API的访问路径均具有该部分。

HTTP 头和内容
--------------

普通请求与回复
```````````````
`POST` 或 `PUT` 请求、以及回复的正文部分 **必须** 是
采用 :term:`UTF-8` 编码的 :term:`JSON` 格式字符串，
:http:header:`Content-Type` 头域的值应是 `application/json`。

例如：

用户应用程序服务发出的请求：

.. code-block:: http

  POST /v0.l/sapi/webrtcclient/create HTTP/1.1
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
    "wskey": "fg430mu3ojfg398u4",
    "expires": 3600
  }

空请求与回复
`````````````
如果 :http:method:`POST` 、 :http:method:`PUT` 请求不包含内容，或回复不包含内容，
其 :http:header:`Content-Length` 头域的值应为 `0`，例如：

请求：

.. code-block:: http

  POST /v0.1/sapi/ping HTTP/1.1
  Host: api.linkrtc.com
  Content-Length: 0

回复：

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Length: 0

HTTP 状态码
--------------

:http:statuscode:`200`
````````````````````````
如果API调用成功，被调用方应返回状态码 `200 OK` 。

:http:statuscode:`401`
````````````````````````
如果 :term:`LinkRTC` 收到的服务器 `API` 请求中，没有正确的身份验证信息，就返回这个状态码。

参见 :ref:`label-auth`

:http:statuscode:`500`
``````````````````````````
如果服务器在响应API调用期间出现错误，或者出现意料之外的情况，应返回该状态码。

:term:`LinkRTC` 在许多情况下，会提供具体的错误编码以及错误信息，这些错误信息用 :term:`JSON` 对象格式存放在回复数据的内容部分。
其中 ``code`` 属性记录错误编码， ``text`` 属性记录错误文本信息。

如：

.. code-block:: http

  HTTP/1.1 500 Internal Server Error
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {"code": 10013, "text": "calee not allowed"}

.. attention::
  :term:`LinkRTC` 后台服务无法在所有情况下都提供 :term:`JSON` 格式错误信息。
  调用方可以根据 :http:header:`Content-Type` 进行判断。

其它
`````````````
其它 `Status Code` 均遵照 `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html>`_ 的定义
