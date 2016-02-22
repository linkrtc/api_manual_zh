WebRTC 客户端管理 API
######################

获取客户端列表
===============

.. http:post:: /v0.1/sapi/webrtcclient/list

  获取 :class:`sapi.WebRtcClient` 实例列表

  :<json int page: 要返回的页码。如果不指定，默认为1（从1开始）。
  :<json int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>json int currentPage: 当前返回结果的页码（从1开始）。
  :>json int perPage: 每页长度。
  :>json int totalPages: 总页数。
  :>json int totalEntries: 总条目数。
  :>json list entries: :class:`sapi.WebRtcClient` 对象列表。

.. rubric:: 举例

**客户端调用：**

.. code-block:: http

  POST /v0.1/sapi/webrtcclient/list HTTP/1.1
  Host: api.linkrtc.com
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {
    "page": 1,
    "perPage": 20
  }

**服务器回复：**

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {
    "currentPage": 1,
    "perPage": 20,
    "totalPages": 3,
    "totalEntries": 57,
    "entries": [
      {"..": "...", "..": "...", "..": "..."},
      {"..": "...", "..": "...", "..": "..."}
    ]
  }

获取客户端详情
===============

.. http:get:: /v0.1/sapi/webrtcclient/(str:webrtcclient_id)/detail

  获取指定 `id` (`webrtcclient_id`) 的 :class:`sapi.WebRtcClient` 实例的详情

  :>json object data: :class:`sapi.WebRtcClient` 对象

  .. rubric:: 举例

  **客户端调用：**

  .. code-block:: http

    GET /v0.1/sapi/webrtcclient/1001/detail HTTP/1.1
    Host: api.linkrtc.com

  **服务器回复：**

  .. code-block:: http

    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: xxx

    {
      "data": {
        "id": "xxxxx",
        "from": "xxxxx",
        "to": "xxxxx",
        "...": "..."
      }
    }

新建客户端
===========

.. http:post:: /v0.1/sapi/webrtcclient/create

  新建一个 :class:`sapi.WebRtcClient` 对象

  :<json str id: 要新建的 :class:`sapi.WebRtcClient` 对象的 `id`
  :>json object data: 新建的 :class:`sapi.WebRtcClient` 对象

删除客户端
===========

.. http:post:: /v0.1/sapi/webrtcclient/(str:webrtcclient_id)/remove

  删除指定 `id` (`webrtcclient_id`) 的 :class:`sapi.WebRtcClient` 实例
