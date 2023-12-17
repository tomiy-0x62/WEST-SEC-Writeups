# Leaked AI Key

Scorer: 100

## 概要
リークしたAPI keyを探しだす問題

## 問題
leekOpenAIkey.tgzという圧縮ファイルが与えられるのでその中からAPI keyを探しだす問題

## 解き方
`tar -xf leekOpenAIkey.tgz`で解凍します。
解凍したディレクトリは以下のようになってます。

![Screenshot from 2023-12-17 23-48-05](https://github.com/tomiy-0x62/WEST-SEC-Writeups/assets/58660268/1746b22d-23fa-478e-8017-8a50ed9b0814)

プロンプトの右端にブランチ名が出ているのでgit管理されていることがわかるのですが、僕の目は節穴なので気づきませんでした。
とりあえずREADME.mdでも読んでみます。neovimで開きます'nvim README.md'。

![Screenshot from 2023-12-17 23-50-23](https://github.com/tomiy-0x62/WEST-SEC-Writeups/assets/58660268/21947835-cdf7-4a95-8b36-4a92087b024c)

neovimにairblade/vim-gitgutterというプラグインを入れていたので、一番左の列に最終commitから変更があったことを示す'~'が表示されているのを見て、このディレクトリがgit管理されていることに気づきました。

コミットログを確認してみます。
```
$ git log
commit 16a1150f88dd5dde51a4036bbf45f3b0ad1f636d (HEAD -> master)
Author: yud-y <westsec-ctf@example.com>
Date:   Tue Nov 28 11:38:58 2023 +0900

    change to use langchain

commit c63a02b538760c021f307b94fa9b2b61e4c29f24
Author: yud-y <westsec-ctf@example.com>
Date:   Tue Nov 28 11:35:44 2023 +0900

    delete environment file and add env example
    
commit fe5eefc72104028175e576a128fac12c359a31c2
Author: yud-y <westsec-ctf@example.com>
Date:   Tue Nov 28 11:34:34 2023 +0900

    create first version of chat-bot
〜省略〜
```
"commit c63a02b538760c021f307b94fa9b2b61e4c29f24"の"delete environment file"が気になります。
web APIを叩くプログラムを書くときに、APIキーをソースコードに直接埋め込むとキーが漏洩する可能性があるので、環境変数からキーを読むという話を聞いたことがあったので、"commit c63a02b538760c021f307b94fa9b2b61e4c29f24"の"delete environment file"が気になります。
コミットの詳細を確認してみます。
```
$ git log --name-status c63a02b538760c021f307b94fa9b2b61e4c29f24
commit c63a02b538760c021f307b94fa9b2b61e4c29f24
Author: yud-y <westsec-ctf@example.com>
Date:   Tue Nov 28 11:35:44 2023 +0900

    delete environment file and add env example

D       .env
A       .env_example
〜省略〜
```
.envファイルの中身を見るために"commit c63a02b538760c021f307b94fa9b2b61e4c29f24"の一つ前のcommitにcheckoutして.envファイルを見てみます。
```
$ git checkout fe5eefc72104028175e576a128fac12c359a31c2
$ cat .env
FLASK_APP=app
FLASK_ENV=development

OPENAI_API_KEY=c2steHh4eHh4eHh3cnNaR09zUmg0R2lUM0JsYmtGSkVudjF1czdKM0M3ZXh4eHh4eHh4
```
この"OPENAI_API_KEY=c~"を提出しましたが、違いました。
.envを読み取っていると思われるapp.pyを読んでみます。
```
openai.api_key = base64.b64decode(os.getenv("OPENAI_API_KEY")).decode()
```
どうやらAPIキーはbase64でエンコードされているみたいなのでデコードしてみます。
```
$ python3
>>> import base64
>>> base64.b64decode("c2steHh4eHh4eHh3cnNaR09zUmg0R2lUM0JsYmtGSkVudjF1czdKM0M3ZXh4eHh4eHh4")
b'sk-xxxxxxxxwrsZGOsRh4GiT3BlbkFJEnv1us7J3C7exxxxxxxx'
```
この'sk-xxxxxxxxwrsZGOsRh4GiT3BlbkFJEnv1us7J3C7exxxxxxxx'がflagでした。

## 学び
web APIを叩くプログラムを書くときにAPI keyをソースコードに埋め込むのは論外だとして、envから読み取るようにしてもenvファイルを一緒にcommitしては意味がない。
.envを使うときは必ず.gitignoreに.envを追加しないといけない。
人類は愚かなので気をつけてもやらかす。
なので、 web APIを叩くプログラムを書くときのための.envを追加した.gitignoreと.env_exampleが入ったレポジトリのテンプレートを用意してそれを使うとこのようなインシデントを防げるのではないかと思った。
