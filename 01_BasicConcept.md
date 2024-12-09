# Basic Concept

## 1. EVM

EVM 是以太坊的核心，是以太坊的执行引擎，负责执行以太坊程序，也称为智能合约。

本质上，EVM 就是一个虚拟机，负责执行以太坊字节码。

## 2. 虚拟机

虚拟机就像是一个假想的计算机，它运行在你的真实计算机上。与需要单独的物理机器不同，它完全使用你已经拥有的计算机上的软件来完成。

就像一个真正的计算机一样，它有自己的语言。对于 EVM 来说，这种语言被称为 以太坊字节码 (Bytecode)。

## 3. 字节码 Bytecode

Ethereum bytecode 是 以太坊 字节码，它只是一个有效的 EVM 操作码列表。一个操作码(opcode)是一个操作，比如 ADD, SUBTRACT 或 STOP。

| OPCODE | NAME | 描述               |
| ------ | ---- | ------------------ |
| 0x00   | STOP | 停止执行           |
| 0x01   | ADD  | 加法运算           |
| 0x02   | MUL  | 乘法运算           |
| 0x03   | SUB  | 减法运算           |
| 0x04   | DIV  | 整数除法运算       |
| 0x05   | SDIV | 有符号整数除法运算 |
| 0x06   | MOD  | 取模运算           |
| 0x07   | SMOD | 有符号取模运算     |
| 0x08   | EXP  | 指数运算           |
| 0x09   | MEXP | 模幂运算           |

EVM 并不理解 ADD 或 SUBTRACT 这样的文字含义。它只认识 Opcode 的标识符。比如 ADD 的标识符是 0x01。

目前有 144 个 Opcode 操作码。我们可以在 [EVM.codes](https://www.evm.codes/?fork=cancun) 看到所有的 Opcode。这个数字一直在变化，并不是固定的。新的 Opcode 会不断被添加，旧的 Opcode 也会经常被弃用。
