# Basic Concept

## 1. EVM 以太坊虚拟机

EVM 是以太坊的核心，是以太坊的执行引擎，负责执行以太坊程序，也称为智能合约。

本质上，EVM 就是一个虚拟机，负责执行以太坊字节码。

## 2. 虚拟机 Virtual Machine

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


## 4. Solidity / Vyper / Huff 智能合约编程语言

作为开发者，通常不会想直接编写字节码。这样做会非常慢而且容易出错。这就是为什么我们需要像 Solidity 或 Vyper 这样的高级编程语言。

但是，一个 Solidity 或 Vyper 文件只是一个文本文件。这不是 EVM 可以理解的。我们需要一个程序，将文本文件转换为 EVM 字节码。这个程序被称为 编译器 Compiler。

如果一个编程语言可以被编译为 EVM 字节码，则称该编程语言是面向 EVM 设计的语言。


## 5. 图灵完备 Turing Complete

图灵完备 是指一个系统能够模拟任何图灵机。

图灵机 是一个抽象的计算模型，它由一个无限长的纸带和读写头组成，纸带上写有输入数据，读写头可以读��和写入数据。图灵机可以执行任何可计算的函数。

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

## 8. 内存 Memory



