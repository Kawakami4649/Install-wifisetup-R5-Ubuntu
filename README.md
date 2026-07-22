# Install-wifisetup-R5-Ubuntu

Ubuntu 上で Raspberry Pi 5 を Wi-Fi アクセスポイント (AP) として動作させるためのセットアップツールです。

hostapd、dnsmasq、iptables による NAT 構成を自動設定し、Web ブラウザから AP 設定を変更できる管理画面を提供します。

---

# 概要

本パッケージは以下の機能を提供します。

* Wi-Fi アクセスポイント構築
* DHCP サーバ機能
* NAT によるインターネット共有
* Web ベースの設定画面
* SSID、パスワード変更
* AP IPアドレス変更
* DHCP設定変更
* DNS設定変更
* SSH有効／無効切替
* BIND9(DNSサーバ)有効／無効切替
* Wi-Fiインターフェース名自動検出
* USB Wi-Fiアダプタ変更時の自動追従

---

# 動作環境

* Ubuntu
* Raspberry Pi 5
* Apache2 (CGI有効)
* hostapd
* dnsmasq
* iptables-persistent
* netplan

---

# ファイル構成

| ファイル                     | 内容                |
| ------------------------ | ----------------- |
| install.sh               | インストールスクリプト       |
| update_wifi_config.sh    | Wi-Fiインターフェース自動更新 |
| ap_config.cgi            | Web設定画面           |
| set_time.cgi             | 時刻設定CGI           |
| ap_config.ini            | 管理者パスワード設定        |
| setconf.tar.gz           | 各種設定ファイル          |
| network-config.tar.gz    | ネットワーク設定          |
| redirect-enabled.conf    | Apache設定          |
| 99-usb-autosuspend.rules | USB省電力設定          |
| rc.local                 | 起動時スクリプト          |
| rc-local.service         | systemd登録         |
| add_hosts.sh             | hostsファイル追加       |

---

# インストール

展開後、

```bash
cd Install-wifisetup-R5-Ubuntu
```

インストールを実行します。

```bash
chmod +x install.sh
./install.sh
```

インストール内容

* hostapd インストール
* dnsmasq インストール
* hostapd 自動起動設定
* dnsmasq 自動起動設定
* iptables NAT設定
* iptables 永続化
* CGIプログラム配置
* Web画面用画像配置
* rc.local 有効化
* USB Wi-Fi省電力無効設定

---

# ネットワーク構成例

```
             Internet
                 │
              eth0
                 │
         +----------------+
         | Raspberry Pi 5 |
         +----------------+
                 │
             wlan0/wlx*
                 │
        192.168.10.0/24
                 │
         Wi-Fi クライアント
```

デフォルトAPアドレス

```
192.168.10.1
```

---

# Web管理画面

ブラウザから

```
http://192.168.10.1/cgi-bin/ap_config.cgi
```

へアクセスします。

設定可能項目

### AP設定

* SSID
* WPA2パスワード
* チャンネル
* 2.4GHz/5GHz

### DHCP設定

* DHCP有効/無効
* 配布開始アドレス
* 配布終了アドレス
* リース時間

### DNS設定

* WAN側DNS
* WLAN側DNS

### システム設定

* SSH有効/無効
* BIND9有効/無効

---

# Wi-Fiインターフェース自動検出

USB Wi-Fiアダプタによって

```
wlan0
```

または

```
wlxXXXXXXXX
```

となるインターフェース名を自動判別し、

* hostapd.conf
* dnsmasq.conf
* netplan設定
* iptablesルール

を自動更新します。

実行

```bash
sudo update_wifi_config.sh
```

---

# hosts登録

以下を追加します。

```text
192.168.10.1 chat.local
```

クライアントから

```
http://chat.local
```

でアクセス可能になります。

---

# 管理者パスワード

設定ファイル

```text
ap_config.ini
```

初期値

```ini
admin_password=secret
```

必要に応じて変更してください。

---

# 使用サービス

* hostapd
* dnsmasq
* Apache2
* systemd
* iptables
* netplan
* （オプション）bind9

---

# 注意事項

* WAN側インターフェースは `eth0` 固定です。
* AP側インターフェースは自動検出されます。
* Apache2 の CGI 機能が有効である必要があります。
* hostapd の起動失敗時は Wi-Fi アダプタの AP モード対応状況を確認してください。
* `update_wifi_config.sh` 実行後は `netplan apply` が実行されます。
* BIND9 はデフォルトでは無効です。

---

# ライセンス

利用者の責任において自由に使用・改変してください。

保証はありません。
運用前に十分な評価を行った上で使用してください。

