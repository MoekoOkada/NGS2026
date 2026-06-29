# Linuxコマンドまとめ <!-- omit in toc -->

## Table of contents <!-- omit in toc -->

- [なぜコマンドを使うのか？](#なぜコマンドを使うのか)
- [Unix/Linuxで重要な概念２つ](#unixlinuxで重要な概念２つ)
- [コマンド一覧](#コマンド一覧)
  - [コマンドのマニュアルを表示する: `man`](#コマンドのマニュアルを表示する-man)
  - [作業ディレクトリを変更する: `cd`](#作業ディレクトリを変更する-cd)
  - [ディレクトリ内のフォルダやファイルを一覧する: `ls`](#ディレクトリ内のフォルダやファイルを一覧する-ls)
  - [シンボリックリンクでストレージを節約する: `ln -s`](#シンボリックリンクでストレージを節約する-ln--s)
  - [ディレクトリを作る: `mkdir`](#ディレクトリを作る-mkdir)
  - [ファイルを移動する: `mv`](#ファイルを移動する-mv)
  - [コピー: `cp`](#コピー-cp)
  - [削除する: `rm`](#削除する-rm)
  - [複数コマンドを走らせるパイプ: `|`](#複数コマンドを走らせるパイプ-)
  - [結果をファイルに出する: `>`](#結果をファイルに出する-)
  - [ファイルを結合する: `cat`](#ファイルを結合する-cat)
  - [大容量のファイルの中身を見る: `less`](#大容量のファイルの中身を見る-less)
  - [ファイル内検索: `grep`](#ファイル内検索-grep)
  - [圧縮ファイルを操作する: `zcat`、`zless`、`zgrep`、`zdiff`](#圧縮ファイルを操作する-zcatzlesszgrepzdiff)
  - [列の選択: `cut`](#列の選択-cut)
  - [数える: `wc`](#数える-wc)
  - [文字置換、削除: `tr`](#文字置換削除-tr)
  - [文字列置換その2: `sed`](#文字列置換その2-sed)
  - [並び替え: `sort`](#並び替え-sort)
  - [重複を除く: `uniq`](#重複を除く-uniq)
  - [権限を操作する: `chmod`](#権限を操作する-chmod)
  - [ウェブからのダウンロード: `wget`](#ウェブからのダウンロード-wget)

## なぜコマンドを使うのか？

- Bioinformaticsのソフトウェアは、コマンドでしか動かないものが多い

- 残念ながら既存のGUIでコムギとその近縁野生種にも使えるものは少ない

- アプリケーションの起動やボタン選択のためのメモリやCPUを解析に割ける

- 自動化・再現性の確保が簡単
  - バイオインフォマティクスを扱う研究者がどんどん増えている
  - フェイクデータや信頼性の低いデータも増えている。
  - それでも**結果はいつ誰がどこでやっても同じでなければいけない**
  - マニュアルでの改変、GUIの操作は記録が難しい
  - コマンドラインの操作だと、保存したスクリプトを使うだけで、解析結果を再現できる

## Unix/Linuxで重要な概念２つ

(linuxとかunixについては各自ググってみてください)

1. ディレクトリ構成  
      **Root directory**: 根本にある大元のディレクトリ(=フォルダ)。top directoryともいう。  
      **Home directory**: ターミナルを起動したときに最初にいる場所  
      **Current directory**: 今いる作業ディレクトリ  
      **Parent directory**: 一つ上のディレクトリ  
   <br>
2. ディレクトリやファイルがある場所を示す**パス**
   **絶対パス(Absolute path)**: Root directoryからの経路。`/Users/name/Desktop/project1/data/`  
   **相対パス(Relative path)**: Current directoryからの経路。`../project2/scripts`

## コマンド一覧

Linuxコマンドでよく使うものたちを網羅的にリストアップしました。  
Mac/Ubuntu/CentOSなど、bioinformaticsを行うのに一般的なOSを搭載したマシンでのコマンド操作はunixをベースにしているかlinuxに近いものなので、これらのコマンドを全く同様に使えます。  
Windowsで使う場合は改変が必要です ("`command` windows" とかってググって対応してください)。

* * *

### コマンドのマニュアルを表示する: `man`

使い方: `man <command>`  

このコマンドの使い方なんだっけ？となったら、ターミナルに`man <command>`と打ってみてください。　　
一通りのオプションが表示されます。

```bash
$ man less
LESS(1)                                                                LESS(1)



NAME
       less - opposite of more

SYNOPSIS
       less -?
       less --help
       less -V
       less --version
       less [-[+]aABcCdeEfFgGiIJKLmMnNqQrRsSuUVwWX~]
            [-b space] [-h lines] [-j line] [-k keyfile]
            [-{oO} logfile] [-p pattern] [-P prompt] [-t tag]
            [-T tagsfile] [-x tab,...] [-y lines] [-[z] lines]
            [-# shift] [+[+]cmd] [--] [filename]...
       (See  the  OPTIONS section for alternate option syntax with long option
       names.)


DESCRIPTION
       Less is a program similar to more (1), but which allows backward  move-
       ment in the file as well as forward movement.  Also, less does not have
・
・
・
$
```

そもそものコマンド名がわからない時はググった方が早いですが、オプションの検索などは、`man`の方が早いことも。

* * *

### 作業ディレクトリを変更する: `cd`

使い方: `cd ~/Desktop/`  

change directoryの略。作業ディレクトリ(=フォルダ)を移動する。
あるファイルを開きたい場合、そのファイルが入っているフォルダまでクリックで進まないと開けないのと同じように、コマンドでもファイルが入っている場所に移動してから各種操作を行う必要がある。  

`cd`のあとに、行きたいディレクトリへのパスを記述する。相対パスでも絶対パスでもOK。  
パスを指定せず、`cd`のみ打つと、home directoryに戻る。  

`cd ..`とすると、parent directoryに移動。  
`cd -`とすると、直前に作業していたディレクトリに移動。

```bash
$ cd /Users/username/Desktop/BioinfBootCamp/
BioinfoBootCamp $ cd ..
Desktop $
```

* * *

### ディレクトリ内のフォルダやファイルを一覧する: `ls`

使い方: `ls <filepath>`  

ファイル名を羅列してくれる。オプションをつけると情報量が増える。パスをつけないとその場所のファイルリストを表示する。

よく使うオプション↓

| options | detail                         |
| ------- | ------------------------------ |
| -a      | (隠しファイルも含めて)全て表示 |
| -l      | 詳細も表示                     |
| -t      | 更新時間でソート               |
| -S      | ファイルサイズでソート         |
| -X      | 拡張子ごとにまとめる           |
| -r      | 逆順で表示                     |

```bash
$ cd ~/Desktop/BioinfoBootCamp/Ahal/
Ahal $ ls
Ahal.gff
Ahal $ ls -la
total 114752
drwxr-xr-x@ 3 username  staff        96 Oct  5 18:25 .
drwxr-xr-x@ 8 username  staff       256 Oct  5 18:24 ..
-rw-r--r--@ 1 username  staff  57913032 Oct  5 18:25 Ahal.gff
Ahal $
```

* * *

### シンボリックリンクでストレージを節約する: `ln -s`

使い方: `ln -s <file path you want to make a symlink>`  

解析中は、同じファイルを複数ディレクトリに置きたくなることが多い。全部コピー`cp`すると、例えばコムギゲノムの場合14 GBのスペースを毎回消費してしまう。  
リンクのみを作成して元ファイルを参照させることでスペースを節約する。

```bash
$ cd ~/Documents/moko/
moko $ ls
moko $ ln -s ~/Desktop/BioinfBootCamp/Ath
moko $ ln -s ~/Desktop/BioinfBootCamp/Ahal
moko $ ln -s ~/Desktop/BioinfBootCamp/file
moko $ ls
Ath Ahal file
moko $
```

* * *

### ディレクトリを作る: `mkdir`

使い方: `mkdir <filename>`  

fileディレクトリ以下に`data`ディレクトリを作ってみてください。

```bash
$ cd ~/Documents/moko/file
file $ ls
file1.txt file2.txt file3.txt
file $ mkdir data
file $ ls
data file1.txt file2.txt file3.txt
file $
```

* * *

### ファイルを移動する: `mv`

使い方: `mv [option] <file name> <path>`  

その名の通り。移動するので、元のディレクトリから元ファイルはなくなる。ファイル名の変更にも使う。  

```bash
$ cd ~/Desktop/BioinfBootCamp/file/
file $ ls
file1.txt file2.txt file3.txt data
file $ mv file1.txt data/
file $ ls
file2.txt file3.txt
file $ cd data/
data $ ls
file1.txt
data $ mv file1.txt file4.txt
data $ ls
file4.txt
data $
```

* * *

### コピー: `cp`

使い方: `cp <filename>`  

ファイルやディレクトリを別の場所にコピーする。

| option | detail                                       |
| ------ | -------------------------------------------- |
| -i     | 上書きする前に確認する                       |
| -d     | シンボリックリンク自体をコピーする           |
| -l     | コピーの代わりにハードリンクを作成する       |
| -s     | コピーの代わりに真っボリックリンクを作成する |
| -r     | ディレクトリをコピーする                     |

```bash
$ cd ~/Desktop/BioinfBootCamp/file/
file $ ls 
file2.txt file3.txt data
file $ cp file2.txt data/
file $ ls
file2.txt file3.txt data
file $ cd data/
data $ ls
file1.txt file2.txt
data $
```

* * *

### 削除する: `rm`

使い方: `rm <filename>`  

ファイルやディレクトリを削除する。  
ゴミ箱にいくのではなく、完全に抹消されるので注意。
`-r`: ディレクトリの削除

```bash
file $ cd data
data $ ls
file1.txt file2.txt
data $ mkdir test
data $ ls
file1.txt file2.txt test
data $ rm file2.txt
data $ ls
file1.txt test
data $ rm -r test
data $ ls
file1.txt
data $
```

* * *

### 複数コマンドを走らせるパイプ: `|`

使い方: `command1 input.txt | command2`  

パイプ (`|`)の左側に書いたコマンドの標準出力(ターミナル画面に表示される結果)を取得し、右側のコマンドに渡す。

* * *

### 結果をファイルに出する: `>`

標準出力は通常ターミナルの画面に表示される。これを受け取って任意の名前のファイルに出力する。パイプも使える。

```bash
$ command1 input.txt > output.txt
$ command2 input.txt | command2 > output.txt
$
```

基本は上書き。追記したければ`>`を2個重ねる。

```bash
$ command input.txt >> output.txt
```

* * *

### ファイルを結合する: `cat`

conCATenateのcat.
結合だけでなく、ファイルの中身を表示させるのにも使う。

```bash
# merge
file $ cat file2.txt file3.txt > merged_file.txt
file $
# quick display on the prompt
$ cat file2.txt
$ cat file3.txt
$ cat merged_file.txt
$
```

catでファイルを表示させると、ファイルの中身全部がそのままターミナルに表示される。

* * *

<div style="page-break-before:always"></div>

### 大容量のファイルの中身を見る: `less`

1 GBを超えるようなファイルはテキストエディタでの表示が難しい。  
`less`で表示させたいものだけを表示させることができる。

```bash
$ less Ahal/Ahal.gff
```

`less`ウィンドウでよく使うキーファンクション

- ⇅: 上下にスクロール
- /word: そのポジション以前の部分から指定した単語を検索
- ?word: そのポジション以降の部分から指定した単語を検索
- n: 次に一致する行へ
- N: 前に一致した行へ
- q: less画面を終了

* * *

### ファイル内検索: `grep`

使い方: `grep [-civ] 'pattern' file(s)`  

指定した文字列に一致する文字列をファイル内から検索する。  
デフォルトでは、一致する文字列を含む行の全体を出力する。  
検索は大文字小文字を区別して行われる。

| option | detail                               |
| ------ | ------------------------------------ |
| -c     | 文字列が出現した行数を数えて出力する |
| -v     | 一致しないものを出力                 |
| -i     | 大文字小文字を区別しない検索         |

```bash
$ cd Ahal/
Ahal $ grep "CDS" Ahal.gff | less
Ahal $ grep -c "CDS" Ahal.gff
Ahal $ grep -vc "CDS" Ahal.gff
```

* * *

### 圧縮ファイルを操作する: `zcat`、`zless`、`zgrep`、`zdiff`

`.gz`形式で圧縮されたファイルにのみ利用可能。  
全てのコマンドにzを付ければいいわけではない。  
これらと別のコマンドをパイプで繋ぐことで、実施全てのコマンドを圧縮ファイルに利用可能。

```bash
$ zless sample_R1.fastq.gz
$ zgrep -c "^#" variants.vcf.gz
$ zless sample_R1.fastq.gz | commands
```

* * *

### 列の選択: `cut`

使い方: `cut -f <number of column> <file>`  

特定の列を切り出す。`-f`オプションでどの列を抽出するかを選択する。

| option | detail                                 |
| ------ | -------------------------------------- |
| -f     | 列を指定                               |
| -s     | 区切り文字を含む行のみを出力する       |
| -d     | 区切り文字を指定する。デフォルトはタブ |

```bash
$ cd Ahal/
Ahal $ cut -f 1,3-5,7 Ahal.gff | less
Ahal $ cut -s Ahal.gff | less
```

* * *

### 数える: `wc`

使い方: `wc [-lmc] file(s)`  

行数、文字数、データ量(byte数)を数える。

| option | detail                  |
| ------ | ----------------------- |
| -l     | 行数を数える            |
| -c     | データ量(bytes)を数える |
| -m     | 文字数を数える          |

```bash
$ cd Ahal/
Ahal $ wc -l Ahal.gff
Ahal $ wc -c Ahal.gff
```

* * *

### 文字置換、削除: `tr`

使い方: `tr [-d] character1 [character2]`  

文字1を文字2で置き換える。`-d`オプションで文字を削除する。  

よく使う正規表現

| character | mean     |
| --------- | -------- |
| `\n`      | 改行     |
| `\t`      | タブ     |
| `' '`     | スペース |

```bash
$ echo "Hello World" | tr ' ' '\n'
$ cd ~/Desktop/BioinfBootCamp/Ahal/
Ahal $ cat Ahal.gff |tr '\t' ' ' | less
Ahal $ echo "ABCDEFGHIAJKBLMNOCPQRSTU" | tr 'ABC' 'XYZ'
Ahal $ echo "ABCDEFGHIAJKBLMNOCPQRSTU" | tr -d 'ABC'
```

:warning: `tr`は文字一つ一つを1:1対応で置換する。  
`tr 'ABC' 'XYZ'`と書いたときは、ABCブロックがXYZに置換されるのではなく、文章中の全てのAがXに、BがYに、CがZにそれぞれ置換される。

* * *

### 文字列置換その2: `sed`

使い方: `sed -e 's/<before>/<after>/`　　

文字列をブロックとして置換する。`s/ABC/XYZ/`と書くと、ABCブロックがXYZに置換される。

| option | detail                         |
| ------ | ------------------------------ |
| -e     | パターンを指定                 |
| -i     | ファイルを直接編集(上書き)する |

```bash
$ echo "Hello World" | sed -e 's/Hello/Goodbye/'
$ echo "ABCDEFGHIAJKBLMNOCPQRSTU" | sed -e 's/ABC/XYZ/' -e 's/GHI/ghi/'
```

* * *

### 並び替え: `sort`

使い方: `sort [-rnu] file`

ファイル内の行をアルファベット順に並び替える。

| option | detail             |
| ------ | ------------------ |
| -r     | 逆順で並び替える   |
| -n     | 数字で並び替える   |
| -u     | ユニークな行を返す |

```bash
$ echo "10 1 2 11 100 5" | tr ' ' '\n' | sort 
$ echo "10 1 2 11 100 5" | tr ' ' '\n' | sort -n
$ echo "10 1 2 11 100 5" | tr ' ' '\n' | sort -nr
$ echo "10 12 11 12 12 11 5" | tr ' ' '\n' | sort -u
```

* * *

### 重複を除く: `uniq`

使い方: `uniq [-c] file`  

連続した重複(1行目と2行目が同じなど)を除く。連続していないと処理されない。  
:warning:まず`sort`する。  
`-c`で出現回数を数える。

```bash
$ echo "12 11 12 10 11 12 12" | tr ' ' '\n' | uniq
$ echo "12 11 12 10 11 12 12" | tr ' ' '\n' | sort | uniq
$ echo "12 11 12 10 11 12 12" | tr ' ' '\n' | sort | uniq -c
```

* * *

### 権限を操作する: `chmod`

使い方: `chmod [option] <file/directory>`

ファイルやディレクトリには

- 読み込み: read
- 書き込み: write
- 実行: excute

のアクセス権限をそれぞれ付与することができる。  
基本的に管理者ログインをしていれば全ての権限が付与されており、読み書き実行が可能なはずだが、たまに権限がなく、"Permission denied"とエラーが出ることがある。  
`chmod`コマンドで都度調整する。ファイルのアクセス権は`ls -l`コマンドで確認できる。

| option |     | discription                    |
| ------ | --- | ------------------------------ |
| u      |     | usr.ファイル所有者             |
| g      |     | group. ファイル所有グループ    |
| o      |     | other. それ以外のユーザー      |
| +      |     | アクセス権を付与する           |
| -      |     | アクセス権を剥奪する           |
| r      |     | 読み込み権限                   |
| w      |     | 書き込み権限                   |
| x      |     | 実行権限                       |
| 0      | --- | 権限なし                       |
| 1      | --x | 実行権のみあり                 |
| 2      | -w- | 書き込み権のみあり             |
| 3      | -wx | 書き込みと実行のみ権限あり     |
| 4      | r-- | 読み込み権のみあり             |
| 5      | r-x | 読み込みと実行のみ権限あり     |
| 6      | rw- | 読み込みと書き込みのみ権限あり |
| 7      | rwx | 全ての権限あり                 |

```bash
# ファイル１の読み込みと書き込みをusr/group/other全てに許可する
$ chmod 666 file1.txt
# usrにfile.txtの実行権を与える
$ chmod u+x file.txt
```

### ウェブからのダウンロード: `wget`

```
wget "https://www.dropbox.com/scl/fo/eveszlvjupqwlngbdpc32/AElC4Iss08IM_1d1-pU8bIo?rlkey=gyx7fzumqmfe4qncv2ru5hbbh&st=5wqpt0ez&dl=0" -O genome
```
