SRS Documentation
======================

Introduction
--------------

AI UnitおよびAndroidアプリを開発し、自転車の走行位置を検知してユーザーへ警告を行うシステムを構築する。
これにより、自転車利用者の意識向上を図る。

本システムは以下を含む：

* 顧客提供ハードウェア上で動作するアプリケーション。
* Androidスマートフォンアプリ。

Scope
------

In-Scope
^^^^^^^^^^^^^^
歩道走行を検知した際にユーザーへ警告を行うスマートフォンアプリを開発する。
Phase 2では、Androidアプリの開発および既存AI Unitとの連携を行う。

主な機能：

* 自転車の走行位置を判定し、減速警告を発する。
* デバイスとスマートフォン間の接続確認。
* AI Unitからスマートフォンアプリへ画像送信。
* 画像内の人物の顔を削除する。
* スマートフォンアプリとAI Unit間のメッセージ暗号化。

Out-of-Scope
^^^^^^^^^^^^^^^^

* iOSアプリは対象外。
* App Store公開は対象外。
* アプリのバックグラウンド動作は非対応。
* Phase 1で開発したAIモデルの最適化は対象外。

Overall Description
---------------------

Constraints
^^^^^^^^^^^^^^

* 顧客提供ハードウェア上で開発する。
* AIモデルはRKNN形式へ変換して実装する必要がある。

Security

* HBLAB開発アプリのみがAI Unitからのメッセージを受信できる認証機能を実装する。
* AI Unitから送信される情報は暗号化し、指定アプリのみが読み取り可能とする。

Functional Requirements
-----------------------------

FR-DA: Device Application
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table:: **FR-DA**
   :widths: 15 10
   :header-rows: 1

   * - Content
     - Detail
   * - Description
     - 顧客提供ハードウェア上で動作するアプリを開発する。
   * - Input
     - カメラで取得した画像。
   * - Output
     - アプリケーションログ。

       スマートフォンアプリへ送信するメッセージ。
   * - Preconditions
     - デバイスに十分な電源が供給されている。

       内部メモリに空きがある。

       カメラと接続されている。
   * - Postconditions
     - プログラムログを保存する。

.. list-table:: **Business Flow**
   :widths: 15 30 30
   :header-rows: 1

   * - Step
     - Description
     - Business Logic Acceptance Criteria
   * - 1. プログラム起動
     - デバイスへ電源供給。
     - 電源投入後、自動起動する。
   * - 2. AI Unitとスマートフォン接続
     - USB Type-Cで接続。
     - 接続成功。
   * - 3. スマートフォンからAI Unit設定
     - スマートフォンアプリを起動。

       AI Unit設定画面を開く。
     - 最低限以下を設定可能：

       AI Unit動作モード（running/stop）。

       連続する2回の撮影間隔（interval）。
   * - 4. 画像送信
     - 取得画像をスマートフォンへ送信。
     - overlay画像、raw画像を送信。

       送信成功後、 AI Unit側で削除。

       ログ保存。

.. list-table:: **External Flow**
   :widths: 15 30 30
   :header-rows: 1

   * - Step
     - Description
     - Business Logic Acceptance Criteria
   * - 1. configファイル更新
     -  PCからconfigファイルを更新できる。

     - configファイルでハードウェア上のアプリのパラメータを調整できる（付録1参照）。

       電源投入時にAI Unitが起動し、自転車位置検知モードで動作する。

       連続する2回の撮影間隔：0.5秒。

       正しいAIモデルで動作し、指定フォルダへ画像を保存する。

       configファイルはADB Interfaceを通じてPCから更新できる。

FR-LW: Location Warning
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FR-LW-1: Location Detection
*******************************

