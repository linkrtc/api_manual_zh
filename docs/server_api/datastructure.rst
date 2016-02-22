数据结构定义
##############
在服务器API中，用 :term:`JSON` 对象格式记录各个实体的属性。

本章节使用面向对象语言类型定义的伪代码形式描述各个实体以及其属性的含义与数据类型。

.. attention:: 事件通知数据的结构定义不在本章，具体请参见 :ref:`label-notify-api`

.. module:: sapi

:term:`WebRTC` 客户端
=======================

.. class:: WebRtcClient

  该数据结构用于记录一个 :term:`WebRTC` 客户端实例的相关信息。

  .. attribute:: id

    客户端ID

    :rtype: str

  .. attribute:: project

    该客户端所属的 :ref:`项目<label-account-project>` 的 `SID`

    :rtype: str

  .. attribute:: wskey

    客户端 :term:`WebSocket` 连接关键字

    :rtype: str

呼叫信息
==========

.. class:: Call

  .. attribute:: id

    呼叫ID

    :rtype: str

  .. attribute:: dir

    呼叫方向

    :rtype: str

    定义:
      ======== ================
      方向      表达式
      ======== ================
      呼入     `incoming`
      呼出     `outgoing`
      ======== ================

  .. attribute:: current_state

    当前呼叫状态，详见 :ref:`label-proc-incoming-call` 与 :ref:`label-proc-outgoing-call`

    :rtype: str

    定义:
      ==================== ==================================
      **状态**              **表达式**
      ==================== ==================================
      待定                  ``pending``
      呼叫中                ``calling``
      等待应答              ``ringing``
      已接通                ``confirmed``
      结束                  ``dropped``
      ==================== ==================================

  .. attribute:: prior_state

    上一个呼叫状态，其属性值含义与 :attr:`current_state` 一致。

    :rtype: str

    .. note:: 当呼叫刚刚建立时，其当前状态 :attr:`current_state` 为 `pending` ，:attr:`prior_state` 值是 ``null`` 。

  .. attribute:: from

    :rtype: str

  .. attribute:: to

    :rtype: str
