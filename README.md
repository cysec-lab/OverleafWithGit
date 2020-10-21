# watchOverleaf

Overleaf プロジェクトを Git/GitHub での差分管理する機能を Overleaf の無償版に追加する

## 依存

- Docker (インストール: https://docs.docker.com/get-docker/)

## セットアップ

```sh
$ git clone https://github.com/TakuKitamura/watchOverleaf.git
$ cd watchOverleaf
$ sudo docker-compose up -d
$ sudo docker ps ## 出力は例
CONTAINER ID        IMAGE                         COMMAND                  CREATED             STATUS                    PORTS                NAMES
ffd1bee143c1        takukitamura/watch-overleaf   "/sbin/my_init"          29 seconds ago      Up 2 seconds              0.0.0.0:80->80/tcp   watch-overleaf
99475ae05634        redis:5                       "docker-entrypoint.s…"   43 hours ago        Up 13 seconds             6379/tcp             redis
3ab5222f1753        mongo                         "docker-entrypoint.s…"   4 days ago          Up 13 seconds (healthy)   27017/tcp            mongo
$ docker exec -it ffd1bee143c1 bash ## 以下, watch-overleafコンテナ内
root@ffd1bee143c1:/# cd
root@ffd1bee143c1:~# ls
watchOverleaf
root@ffd1bee143c1:~/watchOverleaf# git pull origin master

```
