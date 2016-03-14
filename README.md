# 「test」テストリポジトリ〜その日の午前中にGitHubを使えるようになってもらうマニュアル。

「README.md」をコミットできる事を確認するまでがGitHubの初期設定です。  

## 前提。なぜGitHubの時代が来た？

「ファイル名.日付.zip」みたいなファイルを結局ローカルで管理とか、  
zipをメールやFTP(平文)で送るみたいな危なっかしいやり方を卒業するのだ。  

[もうFTPを利用することは止めて、Gitを使おう。そのほうがメリットが多いよー ](http://wp-d.org/2014/02/26/5709/)
>FTPポートを閉じることができるので、セキュリティー面も向上する

[公開鍵暗号方式の誤り解説の氾濫をそろそろどげんかせんと](http://takagi-hiromitsu.jp/diary/20080229.html)
>公開鍵と秘密鍵が「逆に使える」というのはRSAアルゴリズムがたまたま（まあまあ）そうなだけであって、  
>そのような性質を持たない他の公開鍵暗号方式がたくさん存在する。  

## まずはハードディスクの容量確認。

Gitはファイルだけでなくリポジトリ(「.git」フォルダ)をローカルにも保存する仕組み。  
誰でも理論上(鯖管リソース考慮しなければ)Hubになれる仕組み。  

[1.3 使い始める - Gitの基本](https://git-scm.com/book/ja/v1/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-Git%E3%81%AE%E5%9F%BA%E6%9C%AC)  
>スナップショットで、差分ではない  
>ファイルに変更が無い場合は、既に格納してある、以前の同一のファイルへのリンクを格納します。  
>ほとんど全ての操作がローカル Gitはサーバーに履歴を取得しに行って表示する必要がありません。  
>直接にローカル・データベースからそれを読むだけです。これは、プロジェクトの履歴をほとんど即座に知るということです。  

## SSLのバージョンを確認。確認だけじゃ無く更新。

GitHubはSSH鍵を使ってファイルをやり取りする。OpenSSHなどSSHプロトコルはSSLを使う。  
GitHub(Git)使うにはOpenSSLの脆弱性注意。OS毎のセキュリティアップデート方法を確認。  

[Puppy Linux Precise-571JP の OpenSSL を更新](http://near-unix.blogspot.jp/2014/04/puppy-linux-precise-571jp-openssl.html)  
>上書きインストールが完了しましたらバージョンチェックをします。  
>urxvt上からOpenSSLのバージョン確認コマンド（ビルド時刻の表示オプション付き）を実行します。  
>openssl version &#45;b

## SSH鍵はパスワードみたいなもの。最初から複数の鍵を使い分ける事を想定して環境を整える。

### 「ssh&#45;keygen」で「~/.ssh/」に公開鍵.pubと秘密鍵を作成する。

任意に名前を付けた鍵を作成する。  
鍵自体にもパスフレーズ(パスワード)が設置でき、さらにそのパスフレーズ入力手間省く裏技まで存在するっぽいが、  
最初からパスフレーズ入力を省略。  

    # ssh-keygen -t rsa -f ~/.ssh/鍵の名前

鍵を作成すると「~/.ssh/」フォルダができる。  
「~/.ssh/」フォルダ内に公開鍵.pubと公開鍵と同名で拡張子の無い秘密鍵が作成される。  

[SSH-KEYGEN (1)](http://euske.github.io/openssh-jman/ssh-keygen.html)
>~/.ssh/id&#95;rsa 秘密鍵ファイルのデフォルトの名前としてこれが提案されます。  
>&#45;f ファイル名 鍵を格納するファイル名を指定します。  
>&#45;t 生成する鍵の種類を指定します。プロトコルバージョン 2 で使う"dsa","ecdsa","ed25519"または"rsa"があります。  
>コメントは"user@host"の形に初期化されますが、-c オプションを使えば変更することができます。
>&#40;訳注: SSH2 の鍵にはコメントはありません&#41;  

鍵を作ったら右クリックからのプロパティとかで書き込み禁止。  

### 「~/.ssh/config」ファイル作成して鍵を管理する。

「config」ファイルをエディタで作成する。「github」の名前でサイト名と鍵名の管理が用意に。  
ホスト毎にPythonみたく空白インデント。  

    Host github
        HostName       github.com
        User           git
        IdentityFile   ~/.ssh/秘密鍵
        IdentitiesOnly yes
        Compression    yes

[SSH_CONFIG (5)](http://euske.github.io/openssh-jman/ssh_config.html)
>キーワードと引数は、空白またはひとつの = (間に空白を含んでいてもよい) によって区切られます。  
>空白を含む引数は、ダブルクォートで囲んで表現することもできます。  
>Host &#40;ホスト&#41; 設定項目を、ここで指定されたパターンのどれかにマッチするホストだけに制限します。  
>HostName &#40;実際のホスト名&#41; 実際にログインするホスト名を指定します。数字の IP アドレスでもかまいません  
>IdentityFile &#40;identityファイル&#41; 認証時にどのidentityを提供するかを選択することができます。  
>IdentitiesOnly &#40;ファイルに格納された秘密鍵のみを使用&#41; configファイルで指定された秘密鍵のみを使用するよう指定します。  
>Compression &#40;圧縮&#41; データ圧縮をおこなうかどうかを指定します。  
>Port &#40;ポート番号&#41; リモートホストに接続するときのポート番号を指定します。デフォルトのポートは 22 です。  

環境によっては「Port」を443とかに。「config」ファイルも設定終わったら書き込み禁止。  

[Githubにport:22 で push できなかった場合の対処法](http://qiita.com/SOJO/items/74b16221580a17296226)
>Port:443 で接続するように設定ファイルを作ります。  
>chmodコマンドを使って権限を書き換えます。  

## Webの「GitHub」とローカルの「Git」にアカウント登録

GitHubに登録したメールアドレスとユーザIDをローカルのGitにも登録。  
リポジトリ毎に別アカウントを登録できるっぽいけど未確認。  

    # git config --global user.name  ユーザID
    # git config --global user.email メールアドレス

[1.5 使い始める - 最初のGitの構成](https://git-scm.com/book/ja/v1/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-%E6%9C%80%E5%88%9D%E3%81%AEGit%E3%81%AE%E6%A7%8B%E6%88%90)
>--globalオプションを指定するのであれば、この操作を行なう必要はたった一度だけです。  
>違う名前とE&#45;mailアドレスを特定のプロジェクトで上書きしたいのであれば、  
>そのプロジェクトの&#40;訳者注：Gitディレクトリの&#41;中で、--globalオプション無しでこのコマンドを実行することができます。  

## WebのGitHubに公開鍵.pubを登録

### 「公開鍵.pub」をコピペで張り付ける

    # cd ~/.ssh/
    # cat 公開鍵.pub

先頭の「ssh-rsa 」を含めたテキストをGitHubの「New SSH key」に張り付ける。末尾のホスト名は省略可能。  

[SSH keys](https://github.com/settings/ssh)
>This is a list of SSH keys associated with your account

### 「github」と接続テストを行う。

「config」で「Host github」を作成したので「git&#64;github.com」ではなく「github」と入力して接続テスト。  
デフォルトの「ssh &#45;T github.com」だとデフォルト鍵「id&#95;rsa」が使われる怖れ。鍵(パスワード)の使いまわしは危険。  
IPアドレスもだけど「fingerprint」公開鍵にも注目。「github.com」と繋がった事を確認したら「yes」。  
この段階で「~/.ssh/known&#95;hosts」が作成され、GitHubから受け取った公開鍵が管理される。  

    # ssh -T github
    The authenticity of host 'github.com (192.30.252.129)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'github.com,192.30.252.129' (RSA) to the list of known hosts.
    Hi ooblog! You've successfully authenticated, but GitHub does not provide shell access.

[Testing your SSH connection](https://help.github.com/articles/testing-your-ssh-connection/)
>Verify that the fingerprint in the message you see matches the following message, then type yes

## 「git clone」の方が簡単なので「GitHub」でリポジトリを作る。

「GitHub」でリポジトリを作ると「LICENSE」ファイルなどが自動作成されるので便利。  

### 「LICENSE」を選択する&#40;西暦とユーザー名が自動で入力されます&#41;。

ライセンスは訴訟回避の法的おまじない。ここは空気を読んで既存のオープンソースライセンスに寄せておくのがベター。  

[Githubによる、オープンソースライセンスの選び方](http://www.catch.jp/oss-license/2013/09/10/github/)
>なにも明示されていないと、自由に使ってほしくないのでライセンスを適用していないのか、区別が付きません。  
>現在のGithubでは、新しいレポジトリを作るときに公開レポジトリ(Public)を選択すると、  
>Githubで無視させるファイル(gitignore)とライセンスを選択できるようになっています。  
>Githubが設置したライセンス選択サイト「Choosing an OSS license doesn’t need to be scary」  
>オープンソースにするつもりがないにも関わらず、公開リポジトリを利用している人に、注意を促しています。  
>パブリックリポジトリで公開する場合、あなたは、Githubの利用条件に同意して、  
>他のGithubユーザーにいくつかの権利を与えていることになります。  
>ライセンスを選ぶのが面倒ならば、MITライセンス一択でいいんじゃないでしょうか。

### 開発言語を選択すると「.gitignore」が自動生成され余分なファイルがリポジトリに混入するのを防ぎます。

例えばPythonを選択すると「&#42;.py&#91;cod&#93;」が書き込まれますが「.pyc」「.pyo」「.pyd」の意味です。  
[What is the difference between &#34;py&#91;cod&#93;&#34; and &#34;pyc&#34; in .gitignore notation?](http://stackoverflow.com/questions/18729510/what-is-the-difference-between-pycod-and-pyc-in-gitignore-notation)
>You are safe to remove the .pyc entry from your .gitignore, since py&#91;cod&#93; will cover it.
>The square brackets are for matching any one of the characters, so it matches .pyc, .pyo and .pyd.

### 「README.md」は「reddit」みたいに「半角スペース2つで改行」。

「.md」ファイルはGitHub上でも編集できるけど、「pull」「commit」「push」の練習に使う。  
コード表示だと改行自動だけど引用には半角スペース改行が必要なので注意。

[README.mdファイル。マークダウン記法まとめ](http://codechord.com/2012/01/readme-markdown/)
>見出し 行頭の#の個数でH1～H6を表す。  
>改行 行末に2つのスペースを入れる。  
>コード表示（等幅フォントで表示）半角スペースを4つ行頭に  
>引用（blockquote）メールの引用の感じ。&#62;引用  
>リスト 行頭にアスタリスク&#40;&#42;&#41;か、番号ピリオド&#40;1.&#41;  
>強調 アスタリスク&#40;&#42;&#41;またはアンダーバー&#40;&#95;&#41;で囲う  
>リンク &#91;リンクのテキスト&#93;&#40;リンクのアドレス&#34;リンクのタイトル&#34;&#41;リンクのタイトルは省略可能。  
>画像 先頭にビックリマーク &#33;&#91;Alt text&#93;&#40;/path/to/img.jpg&#34;Optional title&#34;&#41;  
>水平線 3つ以上のハイフン、アスタリスク、アンダースコアをならべる  
>その他の注意 &amp;マークなどは「&amp;amp&#59;」に変換しておく。  

「kantray」(GitHub版準備中)を使うと「照&#95;⇔&#38;#95&#59;」とかの機能あるのでお勧め。  

### 「GitHub」で作ったリポジトリをローカルの「Git」に「clone」する。

冒頭で容量確認したハードディスクにgit用のディレクトリとかフォルダとか作っておき、そのフォルダに移動。  

    # cd /mnt/sdb1/github

リポジトリURL…ではなく「config」で設定したホスト名を使う。  
「configホスト名:ユーザID/.git」このフォーマット後述の「pull」の説明でも使います。  
「git clone」を実行するとリポジトリ名でフォルダが作成される。  

    # git clone github:ooblog/test.git
    Cloning into 'test'...
    Warning: Permanently added the RSA host key for IP address '192.30.252.131' to the list of known hosts.
    remote: Counting objects: 8, done.
    remote: Compressing objects: 100% (6/6), done.
    remote: Total 8 (delta 1), reused 0 (delta 0), pack-reused 0
    Receiving objects: 100% (8/8), done.
    Resolving deltas: 100% (1/1), done.

フォルダ内に無事「.git」フォルダ(リポジトリ)ができてるか一応確認する。  

    # cd test
    # git status
    # On branch master
    nothing to commit (working directory clean)

「test.git」を編集する時は「test」フォルダに移動してから「commit」などの作業。  
「git status」でローカルリポジトリにいるか確認。  

## 「commit」して「push」する。

### 例えば「README.md」を編集すると「git status」が変化する。  

    # cd test
    # git status
    # On branch master
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #	modified:   README.md
    #
    no changes added to commit (use "git add" and/or "git commit -a")

編集したら編集内容についてコメントを残す。  
「ニコニコ大百科」で例えると「編集内容についての説明」みたいなもの。  

    # git commit -a -m "「git clone」の辺りまで執筆。"
    [master bf8119d] 「git clone」の辺りまで執筆。
     1 file changed, 154 insertions(+)
    # git commit -a -m "「git commit -a -m」が成功したので「commit」の項目にもつい加筆。"
    [master 1064a63] 「git commit -a -m」が成功したので「commit」の項目にもつい加筆 。
     1 file changed, 6 insertions(+), 1 deletion(-)

[Git - 変更内容のリポジトリへの記録](https://git-scm.com/book/ja/v1/Git-%E3%81%AE%E5%9F%BA%E6%9C%AC-%E5%A4%89%E6%9B%B4%E5%86%85%E5%AE%B9%E3%81%AE%E3%83%AA%E3%83%9D%E3%82%B8%E3%83%88%E3%83%AA%E3%81%B8%E3%81%AE%E8%A8%98%E9%8C%B2)
>コミットメッセージをインラインで記述することもできます。  
>その場合は、commit コマンドの後で &#45;m フラグに続けて記述します。  
>&#45;a オプションを指定すると、追跡対象となっているファイルを自動的にステージしてからコミットを行います。  
>つまり git add を省略できるというわけです。

コミットできてれば「modified」メッセージは出ないはず。  

    # cd test
    # git status
    # On branch master
    # Your branch is ahead of 'origin/master' by 2 commits.

### ローカル「Git」のbranchをに「GitHub」に「push」する。

「git clone」した同じリポジトリに今度は「git push」する(「origin」使わない方法で説明)。  
「configホスト名:ユーザID/.git」このフォーマット先述の「clone」の説明でも使いました。  
「master」ってのはブランチbranchの名前。枝分かれしてないので「master」の更新。  

    # git push github:ooblog/test.git master
    Warning: Permanently added the RSA host key for IP address '192.30.252.128' to the list of known hosts.
    Counting objects: 8, done.
    Compressing objects: 100% (6/6), done.
    Writing objects: 100% (6/6), 4.30 KiB, done.
    Total 6 (delta 3), reused 0 (delta 0)
    To github:ooblog/test.git
       3f19620..1064a63  master -> master

## 「push」できたか確認。

リポジトリのWebページを開くと「README.md」「Initial commit」のテキストが、  
「git commit &#45;a &#45;m」でコミットしたテキストに変化してれば「pull」成功。  

[ooblog/test](https://github.com/ooblog/test)

ちなリポジトリは「Download ZIP」ボタンでもダウンロードができます。  
Git使えない人から「ZIPでくれ」と言われても「そこにZIPがあるじゃろ？」と挨拶できますね。  

## リポジトリにファイルを追加する。

まだですー。  

## あとがき＆今後の予定。

執筆者もGitHub勉強中。  
GitについてググってもGitHub以前の情報だったり断片的だったりvi前提だったり不便だったのでまとめた。  
「使えるようになってもらうマニュアル」とか言いつつ、ファイルの追加削除はまだ書いてない。後日後日。  
ブランチ管理とかGitHub１日目の内容じゃないし知らないので、もし書くとしても別mdだと思う。  

