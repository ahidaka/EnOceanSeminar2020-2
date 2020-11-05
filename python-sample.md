# python-sample


### EnOcean Seminar textbook in 2020 volume 2 / Python sample

EnOcean IoTシステム開発コンテスト！meet up： Python サンプル

- GitHub Page<br/>
    https://ahidaka.github.io/EnOceanSeminar2020-2/python-sample.md

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

この文書は、[EnOcean IoTシステム開発コンテスト！meet up 実習用テキスト](README.md) を補足する、**Python** によるサンプルプログラムの解説です。

マルチセンサーSTM550J のEnOcean電波を受信して動作する、Windows 用の簡単なサンプルプログラムは、下記の**EnOceanWindowsSamples** 内の **DefaultMultiSensor** を参照してください。

https://github.com/ahidaka/EnOceanWindowsSamples

<br/>

Raspberry Pi を含む各種Linuxで動作する **Python** のサンプルプログラムは以下にあります。
この中の **pymulti.py** がプログラム本体部分です。Windows版 Python の **USB400J** からのデータ取得方法が良く分からなかったため、今のところは Linuxだけでの動作です。

補足しますと WSL, WSL2 とも現時点では**USB400J** からデータが取れないことが判明しています。

https://github.com/ahidaka/PyEnOcean

<br/>

---
## 概要
---

以前から **pip** でインストール出来る **Python enocean パッケージ** がありましたが、EU 仕様向けのため、日本国内向けの928MHz帯 製品では利用できませんでした。一方で Linux用のシンプルなサンプルプログラムが用意できていなかったため、これを使って マルチセンサー STM550J対応の簡単なサンプルとして公開しました。

次の点に留意しました。

- **Python enocean パッケージ** にはなるべく手を入れない様にしました。
- マルチセンサーの全データと電波強度を取得する様にしました。
- 拡張性を考慮しながら、機能を絞ってコンテスト作品等での再利用し易さに配慮しました。
- 特にコードのシンプルさ実現のために **Python** を利用するので、基本アプリケーションは1ファイル300行程度とし、[コンテスト 追い込み会](https://algyan.connpass.com/event/190755/) までの完成を目指しました。
- **Sli.do** で要望が上がっていた、「ラズパイで簡単に温度データのみを取り出して表示、テキストデータで保存」の要件を実現しました。

<br/>

[kipe (Kimmo Huoman)](https://github.com/kipe) 氏による、**Python enocean パッケージ** の戦全ソースコードは以下にあります。

https://github.com/kipe/enocean

<br/>

---
## 少しだけ解説
---

元々がERP1だけの対応で、ERP2が全く無視される構造となっていたため、その原因の **Packetクラス** を改造して、**packet_type=ERP2** を受信した場合、従来の **RadioPacketクラス** の代わりに、新たに作った **Radio2Packetクラス** で処理する様にしました。

予定では、**Packetクラス** を置き換えて、**Radio2Packet** とそれに付随する **PacketTypeクラス** を追加するだけで、マルチセンサーのテレグラム処理に必要なプロトコル処理部分のERP2対応が完了するはずでした。

しかしながら、元の **communicator.py** が、

```python
from enocean.protocol.packet import Packet
```

とimportしていて、さらにそれを **serialcommunicator.py** 

```python
from enocean.communicators.communicator import Communicator
```
としていて、これが原因で元の **Packetクラス** を外すことがどうしても出来ませんでした。
やむを得ず **import** 文を書き換えた、 **communicator.py** と **serialcommunicator.py** ソースコードに含めています。

私が改造した **packet.py** をPLしてkipe氏に取り込んで貰えばいいのですが、面倒そうなのと **pymulti.py** が一部未完成なため、Pull Request は今のところ保留しています。
どなたか、もし既存パッケージの **import** 内容やクラスを上書き変更する方法をご存知でしたら教えて下さい。

<br/>

### enocean_example.py ###

元のパッケージに入っている、簡単な動作確認用のテストプログラムの様子です。
それらしく動作させる様にしましたが、EEPの処理部分が正しく動作せず、また内容や扱い方も良く分からなかったため、深追いしていません。

<br/>

### pymulti.py

Windows版 **DefaultMultiSensor** の基本部分の考え方を移植しました。

Windows版はGUIで Python版はCUIのため、起動時のパラメーター指定だけがインターフェースです。
課題の **必要なデータだけを出力** する指定を、出来る様にしました。

EEP定義部分は、**DefaultMultiSensor** ではソースコード埋め込みでしたが、
**pymulti.py** では **オリジナル形式 JSON ファイル** を参照する様にしました。
これにより、実質的に全ての **EEP** に対応出来る様になっています。

 **オリジナル形式 JSON ファイル** の作成手順は、[**PyEnOcean/eep-json/README.md** ファイル](https://github.com/ahidaka/PyEnOcean/blob/main/eep-json/README.md) に記しています。

<br/>

---
## 使い方
---

最初に、次の手順でインストールと設定してください。

```sh
$ sudo apt install python3 #if your environment doesn't have Python3 
$ sudo pip3 install enocean
$ sudo chmod 666 /dev/ttyUSB0
```
<br/>

### Usage

起動時パラメーターは、
[**PyEnOcean**](https://github.com/ahidaka/PyEnOcean) のREADNE 記載の、次の通りです。

```sh
$ ./pymulti.py -\?
PyMulti...
    usage:
    pymulti.py [options...] [TP HU IL...(point name)] [JSON EEP definition file (*.json)]

output example:

TP=28.5,HU=65.0,IL=515.6,...
TP=27.0,HU=67.0,IL=620.0,...
...

options:
    [-u]  add unit to displsy
    [-c]  output with CSV format (default)
    [-p]  output with prefix point name at CSV (default)
    [-l]  accept teach-In telegram by LEARN button

point name:
    TP - Temperature 10
    HU - Humidity
    IL - Illumination
    AS - Acceleration Status
    AX - Acceleration X
    AY - Acceleration Y
    AZ - Acceleration Z
    CO - Contact

Available json eep definition file:
    D2-14-41.json
    D2-14-40.json
    A5-02-05.json
    A5-04-01.json
    A5-04-03.json
    A5-06-02.json
    A5-06-03.json
    A5-14-05.json
    D5-00-01.json
```
<br/>

### 実行＆出力例

```sh
$ ./pymulti.py TP HU
PyMulti...
TP=21.60,HU=66.00,
TP=21.70,HU=67.00,
TP=21.70,HU=68.50,
TP=21.70,HU=70.00,
TP=21.70,HU=70.50,
```

---
## 今後
---

現在は1センサーデバイス、1種類の**EEP**での利用しか考慮していません。
**Teach-In** は受信確認するだけで、EEP自動設定とテレグラムフィルターは実装していません。
Python の **Directory** 機能により、異なるEEPの複数センサー対応が比較的容易に実現できそうですが、
当初の目的から外れるため、今後の課題とします。

以上
