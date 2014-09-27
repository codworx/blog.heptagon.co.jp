---
title: bash-completionでシェル補完の強化
author: tachibana
layout: post
permalink: /archives/28
categories:
  - CentOS
  - linux
---
ubuntuでbashを触ると補完が楽だったので、CentOSでも。

インストールは

<pre class="brush: plain; title: ; notranslate" title="">yum -y install bash-completion</pre>

でOK。

すると、

<pre class="brush: plain; title: ; notranslate" title="">yum p[tab]</pre>

で

<pre class="brush: plain; title: ; notranslate" title="">yum provides</pre>

って補完してくれたり、

<pre class="brush: plain; title: ; notranslate" title="">rpm -ivh [tab]</pre>

で補完候補がrpmファイルだけになる。

プログラマブル補完っていうらしい。