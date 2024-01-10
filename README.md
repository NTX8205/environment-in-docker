# environment-in-docker

- [environment-in-docker](#environment-in-docker)
  - [說明](#說明)
  - [docker compose 使用方式](#docker-compose-使用方式)
    - [設定檔](#設定檔)
    - [container 啟動/關](#container-啟動關)
      - [啟動並新建](#啟動並新建)
      - [關閉並刪除所有容器](#關閉並刪除所有容器)
  - [與其他專案結合使用](#與其他專案結合使用)
  - [服務介紹](#服務介紹)
    - [nginx](#nginx)
      - [nginx 開放埠號](#nginx-開放埠號)
      - [nginx 設定檔](#nginx-設定檔)
      - [重新載入 nginx 設定檔 (服務不會中斷)](#重新載入-nginx-設定檔-服務不會中斷)
    - [PHP](#php)
      - [PHP 設定檔](#php-設定檔)
      - [composer 使用](#composer-使用)
    - [mariadb](#mariadb)
      - [mariadb 設定檔](#mariadb-設定檔)
      - [資料庫位置](#資料庫位置)
    - [phpmyadmin](#phpmyadmin)

## 說明

使用 docker 建置各種環境，並集中至這裡

目前環境使用服務 :

- nginx
- PHP
- mariadb
- phpmyadmin

## docker compose 使用方式

### 設定檔

所有的 container 設定檔都在 `docker-compose.yml` 裡，更動設定後要重啟容器讓設定生效

### container 啟動/關

#### 啟動並新建

```bash

# -d 為在背景執行

docker compose up -d # (會拿舊的 image 緩存來建置)

docker compose up -d --build

```

#### 關閉並刪除所有容器

```bash
docker compose down
```

## 與其他專案結合使用

本專案只有環境的設定，並不會放置其他專案的內容，因此請將其他專案跟本專案放在同一個資料夾底下，確保正常運行

```bash
# 範例

├── Folder
│   ├── project  <--其他專案
│   └── environment <--服務與環境
```

## 服務介紹

### nginx

#### nginx 開放埠號

nginx 目前開放 80 port

#### nginx 設定檔

路徑 : `nginx/config/conf.d/default.conf`

相關設定請在上述路徑的檔案中更改，更改完記得重新載入 nginx 設定檔，讓設定生效

#### 重新載入 nginx 設定檔 (服務不會中斷)

```bash
docker compose exec nginx /etc/init.d/nginx reload
```

### PHP

#### PHP 設定檔

路徑 : `php/config/php.ini`

相關設定請在上述路徑的檔案中更改，更改完記得重新啟動容器，讓設定生效

#### composer 使用

在 PHP 的容器中有安裝 composer，要使用請輸入下面的指令

> 目前 composer 是以 root 權限執行，所以使用 composer 生成的檔案權限為 root
> 需要注意

```bash
# docker compose exec <container> <command>

## 範例
docker compose exec php composer --help

```

執行的預設位置為指定專案(`../project`)

### mariadb

#### mariadb 設定檔

路徑 : `mariadb/config/mariadb.cnf`

相關設定請在上述路徑的檔案中更改，更改完記得重新啟動容器，讓設定生效

#### 資料庫位置

路徑 : `mariadb/data/`

如有更改資料庫的內容請記得刪除 `data` 裡的所有內容，讓資料庫重置

### phpmyadmin
