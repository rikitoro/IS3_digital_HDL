# IS3 ディジタル技術

## HDL実習

## 複雑な組合せ回路(エンコーダ・デコーダ・マルチプレクサ)

力武克彰、武田正則、菅谷純一

---
## デコーダ・エンコーダ・マルチプレクサ

Verilog HDLではcase文により、デコーダやエンコーダ、マルチプレクサ
等の複雑な制御回路(組合せ回路)を設計することができます。

---
### エンコーダ

my_encoderモジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|data[3:0]|SW3-0|
|y[1:0]|LEDR1-0|
|y[2]|LEDR9|


```verilog
`default_nettype none

module my_encoder(
  input   wire  [3:0] data,
  output  reg   [2:0] y
  );

  always @ (*) begin
    case (data)
      4'b0001: y = 3'b0_00;
      4'b0010: y = 3'b0_01;
      4'b0100: y = 3'b0_10;
      4'b1000: y = 3'b0_11;
      default: y = 3'b1_00;
    endcase
  end

endmodule
```

always文のセンシティビティリストに(*)を指定すると、
入力信号のいずれかに変化があった時にalways文の中身が実行されます。
これによりalways文を使って組合せ回路を設計することができます。

case文はalways文の中で使用することができます。
dataの値(パターン)に応じてレジスタ型変数yへの代入値が変わります。
ここでyがレジスタ型変数になっていますが、
実際にできるのはレジスタを含まない組合せ回路になります。

case文で組合せ回路を設計する際には
全ての入力パターンを網羅するようにしましょう。
入力パターンに抜けがあると、組合せ回路ではなく
ラッチ回路がつくられてしまう可能性があります。
全ての入力パターンをつくせるように、
必要に応じてdefaultを利用するとよいでしょう。

---

### 7セグメントデコーダ

seven_segment_decoderモジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|num[3:0]|SW3-0|
|y[6:0]|HEX06-00|

```verilog
`default_nettype none

module seven_segment_decoder(
  input   wire  [3:0] num,
  output  reg   [6:0] y
  );

  always @ (*) begin
    case (num)
      4'h0: y = 7'b100_0000;
      4'h1: y = 7'b111_1001;
      4'h2: y = 7'b010_0100;
      4'h3: y = 7'b011_0000;
      4'h4: y = 7'b001_1001;
      4'h5: y = 7'b001_0010;
      4'h6: y = 7'b000_0010;
      4'h7: y = 7'b101_1000;
      4'h8: y = 7'b000_0000;
      4'h9: y = 7'b001_0000;
      4'ha: y = 7'b000_1000;
      4'hb: y = 7'b000_0011;
      4'hc: y = 7'b010_0111;
      4'hd: y = 7'b010_0001;
      4'he: y = 7'b000_0110;
      4'hf: y = 7'b000_1110;
    endcase
  end
endmodule
```


---
### マルチプレクサ

my_mux4モジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|d3[1:0]|SW7-6|
|d2[1:0]|SW5-4|
|d1[1:0]|SW3-2|
|d0[1:0]|SW1-0|
|sel[1:0]|SW9-8|
|y[1:0]|LEDR1-0|

```verilog
`default_nettype none

module my_mux4(
  input   wire  [1:0] d3,
  input   wire  [1:0] d2,
  input   wire  [1:0] d1,
  input   wire  [1:0] d0,
  input   wire  [1:0] sel,
  output  reg   [1:0] y
  );

  always @ (*) begin
    case (sel)
      2'b00: y = d0;
      2'b01: y = d1;
      2'b10: y = d2;
      2'b11: y = d3;
    endcase
  end
endmodule
```


