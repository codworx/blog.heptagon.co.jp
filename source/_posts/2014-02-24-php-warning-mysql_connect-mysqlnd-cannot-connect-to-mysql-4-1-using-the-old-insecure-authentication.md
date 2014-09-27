---
title: 'PHP Warning:  mysql_connect(): mysqlnd cannot connect to MySQL 4.1+ using the old insecure authentication.'
author: ishizawa
layout: post
permalink: /archives/256
categories:
  - mysql
---
php5.4をインストールしてデータベース接続したらエラー出ました。

<pre class="brush: bash; title: ; notranslate" title=""># /usr/local/lib/php-5.4/bin/php54 -r 'mysql_connect("localhost", "root", "password");';
PHP Warning: mysql_connect(): mysqlnd cannot connect to MySQL 4.1+ using the old insecure authentication.
Please use an administration tool to reset your password with the command SET PASSWORD = PASSWORD('your_existing_password').
This will store a new, and more secure, hash value in mysql.user.
If this user is used in other scripts executed by PHP 5.2 or earlier you might need to remove the old-passwords flag from your my.cnf file in Command line code on line 1
</pre>

とりあえずold_passwords=0を設定してパスワードを再設定すればいいらしい。  
どういうことかというとこちらのサイトさまの説明が詳しいです。

http://serversmanvps.xn--ockc3f5a.com/2013/06/10/old_passwords%E3%81%AE%E7%BD%A0/

要は、  
・old_passwords=0の場合はPASSWORD関数が生成するハッシュが41桁になる  
・old_passwords=1の場合はPASSWORD関数が生成するハッシュが16桁になる  
ということらしい。

とりあえず今回は一時的に@@session.old_passwordsを0にしてパスワード再設定して対応。  
その後無事接続できました。

<pre class="brush: bash; title: ; notranslate" title="">mysql&gt; SELECT @@session.old_passwords, @@global.old_passwords;
+-------------------------+------------------------+
| @@session.old_passwords | @@global.old_passwords |
+-------------------------+------------------------+
|                       1 |                      1 |
+-------------------------+------------------------+

mysql&gt; SET @@session.old_passwords = 0;

mysql&gt; SELECT @@session.old_passwords, @@global.old_passwords;
+-------------------------+------------------------+
| @@session.old_passwords | @@global.old_passwords |
+-------------------------+------------------------+
|                       0 |                      1 |
+-------------------------+------------------------+

mysql&gt; set password for 'user'@'localhost' = PASSWORD('password');
mysql&gt; flush privileges;
</pre>