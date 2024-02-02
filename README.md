# environment-in-docker

- [environment-in-docker](#environment-in-docker)
  - [說明](#說明)
    - [須知](#須知)
  - [目前環境使用服務](#目前環境使用服務)
    - [其他功能](#其他功能)
  - [docker compose 使用方式](#docker-compose-使用方式)
    - [設定檔](#設定檔)
    - [container 啟動/關](#container-啟動關)
      - [啟動並新建](#啟動並新建)
      - [關閉並刪除所有容器](#關閉並刪除所有容器)
  - [與其他專案結合使用](#與其他專案結合使用)
    - [範例](#範例)
    - [Port 使用](#port-使用)
    - [網址與路徑](#網址與路徑)
  - [主要服務介紹](#主要服務介紹)
    - [nginx](#nginx)
      - [nginx 設定檔](#nginx-設定檔)
      - [重新載入 nginx 設定檔 (服務不會中斷)](#重新載入-nginx-設定檔-服務不會中斷)
    - [PHP](#php)
      - [PHP 設定檔](#php-設定檔)
      - [composer 使用](#composer-使用)
    - [nodejs](#nodejs)
      - [額外功能](#額外功能)
    - [mariadb](#mariadb)
      - [mariadb 設定檔](#mariadb-設定檔)
      - [使用者帳號密碼](#使用者帳號密碼)
      - [資料庫位置](#資料庫位置)
    - [phpmyadmin](#phpmyadmin)
      - [使用者預設帳號密碼](#使用者預設帳號密碼)
  - [其他服務](#其他服務)
    - [注意](#注意)
    - [portainer](#portainer)
      - [帳號密碼](#帳號密碼)
    - [swagger-editor](#swagger-editor)

## 說明

使用 docker 建置各種環境，並集中至這裡做個總調整及整合。

### 須知

本項目使用 Laravel 為後端、Vue 為前端使用，以下說明會以這為主。  
設定是以本身參與的項目做出的結果，如果不符合自身需求請自行更改設定。

## 目前環境使用服務

- nginx
- PHP
- mariadb
- phpmyadmin
- nodejs

### 其他功能

- portainer
- swagger-editor

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

前端項目預設名稱為`frontend`  
後端項目預設名稱為`backend`  
如有不同請更改 `docker-compose.yml` 的 `volumes` 內容

### 範例

更改前

```yaml
  php:
    build: ./php/
    image: php8-1:1.0
    networks:
      - lnmp-network
    working_dir: /var/www/backend
    volumes:
      - ../backend:/var/www/backend:z  <-- 更改最前面的 backend 為自己的項目名稱
```

更改後

```yaml
  php:
    build: ./php/
    image: php8-1:1.0
    networks:
      - lnmp-network
    working_dir: /var/www/backend
    volumes:
      - ../<project name>:/var/www/backend:z  <-- 更改這裡的項目名稱
```

項目位置放置

```bash
├── Folder
│   ├── frontend  <--前端項目名稱
│   ├── backend  <--後端項目名稱
│   └── environment <--服務與環境(本專案)
```

### Port 使用

|  服務  | 使用埠號 |
| ----- | ----- |
| nginx  | 80(前端)、8080(後端) |
| nodejs  | 5173 port(提供測試使用) |
| mariadb  | 3306 |
| phpmyadmin  | 81 |
| portainer  | 9000(http)、9443(https) |
| swagger-editor  | 8081 |

### 網址與路徑

預設主機名稱為 localhost，以下是目前已有的路徑。

|  路徑  | 網址 | 說明 |
| ----- | ----- | ----- |
| localhost  | <http:localhost> | 前端網址 會自動導向。 `localhost/app` |
| localhost/app  | <http:localhost/app> | 前端主要網址，以 Vue 的設定為基準，可能會有些許不同。 |
| localhost/api  | <http:localhost/api> | api 使用網址 (無法看見 api 相關文件) ，請至 `localhost:8080` 查詢。 |
| localhost:8080  | <http:localhost:8080> | 後端網址  會自動導向 laravel 的路徑設定。 |
| localhost:8081  | <http:localhost:8081> | swagger-editor 網址。 |
| localhost:9000  | <http:localhost:9000> | portainer http 網址。 |
| localhost:9443  | <https:localhost:9443> | portainer https 網址。 |

## 主要服務介紹

### nginx

#### nginx 設定檔

路徑 : `nginx/config/conf.d/default.conf`

相關設定請在上述路徑的檔案中更改，更改完記得重新載入 nginx 設定檔，讓設定生效

#### 重新載入 nginx 設定檔 (服務不會中斷)

```bash
docker compose exec nginx /etc/init.d/nginx reload
```

### PHP

php 版本為 8.1

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

執行的預設位置為(`../backend`)

### nodejs

目前用於 Vue 相關專案
執行的預設位置為(`../frontend`)

#### 額外功能

內涵 git 供測試使用，需要的話可改動`docker-compose.yml`的 `nodejs`的設定

將註解的部分取消，並將下列內容改動

```yaml
# 原始內容
  nodejs:
    # nodejs and git
    # build: ./nodejs/
    # image: node:1.0

    # only nodejs
    image: node:21.6.1-slim
```

更改後

```yaml
  nodejs:
    # nodejs and git
    build: ./nodejs/
    image: node:1.0

    # only nodejs
    # image: node:21.6.1-slim
```

### mariadb

#### mariadb 設定檔

路徑 : `mariadb/config/mariadb.cnf`

相關設定請在上述路徑的檔案中更改，更改完記得重新啟動容器，讓設定生效

#### 使用者帳號密碼

有需要請自行更改

- root 密碼: very-secret
- 使用者名稱: cms_user
- 使用者密碼: cms_secret
- 資料庫名稱: cms

#### 資料庫位置

路徑 : `mariadb/data/`

如有更改資料庫的內容請記得刪除 `data` 裡的所有內容，讓資料庫重置

### phpmyadmin

#### 使用者預設帳號密碼

- root 密碼: very-secret
- 使用者名稱: cms_user
- 使用者密碼: cms_secret

## 其他服務

### 注意

在這裡的的服務是與主要服務分開的，要啟動服務請到相對應的資料夾裡開啟服務。

### portainer

#### 帳號密碼

預設帳號為 admin，密碼為 adminadminadmin

### swagger-editor

預設檔案放置位置 `swagger/openapi.yaml`
