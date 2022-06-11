# Git Lecture

ここはGitの講義用のページになります。
講義はハンズオン形式で行われます。
なるべくテキストをシンプルに保つために、このドキュメントにはGitに関する基本的な説明が省かれている箇所があるので、気になる方は講演者から説明を受けてください。

## 目次

- [受講対象者](#受講対象者)
- [前提条件](#前提条件)
- [備考](#備考)
- [個人的によく使うGitコマンド](#個人的によく使うgitコマンド)
- [Chapter 1: ローカルリポジトリの作成](#chapter-1-ローカルリポジトリの作成)
- [Chapter 2: ブランチの作成](#chapter-2-ブランチの作成)
- [Chapter 3: Commit(コミット)履歴の作成](#chapter-3-commitコミット履歴の作成)
- [Chapter 4: リモートリポジトリへのプッシュ](#chapter-4-リモートリポジトリへのプッシュ)
- [Chapter 5: 変更をプルする](#chapter-5-変更をプルする)
- [Chapter 6: プルリクエスト(MR)の作成](#chapter-6-プルリクエストmrの作成)
- [Chapter 7: マージ](#chapter-7-マージ)
- [Chapter 8: コンフリクト解決](#chapter-8-コンフリクト解決)
- [Chapter 9: おまけ1 (Revert, Reset, Rebase)](#chapter-9-おまけ1-revert-reset-rebase)
- [Chapter 10: おまけ2 (cherry-pick, stash)](#chapter-10-おまけ2-cherry-pick-stash)

## 受講対象者

- Gitによるソースコード管理をしたことがない、もしくは経験が不足していると感じている方。

## 前提条件

- 自身のコンピューター上にGit(version 2.23以上推奨)がインストールされている。

## 備考

- このドキュメントで使用されるコマンドは基本的にLinuxもしくはMacで使用されるコマンドになります。
  WindowsでWSL以外のCLIをお使いの方などは、適宜別のコマンドに置き換えて実行してください。

## 個人的によく使うGitコマンド

### ブランチの生成に関するコマンド
```sh
# ローカルにリポジトリを新規作成
git init

# ローカルにリモートリポジトリを複製する
git clone <リモートURL>
```

### ブランチの切り替えに関するコマンド
```sh
# ブランチを切り替える
git switch <ブランチ名>

# ブランチを切り替える
git checkout <ブランチ名>
```

### 設定に関するコマンド
```sh
# リポジトリに各種設定を追加する
git config <設定名>.<項目> <値>

# リポジトリにリモートリポジトリ情報を追加する
git remote add <リモート名> <リモートURL>
```

### ファイルの変更差分管理に関するコマンド
```sh
# コミットに載せるファイルを選択する
git add <ファイル名>

# 編集内容を削除する
git restore <ファイル名>

# コミットを作成する
git commit

# 現在のリポジトリの状態を表示する
git status

# ファイルの差分を表示する
git diff

# コミット履歴を表示する
git log

# 差分をstashに一時退避する
git stash
```

### リモートリポジトリとのやりとりに関するコマンド

```sh
# ローカルにリモートリポジトリを複製する
git clone <remote_url>

# リモートリポジトリのブランチに現在のブランチのコミットを追加する
git push <remote_name> <remote_branch_name>

# リモートリポジトリの状態を取得する
git fetch

# リモートリポジトリにあるブランチのコミットを現在のブランチに取り込む(fetchとmergeを同時に行う)
git pull
```

### コミット履歴の操作に関するコマンド

```sh
# 現在のブランチに他のブランチのコミットを統合する
git merge <ブランチ名>

# 他のブランチのコミットを現在のブランチに取り込む
git cherry-pick <対象コミット>

# 指定のコミットを打ち消すコミットを追加する
git revert <対象コミット>

# 指定のコミットをコミット履歴から削除する
git reset <対象コミット>

# 現在のコミット履歴を作り替える
git rebase <対象コミット or ブランチ名>
```

## Chapter 1: ローカルリポジトリの作成

Gitはファイルのバージョン管理ツールです。
Gitでの管理を開始するための最初のステップはリポジトリと呼ばれる履歴の格納庫を用意することです。
リポジトリを用意する方法は2通りあります。

1. `git init`により新規にリポジトリを作成する。
2. `git clone`によりリモートのリポジトリをローカルに複製する。

SESとして現場に入る場合、あまり新規にリポジトリを作成する機会はないと思うので、今回はリモートリポジトリを複製する方法で進めます。
まずは私がGitHubに作成したリポジトリにアクセスするために、GitHubの登録を行ってください。

### GitHubの登録方法

1. `https://github.com/` にアクセス
2. 入力用ボックスに登録用のメールアドレスを入力し、 [Sign up for GitHub] をクリック
3. 必要項目を入力し、[Create account] ボタンをクリック
4. Welcome画面が表示されるので、下のほうにスクロールして [Complete setup] をクリック
5. 入力したメールアドレス宛に確認メールが送信されているので、本文中の [Verify email address] をクリック

### SSHキー登録

次にGitHubにPCから接続するための設定を行います。
GitHubと接続する方法はいくつかあります。

1. httpsとパスワード入力による接続
2. SSH接続
3. httpsとパーソナルトークンによる接続

GitHubの推奨はhttpsとパーソナルトークンを使用した通信方法ですが、実際の開発現場ではSSHを使用するほうが多いと思います。
なので今回はSSHでの接続設定を行っていきます。

下記の手順、またはGitHub公式が公開している手順にしたがって設定を行ってください。
(GitHub公式リファレンス: [GitHub に SSH で接続する](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh))

下記はMacOSまたはLinuxを使用した際の手順になります。

1. CLIウィンドウを開く
2. 下記コマンドを実行
    ```sh
    ssh-keygen -t ed25519 -f ~/.ssh/github
    # パスフレーズを聞かれるので任意の文字列を入力し、忘れないようにしてください。

    touch ~/.ssh/config
    ```
3. ~/.ssh/configに下記を記述 (これによりgithub.comとのSSH接続時にどのキーファイルを使うかを自動で選択してくれます)
    ```
    Host github.com
      HostName github.com
      User git
      HostName github.com
      IdentityFile ~/.ssh/github
      IdentitiesOnly yes
    ```
3. 自動作成された`~/.ssh/github.pub`ファイル内のテキストを全てコピー
4. `https://github.com/settings/keys` にアクセスし [New SSH key] をクリック
5. Titleを適当に入力しKeyの入力エリアに、先程コピーしたテキストをペースト
6. [Add SSH key]をクリック
7. CLIで`ssh github.com`を実行し疎通確認 ('Hi'と返事があればOKです)

### Git Clone

次にリモートリポジトリをクローンします。
リポジトリに招待するので参加が完了したら下記を実行してください。

```sh
mkdir ~/gitlecture && cd ~/gitlecture
git clone git@github.com:Y-Hattori-8576/for_git_lecture.git .
```

上記が完了したら、次は名前とメールアドレスを`./.git/config`に追加します

```sh
git config --local user.name YOUR_NAME
git config --local user.email YOUR_EMAIL

# --local オプションは現在作業中のリポジトリにのみ適用するためのオプションです。
# このオプションがなくてもデフォルトで同様の動作になりますが、ここでは念の為明示的に指定しています。
```

次にGitで使用するエディタを設定します。
設定されたエディタは、何かしらのGitコマンド実行時にエディタ上での編集が必要になった場合に自動で立ち上がります。
エディタを指定するには引数にエディタの実行コマンドを渡してください。

```sh
git config core.editor YOUR_FAVORITE_EDITOR
# 例: git config core.editor vi
```

## Chapter 2: ブランチの作成

クローンが完了したら`git branch`コマンドを実行してみてください。
おそらく`main`というブランチのみ、存在が確認できるでしょう。
これは私がGitHubの方で設定したデフォルトブランチです。

ブランチはそれぞれ別のコミット履歴を持っているので、他のブランチに影響を与えることなく開発を行うことが可能です。
別ブランチに切り替える場合は、`git switch`を利用します。
以下のコマンドで自分用のブランチを作成しつつ、ブランチを切り替えましょう。

```sh
git switch -c test/<yourname>

# version 2.23より古いバージョンを使用している方は代わりに以下のコマンドを実行してください。
git checkout -b test/<yourname>
```

## Chapter 3: Commit(コミット)履歴の作成

Gitにコミットを追加するには以下のステップが必要です。

1. ファイルを編集する
2. `git add`でコミットしたいファイルを選択する
3. `git commit`でコミットする

まずは変更差分を発生させるために、例として以下のコマンドを実行し、`README.md`ファイルを作成してください。

```sh
echo '<yourname>勉強中' >> README.md
```

一度差分を確認してみましょう。
以下のコマンドで確認できます。

```sh
# 差分のあるファイルを確認
git status
# 差分を確認
git diff HEAD README.md
```

確認できたらコミットしていきましょう。
以下のコマンドを実行してください。

```sh
git add README.md # もしくは`git add .`で差分のあるファイル全てを自動でaddしてくれます。
git commit -m 'postscript to README.md'
```

コミットが完了したら、以下のコマンドで履歴を確認しましょう。

```sh 
git log
```

## Chapter 4: リモートリポジトリへのプッシュ

ブランチへコミットが追加できたので、それをリモート(GitHub)のリポジトリにも反映してみましょう。
以下のコマンドを実行してください

```sh
git push -u origin <your_branch_name>
```

`-u`オプションは`push`と同時にローカルブランチとリモートブランチの紐付けを行うためのオプションです。
これをすると、次回からは`git push`のみで`push`することができます。

## Chapter 5: 変更をプルする

他の人がリモートリポジトリに上げた変更を取り込む場合は`git pull`を使用します。
`pull`は内部的に、`git fetch`と`git merge`を同時に行います。
`git fetch`はリモートリポジトリの履歴情報をローカルに持ってきてくれます。
私がブランチにコミットを追加するので、それを`pull`してください。

```sh
git pull
git log
```

ログに私が追加したコミットがあれば成功です。

## Chapter 6: プルリクエスト(MR)の作成

リモートへのプッシュが完了したらプルリクエストを作成しましょう。
プルリクエストはコミットの内容・目的・実装方法をレビュワーに伝え、あなたのコードを評価してもらうために作成します。
使用するサービスによってはプルリクエストのことをMR(マージリクエスト)と呼ぶこともあります。

以下の手順でプルリクエストを作成できます。

1. GitHubで開発中のリポジトリを開き、メニューから`Pull Request`を選択
2. base(統合先)とcompare(統合元)、それぞれのブランチを選択し、`Create pull request`ボタンを押す
   (今回の場合、baseが`main`、compareが`test/<yourname>`)
3. Title, Commentを記入し、Reviewersを選択後、`Create pull request`ボタンを押す
   (今回は私をレビュワーに選択してください。)

## Chapter 7: マージ

マージは、対象ブランチへ指定ブランチのコミットを統合します。
リモートリポジトリ内でのマージはGitHubの場合、ボタンをクリックするだけで簡単に行うことができます。

1. プルリクエストに対してレビュワーから承認をもらえたら、Merge pull requestボタンを押してMergeを実行
  (この際、Merge方法を選択する必要があります。基本的には`Create a merge commit`で良いと思います)
2. `Confirm merge`ボタンを押す
3. Mergeしたブランチが不要な場合は`Delete branch`で削除

## Chapter 8: コンフリクト解決

コンフリクトは、別々のブランチで同じファイルに変更を加え、それをマージしようとした際に発生します。
解決までのステップは以下になります。

1. コンフリクトしたファイルを修正
2. `git add .`と`git commit`を実行
3. 自動でエディタが立ち上がるので、何もせずsaveして閉じる

実際にやってみましょう。
私がREADME.mdに変更を加え、それをリモートにプッシュするので、同じファイルを編集してからそれをプルし、コンフリクトを解決してみてください。

## Chapter 9: おまけ1 (Revert, Reset, Rebase)

おまけでもう少しコマンドを紹介します。
以下のコマンドたちは、間違ったコミット履歴を作成してしまった際に、それを修正するために使用します。

### Revert

Revertは指定したコミットを打ち消すコミット履歴を作成するコマンドになります。
コミット履歴を追加するだけなので`Re~~系`のコマンドで一番安全です。

```sh
git revert <コミットID>
```

### Reset

Resetは指定したコミットまでコミット履歴をリセットするコマンドです。
`--hard`オプションを使用する際は特に注意してください。できることなら使用しないようにしましょう。

```sh
# <コミットID>部分は`HEAD^^^`のようにリセットしたいコミット数と同数の`^`を`HEAD`に続けて付加する記述方法も可能です。
git reset --soft <コミットID> # commitから削除。`add`はされている状態。
git reset --mixed <コミットID> # commit,indexから削除。`add`する前の状態に戻る。デフォルトで使用されるオプション。オプション指定がない場合はこちらになります。
git reset --hard <コミットID> # commit,index,差分を削除。完全に無かったことにする。`reset`する前の状態には戻せないので注意。
```

### Rebase

Rebaseは積み上げてきたコミット履歴に対して破壊的な修正を加えることのできるコマンドです。
その名の通り、土台を作り直すイメージです。
できることの範囲が広く複雑で、後戻りもできないため`git reset`と同様注意が必要なコマンドです。

以下でrebaseを使用したコマンドの用途を紹介します。

- 派生元ブランチに変更があった際、古い派生元のコミット履歴と置き換える。
   ```sh
   # 派生先ブランチで実行
   git rebase 派生元ブランチ名
   ```
- コミット履歴の削除やコミット同士の融合、コミットメッセージの変更などを行う
   ```sh
   # 指定範囲のコミットそれぞれに対して何かしらの変更を、エディタ上でインタラクティブに行うためのコマンド
   git rebase -i <コミットID> # 先頭から指定コミットの直前までを変更可能
   git rebase -i HEAD^^^ # 先頭から3番目までのコミットを変更可能(^の数だけコミットを遡る)
   git rebase -i <コミットID>..<コミットID> # 1つ目に指定したコミットから2つ目に指定したコミットの直前までを変更可能
   ```
- 派生元ブランチを変更する
   ```sh
   git rebase --onto <新しい派生元> <現在の派生元> <派生元を変更したいブランチ>
   ```

他にもfixupやsquashを付加したコミットを自動で対象に統合してくれる`--autosquash`オプションなど色々あるので、興味があれば調べてみてください。

## Chapter 10: おまけ2 (cherry-pick, stash)

以下のコマンドもよく使用されます。

### stash

stashは、現在の編集内容を一時的に退避することができます。

```sh
git stash -u # 一時的に編集内容をstashへ退避させる (-uオプションをつけることで、untrackedなファイルも退避できます)
git stash list # stashリストを表示
git stash apply # 退避した編集内容を現在のブランチに反映
git stash pop # 退避させた編集内容を現在のブランチに反映して、stashから削除
git stash drop # stashから特定のものを削除
git stash clear # stashの中をクリア
```

### cherry-pick

cherry-pickは特定のコミットを現在のブランチに持ってくることができます

```sh
git cherry-pick <コミットID>
```
