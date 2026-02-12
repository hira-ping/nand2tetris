# Hack Computer Hardware Reference

このドキュメントは、Nand2Tetrisプロジェクトにおいて実装されたハードウェア素子の仕様書です。
すべてのチップは、Nandゲートおよびそれ以前に定義されたチップのみを使用して構築されています。

## 1. Elementary Logic Gates (基本論理ゲート)

### NAND (Primitive)

* **Function:** 機能的完全性をもつ二つの二項論理演算子（NANDとNOR）のうち、電子回路で実現しやすい方。否定論理積。

* **Interface:** `IN a, b; OUT out;`

* **Logic:** `if (a==1 and b==1) then out=0 else out=1`

### Not (Inverter)

* **Function:** 入力ビットを反転させる。

* **Interface:** `IN in; OUT out;`

* **Implementation:** `Nand(in, in)`

* **Truth Table:**
  | in | out |
  |:--:|:---:|
  | 0  |  1  |
  | 1  |  0  |

### And

* **Function:** 両方の入力が1の時のみ1を出力する（論理積）。

* **Interface:** `IN a, b; OUT out;`

* **Implementation:** `Not(Nand(a, b))`

* **Truth Table:**
  | a | b | out |
  |:--:|:--:|:--:|
  | 0 | 0 | 0 |
  | 0 | 1 | 0 |
  | 1 | 0 | 0 |
  | 1 | 1 | 1 |

### Or

* **Function:** 少なくともどちらかの入力が1なら1を出力する（論理和）。

* **Interface:** `IN a, b; OUT out;`

