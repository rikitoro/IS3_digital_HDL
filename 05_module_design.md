# IS3 ディジタル技術

## HDL実習

## モジュールを組み合わせた回路設計

力武克彰、武田正則、菅谷純一

---
## モジュールによる回路設計

Verilog HDLでは、これまでに設計してきたような
様々な回路のモジュールを組み合わせてより大規模な回路を
設計することができます。

---
## 加算器と7セグメントデコーダを組み合わせた回路の設計

スイッチから入力された2つの4ビットの数a, bを
加算する回路を設計します。ただし、加算した結果の繰上りは無視するものとします。
入力された数aとb、およびそれらの数を加算した結果を、
それぞれ7セグメントLEDへ16進数で表示します。

この回路のモジュール名を hex_adder とします。
加算器と7セグメントデコーダを組み合わせて hex_adder モジュールを
設計していきます。

---
### 下位モジュールの設計

まず top level entity 名を hex_adder としたプロジェクトを作成し、
下記に示す、4ビット加算器のモジュール adder と、
7セグメントデコーダのモジュール seven_segment_decoder を
プロジェクトに登録しましょう。
それぞれ、adder.v、seven_segment_decoder.vとファイル名を付けて
保存しておくとよいです。

#### 4ビット加算器 adder

```verilog
`default_nettype none

module adder(
  input   wire  [3:0] x0,
  input   wire  [3:0] x1,
  output  wire  [4:0] y
  );

  assign y = x1 + x0;
endmodule
```

#### 7セグメントデコーダ seven_segment_decoder

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

### 上位モジュールの設計

2種類のモジュールseven_segment_decoder、register4 を組合せて
calculator を設計します。
プロジェクトに下記の hex_adder モジュールを登録してください。

hex_adder モジュールを実習ボードに実装し、その動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|a[3:0]|SW7-4|
|b[3:0]|SW3-0|
|hex_a[6:0]|HEX36-30|
|hex_b[6:0]|HEX26-20|
|hex_sum[6:0]|HEX06-00|

```verilog
`default_nettype none

module hex_adder(
  input   wire  [3:0] a,
  input   wire  [3:0] b,
  output  wire  [6:0] hex_a,
  output  wire  [6:0] hex_b,
  output  wire  [6:0] hex_sum
);

  wire [4:0] sum;

  seven_segment_decoder sseg_a(
    .num(a),
    .y(hex_a)
  );

  seven_segment_decoder sseg_b(
    .num(b),
    .y(hex_b)
  );

  seven_segment_decoder sseg_sum(
    .num(sum[3:0]),
    .y(hex_sum)
  );

  adder adder(
    .x0(a),
    .x1(b),
    .y(sum)
  );

endmodule
```



---
## 4ビット電卓の設計

次は、スイッチより入力された4ビットの数を、
クロック信号(の立上り)が入る度に取り込み、
取り込んだ数を次々に加算してその結果を7セグメントLEDに表示する、
電卓のような働きをする回路を設計します。

この回路のモジュール名を calculator とします。
加算器と7セグメントデコーダに加え、
レジスターとを組み合わせて calculator モジュールを
設計していきます。

---
### 下位モジュールの設計

まず top level entity 名を calculator としたプロジェクトを作成し、
先ほど示した、4ビット加算器のモジュール adder と、
7セグメントデコーダのモジュール seven_segment_decoder を
プロジェクトに登録します。
さらに、下記に示す4ビットレジスタモジュール register4 を
プロジェクトに追加します。

#### 4ビットレジスタ register4

```verilog
`default_nettype none

module register4(
  input   wire        clock,
  input   wire  [3:0] data,
  output  reg   [4:0] q
  );

  always @ (posedge clock) begin
    q <= data;
  end
endmodule
```

---
### 上位モジュールの設計

3種類のモジュール adder、seven_segment_decoder、register4 を組合せて
calculator を設計します。
プロジェクトに下記の calculator モジュールを登録してください。

calculator モジュールを実習ボードに実装し、その動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|a[3:0]|SW3-0|
|hex_led[6:0]|HEX06-00|

```verilog
`default_nettype none

module calculator(
  input   wire        clock,
  input   wire  [3:0] a,
  output  wire  [6:0] hex_led
  );

  wire [4:0] sum;    // next state (sum[3:0])
  wire [3:0] result; // current state

  adder adder(
    .x0(a),
    .x1(result),
    .y(sum)
  ); // next state function

  register4 register(
    .clock(clock),
    .data(sum[3:0]),
    .q(result)
  ); // register to keep current state

  seven_segment_decoder sseg_decoder(
    .num(result),
    .y(hex_led)
  ); // output function

endmodule
```
