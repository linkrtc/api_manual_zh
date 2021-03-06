.. _label-proc-outgoing-call:

呼出过程
###########

.. _label-outgoing-call-def:

定义
=====
呼出过程是指：
  具有 :term:`WebRTC` 功能的浏览器/客户端通过 :term:`LinkRTC` 呼叫 :term:`SIP` 终端/服务器的过程。

呼出过程的参与者有：
  * 具有 :term:`WebRTC` 功能的浏览器/客户端
  * 用户应用服务程序
  * :term:`LinkRTC` 服务器
  * 被叫 :term:`SIP` 电话

状态变化
=========
外呼过程中，呼叫的状态有：
  * `pending`：:term:`LinkRTC` 准备进行呼出，但是呼出还没有真正开始的阶段。这是呼叫的开始状态。
  * `calling`：:term:`LinkRTC` 向目标 :term:`SIP` 端点发出了 `INVITE` 指令。
  * `ringing`：目标 :term:`SIP` 端点向 :term:`LinkRTC` 发出了 `RINGING` 指令。这通常出现在被叫电话振铃的时候。
  * `confirmed`：目标 :term:`SIP` 端点向 :term:`LinkRTC` 发出了 `200 OK` 状态码。这通常表示被叫电话接听，双方可以建立通话。
  * `dropped`：呼叫失败或者通话结束。这是呼叫的结束状态。

.. digraph:: outgoing_call_state

    begin [shape=point]
    end [shape=doublecircle]

    begin -> pending [label="准备呼叫"]

    pending -> calling [label="开始呼叫"]
    pending -> dropped [label="禁止呼叫"]

    calling -> ringing [label="回铃"]
    calling -> confirmed [label="接通"]
    calling -> dropped [label="呼叫失败"]

    ringing -> confirmed [label="接通"]
    ringing -> dropped [label="呼叫失败"]

    confirmed -> dropped [label="通话结束"]

    dropped -> end

步骤说明
=========

以下分步骤说明呼出过程。

在顺序图中：
  * `c1` 代表：具有 :term:`WebRTC` 功能的浏览器/客户端 `c1`
  * `appserver` 代表：用户应用服务程序
  * `linkrtc` 代表：:term:`LinkRTC` 服务器
  * `s1` 代表：被叫 :term:`SIP` 端点 `s1`

1. 请求呼出
---------------

1.1. 客户端 `c1` 向 :term:`LinkRTC` 提交呼出请求，在这个请求中，他要求以主叫号码 `x` 、被叫号码 `y` 的名义，向 :term:`SIP` 端点 `s1` 发起呼叫。

1.2. :term:`LinkRTC` 收到请求后，询问用户应用服务程序是否允许这次呼出。

  1.2.1. 如果允许： :term:`LinkRTC` 继续后续的呼出过程。

  1.2.2. 如果拒绝： :term:`LinkRTC` 结束此次呼出过程，并通知客户端呼出被拒绝。

.. rubric:: 请求呼出，并被允许

.. seqdiag::

  c1; appserver; linkrtc; s1;

  c1 -> linkrtc [label="make_call: from=x, to=y, target=s1"];
  linkrtc -->> appserver [label="notify: call outgoing"];
  ... wait ...
  appserver => linkrtc [label="command: allowed"];
  c1 <- linkrtc [label="return: continue"];
  ... continue ...

.. rubric:: 请求呼出，并被拒绝

.. seqdiag::

  c1; appserver; linkrtc; s1;

  c1 -> linkrtc [label="make_call: from=x, to=y"];
  linkrtc -->> appserver [label="notify: call outgoing"];
  ... wait ...
  appserver => linkrtc [label="command: disallowed!", color=red];
  c1 <- linkrtc [label="return: refused", color=red];
  ... break ...

2. 呼叫 SIP
---------------

2.1. :term:`LinkRTC` 以主叫号码 `x` 、被叫号码 `y` 的名义，向 `s1` 发起 :term:`SIP` 呼叫。

