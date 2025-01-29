無料で学ぶ！生成AIとバズった技術まとめ（LLM、LangChain、Dify、SAM2、Stable Diffusion、RVCなど）
https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625

---

# まえがき

「ここ数年でバズったAI技術や、最近勢いのある生成系AI技術をキャッチアップしたい」

最近のLLMブームにより、一般の人も簡単にAI技術を使いこなせるようになりました。  
特に、もともとAIに明るくなくても、エンジニアなら実装までできるので、インパクトのあるプロダクトを作ることが可能になりました。  
「これまでモデル開発をしてきたデータサイエンティスト・AIエンジニアの立場が危ぶまれている気がする」  
そんな危機感から、一通り、まずは知る・使えるようになることを目指してこの記事を書くことにしました。

- 前半パート: 使ってみる編（全員向け）
- 後半パート: LLMを使った開発編（基本エンジニア向け）

スクロールバーが長いけど、画像が多いのでサクサク読めるはずです。  
[![スクリーンショット 2025-01-24 20.57.31.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8b674395-1cd5-19fa-0997-b0946d308263.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=cdb32543c920e23afefff852abe2c415)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8b674395-1cd5-19fa-0997-b0946d308263.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=cdb32543c920e23afefff852abe2c415)
## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E5%AF%BE%E8%B1%A1%E8%AA%AD%E8%80%85)対象読者

- 最近のAIの動向を知りたい人 / 使ってみたい人（前半パート）
- LLMを使ったプロダクト開発（LangChain）を始めようとしている人（後半パート）
- **無料**でLLM技術を学びたい学生
- LLMを全く知らない人

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A0%85)注意事項

- さまざまなツール、ウェブサイトを紹介しますが、**利用規約やライセンスなど確認**のうえお使いください
- この記事は2025年1月ごろに書かれたものです
- この記事を書いている最中も次々と新しいサービスが出てくるため、網羅性はありません

一部、Pythonコードが出てくるパートがありますが、Python単体の環境構築自体は済んでいるものとします。  
筆者の環境:

- Windows11
- Python3.10系
- GPUなし
- RAM 32GB
- AMD Ryzen 5 5560U with Radeon Graphics

本記事は、世の中にある技術とサービスの周知、技術検証を目的にしています。

# [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E4%BD%BF%E3%81%A3%E3%81%A6%E3%81%BF%E3%82%8B%E7%B7%A8)使ってみる編

ほとんどは**環境構築不要でWeb上で利用できる**ものを紹介します。

一部、環境構築が必要なものもあります。  
環境構築は面倒なイメージがありますが、AIツールを楽に試す[pinokio](https://pinokio.computer/)なんてツールもあります。(対応しているもののみ)  
UI上でボタンを押していくだけで環境構築ができ、AI技術を試すことができます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fff2aa104-9a8f-4e6e-caa9-97d15c80b592.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5c14d04968440f86b57879ad923417f5)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fff2aa104-9a8f-4e6e-caa9-97d15c80b592.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5c14d04968440f86b57879ad923417f5)

なお、紹介するサービスや技術自体の詳しい説明はしません。

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#llm)LLM

LLM（Large Language Model）は、大量のテキストデータをもとにトレーニングされた自然言語処理モデルで、質問応答や翻訳、文章生成など多様なタスクをこなします。  
主に[Transformer](https://qiita.com/birdwatcher/items/b3e4428f63f708db37b7)構造を持ち、入力文章から文脈を理解し、次に生成するべき単語を予測しながら文章を生成します。

それぞれの会社が独自のモデルを開発したり、LLMを活用して新たなサービスを作ったりと、利用者にとっては驚きが大きいです。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#chatgpt)ChatGPT

[ChatGPT](https://chatgpt.com/)は、OpenAIのチャットAIサービスです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2728175f-8fdd-df61-ead4-088592b30628.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=60c65cfafcc17cc38a0b61af1da78be3)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2728175f-8fdd-df61-ead4-088592b30628.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=60c65cfafcc17cc38a0b61af1da78be3)

- チャット機能  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2b78e3a6-7575-7ee7-6ebd-b1003c59866a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=816653fb77990167c8a1587ee07d3d6b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2b78e3a6-7575-7ee7-6ebd-b1003c59866a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=816653fb77990167c8a1587ee07d3d6b)
    - GPT-4o (無料枠は1日の上限があり、切れるとminiに切り替わる)
        - 画像の説明もできます  
            [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fad38ae46-c60f-c244-63e3-251009f2b528.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8752c5dcfdf5a6a834beb4b0d568c54d)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fad38ae46-c60f-c244-63e3-251009f2b528.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8752c5dcfdf5a6a834beb4b0d568c54d)
        - 動画もアップロードできるが説明能力はイマイチか？  
            [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F79fd8956-851f-16fb-f8db-ead4fd97eca1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4a9934fed0242cc7d1c3c98aa92f7c30)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F79fd8956-851f-16fb-f8db-ead4fd97eca1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4a9934fed0242cc7d1c3c98aa92f7c30)
            - youtubeのエンディングに使っている短い文鳥動画をアップしてみましたが、ラフなやりとりでは説明してくれませんでした
        - PDF入力  
            [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F01b7f246-7827-c0c6-729f-ce99cc9a1b11.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6b7d5b916cb1feefef1e1d5294689b0b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F01b7f246-7827-c0c6-729f-ce99cc9a1b11.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6b7d5b916cb1feefef1e1d5294689b0b)
            - 自分のarvix論文を入力してみましたが、ちゃんと読み取れているようです
    - GPT-4o mini
        - 添付ファイルはサポートしていません
- Web検索  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F6d2c6a35-fbf1-3ff3-bf68-3894458da025.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f55273a5c4ed7aad128da4f1d858f780)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F6d2c6a35-fbf1-3ff3-bf68-3894458da025.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f55273a5c4ed7aad128da4f1d858f780)  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa0f5e0d3-d15a-bfdb-7ece-632d9bab7271.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3ab4eee5913a20a157f194fa3a924437)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa0f5e0d3-d15a-bfdb-7ece-632d9bab7271.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3ab4eee5913a20a157f194fa3a924437)
    - Web上の情報を元に回答してくれます
    - 情報源も提示してくれます
- 画像の生成も可能（無料版は1日の上限がある模様）
    - DALL-Eが使われているらしい  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe8bbf7a6-87a3-745b-017b-257a8e04c41d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=64e7cd25199ab1cfd55fd65925757d09)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe8bbf7a6-87a3-745b-017b-257a8e04c41d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=64e7cd25199ab1cfd55fd65925757d09)

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#gemini)Gemini

[Gemini](https://gemini.google.com/?hl=ja)はGoogleのチャットAIサービスです。

- Gemini 1.5 Flashが無料で使えます  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5e6a224a-1354-a269-5c05-1e95c6d8a929.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=cc2f05b7e695e7145eef97c399d8abb2)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5e6a224a-1354-a269-5c05-1e95c6d8a929.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=cc2f05b7e695e7145eef97c399d8abb2)
    - 画像入力  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8e3404b6-95b8-9e34-7ed5-b0fa57b7646c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ce005e6292d040fe7dae176e29a8a06d)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8e3404b6-95b8-9e34-7ed5-b0fa57b7646c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ce005e6292d040fe7dae176e29a8a06d)
        - 画像を説明してくれました
    - 動画のアップロード機能はありませんでした
    - 画像を生成することもできる  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Faafb8c53-b269-40c3-b804-f646469ade08.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fb963b60186b9af3792d15fbe730c9ce)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Faafb8c53-b269-40c3-b804-f646469ade08.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fb963b60186b9af3792d15fbe730c9ce)
        - [Imagen 3](https://deepmind.google/technologies/imagen-3/)が使われているようです
- gemini 1.5 pro は有料です

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#google-ai-studio)Google AI Studio

[Google AI Studio](https://aistudio.google.com/)は、開発者向けの生成AIツールです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F0d5ed061-119c-2b7c-9b05-15e9c359d053.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=97b9dcd4cdf5395efe68711eebb403d5)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F0d5ed061-119c-2b7c-9b05-15e9c359d053.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=97b9dcd4cdf5395efe68711eebb403d5)

- 先程のGeminiで有料だったGemini 1.5 Proを試すことができます  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe8025d95-138e-38c5-4b02-7ede18a0ebda.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=785108c65d74f9806b6e03e602c4a4fa)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe8025d95-138e-38c5-4b02-7ede18a0ebda.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=785108c65d74f9806b6e03e602c4a4fa)
    - 先程のGeminiと同様に画像生成をチャットから依頼してみましたが、画像生成はできないようでした  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fd76fcf14-6815-115a-7d65-9fe06e7b6eb9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=316fbf974d32e99185aa888912d39fd8)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fd76fcf14-6815-115a-7d65-9fe06e7b6eb9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=316fbf974d32e99185aa888912d39fd8)
- 画像入力  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fac0d5f79-ab8a-f0ba-3ddd-0a33bef06d2b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3a2a1998dbf43239fa23631efad73fef)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fac0d5f79-ab8a-f0ba-3ddd-0a33bef06d2b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3a2a1998dbf43239fa23631efad73fef)
- 動画入力  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F64177e5d-c675-5ec7-d8a9-a404c5e3022f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bba857a09be685fde89d3d2172ab42fd)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F64177e5d-c675-5ec7-d8a9-a404c5e3022f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bba857a09be685fde89d3d2172ab42fd)
    - 文鳥だけどちゃんとそれっぽい説明がされてます
- pdf入力  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fdaada730-e45a-b698-c048-12fcc24767a8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e2d705f3694ee44be89371a7174cb247)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fdaada730-e45a-b698-c048-12fcc24767a8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e2d705f3694ee44be89371a7174cb247)
- Gemini 2.0 Flash Experimentalや、オープンソースの[Gemma](https://ai.google.dev/gemma/docs/get_started?hl=ja)も試すことができます
- パラメータ/ツール
    - Temperature: 文章生成の多様性・創造性の調整
        - 低い温度: 正確性と一貫性が重要なタスク
        - 高い温度: 多様性と創造性を求めるタスク
    - Structured output: 自然言語ではなく、Jsonの構造化出力をするように指定できる
    - Code execution: プログラムのソースコードを実行できる機能
    - Function calling: LLMが外部のAPIやツールを関数のように呼び出す機能
    - Grounding: Webページなど外部の情報源を参照して回答を生成する機能
        - 回答根拠としてソースを提示できる
- API keyを発行してPythonから利用することもできます（後半パート）

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#claude)Claude

[Claude](https://claude.ai/)は、アメリカのAnthropicのチャットAIサービスです。

- Claude 3.5 Sonnet  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F90ffc56f-3389-7ca6-a6bc-bb953dcdca04.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b43702d20a2d820d58d99107cbae10b9)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F90ffc56f-3389-7ca6-a6bc-bb953dcdca04.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b43702d20a2d820d58d99107cbae10b9)
- 画像入力  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F29780f26-39db-0323-1e27-57af8dc79df1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8ab9370980cca36a47ea6d38d1de9453)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F29780f26-39db-0323-1e27-57af8dc79df1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8ab9370980cca36a47ea6d38d1de9453)
- pdf入力  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F44643331-babc-a47a-27b9-877ee6af236e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6692b45257bd7cdf56e78fbb84ed66d1)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F44643331-babc-a47a-27b9-877ee6af236e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6692b45257bd7cdf56e78fbb84ed66d1)
    - arxiv論文は分量が長すぎて無料版では無理でした
- 動画入力や、画像生成できませんでした

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#notebooklm)NotebookLM

[NotebookLM](https://notebooklm.google.com/)は、外部ドキュメントを説明してくれるGoogleのサービスです。

- URLやドキュメントを与えて質問をなげたりできる  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc81b15ba-9359-8e7a-846d-ba464487b9fa.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9f57940d79cb7222a17a7ac5951466de)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc81b15ba-9359-8e7a-846d-ba464487b9fa.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9f57940d79cb7222a17a7ac5951466de)

「ウェブサイト」から[昔書いたQiitaの記事](https://qiita.com/birdwatcher/items/b3e4428f63f708db37b7)のリンクを入れてみました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb3882d57-721e-7e55-4e13-5bfd326799b3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ffb17091996e12506a9ef521c3a9566b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb3882d57-721e-7e55-4e13-5bfd326799b3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ffb17091996e12506a9ef521c3a9566b)

チャット形式で質問をすると、ソースを元に回答してくれます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2ec28b97-acb2-2991-0584-f32538516bbb.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5186ea60d331acd126e52513fb1912e8)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2ec28b97-acb2-2991-0584-f32538516bbb.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5186ea60d331acd126e52513fb1912e8)

英語の文献を入れても日本語で対話できるため、論文を読む手間を減らせるかもしれません。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#deepseek-v3)DeepSeek V3

[DeepSeek V3](https://www.deepseek.com/)は、中国のAIスタートアップ企業のサービスです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2fe6dd33-a23e-e5a2-7654-cb727f4370b7.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=303b56581e0533a5e635226cbf129a61)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2fe6dd33-a23e-e5a2-7654-cb727f4370b7.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=303b56581e0533a5e635226cbf129a61)  
ChatGPTにも匹敵すると言われているにも関わらず、[オープンソース](https://github.com/deepseek-ai/DeepSeek-V3)であることが特徴です。

- チャット  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F92e9d51b-114c-fea0-69b5-45a33e677e57.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=834b23d8ee4d93f68f6a0cf7553e5231)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F92e9d51b-114c-fea0-69b5-45a33e677e57.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=834b23d8ee4d93f68f6a0cf7553e5231)
    - タイトルは中国語になるようです
    - pdf入力  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F15be474b-1c23-225e-fea2-62623fc4a8e5.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3b043116f109bca43d8db4ed89a1bdfe)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F15be474b-1c23-225e-fea2-62623fc4a8e5.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3b043116f109bca43d8db4ed89a1bdfe)
- DeepThink  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F43bb417a-167a-58b3-6d6e-3976497801eb.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ccbe907255a0df623ed179e659c1cc70)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F43bb417a-167a-58b3-6d6e-3976497801eb.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ccbe907255a0df623ed179e659c1cc70)
    - 英語で返ってきました
- Search  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa5ad3c43-25a3-364c-5e39-950543d912b2.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7959d537851901514da23851d2ce7b39)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa5ad3c43-25a3-364c-5e39-950543d912b2.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7959d537851901514da23851d2ce7b39)
    - Web上のソースを利用して回答してくれます

この記事を出す直前にDeepSeek-R1も出てきました。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#llm-leaderboard)LLM leaderboard

「いろんなチャットAIモデルを紹介したけど、結局どれがいいの？」  
そんな疑問の判断基準に使えるLLMのリーダーボードのサイト紹介です。

- LLMモデルの性能比較
    - [Vellum LLM Leaderboard](https://www.vellum.ai/llm-leaderboard)
    - [Open LLM Leaderboard](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard#/)
        - オープンソースのLLM比較
    - [Chatbot Arena LLM Leaderboard](https://lmarena.ai/)
        - ユーザーの投票による評価
- 日本語モデルの性能比較
    - [Nejumi LLMリーダーボード3](https://wandb.ai/wandb-japan/llm-leaderboard3/reports/Nejumi-LLM-3--Vmlldzo3OTg2NjM2)
    - [オープン日本語LLMリーダーボード](https://huggingface.co/spaces/llm-jp/open-japanese-llm-leaderboard)
    - [日本語 Open LLM Leaderboard](https://wandb.ai/llm-jp-eval/open-llm-leaderboard/reports/-Open-LLM-Leaderboard--Vmlldzo2MTE0NDA3)

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E3%83%97%E3%83%AD%E3%83%B3%E3%83%97%E3%83%88%E3%81%AE%E5%B7%A5%E5%A4%AB%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)プロンプトの工夫について

LLMの出力結果は、入力（プロンプト）のクオリティに依存します。  
これまで使ってきて効果的だと思ったことを記載します。

- markdown形式やXMLなど構造のわかる形式で記載する（モデルごとに適性がある）
- 入出力例を与える（Few-shot）
- 回答を導き出すステップを明示する（Chain of Thought）
- 「順序立てて」「ステップバイステップで」などと命令する（Chain of Thought）
- 役割を与える（「あなたは優秀な〇〇です」）
- 「答えがない場合に無理やり答えないでください」（ハルシネーション対策）
- Json出力（フォーマットに従ってほしくて余分な言葉を出させない場合）

こういったサイトも参考になります：

- [Prompt Engineering Guide](https://www.promptingguide.ai/jp)
- [プロンプト技術を高めるテクニック集【入門編】](https://zenn.dev/knowledgesense/articles/7504b1c6bbba84)

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E3%83%AA%E3%82%B5%E3%83%BC%E3%83%81)リサーチ

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#felo)Felo

[Felo](https://felo.ai/ja/search)は日本発のAI検索サービス。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe0b77bca-d68e-8c30-cec6-4d531012bcb8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=843d71c5da739bd1bf76da144b59f6f9)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe0b77bca-d68e-8c30-cec6-4d531012bcb8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=843d71c5da739bd1bf76da144b59f6f9)  
検索ソースとして、Web、SNS、論文、Xと様々指定ができます。

- クイック検索
    - 「生成AI系のサービスを調査してまとめてください」と入力してみました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5e3ca397-a21f-d76a-2fef-c7607ac83c5f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=627415a3723357b71724c02ade9df7c0)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5e3ca397-a21f-d76a-2fef-c7607ac83c5f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=627415a3723357b71724c02ade9df7c0)
        - 24個のソースからまとめてくれました
        - 結果はMarkdownとしてコピーできるので便利です
        - この結果から「マインドマップ」を押したところこのような図を作ってくれました  
            [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F6c90e971-db14-2ffd-f4d6-7ea3b079e19a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f5796c7d6d948e4402093122c4ebd9fd)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F6c90e971-db14-2ffd-f4d6-7ea3b079e19a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f5796c7d6d948e4402093122c4ebd9fd)
        - 「プレゼンテーション生成」もありました
- ディープ検索
    - 無料プランだと1日5回まで
    - 同じく「生成AI系のサービスを調査してまとめてください」と入力してみました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F06ae76dc-b768-cf25-c19a-94215c734363.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1ef8d2bfe569549280b6a2d5e6ec4ff1)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F06ae76dc-b768-cf25-c19a-94215c734363.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1ef8d2bfe569549280b6a2d5e6ec4ff1)
        - より深く40個のソースからまとめてくれました

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#genspark)Genspark

[Genspark](https://www.genspark.ai/)も、AI検索で有名なサービスです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F7496a4f6-358d-b1d0-06e4-1183caeb7c01.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6e39f5f373818fc6a8dd72b957d7900c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F7496a4f6-358d-b1d0-06e4-1183caeb7c01.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6e39f5f373818fc6a8dd72b957d7900c)

- 一般チャット
    - いろんなモデルを組み合わせて回答することができるようです  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5423d809-fc64-3321-bbff-d6197ecf9168.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4efa416933a945a763430125c48cfae2)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5423d809-fc64-3321-bbff-d6197ecf9168.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4efa416933a945a763430125c48cfae2)
- 画像生成
    - いろんなモデルを組み合わせて生成ができました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F0ce1ba16-f58a-71bb-e7f4-6afaaecea556.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=da4656d77e98680780a000d21ab8bcc6)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F0ce1ba16-f58a-71bb-e7f4-6afaaecea556.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=da4656d77e98680780a000d21ab8bcc6)
