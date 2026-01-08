---
title: "BorisがXのレス内で語ったClaude Codeの使い方"
emoji: "🐿"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["ai", "claudecode", "anthropic", "codingagent", "boris"]
published: true
---

びーぐるです🐶

Claude Code界隈では、年明けから大変有益な情報が共有されました。Claude Codeの生みの親、Boris Cherny氏が自身のClaude Codeの使い方に関する13のTipsをXでポストしたためです。

このポストは世界中で大変な反響を呼び、2026年も引き続きClaude Codeの年であることを多くの人に確信させたと言えるでしょう。

また、Boris Cherny氏は大変マメな方で、「13のTips」に寄せられた質問に積極的に回答されていました。

本記事では、Boris Cherny氏が回答した内容の中から、特に有益そうな情報をまとめてみます。

# 前提知識

この記事を読む上で知っておくとよいことを先に示します。

## Boris Cherny氏とは

Boris Cherny氏は前述の通り、最初にClaude Codeを作った人です。Xのポストのほか、インタビュー形式の動画やイベントにもたびたび出演し、Claude Codeの普及に尽力されています。
昨年12月22日に行われた[Claude Code Meetup Tokyo(@aimeetup)](https://luma.com/ioed4ksj?tk=U0Wvfo)にビデオ通話でご出演され、会場からの質問にご回答いただけました！

この様子をとりしまさんがまとめているので、ご紹介いたします。
https://x.com/iotorishima/status/2003172156134556027

また、ここからは親しみを込めて**Boris**と呼ばせていただきます。

## BorisのXのポストとその翻訳 / 解釈

Borisの元のXのポスト(Claude Codeの使い方に関する13のTips)はこちらです

https://x.com/bcherny/status/2007179832300581177

上記ポストを多くの方が翻訳・解説されていますので、本記事では取り上げませんが、参考になった解説をご紹介いたします。

個人的に最も参考になったのがモヒにゃぱんさんのこちらのzennの記事で、大変見やすくまとまっております。

https://zenn.dev/mohy_nyapan/articles/a07975837386f7

また、[MLBear Times](https://www.ml-bear-times.com/)を運営されているML_BearさんのXのポストも大変参考になりましたので、合わせてご覧いただくと理解が深まるかと思います。

https://x.com/MLBear2/status/2007231818395980252


# Borisの回答 -Claude Code実用編-

Claude Codeの設定や機能、運用について回答しているものを集めました。

## 実際にCLAUDE.mdには何を書いている？

https://x.com/bcherny/status/2007212366094811401

- 一般的なbashコマンド(恐らくgit, npm, uvやテストフレームワークのコマンド)
- コーディング規約
- UI、コンテンツのデザインガイドライン
- 状態管理、ログ、エラーハンドリング、権限チェック、デバッグについてのやり方
- プルリクエストのテンプレート

これらを合わせて**2.5kトークン程度**の分量で記述されているとのことです。

## セッション開始の起点は？

https://x.com/bcherny/status/2007187796453449925

最初はモバイルやWebでセッションを開始し、必要に応じて(例えばPlanモードを使う必要があれば)ローカルのCLIにセッションを飛ばすという方法をとっているようです。

## 並列セッションの具体的な方法は？

https://x.com/bcherny/status/2007191678940238012

各エージェントがそれぞれgit checkout(cloneした作業ディレクトリ)を持ち、それぞれのセッションが独立にPlan + Tasksを実行しています。git worktreeではなく、並列の分だけCloneするということです。

https://x.com/bcherny/status/2007182181375086707

## Skillsについての言及がないのはなぜ？

https://x.com/bcherny/status/2007200163883036738

BorisはSkillsとSlashコマンドは交換可能であるという認識です。

また、後にSlashコマンドでSkillを発動できるようになるアップデートが予定されているようです。

https://x.com/bcherny/status/2007236881122312552

これで**SkillsとSlashコマンドは同義である**と言えるようになります。

## Compactのベストタイミングは？

https://x.com/bcherny/status/2007295481865801946

タイミングはClaudeに任せているとの回答。13のTipsの冒頭で発言していた「Claude Codeはカスタマイズせずともそのままでも使える」を体現した回答です。

## "ultrathink"を使っているか？

https://x.com/bcherny/status/2007218184345243962

Thinkingモードをonにしているので使っていない、との回答。Thinkingがonだとあまり意味がない機能、という旨の発言もありました。

ここで現状のultrathinkモードがどうなっているか⋯という疑問が湧きまして、Xでお聞きしたところ調査していただけました！ありがとうございます！

https://x.com/Ka888aa/status/2007428567047581883

https://x.com/nyapan_mohy/status/2007427304444637485

## 品質検証のためのフィードバックループのコツは？

https://x.com/bcherny/status/2007202802154745888

13tipsの13番目の内容の掘り下げです。シンプルにClaudeに「出力結果を確認できるツール」を与え、それを「いつ・どんな時に使うべきか」を理解できるようにするだけでよくて、あとはClaudeがなんとかしてくれるということです。
テストツールとその正しい使い方を教えておけば、あとはClaudeがテストを実行し、その結果エラーが出たら修正を行う⋯このフィードバックループによって品質が向上します。

## ターミナル名自動生成の無効化方法

https://x.com/bcherny/status/2007957770725949686

環境変数に`CLAUDE_CODE_DISABLE_TERMINAL_TITLE=1`を設定するとよいとのことです。
ターミナルの名前を自分でつけたい人向けの機能です。


## サブエージェントでコンテキスト圧縮は可能か？

https://x.com/bcherny/status/2007210843633840517

「可能だが、圧縮が終わるまで待つ必要がある」という回答です。
ある一定のコンテキスト分量になった際に/compactを発動するサブエージェントを実装すること自体は可能です。しかし、結局圧縮中はメインのエージェントも待たされるわけで⋯これはあまり意味のない機能である、という説明かと思います。

また、コンテキスト圧縮はとてもややこしい問題なので、これに関するブログの記事を書くつもりであるとのことです。

# Borisの回答 -その他-

Claude Code本体以外の話題もありました

## CLIの作業が終わるとスマートフォンへ通知する機能の要望

https://x.com/bcherny/status/2007894828819063225

Good idea!と回答されているので、近々実装されるかもしれません。

なお、ナル先生氏はすでに「Apple Watchで承認できるアプリ」をClaude Codeを利用して作られています。

https://x.com/GOROman/status/2003057993500475674


## Borisは他社コーディングエージェントを利用しているか？

https://x.com/bcherny/status/2007190737721668085

答えはNo、しかも彼が率いるClaude Codeの開発チームに対しても「他社のコーディングツールを見て気を散らすな」的なことを言っています。

真に必要だと思う機能に焦点を当て実装するこだわりが垣間見えるエピソードです。

https://x.com/oikon48/status/2007797745269297200

## Borisの使っているPCは？

https://x.com/bcherny/status/2007218725754442106

a regular macbook と答えています。Claude CodeをiTerm2一つで動かしているため、特別なスペックのPCは必要ないということでしょう。


# Borisの冬休みの活動

なお、BorisのポストのうちClaude Codeの機能拡張及びバグ修正に関する内容については、Oikonさんがまとめています。近日中にこんなにも多くの機能の実装や修正が行われるようです。冬休みとは⋯

https://x.com/oikon48/status/2008125761128144965


# まとめ

BorisとClaude Codeチームに最大限の敬意を表したいと思います。

# 参考文献

https://x.com/bcherny