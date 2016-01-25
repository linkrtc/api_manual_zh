WebRTC 客户端管理 API
######################

获取客户端列表
===============

.. http:get:: /sapi/webrtcclient

  获取 :class:`sapi.WebRtcClient` 实例列表

  :query int page: 要返回的页码。默认为1（1开始）。
  :query int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>header X-Pagination-Current-Page: 每页长度
  :>header X-Pagination-Per-Page: 当前返回结果的页码（1开始）
  :>header X-Pagination-Total-Pages: 总页数
  :>header X-Pagination-Total-Entries: 总条目数

  :>jsonarr object: :datatype: :class:`sapi.WebRtcClient`

获取客户端详情
===============

.. http:get:: /sapi/webrtcclient/(str:client_id)

  获取一个 :class:`sapi.WebRtcClient` 实例的详情

  :>json object: :datatype: :class:`sapi.WebRtcClient`

新建客户端
===========

.. http:post:: /sapi/webrtcclient/create

  新建一个 :class:`sapi.WebRtcClient` 实例

删除客户端
===========

.. http:post:: /sapi/webrtcclient/(str:client_id)/remove

  删除一个 :class:`sapi.WebRtcClient` 实例
