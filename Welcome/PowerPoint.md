# PowerPoint

Scorer: 50 

## 概要
PowerPointファイルに隠されたflagを探す問題


## 問題
logo.pptxというPowerPointファイルが与えられるので、その中からflagを探すという問題

## 解き方
僕はベアメタルのUbuntuで解いていたのでファイルを開けませんでした。
pptxファイルが実態はzipファイルだと知っていたのでとりあえず、`unzip logo.pptx`で解凍してみました。
解凍したディレクトリの構造は以下のようになっていました。

```
$ tree
.
├── [Content_Types].xml
├── docProps
│   ├── app.xml
│   ├── core.xml
│   └── thumbnail.jpeg
├── logo.pptx
├── ppt
│   ├── media
│   │   └── image1.png
│   ├── presentation.xml
│   ├── presProps.xml
│   ├── _rels
│   │   └── presentation.xml.rels
│   ├── slideLayouts
│   │   ├── _rels
│   │   │   ├── slideLayout1.xml.rels
│   │   │   ├─ 〜省略〜
│   │   │   └── slideLayout11.xml.rels
│   │   ├── slideLayout1.xml
│   │   ├── 〜省略〜
│   │   └── slideLayout11.xml
│   ├── slideMasters
│   │   ├── _rels
│   │   │   └── slideMaster1.xml.rels
│   │   └── slideMaster1.xml
│   ├── slides
│   │   ├── _rels
│   │   │   └── slide1.xml.rels
│   │   └── slide1.xml
│   ├── tableStyles.xml
│   ├── theme
│   │   └── theme1.xml
│   └── viewProps.xml
└── _rels
```
テキストファイルの中にflagが含まれていないか調べるため、grepで検索しましたが、見つかりませんでした。
```
$ grep -r flag
$ grep -r Flag
```
テキストファイルからは見つからなかったので、画像ファイルを探してみると、ppt/media/image1.pngにflag "Attention"が隠されていました。
![image1](https://github.com/tomiy-0x62/WEST-SEC-Writeups/assets/58660268/08a30ab4-05b6-4a3a-bbed-7fbe453e4105)

