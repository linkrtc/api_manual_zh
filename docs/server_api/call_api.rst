呼叫 API
##################

获取呼叫列表
===============

.. http:get:: /v0.1/sapi/call

  获取当前正在进行的呼叫 :class:`sapi.Call` 实例列表

  :query int page: 要返回的页码。默认为1（1开始）。
  :query int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>header X-Pagination-Current-Page: 每页长度
  :>header X-Pagination-Per-Page: 当前返回结果的页码（1开始）
  :>header X-Pagination-Total-Pages: 总页数
  :>header X-Pagination-Total-Entries: 总条目数

  :>jsonarr object: 数组中，每个元素表示一个呼叫的信息。

    :datatype: :class:`sapi.Call`

获取呼叫详情
===============

.. http:get:: /v0.1/sapi/call/(str:call_id)

  获取 `ID` 为 `call_id` 的呼叫的详情。

  :>json object: 该呼叫的详细信息。如果该 `call_id` 呼叫不存在，返回 `null`

    :datatype: :class:`sapi.Call`

中断呼叫
===========

.. http:post:: /v0.1/sapi/call/(str:call_id)/drop

    中断 `ID` 为 `call_id` 的呼叫，无论它处于什么状态。

出方向呼叫的允许/禁止
=====================

.. http:post:: /v0.1/sapi/call/(str:call_id)/allow

    允许/禁止 `ID` 为 `call_id` 的出方向呼叫

    :<json bool allowd: 是否允许

    详见 :ref:`label-proc-outgoing-call`

    .. attention:: 仅对 **出方向** 呼叫有效

入方向呼叫交换到客户端
======================

.. http:post:: /v0.1/sapi/call/(str:call_id)/switch

    将入方向呼叫交换到指定的客户端

    :<json str client_type: 客户端类型，目前仅支持 `WebRtc`
    :<json str client_id: 客户端 `ID`

    详见 :ref:`label-proc-incoming-call`

    .. attention:: 仅对 **入方向** 呼叫有效
