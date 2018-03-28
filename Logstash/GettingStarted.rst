=========================
はじめてみよう
=========================

インストールして、適切に動くことを確認する手順についてまず説明する。
最初のイベントをしまう（隠す）方法を学んだ後、拡張したパイプラインを作成してみる。（inputでApacheのログを取って、そのログをパースして、Elasticsearchのクラスタにデータを書き込む。）
ばらばらのソースからのデータを統一する複数のinput、outputのプラグインをを一緒にひとまとめにするやり方を学ぶ。

---------------------------------------
インストール
---------------------------------------

.. admonition:: 要件
   :class: note

   Java8を要求。Java9は未サポート。Oracle JDK or OpenJDK を使う。


.. admonition:: Javaインストール
   :class: note

   .. code-block:: console

      //インストール
      # yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel

      //パスの確認
      # dirname $(readlink ($readlink $(which java)))
      /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/jre/bin

      //環境変数の設定
      # vi /etc/profile
      export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/jre/bin
      export PATH=$PATH:$JAVA_HOME/bin
      export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
      # source /etc/profile

   Linuxの場合、LogStashのインストール中に参照するため、インストールの前にJAVA_HOME環境変数が必要となる。

バイナリを使ってインストール
==================================================
実際の環境にマッチするインストールファイルをダウンロードして、解凍する。
Linuxなパッケージマネージャを使える。


パッケージリポジトリからインストール
=======================================================

YUM
---------------------------

/etc/yum.repos.d/ ディレクトリに、logstash.repoを追加する。

.. code-block:: text

   [logstash-6.x]
   name=Elastic repository for 6.x packages
   baseurl=https://artifacts.elastic.co/packages/6.x/yum
   gpgcheck=0
   enabled=1
   autorefresh=1
   type=rpm-md

.. code-block:: console

   # yum install logstash --disablerepo=\* --enablerepo=logstash-6.x

インストールしたら、6.2.3-1と最新らしきものがインストールできました！！

.. admonition:: インストール時のメッセージ
   :class: note

   | JAVA_HOMEなしの場合
   |     whieh: no java in (/sbin:/bin:/usr/sbin:/usr/bin:/usr/X11R6/bin)
   |     could not find java; set JAVA_HOME or ensure java is in PATH
   |     chmod: `/etc/default/logstash` にアクセスできません： そのようなファイルやディレクトリはありません。
   |     警告： %post(logstash-1:6.2.3-1.noarch) スクリプトの実行に失敗しました。終了ステータス 1
   |     Non-fatal POSTIN scriptlet failure in rom package 1:logstash-6.2.3-1.noarch
   |       検証中                 : 1:logstash-6.2.3-1.noarch
   |
   |     インストール:
   |       logstash.noarch 1:6.2.3-1
   |
   |     完了しました!

   | JAVA_HOME設定した場合
   |     Using provided startup.options file: /etc/logstash/startup.options
   |     OpenJDK 64-Bit Server VM warning: If the number of processors is expected
   |      to increase from one, then you should configure the number of
   |      parallel GC threads appropriately using -XX:ParalelGCTHreads=N
   |     Successfully created system startup script for LogStash
   |       検証中                 : 1:logstash-6.2.3-1.noarch
   |
   |     インストール:
   |       logstash.noarch 1:6.2.3-1
   |
   |     完了しました!

   | 最初JAVA_HOME忘れたので、上記メッセージが出た。
   | yum remove logstashでいったん削除してやり直したのが、成功メッセージ。

logstash.serviceについて
-----------------------------------------------------

.. code-block:: shell
   :name: /etc/systemd/system/logstash.service

   [Unit]
   Description=logStash

   [Service]
   Type=simple
   User=logStash
   Group=logstash

   EnvironmentFile=-/etc/default/logstash
   EnvironmentFile=-/etc/sysconfig/logstash
   ExecStart=/usr/share/logstash/bin/logstash "--path.settings" "/etc/logstash"
   Restart=always
   WorkingDirectory=/
   Nice=19
   LimitNOFILE=16384

   [Install]
   WantedBy=multi-user.target


.. admonition:: 課題？
   :class: todo

   アーカイブからインストールしたlogstashをサービス化する方法は？
   上記のlogstashベースだと失敗した。
   フォアグラウンドで動かすのでもいいのか？

---------------------------------------
最初のイベントをしまう
---------------------------------------
まずは、もっとも基本的な Logstash pipepline を実行して、Logstashを試してみよう。

Logstashパイプラインは、input、outputの2つのエレメントを要求する。そして、オプションで filter も使う。
inputプラグインはソースからデータを消費する（consume）、filterはプラグインは指定したようにデータを変更する、outputプラグインは宛先にデータを書き込む。

  .. figure:: ../_static/basic_logstash_pipeline.png

     基本的なLogstashのパイプライン


Logstashをテストするために、以下のように実行する

  .. code-block:: console

     # cd logstatsh-6.2.3
     # bin/logstash -e 'input { stdin {} } output { stdout {} }'




---------------------------------------
ログのパース
---------------------------------------



---------------------------------------------------
複数のInput/Outputプラグインをひとまとめに
---------------------------------------------------
