# docker_cakePHP

## Step1 必要コンテナ作成まで！
クローンして**docker-compose.yml**ファイルなどあるところで以下コマンド<br>
`$ docker-compose up --build -d` <br>

無事にコンテナが作成されたかの確認 <br>
`$ docker ps`  <br>

output👁

```
d6e752c1a5b7        2020_5_30_template_cakephp_apach   "docker-php-entrypoi…"   4 seconds ago       Up 4 seconds        0.0.0.0:80->80/tcp                  sample-apache-container1
602a4868b08b        2020_5_30_template_cakephp_mysql   "docker-entrypoint.s…"   4 seconds ago       Up 4 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   sample-mysql1
```

ip調べる  <br>
`$ docker-machine ip default` <br>

output👁 <br>

```
***.***.**.***
```

出たipをブラウザに貼り確認。 <br>
![イメージ](https://github.com/sachiko-kame/docker_cakePHP/blob/master/image1.png)

## Step2 apacheコンテナに入りcakePHPインストールまで
アパッチコンテナ入る。以下コマンド。**d6e752c1a5b7**は自分のものを入れてください <br>
`$ docker exec -i -t d6e752c1a5b7 bash` <br>

apache　in🚪 <br>

```
root@d6e752c1a5b7:/var/www/html#
```

**composer**入れる <br>
`$ curl -sS https://getcomposer.org/installer | php` <br>

**composer**で**cakePHP**入れる <br>
`$ php composer.phar create-project --prefer-dist cakephp/app:4.* my_app_name` <br>
Set Folder Permissions ? (Default to Y) [Y,n]?と聞かれてらyにしてエンター！<br>

****cakePHPフォルダ**を移動する <br>
`$ mv my_app_name/* /var/www/html/`

output👁 <br>

```
root@d6e752c1a5b7:/var/www/html# ls
README.md  bin	composer.json  composer.lock  composer.phar  config  index.php	logs  my_app_name  phpcs.xml  phpunit.xml.dist	plugins  resources  src  templates  tests  tmp	vendor	webroot
```

ブラウザリロードして以下のようになっていればOK!!<br>
![イメージ](https://github.com/sachiko-kame/docker_cakePHP/blob/master/image2.png)

もう一度リロード以下のようになるのもOK!!<br>
![イメージ](https://github.com/sachiko-kame/docker_cakePHP/blob/master/image3.png)

コンテナから出る<br>
`$ exit`

apache　out🚪 <br>

## Step3 
