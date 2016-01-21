###########
呼出过程
###########

=====
定义
=====
呼出过程是指：

  具有 `WebRTC`_ 功能的浏览器/客户端通过  `LinkRTC`_ 呼叫 `SIP`_ 终端/服务器的过程。

呼出过程的参与者有：

  * 具有 `WebRTC`_ 功能的浏览器/客户端
  * 用户的 Web 服务器
  * `LinkRTC`_ 服务器
  * 被叫 `SIP`_ 电话

=========
步骤说明
=========

以下分步骤说明呼出过程。

在顺序图中：

  * `browser` 代表：具有 `WebRTC`_ 功能的浏览器/客户端
  * `webserver` 代表：用户的 Web 服务器
  * `linkrtc` 代表：`LinkRTC`_ 服务器
  * `telephone` 代表：被叫 `SIP`_ 电话

------------
1. 发起呼叫
------------

.. seqdiag::

  browser; webserver; linkrtc; telephone;

  browser -> linkrtc [label="make_call(from=x, to=y)"];
  linkrtc -> webserver [label="is_allowed ?"];

.. _LinkRTC: http://linkrtc.com/
.. _WebRTC: http://webrtc.org/
.. _SIP: http://www.ietf.org/rfc/rfc3261.txt