- データ検索
    - AIエージェントが深く調査してくれます  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3fbe0c6a-26c0-75be-6fb3-3389cf93b914.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=88ad7b734186467175832f5e19d00860)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3fbe0c6a-26c0-75be-6fb3-3389cf93b914.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=88ad7b734186467175832f5e19d00860)
        - 平均30~40分くらいかかるようです
        - クイックアンサーはすぐに出ます
        - このタスクは30分で終わりました
- ファクトチェック
    - AIエージェントが事実確認のための調査をしてくれます  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa0fc8abf-7394-dae5-d75e-307bc5671706.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3b0adf8681e04b813ad5393a79c34e8c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa0fc8abf-7394-dae5-d75e-307bc5671706.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3b0adf8681e04b813ad5393a79c34e8c)
        - 平均10分くらいかかるようです
        - このタスクは8分で終わりました

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#perplexity)Perplexity

[Perplexity](https://www.perplexity.ai/)も、AI検索として有名です。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb0f3c9d2-2578-c542-1618-78ea0a5e58b9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=01fe0b939a2173e6b116ec35b5c8e757)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb0f3c9d2-2578-c542-1618-78ea0a5e58b9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=01fe0b939a2173e6b116ec35b5c8e757)

- 通常の検索
    - 「生成AI系のサービスを調査してまとめてください」と入力してみました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff55cbef0-0cc4-9920-218b-e919d2a72872.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d42204da47b4e6b4d3612cb3ff3c68ba)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff55cbef0-0cc4-9920-218b-e919d2a72872.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d42204da47b4e6b4d3612cb3ff3c68ba)
        - 10個のソースからまとめてくれました
- Pro検索
    - 「生成AI系のサービスを調査してまとめてください」と入力してみました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F030723f3-32f8-df1f-6a3e-99df6e290fab.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=dca9b99b9a15ccf46911d27b842b805c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F030723f3-32f8-df1f-6a3e-99df6e290fab.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=dca9b99b9a15ccf46911d27b842b805c)
    - 20個のソースからまとめてくれました
    - 無料プランでは1日3回まで

ソースの数だけでいうとFeloの方が多い結果となりました。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#storm)STORM

[STORM](https://storm.genie.stanford.edu/)は、スタンフォード大学の研究チームによって開発されました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5b2c71aa-0162-f961-3e91-9e25ca589535.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ae8a782405364d4c39d65b5cdf3d266e)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5b2c71aa-0162-f961-3e91-9e25ca589535.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ae8a782405364d4c39d65b5cdf3d266e)

- 検索を通してwikipediaのような記事を作れるサービス
- 現在、英語のみ対応の様子

> STORM is now powered by Bing Search and Azure OpenAI GPT-4o-mini!

- Bing検索とGPT-4o-miniが使われているようです

「Please research and summarize generative AI services.」と入力してみました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff20242ca-866e-fce7-ba21-ff67a5148857.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0d762d82ad80e00635e555f1701ebdaf)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff20242ca-866e-fce7-ba21-ff67a5148857.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0d762d82ad80e00635e555f1701ebdaf)

- wikipediaっぽい記事ができました

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E8%B3%87%E6%96%99%E4%BD%9C%E6%88%90)資料作成

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#napkin-ai-%E5%9B%B3%E4%BD%9C%E6%88%90)Napkin AI: 図作成

[Napkin AI](https://www.napkin.ai/)は、資料の図作成のサービスです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F22eba3fb-2390-73fb-fdc9-effc472b7c05.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ce73daf2f670bd211d5b6acadc5ceb14)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F22eba3fb-2390-73fb-fdc9-effc472b7c05.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ce73daf2f670bd211d5b6acadc5ceb14)

- 「自分で入力した文章」または「プロンプトから生成した文章」から図を選んで作ることができます  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff8c03c7e-7465-49ff-1909-f20a3dc8eeac.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=79252a9b5688d4159ad370f3958466a3)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff8c03c7e-7465-49ff-1909-f20a3dc8eeac.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=79252a9b5688d4159ad370f3958466a3)

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E7%94%BB%E5%83%8F%E5%8B%95%E7%94%BB%E9%9F%B3%E6%A5%BD%E7%94%9F%E6%88%90)画像・動画・音楽生成