.. list-table:: **FR-LW-1**
   :widths: 15 10
   :header-rows: 1

   * - Content
     - Detail
   * - Description
     - カメラ画像を取得し、AIモデルで解析し、走行位置（歩道/車道）を判定する。
   * - Input
     - カメラ画像。
   * - Output
     - AI Unitは、自転車の走行位置（歩道／車道）情報を返す。
   * - Preconditions
     - 電源供給あり。
   * - Postconditions
     - ログ保存。

       overlay画像情報。

       raw画像情報。

FR-LW-2: Warning Notification
********************************

.. list-table:: **FR-LW-2**
   :widths: 15 10
   :header-rows: 1

   * - Content
     - Detail
   * - Description
     - AI UnitがADB interfaceを通じて自転車の走行位置情報をスマートフォンアプリへ送信する。スマートフォンアプリは、歩道走行を(Y)回連続で検知した後に警告を行う。
   * - Input
     - FR-LW-1の走行位置情報。
   * - Output
     - スマートフォンアプリは日本語で「徐行してください」と音声案内を再生する。
   * - Preconditions
     - 電源供給あり。

       デバイスとスマートフォンが接続されている。

       スマートフォンアプリ起動済み。
   * - Postconditions
     - ハードウェアアプリおよびスマートフォンアプリにログを保存する。

       音声通知が正常に再生される。

.. list-table:: **Business Flow**
   :widths: 15 30 30
   :header-rows: 1

   * - Step
     - Desciption
     - Business Logic Acceptance Criteria
   * - 1. デバイスがスマートフォンへ通知送信
     - 自転車の走行位置を検知後、デバイスがADBを通じてスマートフォンへ通知を送信する。
     - AI Unitとスマートフォン間の送受信が成功すること。送受信回数および内容が一致すること。

       送受信ログを保存する。
   * - 2. スマートフォンで警告再生
     - **If:**

       スマートフォンが「歩道走行」の情報を受信した場合。

       **Then:**

       スマートフォンが「徐行してください」と音声案内を再生する。
     - アプリが正常に警告を再生すること。

       ログを保存する。

.. list-table:: **External Flow**
   :widths: 15 30 30
   :header-rows: 1

   * - Step
     - Description
     - Business Logic Acceptance Criteria
   * - 1. 警告用config更新
     - アプリのsetup画面を開く。
     - setup画面が正常に表示される。
   * - 2. ``連続検知回数`` の更新
     - 歩道走行を連続で検知する回数を入力し、警告を行う。
     - 2回の通知間隔は、``直近の通知間隔`` × ``連続検知回数`` とする。
   * - 3. ``連続警告間隔`` の更新
     - 連続する警告の間隔を入力する。
     - 入力可能な値は、（``直近の通知間隔`` × ``連続検知回数``）の倍数のみとする。異なる場合はエラー表示する。


FR-CL: Clear Sensitive Information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

FR-CL-1: Image Handling
***********************

.. list-table:: **FR-CL-1**
   :widths: 15 10
   :header-rows: 1

   * - Content
     - Detail
   * - Description
     - AI Unitが画像を撮影し、ADB interfaceを通じてAndroidアプリへ送信する。

       Androidアプリは保存前に顔をぼかす。
   * - Input
     - AI Unitのカメラで取得した画像。
   * - Output
     - Androidアプリが保存前に顔をぼかす。

       送信成功後、AI Unit内の画像をすべて削除する。
   * - Trigger
     - AI Unitが画像をアプリへ送信した直後。
   * - Preconditions
     - AI Unitに十分な電源が供給されている。

       AI UnitがUSB Type-Cに対応し、ADB Interfaceで情報取得できる。

     （他ポート対応する場合、AI Unit側も対応が必要）
   * - Postconditions
     - プログラムログを保存する。

       ぼかし処理済み画像を保存する。

       AI Unit側画像を削除する。

