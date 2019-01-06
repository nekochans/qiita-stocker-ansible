# qiita-stocker-ansible
Webサーバーの構成管理を行う、Docker移行が終わったらこのプロジェクトは不要になる予定

# 事前準備

## Ansibleのインストール

MacOS上で以下のコマンドを実行して、Ansibleをインストールします。

`brew install ansible`

## SSH接続の設定

AnsibleはサーバーにSSH接続を行い、targetのサーバーにミドルウェアをインストール等を行います。

`~/.ssh/config` に以下のように記載しておくと良いでしょう。

```
Host *
ServerAliveInterval 60
    ForwardAgent yes
    ForwardX11Trusted yes
    ControlMaster auto
    ControlPath /Users/your_username/.ssh/.connect/%r@%h:%p
    ControlPersist 10

Host stg-qiita-stocker-bastion-1a
    HostName {ServerIP}
    Port 22
    User ec2-user
    IdentityFile ~/.ssh/stg_qiita_stocker_aws.pem

Host stg-qiita-stocker-api-1a
    HostName {ServerIP}
    Port 22
    User ec2-user
    IdentityFile ~/.ssh/stg_qiita_stocker_aws.pem
    ProxyCommand ssh -W %h:%p stg-qiita-stocker-bastion-1a
```

SSH接続に使うホスト名は通常なんでも良いですが、Ansibleは `inventory` と呼ばれるファイルに記載されているホスト名でSSH接続を行います。

その為 `hosts/stg/inventory` に書いてあるホスト名でSSH接続が出来る必要があります。

# 使い方

## ステージング環境に対して実行
`ansible-playbook site.yml -i hosts/stg/`

## 本番環境に対して実行
`ansible-playbook site.yml -i hosts/prod/`

# 参考URL

- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)
- [Ansibleをはじめる人に。](https://qiita.com/t_nakayama0714/items/fe55ee56d6446f67113c)