このセクションでは、メディアを生成できるサービスを紹介します。  
動画生成というとOpenAIの[SORA](https://openai.com/sora/)が有名かもしれませんが、有料ユーザーだけが使える機能のため、ここでは紹介しません。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#labsgooglefx)Labs.google/fx

[Labs.google/fx](https://labs.google/fx/ja)は、Googleのサービス。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb71f53fb-8f12-893e-4ed4-aa195019bb7f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7e308bbc6896bd8e5e36efd155a28354)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb71f53fb-8f12-893e-4ed4-aa195019bb7f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7e308bbc6896bd8e5e36efd155a28354)

- [ImageFX](https://labs.google/fx/ja/tools/image-fx): 画像生成
    - 「cream colored java sparrow」と入力してみました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2c1c0acf-812b-2c0b-4ff5-4f9bd0404e45.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=120714f46678aa80223ac6b1cad6ac3a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2c1c0acf-812b-2c0b-4ff5-4f9bd0404e45.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=120714f46678aa80223ac6b1cad6ac3a)
    - [Imagen3](https://deepmind.google/technologies/imagen-3/)が使われているみたい
    - Googleアカウントがあれば無料で画像生成を試すことができます
- [VideoFX](https://labs.google/fx/ja/tools/video-fx): 動画生成
    - [Veo 2](https://deepmind.google/technologies/veo/veo-2/)が使われているみたい
    - 2025年1月現在、順番待ちのためすぐに利用することはできませんでした
- [MusicFX](https://labs.google/fx/ja/tools/music-fx): 音楽生成
    - 「japanese rock」と入力してみました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe822f67f-e3ac-451e-d320-12643b378825.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5be31961edb4697f72816d07996e1eff)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe822f67f-e3ac-451e-d320-12643b378825.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5be31961edb4697f72816d07996e1eff)
        - 30秒の曲が2つ生成されました
- [Whisk](https://labs.google/fx/ja/tools/whisk/): 画像から画像生成
    - 画像入力して画像を生成するサービスのようです
    - 「Whisk is not available in your country yet」ということでまだ使えませんでした

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#hailuo-ai-%E5%8B%95%E7%94%BB%E7%94%9F%E6%88%90)Hailuo AI: 動画生成

[Hailuo AI](https://hailuoai.video/)は中国のAI企業MiniMaxの動画生成サービスです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2b88fdae-c998-383d-b05b-dcd045b1b737.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0635d7189c192644dac70fe8347bf928)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2b88fdae-c998-383d-b05b-dcd045b1b737.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0635d7189c192644dac70fe8347bf928)  
長く待ち時間が発生するケースもありましたが、以下の通り、無事生成できました。

- Text to Video
    
    - 「文鳥が飛んでいる様子」と入力したら、文鳥っぽくない鳥が生成されました  
        [![336949101310341125.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F255eb90a-f26b-e5f1-a2e0-a9d8c589131d.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=27677695b97925b5622b540a359f158c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F255eb90a-f26b-e5f1-a2e0-a9d8c589131d.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=27677695b97925b5622b540a359f158c)
- Image to Video
    
    - 鳥かごの中で正面向いた文鳥の画像と「鳥かごの中にいる文鳥がこちらに向かって飛んできた」を入力したところ、実在しない背中の配色の文鳥が動いてくれました  
        [![334155718497628166.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fff1a4f7d-be6b-39b7-026b-70b09da6617d.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=deaca8faf90cca07f506cb40dbc16186)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fff1a4f7d-be6b-39b7-026b-70b09da6617d.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=deaca8faf90cca07f506cb40dbc16186)

gif画像へ変換した際に圧縮したため画質が悪いですが、実際はきれいな動画が生成されました。  
単純な入力で、想像以上に自然な動画が生成されました。  
プロンプトをしっかりすれば色など細かいところもコントロールできるはずです。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#kling-ai-%E5%8B%95%E7%94%BB%E7%94%9F%E6%88%90)KLING AI: 動画生成

[KLING AI](https://klingai.com/)は中国の企業「快手（Kuaishou）」の動画生成サービスです。

[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2715905d-43e6-c39a-9b92-910781db6e45.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e886be365d46532c70b70ecb3896287c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2715905d-43e6-c39a-9b92-910781db6e45.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e886be365d46532c70b70ecb3896287c)

- Text To Image
    - 「cream colored java sparrow」  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F0375fe5c-32c3-ded9-85ff-f78b2b0de9f0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=742f2126fb07d4d97d0d6e1fa2670457)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F0375fe5c-32c3-ded9-85ff-f78b2b0de9f0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=742f2126fb07d4d97d0d6e1fa2670457)
- Text to Video
    - 「flying java sparrow」と入力してみました  
        [![Standard_Mode_16x9_flying_java_sparrow.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe1648912-0642-9e2f-15d3-ec00c313e9d7.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=99450e3e4bdf4f03573a37e8fc66dc8d)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe1648912-0642-9e2f-15d3-ec00c313e9d7.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=99450e3e4bdf4f03573a37e8fc66dc8d)
        - 文鳥っぽくない鳥が羽ばたいています
- Image to Video
    - 画像のみを入力してみました  
        [![Standard_Mode_16x9_Generated_Video.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F62ccedba-f5ec-112d-32ab-4d0c39a37074.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5706a546174f076895ab9b13251d01ac)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F62ccedba-f5ec-112d-32ab-4d0c39a37074.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5706a546174f076895ab9b13251d01ac)
        - 背後に謎の動きがありますが、自然に動いています

「動画とテキスト」または「動画と音声ファイル」を入力して、人の口の動きを入力されたテキスト/音声に合うように口を動かす「Lip Sync」もありました。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#stable-audio-%E9%9F%B3%E6%A5%BD%E7%94%9F%E6%88%90)Stable Audio: 音楽生成

[Stable Audio](https://ja.stability.ai/audio)は、Stability AIの音楽生成サービスです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8c286172-50e2-4f2f-4df3-6f9dff241622.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fb61a7d1948e912bee93ca3abdb02b32)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8c286172-50e2-4f2f-4df3-6f9dff241622.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fb61a7d1948e912bee93ca3abdb02b32)  
「Japanese Rock」と入力したところ、それっぽい自然な曲が生成されました。  
Qiitaに音声を載せられないので、ご自身で試してみてください。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#luma-ai-%E5%8B%95%E7%94%BB%E7%94%9F%E6%88%90)Luma AI: 動画生成

[Luma AI](https://lumalabs.ai/)はアメリカ・サンフランシスコの企業で、動画生成、3Dモデル生成サービスを提供しています。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2fb38072-0848-e931-c60d-821dfdff9735.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=25dd00755a410074dba51602d19ed59b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F2fb38072-0848-e931-c60d-821dfdff9735.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=25dd00755a410074dba51602d19ed59b)

- [Dream Machine](https://dream-machine.lumalabs.ai/): 動画生成
    - 「Make a video of flying night heron」と入力してみると2つの動画が生成されました  
        [![watermarked_video0ff2f6db3ab1a4501b872efdbb83af6e2.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe8ab32ec-8251-c71c-22d2-8ed4316443ba.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=25888ff51850772ed6850f04f7da02da)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe8ab32ec-8251-c71c-22d2-8ed4316443ba.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=25888ff51850772ed6850f04f7da02da)  
        [![watermarked_video030a8ab77e7144420b9e7a57c4e533264.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F86ce1991-6a68-6814-9fe0-ca5fc4444a20.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0f524e62fa552bcbcf0fb76a9a232bc0)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F86ce1991-6a68-6814-9fe0-ca5fc4444a20.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0f524e62fa552bcbcf0fb76a9a232bc0)
    - 入力通り、ゴイサギ(night heron)が飛んでます
    - 数時間の待ち時間がありました
- [Genie](https://lumalabs.ai/genie?view=create): Text to 3Dモデル
    - 「java sparrow」と入力してみました  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F4cd30d2c-1a9e-a62d-0dbc-4d09af4d032a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=63a67bf703a08a07f21df2314fdc0667)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F4cd30d2c-1a9e-a62d-0dbc-4d09af4d032a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=63a67bf703a08a07f21df2314fdc0667)

画像から3Dモデルを作成できるアプリ[Luma AI: 3D Capture](https://play.google.com/store/apps/details?id=ai.lumalabs.polar&hl=ja)もあるようです。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#heygen-ai%E3%82%A2%E3%83%90%E3%82%BF%E3%83%BC%E3%83%AA%E3%83%83%E3%83%97%E3%82%B7%E3%83%B3%E3%82%AF)HeyGen: AIアバター、リップシンク

[HeyGen](https://www.heygen.com/)は、AIアバター動画の作成サービスです。

人の顔が写った画像をアップまたは生成して、口を動かすことができます。  
今回はもともとテンプレートとして存在しているアバターを選んで、日本語を話させてみました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F42cbaffc-e02b-5626-007f-55e173c72a63.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2815c805537c42e743cede8d55e9a82c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F42cbaffc-e02b-5626-007f-55e173c72a63.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2815c805537c42e743cede8d55e9a82c)  
話す内容を文字で書くと、音声合成されました。  
1枚の画像とテキストから人が話しているような動画を生成できました。  
[![Untitled Video.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe238aa39-5b82-4e67-cc30-b4053a988a6a.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=658451141dcc00de12e0430952e83610)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe238aa39-5b82-4e67-cc30-b4053a988a6a.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=658451141dcc00de12e0430952e83610)  
Qiita上に音声を載せられないので[共有リンク](https://app.heygen.com/videos/6d199465d4664da0ad800b878dd6214e)をおいておきます。

リップシンクの技術はオープンソースのものがいくつかありますが、私の環境ではどこかしらでエラーが出て、どれもうまく動かすことができませんでした。GoogleColabでも難しかったです。

- オープンソース
    - [dreamtalk](https://github.com/ali-vilab/dreamtalk)
    - [EchoMimicV2](https://github.com/antgroup/echomimic_v2)
    - [V-Express](https://github.com/tencent-ailab/V-Express)
- 一般公開されていない
    - [X-Portrait2](https://byteaigc.github.io/X-Portrait2/)
    - [vasa-1](https://www.microsoft.com/en-us/research/project/vasa-1/)

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#stable-diffusion-%E7%94%BB%E5%83%8F%E7%94%9F%E6%88%90)Stable Diffusion: 画像生成

Stable Diffusionは、テキストから画像を生成できる技術です。

オンラインでデモを試すことができます。

- [Stable Diffusion 2.1 Demo](https://huggingface.co/spaces/stabilityai/stable-diffusion)
    - [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F28a1cc9e-7c6d-3f65-d6a6-804f8d98c77c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1b8075fdc86fc9fdf2496430c18928be)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F28a1cc9e-7c6d-3f65-d6a6-804f8d98c77c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1b8075fdc86fc9fdf2496430c18928be)
        - prompt: night heron and gray heron
        - negative prompt: low quality
- [Stable Diffusion オンライン](https://stablediffusionweb.com/ja)
    - prompt: flying java sparrow  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5a304f8b-5fc0-358f-b16c-7bea87635e60.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=27f0259c29d960f8d34071bf47058f19)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5a304f8b-5fc0-358f-b16c-7bea87635e60.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=27f0259c29d960f8d34071bf47058f19)

promptには生成したい内容を英語で列挙し、negative promptには生成したくない内容を英語で列挙します。

オフラインで試すには[Stable Diffusion web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui)がオススメです。

環境構築

- `python3.10.11`: python3.10系じゃないとエラーが出ます
- `webui-user.bat`を実行すると自動的に環境構築されます
    - CPU上で実行するには`webui-user.bat`を以下のように書き換える必要があります
        
        webui-user.batのCPU向け設定
        
        ```
        @echo off
        
        set PYTHON=
        set GIT=
        set VENV_DIR=
        set COMMANDLINE_ARGS=--skip-torch-cuda-test --no-half
        
        call webui.bat
        ```
        
- モデルのダウンロード
    - [huggingface](https://huggingface.co/models?other=stable-diffusion&sort=trending)や[Civitai](https://civitai.com/)からダウンロード可能
    - DLしたモデル本体は`models/Stable-diffusion/`に配置
- 実行
    - model: [stable-diffusion-v1-5](https://huggingface.co/stable-diffusion-v1-5/stable-diffusion-v1-5/tree/main)  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F54b87d7d-c2f8-63a3-12e3-02facb745dc4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=37ce06bf2f5c2a199a343f92fab2da3a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F54b87d7d-c2f8-63a3-12e3-02facb745dc4.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=37ce06bf2f5c2a199a343f92fab2da3a)
    - model: [7th_anime_v3](https://huggingface.co/syaimu/7th_Layer/tree/main/7th_anime_v3)  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F71e537b2-da41-7378-a368-3dfe89ac9c7b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=08bedcd9236bc45bc937c84734bd093f)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F71e537b2-da41-7378-a368-3dfe89ac9c7b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=08bedcd9236bc45bc937c84734bd093f)
    - CPU環境では1枚7分くらい

ちなみに、CPUで遅いからと言って[GoogleColab](https://colab.research.google.com/?hl=ja)無料枠で実行してはいけません。利用規約に違反するようです。

- 参考: [【警告表示】無料枠のGoogle Colab規約変更？！WebUIの使用が制限されるようになった](https://codewizardry.net/2023/04/22/google-colab-limitation/)

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E7%89%B9%E5%AE%9A%E3%81%AE%E3%82%AD%E3%83%A3%E3%83%A9%E3%82%84%E3%82%B7%E3%83%BC%E3%83%B3%E3%81%AE%E7%94%9F%E6%88%90)特定のキャラやシーンの生成

[Civitai](https://civitai.com/)では、モデル本体の他に、特定のキャラクターやポーズ、シーン指定が可能なLoRAモデルもダウンロードできます。  
また、他の人が生成した画像のプロンプトを見ることができ、うまい画像生成のプロンプト勉強になります。  
ダウンロードしたLoRAを`models/Lora`に配置し、読み込んでクリックするとプロンプトに追加され使用できますが、**Civitaiには著作権を無視したようなキャラクターのLoRAや学習データが不明のLoRAなども含まれているので、ここでは利用せず**、image to imageのsketchを使った方法を紹介します。

img2imgタブのSketchから、真っ白な画像をアップロード後、ラフな図を書いて構図を指定してみました。  
[![スクリーンショット 2025-01-27 134509.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F481dd169-f774-80a2-b8b6-ad6875703879.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b529ecf1d023dba17f89a15b74ce0861)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F481dd169-f774-80a2-b8b6-ad6875703879.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b529ecf1d023dba17f89a15b74ce0861)  
このように、ラフスケッチとプロンプトから画像を生成できます。  
色を変えて指定することもできます。

他にも、さまざまな機能が提供されています。

- `img2img`: 既存の画像をもとに新しい画像を生成
    - 元画像の形状や内容を維持しつつ、指定したプロンプトに基づいて画像を変化
    - 画像のスタイル変更やイラスト風画像への変換などに使用
- `Sketch`: 手描きの線画やラフスケッチを基にした画像生成（今回の例）
    - ユーザーが描いたラフなスケッチを入力として、指定したプロンプトに応じて詳細な画像を生成
    - 線画からリアルなイラストや写真風画像を作成する場合に便利
- `Inpaint`: 画像の一部を修正・再生成する機能
    - 指定した領域だけをプロンプトに基づいて再生成
    - 画像内の不要な部分を消したり、修正したい箇所を変える場合に利用
- `Inpaint Sketch`: スケッチとInpaintを組み合わせた機能
    - 修正したい領域をマスクで指定した後、その領域にスケッチ（線画）を描いて新しい内容を生成
    - マスク領域に細かい指示を入れることで、より意図に合った画像生成が可能

LoRAを自作する方法については、次の記事が参考になります。

- [Stable DiffusionのLoRAのつくりかた](https://note.com/redrayz/n/n05e93566e562)
- [【Stable Diffusion】自分だけのLoRAの作り方～その１](https://mountainnavi.com/diffusion/2279/)

学習は流石にCPUでは無理そうです。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#fastsd-cpu-cpu%E7%92%B0%E5%A2%83%E3%81%A7%E3%82%82%E9%AB%98%E9%80%9F%E3%81%AB)FastSD CPU: CPU環境でも高速に

[FastSD CPU](https://github.com/rupeshs/fastsdcpu)はCPUでも高速に動くバージョンです。

- 環境構築: `install.bat`を実行
- 起動: `start-webui.bat`実行後、`http://127.0.0.1:7860/`へアクセス
- 生成してみる  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F94ef1953-1f66-f63a-72fe-80a288ece1b1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2b108d21743f903a266506d40e3fc75e)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F94ef1953-1f66-f63a-72fe-80a288ece1b1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2b108d21743f903a266506d40e3fc75e)
    - LCM-LoRA: `rupeshs/hypersd-sd1-5-1-step-lora`
        - モデルは`Models`タブで選択したものが自動的にダウンロードされます
    - steps: 4
    - 約20秒で生成できました

stable diffusionの方はsteps=20だったので、こちらも20に合わせると、2分程度でした。  
LoRAは、`lora_models/`に配置してUIを起動し直すと使用できます。  
外部のモデル本体は、`models/gguf/diffusion`に置くことで、選択できるようになりましたが、私の環境ではエラーが出て実行できませんでした。

- 参考: [【グラボなしで1秒画像生成】Stable Diffusionの高速版FastSD CPUで遊んでみた](https://note.com/_chibisuke/n/n23afada8420f)

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#flux1-%E7%94%BB%E5%83%8F%E7%94%9F%E6%88%90)FLUX.1: 画像生成

FLUX.1は、Stable Diffusionの研究を行っていたBlack Forest Labsによって開発された画像生成技術です。

オンラインデモがあります。

- [FLUX.1-schnell](https://huggingface.co/spaces/black-forest-labs/FLUX.1-schnell)  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fac017c61-3436-2a6c-f3ed-00f9b32d2684.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=46538868f9812b43aa509618e16030f1)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fac017c61-3436-2a6c-f3ed-00f9b32d2684.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=46538868f9812b43aa509618e16030f1)
- [FLUX.1-dev](https://huggingface.co/spaces/black-forest-labs/FLUX.1-dev)  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Faaa6f16b-8de7-b220-e563-218c07953944.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=14d440ccaf7bc7e38d506eefa141613c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Faaa6f16b-8de7-b220-e563-218c07953944.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=14d440ccaf7bc7e38d506eefa141613c)

オフラインでは[pinokio](https://pinokio.computer/)からflux-webuiをインストールすることで簡単に試せます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F523c7717-c040-ef75-8e15-34b4f89bfef1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=607b72957bb2af5b7c643695f8536f54)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F523c7717-c040-ef75-8e15-34b4f89bfef1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=607b72957bb2af5b7c643695f8536f54)  
なお、残りディスク容量が残り20GBくらいしかなかったので実行し切ることができませんでした。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#omnigen-%E7%94%BB%E5%83%8F%E7%94%9F%E6%88%90%E7%94%BB%E5%83%8F%E7%B7%A8%E9%9B%86)OmniGen: 画像生成、画像編集

[OmniGen](https://github.com/VectorSpaceLab/OmniGen)は、テキストの指示で、様々な画像系のタスク実行できる技術です。[レポジトリ](https://github.com/VectorSpaceLab/OmniGen)の説明を読むと凄さがわかります。  
[OmniGen: Unified Image Generation paper code](https://huggingface.co/spaces/Shitao/OmniGen)でお試しできます。  
文鳥の画像をアップし、`Change the bird in this image to black:<img><|image_1|></img>`と命令してみました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3c0227b2-e0d1-3d96-5896-76e629b8d341.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=22171d759c5c329104a9a94ae6999662)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3c0227b2-e0d1-3d96-5896-76e629b8d341.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=22171d759c5c329104a9a94ae6999662)  
複数の画像を混ぜたような指示も可能で、驚きでした。  
なお、複雑なタスクだとフリーのGPU上限に引っかかってErrorになります。

オフラインでは[pinokio](https://pinokio.computer/)からomnigenをインストールすることで試せますが、こちらもモデルが重いので容量やメモリに注意。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F1b44f744-7ae7-3c2a-3b58-8cfebb2ba2c7.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c7334e468ef6fcc4c24ebca2e7f6dc62)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F1b44f744-7ae7-3c2a-3b58-8cfebb2ba2c7.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c7334e468ef6fcc4c24ebca2e7f6dc62)

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E7%94%BB%E5%83%8F%E6%8A%80%E8%A1%93)画像技術

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#florence-2-%E7%94%BB%E5%83%8F%E3%81%A8%E8%A8%80%E8%AA%9E%E3%82%92%E3%81%A4%E3%81%AA%E3%81%90%E3%83%A2%E3%83%87%E3%83%AB)Florence-2: 画像と言語をつなぐモデル

Florence-2は、Microsoftが開発したVLM（Vision-Language Model）です。  
画像キャプション生成、物体検出、セグメンテーション、文字認識、ビジュアルグラウンディング（画像へのテキストクエリの応答）などができます。

[Florence-2 Demo](https://huggingface.co/spaces/gokaygokay/Florence-2)でお試しできます。

- キャプション生成  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fdaaa296c-8656-bd36-a250-ca9d9013663e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=240849cd36d3a8a68f443714c3a8ab47)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fdaaa296c-8656-bd36-a250-ca9d9013663e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=240849cd36d3a8a68f443714c3a8ab47)  
    `{'<CAPTION>': 'A small white bird sitting on top of a wooden perch.'}`
- 物体検出  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Faee5115c-ca45-cba3-5fe1-d1b1639a266c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=efb743dcbf5af53bc40526e0065bb69a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Faee5115c-ca45-cba3-5fe1-d1b1639a266c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=efb743dcbf5af53bc40526e0065bb69a)
- セグメンテーション  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3bfa2a3c-cac0-6231-0aa8-4e3d32cbb423.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1a61f34c2e5b91078fc66b43a0255a97)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3bfa2a3c-cac0-6231-0aa8-4e3d32cbb423.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1a61f34c2e5b91078fc66b43a0255a97)
- クエリに基づいたセグメンテーション  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F973d8f46-8b3a-17a7-8852-699327fe6de0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ca7a1bd6b5a35211180d66be19af6ea8)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F973d8f46-8b3a-17a7-8852-699327fe6de0.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ca7a1bd6b5a35211180d66be19af6ea8)
    - `The bird next to the big duck`（大きいカモの隣にいる鳥）という少し意地悪なクエリを投げてみましたが、ちゃんと子どもの方のカモをセグメンテーションしてくれました

その他いろいろありました。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#face-fusion-%E9%A1%94%E3%81%AE%E5%85%A5%E3%82%8C%E6%9B%BF%E3%81%88)Face Fusion: 顔の入れ替え

[Face Fusion](https://github.com/facefusion/facefusion)は、顔を入れ替えできる技術です。  
[pinokio](https://pinokio.computer/)でいれることができました。

実在する人の顔をSwapするのは気が引けるので、Stable Diffusionで生成した2人を交換してみました。

Stable Diffusionの設定

[![スクリーンショット 2025-01-25 084616.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8fc90db9-8d8a-5d57-dbd9-7bf7d9603691.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a2b9905de1c13bf079b0d8f53126f926)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8fc90db9-8d8a-5d57-dbd9-7bf7d9603691.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a2b9905de1c13bf079b0d8f53126f926)  
Targetの男性の顔が入れ替わっていることがわかります。  
髪型や表情はTarget側の男性のままです。  
なお、Face Editorを使うと顔の表情まで変えられました。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#yolo-%E7%89%A9%E4%BD%93%E6%A4%9C%E5%87%BA)YOLO: 物体検出

[YOLO](https://docs.ultralytics.com/ja/models/yolo11/)（You Only Look Once）は、物体検出のアルゴリズムです。

- `pip install ultralytics`

yolo

```
from ultralytics import YOLO

# モデル読み込み(自動でDLされる): https://docs.ultralytics.com/ja/models/yolo11/
model = YOLO("yolo11n.pt")

results = model("java_sparrow.JPG")
results[0].save(filename="yolo_result.jpg")
```

[![yolo_result.jpg](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3a4e419a-1bc8-0a94-7bf8-b0eeed972c5a.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c6f3ddb901d35a3ac5da301f18b83c72)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3a4e419a-1bc8-0a94-7bf8-b0eeed972c5a.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c6f3ddb901d35a3ac5da301f18b83c72)

鳥を認識してくれました。オブジェクトが複数ある場合はすべて検知されます。  
デフォルトで検出できるラベルは[ここ](https://github.com/ultralytics/ultralytics/blob/main/ultralytics/cfg/datasets/coco.yaml)に記載されています。

顔検知に特化したバージョンは、[yolo-face](https://github.com/akanametov/yolo-face)としてforkレポジトリに存在していました。  
README.mdに記載されているモデルをダウンロードしてソースコードと同じ階層に置くことで利用できます。

yolo-face

```
model = YOLO("yolov11n-face.pt")
```

独自のラベルを認識したい場合は、ファインチューニングをする必要があります。  
YOLOのファインチューニングについては、 これらの記事が参考になります。

- [YOLOv8のファインチューニング](https://qiita.com/hirekatsu0523/items/f2f0e1a0f8a9ea92d913)
- [YOLOをファインチューニングしてマイクロピペットを認識させる](https://note.com/kan_hatakeyama/n/n8a9ce710f103)

YOLOはバージョン/公開レポジトリによってライセンスが異なるため、しっかり確認しましょう。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#sam2-%E3%82%BB%E3%82%B0%E3%83%A1%E3%83%B3%E3%83%86%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3)SAM2: セグメンテーション

[SAM2](https://github.com/facebookresearch/sam2)（Segment Anything Model 2）は、Metaが開発した何でもセグメンテーションできてしまうモデルです。  
[ultralytics](https://docs.ultralytics.com/models/sam-2/)を使うと簡単にお試しできるようなので、ultralyticsを使ってみます。

- `pip install ultralytics`

SAM2

```
import cv2
from ultralytics import SAM

# モデル読み込み(自動でDLされる): https://docs.ultralytics.com/models/sam-2/
model = SAM("sam2.1_b.pt")
input_image = "java_sparrow.JPG"

# 矩形描画
cv2.imwrite("show_box.jpg", cv2.rectangle(cv2.imread(input_image), (1900, 800), (3400, 3500), color=(0, 255, 0), thickness=10))

# 矩形指定でセグメンテーション
box_results = model(input_image, bboxes=[1900, 800, 3400, 3500])
box_results[0].save("box_result.jpg")

# 点描画
cv2.imwrite("show_point.jpg", cv2.circle(cv2.imread(input_image), (2700, 2000), radius=25, color=(0, 255, 0), thickness=-1, lineType=cv2.LINE_8, shift=0))

# 点指定でセグメンテーション
point_results = model(input_image, points=[2700, 2000], labels=[1])
point_results[0].save("point_result.jpg")
```

矩形指定  
[![show_box.jpg](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc91192c1-64f2-0c87-60e3-7bd1994d0c2f.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a22d1919aab4eefe084126f0e3cd8798)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc91192c1-64f2-0c87-60e3-7bd1994d0c2f.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a22d1919aab4eefe084126f0e3cd8798)  
矩形指定によるセグメンテーション結果  
[![box_result.jpg](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F568f89bb-cbdb-2d92-2021-4892fbe31172.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d91b7120c8bafe624ffe37940d768c0c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F568f89bb-cbdb-2d92-2021-4892fbe31172.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d91b7120c8bafe624ffe37940d768c0c)

点指定  
[![show_point.jpg](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8e6a66bd-e217-ec2c-ac83-dda586cc5ea0.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7f7605654349424a42d71dd6b7fb8baa)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8e6a66bd-e217-ec2c-ac83-dda586cc5ea0.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7f7605654349424a42d71dd6b7fb8baa)  
点指定によるセグメンテーション結果  
[![point_result.jpg](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F33918694-0d71-d83b-074d-d12bf1326f7f.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5161aaf57d6eb09a37c87a5b27d81692)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F33918694-0d71-d83b-074d-d12bf1326f7f.jpeg?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5161aaf57d6eb09a37c87a5b27d81692)

鳥の足までセグメンテーションできているのすごい。

- 参考: [SAM2をUltralyticsで試してみた](https://note.com/thomasmemo/n/n843abd010d4e)

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E9%9F%B3%E5%A3%B0%E7%B3%BB)音声系

音声系の技術・ツールを紹介しますが、音声載せられないので画像でお楽しみください。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#whisper-%E6%9B%B8%E3%81%8D%E8%B5%B7%E3%81%93%E3%81%97speech-to-text)whisper: 書き起こし(Speech To Text)

[whisper](https://github.com/openai/whisper)は、OpenAIの多言語対応した音声認識モデルです。  
pipでライブラリをいれると、ローカルで実行できます。

- `pip install openai-whisper`

whisperによる書き起こし

```
from datetime import timedelta
import whisper

# Whisperモデルをロード (tiny, base, small, medium, large, turbo)
model = whisper.load_model("turbo")
# 音声ファイルの文字起こし(ここの処理が重い)
result = model.transcribe("input_file.mp3")

# 結果を表示
for segment in result["segments"]:
    start = segment["start"]
    end = segment["end"]
    text = segment["text"]
    sentence = f"[{timedelta(seconds=int(start))} - {timedelta(seconds=int(end))}] {text}"
    print(sentence)
```

[![スクリーンショット 2025-01-19 234114.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F32de8f8c-c7f6-a763-1317-e3026a9624ba.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e880d0cd66e2a4ccb216416547bcd92a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F32de8f8c-c7f6-a763-1317-e3026a9624ba.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e880d0cd66e2a4ccb216416547bcd92a)

whisperでは、入力ファイルすべてを処理し終えてから結果がでます。  
ファイルが大きいと時間がかかってしまいます。  
faster-whisperを使うと、一定のセグメントごとに書き起こし結果が出力されます。

- `pip install faster-whisper`

faster-whisperによる書き起こし

```
from datetime import timedelta
from faster_whisper import WhisperModel

# Whisperモデルをロード (e.g., tiny, base, small, medium, large, large-v2, turbo)
model = WhisperModel("turbo")
# 音声ファイルの文字起こし (generatorが返ってくるため、ここの処理自体は軽い)
segments, info = model.transcribe("input_file.mp3")

# 結果を表示
for segment in segments:
    start = segment.start
    end = segment.end
    text = segment.text
    sentence = f"[{timedelta(seconds=int(start))} - {timedelta(seconds=int(end))}] {text}"
    print(sentence)
```

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#voicevox-%E9%9F%B3%E5%A3%B0%E5%90%88%E6%88%90text-to-speech)VoiceVox: 音声合成（Text To Speech）

[VoiceVox](https://voicevox.hiroshiba.jp/)は、オープンソースの音声合成ソフトです。  
インストールするとGUIで、誰でも簡単に様々なキャラクターでテキストを読むことができます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F85e98a78-626a-1fb1-bda0-6948f08cb465.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3c85547f1067e89f41a82fc3ebeb22d8)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F85e98a78-626a-1fb1-bda0-6948f08cb465.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3c85547f1067e89f41a82fc3ebeb22d8)

- YouTubeの合成音声でよく聞くずんだもんさん

アクセントやイントネーションの調整もできます。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#coeiroink-%E9%9F%B3%E5%A3%B0%E5%90%88%E6%88%90text-to-speech)COEIROINK: 音声合成（Text To Speech）

[COEIROINK](https://coeiroink.com/)は、無料の音声合成ソフトです。  
一般ユーザーが公開した声のモデルを[利用](https://coeiroink.com/mycoeiroink/list)できるのが特徴です。  
声のモデルの[作り方](https://coeiroink.com/mycoeiroink/making)も記載されています。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F554f3d1e-80a3-77d0-7fd8-377f7bf7a4fc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=33214f6ebfb31d6cb5b3fe87de6c86b3)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F554f3d1e-80a3-77d0-7fd8-377f7bf7a4fc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=33214f6ebfb31d6cb5b3fe87de6c86b3)  
アクセントやイントネーションの調整もできます。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#style-bert-vits2-%E9%9F%B3%E5%A3%B0%E5%90%88%E6%88%90text-to-speech)Style-Bert-VITS2: 音声合成（Text To Speech）

[Style-Bert-VITS2](https://github.com/litagin02/Style-Bert-VITS2)は、感情豊かな音声合成ができるオープンソースモデル。  
zipをDLして、batファイルを実行するだけで環境が整うので、簡単に利用することができます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F93482f8d-687b-4577-0efe-691599cdfd1e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e59bb0a4a6a5215ee3ce52f240675ab0)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F93482f8d-687b-4577-0efe-691599cdfd1e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e59bb0a4a6a5215ee3ce52f240675ab0)

- `junv-**-jp`系のモデルは感情が乗ったように聞こえました
- モデルによっては平坦に聞こえました

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E3%81%AB%E3%81%98%E3%83%9C%E3%82%A4%E3%82%B9-%E9%9F%B3%E5%A3%B0%E5%90%88%E6%88%90text-to-speech)にじボイス: 音声合成（Text To Speech）

[にじボイス](https://nijivoice.com/)は、感情豊かな音声合成サービスです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F1f473175-0a8d-e4ad-4937-18159e2795ae.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=79b5a70fbeb6aa83372dd31e3c560bbf)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F1f473175-0a8d-e4ad-4937-18159e2795ae.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=79b5a70fbeb6aa83372dd31e3c560bbf)  
使ってみるとわかりますが、テキストを入れただけでかなりクオリティの高い音声が得られました。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#rvc-%E9%9F%B3%E5%A3%B0%E5%A4%89%E6%8F%9B)RVC: 音声変換

RVC（Retrieval-based-Voice-Conversion）は、別の人の声に変換できる技術です。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#retrieval-based-voice-conversion-webui)Retrieval-based-Voice-Conversion-WebUI

[Retrieval-based-Voice-Conversion-WebUI](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI/blob/main/docs/jp/README.ja.md)は、音声変換を気軽に試すことができるWebUIです。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3b146d12-7cd0-0a62-38e5-9393d70c2b8b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=48a53cb1304e096162537d57d32ce3b7)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F3b146d12-7cd0-0a62-38e5-9393d70c2b8b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=48a53cb1304e096162537d57d32ce3b7)

環境構築から起動

```
git clone git@github.com:RVC-Project/Retrieval-based-Voice-Conversion-WebUI.git
cd Retrieval-based-Voice-Conversion-WebUI
poetry install
poetry run python tools/download_models.py
poetry run python infer-web.py
```

1. RVC学習済みモデル(`*.pth`)をDLして、`assets/weights/`に配置する
    - 学習済みモデルは[BOOTH](https://booth.pm/ja/search/RVC?max_price=0)などで公開されています
    - 「音源リストとインデックスパスの更新」を押して配置したモデルを選ぶ
2. 「処理対象音声ファイルのパスを入力してください」に変換したい音声ファイルを設定
3. 「変換」を押す  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fd2546876-bcb6-402c-75b9-6cecb47da453.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2f76fd10839d6bea7dae1effaf359d6b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fd2546876-bcb6-402c-75b9-6cecb47da453.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2f76fd10839d6bea7dae1effaf359d6b)
    - しばらくすると変換結果が出力されます

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#vcclient-%E3%83%AA%E3%82%A2%E3%83%AB%E3%82%BF%E3%82%A4%E3%83%A0%E5%A4%89%E6%8F%9B)VCClient: リアルタイム変換

[VCClient](https://github.com/w-okada/voice-changer)は、RVCでリアルタイム音声変換ができるソフトウェアです。

1. [ここ](https://huggingface.co/wok000/vcclient000/tree/main)から`vcclient_win_std_2.0.61-alpha.zip`をダウンロード(CPUの場合)
2. `start_https.bat`または`start_http.bat`を実行  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fd9d3b255-0288-e094-b87f-1796c57ab44f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7fbb36e781a6d870653fbdc4bd930f71)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fd9d3b255-0288-e094-b87f-1796c57ab44f.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=7fbb36e781a6d870653fbdc4bd930f71)
    - デフォルトでモデルが入っています
    - 「編集」から任意のRVCのモデルを追加できます
3. 「入力」「出力」デバイスを設定したら、「スタート」を押すとリアルタイムで変換した声を聞けます

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#web%E3%82%A2%E3%83%97%E3%83%AA%E4%BD%9C%E6%88%90)Webアプリ作成

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#v0)v0

[v0](https://v0.dev/)は、Vercel社が提供しているWebサイトやアプリのUIを作れるサービスです。

「2人で五目並べをするwebアプリを作って」と入力したら、ソースコードと実際の動作プレビューが実行されました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F16c41ae2-b2dc-f739-71ca-ec5cefb4c973.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4a84363b82343fbc9a129084cf7cb1c1)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F16c41ae2-b2dc-f739-71ca-ec5cefb4c973.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4a84363b82343fbc9a129084cf7cb1c1)  
なぜか縦長の盤面だったので、「盤面を縦長ではなく、30x30のマスにしてください。」と指示してみます。  
依然として縦長だったので再び「縦に並んでしまっています。正方形状に配置してください。」と指示してみました。  
これでも直らなかったので「30x30のテーブルとしてマスを並べてください」と指示してみました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F7e936693-3c33-be10-6ed9-acdf6917e7f3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5569a52789169edd21c70502a646543b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F7e936693-3c33-be10-6ed9-acdf6917e7f3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5569a52789169edd21c70502a646543b)  
なんと、これでもうWebアプリができてしまいました。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#boltnew)bolt.new

[bolt.new](https://bolt.new/)は、StackBlitz社が開発したアプリ開発サービス。  
「五目並べを作ってください」と入力してみました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F682166f3-8c4a-3d71-2f1c-e248f72464cd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8de1d1cb9a8b974e6d6e6815209d599d)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F682166f3-8c4a-3d71-2f1c-e248f72464cd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=8de1d1cb9a8b974e6d6e6815209d599d)  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa1c7b5ec-3515-0959-9cc8-43e073f6837e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e15b84c6e904e03a1809f3da7bf0559a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fa1c7b5ec-3515-0959-9cc8-43e073f6837e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e15b84c6e904e03a1809f3da7bf0559a)  
一発でできました...恐ろしい...

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E3%82%B3%E3%83%BC%E3%83%89%E7%94%9F%E6%88%90%E3%82%A8%E3%83%87%E3%82%A3%E3%82%BF)コード生成・エディタ

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#cursor)cursor

[cursor](https://www.cursor.com/)は、VS Codeをベースに作られたAI機能付きエディタです。  
ダウンロードして、インストールが必要です。

[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F76480d6b-3f0f-c36e-612e-63a3e3796b2d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b4f5be8da520cd1a94b2315b8fa067d0)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F76480d6b-3f0f-c36e-612e-63a3e3796b2d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b4f5be8da520cd1a94b2315b8fa067d0)

- Ctrl+Kでプロンプトの指示から生成したコードを、現在の位置に挿入できます  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8bd8a198-0c1b-7c1c-c9b6-18b8bdd6aafc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ea8bc7fe91b714a61ff0a9ed541c4ae6)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F8bd8a198-0c1b-7c1c-c9b6-18b8bdd6aafc.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=ea8bc7fe91b714a61ff0a9ed541c4ae6)  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F6a129b88-1b2c-0f91-3286-caa644af3820.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=269e6d97d52468ce0360a136900da8b2)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F6a129b88-1b2c-0f91-3286-caa644af3820.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=269e6d97d52468ce0360a136900da8b2)
- AI入力補完も備わっています  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fdc2ca95e-8093-9b26-7369-5b53319aedce.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=29545976f11852052b468662c44bfb5d)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fdc2ca95e-8093-9b26-7369-5b53319aedce.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=29545976f11852052b468662c44bfb5d)  
    Tabキーで採用  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fee11736f-7fd7-b421-33c4-70be5ecfd438.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=21b33f9167ed7c6f517a402a8472c562)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fee11736f-7fd7-b421-33c4-70be5ecfd438.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=21b33f9167ed7c6f517a402a8472c562)
- Ctrl+Lでチャットできます  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff97a738c-93cc-9b84-81b5-0d832520446e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a996ad9f16983b17c4e91a1e0d7cddeb)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Ff97a738c-93cc-9b84-81b5-0d832520446e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a996ad9f16983b17c4e91a1e0d7cddeb)
    - コードブロックのApplyを押すと保存できました
    - 「Add Context」からソースコードを選択することで事前知識を与えることができ、web上のチャットLLMにわざわざソースコードをコピペする必要がありません

composer agent  
Ctrl+IでComposerを起動できます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F4b0c7691-39cc-2e21-ef3c-dc2028bcc448.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=87e85d6bf5c22bedc7ae54812418feaf)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F4b0c7691-39cc-2e21-ef3c-dc2028bcc448.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=87e85d6bf5c22bedc7ae54812418feaf)  
右下の設定をnormalからagentにして命令を出すと、勝手にファイルが作成されました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fed716a07-0a6f-4144-472a-a932dc14d339.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=283bbc8926a3056c716d2dca1c0000a5)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fed716a07-0a6f-4144-472a-a932dc14d339.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=283bbc8926a3056c716d2dca1c0000a5)

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#cline)cline

VSコード拡張として導入可能です。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F571b4a36-c7af-4bbd-788f-1ca1127e1e0a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2f1fa6461b18531a09c59fd969a36ead)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F571b4a36-c7af-4bbd-788f-1ca1127e1e0a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=2f1fa6461b18531a09c59fd969a36ead)  
インストールするとロボットマークが出てきました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc8b5c716-028f-6fef-ebdf-c27896531393.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4e79e31263483b4fc454defb9e1679b4)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc8b5c716-028f-6fef-ebdf-c27896531393.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4e79e31263483b4fc454defb9e1679b4)  
使用するLLMを指定します。今回はGoogle Geminiを選択してお試ししてみました。  
(Gemini APIの無料お試しについてはこの記事の後半で登場します。cline上で使うと一瞬でクオータに引っかかるため、本当にお試しです。)  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fad19f718-042d-14d4-2b72-d10a45e4c668.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fe2f5263961e658a7a62e87967e2fc88)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fad19f718-042d-14d4-2b72-d10a45e4c668.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fe2f5263961e658a7a62e87967e2fc88)  
かなり簡単な命令ですが、素数判定のプログラムを書いてもらいました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fbfbbfaad-190c-d19a-b40f-1cabe3ef52b2.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d4d840e0037ea92776bf886ee2976b29)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fbfbbfaad-190c-d19a-b40f-1cabe3ef52b2.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=d4d840e0037ea92776bf886ee2976b29)  
これだと単純なチャットLLMでも生成できるタスクなので、実際にはもっと複雑なタスクにおいて能力を発揮することでしょう。  
youtubeなどでclineと検索すると、コード生成からエラー解決まで、ステップを踏んでAIが開発していく様子が見られます。（人間がするのは確認と実行の許可だけ）

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#replit)Replit

[Replit](https://replit.com/)は、オンライン上で動くエディタ開発環境です。

レポジトリを作ってみます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F24a9aee6-8bde-585c-2b02-dd60397ff30c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e0a2ca40b608557a08b3fdda503bd12c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F24a9aee6-8bde-585c-2b02-dd60397ff30c.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e0a2ca40b608557a08b3fdda503bd12c)  
無料だとpublicしかありませんでした。

- Assistantの利用
    - チャットでAIとやりとりできます  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F4ea16f1a-750a-7132-f326-29974fcaa130.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=849fe4cbcc8dd8132960470ddaaa7f96)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F4ea16f1a-750a-7132-f326-29974fcaa130.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=849fe4cbcc8dd8132960470ddaaa7f96)
        - 簡単なコード生成ができました
        - （v0やboltにあったような）アプリ丸ごと作るような要求は弾かれました
