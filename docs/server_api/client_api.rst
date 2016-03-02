WebRTC 客户端管理 API
######################

获取客户端列表
===============

.. http:post:: /v0.1/sapi/Client/list

  获取 :class:`sapi.Client` 实例列表

  :<json int page: 要返回的页码。如果不指定，默认为0（从0开始）。
  :<json int per_page: 每页长度。如果不指定，服务器采用其默认设置。
  :>json int current_page: 当前返回结果的页码（从0开始）。
  :>json int per_page: 每页长度。
  :>json int total_pages: 总页数。
  :>json int total_entries: 总条目数。
  :>json list entries: :class:`sapi.Client` 对象列表。

.. rubric:: 举例

**客户端调用：**

.. code-block:: http

  POST /v0.1/sapi/Client/list HTTP/1.1
  Host: api.linkrtc.com
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {
    "page": 1,
    "per_page": 20
  }

**服务器回复：**

.. code-block:: http

  HTTP/1.1 200 OK
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx

  {
    "current_page": 1,
    "per_page": 20,
    "total_pages": 3,
    "total_entries": 57,
    "entries": [
      {"..": "...", "..": "...", "..": "..."},
      {"..": "...", "..": "...", "..": "..."}
    ]
  }

获取客户端详情
===============

.. http:post:: /v0.1/sapi/Client/detail

  获取指定 `id` (`client_id`) 的 :class:`sapi.Client` 实例的详情

  :<json str name: 客户端的名称
  :>json object data: :class:`sapi.Client` 对象

  .. rubric:: 举例

  **客户端调用：**

  .. code-block:: http

    POST /v0.1/sapi/client/1001/detail HTTP/1.1
    Host: api.linkrtc.com
    Content-Type: application/json; charset=utf-8
    Content-Length: xxx

    {"name": "client-01"}

  **服务器回复：**

  .. code-block:: http

    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: xxx

    {
      "data": {
        "id": "xxxxx",
        "name": "xxxxx",
        "...": "..."
      }
    }

新建客户端
===========

.. http:post:: /v0.1/sapi/Client/create

  新建一个 :class:`sapi.client` 对象

  :<json str name: 要新建的 :class:`sapi.Client` 对象的名称，对应 :attr:`sapi.Client.name` 属性.
  :>json object data: 新建的 :class:`sapi.Client` 对象

删除客户端
===========

.. http:post:: /v0.1/sapi/Client/delete

  :<json str name: 要删除的客户端的名称
