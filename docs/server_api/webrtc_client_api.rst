WebRTC 客户端 API
##################

获取客户端列表
===============

.. http:get:: /WebRtcClient

  获取 :term:`WebRTC` 客户端列表

  数据格式参见 :class:`WebRtcClient`

  :query int page: 要返回的页码。默认为1（1开始）。
  :query int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>header X-Pagination-Current-Page: 每页长度
  :>header X-Pagination-Per-Page: 当前返回结果的页码（1开始）
  :>header X-Pagination-Totle-Pages: 总页数
  :>header X-Pagination-Totle-Entries: 总条目数
  :>json string id: 专号

获取客户端详情
===============

.. http:get:: /WebRtcClient/(str: client_id)

  获取一个 :term:`WebRTC` 客户端的详情

新建客户端
===========

.. http:post:: /WebRtcClient

  新建一个 :term:`WebRTC` 客户端

删除客户端
===========

.. http:delete:: /WebRtcClient/(str: client_id)

  删除一个 :term:`WebRTC` 客户端
