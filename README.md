# WatchOverleaf

- ###### Overleaf プロジェクトを Git/GitHub で差分管理する機能を Overleaf の無償版(Community)に追加する
- ###### Overleaf プロジェクトを編集し, PDF が出力されるタイミングでリポジトリに PUSH する

Overleaf とは (https://ja.overleaf.com/)
Overleaf Community版 (https://github.com/overleaf/overleaf)

## 依存関係

- Docker
  https://docs.docker.com/get-docker/

## セットアップ

### GitHub

GitHub の個人アクセストークンを事前に所得する
https://docs.github.com/ja/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token
権限は 'repo' のみ選択すること
トークンは厳重に扱うこと

### CUI 操作

```sh
$ git clone https://github.com/TakuKitamura/watchOverleaf.git
$ cd watchOverleaf
$ sudo docker-compose up -d # サイズは10GB程度.削減する場合は, Latexの不要なパッケージを削除する
$ sudo docker exec sharelatex /bin/bash -c "cd /var/www/sharelatex; grunt user:create-admin --email=admin@example.com" # Adminユーザの作成, 実行後に表示されるURLにアクセスしパスワードを設定する
$ # http://localhost に設定した認証情報でアクセスし, 適当なOverLeafプロジェクトを作成する
$ sudo docker ps ## 出力は例
CONTAINER ID        IMAGE                         COMMAND                  CREATED             STATUS                    PORTS                NAMES
ffd1bee143c1        takukitamura/watch-overleaf   "/sbin/my_init"          29 seconds ago      Up 2 seconds              0.0.0.0:80->80/tcp   watch-overleaf
99475ae05634        redis:5                       "docker-entrypoint.s…"   43 hours ago        Up 13 seconds             6379/tcp             redis
3ab5222f1753        mongo                         "docker-entrypoint.s…"   4 days ago          Up 13 seconds (healthy)   27017/tcp            mongo
$ docker exec -it ffd1bee143c1 bash ## 以下, watch-overleafコンテナ内
root@ffd1bee143c1:/# cd ~/watchOverleaf/
root@ffd1bee143c1:~/watchOverleaf# git pull origin master
root@ffd1bee143c1:~/watchOverleaf# cp main.py demo.py # ソースコードをコピーする
root@ffd1bee143c1:~/watchOverleaf# vim demo.py ## プログラム内に, 書き換え必須 ✅もしくは, 書き換えが必要かもしれない と書いてある変数を適宜書き換え保存する
~~~
# ユーザ変数

# NginxのAccessログのパス, 書き換えの必要が出るかもしれない
NGINX_LOG_PATH = '/var/log/nginx/access.log'

# OverleafのプロジェクトID, ユーザID
# 1. latex編集画面で, Webデベロッパーツールを開く
# 2. コンソールで以下の入力をして出力されるものが, プロジェクトID, ユーザID
# > project_id
# "5f8d2f5e40a9cd007604f46b"
# > user_id
# "5f8f784af6e341007b878a51"
#

# 書き換え必須 ✅
PROJECT_ID = '5f8d2f5e40a9cd007604f46b'  # プロジェクトを一意に決める

# 書き換え必須 ✅
USER_ID = '5f8f784af6e341007b878a51'  # ユーザを一意に決める

# 書き換え必須 ✅
GITHUB_USER_NAME = 'TakuKitamura'  # GitHubのID

# 書き換え必須 ✅
GITHUB_REPO_NAME = 'verified-mqtt-parser-paper'  # Overleafプロジェクトをホスティングしたいリポジトリ名

# 書き換えが必要かもしれない
GIT_BRANCH_NAME = 'master'  # GitHubリポジトリ上でホスティングするブランチ名


# 書き換えが必要かもしれない
PAPER_DIR_NAME = 'overleaf'  # リポジトリルートに作成されるディレクトリ名

# 書き換えが必要かもしれない
EXCLUDE_LIST = './exclude_list'  # OverLeafプロジェクトから余計なファイルがPUSHされた場合は, ここに追加する

~~~
root@ffd1bee143c1:~/watchOverleaf# vim ~/.netrc ## gitコマンドの認証を自動で行う .netrcを作成する. 下記は例
machine github.com
        login TakuKitamura
        password xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
root@ffd1bee143c1:~/watchOverleaf# python3 demo.py
root@ffd1bee143c1:~/watchOverleaf# \
```

起動後, OverLeaf プロジェクト内の文書を変更し, PDF が更新されるのを確認する.そして, その変更がリポジトリ直下の `overleaf` ディレクトリに反映されていれば動作している.
永続化は `$ nohup python3 demo.py &` などする

## トラブルシューティング

> プログラムは動作しているが, リポジトリに変更が反映されない

```sh
コンテナ内でNGINX_LOG_PATHが指す先のログが正しく反映されているか確認する.
例えば, $ tail /var/log/nginx/access.log などでログが更新されているか, NGINX_LOG_PATHが間違っていないか確認する
```

> OverLeaf で文書を編集する際のカーソルの位置が変だ

```sh
どうやら, フォントが等幅でないとそうなるんだそう.
なので, ブラウザのフォント設定から等幅フォントを選択すればなおる.
```

> プログラムが動かない

```sh
すいませんが, Pull Requestやissueください.
まだまだ, バグはあると思います.
```

## TODO

- ユーザが与える変数をコマンドオプションから与えられるようにする
- git の認証情報をプログラムごとに分けられるようにする
- リファクタリング
