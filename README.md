# 各種ドキュメント

## Windows11でWSL2を使ってLinux（Ubuntu）環境を作る
<details>
<summary>WSLを有効にする</summary>

### WSLを有効にする

1. 「スタートボタン」をクリックします。
2. 検索窓に「コントロール」または「control」と入力します。
3. 検索結果に表示された「コントロールパネル」を選択します。
4. 「プログラム」を選択します。
5. 「Windowsの機能の有効化または無効化」を選択し下記を有効にします。
  
  - Linux用Windowsサブシステム
  - Windowsハイパーバイザープラットフォーム
  - 仮想マシンプラットフォーム

  <img src="/img/Enabling_or_Disabling_in_Windows.png">
  
6. インストール完了後「今すぐ再起動」を選択します。
</details>

<details>
<summary>Linuxカーネル更新プログラムパッケージをインストールする</summary>

### Linuxカーネル更新プログラムパッケージをインストールする

1. 最新のパッケージをダウンロードします。
   
   https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
2. 更新プログラムを実行します。
3. 管理者権限のアクセスを求めるメッセージが表示されたら、「はい」を選択してインストールを実行します。

</details>

<details>
<summary>Ubuntuをインストール</summary>

### Ubuntuをインストール

1. Microsoft Storeで「Ubuntu」を検索します。
2. 最新バージョンの「Ubuntu 24.04 LTS」などをインストールします。
> バージョンの記載がない「Ubuntu」は、LTSがリリースされる度に自動でアップデートされていくようなので、環境に影響がでる可能性があります。
3. Ubuntuを起動し、セットアップを完了します。
> ユーザー名/パスワードに任意の値を設定します。
4. Windows PowerSellで以下を実行します。
``` 
wsl -l -v
```

```
    NAME              STATE           VERSION
  * docker-desktop    Running         2
    Ubuntu-24.04      Running         2
```
5. Windows PowerShellで以下を実行し、DNSサーバーのIPアドレスを確認します。
```
ipconfig /all
```

```
  DNS サーバー. . . . . . . . . . . . .: ***.***.***.***
```
6. Ubuntuのコマンドでresolv.confをDNSサーバーのIPアドレスに書き換えます。
```
vi /etc/resolv.conf
```

```
  ---------------------------
  nameserver ***.***.***.***
  ---------------------------
```
7. WSL2の設定は完了です。
</details>

### 参考にさせていただきました
[WindowsでWSL2＋Docker＋PHP開発環境の構築](https://zenn.dev/babyjob/articles/38955cb1956e72)


## WSL2のLinux（Ubuntu）でPHP+MySQL+Laravel環境を作る

<details>
<summary>インストール</summary>

### インストール

1. Ubuntuのコマンドを開きます。
2. インストール可能なパッケージを更新します。
```
sudo apt-get update
```
3. phpをインストールします。
```
sudo apt install -y php

// phpを確認します
php -v

  // 最新バージョン
  PHP 8.3.6 (cli) (built: Jun 13 2024 15:23:20) (NTS)
  Copyright (c) The PHP Group
  Zend Engine v4.3.6, Copyright (c) Zend Technologies
    with Zend OPcache v8.3.6, Copyright (c), by Zend Technologies
```
4. MySQLをインストールします。
```
sudo apt install -y mysql-server

// ステータス確認
sudo service mysql status

  ● mysql.service - MySQL Community Server
     Loaded: loaded (/usr/lib/systemd/system/mysql.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-08-12 13:08:59 JST; 15s ago
    Process: 14819 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=exited, status=0/SUCCESS)
   Main PID: 14827 (mysqld)
     Status: "Server is operational"
      Tasks: 38 (limit: 9425)
     Memory: 370.7M ()
     CGroup: /system.slice/mysql.service
             └─14827 /usr/sbin/mysqld
// 起動
sudo service mysql start

/**
初期設定
・rootユーザーのパスワード設定
・匿名ユーザーの削除
・外部（ローカルホスト以外）からアクセス可能なrootユーザーの削除
・testデータベースの削除
・「test_」から始まるデータベースへの接続権限の削除
・特権テーブルのリロード（更新内容の反映）
*/
sudo mysql_secure_installation

// ルートでログイン
sudo mysql -u root -p

// 文字コードの確認 (utf8mb4)
mysql> show variables like '%char%';

// laravelから接続するデータベースを作成
mysql> create database laravel;
mysql> show databases;

// mysql_native_password へ認証方式を設定
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '********';

// 確認
mysql> SELECT user, host, plugin FROM mysql.user;

// ポート番号の確認 (3306)
mysql> show variables like 'port';



// ログアウト（MySQL）
mysql> \q
Bye
```
5. phpの拡張機能をインストールします。
```
sudo apt install php8.3-bcmath php8.3-mbstring php8.3-xml php8.3-zip php8.3-mysql

// 確認
php -m
　　[PHP Modules]
　　bcmath
　　mbstring
    mysqlnd
　　xml
　　zip
```
6. Composerをインストールします。[Command-line installation](https://getcomposer.org/download/)より
```
// インストール
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"

// 移動
sudo mv  composer.phar /usr/local/bin/composer

// 確認
composer -V

  Composer version 2.7.7 2024-06-10 22:11:12
  PHP version 8.3.6 (/usr/bin/php8.3)
  Run the "diagnose" command to get more detailed diagnostics output.
```
7. laravelをインストールします。
```
// Laravel プロジェクトの作成 {example-app} フォルダ名
composer create-project laravel/laravel example-app

// デバックツールのインストール
composer require barryvdh/laravel-debugbar
```

8. Laravelのローカル開発サーバーを起動します。
```
// 移動
cd example-app

// Laravelの.envを設定
vi .env

  DB_CONNECTION=mysql
  DB_HOST=127.0.0.1
  DB_PORT=3306
  DB_DATABASE=laravel
  DB_USERNAME=root
  DB_PASSWORD=*******

// テーブル作成
php artisan migrate

// 起動
php artisan serve &
```

</details>

### 参考にさせていただきました
[WSL2: UbuntuでPHP+MySQL+Laravelインストールの手順](https://qiita.com/yoshi_yast/items/dcaffa47758a5aa4953b)

## Visual Studio CodeのインストールとUbuntu on WSL2への接続

<details>
<summary>VS Code</summary>

### VS Codeインストーラーのダウンロード

1. インストーラーをダウンロードします。
  [VS Codeダウンロードページ](https://code.visualstudio.com/download)にアクセスし、インストーラーをダウンロードします。

2. ダウンロードしたインストーラーを実行します。
  「PATHへの追加(再起動後に使用可能)」にチェックが入っている状態で「次へ」をクリックします。
  <img src="/img/VScodeInstall.png">
  
    VS Codeのインストールが完了し、Windowsを再起動します。

4. UbuntuからVS Codeを起動します。
  Ubuntu上で下記コマンドを実行することでUbuntuと接続した状態で、VS Codeが起動できます。

```
// VS Codeを開きたいディレクトリに移動
cd example-app

// 
code .

  // 初回の実行時は、VS Code Server for Linux がInstallされます。
  Installing VS Code Server for Linux x64 (eaa41d57266683296de7d118f574d0c2652e1fc4)
  Downloading: 100%
  Unpacking: 100%
  ...
```

</details>

### 参考にさせていただきました
[Visual Studio CodeのインストールとUbuntu on WSL2への接続](https://qiita.com/hiropon1839/items/0f48d85733ad0c85b3b1)

# 参考文献
[GitHub Docs 基本的な書き方とフォーマットの構文](https://docs.github.com/ja/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
