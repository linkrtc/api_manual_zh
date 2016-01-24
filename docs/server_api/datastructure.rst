数据结构定义
##############
在服务器API中，用 :term:`JSON` 对象格式记录各个实体的属性。

本章节使用类定义的形式描述各个实体以及其属性的含义与数据类型。

:term:`WebRTC` 客户端媒体能力
=============================

.. class:: MediaCapability

  .. attribute:: audio

    是否具备音频能力

    :datatype: ``bool``
    :default: ``True``

  .. attribute:: video

    是否具备视频能力

    :datatype: ``bool``
    :default: ``False``

:term:`WebRTC` 客户端
=======================

.. class:: WebRtcClient

  该数据结构用于记录一个 :term:`WebRTC` 客户端实例的相关信息。

  .. attribute:: id

    客户端ID

    :datatype: ``str``

  .. attribute:: project

    该客户端所属的 :ref:`项目<label-account-project>` 的 `SID`

    :datatype: ``str``

  .. attribute:: capability

    媒体能力

    :datatype: :class:`MediaCapability`

呼叫信息
==========

.. class:: Call

  .. attribute:: id

    呼叫ID

    :datatype: str

  .. attribute:: dir

    呼叫方向

    :datateype: str
    :value:
      ======== ================
      方向      表达式
      ======== ================
      呼入     `incoming`
      呼出     `outgoing`
      ======== ================

  .. attribute:: state

    呼叫状态，详见 :ref:`label-proc-incoming-call` 与 :ref:`label-proc-outgoing-call`

    :datateype: str
    :value:
      ============ ================
      状态         表达式
      ============ ================
      待定         `incoming`
      呼叫中       `calling`
      等待应答     `ringing`
      已接通       `confirmed`
      结束         `dropped`
      ============ ================
