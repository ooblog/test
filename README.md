# test

GitHub練習用。  
「README.md」の練習ついでにGitHubの使い方メモ。  

## 超前提。なぜGitHubの時代が来た？

「ファイル名.日付.zip」みたいなファイルを結局ローカルで管理とかzipをメールやFTP(平文)で送るみたいな危なっかしいやり方を卒業するのだ。  

[公開鍵暗号方式の誤り解説の氾濫をそろそろどげんかせんと](http://takagi-hiromitsu.jp/diary/20080229.html)

>公開鍵と秘密鍵が「逆に使える」というのはRSAアルゴリズムがたまたま（まあまあ）そうなだけであって、そのような性質を持たない他の公開鍵暗号方式がたくさん存在する。  

## まずはハードディスクの容量確認。

Gitはファイルだけでなくリポジトリ(「.git」フォルダ)をローカルにも保存する仕組み。誰でも理論上(鯖管リソース考慮しなければ)Hubになれる仕組み。  

[1.3 使い始める - Gitの基本](https://git-scm.com/book/ja/v1/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-Git%E3%81%AE%E5%9F%BA%E6%9C%AC)  

## SSLのバージョンを確認。確認だけじゃ無く更新。

GitHubはSSH鍵を使ってファイルをやり取りする。OpenSSHなどSSHプロトコルはSSLを使う。なのでGitHub(Git)使うにはOpenSSLの脆弱性注意。  

[Puppy Linux Precise-571JP の OpenSSL を更新](http://near-unix.blogspot.jp/2014/04/puppy-linux-precise-571jp-openssl.html)  

## SSHの鍵はパスワードみたいなものなので、最初から複数の鍵を使い分ける事を想定して環境を整える。

### 「ssh&#45;keygen」で「~/.ssh/」に公開鍵.pubと秘密鍵を作成する。

任意の名前の付いた鍵を作成する。  
鍵自体にもパスフレーズ(パスワード)が設置でき、さらにそのパスフレーズ毎回入力の手間も省く裏技まで存在するっぽいが、最初からパスフレーズ省略。  

    # ssh-keygen -t rsa -f ~/.ssh/鍵の名前

鍵を作成すると「~/.ssh/」フォルダができる。フォルダ内に公開鍵.pubと公開鍵と同名で拡張子の無い秘密鍵が作成される。  

[SSH-KEYGEN](http://euske.github.io/openssh-jman/ssh-keygen.html)

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

## Webの「GitHub」とローカルの「Git」にアカウント登録

GitHubに登録したメアドとユーザIDをローカルのGitにも登録。  
リポジトリ毎に別アカウントを登録できるっぽいけど未確認。  

    # git config --global user.name  ユーザID
    # git config --global user.email メルアド

[1.5 使い始める - 最初のGitの構成](https://git-scm.com/book/ja/v1/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-%E6%9C%80%E5%88%9D%E3%81%AEGit%E3%81%AE%E6%A7%8B%E6%88%90)

## WebのGitHubに公開鍵.pubを登録

### 「公開鍵.pub」をコピペで張り付ける

    # cd ~/.ssh/
    # cat 公開鍵.pub

先頭の「ssh-rsa 」を含めたテキストをGitHubの「New SSH key」に張り付ける。末尾のホスト名は省略可能。  

[SSH keys](https://github.com/settings/ssh)

### 「github」と接続テストを行う。

「config」ファイルで「Host github」を作成したので「github」と接続テスト。
「ssh &#45;T github.com」だとデフォルト鍵「id&#95;rsa」とかが使われる怖れ。鍵(パスワード)の使いまわしは危険。  
IPアドレスもだけど「fingerprint」公開鍵にも注目。「github.com」と繋がった事を確認したら「yes」。  
この段階で「~/.ssh/known&#95;hosts」が作成され、GitHubから受け取った公開鍵が管理される。  

    # ssh -T github
    The authenticity of host 'github.com (192.30.252.129)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'github.com,192.30.252.129' (RSA) to the list of known hosts.
    Hi ooblog! You've successfully authenticated, but GitHub does not provide shell access.

[Testing your SSH connection](https://help.github.com/articles/testing-your-ssh-connection/)

## 「git init」より「git clone」の方が簡単なので、「GitHub」でリポジトリを作って「Git」にクローンを作る。

「GitHub」でリポジトリを作ると「LICENSE」ファイルなどが自動作成されるので便利。  

### 「LICENSE」を選択する&#40;西暦とユーザー名が自動で入力されます&#41;。

ライセンスは訴訟回避の法的おまじない。ここは空気を読んで寄せる努力がベター。  

[たくさんあるオープンソースライセンスのそれぞれの特徴のまとめ](http://coliss.com/articles/build-websites/operation/work/choose-a-license-by-github.html)

### 「.gitignore」

gitを使いこなせてないので謎。とりあえずPythonで開発するならPython選択するぐらいの認識。  

### 「README.md」は「reddit」みたいに「半角スペース2つで改行」する「Markdown」である。

[README.mdファイル。マークダウン記法まとめ](http://codechord.com/2012/01/readme-markdown/)

underscoreとかエスケープ(文字参照)する必要がある。「kantray」を使うと「照&#95;⇔&#38;#95&#59;」とかの機能あるのでお勧め。  
その「kantray」をGitHubにうpする練習でこの「test」書いてる。  
「.md」ファイルはGitHub上でも編集できるっぽいけど、ブログの文章が消えるのと同じで、下書きをローカルで書いてからうpした方が安定。  

### 「GitHub」で作ったリポジトリをローカルの「Git」にダウンロードする事を「clone」という。

冒頭で容量確認したハードディスクにgit用のディレクトリとかフォルダとか作っておく。でフォルダに移動。  

    # cd /mnt/sdb1/github

リポジトリを指定して「git clone」を実行するとリポジトリ名でフォルダが作成される。「config」のホスト名はここでも使う。  

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

「test.git」を編集する時は「test」フォルダに移動してから「commitなどの作業」。  

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

    # git commit &#45;a &#45;m "「git clone」の辺りまで執筆。"
    &#91;master bf8119d&#93; 「git clone」の辺りまで執筆。
     1 file changed, 154 insertions(+)



今それ勉強中。コミットとかできてから書く予定。  

