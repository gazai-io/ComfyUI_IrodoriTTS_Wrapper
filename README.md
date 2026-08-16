# ComfyUI_IrodoriTTS_Wrapper

[IrodoriTTS](https://github.com/Aratako/Irodori-TTS)のComfyUI用カスタムノードです。

## install
1. custom_nodesフォルダ上でgit clone
2. 仮想環境を有効化した上で `pip install -r ComfyUI_IrodoriTTS_Wrapper/requirements.txt`
3. 必要なら`checkpoints`フォルダに[Irodori-TTS-v4.1-Small](https://huggingface.co/Aratako/Irodori-TTS-v4.1-Small)の`model.safetensors`をDLする

> [!NOTE]
> v4.1-Smallはupstreamの現行環境に合わせて`transformers>=5.12.1`を必要とします。upstreamはPyTorch/torchaudio 2.10以上を指定していますが、これらはComfyUI側の環境を使用するため、このリポジトリの`requirements.txt`ではインストールしません。

## v4.1-Small

`IrodoriTTS Model Loader HF`のデフォルトは`Aratako/Irodori-TTS-v4.1-Small`です。HF Loaderはモデル本体に加えて、v4.1で必要な同梱tokenizerも取得します。ローカルモデルでは、同梱tokenizerが`model.safetensors`と同じディレクトリの`tokenizer/`にある場合は自動で使用します。

v4.1-Smallは1つのcheckpointで次の生成方法に対応します。

- テキスト + 参照音声によるvoice cloning
- テキスト + `caption`によるVoiceDesign（参照音声なし）
- テキスト + 参照音声 + `caption`によるstyle-controlled voice cloning
- 自動duration prediction（`seconds_override <= 0`）
- 最大120秒の参照音声（`max_ref_seconds <= 0`でcheckpoint既定値を使用）

既存のv2/v3 checkpointもupstreamの後方互換ローダーで引き続き読み込めます。v4.1用に別ノードへ分割する必要がないため、既存ノードを拡張しています。


## ノード一覧
- IrodoriTTS Model Loader
- IrodoriTTS Model Loader HF
  - モデルを読み込みます
  - HF版はHugging Faceリポジトリからモデルと同梱tokenizerをDLします
  - `owner/repo/subfolder`形式にも対応します（量子化checkpointを使う場合、別途`torchao`が必要です）

- IrodoriTTSSampler
  - テキストの入力と実際の生成を行います
  - v3/v4.1では`seconds_override <= 0`で自動長さ推定、`duration_scale`で長さを微調整できます
  - VoiceDesignおよびv4.1モデルでは`caption`に声質やスタイル指示を入力できます

- IrodoriTTS Referenec Audio
- IrodoriTTS Advanced CFG
- IrodoriTTS Rescale Config
  - オプション設定用のカスタムノードです

- IrodoriTTS Emoji Selector
  - IrodoriTTSで使用できる絵文字一覧です。
  - ボタンクリックで、クリップボードに絵文字をコピーします
