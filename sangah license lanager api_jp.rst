新しいライセンスキーのリクエスト
==========

WebサービスのURL
-------------------

POSTリクエストを次のURLに送信します。

** / shop / api / license / create.action **



サービス認証
------------------------

サービスにアクセスするには、ユーザーを認証する必要があります。

リクエストは次のヘッダーフィールドを使用して送信する必要があります::

Authorization：Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ ==

権限フィールドは、下記のように構成されます。

- ユーザ名とパスワードは、1つのコロンで結合されます。
- 結果の文字列は、Base64を使用してエンコードされます。
- Authorization方法とスペース、すなわち「Basic」が符号化された文字列の前に置かれる。

.. note ::パスワード `` open sesame``を持つユーザ `` Aladdin``の場合、結合された文字列は次のようになります：
   ``Aladdin `` + ``： `` + ``オープンゴマ ``
   Base64を使ってエンコードすると、結果は `` QWxhZGRpbjpvcGVuIHNlc2FtZQ == ``になります。


トークン認証を使用したリクエストヘッダーの例：

.. code-block:: none
    :linenos:
    :emphasize-lines: 5

    POST /ezpert/api/license/create.action HTTP/1.1
    ...
    Origin: http://localhost:8003
    User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36
    Authorization: Token ZGlzY28xMjM0OltCQDFiNDVkODE=
    Content-Type: application/x-www-form-urlencoded
    Referer: http://localhost:8003/acx/index.jsp
    ...

---------------




必要なリクエストパラメータ
------------------------------

client_code
    新しいライセンスをリクエストするクライアントの識別子。この識別子は、Ezpertログインプロセスのライセンスおよびその他の情報とともに保存されます。

製品コード
    この場合のプロダクトコードは `ezpert`です

ライセンス（デフォルトは1）
    生成するライセンスの数


HTTPリクエストの例
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`CLIENT0001`というコードでクライアントのライセンスを1つリクエストします

..code-block:: bash

    $ curl \
    --data "client_code=CLIENT0001&product=ezpert" \
    --user username:password \
    http://ezpert.com/shop/api/license/create.action

クライアントに対して2つのライセンスをリクエストする：

.. code-block:: bash

    $ curl \
    --data "client_code=CLIENT0001&product=ezpert&license=2" \
    --user username:password \
    http://ezpert.com/shop/api/license/create.action


.. note ::上記のサンプルはlinuxで `` curl``コマンドを利用していますので、使用環境に合わせて試す必要があります。

.. note :: ** .NET **ユーザーの場合、.NETアプリケーションを介してリクエストを送信する際の参照と
下記ののウェブサイトで利用可能です。：
   
   * https://msdn.microsoft.com/en-us/library/debx8sh9(v=vs.110).aspx
   * https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest(v=vs.110).aspx
   * https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest.headers(v=vs.110).aspx
   * https://msdn.microsoft.com/en-us/library/system.web.httprequest.inputstream.aspx
   * https://msdn.microsoft.com/en-us/library/system.web.script.serialization.javascriptserializer.aspx

リスポンスタイプ
---------------

JSON出力
^^^^^^^^^^^^^^^^^

リスポンスが `` json``である場合、結果は1つのライセンスに対して下記のコードと同じようなものが出ると思います。：

.. code-block:: json

    {
        "client_code": "CLIENT0001",
        "license": ["ACTR-9QGO-BNCC-JWM0"]
    }

2つ以上のライセンスの場合：

.. code-block:: json

    {
        "client_code": "CLIENT0001",
        "license": ["ACTR-9QGO-BNCC-JWM0", "9AAI-CJKJ-PIDF-HKJ3"]
    }


XML出力
^^^^^^^^^^^^^^^

リスポンスが `` xml``の場合、結果は下記のサンプルと似ています：

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <Response>
        <ClientCode>CLIENT0001</ClientCode
        <Licenses>
            <License>ACTR-9QGO-BNCC-JWM0</License>
            <License>9AAI-CJKJ-PIDF-HKJ3</License>
        <Licenses>
    </Response>


エラーリスポンス
---------------------

認証資格情報が送信されていない場合::

    {
        "error"：{
            "message": "Unauthorized operation."
        }
    }

認証情報が有効ではない場合、認証は次の応答で失敗になります。

    {
        "error"：{
            "type"： "org.springframework.security.BadCredentialsException"、
            "message": "Login failed - username or password incorrect; nested exception is java.lang.RuntimeException: Login failed - username or password incorrect"
        }
    }