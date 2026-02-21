# Nand2Tetris Implementation
Building a modern computer system from first principles, starting with a single NAND gate.このリポジトリは、「コンピュータシステムの理論と実装（Nand2Tetris）」プロジェクトの実装記録です。論理ゲートの物理的な最小単位から、ALU、CPU、メモリ、そしてOSに至るまでの全レイヤーを、ブラックボックスなしで実装する過程を記録しています。

## 🚀 Project Status現在の進捗状況:
 Phase 1 - Hardware Implementation

 [x] Project 01: Boolean Logic (Not, And, Or, Xor, Mux, DMux, 16-bit variants)

 [x] Project 02: Boolean Arithmetic (HalfAdder, FullAdder, Inc16, ALU) 

 [x] Project 03: Sequential Logic (DFF, Bit, Register, RAM8/64/512/4K/16K, PC)

 [x] Project 04: Machine Language (Assembly scripts for Mult, Fill)

 [x] Project 05: Computer Architecture (Memory, CPU, Computer)

 [ ] Project 06: Assembler (Software implementation)👈 Current Focus

 [ ] Project 07: VM I: Stack Arithmetic

 [ ] Project 08: VM II: Program Control

 [ ] Project 09: High-Level Language (Jack application)

 [ ] Project 10: Compiler I: Syntax Analysis

 [ ] Project 11: Compiler II: Code Generation

 [ ] Project 12: Operating System
 
 
## 💡 Technical Highlights
 1. Design Philosophy (設計思想)実装にあたっては、単なる動作確認だけでなく、以下のプロセスを徹底しています。
 Mathematical Derivation:真理値表（Truth Table）を作成し、ド・モルガンの法則などを用いて論理式を最小化する。
 Cost Optimization:シリコン面積（Nandゲート総数）を意識した回路設計を行う。
 Verification:公式テストスクリプトによる完全な動作確認（"Comparison ended successfully"）を必須とする。
 
 2. Implementation Notes (実装メモ)Project 01: Boolean Logic
 Xor Gate Optimization:標準的な Not(A)And(B) Or A And(Not(B)) の実装（Nand数9）ではなく、Or(A,B) And Nand(A,B) の構成（Nand数6）を採用し、33%のゲート削減に成功。
 Bus Width Realization:Not16 などの実装を通じて、ビット幅が増えることの物理的なコスト（回路の並列配置）を体感。
 
 Project 02: Boolean Arithmetic (In Progress)HalfAdder: Sum (Xor) と Carry (And) の並列処理による加算の実装。
 ALU Design: 制御ビット（zx, nx, zy, ny, f, no）の組み合わせによる演算ロジックの設計。（設計進行中）
 
 📂 Directory Structure.
 
```
├── projects/
│   ├── 01/              # Boolean Logic (Done)
│   │   ├── And.hdl
│   │   ├── Mux.hdl
│   │   ├── Xor.hdl      # Optimized implementation
│   │   └── ...
│   ├── 02/              # Boolean Arithmetic (In Progress)
│   │   ├── HalfAdder.hdl
│   │   ├── ALU.hdl
│   │   └── ...
│   └── ...
├── docs/
│   ├── schematics/      # 手書きの回路図、設計メモの画像
│   └── notes/           # 技術的な考察ログ
└── tools/               # 開発用ツール (HardwareSimulatorなど)
```

## ✅ Verification 
全てのチップは、Nand2Tetris提供のテストスイート (.tst files) を通過しています。

### Test Result Example
 (Project 01):Comparison ended successfully
 
## 🛠 Environment & Tools
Hardware Description Language (HDL)
Nand2Tetris Software Suite (Hardware Simulator, CPU Emulator)
Editor: VS Code (with HDL syntax highlighting)
Version Control: Git / GitHub

# From Nand to Tetris

Building a Modern Computer From First Principles

