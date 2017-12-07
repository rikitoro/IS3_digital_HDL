# IS3 ディジタル技術

## HDL実習

## 基本的な回路(レジスタ・カウンタ)

力武克彰、武田正則、菅谷純一

---
## レジスタ・カウンタ

Verilog HDLでは順序回路を設計することができます。
まずは基本的な順序回路であるレジスタやカウンタの
設計手法を学びましょう。

---
### 4ビットレジスタ

register4モジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|data[3:0]|SW3-0|
|q[3:0]|LEDR3-0|

```verilog
`default_nettype none

module register4(
  input   wire        clock,
  input   wire  [3:0] data,
  output  reg   [3:0] q
  );

  always @ (posedge clock) begin
    q <= data;
  end

endmodule
```

クロックなどの特定の信号に同期して状態や出力が変化する
順序回路はalways文を用いて設計することができます。

always文は@以下のセンシティビティリストに示された信号に変化が
あったタイミングで実行されます。
立上り、立下りのどちらのタイミングで実行するかは
信号名にposedgeもしくはnegedgeをつけることで指定できます。
always文中ではレジスタ型の変数への値の代入を行うことができます。

register4モジュールの例では、clockの立上りのタイミングで、
4ビットの入力dataの値を4ビットのレジスタ型変数qに取り込む
回路が構築されます。


---
### 4ビットカウンタ

counterモジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|count[3:0]|LEDR3-0|

```verilog
`default_nettype none

module counter4(
  input   wire        clock,
  output  reg   [3:0] count
  );

  always @ (posedge clock) begin
    count <= count + 1'b1;
  end

endmodule
```

clockの立上りをカウントし、カウント数をcountへ出力します。

---
---
### シフトレジスタ

shift_registerモジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|data|SW0|
|q[3:0]|LEDR3-0|

```verilog
`default_nettype none

module shift_register(
  input   wire        clock,
  input   wire        data,
  output  reg   [3:0] q
  );

  always @ (negedge clock) begin
    q <= {q[2:0], data};
  end

endmodule
```

センシティビティリストにnegedge clockと
指定していますので、
clockの立下りのタイミングでレジスタ変数qの値が更新されます。
レジスタ変数qへの代入の右辺では、ビット連結演算子{,}を
使って、4ビット信号qの下位3ビット分q[2:0]と
1ビット信号dataを結合しています。
この記述により、レジスタ変数qに保持されたデータが左に1bitシフトし、
qのLSBにはdataの値が取り込まれます。

---
## if文

always文の中ではif文を使うことができます。
これにより、入力や状態によって
振舞いを切り替えるような順序回路を設計できます。

---
### 書き込み許可付きレジスタ

register4_we モジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|data[3:0]|SW3-0|
|we|SW9|
|q[3:0]|LEDR3-0|

```verilog
`default_nettype none

module register4_we(
  input   wire        clock,
  input   wire  [3:0] data,
  input  wire      we, // write enable
  output  reg   [3:0] q
  );

  always @ (posedge clock) begin
    if (we == 1'b1) begin
      q <= data;
    end else begin
      q <= q;
    end
 end

endmodule
```

clockの立上りのタイミングでif文が評価されます。
weが1の場合はレジスタ型変数qの値が入力信号dataの値に更新されます。
weが1でない場合、つまり0の場合はqの値はそのまま保持されます。

---
### 10進カウンタ

count10モジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|count[3:0]|LEDR3-0|

```verilog
`default_nettype none

module counter10(
  input   wire        clock,
  output  reg   [3:0] count
  );

  always @ (posedge clock) begin
    if (count >= 4'b1010) begin
      count <= 4'b0000;
    end else begin
      count <= count + 1'b1;
    end
  end

endmodule
```

countの値が10以上かどうかで
clockの立上りが入ってきた時の回路の振る舞いが変わります。
countが10未満のときはcountを1ずつカウントアップしてゆき、
10以上となったときcountは0にクリアされます。

---
### 同期式リセット付きレジスタ

register4_sync_resetモジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|n_reset|KEY3|
|data[3:0]|SW3-0|
|q[3:0]|LEDR3-0|

```verilog
`default_nettype none

module register4_sync_reset(
  input   wire        clock,
  input   wire        n_reset,  // reset(active low)
  input   wire  [3:0] data,
  output  reg   [3:0] q
  );

  always @ (posedge clock) begin
    if (n_reset == 1'b0) begin
      q <= 4'b0000; // reset if n_reset is active
    end else begin
      q <= data;
    end
  end

endmodule
```

---
## 非同期式リセット

これまでは、クロック信号に同期して状態や
出力が変化する順序回路を設計していました。
verilogではクロックだけではなく他の入力信号の立上りや立下りのタイミング
で状態や出力を変化させるような回路を設計できます。

例えば、次ページ以降に示す非同期式のリセット機能をもった
回路を設計することができます。

---
### 非同期式リセット付きレジスタ

register4_async_resetモジュールを実習ボードに実装し、
動作を確かめましょう。
先ほどの同期式リセット付きレジスタ(register4_async_reset)との動作の違い、
特にリセットが起こるタイミングの違いに注目しましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|n_reset|KEY3|
|data[3:0]|SW3-0|
|q[3:0]|LEDR3-0|

```verilog
`default_nettype none

module register4_async_reset(
  input   wire        clock,
  input   wire        n_reset,  // reset (active low)
  input   wire  [3:0] data,
  output  reg   [3:0] q
  );

  always @ (posedge clock, negedge n_reset) begin
    if (n_reset == 1'b0) begin
      q <= 4'b0000;
    end else begin
      q <= data;
    end
  end

endmodule
```

clockの立上りもしくはn_resetの立下りが入ってきたタイミングで
always文が実行されるような回路が構成されます。
n_resetの立下りが入った時はn_resetが0となっているはずですので、
qが0にリセットされます。

---
### 非同期式リセット付き4ビットカウンタ

counter4_async_resetモジュールを実習ボードに実装し、
動作を確かめましょう。

ポートの割り当ては下記の表の通りにしてください。

|node name|I/Oデバイス|
|:---|:---|
|clock|KEY0|
|n_reset|KEY3|
|count[3:0]|LEDR3-0|

```verilog
`default_nettype none

module counter4_async_reset(
  input   wire        clock,
  input   wire        n_reset,
  output  reg   [3:0] count
  );

  always @ (posedge clock, negedge n_reset) begin
    if (n_reset == 1'b0) begin
      count <= 4'b0000;
    end else begin
      count <= count + 1'b1;
    end
  end

endmodule
```

n_resetの立下りが入ると、countが強制的に0へリセットされます。

