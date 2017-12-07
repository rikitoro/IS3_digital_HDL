# IS3 ディジタル技術

## HDL実習

## 実習ボード、開発ツールの使い方

力武克彰、武田正則、菅谷純一

---

## 実習ボードについて

本実習では ALTERA社のFPGAチップCyclone Vが搭載された
DE0-CV開発ボードを使います。

スライドスイッチ(SW9-0)、プッシュスイッチ(KEY0-4)、
LED(LEDR9-0)、7セグメントLED(HEX5-0)、GPIOなど多くのI/Oを
備えたボードです。

[http://cd-de0-cv.terasic.com](http://cd-de0-cv.terasic.com)
より、ユーザーマニュアルをダウンロードしておいてください。

---

## 開発ツールについて

本実習では回路設計を行うための
EDA(electronic design automation)ツールとして
Quartus Prime Lite Edition を利用します。

[インテル Quartus Prime](https://www.altera.co.jp/products/design-software/fpga-design/quartus-prime/download.html)

---

# 実習ボード・開発ツールの使い方

実習ボードのスライドスイッチSW9-0に入力された0/1のビットパターンを
そのままLEDのLED9-0に表示する回路の設計実装を行う実習を通して、
実習ボードと開発ツールの使い方を学んでいきましょう。

![DE0-CV](./assets/DE0_CV_simple_io.png)

---
## Directory, Name, Top-Level Entityの設定

Quartus Prime Lite Editionを起動します。

起動したら、[File]>[New Project Wizard]
よりプロジェクト作成のためのウィザードを起動し、下記を設定します。
- working directory (プロジェクトフォルダが置かれる作業フォルダ)
- project (プロジェクト名)
- top-level entity (PIN接続される一番外側の回路モジュール名)

設定例:
- working directory: Z:/digital/working_directory
- project: simple_io
- top-level entity: simple_io

---

## Project Typeの設定

今回はEmpty projectを選択してください。

---
## Add Filesの設定

既に作成したデザインファイルやライブラリを
プロジェクトに取り込みたい場合に設定します。

今回は何も追加せずに次に進んでください。

---
## Family, Device & Board Settings

開発対象のデバイス(FPGAチップ)を選択します。

今回は Cyclone V 5CEBA4F23C7 を選択します。

---
## EDA Tool Settings

シミュレータなど他のEDAツールを使う場合の設定です。

今回はそのままで次に進んでください。

---
## Summary

これまでの設定をここで確認します。
問題なければ[Finish]を押してください。
プロジェクトのひな型が作成されます。

---
## デザインファイルの作成
[File]>[New]>[Design Files/VerilogHDL]を選択します。

エディタが立ち上がりますので、下記のプログラムを作成し、
適切なファイル名を付けて保存します。
ファイル名は simple_io.v の様に拡張子を .v としてください。

なお、モジュール名(module の後についている名前)は
top-level entityと同じにする必要があります。

````verilog
`default_nettype none

module simple_io(
    input wire  [9:0] sw,
    output wire [9:0] led
);

    assign led = sw;

endmodule
````

---
## デザインファイルの解析

[Processing]>[Start]>[Start Analysis & Elaboration]
を選択し、デザインファイルの解析を行います。

下記の様に表示されれば大丈夫です。

```
Info: Quartus Prime Analysis & Elaboration was successful.
```

デザインファイルに誤りがあると下記の様なエラー表示がでます。
修正を行ったうえで再度[Start Analysis & Elaboration]を行ってください。

```
Error (10161): Verilog HDL error at simple_io.v(8): ...
```

---
## Pin plannerによるピンの設定

[Assignments]>[Pin Planner]を選択してPin Plannerを起動します。

作成した simple_io モジュールの入出力ポートが適切なI/Oデバイスに
接続されるよう、ピンの割り当てを行います。

今回はled[9]-led[0]がボードのLEDR9-0、sw[9]-[0]がSW9-0に接続されるように下記のとおりピンの設定を行います。

|Node Name|Location|
|:---|:---|
|led[0]|PIN_AA2|
|led[1]|PIN_AA1|
|led[2]|PIN_W2|
|led[3]|PIN_Y3|
|led[4]|PIN_N2|
|led[5]|PIN_N1|
|led[6]|PIN_U2|
|led[7]|PIN_U1|
|led[8]|PIN_L2|
|led[9]|PIN_L1|
|sw[0]|PIN_U13|
|sw[1]|PIN_V13|
|sw[2]|PIN_T13|
|sw[3]|PIN_T12|
|sw[4]|PIN_AA15|
|sw[5]|PIN_AB15|
|sw[6]|PIN_AA14|
|sw[7]|PIN_AA13|
|sw[8]|PIN_AB13|
|sw[9]|PIN_AB12|

実習ボードDE0-CVにおいて、
各ピンがどのI/Oデバイスに接続されているかは
ユーザーマニュアルを確認してください。

---
## プロジェクトのコンパイル

[Processing]>[Start Compilation]を選択して、
プロジェクトのコンパイルを行います。

しばらく時間がかかります。下記の様に表示されるとコンパイル成功です。

```
Info (293000): Quartus Prime Full Compilation was successful.
```

---
## デバイスへの回路情報の書き込み

コンパイルで作成された回路情報を実習ボードに書き込みます。

まず、PCと実習ボードDE0-CVをUSBケーブルで接続し、実習ボードの電源を入れます。
SW10はRUNに設定しておいてください。

Quartus Primeの方で、[Tools]>[Programmer]を選択しProgrammerを起動します。

- [Hardware Setup]でUSB-Blaster[USB-0]を選択
- ModeはJTAGを選択

Fileが空欄の場合は[Add File]よりoutput_files/下にあるsofファイルを選び追加してください。

sofファイルのProgram/Configureにチェックを入れてください。

この状態で[Start]をクリックすると、書き込みが開始されます。
Progressが100%になると書き込み完了です。

---
## 動作確認

実習ボードDE0-CVのスライドスイッチSW9-0をいろいろと切り替えて、
LEDR9-0がどの様に点灯するかを確認しましょう。

---
## 回路の修正その1

回路のデザインファイルを下記の様に修正し、
再度、プロジェクトのコンパイルとデバイスへの書き込みを行いましょう。
(回路モジュールの入出力に変化がなく、ピンの割り当ての変更がないので、
Pin plannerによるピンの再設定は必要ありません)

回路の動作はどの様に変化するでしょうか？

````verilog
`default_nettype none

module simple_io(
    input wire  [9:0] sw,
    output wire [9:0] led
);

    assign led = sw[9:5] & sw[4:0]; // 修正

endmodule
````

---
## 回路の修正その2

回路のデザインファイルを下記の様に修正し、
再度、プロジェクトのコンパイルとデバイスへの書き込みを行いましょう。

回路の動作はどの様に変化するでしょうか？

````verilog
`default_nettype none

module simple_io(
    input wire  [9:0] sw,
    output wire [9:0] led
);

    assign led = sw[9:5] + sw[4:0]; // 修正

endmodule
````

---
## プロジェクトの保存

[File]>[Save Project]より、プロジェクトの保存ができます。

次回以降、保存したプロジェクトを利用したいときは
[File]>[Open Project]からプロジェクト名の付いたqpfファイルを選択します。

---
# Q & A