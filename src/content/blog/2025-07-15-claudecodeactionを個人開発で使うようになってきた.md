---
title: ClaudeCodeActionを個人開発で使うようになってきた
pubDate: 2025-07-15T03:41:03.671Z
updatedDate: 2025-07-15T03:41:03.671Z
fmContentType: blog
description: ClaudeCodeActionを個人開発で使うようになってきた
heroImage: /ClaudeCode画像.jpeg
---

# Claude Code Actionで個人開発を加速させる - IssueとPRの効果的な活用術

## はじめに

この記事では、個人開発プロジェクトでAIツール「Claude Code Action」をどのように活用し、開発プロセスを改善しているかについて、具体的な事例を交えて紹介します。

AIによるコード生成は非常に強力ですが、そのまま使うだけでは意図しない結果を招くこともあります。そこで重要になるのが、開発ワークフローの中核であるIssueとPull Requestの場面で、AIをいかにうまく活用するかです。

AIを単なる作業者としてではなく、開発をサポートしてくれる「パートナー」として扱うための、私の実践方法を解説します。

## Issue活用法：AIと協力して実装プランを練り上げる

いきなりAIに「この機能を作って」と指示すると、自分が思っていたよりも大規模なPRができてしまい、レビューやマージが困難になるケースがあります。

この問題を避けるため、私は「実装前のIssueでAIと実装プランを練り上げる」というステップを設けています。

### 実装プランニングのワークフロー

具体的な流れは、以下の3ステップです。

1. **Issueを作成する**: まず、実装したい機能の要件をIssueに書き出す。ここでは詳細な仕様書は必要なく、概要が分かれば十分。

2. **AIにプラン作成を依頼する**: 次に、あらかじめ用意しておいたプロンプトを使い、`@claude`メンションでAIを呼び出す。Issueの要件を元に、技術仕様の調査や具体的な実装計画の作成を指示する。

3. **プランをレビューし、調整する**: AIが提案した実装プランを人間が確認する。もし計画が大きすぎると判断すれば、この段階でIssueを分割するなどして、タスクのスコープを調整する。

このプロセスを経ることで、変更の全体像を把握でき、手戻りを減らすことができます。結果として、1つ1つのPRが管理しやすいサイズに保たれ、開発プロセス全体がスムーズに進みます。

### 指示に使うプロンプトテンプレート

AIへの指示には、構造を理解しやすいとされるXML形式のプロンプトを使っています。これは、ベースとなる指示を考えた後、GeminiなどのLLMに手伝ってもらってXMLテンプレート化したものです。

```xml
@claude

<prompt>
  <system_instruction>
    あなたは経験豊富なソフトウェアエンジニアとして、このissueを解決するための実装プランを提案する役割を担っています。以下の指示に厳密に従い、詳細かつ実行可能なプランを作成してください。
  </system_instruction>

  <objective>
    このissueの解決に向けた、具体的で実行可能な実装プランを策定し、提示すること。
  </objective>

  <process>
    <step name="1. 要件の理解 (Understand Requirements)">
      <instruction>
        issueのタイトル、本文、関連コメントを精読し、解決すべき課題と達成すべきゴールを正確にリストアップしてください。
      </instruction>
    </step>

    <step name="2. コードベースの調査 (Investigate Codebase)">
      <instruction>
        関連するドキュメントとコードベースを調査します。下記の「ソースコード調査のヒント」を参考に、変更が必要なファイルや影響範囲を特定してください。調査した結果、判明したことをコンテキストとしてまとめてください。
      </instruction>
      <tips name="ソースコード調査のヒント">
        <tip>1. 関連キーワード（機能名、APIエンドポイント、DBテーブル名など）でリポジトリ全体を検索する。</tip>
        <tip>2. 処理のエントリーポイントから順にコードを追い、主要なロジックを把握する。</tip>
        <tip>3. 変更箇所に関連するテストコードを読み、現在の仕様と期待される動作を確認する。</tip>
        <tip>4. 影響がありそうな他の機能との関連性を調べる。</tip>
      </tips>
    </step>

    <step name="3. 実装プランの提案 (Propose Implementation Plan)">
      <instruction>
        調査結果を踏まえ、実装プランを策定します。下記の項目をすべて含んだ、詳細なプランを提案してください。
      </instruction>
      <output_structure>
        <section name="思考プロセス">
          <detail>なぜこの実装アプローチを選択したのか、背景や理由を簡潔に説明してください。</detail>
        </section>
        <section name="実装TODOリスト">
          <detail>具体的な作業手順をチェックボックス形式（- [ ]）で詳細に記述してください。ファイル名の変更や、必要であれば具体的なコードの変更箇所（関数名、クラス名など）も明記してください。</detail>
          <example>
            - [ ] `src/components/feature_a/component.tsx` に新しいprops `isNewFeatureEnabled` を追加する。
            - [ ] `src/services/api/feature_a.ts` の `fetchData` 関数を修正し、新しいパラメータを渡す。
            - [ ] 上記の変更に対応するテストコードを `src/tests/feature_a.test.ts` に追加・修正する。
          </example>
        </section>
        <section name="代替案（任意）">
          <detail>もし他に有力な実装方法があれば、そのメリットとデメリットを併記してください。</detail>
        </section>
        <section name="懸念事項とリスク">
          <detail>この実装によって予期される問題点、パフォーマンスへの影響、後方互換性などのリスクがあれば指摘してください。</detail>
        </section>
      </output_structure>
    </step>
  </process>

  <final_instruction>
    上記のすべてのステップと思考を経て、最終的な実装プランを単一のMarkdownコメントとして出力してください。
  </final_instruction>
</prompt>
```

## PR活用法：AIレビューでコードの品質を高める

個人開発では、自分以外の視点でコードをチェックする機会がなかなかありません。Claude Code ActionのPRレビュー機能は、この課題に対する強力な解決策になります。

PRを作成・更新すると、AIが設定ファイル（`claude.md`など）に書かれたプロジェクト独自のルールに基づき、コードレビューを自動で行ってくれます。これにより、自分では気づきにくいコーディング規約の違反や、設計上の問題点を客観的に指摘してもらえます。

このAIレビューを開発サイクルに取り入れることで、次のようなメリットが生まれます。

- 品質の向上：統一された基準でコードがチェックされるため、プロジェクト全体の品質が安定する。
- 判断コストの削減：レビューでの指摘を元に修正すべきかどうかの判断がしやすくなり、開発の迷いが減る。
- 開発のスピードアップ：レビューの一部が自動化されることで、開発サイクルがより速く回るようになる。

## 使いどころを考える：「IssueからのPR直接生成」について

便利な機能がある一方で、自分の開発スタイルに合わないものもあります。例えば、「Issueから直接PRを作成する」機能は、現在の私のワークフローではあまり活用していません。

これは、私が特定のコマンドを起点に、決められた手順で開発を進めることを好んでいるためです。しかし、この機能が役立たないというわけではありません。

例えば、エラー監視システムと連携させて、検知したエラーに対する一次対応のPRを自動で作成する仕組みなどには有効だと考えています。今後はこういった自動化での活用を検討していく予定です。