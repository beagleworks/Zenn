---
title: "意外と知名度の低いWindowsのパッケージマネージャ「WinGet」について"
emoji: "🐂"
type: "tech"
topics:
  - "windows"
  - "winget"
  - "windows11"
  - "パッケージマネージャ"
published: true
published_at: "2025-05-22 12:51"
---

びーぐるです🐶

パッケージマネージャといえば、Linuxでは**apt**や**yum(dnf)**、MacOSでは**Homebrew**を思い浮かべる方が多いかと思います。
実はWindows11にも標準で**WinGet**というOSSのパッケージマネージャが搭載されています。
ただし、まだまだ知名度は低く・・・
今日であっても「Windowsには(CLIの)パッケージマネージャがないから辛い」と言われてしまうような状況ですので、WinGetについて整理しながら紹介してみます。

## WinGetの基本知識
ここではWindowsしか触れたことのない方にもわかるように説明します

### WinGet?
正式には[Windows Package Manager](https://github.com/microsoft/winget-cli)というようです

### CLI? パッケージマネージャ？
PowerShellからソフトウェア（アプリ）をまとめて管理する仕組みのことです。
GUIの **設定 - インストールされているアプリ** のようなものがPowerShell上で動くという感覚かと思います。

### 何が良いの？
ソフトウェアの管理、特にアップグレードが簡単になります。
現状であれば
各ソフトウェアを起動 → 自動アップデート(もしくは配布場所を訪問しダウンロード→インストーラーを起動…etc)
と手間がかかります
WinGetだとPowerShellを起動して
```
winget upgrade
```
でアップグレードがあるソフトウェアのリストを取得し
```
winget upgrade <パッケージ名>
```
でアップグレードが出来ます。
全てアップグレードしたければ **winget upgrade --all** でいけます。

### どうやって入れるの？もう入ってる？
最新のWindows11, Windows10には入っているようです。
ない場合、Microsoft Storeから「アプリ インストーラー」をインストールすることで利用可能になります。

## WinGetの主なコマンド
Linuxのaptコマンドと比較してみます。

| 操作内容              | WinGet コマンド                 | apt コマンド                      |
| :---------------- | :-------------------------- | :---------------------------- |
| パッケージのインストール      | `winget install <パッケージ名>`   | `apt install <パッケージ名>`   |
| パッケージのアンインストール    | `winget uninstall <パッケージ名>` | `apt remove <パッケージ名>`<br>`apt purge <パッケージ名>`|
| インストール済みパッケージ一覧   | `winget list`               | `apt list --installed`        |
| パッケージのアップグレード     | `winget upgrade <パッケージ名>`   | `apt upgrade <パッケージ名>`|
| すべてのパッケージをアップグレード | `winget upgrade --all`      | `apt upgrade`            |
| パッケージの検索          | `winget search <パッケージ名>`    | `apt search <パッケージ名>`         |
| パッケージ情報の表示        | `winget show <パッケージ名>`      | `apt show <パッケージ名>`           |
| パッケージのソース追加       | `winget source add <ソース>`     | `add-apt-repository <ソース>` |
| パッケージデータベースの更新| なし（自動更新）| `apt update`|

ほぼaptの感覚で使えます。
WinGetは常に最新のソースを取得しているため、updateは必要ないです。

## 使い方と注意

### 管理者権限
管理者権限がないとパッケージの操作に失敗する場合があります。
環境が許されるのであれば、PowerShellを管理者権限で実行するのが良いです。

### インストール例 Git
パッケージのインストールは、基本的には上記コマンド表のように
`winget install <パッケージ名>`で問題ありません。
Gitに関しても
`winget install git`
としたいところですが
```
複数のパッケージが入力条件に一致しました。入力内容を修正してください。
名前    ID            ソース
-----------------------------
GitVine 9P3BLC2GW78W  msstore
Git     Git.Git       winget
Git     Microsoft.Git winget
```
と言われてしまいます。このように複数のパッケージが対象となるものは
`winget install --id Git.Git`
とインストールするパッケージのIDを指定します。

### アップグレード例
上記コマンド例に従ってMySQL Workbench 8.0 CEをアップグレードします。
パッケージ名にスペースが含まれているので、ダブルクォーテーションで囲い
`winget upgrade "MySQL Workbench 8.0 CE"`
とします。
```
見つかりました MySQL Workbench 8.0 CE [Oracle.MySQLWorkbench] バージョン 8.0.41
このアプリケーションは所有者からライセンス供与されます。
Microsoft はサードパーティのパッケージに対して責任を負わず、ライセンスも付与しません。
このパッケージには次の依存関係が必要です:
  - パッケージ
      Microsoft.VCRedist.2015+.x64
ダウンロード中 https://cdn.mysql.com//Downloads/MySQLGUITools/mysql-workbench-community-8.0.41-winx64.msi
  ██████████████████████████████  42.0 MB / 42.0 MB
インストーラーハッシュが正常に検証されました
パッケージのインストールを開始しています...
インストールが完了しました
```

無事にアップグレードが完了しました。

### winget list
`winget list`で表示されるパッケージは、WinGet以外の方法でインストールしたものも含まれます。
それらもWinGetからアップグレードやアンインストールができます(一部できないもの、正常に動作しないものがあります)。

### winget installでポータブル版が選ばれる
VSCodeなど、一部のパッケージは`winget install`でポータブル版がインストールされます。
ポータブル版でも動作に問題ない場合もありますが、パスが自動で登録されない、右クリックメニューに自動で追加されないなどのデメリットがあります。
このあたりの挙動が問題になりそうなパッケージの場合、初回は手動でインストールするのが良いかもしれません。

### exportとimport
コマンド表では紹介しませんでしたが、WinGetにはexportコマンドとimportコマンドがあります。
`winget export <ファイル名>`
で現在インストール済みのパッケージの一覧をファイルに書き出し
`winget import <ファイル名>`
でパッケージリストを読み込み順次インストールできます。
export/importは、PCの引っ越しや複数のWindows環境の統一などに活用できます。
様々なオプションがあるため公式GitHubの([export](https://github.com/MicrosoftDocs/windows-dev-docs/blob/docs/hub/package-manager/winget/export.md), [import](https://github.com/MicrosoftDocs/windows-dev-docs/blob/docs/hub/package-manager/winget/import.md))ドキュメントも参照しながら使うのが良いかと思います。

## サードパーティーのWindows用パッケージマネージャ
実は昔から[Chocolatey](https://chocolatey.org/)や[Scoop](https://github.com/ScoopInstaller/Scoop)といったパッケージマネージャも存在しています。
WinGetができる前は私もScoopを利用していました。
Microsoftの管理下にあることにこだわりがなければ、今であってもこちらを選ぶのもアリでしょう。

## まとめ
WindowsでもCLIだけでパッケージ管理が完結！とはなっていませんが、メジャーなものは比較的リポジトリに存在してきており、今後も対応パッケージが増えていくものと思われます。

普段WSL2に籠りっぱなしで、いざWindows側の環境整備をしたいときにWinGetで手軽にアップグレードができるのは非常に便利です。
WinGet、皆様もぜひ一度お試しください。

## 追記(2025/06/21) : バンドルソフトを付随するインストーラーについて
一部のソフトウェアでは、インストーラーがバンドルソフトのインストールを推奨してくる場合があります。このタイプのインストーラーをWinGet経由で実行すると、推奨されるバンドルソフトが自動的にインストールされてしまいます。
恐らく、デフォルトでサイレントインストール方式が選択されているためと思われます。

この挙動を避けるのはなかなか難しいものがあります。
初めてインストールする開発元のパッケージであったり、あらかじめインストーラーの動作を把握している場合であれば、wingetコマンド実行時に`-i`オプションを付けてインストーラーの操作を手動で行うと良いかと思います。

※ 私のPCにいつの間にかスパイウェア疑惑のある悪名高き某アプリが混入していたため、原因を特定し、注意喚起のために書きました
