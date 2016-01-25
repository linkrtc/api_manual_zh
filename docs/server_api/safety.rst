安全
############

数据加密
============
用户应用服务程序向 :term:`LinkRTC` 后台发送请求时， **必须** 使用 :term:`HTTPS` 连接。

.. _label-auth:

身份认证
===========
用户应用服务程序向 :term:`LinkRTC` 发送的请求需要提供 :term:`HTTP Basic Authentication` （`HTTP` 基本认证）信息。
:term:`LinkRTC` 根据认证信息中的用户名和密码对调用者进行身份认证。

例如，某用户在 :term:`LinkRTC` 拥有一个 `SID` 为 ``Project1`` 的 :ref:`项目<label-account-project>` ，
该项目的访问密码是 ``abc123``，那么，用户应用服务程序发送的 `HTTP` 请求 **必须** 带有正确的 :http:header:`Authorization` 头：

.. code-block:: http

  GET /sapi/webrtcclient/5 HTTP/1.1
  Host: api.linkrtc.com
  Authorization: Basic UHJvamVjdDE6YWJjMTIz

如果用户应用服务程序缺少或者没有提供正确 :http:header:`Authorization` 信息，
:term:`LinkRTC` 将返回 :http:statuscode:`401` 。

消息签名
===========
:term:`LinkRTC` 在其向用户应用服务程序发送的 `HTTP` 请求中附加了签名和时间戳。
用户应用服务程序可以对签名进行验证，防止冒充的消息。

签名算法所需参数有：
  * Project SID: 该 :ref:`项目<label-account-project>` 的 `SID`
  * AppSecret: 该 :ref:`项目<label-account-project>` 的反向访问密码（与 :ref:`label-auth` 过程中所使用的密码不同）
  * TimeStamp: Unix 时间戳

计算步骤：
  1. 分别计算上述三个字符串参数的MD5散列值的十六进制表达式（字母部分大写）。
  2. 将这三个散列值按照字符顺序排序后，依次首尾连接，得到一个新字符串。
  3. 计算上一个步骤中得到的新字符串的MD5散列值的十六进制表达式（字母部分大写），即为签名。

签名和Unix时间戳被附加在 `HTTP` 请求的头域中，
  * 时间戳的头域: :http:header:`X-LinkRTC-Signature`
  * 签名的头域: :http:header:`X-LinkRTC-Timestamp`

带有签名信息的 `HTTP` 请求形如:

.. code-block:: http

  POST /your/script.php HTTP/1.1
  Host: your.company.com
  Content-Type: application/json; charset=utf-8
  Content-Length: xxx
  X-LinkRTC-Timestamp: 1453543759
  X-LinkRTC-Signature: E6E157A9FA805921DA12A86A40CC2A15

  {
    "type": "xxxx",
    "data": "xxxx",
  }

签名算法
-----------------
在下面的代码片段中：
  * :ref:`项目<label-account-project>` `SID` ： ``Project1``
  * `AppSecret` ： ``123abc``
  * `Timestamp` ： ``1453543759``

得到的签名应该是::

  E6E157A9FA805921DA12A86A40CC2A15

以下几个小节是几种常见语言的签名算法实现代码片段：

Java
``````

.. code-block:: java

  import java.security.MessageDigest;
  import java.security.NoSuchAlgorithmException;
  import java.util.ArrayList;
  import java.util.Collections;

  public class SignatureExample {

    public static String byteArrayToHex(byte[] byteArray) {
        char[] hexDigits = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };
        char[] resultCharArray = new char[byteArray.length * 2];
        int index = 0;
        for (byte b : byteArray) {
            resultCharArray[index++] = hexDigits[b >>> 4 & 0xf];
            resultCharArray[index++] = hexDigits[b & 0xf];
        }
        return new String(resultCharArray);
    }

    public static String md5Str(String input)
            throws NoSuchAlgorithmException {
        MessageDigest messageDigest = MessageDigest.getInstance("MD5");
        byte[] inputByteArray = input.getBytes();
        messageDigest.update(inputByteArray);
        byte[] resultByteArray = messageDigest.digest();
        return byteArrayToHex(resultByteArray);
    }

    public static void main(String[] args)  {
        try {
            String projectSid = "Project1";
            String appSecret = "123abc";
            String timestamp = "1453543759";

            ArrayList<String> tmpList = new ArrayList<String>();
            tmpList.add(md5Str(projectSid);
            tmpList.add(md5Str(appSecret);
            tmpList.add(md5Str(timestamp);
            Collections.sort(tmpList);

            String signature = md5Str(String.join("", tmpList));

            System.out.format("signature = %s", signature);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }
    }

  }

NodeJs
```````
.. code-block:: js

  var crypto = require('crypto');

  (function() {
    var projectSid = "Project1";
    var appSecret = "123abc";
    var timestamp = "1453543759";

    var md5Str = function(s) {
      var hasher = crypto.createHash('md5');
      hasher.update(s);
      return hasher.digest('hex').toUpperCase();
    }

    var tmpArr = [projectSid, appSecret, timestamp].map(md5Str);
    tmpArr.sort();
    var signature = md5Str(tmpArr.join(''));

    console.log(`signature = ${signature}`);
  })();

Php
```````

.. code-block:: php

  <?php
  $project_sid = 'Project1';
  $app_secret = '123abc';
  $timestamp = '1453543759';

  function md5_str($s) {
      return strtoupper(md5($s));
  }

  $tmp_arr = array_map(md5_str, array($project_sid, $app_secret, $timestamp));
  sort($tmp_arr, SORT_STRING);

  $signature = md5_str(implode($tmp_arr));

  echo('signature = ' . $signature);

Python
```````

.. code-block:: py

  from hashlib import md5

  project_sid = b'Project1'
  app_secret = b'123abc'
  timestamp = b'1453543759'

  def md5_str(s):
    return md5(s).hexdigest().upper().encode()

  signature = md5_str(b''.join(sorted(map(md5_str, [project_sid, app_secret, timestamp]))))

  print('signature = %s' % signature)
