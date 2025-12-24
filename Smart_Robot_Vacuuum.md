# スマートクリーンカム (Smart Clean Cam)

## 📌 概要
ホームカメラ + ロボット掃除機 = **スマートクリーンカム**

## ✨ 主な機能
- **清掃モード**: 自動清掃および手動清掃
- **カメラ機能**: Wi-Fi経由のカメラストリーミング
- **遠隔操作**: Bluetoothによる掃除機制御

## 🏗 全体システム構成
<img width="1602" height="822" alt="image" src="https://github.com/user-attachments/assets/da345b8b-8f88-45bb-b322-76be4676ec6c" />

- **開発言語**: C言語、Python
- **ライブラリ**: softpwm, wiringPi
- **その他**: Flask Webサーバー

## 🧵 マルチスレッド構造
- 経路計算スレッド
- Bluetooth通信スレッド
- センサー測定スレッド
- モーター制御スレッド

- **Python**: カメラストリーミング

### 🔒 共有変数 (Mutex使用)
以下の変数はMutexを使用してスレッド間の競合を防いでいます。

- **モーター制御変数**
  - Bluetooth通信スレッドがコマンドを受信する場合
  - 経路計算スレッドがモーターに指示を出す場合
  - モーター制御スレッドが変数を参照してモーターを駆動する場合

- **手動/自動モード切替変数**
  - 経路計算スレッドが経路制御アルゴリズムを使用するかどうかを決定する場合

- **超音波センサー距離変数**
  - 超音波で測定された値を記録する場合
  - 経路計算スレッドがセンサー値を利用して経路を制御する場合

- **システム開始/終了変数**
  - モーター制御スレッドが各種モーターを制御するかどうかを決定する場合

<img width="1676" height="919" alt="image" src="https://github.com/user-attachments/assets/a9562a4e-3275-4ce8-87ce-ff1c1453540d" />

## 🚀 ビルドおよび実行方法

### C言語ファイルの実行コマンド
```bash
gcc -o robot_car main.c bluetooth.c sensors.c motors.c navigator.c -lwiringPi -lpthread -lm
```
- Pythonファイルの実行コマンド
```
python camera_streaming.py
```
## デモ映像
- マニュアルモードおよびカメラ確認<br>
- https://github.com/user-attachments/assets/cd7c9f53-740d-4a64-9b34-c7d0122c28df

- 吸引デモンストレーション<br>
- https://github.com/user-attachments/assets/b134cdee-bcc7-4642-bd0e-ef382de86239

- オートモードデモンストレーション<br>
- https://github.com/user-attachments/assets/aa6e0d9a-5a15-411a-b899-934ac97f823c



## 課題と解決策 / 限界
1. 電源分配の問題
  - 課題: 11.1Vの電源装置一つで高出力の吸引モーター、各種アクチュエータ、センサー、Raspberry Piを無理に稼働させると、電力不足によりシステムがダウンする現象が発生。
  - 検討: 吸引モーターの出力を下げると、吸引力が著しく低下するため不可。
  - 解決策: 吸引モーター以外の部品（Raspberry Pi等）には、別途モバイルバッテリーを使用して電源を供給することで解決。
2. 映像送信の問題
  - 課題: 当初C言語での実装を試みたが、実装が困難であった。また、学内Wi-Fiを通じたローカル網でのストリーミングを試みたが、ネットワークセキュリティの問題で接続できなかった。
  - 解決策: Python（Flask）で実装し、スマートフォンのテザリング（ホットスポット）を使用することでネットワーク問題を解決。
- GPIO出力の限界
  - 限界: Raspberry PiのGPIO出力電圧は最大3.3Vだが、吸引モーターの最大入力信号電圧は5Vであるため、モーターの最大性能（フルパワー）を引き出すことができていない。

