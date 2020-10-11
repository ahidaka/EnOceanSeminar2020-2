# nodejs-setup

### EnOcean Seminar textbook in 2020 volume 2 / node.js setup

EnOcean IoTシステム開発コンテスト！meet up：node.js セットアップ

- GitHub Page<br/>
    https://ahidaka.github.io/EnOceanSeminar2020-2/nodejs-setup.md

<br/>

### [EnOcean IoTシステム開発コンテスト！meet up 募集・資料ページ](https://algyan.connpass.com/event/183265/)

- URL<br/>
    https://algyan.connpass.com/event/183265/

<br/>

### [メインページ：EnOcean IoTシステム開発コンテスト！meet up 実習用テキスト](README.md)

- GitHub Page<br/>
    https://ahidaka.github.io/EnOceanSeminar2020-2

<br/>

![EnOceanマルチセンサー STM550J](image/stm550jp50.jpg)
##### 写真：EnOceanマルチセンサー STM550J
<br/>

---
## はじめに
---

この文書は、[EnOcean IoTシステム開発コンテスト！meet up 実習用テキスト](README.md)の、node.js サンプルプログラムの実行で発生したトラブルを解決するためのヒントを載せています。他の用途で参考にして頂いても構いませんが、本書の目的は前述のnode.js 各サンプルプログラムを実行できる環境を構築することだけですので、その場合は自己責任でお願いします。

なお前述の node.js サンプルプログラムが問題無く動作している方は、何もしない事をお勧めします。

<br/>

---
## node.js のモジュールの配置
---

**node.js** では **npm** というパッケージ管理ツールを使用して公開されている膨大な量のパッケージを管理します。しかしインストールするモジュールのバージョンアップが頻繁にあり、またインストール先もかなり自由度があることから、古いインストールが残っていた場合等、正しく動作する環境をインストール（構築）するのが難しい場合があります。

各公開パッケージモジュールは、**mode_modules** というディレクトリに配置されるのですが、これが環境によっては、様々な場所に出来ていたりします。また、**npm** 自体がモジュールなので、それもこの様々な場所にあるという事を良く覚えておきましょう。

通常 **mode_modules** はnpmでローカルインストールの場合は、ホームディレクトリまたは、現在実行中のディレクトリの下、または別の定義ファイル設定に従って作成されます。この様に様々な場所にモジュールが配置される可能性があるのと、前項のサンプルプログラムは権限の都合でスーパーユーザーで実行する場合があるため、全て必ず **-g** 付きで実行してインストールする様にします。これが第一のポイントです。

次のポイントは、Raspbian等では、**npm** によるインストールで **-g** 付きでインストールしたのにも関わらず、**node** ではそのインストール先が見えない（検索パスに入らない）というほとんどバグの様な状況があります。これにも対処します。

<br/>

---
## 再インストール手順
---

まず本題とは直接関係ありませんが、システムを最新にします。

```sh
$ sudo apt update
$ sudo apt upgrade
$ sudo shutdown -r now
```

システム起動直後に、この apt upgrade でエラーまたは警告が出る場合は、システムのaptデータベースが更新中です。10分程度待ってから再実行する必要があります。

一応、**nodejs** を再インストールします。

```sh
$ sudo apt install -y nodejs
```

次に **mode_modules** を **node** が発見するリンクを張ります。

```sh
$ sudo ls -l /usr/local/lib/node_module /home
```

以下、**npm** とサンプルプログラムが使うモジュールの再インストールです。

```sh
$ sudo npm install -g npm
$ sudo npm install -g express
$ sudo npm install -g views
$ sudo npm install -g ejs
$ sudo npm install -g fs
$ sudo npm install -g ip
```

いかがでしょうか。これで、以下の４つのサンプルプログラムが動作するはずです。

```sh
jsclient/app.js
jsclient/app2.js
jsclient/app3.js
wsclient/app.js
```

<br/>
なお実習中に問題が判明した。wsclient/app.js は更新しています。<br/>

https://github.com/ahidaka/EnOceanGateways

を入手し直してから、試して下さい。

以上。


