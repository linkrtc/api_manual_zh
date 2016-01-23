呼入过程
###########

.. _label-incoming-call-def:

定义
=====
呼入过程是指：
  :term:`SIP` 终端/服务器向 :term:`LinkRTC` 发起呼叫， :term:`LinkRTC` 将呼叫交换到具有 :term:`WebRTC` 功能的浏览器/客户端的过程。

呼出过程的参与者有：
  * 主叫 :term:`SIP` 电话
  * :term:`LinkRTC` 服务器
  * 具有 :term:`WebRTC` 功能的浏览器/客户端
  * 用户的 Web 服务器

状态变化
=========
外呼过程中，呼叫的状态有：
  * `pending`：:term:`LinkRTC` 收到了来自 :term:`SIP` 端点的呼入，单还没有决定如何处理。
  * `calling`：用户的程序告知 :term:`LinkRTC` 将呼入选择到某一客户端，:term:`LinkRTC` 开始呼叫这个客户端。
  * `ringing`：客户端收到了 :term:`LinkRTC` 的呼叫。
  * `confirmed`：客户端接受了呼叫，双方可以建立通话。
  * `dropped`：呼叫失败或者通话结束。这是呼叫的结束状态。

.. digraph:: incoming_call_state

    begin [shape=point]
    end [shape=doublecircle]

    begin -> pending [label="呼入"]

    pending -> calling [label="确定了目标客户端"]
    pending -> dropped [label="呼叫禁止"]

    calling -> ringing [label="客户端收到呼叫"]
    calling -> dropped [label="呼叫失败"]

    ringing -> confirmed [label="接通"]
    ringing -> dropped [label="呼叫失败"]

    confirmed -> dropped [label="通话结束"]

    dropped -> end

步骤说明
=========

以下分步骤说明呼入过程。

在顺序图中：
  * `s1` 代表：主叫叫 :term:`SIP` 端点 `s1`
  * `linkrtc` 代表：:term:`LinkRTC` 服务器
  * `webserver` 代表：用户的 Web 服务器
  * `c1` 代表：具有 :term:`WebRTC` 功能的浏览器/客户端 `c1`

1. 呼入选择
---------------

1.1. :term:`LinkRTC` 收到了来自 `s1` 的呼入请求

1.2. :term:`LinkRTC` 向 `s1` 回复 :term:`SIP` 状态码 `100 TRYING`

1.3. :term:`LinkRTC` 询问用户的服务程序，是否允许这个呼入，以及要将这个呼入交换到哪个客户端

  1.3.1 如果用户的服务程序禁止该呼入，则结束呼叫过程

  1.3.2 如果用户的服务程序指定了目标客户端，则继续后续过程

.. rubric:: 呼入选择

.. seqdiag::

  s1; linkrtc; webserver; c1;

  s1 -->> linkrtc [label="INVITE"];
  s1 <<-- linkrtc [label="TRYING"];
  linkrtc -> webserver [label="incoming_call: to whom?"];
  linkrtc <- webserver [label="return: to 'c1'"];
  ... continue ...

.. rubric:: 呼入拒绝

.. seqdiag::

  s1; linkrtc; webserver; c1;

  s1 -->> linkrtc [label="INVITE"];
  s1 <<-- linkrtc [label="TRYING"];
  linkrtc -> webserver [label="incoming_call: to whom?"];
  linkrtc <- webserver [label="return: refused!", color=red];
  s1 <<-- linkrtc [label="403 Forbidden", color=red];
  ... break ...

2. 呼叫客户端
---------------

2.1. :term:`LinkRTC` 向目标客户端 `c1` 发起呼叫

  2.1.1. 如果 `c1` 无法收到呼叫指令，则结束此次呼入过程，并通知用户的应用服务器。

  2.1.2. 如果 `c1` 收到了呼叫，通知主叫 :term:`SIP` 和用户的应用服务器，然后等待 `c1` 的响应。

2.2. 然后等待 `c1` 的响应。

  2.2.1. 如果 `c1` 拒绝，或者超时无响应，则结束此次呼入过程，并通知用户的应用服务器。

  2.2.2. 如果 `c1` 接受呼入，则通知主叫 :term:`SIP` 和用户的应用服务器，继续后续步骤。

.. rubric:: 呼叫客户端成功

.. seqdiag::

  s1; linkrtc; webserver; c1;

  ... continue ...
  s1 -->> linkrtc [label="INVITE: from='x', to='y'"];
  s1 <<-- linkrtc [label="TRYING"];
  linkrtc -->> webserver [label="notify: incoming call(from='x', to='y')"];
  ... wait ...
  linkrtc <<-- webserver [label="notify: switch the call to 'c1'"];
  linkrtc -->> webserver [label="notify: state=calling"];
  linkrtc -->> c1 [label="incoming call: from='x', to='y'"];
  linkrtc <<-- webserver;
  ... wait ...
  linkrtc <<-- c1 [label="return: accept"];
  linkrtc -->> webserver [label="notify: accepted"];
  s1 <<-- linkrtc [label="200 OK"];
  ... continue ...

.. rubric:: 呼叫客户端失败

.. seqdiag::

  s1; linkrtc; webserver; c1;

  ... continue ...
  s1 -->> linkrtc [label="INVITE: from='x', to='y'"];
  s1 <<-- linkrtc [label="TRYING"];
  linkrtc -->> webserver [label="notify: incoming call(from='x', to='y')"];
  ... wait ...
  linkrtc <<-- webserver [label="notify: switch the call to 'c1'"];
  linkrtc -->> webserver [label="notify: state=calling"];
  linkrtc -->> c1 [label="incoming call: from='x', to='y'", failed, color=red];
  linkrtc <<-- webserver;
  linkrtc -->> webserver [label="notify: state=dropped"];
  s1 <<-- linkrtc [label="480 Temporarily Unavailable", color=red];
  linkrtc <<-- webserver;
  ... break ...

3. 媒体连接
---------------
如果呼叫成功，客户端 `c1` 会收到 :term:`LinkRTC` 转发的对端 :term:`SIP` 终端的 :term:`SDP` ，
`c1` 根据该 :term:`SDP` ，使用 :term:`WebRTC` 建立点对点媒体通道。

4. 呼叫结束
--------------
:term:`SIP` 终端 `s1` 向 :term:`LinkRTC` 发送 :term:`SIP` `BYE` 指令；或者 `c1` 向 :term:`LinkRTC` 发结束命令，都会导致呼叫的结束。

当 `s1` 主动结束呼叫时， :term:`LinkRTC` 会将通话状态变化 **同时** 通知 `c1` 和 用户的 Web 服务器。

.. rubric:: :term:`SIP` 一方结束呼叫

.. seqdiag::

  s1; linkrtc; webserver; c1;

  ... continue ...
  s1 -->> linkrtc [label="BYE"];
  linkrtc ->> webserver [label="call state: disconnected"];
  linkrtc ->> c1 [label="call state: disconnected"];
  linkrtc <<- webserver;
  linkrtc <<- c1;
  s1 <<-- linkrtc [label="ACK"];

.. rubric:: :term:`WebRTC` 一方结束呼叫

.. seqdiag::

  s1; linkrtc; webserver; c1;

  ... continue ...
  c1 -> linkrtc [label="end call"];
  linkrtc -->> s1 [label="BYE"];
  c1 <- linkrtc;
  linkrtc ->> webserver [label="call state: disconnected"];
  linkrtc <<- webserver;
  linkrtc <<-- s1 [label="ACK"];
