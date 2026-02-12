# Hack Software Reference

このドキュメントは、Nand2Tetrisプロジェクトにおいて実装されたソフトウェア（アセンブリプログラム等）の仕様書です。

## 1. Hack Assembly Language (機械語)

Hackコンピュータを制御するための低水準言語。`.asm` ファイルとして記述され、アセンブラによってバイナリ（`.hack`）に変換される。

### Memory Access

* **A-Register:** アドレスポインタおよび即値として使用。

  * `@value` (`0vvv...`) : Aレジスタに値をセットする。

* **D-Register:** データ演算用レジスタ。

* **M (Memory):** 現在Aレジスタが指しているRAMのアドレス (`RAM[A]`) への参照。

### I/O Maps (メモリマップドI/O)

* **SCREEN:** `RAM[16384]` (0x4000) から始まる 8K ワードの領域。

  * 1ビットが1ピクセルに対応（1=黒, 0=白）。

* **KEYBOARD:** `RAM[24576]` (0x6000) の 1ワード。

  * 押されているキーのASCIIコードが入る。何も押されていなければ 0。

## 2. Standard Programs (Project 04)

### Mult.asm (Multiplication)

* **Function:** 乗算 `R0 * R1` を計算し、結果を `R2` に格納する。

* **Interface:**

  * Input: `R0`, `R1` (RAM[0], RAM[1])

  * Output: `R2` (RAM[2])

* **Algorithm:** 反復加算 (Iterative Addition)

  * `R2` を 0 に初期化。

  * `R0` を `R1` 回足し合わせるループを実行。

  * `R1` (カウンタ) が 0 になったら終了。

### Fill.asm (I/O Control)

* **Function:** キーボード入力を監視し、画面全体を塗りつぶす/クリアする。

* **Interface:**

  * Input: `KBD` (RAM[24576])

  * Output: `SCREEN` (RAM[16384] 〜 RAM[24575])

* **Logic:** 無限ループにより以下を常時実行する。

  1. **Watch:** `KBD` の値を読み取る。

  2. **Select Color:**

     * キー押下あり (`KBD != 0`) → 色を `-1` (全ビット1 = 黒) に設定。

     * キー押下なし (`KBD == 0`) → 色を `0` (全ビット0 = 白) に設定。

  3. **Draw:** `SCREEN` の先頭から 8192 ワード分、設定した色を書き込む。

*Created by: hira-ping*