2.2. :term:`LinkRTC` 将呼叫状态的变化 **同时** 通知 客户端 `c1` 和 用户应用服务程序，直到呼叫建立或者失败。

.. rubric:: SIP 呼叫成功

.. seqdiag::

  c1; appserver; linkrtc; s1;

  ... continue ...

  linkrtc ->> appserver [label="call state: pending"];
  linkrtc ->> c1 [label="call state: pending"];
  linkrtc <<- appserver;
  linkrtc <<- c1;

  linkrtc -->> s1 [label="INVITE"];
  linkrtc ->> appserver [label="call state: calling"];
  linkrtc ->> c1 [label="call state: calling"];
  linkrtc <<- appserver;
  linkrtc <<- c1;

  ... wait ...

  linkrtc <<-- s1 [label="RINGING"];
  linkrtc ->> appserver [label="call state: ringing"];
  linkrtc ->> c1 [label="call state: ringing"];
  linkrtc <<- appserver;
  linkrtc <<- c1;

  ... wait for answer ...

  linkrtc <<-- s1 [label="OK with SDP"];
  linkrtc ->> appserver [label="call state: confirmed(with SDP)"];
  linkrtc ->> c1 [label="call state: confirmed(with SDP)"];
  linkrtc <<- appserver;
  linkrtc <<- c1;

  ... continue ...

.. rubric:: SIP 呼叫失败

.. seqdiag::

  c1; appserver; linkrtc; s1;

  ... continue ...

  linkrtc ->> appserver [label="call state: pending"];
  linkrtc ->> c1 [label="call state: pending"];
  linkrtc <<- appserver;
  linkrtc <<- c1;
  linkrtc -->> s1 [label="INVITE"];
  linkrtc ->> appserver [label="call state: calling"];
  linkrtc ->> c1 [label="call state: calling"];
  linkrtc <<- appserver;
  linkrtc <<- c1;

  ... wait ...

  linkrtc <<-- s1 [label="486 Busy Here", color=red];
  linkrtc ->> appserver [label="call state: disconnected", color=red];
  linkrtc ->> c1 [label="call state: disconnected", color=red];
  linkrtc <<- appserver;
  linkrtc <<- c1;

  ... break ...

3. 媒体连接
---------------
如果呼叫成功，客户端 `c1` 会收到 :term:`LinkRTC` 转发的对端 :term:`SIP` 终端的 :term:`SDP` ，
`c1` 根据该 :term:`SDP` ，使用 :term:`WebRTC` 建立点对点媒体通道。

4. 呼叫结束
--------------
:term:`SIP` 终端 `s1` 向 :term:`LinkRTC` 发送 :term:`SIP` `BYE` 指令；或者 `c1` 向 :term:`LinkRTC` 发结束命令，都会导致呼叫的结束。

当 `s1` 主动结束呼叫时， :term:`LinkRTC` 会将通话状态变化 **同时** 通知 `c1` 和 用户应用服务程序。

.. rubric:: :term:`SIP` 一方结束呼叫

.. seqdiag::

  c1; appserver; linkrtc; s1;

  ... continue ...
  s1 -->> linkrtc [label="BYE"];
  linkrtc ->> appserver [label="call state: disconnected"];
  linkrtc ->> c1 [label="call state: disconnected"];
  linkrtc <<- appserver;
  linkrtc <<- c1;
  s1 <<-- linkrtc [label="ACK"];

.. rubric:: :term:`WebRTC` 一方结束呼叫

.. seqdiag::

  c1; appserver; linkrtc; s1;

  ... continue ...
  c1 -> linkrtc [label="end call"];
  linkrtc -->> s1 [label="BYE"];
  c1 <- linkrtc;
  linkrtc ->> appserver [label="call state: disconnected"];
  linkrtc <<- appserver;
  linkrtc <<-- s1 [label="ACK"];