* **Implementation:** `Nand(Not(a), Not(b))` (De Morgan's laws)

* **Truth Table:**
  | a | b | out |
  |:--:|:--:|:--:|
  | 0 | 0 | 0 |
  | 0 | 1 | 1 |
  | 1 | 0 | 1 |
  | 1 | 1 | 1 |

### Xor (Exclusive Or)

* **Function:** 入力が異なる場合のみ1を出力する（排他的論理和）。

* **Interface:** `IN a, b; OUT out;`

* **Implementation Note:** `Or(a,b) And Nand(a,b)`

  * 標準的な実装（Nand数9）よりも効率的な構成（Nand数6）を採用。

* **Truth Table:**
  | a | b | out |
  |:--:|:--:|:--:|
  | 0 | 0 | 0 |
  | 0 | 1 | 1 |
  | 1 | 0 | 1 |
  | 1 | 1 | 0 |

## 2. Selectors & Distributors (制御・分岐)

### Mux (Multiplexer)

* **Function:** 選択信号 `sel` に応じて、入力 `a` か `b` のどちらかを通す（if-else）。

* **Interface:** `IN a, b, sel; OUT out;`

* **Logic:**

  * If `sel==0` then `out=a`

  * If `sel==1` then `out=b`

### DMux (Demultiplexer)

* **Function:** 選択信号 `sel` に応じて、入力 `in` を `a` か `b` のどちらかに流す。

* **Interface:** `IN in, sel; OUT a, b;`

* **Logic:**

  * If `sel==0` then `a=in, b=0`

  * If `sel==1` then `a=0, b=in`

### Multi-Bit / Multi-Way Variants

これらは基本ゲートを並列配置、または木構造（Tree）状に配置して実装される。

* **Not16, And16, Or16:** 16ビットバスに対するビットごとの演算。物理的にゲートを16個並列に配置して実装。

* **Mux16:** 16ビットバス同士の選択。`sel` は1ビット。

* **Or8Way:** 8ビット入力の論理和。

  * **Function:** 入力ビットのうち、1つでも1なら1を出力。

  * **Implementation:** トーナメント方式（木構造）。

    1. 8入力を2つずつOrして4本にする。

    2. 4本を2つずつOrして2本にする。

    3. 最後の2本をOrして出力。

* **Mux4Way16 / Mux8Way16:** 多入力の選択器。

  * **Function:** 4つ（または8つ）の16ビット入力から、選択信号 `sel` に応じて1つを選んで出力する。

  * **Key Concept:**

    * **Mux4Way16:** `sel[2]`を使用。下位ビット(`sel[0]`)で予選（a vs b, c vs d）を行い、上位ビット(`sel[1]`)で決勝を行う。

    * **Mux8Way16:** `sel[3]`を使用。`Mux4Way16` を2つ使い、最後に `Mux16` で統合する。バスのスライス（`sel[0..1]`）を使用。

* **DMux4Way / DMux8Way:** 多出力への分配器。

  * **Function:** 1つの入力を、選択信号 `sel` に応じて4つ（または8つ）の出力先へ分配する。

  * **Key Concept:**

    * 上位ビットで「上流か下流か（例: a,b系 か c,d系か）」を大きく分岐させ、下位ビットでさらに細かく分岐させる階層構造を持つ。

## 3. Arithmetic Chips (算術演算)

### HalfAdder (半加算器)

* **Function:** 2つの1ビット入力を加算する。

* **Interface:** `IN a, b; OUT sum, carry;`

* **Implementation:**

  * `sum = Xor(a, b)`

  * `carry = And(a, b)`

### FullAdder (全加算器)

* **Function:** 3つの1ビット入力（繰り上がり含む）を加算する。

* **Interface:** `IN a, b, c; OUT sum, carry;`

* **Implementation:** 2つの `HalfAdder` と1つの `Or` を組み合わせて構成。

### Add16 (16-bit Adder)

* **Function:** 2つの16ビット数値を加算する。

* **Interface:** `IN a[16], b[16]; OUT out[16];`

* **Implementation:** Ripple Carry Adder（波及桁上げ加算器）。

  * 0ビット目は `HalfAdder`、1\~15ビット目は `FullAdder` を数珠繋ぎにする。

  * 最上位ビットのオーバーフローは無視する仕様。

### Inc16 (16-bit Incrementer)
* **Function:** 入力値に1を加算する (`out = in + 1`)。
* **Interface:** `IN in[16]; OUT out[16];`
* **Implementation:** `Add16` を使用し、一方の入力の最下位ビット(`b[0]`)のみを `true` に固定する。

### ALU (Arithmetic Logic Unit)
* **Function:** Hackコンピュータの計算中枢。2つの入力に対し、制御ビットに基づいた演算を行う。
* **Interface:**
  * **Inputs:** `x[16], y[16]`, `zx, nx, zy, ny, f, no`
  * **Outputs:** `out[16]`, `zr, ng`

#### Control Bits Logic (制御ロジック)
入力データは以下の順序で加工される。

| Step | Bit | Name | Action if bit == 1 | Implementation |
|:---:|:---:|:---:|:---|:---|
| 1 | **zx** | Zero x | 入力 `x` をゼロにする | `Mux16(x, false, zx)` |
| 2 | **nx** | Not x | `x` を反転(Bitwise Not)する | `Not16` + `Mux16` |
| 3 | **zy** | Zero y | 入力 `y` をゼロにする | `Mux16(y, false, zy)` |
| 4 | **ny** | Not y | `y` を反転(Bitwise Not)する | `Not16` + `Mux16` |
| 5 | **f** | Function | `1`: 加算 (`x+y`) <br> `0`: 論理積 (`x&y`) | `Add16`, `And16` + `Mux16` |
| 6 | **no** | Not out | 出力を反転する | `Not16` + `Mux16` |

#### Output Flags (出力フラグ)
* **zr (Zero Flag):** `out == 0` のとき `1`。 (`Or8Way` x2 -> `Or` -> `Not` で判定)
* **ng (Negative Flag):** `out < 0` のとき `1`。 (最上位ビット `out[15]` をそのまま出力)

## 4. Sequential Logic (順序回路)

### Clock & Time (クロックと時間)
* **Concept:** Hackコンピュータは同期回路であり、全ての順序回路は「クロック信号」に合わせて状態を更新する。
* **Notation:**
    * $t$: 現在のクロックサイクル
    * $t+1$: 次のクロックサイクル
    * 順序回路の出力は、常に入力に対して**1クロック遅れて**変化する。

### DFF (Data Flip-Flop)
* **Function:** 最も基本的な記憶素子。入力を1クロックサイクルだけ遅延させて出力する。
* **Interface:** `IN in; OUT out;`
* **Logic:** $out(t+1) = in(t)$
* **Implementation:** Nand2Tetrisシミュレータのプリミティブ（組み込み素子）として提供される。

### Bit (1-Bit Register)
* **Function:** 1ビットの情報を記憶（維持）または更新する。
* **Interface:** `IN in, load; OUT out;`
* **Logic:**
    * **Load=1 (Write):** 次の時刻に新しい入力値 `in` を記憶する。
      $$out(t+1) = in(t)$$
    * **Load=0 (Maintain):** 次の時刻も現在の値 `out` を維持する（フィードバック）。
      $$out(t+1) = out(t)$$
* **Implementation:** `Mux` と `DFF` を組み合わせたフィードバックループ構造。
    * `Mux` で「新しい値」か「前の値（ループ）」かを選び、`DFF` に入力する。

### Register (16-Bit Register)
* **Function:** 16ビットの情報を記憶する。
* **Interface:** `IN in[16], load; OUT out[16];`
* **Implementation:** 16個の `Bit` レジスタを並列配置し、`load` 信号を全ビットで共有（ブロードキャスト）する。

### RAMn (Random Access Memory)
* **Function:** $n$ 個の16ビットレジスタからなるメモリバンク。任意の場所（アドレス）にアクセスできる。
* **Interface:** `IN in[16], load, address[k]; OUT out[16];`
    * `k = log2(n)` (例: RAM8なら3ビット、RAM64なら6ビット)
* **Logic:**
    * **Read:** `load=0` の時、`address` で指定されたレジスタの値を出力する。
    * **Write:** `load=1` の時、`address` で指定されたレジスタに `in` を書き込み、その値を出力する。

#### Architecture Note: Address Decoding (アドレスデコーディング)
RAMは以下の「サンドイッチ構造」で実装される。
1.  **Write Logic (DMux):** `load` 信号をデコードし、指定されたレジスタ**のみ**に `1` を送る。他には `0` を送る。
2.  **Storage (Registers):** 全てのレジスタが並列に配置される。
3.  **Read Logic (Mux):** 全てのレジスタの出力を受け取り、指定された1つだけを選択して出力する。

> **Insight:**
> 外部からは「指定した1つのレジスタ」だけが動いているように見えるが、物理的には **「全てのレジスタが常に稼働している」**。
> * 選択されていないレジスタも、`load=0`（記憶保持モード）として、クロックごとに自分自身の値をリフレッシュし続けている。
> * この「全員が常に起きている」特性が、ハードウェアの電力消費の一因であり、同時に「どの場所でも同じ速度でアクセスできる（Random Access）」という特性を実現している。

### PC (Program Counter)
* **Function:** 次に実行すべき命令のアドレス（行番号）を保持・計算する。
* **Interface:** `IN in[16], load, inc, reset; OUT out[16];`
* **Logic:** 以下の優先順位で次の値を決定し、毎クロック更新する。
    1.  **Reset:** `if reset(t-1) then out(t) = 0`
    2.  **Load:** `else if load(t-1) then out(t) = in(t-1)`
    3.  **Inc:** `else if inc(t-1) then out(t) = out(t-1) + 1`
    4.  **Else:** `out(t) = out(t-1)` (維持)
* **Implementation:**
    * 3つの `Mux16` を直列に配置し、優先度の高い操作を後段（Registerの直前）に置くことで制御する。
    * `Register` の `load` ピンは常に `true` に固定し、毎クロック更新を行う。

---

## 5. System Architecture (システムアーキテクチャ)

### Memory Map (メモリマップ)
Hackコンピュータのアドレス空間（15ビット: 0〜32767）は、以下の3つのチップに物理的にマッピングされる。

| Address Range | Address (Hex) | Target Chip | Description |
| :--- | :--- | :--- | :--- |
| **00000 - 16383** | `0x0000 - 0x3FFF` | **RAM16K** | データメモリ (Read/Write) |
| **16384 - 24575** | `0x4000 - 0x5FFF` | **Screen** | 画面メモリマップ (Read/Write) |
| **24576** | `0x6000` | **Keyboard** | キーボードメモリマップ (Read Only) |
| 24577 - 32767 | `0x6001 - 0x7FFF` | (Unused) | 使用不可領域 |

* **Mapping Logic (Project 05 Memory.hdl):**
    * 最上位ビット `address[14]` が `0` → **RAM16K**
    * 最上位ビット `address[14]` が `1`
        * 次のビット `address[13]` が `0` → **Screen**
        * 次のビット `address[13]` が `1` → **Keyboard**

*Created by: hira-ping*