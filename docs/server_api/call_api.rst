呼叫 API
##################

获取呼叫列表
===============

.. http:get:: /call

  获取 :class:`WebRtcClient` 实例列表

  :query int page: 要返回的页码。默认为1（1开始）。
  :query int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>header X-Pagination-Current-Page: 每页长度
  :>header X-Pagination-Per-Page: 当前返回结果的页码（1开始）
  :>header X-Pagination-Total-Pages: 总页数
  :>header X-Pagination-Total-Entries: 总条目数

  :>jsonarr object: :datatype: :class:`WebRtcClient`