This repository contains all the tools and files necessary for completing all the projects described in [nand2tetris.org](https://www.nand2tetris.org).
Once you download the software suite to your PC, there is no need to download anything else throughout your Nand to Tetris learning experience.

The [main](../../tree/main) branch contains the downloaded software from the official website without solutions.

Some possible implementations can be found in the branch [solutions](../../tree/solutions) (wip).

## How to test your implementation with docker

You may test implementations (.hdl files) using the docker image (tested in linux only)

Test all `.hdl` files inside project `1`:
```
make 01.test
``` 

Test `Sum16.hdl` file inside project `2`:
```
make 02.part P=Sum16
``` 


---

## Software

(from [https://www.nand2tetris.org/software](https://www.nand2tetris.org/software))

The Nand to Tetris Software Suite contains all the tools and files necessary for completing all the projects described in this site, and in the book The Elements of Computing Systems. Once you download the software suite to your PC, there is no need to download anything else throughout your Nand to Tetris learning experience.
 
The software runs as is on Windows, Unix, and Mac OS.
 
The software can be used freely under the terms of the GNU GPL (General Public License).  
The software is open source. If you wish to inspect, modify, or extend the software, see the bottom of this page.


### Download
(Note: after we update the software, which happens now and then, there is a limited time period in which some browsers may warn that "this file is not frequently downloaded and can be dangerous", or something like this. Please ignore this warning, and proceed to download and extract the nand2tetris.zip file to your computer.)
 
~~Download the Nand2tetris Software Suite~~  
**This repository contains the downloaded file.**
 
**Mac users:** after downloading, read this [Setup Guide for Apple MacOS](https://drive.google.com/open?id=1QDYIvriWBS_ARntfmZ5E856OEPpE4j1F), written by Yong Bakos.
 
**Windows users:** after downloading, put the downloaded zip file in an empty directory on your computer, and extract its contents as is, without changing the directories structure and names.
 
In order to use the nand2tetris software tools, your computer must be equipped with a Java Run-time Environment. The JRE can be downloaded freely from many sites including [this one](http://java.com/en/download/index.jsp). For best performance, download the latest available version.
 
**Problems?** Describe your problem in our [Q&A Forum](http://tecs-questions-and-answers-forum.32033.n3.nabble.com/) and you will get help soon


### About the Software
The Nand2tetris Software Suite consists of two directories: *projects*, and *tools*.
 
**The projects directory** is divided into 14 project directories named 00, 01, ..., 13 (of which project 00 is relevant only to learners who take the course in Coursera, and project 13 is open-ended). These directories contain files that you have to modify and complete as you work on various nand2tetris projects.
 
**The tools directory** contains the nand2tetris software tools. It's a collection of programs and files that will be explained as you follow the various projects.

The remainder of this section should be used as reference; there is no need to read what follows until you will be asked to use a particular software tool.
 

**The .bat and .sh files** are batch and script files, used to invoke the nand2tetris software tools. These files are explained in detail below.

**The bin directory** contains the code of the nand2tetris software tools. It consists of several subdirectories containing Java class files and supporting files.

**The builtInChips and the builtInVMCode directories** contain files that are used by the supplied Hardware Simulator and VM Emulator, respectively.

**The OS directory** contains a compiled version of the Jack operating system.


### Software Tools
The Nand to tetris Software Suite features the following software tools:


| Tool               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware Simulator | Simulates and tests logic gates and chips implemented in the HDL (Hardware Description Language) described in the book. Used in hardware construction projects. <br>Hardware Simulator Tutorial: [PPT](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_a1204dadc1c34d32a30256e5652abb97.ppt?dn=Hardware%20Simulator%20Tutorial.ppt) \| [PDF](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_bfd91435260748439493a60a8044ade6.pdf)                                                                |
| CPU Emulator       | Emulates the operation of the Hack computer system. Used to test and run programs written in the Hack machine language, in both its binary and assembly versions.<br>CPU Emulator Tutorial: [PPT](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_c37c115e88f849ba8e0beeab98b1160d.ppt?dn=CPU%20Emulator%20Tutorial.ppt) \| [PDF](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_f63aba2611944e82974c9c5d5a3821fe.pdf)                                                                           |
| VM Emulator        | Emulates the operation of our virtual machine (similar to Java's JVM); used to run and test programs written in the VM language (similar to Java's Bytcode).<br>VM Emulator Tutorial: [PPT](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_92baafe08ad14b66b877d80b105d59ec.ppt?dn=VM%20Emulator%20Tutorial.ppt) \| [PDF](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_b74d071ee4b74279b211acede232ced9.pdf)                                                                                  |
| Assembler          | Translates programs from the Hack assembly language to Hack binary code. The resulting code can be executed directly on the Computer chip (in the hardware simulator), or emulated on the supplied CPU Emulator (much faster and more convenient).<br> Assembler Tutorial: [PPT](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_32af60c3c5384df0bdbe23067a3b01e6.ppt?dn=Assembler%20Tutorial.ppt) \| [PDF](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_759f4f811ad14e12ac45bc60dd679fa3.pdf) |
| Compiler           | Translates programs written in Jack (a simple, Java-like object-based language) into VM code. The resulting code can run on the supplied VM Emulator. Alternatively, the VM code can be translated further by the supplied VM translatorinto Hack assembly code that can then be executed on the supplied CPU Emulator.                                                                                                                                                                                                                     |
| Operating system   | Two OS implementations are supplied: (i) a collection of eight .vm class files, written originally in Jack (just like Unix is written in C), and (ii) a faster implementation of all the OS services, embedded in the supplied VM Emulator.                                                                                                                                                                                                                                                                                                 |
| Text Comparer      | This utility checks if two input text files are identical, up to white space differences. Used in various projects. In Unix use "diff" instead.                                                                                                                                                                                                                                                                                                                                                                                             |


### Running the Software Tools
The supplied software tools are designed to be run from your computer's command-line environment (also known as "terminal", or "shell"). Command-line environments vary from one operating system to another, and working in them requires some knowledge of various OS shell commands.

In order to eliminate this overhead, we supply batch files (for Windows) and scripts (for Unix and Mac OS), developed by Mark Armbrust. These batch and script files enable invoking the supplied nand2tetris tools from the command line on your computer, painlessly. They can be used from any working directory on your computer, without requiring full paths to the files on which they operate. Further, they accept spaces in directory and file names, so they will work if nand2tetris is installed under a directory named, say, "My Documents".

**Mac and Linux users:**

~~Before running the scripts, you must first change their file attributes to include "executable". You can then run the scripts by typing their name, as well as the .sh extension, in the terminal environment.~~ (already done in this repo)

If you want to avoid typing the 'sh' extensions, you can create (once and for all) symbolic links in your ~/bin directory. Here is an example how to do it for, say, the HardwareSimulator tool:

```
ln -s ~/nand2tetris/tools/HardwareSimulator.sh HardwareSimulator
chmod +x HardwareSimulator
``` 

**Windows users:**

For the batch files to work from the command line, you must add (once and for all) the nand2tetris/toolsdirectory to your PATH variable.

To run a batch file from command-line, type its name, without the .bat extension.

If you use Windows 7 64-bit you need to install the 64-bit version of Java so that 64-bit cmdexe can run Java commands in batch files. If you get the output "'java' is not recognized..." you likely only have the 32-bit Java installed on your computer.

You can create desktop icons and use them to invoke the interactive versions of the following supplied tools: HardwareSimulator, Assembler, CPUEmulator and VMEmulator. This can be done by finding the disk locations of the respective batch files, right-clicking on them and picking "Send to > Desktop." Edit the shortcuts' properties and set "Run" to "minimized."

​
### Usage
​
**Hardware Simulator:** To invoke the hardware simulator in interactive mode, type "HardwareSimulator" in the command line. For example:
```
C:\...\projects\02>HardwareSimulator
(a window will open up, running the interactive version of the Hardware Simulator)
``` 

To invoke the hardware simulator in batch (shell/cmd) mode, type "HardwareSimulator" in the command line. For example:
```
C:\...\projects\02>HardwareSimulator ALU.tst
(invokes the simulator, loads the given test script, executes it, and reports the result). Note that the simulator's interactive mode also enables loading and executing test scripts.
```

- Successful test (example):
    ```
    C:\...\projects\02>HardwareSimulator ALU.tst
    End of script - Comparison ended successfully
    ```
- Failed test (example):
    ```
    C:\...\projects\02>HardwareSimulator ALU.tst
    Comparison failure at line 24
    ```
- Error in the associated HDL file:
    ```
    C:\...\projects\02>HardwareSimulator ALU.tst
    In HDL file C:\...\projects\02\ALU.hdl, Line 60, out[16]: the specified sub bus is not in the bus range: load ALU.hdl
    ``` 

**CPU Emulator and VM Emulator:** These operation of these tools follow the same convention described above. If you invokve either tool without a parameter, the tool will work in interactive mode; if you supply a parameter (test script), the tool will run batch-style.

**Assembler:** Typing "Assembler" will start the supplied assembler in interactive mode. Typing "Assembler xxx.asm" will assemble the specified xxx.asm file and generate a file named xxx.hack, containing the translated binary code. Note that the assembler's interactive mode also enables loading and translating .asm files.
- Successful assembly (example):
    ```
    C:\...\projects\04\fill>Assembler Fill.asm Assembling "c:\...\projects\04\fill\Fill.asm"
    ``` 
- Failed assembly (example):
    ```
    C:\...\projects\04\fill>Assembler Fill.asm Assembling "C:\...\projects\04\fill\Fill.asm" In line 15, Expression expected
    ``` 

To compare the resulting .hack code file to some expected .hack file, use the supplied TextComparer tool, described below.


**Compiler:** TTyping "JackCompiler fileName.jack" will compile the supplied Jack file. Typing "JackCompiler directoryName" will compile all the Jack file that are found in the specified directory. Wildcards are not supported. Here are some examples:

- Compile the current directory:
    ```
    C:\...\projects\09\Reflect>JackCompiler
    Compiling "c:\...\projects\09\Reflect"
    ```
- Compile a single file:
```
C:\...\projects\09\Reflect>JackCompiler Mirrors.jack Compiling "C:\...\projects\09\Reflect\Mirrors.jack"
``` 
- Compile the "Reflect" directory (for example):
```
C:\...\projects\09>JackCompiler Reflect
Compiling "C:\...\projects\09\Reflect"
``` 

**TextComparer:** Compares two given files ignoring white space, and reports success or failure. For example, suppose you run the hardware simulator with some test script and get a comparison failure. If you want, you can then use the TextComparer to investigate the problem:

```
C:\...\projects\02>HardwareSimulator ALU.tst
Comparison failure at line 24
``` 

```
C:\...\projects\02>TextComparer ALU.cmp ALU.out
Comparison failure in line 23:
|0101101110100000|0001111011010010|1|1|0|0|0|0|0001111011010010|0|0|
|0101101110100000|0001111011010010|1|1|0|0|0|0|0001111011010010|0|1|
``` 

(Note the line number discrepancy between the reports of the two tools).

​**Help:** In Windows, each batch file accepts a "/?" option that shows its intended usage. In Mac and Unix, use "-h". For example:
```
C:\...\projects\09>JackCompiler /?
Usage:
    JackCompiler Compiles all the .jack files in the current working directory.
    JackCompiler directoryName Compiles all the .jack files in the specified directory.
    JackCompiler fikeName.jack Compiles the specified Jack file
``` 

### Source Code
All the nand2tetris software tools are written in Java. If you wish to inspect, modify, or extend some tool, you can [download the source code](https://drive.google.com/file/d/1stcWUSeAixCRHWOjc9sgBhq5voSLvun8/view). Before compiling the source code on your computer, read [Readme.txt](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_9a0f3b6169c2451b9b37ed62595dc169.pdf).  
For details on what's new in the current version of the software (somewhat technical but useful for porting old modifications to the current version), read this [ChangeLog.txt](https://b1391bd6-da3d-477d-8c01-38cdf774495a.filesusr.com/ugd/44046b_2a164674985a4d42a7a2770659606f85.pdf) file.  
If you wish to share your software extensions with others, please email us at [nand2tetris@gmail.com](mailto:nand2tetris@gmail.com).


## Licence

https://www.nand2tetris.org/license

All Nand to Tetris materials and tools are protected under a [Creative Common Attribution-NonCommercial-ShareAlike 3.0 Unported License](https://creativecommons.org/licenses/by-nc-sa/3.0/).
