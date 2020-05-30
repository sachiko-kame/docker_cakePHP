# docker_cakePHP

💁‍♀️データベース接続の確認まで行っていますが最後確認はリライトの確認のみです。

## Step1 必要コンテナ作成まで！
クローン<br>
`$ git clone https://github.com/sachiko-kame/docker_cakePHP.git`

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
<img src="https://github.com/sachiko-kame/docker_cakePHP/blob/master/image1.png" width="500">

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

**cakePHP**フォルダ中身を移動する <br>
`$ mv my_app_name/* /var/www/html/`

output👁 <br>

```
root@d6e752c1a5b7:/var/www/html# ls
README.md  bin	composer.json  composer.lock  composer.phar  config  index.php	logs  my_app_name  phpcs.xml  phpunit.xml.dist	plugins  resources  src  templates  tests  tmp	vendor	webroot
```

ブラウザリロードして以下のようになっていればOK!!<br>
<img src="https://github.com/sachiko-kame/docker_cakePHP/blob/master/image2.png" width="500">

もう一度リロード以下のようになるのもOK!!<br>
<img src="https://github.com/sachiko-kame/docker_cakePHP/blob/master/image3.png" width="500">

コンテナから出る<br>
`$ exit`

apache　out🚪 <br>

## Step3 出ているエラーの解決
ファイルのアクセス権限の問題なので以下コマンドうつ<br>
`$ chmod -R 777 apach/html/logs` <br>
`$ chmod -R 777 apach/html/tmp ` <br>

ブラウザリロードしてエラーが消えていればOK<br>

## Step4 コンテナにデータベース作成、cakePHPのデータベース接続まで完了させる。
mysqlコンテナに入る。**602a4868b08b**は自分のものを入れてください  <br>
`$ docker exec -i -t 602a4868b08b bash` <br>

mysql　in🚪 <br>

```
root@602a4868b08b:/#
```

mysqlコンテナでmysqlにログインする <br>
`$ mysql -u root -p` <br>
パスワード聞かれたら　`example` 等つ <br> ちなみにパスワードは打っても表示されないようになってます！<br>

```
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.20 MySQL Community Server - GPL

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```

mysqlコンテナ内に今回使用するためのデータベースを作成する <br>
`$ create database mydb01;` <br>
`$ show databases;` <br> ←ただの確認コマンド

**mysqlコンテナ**から出る <br>
`$ exit;` を2回打てば出られます！ <br>

mysql　out🚪 <br>


データベース接続できるようにcakePHPファイルの修正をする
『/apach/html/config/app_local.php』
の`Datasources`を
以下のように変えます。

```
    'Datasources' => [
        'default' => [
          'className' => 'Cake\Database\Connection',
          'driver' => 'Cake\Database\Driver\Mysql',
          'persistent' => false,
          'encoding' => 'utf8mb4',
          'timezone' => 'UTC',
          'cacheMetadata' => true,

          'host' => 'mysql',
            /*
             * CakePHP will use the default DB port based on the driver selected
             * MySQL on MAMP uses port 8889, MAMP users will want to uncomment
             * the following line and set the port accordingly
             */
            //'port' => 'non_standard_port_number',

            'username' => 'root',
            'password' => 'example',

            'database' => 'mydb01',
            /**
             * If not using the default 'public' schema with the PostgreSQL driver
             * set it here.
             */
            //'schema' => 'myapp',

            /**
             * You can use a DSN string to set the entire configuration
             */
            'url' => env('DATABASE_URL', null),
        ],
```
できると以下のようにデータベース接続出来た形になっているかと思います <br>

<img src="https://github.com/sachiko-kame/docker_cakePHP/blob/master/image4.png" width="500">


## Step5 cakePHPリライトできるようにapache周辺の設定をする

apache　in🚪 <br>

apacheコンテナに入る <br>
`$ docker exec -i -t d6e752c1a5b7 bash` <br>

リライトモジュールを有効にするコマンドうつ <br>
`$ a2enmod rewrite` <br>
`$ service apache2 restart` <br>

apache　out (auto)🚪 <br>

再度Dockerのビルドし直し <br>
`$ docker-compose up --build -d` <br>

ここで以下のように移動されていないファイルあれば移動する
<img src="https://github.com/sachiko-kame/docker_cakePHP/blob/master/image5.png" width="500">

ipアドレス/pagesのような形で打ってipアドレスに戻ってくればOKです！

**my_app_name**フォルダは空なので削除します！　my_app_namefolder is empty. my_app_namefolder remove.


## Step6 ページを少し作成して表示を確認してみる。
/apach/htmlまで移動して以下コマンドでuserようのコントローラーを作成<br>
`$ bin/cake bake controller users`<br>

コマンドで作成した以下のファイルの修正<br>
/apach/html/src/Controller/UsersController.php<br>
以下のようにindex()中をコメントアウトします<br>

```
    public function index()
    {
        //$users = $this->paginate($this->Users);

        //$this->set(compact('users'));
    }
```


その後**/apach/html/templates**フォルダに**usersフォルダ**作成。その中に**index.php**ファイルを作成<br>
index.phpには以下のように適当に何か書いておけば問題ないです！<br>

```
<h1>sample!!!</h1>
```
<img src="https://github.com/sachiko-kame/docker_cakePHP/blob/master/image6.png" width="500">
