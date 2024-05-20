# ham-logger
Raspberry Pi Picoを使ってハムスター用回し車で走行ログを記録するプログラムです。
以下のメルカリページで販売しているシステムに組み込んで使用します。
- [16cm版](https://jp.mercari.com/item/m17639796581)
- [18cm版](https://jp.mercari.com/item/m11625269172)

## 表示画面の読み方
- ハムスターの走行距離、走行時間、速度、温度を時系列で自動記録・表示します。
- 図の例では、約11時間前から走り始め、ときどき休みながら約６時間前まで合計約900m走った記録が表示されています。
- 縦軸(左:温度と右:走行距離)の最小値と最大値は変動幅に応じて自動でスケーリングされます。
- 表示は5分間隔で自動更新されます。画面更新の際、表示書き換えのために3回ほど画面全体が短く点滅します。
- 電源を切ってもログは保存されているため、電源再投入時は前回の記録に続いて表示が更新されます。
- 気温はRaspberry Pi Pico内蔵のセンサーを用いているため、部屋全体と比べて相違がある場合があります。
- 現在の時刻を表示する機能はなく、横軸は現在時刻を基準とした過去18時間の時系列を意味しています。

![example](https://github.com/KotaroHashimoto/ham-logger/assets/12003444/1fcad37b-73c5-4e9a-bef5-317cc663c97e)

## センサー部分の構成
回し車の裏側に磁石が取り付けられており、支柱に設置された磁気センサー付近をホイール側の磁石が通過するたびに回転を検知します。  
ホイールを付け替える場合は同様にホイール側に磁石を固定し、センサーと十分接近するよう調整してください。 

![ura](https://github.com/KotaroHashimoto/ham-logger/assets/12003444/dc5c1006-af4d-431d-bc32-46dfaac1533c)
- 商品によって見た目が異なることがありますが仕組みは同じです。

## 設置方法
- ケージの隙間からコードを引き出し、表示部分をケージの外側に配置します。コード部分をハムスターが噛まないよう、位置関係に注意してください。
- 表示部分にMicro-USBケーブルを挿して電源に接続します。家庭用電源のほかスマートフォン用のモバイルバッテリーなどを使うことも可能です。
- 電源を投入すると、表示部分が一度白に初期化されたのち、画面が白黒に点滅し、数秒後に各種数値とグラフが表示されます。
- 電源を切ってもそれまでのログは保存されているため、電源再投入時は前回の記録に続いて表示が更新されます。

![install](https://github.com/KotaroHashimoto/ham-logger/assets/12003444/23b0e937-cbd7-463a-bb93-639e27c9d778)


- 回し車の回転が検知されると表示部分の裏側にあるLEDが緑色に点滅します。もしLEDの点滅が確認できない場合は電源の接続や磁石とセンサーの近さを度確認してください。
- ホイールの回転は常時記録され、5分おきの表示更新時に反映されます。表示部分は5分毎に画面が白黒に点滅したのち各種数値とグラフが表示されます。
- 部屋の照明器具や電気機器の電源ONOFF時に発生する電磁波にセンサーが反応してLEDが点滅することがあります。気になるようでしたら電磁波を発する環境からケージを離してご使用ください。

![led](https://github.com/KotaroHashimoto/ham-logger/assets/12003444/22faa3b9-66c1-45e0-ac66-ccac0e60e534)

## プログラムの書き換え方
ホイールを異なる大きさの物に付け替えたい場合や、表示部分左上のハムスターの名前を書き換えたい場合は、以下の手順でソフトウェアを書き換えてください。  
Raspberry Pi PicoへThonnyというソフトウェアを使って Pythonスクリプトを書き込みます。

### 環境構築
#### micro USBケーブルについて
単に動作させるだけであればデータ通信ができなくても給電可能なUSBケーブルを使用することで問題ありませんが、プログラムの書き換え等のためにPCと通信を行う場合はデータ通信可能なmicro USBケーブルを使用する必要があることに注意ください。
#### Raspberry Pi Pico と PC の接続
[こちらのページを参考に](https://krr910183393.wordpress.com/2021/02/15/raspberry-pi-pico-micropython-thonny-python-ide/)、「Thonny Python IDE のインストール」と「Raspberry Pi Pico を接続」を実施してください。  
Raspberry Pi公式ページや他のWebサイトも似たような手順が書かれていて参考になりますが、"MicroPython firmwareの書き込み"手順は不要です。firmwareはすでに書き込んであり、もし最新バージョンに上げるとスクリプトと互換性がなくなる可能性があるため、MicroPython firmwareの書き込みは行わないでください。  
#### Raspberry Pi Pico 中のファイル閲覧
[こちらのページを参考に](https://sanuki-tech.net/and-more/2022/raspberry-pi-pico-file-list/)、Raspberry Pi Picoに書き込んであるスクリプト(main.py)をThonnyで開いてください。  
[こちらのレポジトリ](https://github.com/KotaroHashimoto/ham-logger/blob/main/main.py)でも同一のスクリプトを取得することができます。  

### 設定の書き換え
#### ホイール内径の変更
スクリプト main.py の 564行目で、Counter() の引数でしている数値を、ホイール内径[m]の数値に書き換えます。例えば21cmのホイールへ設定変更する場合は0.21に変更します。  
<img width="800" alt="size" src="https://github.com/KotaroHashimoto/ham-logger/assets/12003444/59bc4f21-ce88-4911-825b-c0657876c24e">  
Raspberry Pi Pico 上でスクリプトを書き換えたら上書き保存すると次回起動時に変更が反映されます。

#### ハムスターの名前の変更
スクリプト main.py の 573行目で、Environment() の引数でしている文字列を変更します。使用できる文字はアルファベット大文字小文字と数字のみです。  
<img width="800" alt="name" src="https://github.com/KotaroHashimoto/ham-logger/assets/12003444/464cbd6b-e360-430f-b161-700ea112aef8">  
Raspberry Pi Pico 上でスクリプトを書き換えたら上書き保存すると次回起動時に変更が反映されます。

#### ログファイルについて
一度起動すると、以下のようにmain.pyの他に３つのlogファイルが自動で生成されています。これらは電源OFF->ON時に再度読み込まれて前回までの記録を再表示するために使用されます。  
![logs](https://github.com/KotaroHashimoto/ham-logger/assets/12003444/4f17824e-4de7-4eef-88a0-77f08e189823)  

もしこれまでのログをリセットしたい場合はRaspberry Pi Picoの中の3つのlogファイルを消してmain.pyだけが存在する状態にして電源投入してください。空のlogファイルが作成され新たにログが蓄積されていきます。
