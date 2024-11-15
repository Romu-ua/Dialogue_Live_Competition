<!-- ![Color logo with background](https://github.com/remdis/remdis/assets/15374299/da5eb1c0-b3b4-4056-9c68-99448265e9a4) -->

# [対話システムライブコンペ 7](https://sites.google.com/view/dslc7)配布ソフトウェア

対話システムライブコンペ 7 では，[Remdis](https://github.com/remdis/remdis)をベースとしたシステムを使用します．

## Release Notes

- 2024/10/31 `v0.0.2`の公開
  - MacOS/Windows に対応した Docker image を配布
  - Docker を用いるためのドキュメントを追加
  - 本ソフトウェアの安定性向上
  - TTS のスタイル指定機能を追加
  - シチュエーショントラック・タスクトラックに応じた MMDAgent の実行環境を追加
  - TravelViewer を追加
- 2024/09/12 `v0.0.1`の公開
  - TTS を Azure TTS へ変更
  - 動画入力への対応
  - Py-feat を用いた感情認識と顔向き推定
- 2024/10/xx Docker image およびソフトウェアの配布

## Remdis: Realtime Multimodal Dialogue System Toolkit とは？

![git_top_remdis](https://github.com/remdis/remdis/assets/15374299/dbc9deab-54b2-4b72-9ef9-06d6fcf38240)

> Remdis はテキスト・音声・マルチモーダル対話システム開発のためのプラットフォームです．

### 特徴

- 非同期処理に基づくモジュールベースの対話システム
- Incremental Units (IU)を単位としたメッセージングと Incremental Modules (IM)による逐次処理
- Large Language Model (ChatGPT)の並列実行・ストリーミング生成による疑似的な逐次応答生成
- Voice Activity Projection (VAP)によるターンテイキング
- MMDAgent-EX との連携によるエージェント対話
- Python 実装，クロスプラットフォーム (Windows/MacOS/Ubuntu)
- マルチモーダル対応 (テキスト対話/音声対話)

## インストール方法

以下の Step.1 から Step.4 を順に行ってください．

**注意) Windows 環境で実施する場合，WSL はオーディオデバイスとの相性がよくないため，コマンドプロンプトの利用を推奨します．**

### Step 1. 事前準備

本ソフトウェア では Docker を利用します．

- Docker Desktop のインストール
  - MacOS
    ```
    brew install --cask docker
    ```
  - Ubuntu
    - 最新の deb パッケージを DL し，インストール ([こちらのページ](https://docs.docker.jp/desktop/install/ubuntu.html)をご参照ください)
      ```
      sudo apt-get install ./docker-desktop-<version>-<arch>.deb
      ```
  - Windows
    - [Docker docs](https://docs.docker.com/desktop/install/windows-install/)からインストーラをダウンロードし，実行

### Step 2. Remdis 本体のインストール

- Clone

  ```bash
  git clone --recursive https://github.com/p1n0k0/Dialogue_System_Live_Competition_7.git
  cd Dialogue_System_Live_Competition_7
  git submodule init
  git submodule update
  ```

- Docker ファイルのビルド

  ```bash
  cd docker

  # タスクトラックの開発者向け
  docker compose -f docker-compose.dev.yaml build

  # シチュエーショントラックの開発者向け
  docker compose -f docker-compose.prompt-only.yaml build
  ```

  Windows を使用している方は追加で以下を実行してください。

  ```bash
  sed -i 's/\r//g' run.sh

  # 上記コマンドが必要なのはなぜ？
  # Windowsは改行コードが\r\nですが、UbuntuなどのUNIXでは\nのため、
  # 次のようなエラーが発生しうるためです。
  # $’\r’: command not found
  ```

- 配布ソフトウェアのダウンロード
  - Docker を用いたシステムを使用するためのソフトウェアを[https://huggingface.co/datasets/yubo0306/remdis-tools](https://huggingface.co/datasets/yubo0306/remdis-tools)よりダウンロードし、以下のように配置する
    ```
    Dialogue_System_Live_Competition_7
    |- config/
    |  |- config.yaml
    |- dist/
    |  |- input
    |  |- input.exe
    |  |- output
    |  |- output.exe
    |- docker/
    |
    ...
    ```
  - 上記のダウンロードは以下のコマンドで簡単に実現できる
    ```
    # ダウンロード
    cd Dialogue_System_Live_Competition_7
    git clone https://huggingface.co/datasets/yubo0306/remdis-tools dist
    ```

### Step 3. 各種 API 鍵の取得と設定

- Google Speech Cloud API の API 鍵を JSON 形式で取得し，config/config.yaml の下記部分にパスを記載
  ```
  ASR:
   ...
   json_key: <enter your API key>
  ```
  Docker を使用している場合、JSON ファイルを config 配下に配置するとマウントされます。その場合のファイルパスは`/home/ubuntu/dslc7/config/<file>.json`です。
- OpenAI の API 鍵を取得し，config/config.yaml の下記部分に記載
  ```
  ChatGPT:
    api_key: <enter your API key>
  ```
- Azure TTS の API 鍵を取得し，config/config.yaml の下記部分に記載
  ```
  azure: # This option is only used when "engine_name" is "azure". Otherwise, no setting is required.
    api_key: <enter your azure API key>
    region: <enter your azure region> # <-- japaneast or japanwest
  ```

### Step 4. MMDAgent-EX のインストール (Windows 以外)

- Windows 以外の OS は，[MMDAgent-EX 公式サイト](https://mmdagent-ex.dev/ja/)の[入手とビルド](https://mmdagent-ex.dev/ja/docs/build/) に従って MMDAgent-EX をインストール
- Windows はそのまま次へ（実行バイナリが同梱されているので手順不要）

## 利用方法

### Docker を用いて対話を行う場合

### Remdis の実行

```bash
cd docker

# タスクトラックの場合
docker compose -f docker-compose.dev.yaml up -d
docker compose -f docker-compose.dev.yaml exec remdis bash run.sh

# シチュエーショントラックの場合
docker compose -f docker-compose.prompt-only.yaml up -d
docker-compose -f docker-compose.prompt-only.yaml exec remdis bash run.sh
```

### 音声/動画入力サーバの起動

- Windows の場合
  エクスプローラから`dist/input.exe`を実行
- Mac の場合
  Finder から`dist/input`を実行

### MMDAgent-EX を起動せずに音声対話のみを行う場合

- Windows の場合
  エクスプローラから`dist/output.exe`を実行
- Mac の場合
  Finder から`dist/output`を実行

### MMDAgent-EX を起動した音声対話を行う場合

- Windows: `MMDAgent-EX/run.vbs` を実行
- Windows 以外: MMDAgent-EX フォルダのファイルを指定して MMDAgent-EX を実行
  ```sh
  cd MMDAgent-EX
  # タスクトラックの場合
  /somewhere/MMDAgent-EX/Release/MMDAgent-EX task.mdf
  # シチュエーショントラックの場合
  /somewhere/MMDAgent-EX/Release/MMDAgent-EX situation.mdf
  ```

### Travel Viewer を動かしてみる

Travel Viewer は以下の URL で公開されています。
- App: https://dslc7.github.io/travel-viewer/
- Repo: https://github.com/dslc7/travel-viewer

アプリを開き、`ws://localhost:9999` を設定し、CONNECT を押下してください。その後、以下のスクリプトを実行してください。

- [`scripts/run_sample_travel_viewer.sh`](scripts/run_sample_travel_viewer.sh) を実行
  ```sh
  docker compose -f docker-compose.dev.yaml exec remdis  bash scripts/run_sample_travel_viewer.sh
  ```

## TIPS

### マイクとスピーカーが正しく接続されているか確認したい

- chk_mic_spk.py を実行
  ```
  # 自分の発話がフィードバックされて聴こえていればOK
  python input.py
  python chk_mic_spk.py
  python output.py
  ```

### Audio VAP の出力を可視化したい

- draw_vap_result.py を実行
  ```
  # 音声対話の例
  python input.py
  python audio_vap.py
  python asr.py
  python dialogue.py
  python tts.py
  python output.py
  python draw_vap_result.py
  ```

### 一定時間が経過したらシステム側から話しかけるようにしたい

- time_out.py を実行
  ```
  # テキスト対話の例
  python tin.py
  python dialogue.py
  python tout.py
  python time_out.py
  ```

## 開発可能範囲

### シチュエーショントラック

シチュエーショントラックでは，本ソフトウェアのうち `prompts/response.txt`，`prompts/text_vap.txt`，`prompts/time_out.txt` の 3 ファイルについて自由に書き換えることができます．  
対話戦略へ焦点を当てた評価を実施するために，その他の部分については全チームで共通とさせていただきます．

### タスクトラック

タスクトラックでは，以下の3点を遵守していれば，他の部分は自由に開発いただけます．
- 音声合成に Azure API の `ja-JP-NanamiNeural` を用いること
- 指定のソフトウェア(MMDAgent-EX)において、指定の設定（[`task.mdf`](MMDAgent-EX/task.mdf), CGアバター・背景・各画像の配置等を指定）で表示・動作させる
- 指定の画像・地図表示システム([Travel Viewer](https://dslc7.github.io/travel-viewer/))を使用すること

## 開発時の参考情報

### 入力情報・形式

ユーザの音声と映像から発話文・顔向き・感情を取得します．取得された顔向きと感情は，離散化（顔向き：右向き・左向き・うなずき・首をかしげる・正面，感情：怒り・嫌悪・恐怖・幸福・悲しみ・驚き・中立）され，発話文/感情/顔向きの形で LLM に入力されます．

### CG アバターのモーションについて

現在はプロンプト中に記述されている以下の標準で定義されている感情と動きに対応しています．今後はなるべく早くより多様なモーションに対応する予定です．

感情：平静・喜び・感動・納得・考え中・眠い・ジト目・同情・恥ずかしい・怒り

動き：待機・ユーザの声に気づく・うなずく・首をかしげる・考え中・会釈・お辞儀・片手を振る・両手を振る・見渡す

### TTS の出力スタイルについて

TTS の出力スタイルを発話にあわせて chat・cheerful・customerservice の 3 つからプロンプトを用いて動的に変更することができます．TTS のスタイルを変更したい場合は config/config.yaml の ChatGPT 内の output_tts_style を on に、prompts 内の RESP を prompt/response_w_tts_style.txt に変更してください．

## ライセンス

### ソースコードの利用規約

本リポジトリに含まれるオリジナルのソースコードは，ライブコンペ 7 にエントリし，本リポジトリへのアクセス権を付与された代表者およびその共同開発者が，ライブコンペ 7 のシステムを開発する際のみにご利用いただけるものとし，利用者以外の第三者に提供、販売、貸与、譲渡、再配布する行為を禁じます．
加えて，他のライセンスがすでに付与されているファイルはそのライセンスにも注意を払ってご利用ください．

### 外部パッケージの利用規約

本ソフトウェアでは，音声認識に[Google Cloud Speech-to-Text API](https://cloud.google.com/speech-to-text?hl=ja)，音声合成に[Azure Text-to-Speech](https://learn.microsoft.com/en-us/azure/ai-services/speech-service/text-to-speech)，対話生成に[OpenAI API](https://openai.com/blog/openai-api)，ターンテイキングに[VAP](https://github.com/ErikEkstedt/VAP.git)，顔画像処理に[Py-feat](https://github.com/cosanlab/py-feat)及び[Py-feat 内のモデル](https://github.com/cosanlab/py-feat?tab=License-1-ov-file)といった外部パッケージを利用します．
ライセンスに関してはそれぞれのパッケージの利用規約をご参照ください．

## References

### Remdis

```
@inproceedings{remdis2024iwsds,
  title={The Remdis toolkit: Building advanced real-time multimodal dialogue systems with incremental processing and large language models},
  author={Chiba, Yuya and Mitsuda, Koh and Lee, Akinobu and Higashinaka, Ryuichiro},
  booktitle={Proc. IWSDS},
  pages={1--6},
  year={2024},
}
```

```
@inproceedings{remdis2023slud,
  title={Remdis: リアルタイムマルチモーダル対話システム構築ツールキット},
  author={千葉祐弥 and 光田航 and 李晃伸 and 東中竜一郎},
  booktitle={人工知能学会 言語・音声理解と対話処理研究会（第99回）},
  pages={25--30},
  year={2023},
}
```

### Audio VAP

```
@inproceedings{vap-sato2024slud,
  title={複数の日本語データセットによる音声活動予測モデルの学習とその評価},
  author={佐藤友紀 and 千葉祐弥 and 東中竜一郎},
  booktitle={人工知能学会 言語・音声理解と対話処理研究会（第100回）},
  pages={192--197},
  year={2024},
}
```
 