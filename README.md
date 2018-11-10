# STNS as a Service

STNSを利用した、Linux名前解決のSaaSを[ロリポップ！マネージドクラウド](https://mc.lolipop.jp)で提供しています。このリポジトリを利用することでどなたでもSTNSを利用することが出来ます。

# PR方法
userを追加します。`id`については未使用の値を探して設定してください。また `group_id` は `10000` を設定すれば、デフォルトで定義されている `stns` グループに所属します。独自のグループが定義したい場合は、` groups.conf` に定義してください。`keys`には公開鍵を指定してください。

```
$ vi users.conf
[users.pyama]
id = 10000
group_id = 10000
keys = ["ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDZx4lWgqP3VDoypCihYrC7SZRWVuvqejTeo6Jf5MvS4m1U6NDGcdUHoi3S0LuB6IW3x7NkNYrGMvucQxHdIqL9iNbtDvXVMJwE1w77htGCvEl3yYgdqCgIuoK/IwOiQFUaSjmTzX87WC3ZIolGp/nUO1nhOKEM9b6yETEzGAkg4Gx0sSeI7IcBkyXzboS/WiVviEvyt/6KhrpoAiGCt/VhKpZVRa+V+J/WL0nzYcM5u+yQrYddOA6zHcd5xe/+chtg7LzC/rA6anCEP0GVAT9eYHkz9nR9Z+gq4mg/Yoi0MZp7bAfp3RZi4GM5Ey5RZHWUaVhjP0QwdKPLeMHTuAWd"]
```

ファイルを編集したら、PRを投げてもらえれば @pyama86 がレビュー後マージし、自動でデプロイされます。

# サーバでSTNSを利用した名前解決をする。
[ドキュメントサイト](https://stns.jp)を参考にインストールしてください。

```
$ curl -fsSL https://repo.stns.jp/scripts/yum-repo.sh | sh
$ yum -y install libnss-stns-v2
$ vi /etc/stns/client/stns.conf
$ cat /etc/stns/client/stns.conf
api_endpoint     = "https://stns.lolipop.io/v1/"
$ vi /etc/nsswitch.conf
$ cat /etc/nsswitch.conf |grep stns
passwd:     files stns
shadow:     files stns
group:      files stns
$ id pyama
uid=10000(pyama) gid=10000(stns) groups=10000(stns)
```

SSHログインもSTNSを利用する場合、下記を参考にSSHの設定してください。

```
$ vi /etc/ssh/sshd_config
$ cat /etc/ssh/sshd_config
AllowUsers pyama
AuthorizedKeysCommand /usr/lib/stns/stns-key-wrapper
AuthorizedKeysCommandRunAs root
ChallengeResponseAuthentication no
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
UsePAM yes
$ service sshd restart
```