.. list-table:: **Business Flow**
   :widths: 15 30 30
   :header-rows: 1

   * - Step
     - Desciption
     - Business Logic Acceptance Criteria
   * - 1. AI処理後の画像送信
     - AI処理後（歩道／車道判定）の画像を自動でAndroidアプリへ送信する。
     - すべての画像（raw画像、処理後overlay画像）を送信する。

       送信は暗号化し、HBLAB開発アプリのみが画像を読み取れる。
   * - 2. スマートフォンで画像受信
     - スマートフォンアプリが画像を受信し、保存する。
     - 保存成功。

       AI Unitへ通知を送信する。
   * - 3. AI Unitで画像削除
     - メモリ内の画像をすべて削除する。
     - 削除成功。

       ログに保存する。

FR-CL-2: Clear Sensitive Info
***********************************

.. list-table:: **FR-CL-1**
   :widths: 15 10
   :header-rows: 1

   * - Content
     - Detail
   * - Description
     - 日本の法令違反を防ぐため、スマートフォンアプリで画像内の個人情報（歩行者の顔）を削除／ぼかす。
   * - Input
     - AI Unitからスマートフォンアプリへ送信された画像。
   * - Output
     - 歩行者の顔がぼかされた画像（スマートフォンアプリ上）。
   * - Trigger
     - スマートフォンアプリがAI Unitからの画像を受信した直後。
   * - Preconditions
     - AI Unitから画像を受信している。
   * - Postconditions
     - プログラムログを保存する。

       ぼかし処理済み画像を保存する。

.. list-table:: **Business Flow**
   :widths: 15 30 30
   :header-rows: 1

   * - Step
     - Description
     - Business Logic Acceptance Criteria
   * - 1. 顔検出
     - Androidアプリで画像内の歩行者の顔を検出する。
     - 画像内のすべての顔を検出する。
   * - 2. 顔ぼかし
     - 顔をぼかす。
     - 可能な限り強くぼかす。

FR-CC: Connection Checking
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. list-table:: **FR-CC**
   :widths: 15 10
   :header-rows: 1

   * - Content
     - Detail
   * - Description
     - デバイスとスマートフォンの接続状態を確認し、通知する。
   * - Input
     - 接続情報。
   * - Output
     - connect／disconnect通知。
   * - Preconditions
     - デバイスに十分な電源が供給されている。

       必須: AI UnitはUSB Type-Cポートを備えている必要がある。Android Type-Cと接続し、ADB Interfaceで情報取得可能であること.（他ポート対応する場合、AI Unit側も対応が必要）
   * - Postconditions
     - プログラムログを保存する。

.. list-table:: **Business Flow**
   :widths: 15 30 30
   :header-rows: 1

   * - Step
     - Description
     - Business Logic Acceptance Criteria
   * - 1. アプリ起動
     - アプリを起動する。
     - スマートフォンアプリがAI Unitに接続されていない状態を表示する。
   * - 2. AI Unitをアプリに接続する。
     - USBケーブルを物理的に接続する。
     - アプリに「AI Unitデバイスに接続しました」と表示する。
   * - 3. 切断
     - USBケーブルを抜く。
     - アプリに「AI Unitデバイスから切断しました」と表示する。

Non-Function Requirement
-----------------------------

* MIoU: 70%
* Accuracy: 70%

Appendix
-------------------

Config file

.. list-table:: **Business Flow**
   :widths: 10 20 30
   :header-rows: 1

   * - Key
     - Description
     - Value
   * - ``mode``
     - AI Unitの起動方法を選択する。
     - Datatype: String

       ``paused`` : PCからのコマンドで起動

       ``running`` : 電源投入で自動起動
   * - ``processing_mode``
     - 動作モードを選択する。
     - Datatype: String

       ``capture-segment`` : メイン動作

       ``capture`` : 画像取得
   * - ``interval``
     - 連続する2回の撮影間隔
     - Datatype: Float (unit: 秒)
   * - ``model_path``
     - モデルフォルダのパス
     - Datatype: String

       Sample: "/userdata/models/ddrnet_rk1808.rknn"
   * - ``base_dir``
     - 画像保存フォルダのパス
     - Datatype: String

       Sample: "/userdata/captures"