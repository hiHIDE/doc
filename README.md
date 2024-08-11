# doc
各種ドキュメント

## Windows11でWSL2を使ってLinux（Ubuntu）環境を作る

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

### Linuxカーネル更新プログラムパッケージをインストールします。

1. 最新のパッケージをダウンロードします。
   
   https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
2. 更新プログラムを実行します。
3. 管理者権限のアクセスを求めるメッセージが表示されたら、「はい」を選択してインストールを実行します。

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




### 参考
[GitHub Docs 基本的な書き方とフォーマットの構文](https://docs.github.com/ja/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
