======================================
Elastic Stack のインストール
======================================
Elastic Stackをインストールするとき、全体のStackを横通しで同じバージョンを使わなければならない。
例えば、Elasticsearch 6.2.3を使っているなら、Beats 6.2.3、Elasticsearch Hadoop 6.2.3、
Kibana 6.2.3、Logstash 6.2.3、X-Pack 6.2.3をインストールする必要がある。

既存のものをアップグレードするには、「Upgrading the Elastic Stack」を参照してくれ。


---------------------------------------
インストール順序
---------------------------------------
以下の順番で、使いたいElastic Stackプロダクトをインストールする

1. Elasticsearch (:doc:`Install instructions for Elasticsearch<Elasticsearch/installation>`)
2. X-Pack for Elasticsearch
3. Kibana (:doc:`Install for Kibana<Kibana/installation>`)
4. X-Pack for Kibana
5. Logstash (:doc:`Install for Logstash<../Logstash/GettingStarted>`)
6. X-Pack for Logstash
7. Beats
8. Elasticsearch Hadoop

この順番でインストールすることは、各プロダクトが依存するコンポーネントが置かれていることを保証する。


---------------------------------------
Elastic Cloudのインストール
---------------------------------------
Elastic Cloudは、Elastic社から提供されている公式のホストされたElasticsearchとKibanaである。

...使わないので、SKIP...
