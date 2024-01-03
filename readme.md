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
- Youtubeから音声データの抜き取り、文字起こしまでを行う

## RetrievalQA_langchain_・・・（バージョン名）
- audio2testで文字起こししたテキストをlangchainを用いてretrievalQAを行う

# 使い方
## audio2text
modelの種類を以下の行で変更する。largeモデルの方が精度が高いが、時間がかかる。
```
model_size = 'medium' # 'large', 'medium', 'small', 'base'
```

## RetrievalQA_langchain
langchainのモデルによって仮想環境を変えているので、notebookを使うときにカーネル（仮想環境）の選択に注意。  
0.0.239の場合は以下でチェーンタイプを変更可能。チェーンタイプが全く違うのでノートブック内の記述に注意。  
modelの種類とtemperatureの設定にも留意すること。  
```
qa_chain = RetrievalQA.from_chain_type(
    llm=OpenAIChat(model='gpt-4-1106-preview', temperature=0), # LLM
    # llm=OpenAI(model='gpt-4-1106-preview', temperature=0), # LLM
    # chain_type='stuff', # チェーンタイプ
    chain_type='refine', # チェーンタイプ
    # chain_type='map_reduce', # チェーンタイプ
    # chain_type='map_rerank', # チェーンタイプ
    retriever=docsearch.as_retriever(), # リトリバー
)
```

0.0.350の場合は以下のllmのモデル選定のところで入力トークン数にあったモデルを選択することが大事。
```
llm = ChatOpenAI(model_name="gpt-4-1106-preview", temperature=0)
```


# 出力結果について
0.0.239の使い方と0.0.350の使い方で結果が大きく異なる。

## 0.0.350のQuestion Answering
```
rag_chain.invoke('講演者はチャットGPTを使ったことがない人に何をした方がいいと言っていますか？')
```
Answer
```
講演者は、チャットGPTを使ったことがない人に対して、「人生を悔い改めた方がいい」と言っています。また、「自分自身の人生を自分は一体どういう考えで今まで生きてきたんだと」と自問自答するように促しています。
```

## 0.0.239のQuestion Answerring
```
qa_chain.run('孫さんは生成AIを使ったことがない人になんと言っていますか？日本語で教えてください。')
```
Answer
```
新しいコンテキストを考慮に入れた上で、元の回答を修正する必要があります。孫さんは、生成AIを使ったことがない人に対して、AIの可能性について語っているようです。彼はAIが特定の分野で人間の能力を超えていることを認めつつも、まだ全ての分野で人間を超えるには至っていないと述べています。そして、AIに限界があるという意見に対して、それはAIの限界ではなく、人々の理解に限界があると反論しています。彼は、将来的にAIがほぼ全ての分野で人間の知識を超えると信じていることを示唆しています。\n\nこのコンテキストに基づいて、孫さんが生成AIを使ったことがない人に言っていることは、AIの現在の能力と将来の可能性についての彼の見解であり、特に生成AIの活用コンテストについて言及しているわけではないようです。したがって、元の回答はこの新しいコンテキストには適していないため、以下のように修正します。\n\n孫さんは、生成AIを使ったことがない人に対して、AIが特定の分野では既に人間の能力を超えているが、全ての分野でそれが実現しているわけではないと説明しています。そして、AIに限界があるという意見に対しては、それはAIの限界ではなく、人々の理解に限界があると指摘しています。彼はAIの将来的な発展に楽観的であり、いずれAIがほぼ全ての分野で人間の知識を超えると考えているようです。
```