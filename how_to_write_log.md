# ログを書く <!-- omit in toc -->

Date: 27-Oct-2021

NGS解析の再現性を確保するため、ログ、つまり解析の記録を毎回必ず記録する。実験ノートの解析バージョン。
ログを書くことで、解析内容、実行内容を整理して理解するための手助けとなる。  
ここでは、マークダウン記法を用いたログの書き方を紹介する。

## Table of contents <!-- omit in toc -->

- [1. 必ず記載すること](#1-必ず記載すること)
- [2. 前回のQCを例にログを書く。](#2-前回のqcを例にログを書く)
- [3. 参考；岡田のログ](#3-参考岡田のログ)
  - [3-1. ヘッダー部分](#3-1-ヘッダー部分)
  - [4-2. ソフトウェアの説明](#4-2-ソフトウェアの説明)
  - [4-3. 設定した閾値などの説明](#4-3-設定した閾値などの説明)
- [4-4. 事前準備について](#4-4-事前準備について)
- [4-5. 使ったshell scriptとその説明](#4-5-使ったshell-scriptとその説明)
- [4-6. ターミナル上で実行したコマンド](#4-6-ターミナル上で実行したコマンド)
- [4-7. 結果のグラフ](#4-7-結果のグラフ)
- [4-8. 結果のグラフをどうやって作ったか](#4-8-結果のグラフをどうやって作ったか)
- [4-9. ターミナルの出力結果](#4-9-ターミナルの出力結果)

## 1. 必ず記載すること

- 解析のテーマ
- 解析した日
- 使ったデータ
- 使用したソフトウェアとそのバージョン
- 使用したスクリプト
- 打ち込んだコマンド
- どのファイルに結果が保存されているか

## 2. 前回のQCを例にログを書く。

前回やったこと

1. シロイヌナズナのデータ４サンプル分を解析
2. Trimmomatic v0.39を使って不要なリードを除いた
3. ４サンプル分のTrimmomaticの実行はshell scriptを使用して自動で行った
4. 結果をperl/pythonで整形
5. Rでグラフを作った

これをマークダウン記法を使って記録し、ログとして残す。さらに、GitHubにアップロードする。

基本的に、最低限のことが書かれていれば書き方は自由。
ログを書くためのテキストエディタは何でも良いが、markdown記法を使うなら、プレビューを見ながら操作できるVisual Studio Codeが便利。

## 3. 参考；岡田のログ

参考までに、岡田のログの書き方を紹介する。
説明の都合上部分ごとに分けたが、全て１枚のファイルに記載している。

### 3-1. ヘッダー部分

解析に関する基本情報(ログのタイトル、日付、プロジェクト名、全体の要約など)を記載する。

```text
# リードのクオリティコントロール

Project: BioinfBootCamp
Date: 20-Oct-2021

Trimmomatic v0.39を用いてアダプター配列や信頼性の低いリードを除去する
```

### 4-2. ソフトウェアの説明

論文を書くときにすぐに引用文献に当たれるように、ソフトウェアに関する情報を書いておく

```text
## Trimmomaticについて

一般的なリードトリミング用ソフトウェア。2021年10月７日時点でv0.39が最新。

Reference: [Bolger et al. _Bioinformatics_ 2014](https://academic.oup.com/bioinformatics/article/30/15/2114/2390096>)

Download: [公式ページ](http://www.usadellab.org/cms/?page=trimmomatic)よりバイナリでダウンロード
```

### 4-3. 設定した閾値などの説明

各自で設定した値がある場合は、各値の説明とともに設定値を記載する。

```text
## 閾値など

- アダプター配列のファイル: TruSeq-PE-all.fa
- 許容ミスマッチ数: 2
- アダプター配列とパリンドローム構造の一致度: 30
- アダプター配列との一致度: 10
- スライディングウィンドウサイズ: 4
- クオリティ値: 30
- 最短リード長: 50
```

## 4-4. 事前準備について

ディレクトリ作成など、事前準備をしたのであれば、それについて記述しても良い。

````text
## ディレクトリの作成

ターミナルにて、必要なディレクトリを作成した。

```bash
$ cd ~/Documents/okada
okada $ ls
Ahal  Ath file
okada $ mkdir DNAseq
okada $ ls
Ahal Ath DNAseq file
okada $ cd DNAseq
DNAseq $ mkdir script
DNAseq $ mkdir result
DNAseq $ ls
script	result
DNAseq $
```
````

## 4-5. 使ったshell scriptとその説明

４サンプルをshell scriptで行ったので、そのshell script全文と保存場所を記載。必要なら説明も書く。

````text
## QCの実行

`1_QC.sh`を用いてTrimmomaticによる処理を4サンプルに対し自動で行った。
この`1_QC.sh`は、`~/Documents/okada/DNAseq/script/`以下に保存した。

### 1_QC.shの内容

```shell
#!/bin/sh

# 1_QC.sh
# Date: 20-Oct-2021
# BioinfBootCamp_QualityControl

# softwares
## Trimmomatic v0.39

PROJECT=/Users/spe/Documents/okada/
DATA=${PROJECT}/Ath/data
CLEAN=${PROJECT}/DNAseq/clean

# dataディレクトリに移動
cd ${DATA}
# 確認のためにパスを表示
pwd

# cleanディレクトリを作成
mkdir ${CLEAN}

# 全４サンプルにTrimmomaticを実行
for file in `ls *_R1.fastq.gz`; do
  # "_R1.fastq.gz"で終わるファイルのファイル名を取得し、"file"という変数として設定する。
  # フォルダ内の全てのファイルに対して行われ、１つずつ処理される

  filename=`basename ${file} _R1.fastq.gz`
    # ファイル名から"_R1.fastq.gz"以外の部分を取得し、"filename"という変数として設定する。
  echo ${filename}
    # 確認のために、"filename"を表示させる

  # Trimmomaticの実行
  java -jar /Users/spe/src/Trimmomatic-0.39/trimmomatic-0.39.jar PE \
    -phred33 -trimlog ${CLEAN}/${filename}.log \
    ${file} ${filename}_R2.fastq.gz \
    ${CLEAN}/${filename}_R1_pair.fastq.gz ${CLEAN}/${filename}_R1_unpair.fastq.gz \
    ${CLEAN}/${filename}_R2_pair.fastq.gz ${CLEAN}/${filename}_R2_unpair.fastq.gz \
    ILLUMINACLIP:/Users/spe/src/Trimmomatic-0.39/adapters/TruSeq-PE_all.fa:2:30:10 \
	SLIDINGWINDOW:4:30 MINLEN:50
done

# 確認として「終わったよ」と表示させる
echo "finish quality control"
```
````

## 4-6. ターミナル上で実行したコマンド

ターミナルでどのようなコマンドを打ったかを控えておく。

````text
`1_QC.sh`には実行権がなかったため、`chmod`コマンドで実行権を付加してから実行した。

```bash
DNAseq $ cd script
script $ ls -l 1_QC.sh
-rw-r--r--@ 1 spe  staff  920 Oct 20 16:34 1_QC.sh
script $ chmod u+x 1_QC.sh
script $ ls -l
-rwxr--r--@ 1 spe  staff  920 Oct 20 16:34 1_QC.sh
script $ ./1_QC.sh
・
・
・
script $
```
````

## 4-7. 結果のグラフ

何かグラフなどの結果が出ていれば、グラフを貼り付け、結果から考えたことを記載する

```text
## クオリティコントロールの結果

<img src=/user/spe/Documents/okada/DNAseq/result/QCresult.png width=49%> <img src=/user/spe/Documents/okada/DNAseq/result/QCresult_percent.png width=49%>

75%超のリードペアが高品質のリードとして出力されている。リードデータの質としては問題がなさそうなので、次の解析に進む。
```

## 4-8. 結果のグラフをどうやって作ったか

グラフを作る際に必要なスクリプトがあれば、全文を記載する。必要なら説明も付け加える。

````text
### 上記プロットの作り方

ターミナルに出力されるQC結果を保存したテキストファイル(末尾の"ターミナルに出力された結果"部分と同じ)から、必要な数値のみをperlを使って取り出し、Rで作図する。

#### 1. perlを使った出力結果の整形

1-1_ModifyQCresults_PE.plを使用し、必要な数値のみを取り出す。

##### 1-1_ModifyQCresults.pl

```perl
#!/usr/bin/perl -w
use strict;

#20211008_okada
#usage: perl 1-1_ModifyQCresults_PE.pl input output

# コマンドラインの引数を受け取る
my $input = $ARGV[0];
my $output = $ARGV[1];

# 配列(アレイ)を作成
push (my @name, "sample \t");
push (my @both, "Both surviving\t");
push (my @F, "Forward only surviving\t");
push (my @R, "Reverse only surviving\t");
push (my @Drop, "Dropped\t");

# ファイルの読み込みと出力ファイルの作成
open (IN, $input) or die "cannot open results";
open(DEST, ">$output") or die "cannot create outputPE";

while (<IN>){ # while: 要素１つずつに{}で囲まれた内容を適用する
  # <>: ファイルを1行ずつ読み込んで処理する
  chomp;  # 改行コードを除く
  @_ = $_;  # 1行を１要素として扱う
  if (/^sample/){ # 行の先頭(^)が"sample"という文字列と一致していたら ("Sample"の箇所は、処理するファイル名やサンプル名に応じて変更する)
    my $sample = $_;  # 1行全体を"sample"という名前の要素とする
    push (@name, "$sample\t");  # @nameのアレイに要素"sample"を格納する
  }elsif (/^Input\sRead\sPairs/){ # もし、行の先頭(^)が "Input Read Pairs"という文字列に一致していれば
    push (@both, "$2\t") if (/(Both\sSurviving:.)(.*)(\s\(\d+\.\d+\%\)\s)(Forward\sOnly)/); # "Both Surviving: "の後ろの数値を@bothに格納
    push (@F, "$2\t") if (/(Forward\sOnly\sSurviving:.)(.*)(\s\(\d+\.\d+\%\)\s)(Reverse\sOnly)/); # "Forward Only Surviving: "の後ろの数値を@Fに格納
    push (@R, "$2\t") if (/(Reverse\sOnly\sSurviving:.)(.*)(\s\(\d+\.\d+\%\)\s)(Dropped)/); # "Reverse Only Surviving: "の後ろの数値を@Rに格納
    push (@Drop, "$2\t") if (/(Dropped:.)(.*)(\s\(.*\))/);   # "Dropped: "の後ろの数値を@Dropに格納
  }else{  # 上記の２つともに一致しなければ
      next; # 無視して次の行に進む
  }
}

# 全ての行を処理し終えたら、各アレイをアウトプットファイルに出力する
print DEST "@name\n@both\n@F\n@R\n@Drop\n";

close IN;
close DEST;
```

ターミナルで実行する。

```bash
DNAseq $ perl scripts/1-1_ModifyQCresults_PE.pl results/QCresults.txt results/QCresults_mod.txt
DNAseq $ cat QCresults_mod.txt
sample 	 Sample1	 Sample2	Sample3	 Sample4
Both surviving	 23198487	 21828130	 18452117	 23310316
Forward only surviving	 2011292	 1685497	 1608186	 1799890
Reverse only surviving	 2404960	 2342742	 1965307	 2454842
Dropped	 2320633	 2036949	 1884824	 2121644
DNAseq $
```

#### 2. Rでの作図

`QCresults_mod.txt`を入力ファイルとして、`1-2_BarPlot_QCresult.R`を使って縦軸が実数のグラフと縦軸が割合のグラフ、２通りの積み上げ棒グラフを作る。

##### 2-2_BarPlot_QCresult.R

```R
# ライブラリの読み込み
library(ggplot2)
library(tidyr)
library(dplyr)
library(scales)

# コマンドライン引数を使用
args <- commandArgs(trailingOnly = T)

# データの読み込み
data <- read.table(args[1], header = T, sep = "\t")
# 余分な列を削除
data <- data[, -6]
head(data)

# フォーマットの変更(縦長にする)
data2 <- pivot_longer(data, -sample, names_to = "variable", values_to = "values")
head(data2)

# 積み上げ棒グラフの積み上げ順を指定する
data2$category <- factor(data2$sample, levels = c("Dropped", "Reverse only surviving", "Forward only surviving", "both surviving")) # nolint
head(data2)

# 色を指定する
Colours1 <- c("#7B7B7B", "#629d62", "#4eb14e", "#039BE5") # nolint

# 実数でのプロット
p1 <- ggplot(data2, aes(x = variable, y = values)) +
    geom_bar(stat = "identity", aes(fill = category))
p1 <- p1 + theme(
    panel.background = element_rect(fill = "transparent", color = "black"),
    panel.grid.minor = element_line(color = NA),
    panel.grid.major = element_line(color = "grey"),
    axis.text.x = element_text(size = 12, angle = 90, hjust = 1, color = "black", vjust = 0.5), # nolint
    axis.text.y = element_text(size = 12, color = "black"),
    axis.title.x = element_blank(),
    axis.title.y = element_blank()
) + scale_y_continuous(expand = c(0, 0)) +
    scale_fill_manual(values = Colours1)
p1
ggsave(args[2], dpi = 200, bg = "transparent")

# 割合でのプロット
p2 <- ggplot(data2, aes(x = variable, y = values)) +
    geom_bar(stat = "identity", aes(fill = category), position = "fill")
p2 <- p2 + theme(
    panel.background = element_rect(fill = "transparent", color = "black"),
    panel.grid.minor = element_line(color = NA),
    panel.grid.major = element_line(color = "grey"),
    axis.text.x = element_text(size = 12, angle = 90, hjust = 1, color = "black", vjust = 0.5), # nolint
    axis.text.y = element_text(size = 12, color = "black"),
    axis.title.x = element_blank(),
    axis.title.y = element_blank()
) +
    scale_y_continuous(labels = percent, expand = c(0, 0)) +
    scale_fill_manual(values = Colours1)
p2
ggsave(args[3], dpi = 200, bg = "transparent")
```
````

## 4-9. ターミナルの出力結果

データに関わる部分は、テキストとして保存していても、何かの拍子に失われる可能性があるので、ログにも記載しておく。

````text
### 参考 1. ターミナルに出力されたQC結果全文

```bash
hpc@ubuntu1804:/data/Okada/scripts$ ./1_ApomicsisRNAseq.sh
Kot-SlmOv-1
TrimmomaticPE: Started with arguments:
-phred33 -trimlog Kot-SlmOv-1.log Kot-SlmOv-1_1.fastq.gz Kot-SlmOv-1_2.fastq.gz /data/Okada/clean/Kot-SlmOv-1_1_pair.fastq.gz /data/Okada/clean/Kot-SlmOv-1_1_unpair.fastq.gz /data/Okada/clean/Kot-SlmOv-1_2_pair.fastq.gz /data/Okada/clean/Kot-SlmOv-1_2_unpair.fastq.gz ILLUMINACLIP:/home/hpc/src/Trimmomatic-0.39/adapters/TruSeq-PE-all.fa:2:30:10 SLIDINGWINDOW:4:30 MINLEN:50
Using PrefixPair: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT' and 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTA'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTAGATCTCGGTGGTCGCCGTATCATT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGGTTCAGCAGGAATGCCGAGACCGATCTCGTATGCCGTCTTCTGCTTG'
Using Long Clipping Sequence: 'TTTTTTTTTTAATGATACGGCGACCACCGAGATCTACAC'
Using Long Clipping Sequence: 'TTTTTTTTTTCAAGCAGAAGACGGCATACGA>PrefixPE/1TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC'
Using Long Clipping Sequence: 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'CAAGCAGAAGACGGCATACGAGATCGGTCTCGGCATTCCTGCTGAACCGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AATGATACGGCGACCACCGAGATCTACACTCTTTCCCTACACGACGCTCTTCCGATCT'
ILLUMINACLIP: Using 1 prefix pairs, 10 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
Input Read Pairs: 29935372 Both Surviving: 23198487 (77.50%) Forward Only Surviving: 2011292 (6.72%) Reverse Only Surviving: 2404960 (8.03%) Dropped: 2320633 (7.75%)
TrimmomaticPE: Completed successfully
Kot-SlmOv_pls1
TrimmomaticPE: Started with arguments:
-phred33 -trimlog Kot-SlmOv_pls1.log Kot-SlmOv_pls1_1.fastq.gz Kot-SlmOv_pls1_2.fastq.gz /data/Okada/clean/Kot-SlmOv_pls1_1_pair.fastq.gz /data/Okada/clean/Kot-SlmOv_pls1_1_unpair.fastq.gz /data/Okada/clean/Kot-SlmOv_pls1_2_pair.fastq.gz /data/Okada/clean/Kot-SlmOv_pls1_2_unpair.fastq.gz ILLUMINACLIP:/home/hpc/src/Trimmomatic-0.39/adapters/TruSeq-PE-all.fa:2:30:10 SLIDINGWINDOW:4:30 MINLEN:50
Using PrefixPair: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT' and 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTA'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTAGATCTCGGTGGTCGCCGTATCATT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGGTTCAGCAGGAATGCCGAGACCGATCTCGTATGCCGTCTTCTGCTTG'
Using Long Clipping Sequence: 'TTTTTTTTTTAATGATACGGCGACCACCGAGATCTACAC'
Using Long Clipping Sequence: 'TTTTTTTTTTCAAGCAGAAGACGGCATACGA>PrefixPE/1TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC'
Using Long Clipping Sequence: 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'CAAGCAGAAGACGGCATACGAGATCGGTCTCGGCATTCCTGCTGAACCGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AATGATACGGCGACCACCGAGATCTACACTCTTTCCCTACACGACGCTCTTCCGATCT'
ILLUMINACLIP: Using 1 prefix pairs, 10 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
Input Read Pairs: 27893318 Both Surviving: 21828130 (78.26%) Forward Only Surviving: 1685497 (6.04%) Reverse Only Surviving: 2342742 (8.40%) Dropped: 2036949 (7.30%)
TrimmomaticPE: Completed successfully
SlmOv-1
TrimmomaticPE: Started with arguments:
-phred33 -trimlog SlmOv-1.log SlmOv-1_1.fastq.gz SlmOv-1_2.fastq.gz /data/Okada/clean/SlmOv-1_1_pair.fastq.gz /data/Okada/clean/SlmOv-1_1_unpair.fastq.gz /data/Okada/clean/SlmOv-1_2_pair.fastq.gz /data/Okada/clean/SlmOv-1_2_unpair.fastq.gz ILLUMINACLIP:/home/hpc/src/Trimmomatic-0.39/adapters/TruSeq-PE-all.fa:2:30:10 SLIDINGWINDOW:4:30 MINLEN:50
Using PrefixPair: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT' and 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTA'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTAGATCTCGGTGGTCGCCGTATCATT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGGTTCAGCAGGAATGCCGAGACCGATCTCGTATGCCGTCTTCTGCTTG'
Using Long Clipping Sequence: 'TTTTTTTTTTAATGATACGGCGACCACCGAGATCTACAC'
Using Long Clipping Sequence: 'TTTTTTTTTTCAAGCAGAAGACGGCATACGA>PrefixPE/1TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC'
Using Long Clipping Sequence: 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'CAAGCAGAAGACGGCATACGAGATCGGTCTCGGCATTCCTGCTGAACCGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AATGATACGGCGACCACCGAGATCTACACTCTTTCCCTACACGACGCTCTTCCGATCT'
ILLUMINACLIP: Using 1 prefix pairs, 10 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
Input Read Pairs: 23910434 Both Surviving: 18452117 (77.17%) Forward Only Surviving: 1608186 (6.73%) Reverse Only Surviving: 1965307 (8.22%) Dropped: 1884824 (7.88%)
TrimmomaticPE: Completed successfully
SlmOv_pls1
TrimmomaticPE: Started with arguments:
-phred33 -trimlog SlmOv_pls1.log SlmOv_pls1_1.fastq.gz SlmOv_pls1_2.fastq.gz /data/Okada/clean/SlmOv_pls1_1_pair.fastq.gz /data/Okada/clean/SlmOv_pls1_1_unpair.fastq.gz /data/Okada/clean/SlmOv_pls1_2_pair.fastq.gz /data/Okada/clean/SlmOv_pls1_2_unpair.fastq.gz ILLUMINACLIP:/home/hpc/src/Trimmomatic-0.39/adapters/TruSeq-PE-all.fa:2:30:10 SLIDINGWINDOW:4:30 MINLEN:50
Using PrefixPair: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT' and 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTA'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTAGATCTCGGTGGTCGCCGTATCATT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCGGTTCAGCAGGAATGCCGAGACCGATCTCGTATGCCGTCTTCTGCTTG'
Using Long Clipping Sequence: 'TTTTTTTTTTAATGATACGGCGACCACCGAGATCTACAC'
Using Long Clipping Sequence: 'TTTTTTTTTTCAAGCAGAAGACGGCATACGA>PrefixPE/1TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC'
Using Long Clipping Sequence: 'GTGACTGGAGTTCAGACGTGTGCTCTTCCGATCT'
Using Long Clipping Sequence: 'TACACTCTTTCCCTACACGACGCTCTTCCGATCT'
Using Long Clipping Sequence: 'CAAGCAGAAGACGGCATACGAGATCGGTCTCGGCATTCCTGCTGAACCGCTCTTCCGATCT'
Using Long Clipping Sequence: 'AATGATACGGCGACCACCGAGATCTACACTCTTTCCCTACACGACGCTCTTCCGATCT'
ILLUMINACLIP: Using 1 prefix pairs, 10 forward/reverse sequences, 0 forward only sequences, 0 reverse only sequences
Input Read Pairs: 29686692 Both Surviving: 23310316 (78.52%) Forward Only Surviving: 1799890 (6.06%) Reverse Only Surviving: 2454842 (8.27%) Dropped: 2121644 (7.15%)
TrimmomaticPE: Completed successfully
finish trimmomatic
```
````