- AIコード補完
    - plusとminusの関数がある状態で、続けて下に関数を作ろうとしたら、multiplyを補完してくれました[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F1b4f4c6c-8e95-65af-bf9d-f1d731dc7808.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=87cce0c71830151e587a1ee5fb07262e)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F1b4f4c6c-8e95-65af-bf9d-f1d731dc7808.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=87cce0c71830151e587a1ee5fb07262e)
- Run
    - 画面上部のRunを押すとpythonを実行できました

無料版ではReplit Agentは使えませんでした。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F7eac796a-7ffb-d536-948c-cc6eb9905a6d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=db0e19e244e11519bf6a590b006f73fc)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F7eac796a-7ffb-d536-948c-cc6eb9905a6d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=db0e19e244e11519bf6a590b006f73fc)  
これが使えるとv0やboltのように、プロンプトからコード生成をして丸ごとアプリができると思います。

# [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#llm%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%9F%E9%96%8B%E7%99%BA%E7%B7%A8)LLMを使った開発編

前半パートでいろんなサービスやモデル、技術を紹介してきました。  
ここからは、LLMに焦点を当てて、実際にプロダクト開発をする際に必要になりそうな知識を学びます。

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%ABllm)ローカルLLM

プロダクト開発では、扱うデータや価格の面から、ローカルで動くLLMを使うことがあります。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#ollama)Ollama

[Ollama](https://ollama.com/)はローカルでLLMを実行できるオープンソースのツールです。

- インストールするだけで簡単にローカルでLLMが使える  
    [![スクリーンショット 2025-01-19 083306.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5c011d26-c0bf-96ae-1b2f-30f9067b4d85.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a0e5317644310cf800945cff2fda5719)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5c011d26-c0bf-96ae-1b2f-30f9067b4d85.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a0e5317644310cf800945cff2fda5719)
- `ollama run <model name>`でモデルを実行できます
    - [モデル一覧](https://ollama.com/search)
    - 初回実行ではモデルのダウンロードが行われます  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F62ccb0d6-bd88-74ae-a428-6295752d3500.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c16a9f20122bde7bdca1b6dd99775b1a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F62ccb0d6-bd88-74ae-a428-6295752d3500.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c16a9f20122bde7bdca1b6dd99775b1a)
    - `/?`でhelp  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc11dada5-afaf-08e5-1071-08abe8845fb8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a0e93e5cfdd742abf7386923cd114c72)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc11dada5-afaf-08e5-1071-08abe8845fb8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a0e93e5cfdd742abf7386923cd114c72)
- [apiとして](https://github.com/ollama/ollama/blob/main/docs/api.md)も呼べます
    - generate形式
        
        ```
        curl -X POST http://localhost:11434/api/generate -d '{
            "model": "gemma:2b",
            "prompt":"キジバトとドバトの違いは？"
        }'
        ```
        
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F79f17fc7-a3e9-77e5-2c1b-0e50ec9d7f85.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=267f455e1f35758640991370cbc20df1)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F79f17fc7-a3e9-77e5-2c1b-0e50ec9d7f85.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=267f455e1f35758640991370cbc20df1)
        - デフォルトではstreamで返ってきますが、`"stream": false`を指定すると、すべて生成し終わった後に生成文が返ってきます
    - chat形式
        
        ```
        curl -X POST http://localhost:11434/api/chat -d '{
            "model": "gemma:2b",
            "messages": [
                { "role": "user", "content": "キジバトとドバトの違いは？" }
            ]
        }'
        ```
        
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F219fefef-bfcc-1e55-30bf-963715404132.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=00aaabeb470186d06634b22c9cc72b43)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F219fefef-bfcc-1e55-30bf-963715404132.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=00aaabeb470186d06634b22c9cc72b43)
- langchainから呼ぶ
    
    - `pip install langchain langchain_ollama`
    
    langchainからollamaを呼ぶ
    
    ```
    from langchain_ollama import ChatOllama
    
    model = ChatOllama(model="gemma:2b")
    result = model.invoke("LLMについて教えてください")
    print(result.content)
    ```
    
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fcf39b4df-b9db-a39b-13e7-fb96fdaefd63.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0a975d1601347ad9fbdfd57252c73907)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fcf39b4df-b9db-a39b-13e7-fb96fdaefd63.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=0a975d1601347ad9fbdfd57252c73907)
    - langchainについては後述

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#transformers)transformers

transformersライブラリを使って、ローカルLLMを推論、ファインチューニングしてみよう。  
今回はGoogleのオープンソースLLMのGemmaを使ってみます。  
transformersライブラリでGemmaのモデルを使うには、huggingfaceの登録が必要です。

