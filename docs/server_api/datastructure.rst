数据结构定义
##############
在服务器API中，用 :term:`JSON` 对象格式记录各个实体的属性。

本章节使用类定义的形式描述各个实体以及其属性的含义与数据类型。

:term:`WebRTC` 客户端媒体能力
=============================

.. class:: MediaCapability

  .. attribute:: audio: bool

    是否具备音频能力

    :data type: bool
    :default: ``True``

  .. attribute:: video: bool

    是否具备视频能力
    
    :data type: bool
    :default: ``False``

:term:`WebRTC` 客户端
=======================

.. class:: WebRtcClient

  :term:`WebRTC` 客户端

  .. attribute:: id

    客户端ID

    :data type: str

  .. attribute:: capability

    :data type: :class:`MediaCapability`
