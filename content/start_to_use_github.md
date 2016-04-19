+++
date = "2016-04-19T16:37:28+09:00"
draft = false
title = "SourceTreeとGitHubを使いはじめるまで"
categories = "技術解説"

+++

この文書ではGitリポジトリを扱うためのツールの1つであるSourceTreeのインストールの仕方と、GitHubを使い始めるまでの手順を解説します。

<!--more-->

# SourceTreeのインストール

まず、SourceTreeをダウンロードしてインストールしましょう。

![SourceTreeのダウンロード](/img/sourcetree_download.png)

ダウンロードしてインストールすると最終的に次のような画面になると思います。

![SourceTreeのインストール完了](/img/sourcetree_install_completed.png)

赤い枠で囲ってありますが、私個人としてはSourceTreeを実行する前に入れておきたいソフトウェアがあります。gitをコマンドプロンプトから使う時に便利な[Windows Credential Manager](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)です。

まず、Windows Credential Managerのページに行って…。

![Windows Credential Managerのページ](/img/git_windows_credential_download_1.png)

releaseタブからインストーラをダウンロードして…。

![ダウンロードの画面](/img/git_windows_credential_download_2.png)

普通にインストールしましょう。これで何も考えなくてもgitが素敵なものになります(具体的にはコマンドプロンプトでパスワードをいちいち入力しなくてもよくなる)。

![インストール時のライセンス同意画面](/img/git_windows_credential_license.png)

では、いよいよSourceTreeのセットアップです。スタートメニューか何かからSourceTreeを実行すると初回セットアップが始まります。

![SourceTreeのライセンス同意画面](/img/sourcetree_initial_1.png)

ライセンスを読んで同意したら次はAtlassianアカウントの確認です。BitBucketなどを使っているのならそのアカウントがそのまま使えます。画像では青の矢印がアカウントを持っているときに使用するボタンです。出てきた画面で登録したときのメールアドレスとパスワードを入力しましょう。

![SourceTreeでのAtlassianアカウント入力](/img/sourcetree_initial_2.png)

次はBitBucketやGitHubのアカウント登録です。ここでは何も登録せずにスキップしています。

![SourceTreeでのリポジトリサービスの入力](/img/sourcetree_initial_3.png)

SSHキーの読みこみをするかどうか聞かれるかもしれません。GitHubでは **SSHキーの使用は非推奨** なのでここでは登録しません。

![SourceTreeでのSSHキーの読み込み](/img/sourcetree_initial_4.png)

最後にMercurialとGitのインストールです。インストールする場合は内蔵版をインストールすることができます。これとは別にGit等をダウンロードしてインストールすることもできます。

![Mercurialのインストール選択](/img/sourcetree_initial_5.png)

ついにSourceTreeが立ちあがりました。これで一段落です。

![SourceTreeのトップ画面](/img/sourcetree_top.png)

ちなみに内蔵のgitを使うか、それとも別にインストールしたgitを使うかは設定で変更できます。

![SourceTreeで使うGitを選択する](/img/sourcetree_option_git_select.png)

# SourceTreeでBitbucketのアカウントを結びつける

では(Bitbucketのアカウントを持っていると仮定して)Bitbucketのアカウントを結びつけてみましょう。

まず、SourceTreeのトップ画面から *新規/クローンを作成する* を選択します。

![クローンの作成を選択](/img/sourcetree_clone_top.png)

出た画面でリポジトリのURLを選択して直接手元にリポジトリをクローン(コピー)することも出来ますが、ここでは地球アイコンをクリックします。

![クローンURLの指定画面](/img/sourcetree_clone_select.png)

そうすると、結びつけられたアカウントでクローンできるリポジトリの一覧が表示されるのですが、まだアカウントを結びつけていないので何も表示されません。ここではアカウントを結びつけるために右上のボタンをクリックします。

![ホストされているリポジトリの一覧画面](/img/sourcetree_clone_repository.png)

そうするとアカウントを指定する画面が表示されるので、Bitbucketを指定してユーザー名を入力します。そして次の画面でbitbucketのパスワードを入力すれば結びつけは完了です。

![ホスティングアカウントの指定](/img/sourcetree_clone_account.png)

# GitHubアカウントの初期設定(二要素認証)

この節ではアカウントを作成した後に、安心してGitHubを使用するための初期設定を解説します。

まず、なんとか頑張ってGitHubのアカウントを作成しましょう。おそらくメールでの認証も必要となります。

![GitHubアカウントの作成](/img/github_create_account.png)

さて、適当に頑張るとこんな感じの画面になります(画像は私のアカウントのトップ画面です)。右上のマークからsettingsを選択しましょう。

![GitHubのトップ画面](/img/github_top.png)

GitHubでは二要素認証、つまりパスワードの他にワンタイムパスワードを用いた認証が推奨されています。この設定を行います。

左側のメニューからSecurityを選択し、Two-factor authenticationをクリックしましょう。ここからの手順は[他のページにも書いてあります](https://www.hde.co.jp/otp/ja/github.html)が一応解説します。

おそらく、次のような画面が表示されるはずです。この画面で *Set up using an app* をクリックすると、[Google認証システム](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=ja)を使うためのQRコードが表示されると思います。手元のスマートフォンのGoogle認証システムアプリからアカウントを追加してQRコードを読ませてください。

![二要素認証でアプリを使用する](/img/github_twofactor_setting.png)

なお、先の画面に書いてありますが、携帯電話のショートメッセージでワンタイムパスワードを受けとることも出来ます。

設定したらリカバリーコードを表示させてどこかに控えておきましょう。これはスマホを無くすなどしてワンタイムパスワードが使えなくなった場合に使用します。

![リカバリーコードの表示画面](/img/github_twofactor_recovery_code.png)

# gitからGitHubを扱うためのパスワード生成

さて二要素認証を有効にすると、gitは通常の方法ではGitHubにプッシュ(コードを送る)することが出来なくなります。それはgitが通常はユーザーIDとパスワードの組みあわせでしか認証しないためです。ですから、gitから使用する専用のパスワードを生成します。

左のメニューからPersonal Access Tokenをクリックしてください。

![PersonalAccessTokenの画面](/img/github_token_generate.png)

私の画面では既にいくつかパスワードが生成されているのでこうなっています。新しく作成する場合にはGenerate new tokenを選択しましょう。

![新規トークンの作成画面](/img/github_token_new.png)

するとこのような画面が表示されます。ここでは新しく生成したパスワード(トークン)で何が出来るかを指定します。

上の赤い枠はトークンの説明です。認証には使わないのでわかりやすい名前を付けましょう。下の赤い枠が重要でトークンを何に使うかの設定です。通常はチェックされている3つがあれば十分でしょう。

さて、下の方にあるボタンをクリックすると実際にトークンが生成されます。この画像で赤く染まっている部分がそのトークンです。この画面で一度きりしか表示されないのでクリップボードにコピーするなりなんなりして保存しておきましょう。

![新規トークンの作成完了](/img/github_token_complete.png)

ここまでやれば、あとは先の手順で示したようにGitHubのアカウントを結びつければおおよそのことは出来ると思います。お疲れさまでした！
