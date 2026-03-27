# PRO-1769 Bike camera segmentation

## 目次

[ソースコードのダウンロード](#download-source-code)

[クイックスタート](#quick-start)

[AI Unit用アプリのインストール](#cài-đặt-ứng-dụng-cho-ai-unit)

[スマートフォン用アプリのインストール](#cài-đặt-ứng-dụng-cho-điện-thoại)

[システム利用ガイド](#hướng-dẫn-sử-dụng-cho-hệ-thống)

## ソースコードのダウンロード

### ソースコードファイルをダウンロード

**Step 1:**
GitLab上でプロジェクトを開き、[Phase2](https://git.hblab.vn/rnd/pro-1769-bike-camera-segmentation/-/tree/phase2?ref_type=heads)ブランチに切り替えます

![Select branch phase2](Document/img/README/phase2.png)

**Step 2:** **Code を選択**

**Step 3:** zip を選択してソースコードをPCにダウンロード

![Download zip file](Document/img/README/download.png)

## クイックスタート

|Component|Technology|
|-|-|
|Programming Language|Python 3.x|
|Computer Vision Library|OpenCV (cv2)|
|Operating System|Linux-based (userdata path)|
|Camera Interface|Video4Linux (V4L)|

### アプリインストール準備ガイド

**Windows OSでの環境準備**

*Android SDK Platform-Toolsを使用*

1. SDK Platform-Toolsのダウンロード:

   * アクセス:  [Platform Tool](https://developer.android.com/tools/releases/platform-tools)
   * Windows OS向けのアプリケーションファイルをダウンロード
   * PATHへ追加:

     `Win + X` を押す

     `System` を選択

     `Advanced system settings` を選択

     `Environment Variables` をクリック

     `System variables` 内の `Path` を選択し、`Edit` をクリック

     `New` をクリックして `C:\\platform-tools` を追加

     `OK` をクリック

     　

* 　 インストール確認

  `Windows` ボタンを押す

  ``PowerShell`` と検索

  `Enter` を押す



  アプリケーション画面で以下のコマンドを実行:

   ``` bash
   adb version
   ```

  画面にアプリケーション情報が表示されれば、インストールは正常に完了しています



  **Linux（Ubuntu/Debian）での環境準備**



1. パッケージリストを更新

   ``` bash
    sudo apt update
    ```

2. adbをインストール

   ```bash
    sudo apt install adb -y
    ```

3. バージョン確認

   ``` bash
   adb version
   ```

### AI Unit用アプリのインストール

#### 1. プログラムをPCにダウンロード

ソースコードを以下からダウンロード: [AI Unit Application](https://git.hblab.vn/rnd/pro-1769-bike-camera-segmentation/-/tree/phase2?ref_type=heads)

#### 2. ソースコードを解凍

PCにあるツールを使用して、ダウンロードしたファイルを解凍

解凍後のフォルダ構成:

![Folder Structure](Document/img/Folder_structure.png)

|ファイル/フォルダ名|説明|
|-|-|
|app|AI Unitアプリケーションを格納|
|Document|プロジェクトドキュメントを格納|
|models|学習済みモデルを格納|
|build_windows_exe.bat|Windows用exeファイルをビルドするアプリケーション|
|hblab_decrypt_images.exe|AI Unitから取得した画像を復号するアプリケーション|
|hblab_decrypt_images.py|画像復号アプリケーションのソースコード|
|README.md|ソースコードダウンロード手順書|

#### 3. デバイスとPCの接続

* 電源を接続し、USBポート経由でデバイスをPCに接続します。
* PCでTerminalまたはPowerShellを起動します。
* 以下のコマンドでデバイスとPCの接続を確認します:

```bash
adb devices
```

* 接続失敗の場合:

![fail_connect_device](Document/img/fail_connect_device.png)

* 接続成功の場合:

![Success_connect](Document/img/success_connect.png)

#### 4. アプリのインストール

* Terminalにデバイスが表示されたら、以下のコマンドを実行:

```
adb push {path/to/service/code/file} userdata/
```

**デバイスを再起動**

**方法1:** 電源を抜き差しする  

**方法2:** Consoleで以下のコマンドを実行:

```
adb shell reboot
```

**デバイスが正常に起動していることを確認**

ログファイルの確認:

PCの**Console**で以下を実行:

```
adb pull userdata/app\_log.txt {path/to/save (optional)}
```

#### 5. デバイスからPCへ画像を取得

データ取得後、以下手順で画像をPCへ取得します:

**Step 1:** [デバイスとPCの接続](#3-kết-nối-thiết-bị-với-pc)**と同様に接続**  

**Step 2:** 以下コマンドを実行して画像をダウンロード:

```
adb pull userdata/captures {path/to/save (optional)}
```

*Note*:

`adb pull`: デバイス → PC にダウンロード  
`adb push`: PC → デバイス にアップロード  

#### 6. 画像の復号

取得した画像は `.enc`（暗号化形式）です。閲覧前に復号が必要です。

pull後、`captures`フォルダ内に `captures1`, `captures2`, ... が作成されるため、対象フォルダを選択して復号します。

---

**Windowsの場合 — `decrypt_images.exe` を使用:**

1. `decrypt_images.exe` をダブルクリックするとCMDウィンドウが表示されます  

![HBLAB_Decryption](Document/img/hblab_decryption.png)

2. フォルダパスを入力（例） 

``` bash
C:\Users\user\Downloads\captures\captures2
```

3. 秘密鍵ファイルパスを入力（例）:

``` bash
C:\Users\user\Downloads\private_key.pem
```

4. 復号後の画像は `captures2_decrypted\`フォルダに自動保存されます

---

**Linux/macOSの場合 —  `decrypt_images.py`を使用:**

```bash
python decrypt_images.py captures/captures2 private_key.pem
```

復号後の画像は `captures2_decrypted/` フォルダに保存されます

---

**7. 画像の削除**

**方法1**

Step 1:  デバイスをパソコンに接続する ([デバイスとPCの接続](#316-cài-đặt-ứng-dụng-cho-thiết-bị)と同様に接続) 

Step 2: 画像を格納しているフォルダを削除するため、以下のコマンドを実行:

```
adb shell "rm -r userdata/captures"
```

**方法2**

AI Unitのメモリが満杯になると、スマートフォンアプリに警告が表示されます。

アプリ画面上で「メモリを削除するか」の確認が表示され、ユーザーはAI Unit内の画像をすべて削除できます。

### スマートフォンアプリのインストール

**Step 1: アプリをスマートフォンにダウンロード**

ソースコードをダウンロード・解凍後、apkファイルをスマートフォンへ転送

|選択|説明|メリット|
|-|-|-|
|方法1|USBでスマートフォンとPCを直接接続し、`app-release.apk` をスマートフォンにコピー	|そのままapkをインストール可能。<br />ただし1台・1ケーブルの場合、同時に1回のみ実施可能|
|方法2|`app-release.apk` をクラウドにアップロードし、スマートフォンからリンク経由でダウンロード|複数端末で同時にダウンロード可能|



**Step 2: アプリのインストール**

apkファイルをダウンロード後、ファイルをタップします。

`INSTALL` を選択  

![Install Notice](Document/img/Install_1.png)

画面の指示に従って操作  

![Install Notice](Document/img/Install_2.png)

apkファイルは自動的にインストールされます。

アプリがスマートフォン内のファイルへアクセスできるよう、権限を付与してください  

![Install](Document/img/Install_3.png)

### システム利用ガイド

#### 事前準備

* AI Unit用アプリをインストール後、デバイスに電源が供給されるとアプリは自動的に起動します。
* アプリインストール後、ユーザーはアプリを起動します。  

![Open app](Document/img/2203.png)

#### 利用手順

デバイスのUSBポートとスマートフォンを接続します。

通知が表示されたら、`OK` を選択します。  

![Accept connect](Document/img/aceppt_1.png)

スマートフォンとアプリの接続が成功すると、USB接続およびADB接続の情報がアプリのホーム画面に表示されます。

![Connect success](Document/img/2202.png)

#### 設定ガイド

本アプリでは動作設定の変更が可能です。

スマートフォンアプリ画面の ⚙️ を選択します。  

![Setting_sceen](Document/img/Change\_setting.png)

アプリの設定画面

![Setting_1](Document/img/2859.png) ![Setting_2](Document/img/2880.png) ![Setting_3](Document/img/2902.png)

|No|内容|値|
|-|-|-|
|1|AI Unitの設定ファイルを初回取得	<br />現在の設定内容を確認するために使用|取得結果は項目2および3に反映される|
|2|AI Unitの動作モード選択	|利用不可：AIユニットとスマートフォンアプリが未接続のため、デフォルトでは設定情報は未取得の状態となる。<br /><br />実行中：電源接続後、AIユニットが自動的に起動する<br /><br />一時停止：AIユニットのすべての動作を停止する|
|3|<br />連続する2回の自転車位置検知間隔を設定する|単位: `秒`<br> 最小値: 0.2|
|4|歩道の連続検知回数（この回数に達すると警告を発報）|データ型: `数値` <br> 単位: `回`|
|5|通知間隔（2回の警告間の時間）。本値は項目 3 × 4 の値より大きく設定する必要がある。|単位: `秒`|
|6|スマートフォン画面に表示する警告テキスト|テキスト形式で入力可能（任意入力）<br>制限：全角30文字以内|
|7|警告テキストのフォントを設定する|選択式。対応フォント：<br>MS Gothic (ＭＳ ゴシック)<br>MS Mincho (ＭＳ 明朝)<br>Meiryo (メイリオ)<br>Yu Gothic (遊ゴシック)<br>Yu Mincho (遊明朝)<br>Hiragino Kaku Gothic (ヒラギノ角ゴ)|
|8|警告テキストの文字サイズを設定する|入力形式：数値入力|
|9|警告テキストの表示時間を設定する|単位: `秒`|
|10|フォント色および背景色を変更する|選択式|
|11|警告テキストのプレビュー表示|プレビュー画像|
|12|設定内容を保存し、スマートフォンアプリに反映する||

#### エラー通知

スマートフォンアプリが警告を発する際、同時に画面にも通知が表示されます

![error_notice](Document/img/2705.png)

## インストールおよび使用詳細

以下の資料をご参照ください:

* Vietnamese: [HLD\_Document\_VN](Document/HLD_Doc.md)
* Japan: [HLD\_Document\_JP](Document/HLD_Doc_JP.md)

今週中にHLDの詳細資料を更新する予定です。



