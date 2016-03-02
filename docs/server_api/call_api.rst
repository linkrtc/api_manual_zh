呼叫 API
##################

获取呼叫列表
===============

.. http:post:: /v0.1/sapi/Call/list

  获取当前正在进行的呼叫 :class:`sapi.Call` 实例列表

  :<json int page: 要返回的页码。如果不指定，默认为1（从1开始）。
  :<json int perPage: 每页长度。如果不指定，服务器采用其默认设置。
  :>json int currentPage: 当前返回结果的页码（从1开始）。
  :>json int perPage: 每页长度。
  :>json int totalPages: 总页数。
  :>json int totalEntries: 总条目数。
  :>json list entries: :class:`sapi.Call` 对象列表。

获取呼叫详情
===============

.. http:post:: /v0.1/sapi/Call/detail

  :<json str id: `call_id`
  :>json data: 呼叫详细信息( :class:`sapi.Call` 对象)。如果该 `call_id` 呼叫不存在，返回 `null`。

中断呼叫
===========

.. http:post:: /v0.1/sapi/Call/drop

    :<json str id: 中断该 `id` 呼叫，无论它处于什么状态。

呼叫交换
======================

.. http:post:: /v0.1/sapi/Call/switch

    :<json str id: `Call ID`

    :<json str dest: 交换目标

        * 如果目标是 :term:`SIP` 端点，该属性应是 :term:`SIP` URL 表达式；
        * 如果目标是 :term:`WebRTC` 客户端，该属性表达式是：
          ``linkrtc-client:<client_name>@<project_name>``

    .. note::
      目前只能将 :term:`SIP` 端点发起的呼叫交换到 :term:`WebRTC` 客户端，
      详见 :ref:`label-proc-incoming-call`

      或者将 :term:`WebRTC` 客户端发起的呼叫交换到 :term:`SIP` 端点，
      详见 :ref:`label-proc-outgoing-call`
