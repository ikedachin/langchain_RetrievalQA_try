# 目的
audioデータから文字起こし、内容抽出する
- OpenAIのwhisperを用いて文字起こし
- langchainとOpenAIのAPIを使って、文字起こししたテキストから必要な内容を抽出する

# langchainのバージョンが二つ
- ver=0.0.239（クラシックなスタイル）
- ver=0.0.350（LCELのスタイル）

## 共通
api keyをソース内に記述しないための準備をします。 
このreadme.mdファイルと同じ階層に「api.yaml」ファイルを作成する。
OpenAIでapi keyを取得し、ファイルの中には以下のように記述する。
```
'openai': "sk-*****（OpenAIのAPI keyを記述）"
```
# 環境構築
私の環境構築のスタイルはvenvを用いて、作業フォルダ内に環境を作る。（作業内容と1:1で対応させるため）
langchainの二つのバージョンは同時に存在できないので、以下のように環境を分けている。  
実際に使うときはテキスト起こしと、いずれかのlangchainの環境を組み合わせた一つの仮想環境を作った方が便利。
- audio2textの環境
- langchain0.0.239の環境
- langchain0.0.350の環境

## audio2textの環境の作り方
windowsの場合python3の「３」は不要。
```
python3 -m venv audio2text_env（環境名）
```
### 仮想環境に入る
#### Mac
cmdで以下を手打ち
```
source ./audio2text_env/bin/activate
```
（いつも「source」だけ手打ちして、「activate」フォルダをドラッグ&ドロップで仮想環境に入る）

次もcmdに入力
```
pip install -r ./audio2text/requirements.txt 
```
audio2textフォルダ内のipynbをポチポチと進めるだけ
途中、whisperのインストールは最初だけ実行すること。


## retrievalQAの環境の作り方
langchain==0.0.350を例に記す。0.0.239も同じ（はず）。（0.0.239の場合は環境名を変えてね）
windowsの場合python3の「３」は不要。
```
python3 -m venv langchain_350_env（環境名）
```
### 仮想環境に入る
#### Mac
cmdで以下を手打ち（0.0.239の場合は環境名を変えてね）
```
source ./langchain_350_env/bin/activate
```
（いつも「source」だけ手打ちして、「activate」フォルダをドラッグ&ドロップで仮想環境に入る）

次もcmdに入力
```
pip install -r ./RetrievalQA_langchain_0.0.350/requirements.txt 
```
RetrievalQA_langchain_0.0.350フォルダ内のipynbをポチポチと進めるだけ


# フォルダ説明
## audio2test
Youtubeから音声データの抜き取り、文字起こしまでを行う

## RetrievalQA_langchain_・・・（バージョン名）
audio2testで文字起こししたテキストをlangchainを用いてretrievalQAを行う

# 使い方
## audio2text



