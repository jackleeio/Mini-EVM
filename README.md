
# Mini EVM

Build the EVM from scratch
从零开始构建 EVM



## 1. EVM 以太坊虚拟机

EVM 是以太坊的核心，是以太坊的执行引擎，负责执行以太坊程序，也称为智能合约。

本质上，EVM 就是一个虚拟机，负责执行以太坊字节码。

## 2. 虚拟机 Virtual Machine

虚拟机就像是一个假想的计算机，它运行在你的真实计算机上。与需要单独的物理机器不同，它完全使用你已经拥有的计算机上的软件来完成。

就像一个真正的计算机一样，它有自己的语言，对于 EVM 来说，这种语言被称为 以太坊字节码 (Bytecode)。

## 3. 字节码 Bytecode

Ethereum Bytecode 是 以太坊 字节码，它只是一个有效的 EVM 操作码列表。一个操作码(opcode)是一个操作，比如 `ADD`, `SUBTRACT` 或 `STOP`。

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

EVM 并不理解 `ADD` 或 `SUBTRACT` 这样的文字含义，它只认识 Opcode 的标识符，比如 `ADD` 的标识符是 `0x01`。

目前有 144 个 Opcode 操作码，我们可以在 [EVM.codes](https://www.evm.codes/?fork=cancun) 看到所有的 Opcode，但这个数字一直在变化，并不是固定的，新的 Opcode 在不断被添加，旧的 Opcode 有时也会被弃用。

## 4. Solidity / Vyper / Huff 智能合约编程语言

作为开发者，通常不会想直接编写字节码，这样做会非常慢而且容易出错。这就是为什么我们需要像 [Solidity](https://soliditylang.org/) 或 [Vyper](https://vyper.dev/) 这样的高级编程语言。

但是，一个 Solidity 或 Vyper 文件只是一个文本文件，这不是 EVM 可以理解的。我们需要一个程序，将文本文件转换为 EVM 字节码，这个程序被称为 编译器 Compiler。

如果一个编程语言可以被编译为 EVM 字节码，则称该编程语言是面向 EVM 设计的语言。

## 5. 图灵完备 Turing Complete

图灵完备 是指一个系统能够模拟任何图灵机。

图灵机 是一个抽象的计算模型，它由一个无限长的纸带和读写头组成，纸带上写有输入数据，读写头可以读 �� 和写入数据。图灵机可以执行任何可计算的函数。

以太坊是图灵完备的，这意味着任何程序都可以在 EVM 上运行（忽略 gas 和内存限制）。

## 6. 燃料 Gas

Gas 是以太坊中的一种资源单位，用于衡量执行智能合约所需的工作量。

我们需要 gas 是因为计算资源是有限的，为了避免 DDOS 攻击，用户需要花费一些 gas 来运行他们的程序，如果在执行字节码时 gas 用完了，程序的执行就会停止。

## 7. 栈 Stack

栈是一种后进先出(LIFO - Last In First Out)的数据结构。想象一叠盘子：你只能在顶部放入新盘子或取出最上面的盘子。栈的工作原理与此类似。

在 EVM 中：

- 栈的最大深度为 1024 个元素
- 每个栈元素为 256 位（32 字节）
- 只能对栈顶进行操作（push 和 pop）

主要的栈操作：

- `PUSH`：将一个值推入栈顶
- `POP`：移除并返回栈顶的值
- `DUP`：复制栈顶的值
- `SWAP`：交换栈顶的值

```solidity
// 示例：栈的操作
PUSH1 0x01  // 将 1 推入栈
PUSH1 0x02  // 将 2 推入栈
POP         // 弹出 2
```

如果尝试弹出一个空栈的值，则会抛出异常。

#### 下面用 Python 实现一个栈，并演示栈的操作。

```python
class Stack:
    MAXIMUM_STACK_SIZE = 1024  # EVM 栈的最大深度

    def __init__(self):
        self.items = []

    def __str__(self):
        if not self.items:
            return "Empty Stack"

        stack_view = []
        for i, item in enumerate(self.items[::-1]):
            if i == 0:
                stack_view.append(f"{item} ← TOP")
            elif i == len(self.items) - 1:
                stack_view.append(f"{item} ← BOTTOM")
            else:
                stack_view.append(str(item))
        return "\n".join(stack_view)

    def push(self, value):
        """将值推入栈顶"""
        if len(self.items) >= self.MAXIMUM_STACK_SIZE:
            raise StackOverflowError("Stack has reached maximum size of 1024")
        self.items.append(value)

    def pop(self):
        """弹出并返回栈顶的值"""
        if not self.items:
            raise StackUnderflowError("Cannot pop from an empty stack")
        return self.items.pop()

    def peek(self):
        """查看栈顶的值但不移除"""
        if not self.items:
            raise StackUnderflowError("Stack is empty")
        return self.items[-1]

    def size(self):
        """返回栈中元素数量"""
        return len(self.items)

    def is_empty(self):
        """检查栈是否为空"""
        return len(self.items) == 0

class StackOverflowError(Exception):
    pass

class StackUnderflowError(Exception):
    pass

# 使用示例
def stack_demo():
    print("=== EVM Stack Demo ===")
    stack = Stack()

    # 压入值
    print("\nPushing values: 5, 2, 4")
    stack.push(5)
    stack.push(2)
    stack.push(4)
    print(stack)

    # 弹出值
    print("\nPopping top value...")
    popped = stack.pop()
    print(f"Popped value: {popped}")
    print(stack)

    # 查看栈顶
    print("\nPeeking at top value...")
    top = stack.peek()
    print(f"Top value: {top}")
    print(stack)

# 运行演示
if __name__ == "__main__":
    stack_demo()
```

## 8. EVM 内存 Memory

内存是一个字节数组，具有以下特点：

- 每个内存单元可以存储 1 字节（8 位）的数据，范围是 0-255
- 类似 RAM，初始状态完全为空
- 具有易失性，执行完成后内存会被重置
- 可以通过索引（offset）直接访问任意位置

内存访问主要通过偏移量（offset）和大小（size）两个参数：

- offset：表示起始访问位置
- size：表示要访问的字节数量

例如：

- offset = 2, size = 5 表示访问索引 2 到 7 的内存区域
- 每次 load 操作默认读取 32 字节
- store 操作可以写入指定长度的数据

```python
class SimpleMemory:
    """简单的EVM内存模拟实现"""

    def __init__(self):
        self.memory = []

    def access(self, offset, size):
        """从起始位置 offset 访问指定大小 size 的内存"""
        return self.memory[offset:offset+size]

    def load(self, offset):
        """从起始位置 offset 读取 32 字节"""
        return self.access(offset, 32)

    def store(self, offset, value):
        """在指定位置 offset 写入数据"""
        self.memory[offset:offset+len(value)] = value
```

扩展内存空间会消耗非线性的 gas。这意味着创建更大的内存空间会变得越来越昂贵。

让我们为内存添加 gas 扩展计算

```python
class Memory(SimpleMemory):
    """增强版EVM内存实现，包含gas计算"""

    def store(self, offset, value):
        # 在指定位置 offset 存储数据，并计算内存扩展的 gas 成本
        memory_expansion_cost = 0
        required_size = offset + len(value)

        # 如果需要扩展内存
        if len(self.memory) < required_size:
            expansion_size = 0

            # 如果内存为空，初始化32字节的空间
            if len(self.memory) == 0:
                expansion_size = 32
                self.memory = [0x00] * 32

            # 计算需要额外扩展的大小
            if len(self.memory) < required_size:
                expansion_size += required_size - len(self.memory)
                self.memory.extend([0x00] * expansion_size)

            # 计算扩展消耗的gas（简化版本）
            # 实际EVM中的计算更复杂
            memory_expansion_cost = expansion_size ** 2

        super().store(offset, value)
        return memory_expansion_cost


# 使用示例
def memory_demo():
    print("=== EVM 内存演示 ===")
    memory = Memory()

    # 存储一些数据
    print("\n存储数据: [1, 2, 3, 4]")
    gas_cost = memory.store(0, [0x01, 0x02, 0x03, 0x04])
    print(f"内存扩展消耗的gas: {gas_cost}")

    # 读取数据
    print("\n从位置 0 读取 32 字节:")
    data = memory.load(0)
    print(f"读取的数据: {data}")

if __name__ == "__main__":
    memory_demo()
```
输出结果：

```bash
=== EVM 内存演示 ===

存储数据: [1, 2, 3, 4]
内存扩展消耗的gas: 1024

从位置 0 读取 32 字节:
读取的数据: [1, 2, 3, 4, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```

这个示例展示了 EVM 内存的两个基本操作：

1. `store`: 在指定位置存储数据
2. `load`: 从指定位置读取固定长度(32 字节)的数据

注意：

- 内存初始化和扩展会消耗 gas
- 未初始化的内存位置默认值为 0
- 每次 load 操作都会返回 32 字节的数据