1. [huggingface](https://huggingface.co/)登録
2. Email認証後、右上メニューからAccess Tokens、「Create new token」からReadで作成
3. [Gemma](https://huggingface.co/google/gemma-2-2b-it)モデルへのアクセス許可を申請して、許諾を貰う
    - [許諾の状況確認](https://huggingface.co/settings/gated-repos)

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E6%8E%A8%E8%AB%96)推論

軽めのモデル"google/gemma-2-2b-it"（約5GB）で試します。

- 環境構築: `pip install accelerate torch transformers bitsandbytes`

gemma2の推論

```
from transformers import AutoModelForCausalLM, AutoTokenizer

model_name = "google/gemma-2-2b-it"
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    device_map="auto",  # 自動的にGPU/CPUに割り当て
    # load_in_8bit=True, # 量子化オプション
    # load_in_4bit=True,
    # token=os.getenv("HUGGING_FACE_TOKEN")
)
tokenizer = AutoTokenizer.from_pretrained(model_name)

input_text = """
<start_of_turn>user
カワセミについて教えて<end_of_turn>
<start_of_turn>model
"""
input_ids = tokenizer(input_text, return_tensors="pt")
outputs = model.generate(**input_ids, max_new_tokens=50)

print(tokenizer.decode(outputs[0]))
```

生成された文章

```
<bos>
<start_of_turn>user
カワセミについて教えて<end_of_turn>
<start_of_turn>model
## カワセミについて

カワセミは、日本の象徴的な鳥の一つで、美しい色彩と優雅な飛び方を特徴として知られています。

**特徴**

* **体長:** 20-30cm
* **
```

max_new_tokensを50にしたので途中で途切れましたが、無事生成されました。  
プロンプトの形式は[gemmaのフォーマット](https://ai.google.dev/gemma/docs/formatting?hl=ja)に従っています。

参考にしたこちらの記事、

- [Googleによる生成AIモデル「Gemma」をMacBook(M2)で動かしてみた  
    ](https://qiita.com/nabata/items/01cf907c289db3fdb47d)

では、huggingfaceのトークンを渡していましたが、筆者のWindows環境ではなくても動きました。（認証情報がキャッシュとして残っていたのかな？）  
必要があれば記載しましょう。

モデル読み込み時に`load_in_8bit=True`や`load_in_4bit=True`を設定すると、[量子化](https://huggingface.co/docs/transformers/ja/main_classes/quantization)され、計算量とメモリ使用量を抑えることができます。  
筆者のCPU環境だとエラーがでました。GPU環境でないと利用できないようです。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#vllm-%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%ABllm%E6%8E%A8%E8%AB%96%E9%AB%98%E9%80%9F%E5%8C%96)vLLM: ローカルLLM推論高速化

[vLLM](https://docs.vllm.ai/en/latest/)の推論を高速化するためのオープンソースライブラリです。

ローカルPCとGoogleColabのCPUではエラーとなりましたが、GoogleColabのGPU環境では動作確認できました。

環境構築

```
!pip install vllm
```

huggingfaceの認証

```
from huggingface_hub import notebook_login
notebook_login()
```

[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5f7b64c1-a179-39f0-95b8-8218adcf99b8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=34e9bd13ce767abe0cf6576c10930988)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5f7b64c1-a179-39f0-95b8-8218adcf99b8.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=34e9bd13ce767abe0cf6576c10930988)

- hugginfaceの[アクセストークン](https://huggingface.co/settings/tokens)を入力

モデル読み込み

```
from vllm import LLM, SamplingParams
model_name = "google/gemma-2-2b-it"
llm = LLM(model=model_name, dtype="float16")
```

推論

```
prompts = ["LLMとは、"]
sampling_params = SamplingParams(temperature=0.8, top_p=0.95)
outputs = llm.generate(prompts, sampling_params)

print("\n", outputs[0].prompt, "\n", outputs[0].outputs[0].text)
```

> Processed prompts: 100%|██████████| 1/1 [00:00<00:00, 2.01it/s, est. speed input: >10.07 toks/s, output: 32.23 toks/s]  
> LLMとは、  
> 大規模言語モデル (Large Language Model) の略語です。
> 
> LL

無事出力されました。  
セルをモデル読み込み部分と推論部分で分けている理由としては、モデル読み込み部分のセルを再実行するとOut of Memoryになるためです。  
モデルは一度のみ定義しておけば、プロンプトを変えて何度も推論を試すことができました。

`%%time`コマンドで推論部分のみを時間計測したところ、2倍くらい速くなりました。

- transformers: `Wall time: 1.19 s`
- vllm: `Wall time: 509 ms`

比較に使用したtransformersのコード

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#fine-tuning)Fine Tuning

Fine Tuningとは、学習済モデルに追加の学習をして、特定のタスクやデータに適応させるためのプロセスです。

LLMがユーザーの指示（インストラクション）に従うように学習するインストラクションチューニングも、ファインチューニングの一種で、huggingface上では`モデル名-it`や`モデル名-instuct`などと接尾辞がついています。

Google Cloudの[Model Garden](https://cloud.google.com/model-garden?hl=ja)には、ファインチューニング可能なモデルとしてgemma2やllama3.3などがあり、notebookから動かすことができるようです（課金設定が必要）。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#loralow-rank-adaptation)LoRA（Low-Rank Adaptation）

LoRAは、モデルのパラメータを直接更新せず、モデル内の特定の層に対して低ランクの適応（調整）を追加することで、効率的にタスクに特化した学習を行う手法です。

- 環境構築: `pip install datasets peft trl`

ローカルLLMのファインチューニング

```
from transformers import AutoModelForCausalLM, AutoTokenizer
from datasets import load_dataset
from peft import get_peft_model, LoraConfig, TaskType
from transformers import TrainingArguments, Trainer
from trl import DataCollatorForCompletionOnlyLM

model_name = "google/gemma-2-2b-it"
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    device_map="auto",  # 自動的にGPU/CPUに割り当て
)
tokenizer = AutoTokenizer.from_pretrained(
    model_name,
)

dataset = load_dataset("csv", data_files={
    "train": "./ft_sample.csv", 
    "validation": "./ft_sample.csv" # 実際はtrainと別のものを指定してください
})
def formatting_prompts_func(row):
    text=f"""
<start_of_turn>user
あいさつから時間帯を予測してください。
input: {row["input"]}<end_of_turn>
<start_of_turn>model
output: {row["output"]}
"""
    return tokenizer(text, add_special_tokens=False, truncation=False)

tokenized_dataset = dataset.map(formatting_prompts_func)

response_template_ids = tokenizer.encode("output:", add_special_tokens=False, truncation=False)
collator = DataCollatorForCompletionOnlyLM(response_template_ids, tokenizer=tokenizer)

# LoRA 設定
peft_config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,  # 生成タスク
    r=16,  # ランク
    lora_alpha=32,  # LoRAのスケーリング係数
    lora_dropout=0.05,  # ドロップアウト率
)

# モデルにLoRAを適用
model = get_peft_model(model, peft_config)

# トレーニング設定
training_args = TrainingArguments(
    output_dir="./results",  # モデルの保存先
    eval_strategy="epoch",  # 評価頻度
    learning_rate=2e-4,  # 学習率
    per_device_train_batch_size=4,  # バッチサイズ
    num_train_epochs=3,  # エポック数
    weight_decay=0.01,  # L2正則化
    save_total_limit=2,  # 保存するチェックポイントの数
    fp16=True,  # 半精度訓練
    logging_dir="./logs",  # ログの保存先
    logging_steps=50,  # ログの記録頻度
)

# Trainer を使ったトレーニング
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_dataset["train"],
    eval_dataset=tokenized_dataset["validation"],
    data_collator=collator,
)
trainer.train()

# トレーニング後の保存
model.save_pretrained("./fine_tuned_gemma")
tokenizer.save_pretrained("./fine_tuned_gemma")

# 推論テスト
from transformers import pipeline
pipe = pipeline("text-generation", model="./fine_tuned_gemma", tokenizer=tokenizer)
output = pipe("""
<start_of_turn>user
あいさつから時間帯を予測してください。
input: こんにちは<end_of_turn>
<start_of_turn>model
output:
""")
print(output)
```

挨拶から時間帯を当てる謎のダミータスクです。

ft_sample.csv

```
input,output
こんにちは,昼
おはようございます,朝
こんばんは,夜
```

学習パラメータは、速く終わるようにエポック数を小さくして適当に設定しているので、実際のタスクに応じてチューニングしましょう。

ファインチューニングを実行すると、次のようなエラーに遭遇することがあります。

> This instance will be ignored in loss calculation. Note, if this happens often, consider increasing the `max_seq_length`.

このエラーは、`response_template_ids`がサンプルに登場しない場合に出ます。（この例では"output:"が入力プロンプトに存在しないと判定される）  
この原因は、前後の文字によってトークン化のされ方が異なる場合があるためです:

トークン化のチェック

```
print(tokenizer.encode("output: 朝", add_special_tokens=False, truncation=False)) 
# -> [4328, 235292, 61729]
print(tokenizer.encode("output: ", add_special_tokens=False, truncation=False)) 
# -> [4328, 235292, 235248]
print(tokenizer.encode("output:", add_special_tokens=False, truncation=False)) 
# -> [4328, 235292]
```

参考

- atmaCup#17の[1st place solution](https://www.guruguru.science/competitions/24/discussions/21027ff1-2074-4e21-a249-b2d4170bd516/)
    - 機械学習コンペでLLMをFTする
- [Supervised Fine-tuning Trainer](https://huggingface.co/docs/trl/sft_trainer)

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#unsloth-%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%ABllm%E3%81%AE%E3%83%95%E3%82%A1%E3%82%A4%E3%83%B3%E3%83%81%E3%83%A5%E3%83%BC%E3%83%8B%E3%83%B3%E3%82%B0)unsloth: ローカルLLMのファインチューニング

[unsloth](https://github.com/unslothai/unsloth/tree/main)は、ローカルLLMのファインチューニングをより高速に、より低メモリで実行できるライブラリです。  
[README](https://github.com/unslothai/unsloth/tree/main)によると、これくらい速くなるみたいです。

|Unsloth supports|Performance|Memory use|
|---|---|---|
|**Llama 3.2 (3B)**|2x faster|60% less|
|**Phi-4 (14B)**|2x faster|50% less|
|**Llama 3.2 Vision (11B)**|2x faster|40% less|
|**Llama 3.1 (8B)**|2x faster|60% less|
|**Gemma 2 (9B)**|2x faster|63% less|
|**Qwen 2.5 (7B)**|2x faster|63% less|
|**Mistral v0.3 (7B)**|2.2x faster|73% less|
|**Ollama**|1.9x faster|43% less|
|**ORPO**|1.9x faster|43% less|
|**DPO Zephyr**|1.9x faster|43% less|

[README](https://github.com/unslothai/unsloth/tree/main)に各モデルに対するGoogleColab用のリンクが貼られており、動作確認できました。  
必要になったときに、動かしながら学ぶことができるので、今ここでコード解説をするのは避けます。  
今この段階では、こういうライブラリがあると知っておくことが重要です。

日本語の記事もありました: [UnslothでLlama3をファインチューニングする](https://zenn.dev/the_exile/articles/unsloth-llama3-fine-tuning)

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E7%84%A1%E6%96%99%E3%81%AEllm-api%E3%82%92%E4%BD%BF%E3%81%86)無料のLLM APIを使う

ここまでで、無料でもChatGPTやGeminiを使えることがわかりました。  
一方、APIは有料であるケースが多いですが、**2025年1月現在、Google AI StudioのGemini APIは無料**でお試しすることができます。  
Vertex AIのGemini と Google AI StudioのGeminiがありますが、ここで述べているのはGoogle AI Studioの方です。

2025年1月時点の[料金プラン](https://aistudio.google.com/app/plan_information)は次のようになっていました。  
[![price.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc7d91435-cda4-fa80-17b0-2743674b4c23.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=115a97fc298ca5c7c522289dc9f32e88)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc7d91435-cda4-fa80-17b0-2743674b4c23.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=115a97fc298ca5c7c522289dc9f32e88)  
Free of charge（無料）のプランが存在しています。  
RATE LIMITは厳し目であるため、お試しや勉強目的で使えるといった感じでしょうか。

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#api%E3%82%AD%E3%83%BC%E3%82%92%E4%BD%9C%E3%82%8B)APIキーを作る

Google AI Studioの「[Get API Key](https://aistudio.google.com/app/apikey)」を押して遷移したページでAPIキーを作成できます。  
「APIキーを作成」をクリックすると、下の画像のようにAPIキーが発行されます。  
[![](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F012ae73c-ab33-3f2b-4d37-260de64d6a7e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b2e9fbe51eb31b480247bfce58e06435)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F012ae73c-ab33-3f2b-4d37-260de64d6a7e.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=b2e9fbe51eb31b480247bfce58e06435)  
この画像では作成した瞬間は「有料」になっていましたが、リロードしたか時間が経過したらかはわかりませんが、あとから無料になっていました。

有料になっていたときに確認していた項目をここに記載しておきます。

- [請求先プロジェクトの確認](https://console.cloud.google.com/billing/linkedaccount)
    - 「このプロジェクトには請求先アカウントがありません」ならOKなはず
    - または、該当プロジェクトに「課金を無効にする」を設定しているならOKなはず
- [Generative Language APIの費用チェック](https://console.cloud.google.com/apis/api/generativelanguage.googleapis.com/cost)
    - 「費用は発生していません」なら大丈夫なはず

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#gemini-api%E3%81%AE%E4%BD%BF%E3%81%84%E6%96%B9)Gemini APIの使い方

[公式チュートリアル](https://ai.google.dev/gemini-api/docs/get-started/tutorial?lang=python&hl=ja)も充実しているので参考にしましょう。

環境構築

```
pip install google-generativeai
pip install python-dotenv
```

.env

```
GOOGLE_API_KEY="ここにAPIキーをいれる"
```

pythonコード

```
from dotenv import load_dotenv
load_dotenv()

import google.generativeai as genai
model = genai.GenerativeModel(model_name="gemini-1.5-flash")
result = model.generate_content("LLMについて教えてください")
print(result.text) # ここで自然言語の結果が得られる
```

これだけで動いてしまいます。簡単！  
これ以降、環境変数`GOOGLE_API_KEY`にAPIキーが格納されている前提で説明します。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#gemini%E3%81%AEjson%E5%87%BA%E5%8A%9B%E6%A9%9F%E8%83%BD)GeminiのJson出力機能

LLMを活用したシステムを組むときに、ときどき想定した出力形式にならず、困ったことはありませんか？  
どれだけプロンプトで指定しても、レスポンスに「Yes」か「No」を指示しているのに「Yesです」と余計な「です」が現れるといった余分な自然言語も返ってきたり...

これに対応するには、LangChainでフォーマットを指定するのが定石だと思いますが、[Geminiでは標準でJson出力をサポート](https://ai.google.dev/gemini-api/docs/structured-output?hl=ja&lang=python)しています。

geminiのjson出力例

```
import google.generativeai as genai
import typing_extensions as typing

class BirdGroup(typing.TypedDict):
    family: str # 科
    names: list[str] # 鳥の名前のリスト

model = genai.GenerativeModel("gemini-1.5-flash")
result = model.generate_content(
    "日本の身近な野鳥の名前を分類学の科(family)ごとにいくつか列挙してください。",
    generation_config=genai.GenerationConfig(
        response_mime_type="application/json", response_schema=list[BirdGroup]
    ),
)
print(result.text)
```

出力結果

```
[{"family": "アトリ科", "names": ["カワラヒワ", "アオジ", "イカル"]}, {"family": "スズメ科", "names": ["スズメ", "ハクセキレイ", "ツバメ"]}, {"family": "カラス科", "names": ["ハシブトガラス", "ハシボソガラス", "カケス"]}, {"family": "キジ科", "names": ["キジ", "ヤマドリ"]}, {"family": "タカ科", "names": ["トビ", "ノスリ"]}]
```

クラスを定義して、`generation_config`にフォーマットを指定してあげることでその形式に従ってくれます。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E7%94%BB%E5%83%8F%E5%85%A5%E5%8A%9B)画像入力

gemini APIは画像も入力できます。

- 環境構築: `pip install pillow`

geminiの画像入力

```
import PIL.Image
import google.generativeai as genai

model = genai.GenerativeModel("gemini-1.5-flash")

prompt = "この画像について説明してください"
image = PIL.Image.open("./java_sparrow.JPG")

response = model.generate_content([prompt, image])
print(response.text)
```

[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F945630bd-e78f-42ac-daf4-cbb0cfa627e2.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fe6db5d5a39ecc98de52606e7baf7321)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F945630bd-e78f-42ac-daf4-cbb0cfa627e2.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=fe6db5d5a39ecc98de52606e7baf7321)

- 参考: [Gemini API でビジョン機能を試す](https://ai.google.dev/gemini-api/docs/vision?hl=ja&lang=python)
    - 動画も入力できます

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#fine-tuning-1)Fine Tuning

[公式ドキュメント](https://ai.google.dev/gemini-api/docs/model-tuning?hl=ja)が丁寧なので、それに従うとうまくいきました。

Geminiのファインチューニング

```
import google.generativeai as genai

# 英単語の頭文字だけをみて何番目のアルファベットかを答えるダミーのトレーニングサンプル
training_data = [
    {"text_input": "Ant", "output": "1"},
    {"text_input": "Ball", "output": "2"},
    {"text_input": "Color", "output": "3"},
    {"text_input": "Destiny", "output": "4"},
    {"text_input": "Equal", "output": "5"},
    {"text_input": "Zebra", "output": "26"},
]
operation = genai.create_tuned_model(
    display_name="initial_to_number",
    source_model="models/gemini-1.5-flash-001-tuning",
    epoch_count=3,
    batch_size=4,
    learning_rate=0.001,
    training_data=training_data,
)
```

以上のコードで学習ジョブを投げることができます。  
パラメータやサンプルは適当なので実際やる際には調整しなければいけません。

学習状況の確認

```
import time
for status in operation.wait_bar():
    time.sleep(10)
```

学習のキャンセル

```
operation.cancel()
```

学習結果の取得

```
result = operation.result()
```

チューニングしたモデルを使う

```
model = genai.GenerativeModel(model_name=result.name)
output = model.generate_content("Ant")
print(output.text)
```

チューニングしたモデルの削除

```
genai.delete_tuned_model(result.name)
```

チューニング済みモデルの存在確認

```
for model_info in genai.list_tuned_models():
    print(model_info.name)
```

学習が適当なので結果は想定どおりになりませんでしたが、一通り動かせることを確認できました。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#text-embedding)Text Embedding

Text Embeddingは、自然言語の文字列をベクトル化するものです。

```
import google.generativeai as genai

result = genai.embed_content(
        model="models/text-embedding-004", # 英語モデル
#         model="models/text-multilingual-embedding-002", # 多言語モデル: 404になる
        content="This is a pen."
)

print(result['embedding'])
```

使えるモデル一覧は[こちら](https://cloud.google.com/vertex-ai/generative-ai/docs/embeddings/get-text-embeddings?hl=ja)に記載があります。  
2025年1月現在、多言語モデルを使おうとすると404になります。

> NotFound: 404 models/text-multilingual-embedding-002 is not found for API version v1beta, or is not supported for embedContent.

[issue](https://github.com/google-gemini/generative-ai-dart/issues/209)としても述べられていた。  
どうやらVertex AIのAPIからは使えるが、Google AI StudioのAPIからは呼べないっぽい。  
無理にAPIを使わずとも、SentenceTransformerやUniversal Sentence Encoderでもよい。

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#llm%E3%82%92%E7%B5%84%E3%81%BF%E5%90%88%E3%82%8F%E3%81%9B%E3%82%8B)LLMを組み合わせる

複数のLLMを組み合わせると様々なことが可能になります。

- 複雑な一連の処理を順番に別々のLLMに処理させ、1つのLLMでは難しかった複雑な処理を可能にする
- 数多く行う処理をタスクごとに複数のLLMに並列処理させ、各々の結果を集約する
- あるLLMの出力を別のLLMが評価して（LLM-as-a-judge）、変な出力になっていないかを確認する

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#dify)Dify

[Dify](https://dify.ai/jp)は、ノーコード（プログラミングしない）でLLMを組み合わせたチャットボットやワークフローが作れるサービスです。  
だれでも、UIを触れば使い方がわかるのが特徴です。

無料だとお試し程度に使えます。（[プラン比較](https://dify.ai/pricing)）  
OpenAIの無料クレジットが切れると次のようなエラーが出ます。

> Run failed: Model gpt-4o-mini credentials is not initialized.

代わりに、Geminiの無料APIキーを設定しましょう。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb966e301-24d7-c5bc-3886-ed9c9fff5151.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=67162d52d9a67cf2d96474cb7e24c31b)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb966e301-24d7-c5bc-3886-ed9c9fff5151.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=67162d52d9a67cf2d96474cb7e24c31b)

作れるアプリの種類はこれだけあります。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5bda338c-6394-c362-0cf6-47f933b8acfe.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=caf1f7ee82a37bbcf62ba201a735b7ea)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5bda338c-6394-c362-0cf6-47f933b8acfe.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=caf1f7ee82a37bbcf62ba201a735b7ea)

- チャットボット  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F02d7e95a-f1cf-8d40-76a1-375d16000081.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f7ef7446bbe14613cb08d52050b7246d)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F02d7e95a-f1cf-8d40-76a1-375d16000081.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=f7ef7446bbe14613cb08d52050b7246d)
    - 変数を入力させ、プロンプトに組み込める
    - コンテキストを埋め込める
- エージェント
    - 画像生成ツールや検索エンジンなど各種ツールと連携できます  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb8ae23a2-f45a-37c3-9fa4-013f32da6dbd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=524b8e1bb9d5ac2584c458f4a1ec79d4)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb8ae23a2-f45a-37c3-9fa4-013f32da6dbd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=524b8e1bb9d5ac2584c458f4a1ec79d4)
        - ツールに`current_time`と`qrcode_generator`を追加してみました
        - プロンプトに特に指示を与えなくても入力に応じてツール選択をしてくれてます
- テキストジェネレーター  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe34ab6e7-ee32-42c5-136e-ac8577a27bf1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=025d00a67c93a47076d53eddaf6d8fdc)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe34ab6e7-ee32-42c5-136e-ac8577a27bf1.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=025d00a67c93a47076d53eddaf6d8fdc)
    - チャットボットやエージェントと違って、繰り返しやり取りしない1度の生成タスク
- チャットフロー
    - 定義したフローをユーザーとの対話をしながら、繰り返し実行してくれます
    - LLMによる回答生成やIF文による分岐、変数の処理、ツール呼び出しなど自由な組み合わせが可能
    - 趣味と年齢を聞き出すまで繰り返すBOTの例  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fbe66eb4d-edad-3fcc-33b0-dc7c2f35efdf.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6b4b5d8362a9d3c55232b46a2e87981a)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fbe66eb4d-edad-3fcc-33b0-dc7c2f35efdf.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6b4b5d8362a9d3c55232b46a2e87981a)
        - 年齢を答えたにも関わらず表示が「0歳なんですね！」になってますが、ワークフロー処理を見てみるとちゃんと想定通りの回答になっています。  
            [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F197baaaa-2d31-f95e-8b1f-a71fb032949b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c49c629979a05d0fbf51c34e6332c389)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F197baaaa-2d31-f95e-8b1f-a71fb032949b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=c49c629979a05d0fbf51c34e6332c389)
    - 続けてみます  
        [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc64e7eda-195f-a58b-4027-d33203fa2d8b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a7002eb62be5b298a1434fef53081078)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc64e7eda-195f-a58b-4027-d33203fa2d8b.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a7002eb62be5b298a1434fef53081078)
    - またもや表示がおかしい現象に遭遇しました。なぜか2重に表示されています。同様にワークフロー処理を見ると正しく処理されてそうだったので、おそらくDify側のバグだと思われます （過去に[Issue報告](https://github.com/langgenius/dify/issues/9560)されていましたが、まだ直っていないんですかね。）
- ワークフロー
    - チャットフローと同じように複雑なフローを書けます
    - 違いはユーザーとの繰り返しのやり取りをしない点です
    - 構成要素は同じなので省略します

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#langchain)LangChain

LangChainとは、LLMを使ったアプリケーション開発を楽にするライブラリです。  
次のようなことができます:

- 指定した形式での出力
- 複数のLLMをつなげて、順番に処理をする
- それぞれ機能を持ったLLMや関数を用意して、状況に応じたLLMや関数を呼び出す

Difyでは手の届かなかった部分、かゆいところに手が届くといった感じです。

ここでもGoogle AI StudioのGemini APIを使います。

環境構築

```
pip install langchain
pip install langchain-google-genai
```

langchainでgeminiを使うには`langchain-google-genai`をインストールします。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E5%9F%BA%E7%A4%8E)基礎

基礎

```
from dotenv import load_dotenv
load_dotenv()

from langchain_google_genai import ChatGoogleGenerativeAI
llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")
result = llm.invoke("LLMについて教えて下さい")
print(result.content)
```

- dotenvで環境変数`GOOGLE_API_KEY`にAPIキーを格納

ChatGoogleGenerativeAI vs GoogleGenerativeAI

- [](https://stackoverflow.com/questions/76950609/what-is-the-difference-between-openai-and-chatopenai-in-langchain)
- [](https://qiita.com/nakamasato/items/7366dc4d7278799ef04a)
- [](https://blog.micheam.com/2023/08/09/openai-chat-completions-vs-completions/)

  

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E5%AE%9F%E8%A1%8C%E5%B1%A5%E6%AD%B4%E3%81%AE%E3%83%88%E3%83%AC%E3%83%BC%E3%82%B9)実行履歴のトレース

LangChainでは複数のLLMを組み合わせて開発していくため、動作の把握が大変になります。

ここではConsoleCallbackHandler, LangSmithを紹介しますが、他には[Langfuse](https://langfuse.com/)もあります。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#consolecallbackhandler)ConsoleCallbackHandler

ConsoleCallbackHandler

```
from langchain.callbacks.tracers import ConsoleCallbackHandler
result = llm.invoke(
    "LLMについて教えて下さい", 
    config={"callbacks": [ConsoleCallbackHandler()]}
)
```

invoke時に`ConsoleCallbackHandler`を渡すと、実行結果をトレースできます。  
LLMが1つしか無い場合は必要性を感じませんが、後述するようにLLMをつなぎ合わせたchainに対してinvokeする際に、各LLMの結果が出力されるようになるため、デバッグに役立ちます。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#langsmith)LangSmith

実行結果をオンライン上でトレース可能なツールに[LangSmith](https://www.langchain.com/langsmith)があります。  
登録後のチュートリアルに従って一瞬で設定できます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc9c8b981-b2cf-b969-9b3f-c9aa06160286.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1ed593005de87f140373bd15bf6cff0c)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc9c8b981-b2cf-b969-9b3f-c9aa06160286.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=1ed593005de87f140373bd15bf6cff0c)

- `pip install langsmith`
- `.env`ファイルを以下のように追記します
    
    .env
    
    ```
    GOOGLE_API_KEY="GOOGLEのAPIキー"
    # 以下を追加
    LANGSMITH_TRACING=true
    LANGSMITH_ENDPOINT="https://api.smith.langchain.com"
    LANGSMITH_API_KEY="LANGSMITHのAPIキー"
    LANGSMITH_PROJECT="プロジェクト名"
    ```
    

この状態でソースコードを実行すると、自動的にウェブ上で実行結果のトレースができます。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F57f48a29-6c1f-db38-6409-5cfa1e015865.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bbcb2437b41ac6cf4c277c8871f2f690)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F57f48a29-6c1f-db38-6409-5cfa1e015865.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bbcb2437b41ac6cf4c277c8871f2f690)

オンライン上で見やすく確認できますが、プロジェクトによってはWeb上にアップされると困るため注意が必要です。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#json%E5%87%BA%E5%8A%9B%E6%A7%8B%E9%80%A0%E5%8C%96%E5%87%BA%E5%8A%9B)Json出力、構造化出力

入力分から要素を抽出するタスクを解いてみます。

json出力

```
from langchain.output_parsers import ResponseSchema, StructuredOutputParser
from langchain.prompts import PromptTemplate
from langchain_google_genai import ChatGoogleGenerativeAI

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")

SYSTEM_PROMPT = """
「入力文」から「趣味」「職業」「年齢」を抽出してください。
該当する項目がなければnullで埋めてください。

# 入力文
{input_str}

# Format instructions
{format_instructions}
"""

response_schemas = [
    ResponseSchema(name="趣味", description="ユーザーの入力文から判断できる趣味", type="string"),
    ResponseSchema(name="職業", description="ユーザーの入力文から判断できる職業", type="string"),
    ResponseSchema(name="年齢", description="ユーザーの入力文から判断できる年齢", type="integer"),
]
output_parser = StructuredOutputParser.from_response_schemas(response_schemas)

prompt_template = PromptTemplate(
    template=SYSTEM_PROMPT,
    input_variables=["input_str"],
    partial_variables={"format_instructions": output_parser.get_format_instructions()},
)

chain = prompt_template | llm | output_parser
result=chain.invoke("Webデザイナーをしています。山を登ることが趣味です。生まれてから20年経過しました。")
print(result) # -> {'趣味': '山登り', '職業': 'Webデザイナー', '年齢': 20}
print(type(result)) # -> <class 'dict'>
```

dictで結果を得る事ができました。  
プロンプトに例を示すときは[FewShotPromptTemplate](https://python.langchain.com/docs/how_to/few_shot_examples/)も使えます。

無事dictで結果を得られましたが、いくらプロンプトに出力指示を入れたとしても、LLMの気分によっては想定通りの回答にならない可能性があります。  
`OutputFixingParser`で`StructuredOutputParser`をラップすると、正しいフォーマットになるように別のLLMに依頼できます。  
リトライする回数も指定できます。

OutputFixingParserの使い方

```
from langchain.output_parsers import OutputFixingParser

output_fixing_parser = OutputFixingParser.from_llm(
    parser=output_parser, llm=llm, max_retries=3,
)

chain = prompt_template | llm | output_fixing_parser
result = chain.invoke("普段はAIエンジニアをしていますが、休日には外に出て鳥を観察しに行き、撮影をします")
print(result) # -> {'趣味': '鳥の観察、撮影', '職業': 'AIエンジニア', '年齢': None}
```

リストで出力したいケースは、[ListOutputParser](https://python.langchain.com/api_reference/core/output_parsers/langchain_core.output_parsers.list.ListOutputParser.html)を使いましょう。

dictのlistのような複雑な構造を扱う場合は、`PydanticOutputParser`や`with_structured_output`が使えます。

dictのlistのような構造出力の場合

```
SYSTEM_PROMPT = """
日本の身近な野鳥を、名前と体長をセットでいくつか列挙してください。

# Format instructions
{format_instructions}
"""
# 出力したい型を定義
class Bird(BaseModel):
    name: str = Field(description="名前")
    length: int = Field(description="体長(cm)")

class BirdList(BaseModel):
    birds: list[Bird] = Field(description="Birdのリスト")

# PydanticOutputParserで行う場合
output_parser = PydanticOutputParser(pydantic_object=BirdList)
prompt_template = PromptTemplate(
    template=SYSTEM_PROMPT,
    partial_variables={"format_instructions": output_parser.get_format_instructions()},
)

chain1 = prompt_template | llm | output_parser
print(chain1.invoke({}))
# -> birds=[Bird(name='スズメ', length=14), Bird(name='ハト', length=30), Bird(name='ムクドリ', length=22), Bird(name='カラス', length=45), Bird(name='メジロ', length=11), Bird(name='シジュウカラ', length=14)]

# with_structured_outputで行う場合
chain2 = llm.with_structured_output(BirdList)
print(chain2.invoke("日本の身近な野鳥を、名前と体長をセットでいくつか列挙してください。"))
# -> birds=[Bird(name='スズメ', length=14), Bird(name='ハクセキレイ', length=27), Bird(name='ムクドリ', length=22)]
```

`BirdList`のクラスで結果を得る事ができます。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#chain%E8%A4%87%E6%95%B0%E3%81%AEllm%E3%82%92%E7%B5%84%E3%81%BF%E5%90%88%E3%82%8F%E3%81%9B%E3%82%8B)Chain：複数のLLMを組み合わせる

いよいよLangChainの名にふさわしいLLMを組み合わせる話です。  
1つのLLMで処理するより、複数のLLMに分けて順番に処理したほうが性能向上できたり、どこがボトルネックかを判断しやすくなります。  
タスクを分解して並列処理した結果をまとめ上げることもできます。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E9%A0%86%E7%95%AA%E3%81%AB%E5%87%A6%E7%90%86%E3%81%99%E3%82%8B)順番に処理する

段階に分けた処理

```
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.output_parsers import StrOutputParser
from langchain.prompts import PromptTemplate
from langchain.callbacks.tracers import ConsoleCallbackHandler

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")

prompt1 = PromptTemplate.from_template("次の文章から趣味を抽出して1つの単語で回答してください。\n{user_input}")
chain1 = prompt1 | llm | StrOutputParser()

prompt2 = PromptTemplate.from_template("次の記述を英語に翻訳してください。\n{hobby}")
chain2 = prompt2 | llm | StrOutputParser()

chain = chain1 | chain2

print(chain.invoke(
    "普段はAIエンジニアをしていますが、休日には外に出て鳥を観察しに行き、撮影をします", 
    config={'callbacks': [ConsoleCallbackHandler()]}
))
```

- `ConsoleCallbackHandler`: デバッグ用ターミナル出力（なくてもよい）

LangSmithでは次のように2つのLLMの入出力を確認できました。  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F20daffad-3bf5-5924-7049-32fd25594199.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3238ce758c83191230861c294c6a8bbd)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F20daffad-3bf5-5924-7049-32fd25594199.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=3238ce758c83191230861c294c6a8bbd)  
[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F54d4c85d-b275-f818-8b73-4ad616b2969d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e681e9378bab7f7a84564c90ccefafbd)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F54d4c85d-b275-f818-8b73-4ad616b2969d.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e681e9378bab7f7a84564c90ccefafbd)  
バードウォッチング→Birdwatchingと順に処理できていることがわかります。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E4%B8%A6%E5%88%97%E5%87%A6%E7%90%86)並列処理

並列処理

```
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.output_parsers import StrOutputParser
from langchain.prompts import PromptTemplate
from langchain.callbacks.tracers import ConsoleCallbackHandler
from langchain_core.runnables import RunnableLambda, RunnableParallel

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")

prompt1 = PromptTemplate.from_template("次の文章から趣味を抽出して1つの単語で回答してください。\n{user_input}")
chain1 = prompt1 | llm | StrOutputParser()
prompt2 = PromptTemplate.from_template("次の文章から職業を抽出して1つの単語で回答してください。\n{user_input}")
chain2 = prompt2 | llm | StrOutputParser()

prompt3 = PromptTemplate.from_template("次の記述を{language}に翻訳してください。\n{hobby}, {occupation}")
chain3 = prompt3 | llm | StrOutputParser()

chain = RunnableParallel(
    {"hobby": chain1, "occupation": chain2, "language": RunnableLambda(lambda x: "英語")}
) | chain3

print(chain.invoke(
    "普段はAIエンジニアをしていますが、休日には外に出て鳥を観察しに行き、撮影をします", 
    config={'callbacks': [ConsoleCallbackHandler()]},
))
```

- `RunnableParallel`: 並列実行してくれます
    - RunnableParallelを削除しても動きますが、並列ではなく順番に実行されるはずです
- `RunnableLambda`: 関数の結果をLLMに渡せます
    - この例では"英語"という定数を返す関数となっています

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#langgraph-%E3%83%AF%E3%83%BC%E3%82%AF%E3%83%95%E3%83%AD%E3%83%BC%E5%AE%9A%E7%BE%A9)LangGraph: ワークフロー定義

LangGraphはLangChainに含まれるライブラリの1つで、LangChainが直線的なつながりを処理するのに対して、LangGraphはその名の通りグラフ構造を持ったワークフローを定義できます。

LangGraphの特徴

- サイクリックグラフをサポート: ループや分岐など自由な記述が可能
- ステート管理: グラフの実行中に状態（State）を管理する機能を持つ
- 可視化: 処理の流れを図に出力できる

LangGraphの基本的な機能を説明するために、次のグラフのワークフローを定義する例を示します。  
次の例は、3羽分鳥に関する情報を集めるワークフローです。

これをLangGraphで書くとこうなります。  
コメントを見ながら御覧ください。

- `pip install langgraph`

LangGraphの基礎

```
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.output_parsers import StrOutputParser

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash") | StrOutputParser()

from typing_extensions import TypedDict, Literal
from langchain_core.runnables import RunnableConfig
from langgraph.graph import StateGraph

# 保持したい情報
class State(TypedDict):
    name: str
    area: str
    feature: str
    names: list[str]

# nodeに使う関数定義（returnした値が更新されます）
def get_name(state: State, config: RunnableConfig):
    return {"name": chain.invoke(f"{state['names']}以外の鳥の名前を1つあげて")}

def get_feature(state: State, config: RunnableConfig):
    return {"feature": chain.invoke(f"{state['name']}の特徴を一言で")}

def get_area(state: State, config: RunnableConfig):
    return {"area": chain.invoke(f"{state['name']}の生息地を一言で")}

def output(state: State, config: RunnableConfig):
    print(state)
    return {"names": state["names"] + [state["name"]]}

def finish(state: State, config: RunnableConfig):
    return {}

# 分岐
def judge(state: State, config: RunnableConfig) -> Literal["name_node", "finish"]:
    return "name_node" if len(state["names"]) < 3 else "finish"

# ワークフロー定義
workflow = StateGraph(State)
# node追加
workflow.add_node("name_node", get_name)
workflow.add_node("feature_node", get_feature)
workflow.add_node("area_node", get_area)
workflow.add_node("output", output)
workflow.add_node("finish", finish)
# edge追加
workflow.add_edge("name_node", "area_node")
workflow.add_edge("name_node", "feature_node")
workflow.add_edge("area_node", "output")
workflow.add_edge("feature_node", "output")
# 始点
workflow.set_entry_point("name_node")
# 終点
workflow.set_finish_point("finish")
# 分岐
workflow.add_conditional_edges("output", judge)

# コンパイル
graph = workflow.compile()
# 実行
print(graph.invoke({"names": []}))
# グラフのmermaid図示
print(graph.get_graph().draw_mermaid())
```

出力結果

> {'name': 'スズメ', 'area': '全世界', 'feature': '身近な小鳥', 'names': []}  
> {'name': 'カラス', 'area': '世界中', 'feature': '知能が高い', 'names': ['スズメ']}  
> {'name': 'ハト', 'area': '世界中', 'feature': '平和の象徴', 'names': ['スズメ', 'カラス']}  
> {'name': 'ハト', 'area': '世界中', 'feature': '平和の象徴', 'names': ['スズメ', 'カラス', 'ハト']}

Stateの更新の仕方が「returnした部分が上書きされる」という少し特殊な書き方に思えますが、他の部分は直感的に理解できる記述方法だと思います。  
また、最後のmermaid出力は、最初に見せた図から日本語の説明を省いたものとなっているはずです。  
最初に見せた図はこの出力を元に作りました。（mermaidのプレビューは[こちら](https://birdwatcheryt.github.io/software/mermaid/index.html)）

- 参考: [LangGraphの基本的な使い方](https://zenn.dev/pharmax/articles/8796b892eed183)

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#agent-%E7%8A%B6%E6%B3%81%E3%81%AB%E5%BF%9C%E3%81%98%E3%81%9F%E9%96%A2%E6%95%B0%E3%82%92%E5%91%BC%E3%81%B3%E5%87%BA%E3%81%99%E4%BE%8B)Agent: 状況に応じた関数を呼び出す例

非推奨な旧バージョンの方法

[](https://python.langchain.com/docs/how_to/migrate_agent/)

[LangGraph](https://langchain-ai.github.io/langgraph/)のcreate_react_agentを使って、状況に応じて関数を呼び出す例を書いてみます。

- 追加ライブラリ: `pip install langgraph`

状況に応じた関数呼び出し

```
from langchain_google_genai import ChatGoogleGenerativeAI
from langgraph.prebuilt import create_react_agent
from langchain_core.tools import tool

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")

@tool
def func_bird(input_str: str) -> str:
    """鳥に関する質問に答える"""
    print("called func_bird")
    return "それは鳥です。"

@tool
def func_add(a: int, b: int) -> int:
    """足し算をする"""
    print("called func_add")
    return a + b

tools = [func_bird, func_add]

langgraph_agent_executor = create_react_agent(llm, tools)
result = langgraph_agent_executor.invoke(
    {"messages": [("human", "ハトについて教えて")]}
    # {"messages": [("human", "3と4を足すとどうなる？")]}
)
print(result["messages"][-1].content)
```

- `create_react_agent`でエージェントを作れます
    - ReAct（REasoning and ACTing）: 推論と行動
- 関数のdocstringの説明を見て呼ぶ関数を判断してくれます
    - "ハトについて教えて"では、func_birdが呼ばれる
    - "3と4を足すとどうなる？"では、func_addが呼ばれる

[![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5c88ee80-9a40-f756-03c2-cd59c4e3cbe3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e98f4244d39846a0b764c5e522655059)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F5c88ee80-9a40-f756-03c2-cd59c4e3cbe3.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e98f4244d39846a0b764c5e522655059)

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#memory-%E5%AF%BE%E8%A9%B1%E5%B1%A5%E6%AD%B4%E3%81%AE%E8%A8%98%E6%86%B6)Memory: 対話履歴の記憶

langchainで定義したモデルに、連続してクエリを投げても、前のチャット履歴を覚えていません。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#runnablewithmessagehistory%E3%82%92%E4%BD%BF%E3%81%86)RunnableWithMessageHistoryを使う

会話履歴を覚えておくには、`RunnableWithMessageHistory`を使った以下のような実装が必要です。  
(調べると`ConversationBufferMemory`を使った方法も出てきますが非推奨となったため、[公式ドキュメント](https://python.langchain.com/api_reference/core/runnables/langchain_core.runnables.history.RunnableWithMessageHistory.html)を参考に短いコードで書くとこんな感じになりました。)

対話履歴の記憶

```
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.chat_history import BaseChatMessageHistory
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import ChatMessageHistory

prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "あなたは優秀なアシスタントです。"),
        MessagesPlaceholder(variable_name="chat_history"), # ここにチャット履歴が入る
        ("human", "{human_input}"),
    ]
)
llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")
chain = prompt | llm

store = {}
def get_history(session_id: str) -> BaseChatMessageHistory:
    if session_id not in store:
        store[session_id] = ChatMessageHistory()
    return store[session_id]

chain_with_history = RunnableWithMessageHistory(
    chain,
    get_history,
    input_messages_key="human_input",
    history_messages_key="chat_history",
)

print(chain_with_history.invoke(
    {"human_input": "こんにちは！私の名前はbirdwatcherです。"},
    config={"configurable": {"session_id": "test-id"}},
))
print(chain_with_history.invoke(
    {"human_input": "私の名前を覚えてますか？"},
    config={"configurable": {"session_id": "test-id"}},
))
print(store)
```

> はい、覚えています。あなたはbirdwatcherさんですね。

と前のやり取りを覚えてくれてました。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#memorysaver%E3%82%92%E4%BD%BF%E3%81%86)MemorySaverを使う

agentを使った書き方もあります。  
MemorySaverをcreate_react_agentに渡してあげるだけで記憶してくれます。

agentによる対話履歴の記憶

```
from langgraph.checkpoint.memory import MemorySaver
from langgraph.prebuilt import create_react_agent
from langchain_google_genai import ChatGoogleGenerativeAI

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")

system_message = "あなたは優秀なアシスタントです"

memory = MemorySaver()
langgraph_agent_executor = create_react_agent(
    llm, [], state_modifier=system_message, checkpointer=memory
)

config = {"configurable": {"thread_id": "test-id"}}
print(
    langgraph_agent_executor.invoke(
        {"messages": [("user", "こんにちは！私の名前はbirdwatcherです。")]},
        config,
    )["messages"][-1].content
)
print(
    langgraph_agent_executor.invoke(
        {"messages": [("user", "私の名前を覚えてますか？")]}, config
    )["messages"][-1].content
)
```

> こんにちは、birdwatcherさん！はじめまして。何かお手伝いできることはありますか？  
> はい、覚えています。 あなたの名前はbirdwatcherさんです。

- 参考: [How to add chat history](https://python.langchain.com/docs/how_to/qa_chat_history_how_to/)

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E4%BC%9A%E8%A9%B1%E5%B1%A5%E6%AD%B4%E3%81%AE%E8%A6%81%E7%B4%84)会話履歴の要約

先程の方法で会話履歴を保持しつづけると、どんどん長くなっていくため、適当に切り捨てたり、要約が必要になるでしょう。  
ここでは簡単な会話履歴の要約を紹介します。  
（調べると`ConversationSummaryMemory`を使った方法も出てきますが非推奨となったため、[LangChainのドキュメント](https://python.langchain.com/docs/how_to/chatbots_memory/)から拝借しました。[LangGraph側のドキュメント](https://langchain-ai.github.io/langgraph/how-tos/memory/add-summary-conversation-history/)も参考になります。）

会話履歴の要約

```
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, RemoveMessage
from langgraph.checkpoint.memory import MemorySaver
from langgraph.graph import START, MessagesState, StateGraph
from langchain_google_genai import ChatGoogleGenerativeAI

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")

def call_model(state: MessagesState):
    system_prompt = "あなたは有能なアシスタントです。すべての質問にできる限り答えてください。提供されたチャット履歴には、以前の会話の要約が含まれることがあります。"
    system_message = SystemMessage(content=system_prompt)
    message_history = state["messages"][:-1]
    if len(message_history) >= 4:  # 一定の長さを超えたら要約する
        last_human_message = state["messages"][-1]
        summary_prompt = "上記のチャットメッセージを要約してください。ただし、できるだけ多くの具体的な詳細を含めてください。"
        summary_message = llm.invoke(
            message_history + [HumanMessage(content=summary_prompt)]
        )
        delete_messages = [RemoveMessage(id=m.id) for m in state["messages"]]
        human_message = HumanMessage(content=last_human_message.content)
        response = llm.invoke([system_message, summary_message, human_message])
        message_updates = [summary_message, human_message, response] + delete_messages
    else:
        message_updates = llm.invoke([system_message] + state["messages"])
    return {"messages": message_updates}


workflow = StateGraph(state_schema=MessagesState)
workflow.add_node("llm", call_model)
workflow.add_edge(START, "llm")

memory = MemorySaver()
app = workflow.compile(checkpointer=memory)

chat_history = [
    HumanMessage(content="こんにちは！私の名前はbirdwatcherです。"),
    AIMessage(content="こんにちは!どうしましたか？"),
    HumanMessage(content="今日の気分は？"),
    AIMessage(content="とてもよいです。"),
]
print(app.invoke(
    {"messages": chat_history + [HumanMessage("私の名前を覚えていますか？")]},
    config={"configurable": {"thread_id": "test-id"}},
))
```

- 削除するメッセージは、リストからpopするだけでよいわけではなく、`RemoveMessage`で返してあげる必要があります

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#rag-%E5%A4%96%E9%83%A8%E7%9F%A5%E8%AD%98%E3%82%92%E6%B4%BB%E7%94%A8%E3%81%99%E3%82%8B%E4%BE%8B)RAG: 外部知識を活用する例

RAG（Retrieval-augmented generation）とは、LLMに検索機能を組み合わせた仕組みです。

ユーザーの質問(query)から、関連性の高いドキュメントを検索しLLMに渡した上で(context)、回答文を生成します(answer)。

- 環境構築: `pip install langchain_community chromadb`

近似最近傍探索

```
from langchain_community.vectorstores import Chroma
from langchain_google_genai import GoogleGenerativeAIEmbeddings

target_texts = ["This is sample.", "This is a pen."]

vectorstore = Chroma.from_texts(
    texts = target_texts,
    embedding=GoogleGenerativeAIEmbeddings(model="models/text-embedding-004")
)
docs = vectorstore.similarity_search_with_score('This is a pencile')

for doc in docs:
    print(doc)
```

Chromaは、近似近傍探索のライブラリです。FAISSを使うこともできます。

テキストファイルから連続2つの改行でチャンク分割してデータベースを作り、質問に応答するRAGサンプルを次に示します。

外部ドキュメントを使って応答する(RAG)

```
from langchain_google_genai import ChatGoogleGenerativeAI, GoogleGenerativeAIEmbeddings
from langchain_community.document_loaders import TextLoader
from langchain.text_splitter import CharacterTextSplitter
from langchain_community.document_loaders import TextLoader
from langchain_community.vectorstores import Chroma
from langchain import hub
from langchain.chains import create_retrieval_chain
from langchain.chains.combine_documents import create_stuff_documents_chain
# テキストファイルからドキュメントを作る
loader = TextLoader("./sample.txt")
text_splitter = CharacterTextSplitter(
    separator="\n\n", # 改行2つで区切ってチャンクを作る
    chunk_size=200, # チャンクの最大サイズ
    chunk_overlap=0, # チャンク同士の重なりの許容量
    length_function=len,
)
docs = loader.load_and_split(text_splitter)
vectorstore = Chroma.from_documents(
    docs, GoogleGenerativeAIEmbeddings(model="models/text-embedding-004")
)

llm = ChatGoogleGenerativeAI(model="gemini-1.5-flash")
prompt = hub.pull("langchain-ai/retrieval-qa-chat") # 中身はこちら https://smith.langchain.com/hub/langchain-ai/retrieval-qa-chat

combine_docs_chain = create_stuff_documents_chain(llm, prompt)
rag_chain = create_retrieval_chain(
    vectorstore.as_retriever(search_kwargs={"k": 1}), combine_docs_chain
)
print(rag_chain.invoke({"input": "What are the characteristics of the Oriental Turtle Dove?"}))
print(rag_chain.invoke({"input": "Tell me the pattern of the rock dove?"}))
```

sample.txt

```
# Types of pigeons
The pigeons we often see around us are the rock dove and the turtle dove.

## Rock dove
The distinctive feature of the rock dove's appearance is the purplish gradient on its neck.
Rock doves are also called feral pigeons.

## Turtle dove
The distinctive feature of the turtle dove's appearance is the blue and white pattern on its neck.
Turtle doves chirp "de-de-po-po."
```

- 1つ目の質問: What are the characteristics of the Oriental Turtle Dove?  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc520cba1-1507-26d3-4bc6-6df6e9f67025.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a3479562b5502030b0b69b0126c479f6)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fc520cba1-1507-26d3-4bc6-6df6e9f67025.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=a3479562b5502030b0b69b0126c479f6)
    - documentsとしてturtle doveのセクションだけが渡されていることがわかります
    - このドキュメントを元に回答が得られました
- 2つ目の質問: Tell me the pattern of the rock dove?  
    [![image.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F93eecd14-0f61-6f86-69ee-633e2e116eee.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=416c9d13b54bb4e08c2ff318f0e46f55)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F93eecd14-0f61-6f86-69ee-633e2e116eee.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=416c9d13b54bb4e08c2ff318f0e46f55)
    - documentsとしてrock doveのセクションだけが渡されています

`VectorstoreIndexCreator`を使った書き方だと近傍数`k=1`を指定する方法がわからなかったり、ネット上には`RetrievalQA`を使った非推奨コードも多く苦労しましたが、[公式の移行ドキュメント](https://python.langchain.com/docs/versions/migrating_chains/retrieval_qa/)で解決できました。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#graphrag-%E7%9F%A5%E8%AD%98%E3%82%B0%E3%83%A9%E3%83%95%E3%81%AE%E6%B4%BB%E7%94%A8)GraphRAG: 知識グラフの活用

先程はベクトル検索を使った情報検索でしたが、今度はグラフを使った情報検索です。  
GraphRAGは、3種類の検索を扱います。

- グラフ検索
- 全文検索
- ベクトル検索

これらの検索結果を組み合わせて、ユーザーの質問に回答します。  
LangChainとグラフデータベースNeo4jを使って実装できます。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#neo4j%E3%82%BB%E3%83%83%E3%83%88%E3%82%A2%E3%83%83%E3%83%97)Neo4jセットアップ

まずはNeo4jの環境構築から。  
[ここ](https://neo4j.com/download/)からダウンロードして、インストールします。  
起動後の画面でDL時にブラウザに表示された「Activation Key」を入力します。  
最初、「Movie DBMS」というExampleが起動しているので、「Stop」で停止します。

今回はグラフデータベースをローカルに作ります。  
「Add」から「Local DBMS」を選択し、適当なパスワード（後に使います）を設定して作成します。  
[![スクリーンショット 2025-01-24 001741.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe5b22949-1936-25a7-ad64-988355148e10.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5bb7122a0b81be283880ac1204825c72)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe5b22949-1936-25a7-ad64-988355148e10.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=5bb7122a0b81be283880ac1204825c72)  
「Active」状態になったらOKです。  
[![スクリーンショット 2025-01-24 002848.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe204faba-8193-03f3-6aee-c620a8f20d18.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=08b2f92188e907a9e9969649fc7d7aa6)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fe204faba-8193-03f3-6aee-c620a8f20d18.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=08b2f92188e907a9e9969649fc7d7aa6)  
次に「Graph DBMS」を押したときに出る右ペインの「Plugins」から、「APOC」をインストールします。  
[![スクリーンショット 2025-01-24 003603.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F66e67fa7-d8cb-2c77-83d0-fee89c08e573.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e89324f895247fb511bfbcc186ddf5d9)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F66e67fa7-d8cb-2c77-83d0-fee89c08e573.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e89324f895247fb511bfbcc186ddf5d9)  
その後、「Stop」の右にある更新マークから「Graph DBMS」を再起動します。

これで準備完了です。  
LangChainからの接続を確認しましょう。

- `pip install langchain_neo4j`

接続確認用コード

```
from langchain_neo4j import Neo4jGraph

url = "bolt://localhost:7687" # LLMからのアクセス
username = "neo4j"
password = "password" # 設定したパスワード
graph = Neo4jGraph(url=url, username=username, password=password)
```

正常にセットアップできていれば、エラーが出ることなくこのプログラムは終了します。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E3%81%8B%E3%82%89%E3%82%B0%E3%83%A9%E3%83%95%E5%8C%96)テキストからグラフ化

テキストデータからグラフに変換します。  
RAGのときに使った`sample.txt`を使います。

まず、テキストからドキュメントへ分割します。

ドキュメントへの分割

```
from langchain_community.document_loaders import TextLoader
from langchain.text_splitter import TokenTextSplitter

raw_documents = TextLoader("./sample.txt").load()
text_splitter = TokenTextSplitter(chunk_size=64, chunk_overlap=16)
documents = text_splitter.split_documents(raw_documents)
print(documents)
```

出力結果

テキストデータから、適当なチャンクサイズでDocumentという単位に分割しました。  
今回は小さいテキストファイルなので、Documentが1つだけできました。

次にドキュメントからグラフデータに変換します。

- `pip install langchain_experimental`

グラフへの変換

```
from langchain_google_genai import GoogleGenerativeAI # ChatGoogleGenerativeAIだとなぜかエラーが出た
from langchain_experimental.graph_transformers import LLMGraphTransformer

llm = GoogleGenerativeAI(model="gemini-1.5-flash")
llm_transformer = LLMGraphTransformer(llm=llm)
graph_documents = llm_transformer.convert_to_graph_documents(documents)
print(graph_documents)
```

出力結果

GraphDocumentというクラスで、NodeやRelationshipが記載されています。

neo4jデータベースへの登録

```
from langchain_neo4j import Neo4jGraph

url = "bolt://localhost:7687"
username = "neo4j"
password = "password"
graph = Neo4jGraph(url=url, username=username, password=password)
graph.add_graph_documents(graph_documents, baseEntityLabel=True, include_source=True)
```

- `baseEntityLabel=True`: 各データに対して基本的なエンティティラベルを追加
- `include_source=True`: ドキュメントのソース情報をグラフに保存する

「Neo4j Desktop」右上の「Open」から、ブラウザでグラフを確認できます。  
起動後、左側のデータベースマークを押した後、「Node labels」から「*」を押すとこのような画面が現れました。  
[![スクリーンショット 2025-01-24 030129.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F06957d64-4301-7f40-6b44-a73aae43f049.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4395f0ba88644cf802bffbcddb78b4bd)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F06957d64-4301-7f40-6b44-a73aae43f049.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=4395f0ba88644cf802bffbcddb78b4bd)

テキストをLLMに与えただけなのに、勝手にグラフができるので驚きですね。

何度も実行する用にグラフをすべて削除するクエリを置いておきます。

- `MATCH (n) DETACH DELETE n;`

Neo4jの上の窓から実行できます。

##### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E5%85%A8%E6%96%87%E6%A4%9C%E7%B4%A2%E3%81%A8%E3%83%99%E3%82%AF%E3%83%88%E3%83%AB%E6%A4%9C%E7%B4%A2%E3%81%AE%E3%83%8F%E3%82%A4%E3%83%96%E3%83%AA%E3%83%83%E3%83%89%E6%A4%9C%E7%B4%A2--%E3%82%B0%E3%83%A9%E3%83%95%E6%A4%9C%E7%B4%A2)全文検索とベクトル検索のハイブリッド検索 + グラフ検索

GraphRAGでは、3つの検索を扱うと説明しました。  
まずは、全文検索とベクトル検索をできるようにインデックスを作成します。

ベクトルインデックスの作成

```
from langchain_community.vectorstores import Neo4jVector
from langchain_google_genai import GoogleGenerativeAIEmbeddings
# グラフからインデックスを生成する
vector_index = Neo4jVector.from_existing_graph(
    GoogleGenerativeAIEmbeddings(model="models/text-embedding-004"), # Embeddingモデル
    url=url,
    username=username,
    password=password,
    search_type="hybrid",  # 全文検索とベクトル検索のハイブリッド
    node_label="Document",  # 検索対象ノードのラベル
    text_node_properties=["text"],  # 検索対象プロパティ
    embedding_node_property="embedding",
)
# 全文検索インデックスの作成
graph.query("CREATE FULLTEXT INDEX entity IF NOT EXISTS FOR (e:__Entity__) ON EACH [e.id]")
```

今回最初のチャンク分割により生成されたDocumentラベルのあるノードを検索対象とし、そのノードのtextプロパティをベクトル化することを意味しています。  
[![スクリーンショット 2025-01-24 030423.png](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F482419cf-eba8-10ac-c1f8-097585c8accd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=243cb56d54f319b17b9a8d07953cd8e9)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2F482419cf-eba8-10ac-c1f8-097585c8accd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=243cb56d54f319b17b9a8d07953cd8e9)

この時点でクエリを投げてみます。

全文検索とベクトル検索

```
from langchain.chains import RetrievalQAWithSourcesChain

chain = RetrievalQAWithSourcesChain.from_chain_type(
    llm,
    chain_type="stuff", # 検索結果として得られたすべてのドキュメントを「そのまま（stuff）」LLMに渡す
    retriever=vector_index.as_retriever(),
    verbose=True,
)
print(chain.invoke("Tell me the pattern of the rock dove"))
print(chain.invoke("Please tell me the type of pigeon"))
```

> {'question': 'Tell me the pattern of the rock dove', 'answer': "The distinctive feature of the rock dove's appearance is a purplish gradient on its neck.\n", 'sources': './sample.txt'}  
> {'question': 'Please tell me the type of pigeon', 'answer': 'The provided text mentions two types of pigeons: the rock dove (also called feral pigeon) and the turtle dove.\n', 'sources': './sample.txt'}

ドキュメント2つしかありませんし、普通に回答が得られました。  
ここまではグラフを活用できていません。

次にグラフを活用した検索を行います。

グラフ検索

```
from langchain_neo4j import GraphCypherQAChain

qa = GraphCypherQAChain.from_llm(
    llm=llm,
    graph=graph,
    allow_dangerous_requests=True,
    verbose=True,
)
print(qa.invoke("Tell me the pattern of the rock dove"))
print(qa.invoke("Please tell me the type of pigeon"))
```

GraphCypherQAChainを使うと、自然言語の質問文からCypherクエリ（グラフデータベースへのクエリ）に変換して、そのクエリ結果を元に回答してくれます。

出力結果

```
> Entering new GraphCypherQAChain chain...
Generated Cypher:
MATCH p=(b:Bird)-[r*..]->(c:Characteristic) WHERE b.id = 'rock dove' RETURN p

Full Context:
[{'p': [{'id': 'rock dove'}, 'HAS_CHARACTERISTIC', {'id': 'purplish gradient on its neck'}]}]

> Finished chain.
{'query': 'Tell me the pattern of the rock dove', 'result': 'The rock dove has a purplish gradient on its neck.\n'}


> Entering new GraphCypherQAChain chain...
Generated Cypher:
MATCH p=(b:Bird)-[:IS_A]->(a:Animal) WHERE b.id CONTAINS 'pigeon' RETURN a.id, labels(a)

Full Context:
[]

> Finished chain.
{'query': 'Please tell me the type of pigeon', 'result': "I don't know the answer.\n"}
```

出力結果から、Cypherクエリに変換している過程と結果が見えます。  
1つ目の質問には回答できましたが、2つ目の質問には答えられませんでした。  
何回か実行してみましたが、かなり不安定で、1つ目にも答えられないことがあります。

試してみて、グラフ検索には難所が2つあることがわかりました。

- LLMによるグラフ構築の運
- LLMによるCypherクエリの運

LLMによるグラフ構築には、実行するたびに構築されるグラフが変わりますが、いかに良い表現でグラフにできるかが重要になってきます。  
あえてグラフをリセットせずに、複数回グラフ構築を行った結果に対してクエリを投げたところ、回答できる確率が上がったので、グラフ構築部分の段階でいろんな表現パターンで構築しておくことが、クエリに回答できる確率を上げる行為だと言えます。  
後者のクエリ生成を頑張る可能性もあります。  
GraphCypherQAChainには、プロンプトも渡せるので、いかにうまいCypherクエリを生成できるかのプロンプトゲーになりそうです。

今回は「ハイブリッド検索（全文検索とベクトル検索）」と「グラフ検索」をそれぞれ独立に実行しましたが、実際にはこれらの検索結果をコンテキストとして回答を作ることになります。

なお、Microsoftが[GraphRAG](https://github.com/microsoft/graphrag)ライブラリを公開したのですが、OpenAI以外での使い方がわからなかったので、試すことができませんでした。  
[こちら](https://www.chowagiken.co.jp/blog/graphrag_langchain)の結果を見てみると、かなり良さそうなので、OpenAIのAPIが使える方はこちらから試すと良いと思います。

参考:

- [GraphRAGをわかりやすく解説](https://qiita.com/ksonoda/items/98a6607f31d0bbb237ef)
- [LLMによるナレッジグラフの作成とハイブリッド検索 + RAG](https://zenn.dev/yumefuku/articles/llm-neo4j-hybrid)
- [Connect to a Neo4j DBMS](https://neo4j.com/docs/browser-manual/current/operations/dbms-connection/)
- [Neo4j Vector Index](https://python.langchain.com/docs/integrations/vectorstores/neo4jvector/)
- [Microsoft「GraphRAG」とLangchainの知識グラフを活用したRAGを比較](https://www.chowagiken.co.jp/blog/graphrag_langchain)

### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E6%9C%80%E8%BF%91%E5%A2%97%E3%81%88%E3%81%A6%E3%81%84%E3%82%8Bai-agent)最近増えているAI Agent

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#pydanticai-%E5%9E%8B%E3%81%AB%E5%BC%B7%E3%81%84)PydanticAI: 型に強い

[PydanticAI](https://ai.pydantic.dev/)は、AI Agentを書けるライブラリです。  
ドキュメントを見つつ軽く使ってみた感想を書きます。

- [型安全性](https://ai.pydantic.dev/agents/#static-type-checking)・[修復](https://ai.pydantic.dev/agents/#reflection-and-self-correction)
    
    - Agentの入出力に型を定義できる
    - エラー時に修正するようリトライ設定できる
        - 「出力結果を型に沿うよう修正する」作業のリトライ
            - LangChainでいうところの`OutputFixingParser`
        - 定義した関数（ツール）内でエラーをraiseすることによるツール単位のリトライ
            - ツール内で`raise ModelRetry("もっと情報よこせ")`みたいなことができる
    - mypyが使える（ソースコード時点の型チェック）
- [モデルに依存しない](https://ai.pydantic.dev/models/)について。
    
    - `Agent("gemini-1.5-flash")`や`Agent("openai:gpt-4o")`というように文字列で指定できる
        - LangChainだとモデルを変える場合、import先を変えないといけないかった
- [ストリーミング構造化出力](https://ai.pydantic.dev/results/#streaming-structured-responses)について。
    
    - `{"name": "aaa"}`
    - `{"name": "aaa", "age": 20}`
    - `{"name": "aaa", "age": 20, "like": "bbb"}`
    
    みたいなことができる。これは驚き。いつ使うんだろう。
    
- 比較的短いコードで書ける
    
    ユーザーと対話するAI
    
    ```
    from pydantic_ai import Agent
    
    agent = Agent("gemini-1.5-flash")
    messages = []
    while True:  # Ctrl+Cで終了してください
        result = agent.run_sync(input("USER: "), message_history=messages)
        print("AI: ", result.data)
        messages = result.all_messages()
    ```
    

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#ag2-autogen-%E3%83%9E%E3%83%AB%E3%83%81%E3%82%A8%E3%83%BC%E3%82%B8%E3%82%A7%E3%83%B3%E3%83%88)AG2 (AutoGen): マルチエージェント

[AG2 (AutoGen)](https://docs.ag2.ai/docs/Home)は、マルチエージェントの処理を書きやすいライブラリです。

- `pip install ag2[gemini]`

AI同士に漫才をさせてみました。

2人のエージェントに漫才をさせる

```
import os
from autogen import ConversableAgent

takeshi = ConversableAgent(
    "たけし",
    system_message="あなたの名前はたけしです。漫才師のボケです。漫才をしてください。",
    llm_config={
        "config_list": [
            {
                "model": "gemini-1.5-flash",
                "api_key": os.environ.get("GOOGLE_API_KEY"),
                "api_type": "google",
            }
        ]
    },
    human_input_mode="NEVER", # 人間が介入しない設定
    is_termination_msg=lambda msg: "もういいぜ" in msg["content"], # 終了条件
)

mikio = ConversableAgent(
    "みきお",
    system_message="あなたの名前はみきおです。漫才師のツッコミです。漫才をしてください。5回会話のリレーをしたら「もういいぜ」と言って終わらせてください。",
    llm_config={
        "config_list": [
            {
                "model": "gemini-1.5-flash",
                "api_key": os.environ.get("GOOGLE_API_KEY"),
                "api_type": "google",
            }
        ]
    },
    human_input_mode="NEVER", # 人間が介入しない設定
)
# 2人のAIエージェントを紐づける
result = takeshi.initiate_chat(
    mikio, message="この間コンビニに行ったんだけどさ。", # takeshiに最初の会話を付与
    max_turns=10 # 最大ターン数で無限ループにならないようにする
)
```

謎の漫才ができました。

出力結果

```
たけし (to みきお):

この間コンビニに行ったんだけどさ。
--------------------------------------------------------------------------------
みきお (to たけし):

へぇー、コンビニかぁ。何買ったの？宝くじでも買ったの？当たってたら今頃ハワイで優雅に過ごしてるんじゃねえの？
--------------------------------------------------------------------------------
たけし (to みきお):

いやいやいや！宝くじは買ってないよ！だってさ、店員さんに「宝くじありますか？」って聞いたらさ、「ありますけど…　**あなた に当たる確率の方が低いですよ**」って言われたんだもん！  ショックで、結局、かき氷と、賞味期限切れ間近のバナナミルクしか買わなかったよ！
--------------------------------------------------------------------------------
みきお (to たけし):

はっはっは！  店員さん、毒舌すぎだろ！  客商売としてどうなの！？  それよりさ、賞味期限切れ間近のバナナミルク…　飲むの勇 気いるな！  もしかして、すでに…　変な味がしたとか？
--------------------------------------------------------------------------------
たけし (to みきお):

いやいや、大丈夫！  だってさ、**賞味期限が「間近」って書いてあっただけで、「切れ」って書いてなかったんだもん！**  まだイケる！…多分…  ちょっとヨーグルトっぽい酸味があったけどさ！  うん、ヨーグルト風味のバナナミルクってことで！  ポジティブに考えるんだ！
--------------------------------------------------------------------------------
みきお (to たけし):

ポジティブ思考もいいけどさ、胃腸が丈夫じゃないと危険だよ！  ヨーグルト風味のバナナミルク…　想像しただけでちょっとゾッと するわ。  もういいぜ。
--------------------------------------------------------------------------------
```

会話の保存設定を自分で書かなくてよいので、シミュレーションするのに適してますね。

マルチエージェントを得意とするライブラリとして[CrewAI](https://docs.crewai.com/introduction)もあります。  
きりが無いので今回は省略します。

#### [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#browser-use-%E3%83%96%E3%83%A9%E3%82%A6%E3%82%B6%E6%93%8D%E4%BD%9C)browser-use: ブラウザ操作

[browser-use](https://docs.browser-use.com/introduction)は、ブラウザを操作できるエージェントです。

※python3.11以上が必要です。

- `pip install browser-use`
- `playwright install`

browser-use

```
import os
import asyncio
from langchain_google_genai import ChatGoogleGenerativeAI
from browser_use import Agent

async def main():
    agent = Agent(
        task="Qiitaのユーザー@birdwatcherについて、人気の記事を教えてください。",
        llm=ChatGoogleGenerativeAI(model="gemini-1.5-flash"),
    )
    result = await agent.run()
    print(result)

asyncio.run(main())
```

勝手にブラウザが開いて情報取得する様子を確認できました。  
[![browser-use.gif](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb74fb243-1ea2-d3db-4fff-292c716e5187.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e7263376094ee04e333eb754a4f8d416)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F294661%2Fb74fb243-1ea2-d3db-4fff-292c716e5187.gif?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=e7263376094ee04e333eb754a4f8d416)

仕組みはこちらの記事にまとめられていました。

- [browser-useの基礎理解](https://zenn.dev/gunjo/articles/8450e69537dbb6)

# [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E3%81%82%E3%81%A8%E3%81%8C%E3%81%8D)あとがき

最近の生成AIの技術のスピードが凄まじく、キャッチアップが大変でした。  
そして、記事を書いている最中にも次々と新しい技術が出てきて、まとめるのが難しかったです。  
技術の流れと同時にライブラリのバージョンアップもあり、langchainは非推奨のWarningを解消するのが大変でした。

LLMが発展し、ユーザーへの自然な応答が現実的になったことにより、できることの幅が広がりました。  
今後も驚くような技術が登場することでしょう。  
LLMと何かを組み合わせると、驚くようなユーザーの体験を実現できます。  
たとえば、LLMと音声合成を組み合わせると、リアルタイム音声対話ができますよね。  
今回紹介しませんでしたが、OpenAIからRealtime APIが出てきたり、Cotomoも有名ですね。

なお、生成AI系の最新情報を手に入れるのはX（Twitter）がおすすめです。  
情報収集用のXアカウントを作って、生成AI系のPostをしているユーザーをフォローしまくりましょう。

まだ読んでいないけど、勉強になりそうな資料：

- [Kaggle's 5-Day Gen AI Intensive Course](https://www.kaggle.com/discussions/general/545082)

## [](https://qiita.com/birdwatcher/items/b5cc66ce59095dee5625#%E7%B4%B9%E4%BB%8B%E3%81%A7%E3%81%8D%E3%81%AA%E3%81%8B%E3%81%A3%E3%81%9F%E3%82%82%E3%81%AE)紹介できなかったもの

ローカルPCや[Google Colaboratory](https://colab.research.google.com/?hl=ja)上でも実行できなかったものたち。  
ディスク容量不足やメモリ不足で落ちました。

- [BLIP2](https://github.com/salesforce/LAVIS/tree/main/projects/blip2)
    - Vision-Language Model
    - できること
        - キャプション生成
        - VQA（Visual Question Answering）
            - 画像と質問を入力すると、その画像に基づいた質問への回答を生成
            - 例: 画像を見て「この車は何色ですか？」と質問すると「赤色です」と回答
        - 画像検索(テキストから関連する画像を検索したり、画像から関連するテキストを検索)
    - `pip install salesforce-lavis`
        
        BLIP2によるVQAの例(未検証)
        
        ```
        import torch
        from PIL import Image
        device = torch.device("cuda") if torch.cuda.is_available() else "cpu"
        raw_image = Image.open("ducks_image.jpg").convert("RGB")
        
        import torch
        from lavis.models import load_model_and_preprocess
        model, vis_processors, _ = load_model_and_preprocess(name="blip2_t5", model_type="pretrain_flant5xxl", is_eval=True, device=device)
        image = vis_processors["eval"](raw_image).unsqueeze(0).to(device)
        
        model.generate({"image": image, "prompt": "Question: how many ducks are there? Answer:"})
        ```
        
- [LLaVA-NeXT](https://github.com/LLaVA-VL/LLaVA-NeXT)
    - オープンソースなマルチモーダルLLM
    - ByteDance（TikTokの会社）の研究者らによって開発された