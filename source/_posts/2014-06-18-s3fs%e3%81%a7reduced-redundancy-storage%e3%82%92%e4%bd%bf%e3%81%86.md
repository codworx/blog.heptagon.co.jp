---
title: s3fsでReduced Redundancy Storageを使う
author: tachibana
layout: post
permalink: /archives/334
categories:
  - aws
  - CentOS
  - ec2
  - S3
---
S3をファイルシステムとしてマウントできるs3fsを設定します。  
環境はCentOS6 on EC2です。

まずは、fuseのインストール。  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-  
yum remove fuse fuse* fuse-devel  
yum install gcc libstdc++-devel gcc-c++ curl curl\* curl-devel libxml2 libxml2\* libxml2-devel openssl-devel mailcap

cd /usr/local/src  
wget http://sourceforge.net/projects/fuse/files/latest/download?source=files  
tar zxvf download\?source\=files  
cd fuse-2.9.3/  
./configure prefix=/usr  
make  
make install  
ldconfig  
modprobe fuse  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

次にs3fsのインストール。

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-  
cd /usr/local/src  
wget https://s3fs.googlecode.com/files/s3fs-1.72.tar.gz  
tar -xzvf s3fs-1.72.tar.gz  
rm s3fs-1.72.tar.gz  
cd s3fs-1.72  
export PKG\_CONFIG\_PATH=/usr/lib/pkgconfig  
./configure &#8211;prefix=/usr  
make  
make install  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

IAMの設定をします。  
IAMポリシーはこんなかんじで。  
&#8212;&#8212;&#8212;&#8212;&#8212;-  
{  
&#8220;Version&#8221;: &#8220;2012-10-17&#8243;,  
&#8220;Statement&#8221;: [  
{  
"Effect": "Allow",  
"Action": "s3:*",  
"Resource": [  
"arn:aws:s3:::バケット名",  
"arn:aws:s3:::バケット名/*"  
]  
}  
]  
}  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

バケットに接続できるように設定します。

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-  
vi /etc/passwd-s3fs  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;  
アクセスキー:シークレットキー  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

chmod 600 /etc/passwd-s3fs  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

マウントしてみます。  
use_rrs=1オプションを付ける事で、Reduced Redundancy Storage（低冗長化ストレージ）を使う事ができます。

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-  
s3fs バケット名 /mnt -o rw,allow\_other,use\_rrs=1  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

OS起動時に自動でマウントするように設定をします。

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-  
umount /mnt

vi /etc/rc.d/rc.sysinit  
&#8212;&#8212;&#8212;-  
. /etc/init.d/functions  
modprobe fuse  
&#8212;&#8212;&#8212;-

vi /etc/rc.d/rc.local  
&#8212;&#8212;&#8212;&#8211;  
umount /var/www/html/public_html/upload/  
s3fs バケット名 /mnt -o rw,allow\_other,use\_rrs=1  
&#8212;&#8212;&#8212;-

reboot  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

fstabに記載する方法もあるようですが、起動時にマウントできない場合があったので、  
rc.localでマウントするように設定しました。  
（停止時にうまくアンマウントできないとだめ？）

うまくマウントできるとこんな感じで見えます。

&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-  
df -h  
Filesystem Size Used Avail Use% Mounted on  
/dev/xvde 7.9G 1.6G 6.0G 21% /  
tmpfs 296M 0 296M 0% /dev/shm  
s3fs 256T 0 256T 0% /mnt  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

IOのパフォーマンスはあまり期待できないので、用途は考える必要がありそうですね。