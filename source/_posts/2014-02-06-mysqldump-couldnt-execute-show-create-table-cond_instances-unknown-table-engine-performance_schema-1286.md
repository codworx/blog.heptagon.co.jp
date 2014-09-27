---
title: 'mysqldump: Couldn&#8217;t execute &#8216;show create table `cond_instances`&#8217;: Unknown table engine &#8216;PERFORMANCE_SCHEMA&#8217; (1286)'
author: ishizawa
layout: post
permalink: /archives/215
categories:
  - mysql
---
mysqldump時にエラー出ました。。

備忘録です。

&nbsp;

環境はこちら。

<pre class="brush: bash; title: ; notranslate" title="">[root@localhost ~]# cat /etc/redhat-release
CentOS release 5.6 (Final)

[root@localhost ~]# uname -a
Linux localhost 2.6.18-238.5.1.el5 #1 SMP Fri Apr 1 18:41:58 EDT 2011 x86_64 x86_64 x86_64 GNU/Linux

[root@localhost ~]# mysql -V
mysql Ver 14.14 Distrib 5.1.52, for redhat-linux-gnu (x86_64) using readline 5.1

</pre>

&nbsp;

mysqldump時に起こったエラーがこちら。

<pre class="brush: bash; title: ; notranslate" title="">[root@localhost ~]# mysqldump -u root -p --all-databases --lock-all-tables &gt; /tmp/mysqldump.sql
Enter password:
mysqldump: Couldn't execute 'show create table `cond_instances`': Unknown table engine 'PERFORMANCE_SCHEMA' (1286)

</pre>

&nbsp;

んで解決方法調べたらとりあえずスキップしろとのことだったので下記で突破。

<pre class="brush: bash; title: ; notranslate" title="">[root@localhost ~]# mysqldump -u root -p --all-databases --lock-all-tables \
--ignore-table=performance_schema.cond_instances \
--ignore-table=performance_schema.events_waits_current \
--ignore-table=performance_schema.cond_instances \
--ignore-table=performance_schema.events_waits_history \
--ignore-table=performance_schema.events_waits_history_long \
--ignore-table=performance_schema.events_waits_summary_by_instance\
--ignore-table=performance_schema.events_waits_summary_by_thread_by_event_name \
--ignore-table=performance_schema.events_waits_summary_global_by_event_name \
--ignore-table=performance_schema.file_instances \
--ignore-table=performance_schema.file_summary_by_event_name \
--ignore-table=performance_schema.file_summary_by_instance \
--ignore-table=performance_schema.mutex_instances \
--ignore-table=performance_schema.performance_timers \
--ignore-table=performance_schema.rwlock_instances \
--ignore-table=performance_schema.setup_consumers \
--ignore-table=performance_schema.setup_instruments \
--ignore-table=performance_schema.setup_timers \
--ignore-table=performance_schema.threads &gt; /tmp/mysqldump.sql

</pre>

とりあえず解決！

&nbsp;

でもインポートのときにもエラーがありまして、

文字コードがsjisのテーブルをそのままインポートしようとしたら下記エラーが。。

<pre class="brush: bash; title: ; notranslate" title="">[root@localhost ~]# mysql -u root -p &lt; /tmp/mysqldump.sql
Enter password:
ERROR at line 1157: Unknown command '\�'.

</pre>

&nbsp;

&#8220;/etc/my.cnf&#8221;に書いたけどダメだったので、

&#8211;default-character-set オプションを渡して解決。

<pre class="brush: bash; title: ; notranslate" title="">[root@localhost ~]# mysql -u root -p --default-character-set=sjis &lt; /tmp/mysqldump.sql
</pre>

&nbsp;  
&nbsp;