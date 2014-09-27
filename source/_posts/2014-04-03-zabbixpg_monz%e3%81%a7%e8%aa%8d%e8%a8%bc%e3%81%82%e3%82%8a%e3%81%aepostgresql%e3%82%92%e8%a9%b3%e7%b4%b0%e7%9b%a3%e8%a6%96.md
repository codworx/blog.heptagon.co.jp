---
title: zabbixとpg_monzで認証ありのpostgresqlを詳細監視
author: tachibana
layout: post
permalink: /archives/312
categories:
  - PostgreSQL
  - RDS
  - zabbix
  - 監視
---
pg_monzはzabbixでPostgreSQLを監視するためのテンプレートです。  
以下導入・設定方法は公式ページを参考に。

http://pg-monz.github.io/pg_monz/

主にZabbixのUserParameterで、psqlを用いて情報を取得しているようですが、  
現段階ではパスワード認証に対応していないようです。

パスワード認証が必要なPostgreSQLサーバへの監視の場合は、  
当然zabbixのログに  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;  
ユーザ postgres のパスワード:  
psql: fe_sendauth: no password supplied  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;  
のようにエラーが出力されてしまいます。

そこで、pgpassを用いて対話的にパスワードが聞かれないよう設定をします。

http://www.postgresql.jp/document/9.2/html/libpq-pgpass.html

環境に合わせて、zabbixのホームディレクトリに  
以下のように.pgpassを作成します。

vi /var/lib/zabbix/.pgpass  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;  
localhost:5432:*:postgres:postgrespass  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

chown zabbix. /var/lib/zabbix/.pgpass  
chmod 600 /var/lib/zabbix/.pgpass

上記を設定すれば、  
zabbixからパスワード認証付きのPostgreSQLの監視ができるようになります。

これでRDS for PostgreSQLの監視もできそう！