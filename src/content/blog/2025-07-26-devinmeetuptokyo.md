---
title: DevinMeetupTokyo
pubDate: 2025-07-26T03:49:21.478Z
updatedDate: 2025-07-26T03:49:21.479Z
fmContentType: blog
description: DevinMeetupTokyo_2025_07_26参加記録
heroImage: /Devin.webp
---

## 参加したイベント

https://aiau.connpass.com/event/357271/

## 	少人数でも回る！DevinとPlaybookで支える運用改善のメモ

- 課題：約40のマイクロサービスを少人数で運用
    - バージョンアップ作業が大きな負担となる
        - Renovateとかの自動更新の構築や運用もコストがかかる
        - 作業は単純だけど時間がかかる
- 課題：インフラ構成が複雑で作業が属人化する
- Devinの導入とPlayBookで解決
    - 2月に数名で検証して、5月で全Engに整備展開
    - KnowledgeとPlaybookが導入の決めて
- Knowledge機能：コードベースや組織固有のルールを覚える機能
    - より少ない指示でタスクがこなせるようになる
    - Devinのサービス側に蓄積するため、**リポジトリを横断して参照可能**
- Playbook機能：定型タスクの手順書
    - インフラ対応振り返り用のIssueを自動生成しDevinが参照できるようにしている。
        - コンテキストとしての利用を期待
    - K8sのサーバー増強作業に半日かかっていたのが、15-30分に短縮された
- バージョンアップ作業は10人 x 1ヶ月 → 1人 x 1週間に短縮
- 質問：自立型AIが本番環境をいじっている恐怖感はないのか？
    - Yamlで管理しているため、人間が確認を入れている。
    - レビューを入れることで防ぐ
- 質問：新規リポジトリの構築などお願いできるレベルですか？
    - テンプレを用意してあげればある程度なんでもできる。
- 質問：Devinを導入するまでに検討したことは？
    - 活用ガイドラインが社内に存在している
        - セキュリティ面など
        - 学習に使われないことを確認

## マルチテナントSaaSでのRLS導入に際し、並列でDevinを動かし高い品質のコードをパワフルにプロダクションへ反映させた事例のメモ
- Devinは並列・体量の作業に相性が良い
    - 指示に対してPR単位で成果物をまとめてくれる
    - 複数セッションとして同時に立ち上げができる
    - 推論能力と推進力が高くて個々のケースで自己解決してやり切る力を持っている
- 流れ
    - 設計・実装方針を立てる
        - ADRにまとめて明文化
        - git管理している
    - モデルケースを作る
        - マージできる品質のものを作っておく
        - プロンプトの改善にもつながる
        - モデルケースを作成したセッションでプロンプトの作成依頼を行う
            - 議論の背景もコンテキストに含める
    - Devinに依頼してドラフトPRを作る
    - 人力で確認してPRをOpenにする
        - Devinへの依頼者＋レビュワーのApproveが必要な運用ルールを敷いている
- Devinの稼働に穴が開かないように次のタスクを用意する
    - Devinのタスクが途切れないように調査設計指示を出し続ける

## コードを書かせないDevinの使い方のメモ

- Ask DevinはACUの対象外でコスパが良い
    - 基盤モデルのバージョン追従やrepoの検索機能を提供してくれる
    - Slack連携もついてくるので便利
- Devinにドキュメントの素案を書かせる
    - Technical WriterとEngneer間で責任を分担する
        - 技術的な整合性に責任をおうEngneer
        - Docs全体の構成に責任をおうTechnical Writer
    - 表記揺れを解消できる
- うまくいかなかった試み: Documentのシナリオテスト
    - Devinにドキュメントとシナリオを与えてタスクをとくテスト
        - シナリオを考える負担が大きい
- Onboarding
    - AskDevinでrepoを跨いで質問ができる
    - エラーログを渡した時にreporting能力が格段に向上する
    - infraとpaltformのrepoを跨いだ検索ができる
    - 本番とlocalの環境変数の違いがわかるようになる
- 権限管理は問題になる
    - Enterpriseプランが必要になる（500万〜2500万とからしい）
    - Orgレベルの管理など
- 最近MCP連携がリリースされた
    - Notionとかデフォで用意されている
    - 社内MCP Toolを配れる
        - ここにもKnowledgeが必要になってくるかも

### 自分の感想（セッション中に思ったメモ）
- Ask DevinがACU外なのは自社導入のアピールに有効そう（言えばよかった）
- DevinにDocumentの素案を書かせて、PdMとEngineer間で責任を分担する仕組みは提案できるかもしれない。
- infraのリポジトリとアプリケーション側のリポジトリの関係性をDevinが理解できるようになればAsk Devinがより強力になりそう。
    - Documentやknowledgeのメンテが必要そう
- 既存仕様を直接Ask repoできるなら権限を渡せるなら自社の利用者全員に配ればPdMの仕様説明の負担が減るのか？
- Devinが育ってきた時に権限管理が悩みの種になったりするのだろうか
- 社内MCPツールを配る用途にDevinのMCP連携が一役買ってくれるかも
    - 絶対に検証しておきたい
    - Notionの情報蓄積を有効活用できるかも

## Devinで変えるエンプラシステム開発の未来のメモ
- エンプラあるある
    - 10、20年当たり前で技術が陳腐化
    - 複雑でモノリスでドキュメントがなく止められない
- コードを元に設計書をリバースエンジニアリング
    - 欲しい設計書にするために追加の指示を行って仕上げる
    - 開発者向け情報のためにDeepWikiを使用する
- 組織醸成
    - AgentFirst, AgentOps等のマインドやカルチャー醸成
    - 活用する上での共通的な考え方
- DevinはExcelを直接読むことができる
- 画面仕様書とは相性が良くない
    - Figma MCPに期待

### 自分の感想（セッション中に思ったメモ）
- Devinがスプレッドシートに書かれたドキュメントを読んで理解することができるならドキュメント作成に役にたつかも

## コンテキストを制するものがDevinを制すのメモ
- Ask Devin
    - 実装方針の相談
    - エラーの原因調査
    - 機能追加の詳細検討
    - 複数のリポジトリを横断検索
- Ask DevinのTips
    - まず実装状況を聞く
    - Ask Devinでタスクを投げる
        - プロンプトを作れる
- Devin Spaces(Beta)
    - 外部ツール起点のコンテキスト拡充
    - LinearとDevinを繋げると有効になる
    - 外部ツールからアクセスされて自動調査を開始し、リアルタイムで閲覧と介入ができる
    - Devinにどのタスクを任せられそうなのかわかりやすい


### 自分の感想（セッション中に思ったメモ）
- Devin Spacesは初めて知った
    - JiraかLinerに繋がないといけないのか...