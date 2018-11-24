## dhcp option set 問題


### 症状
dhcp option setのdomain-nameに複数のドメインを登録すると、systemd系のOS(centos7,amazonlinux2)の起動後にhostnameが意図しないものとなる、また再起動後にresolve.confのsearch listも意図しないものとなる

例
domain-name=us-west-2.compute.internal my.local

と設定した場合

```
$ hostname
ip-10-0-0-206.us-west-2.compute.internalmy.local
```
となる

rebootを行った際　resolv.confが以下のように変化する

before
```
cat /etc/resolv.conf
options timeout:2 attempts:5
; generated by /usr/sbin/dhclient-script
search us-west-2.compute.internal my.local 
nameserver 10.0.0.2
```

after 
```
cat /etc/resolv.conf
options timeout:2 attempts:5
; generated by /usr/sbin/dhclient-script
search us-west-2.compute.internal my.local us-west-2.compute.internalmy.local
nameserver 10.0.0.2
```

### 経緯

サーバ、アプリケーション間の名前解決の補助を行う際の自動化の手法の一つとして、
dhcp option setのdomain-nameにスペース区切りで複数のドメインを登録する。
これにより、ネットワーク起動時にdhclient-scriptがresolv.confを生成する際にdhcpのoption domain-nameに指定した複数ドメインが渡され、
search listにセットされる。

このdhcp option setのdomain-nameの情報はinstanceのmeta-dataのlocal-hostnameのvalueにもセットされる。
### 原因

systemd系OSのcloud-initは初期化時にhostnameの初期化を行うが、その際にinstanceのmeta-dataのlocal-hostnameのvalueを利用するので、２つのドメインがセットされることを想定していないように見受けられ、スペース区切りの文字列は本来ドメインとして不正であるが、そのままhostnamectl set-hostnameの引数として渡される。結果意図しないhostnameがセットされる。

また再起動時にhostnameが意図しないものであるために、resolv.confが再作成する処理が走り、resolv.confのsearch listに意図しない３つ目のドメインがセットされる。

### 関連情報

dhclientのlease情報

`/var/lib/dhclient/dhclient--eth0.lease`
```
lease {
  interface "eth0";
  fixed-address 10.0.0.206;
  option subnet-mask 255.255.255.0;
  option routers 10.0.0.1;
  option dhcp-lease-time 3600;
  option dhcp-message-type 5;
  option domain-name-servers 10.0.0.2;
  option dhcp-server-identifier 10.0.0.1;
  option interface-mtu 9001;
  option broadcast-address 10.0.0.255;
  option host-name "ip-10-0-0-206";
  option domain-name "us-west-2.compute.internal my.local";
  renew 1 2018/11/12 06:34:41;
  rebind 1 2018/11/12 07:01:35;
  expire 1 2018/11/12 07:09:05;
}
```

meta data情報
```
$ ec2-metadata |grep local-hostname
local-hostname: ip-10-0-0-206.us-west-2.compute.internal my.local
```

cloud-initがhostnameを処理している箇所

`/var/log/cloud-init.log`
```
Nov 12 03:22:06 cloud-init[3102]: util.py[DEBUG]: Running command ['hostnamectl', 'set-hostname', 'ip-10-0-0-206.us-west-2.compute.internal my.local'] with allowed return codes [0] (shell=False, capture=True)
Nov 12 03:22:06 cloud-init[3102]: __init__.py[DEBUG]: Non-persistently setting the system hostname to ip-10-0-0-206.us-west-2.compute.internal my.local
```

## 回避策

- 手動でhostnameを正しくセットする
- user_dataにhostnameを正しくセットし直すscriptを設置する

## 希望する対策

local-hostnameの仕様のコンセンサスをcloud-initと取る