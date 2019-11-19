# Tableau-Server-GCP-init
GCPでTableau Serverを立てるときの基本手順


## Google Compute Engine
- n1-standard-16
- ubuntu-1804
- SSD 128GB〜
- ファイアウォール
  - HTTP トラフィックを許可する
  - HTTPS トラフィックを許可する
- ネットワークタグ
  - allow-tableau-server-test
  - http-server
  - https-server
- https://help.tableau.com/current/server-linux/ja-jp/ts_gcp_virtual_machine_selection.htm

```
gcloud beta compute --project=excellent-guard-91807 instances create ishihara-tableau-server-test-1 --zone=us-east1-b --machine-type=n1-standard-16 --network=default --network-tier=PREMIUM --maintenance-policy=MIGRATE --service-account=1049054663987-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --tags=allow-tableau-server-test,http-server,https-server --image=ubuntu-1804-bionic-v20191113 --image-project=ubuntu-os-cloud --boot-disk-size=128GB --boot-disk-type=pd-ssd --boot-disk-device-name=ishihara-tableau-server-test-1 --reservation-affinity=any

```

## VPCネットワーク > ファイアウォール ルール
- 上り tcp:8850

## シェルコマンド
```
$ apt-get update

$ apt-get upgrade

$ curl https://downloads.tableau.com/tssoftware/tableau-server-2019-4-0_amd64.deb > tableau-server-XXXX.deb

$ sudo apt install tableau-server-XXXX.deb

# ↑フォルダ名は最新のものに修正した上で実行すること
$ ls /opt/tableau/tableau_server/packages/scripts.20194.19.1105.1444

$ echo "export LC_CTYPE=en_US.UTF-8" >>~/.bashrc
$ echo "export LC_ALL=en_US.UTF-8" >>~/.bashrc

$ sudo ./initialize-tsm --accepteula

$ logout

$ tsm initialize -r -u ishihara -p start-tableau-server
$ sudo useradd tableau_admin
$ sudo passwd tableau_admin

$ tsm licenses activate -t
$ tsm register --template >template.json

# {
#   "zip" : "[value]",
#   "country" : "[value]",
#   "city" : "[value]",
#   "last_name" : "[value]",
#   "industry" : "[value]",
#   "eula" : "[value]",
#   "title" : "[value]",
#   "phone" : "[value]",
#   "company" : "[value]",
#   "state" : "[value]",
#   "department" : "[value]",
#   "first_name" : "[value]",
#   "email" : "[value]"
# }

$ tsm register --file template.json 
$ tsm status -v # 現在のステータス確認
$ tsm settings import -f /opt/tableau/tableau_server/packages/scripts.20194.19.1105.1444/config.json
$ tsm pending-changes apply
$ tsm initialize --start-server --request-timeout 1800


```

## 結局このページを見ればいいね
https://help.tableau.com/current/server-linux/ja-jp/jumpstart.htm
