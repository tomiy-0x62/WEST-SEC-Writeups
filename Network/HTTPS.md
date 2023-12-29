# HTTPS

Scorer: 50

## 概要
webサイトにhttps接続したときのパケットキャプチャからTLS v1.3で使用された暗号化スイートを特定する問題


## 問題
https.pcapngというhttpsでウェブサイトに接続したときのパケットキャプチャファイルが与えられるので、そこからTLS v1.3の暗号化に使用された暗号化スイートを特定する問題

## 解き方
パケットキャプチャから最初のTLSv1.3のパケットを探すと、Infoが"Client Hello"となっているパケットがみつかる。
その中身を見ると、Ciher Suitesのセクションで16種類の暗号スイートが列挙されている。

![Screenshot from 2023-12-29 11-59-08](https://github.com/tomiy-0x62/WEST-SEC-Writeups/assets/58660268/68614ca8-ef80-44d7-bb19-9949cc8725f9)

ここから、クライアントが対応している暗号スイートを列挙してサーバーがその中からスイートを選択するのだと推測できる。
"Client Hello"に対するServerの応答を探すと"Server Hello"がみつかる。
その中を見てみると、Ciher Suite: TLS_AES_128_GCM_SHA256がみつかる。
従って、falgは"TLS_AES_128_GCM_SHA256"だとわかる。

![Screenshot from 2023-12-29 12-04-20](https://github.com/tomiy-0x62/WEST-SEC-Writeups/assets/58660268/e0dca60a-8441-460e-a563-c0a70d25be8f)
