---
layout: page
title: L04 Verilog II
permalink: /L04/
description: "Lecture 4 Verilog II"
nav_order: 4




---

# Lecture 4 Verilog II

# Review

在芯片设计中，有几个关键的**设计指标（Design Metrics）**需要考虑，这些指标直接影响芯片的性能、成本和功耗：

- **功能性与鲁棒性（Functionality and Robustness）**：确保设计能够在各种条件下稳定工作，并正确执行指定任务。
- **成本（Cost）**：控制芯片设计和制造的费用，以便在满足性能需求的前提下最小化成本。
- **性能（Performance）**：衡量芯片的处理速度和效率，尤其是在关键应用场景中。
- **功耗与能效（Power and Energy）**：衡量芯片在工作期间的能耗，特别是在移动设备和大规模计算中，低功耗设计尤为重要。

## Verilog 

Verilog 是一种硬件描述语言（HDL），用于描述和设计硬件电路。它的核心功能是通过代码来描述电路的结构和行为。Verilog 可以通过**逻辑综合（Logic Synthesis）**将代码转换为**门级网表（Gate-level Netlists）**，用于实际的硬件实现。Verilog 被广泛应用于 ASIC（专用集成电路）和 FPGA（现场可编程门阵列）设计中。

- **Verilog Assign 语句**：这是 Verilog 中最常用的语句之一，用于定义组合逻辑，表示一个信号持续地分配给另一个信号。

### Verilog 模块定义

在 Verilog 中，**模块（module）** 是电路设计的基本构建块。模块定义电路中的组件，可以是简单的逻辑门，也可以是复杂的子电路。模块通过端口（ports）与其他模块或信号交互。

### 描述模块内容的两种方式：

1. **结构化 Verilog（Structural Verilog）**：
   - 列出子组件以及它们的连接方式，类似于用文本描述的电路原理图。
   - 这种方法虽然精确，但编写繁琐且难以解读，尤其是在大型设计中。
   - 适合需要精确控制电路细节的设计场景，如在 FPGA 或 ASIC 上映射特殊资源。

2. **行为化 Verilog（Behavioral Verilog）**：
   - 描述组件的功能，而不是如何实现这些功能。
   - 代码会被综合工具转换成具有相应功能的电路，最终生成门级设计。
   - 行为化描述的结果取决于所使用的综合工具，工具的效率和优化能力会影响最终设计。

### 模块层次结构

通过构建模块的层次结构，设计人员可以管理复杂的系统设计。顶层模块通常代表整个设计或设计测试环境。在实际设计中，多个子模块可以被实例化并组合成更高级别的模块。这种方法有助于减少代码的复杂度，同时提高设计的可复用性。

...

## Continuous Assignment Examples

**连续赋值（Continuous Assignment）** 在 Verilog 中用于持续监控右侧表达式的变化，并将结果立即更新到左侧的信号。这种方式常用于建模组合逻辑电路，避免手动定义逻辑门的互连。

以下是一些典型的连续赋值例子：

### 示例代码

```verilog
wire [3:0] A, X, Y, R, Z;
wire [7:0] P;
wire r, a, cout, cin;
```

| 赋值语句                               | 解释                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| `assign R = X | (Y & ~Z);`             | 使用按位布尔操作符：对 `Y` 取反后与 `Z` 进行按位与操作，再与 `X` 进行按位或操作。 |
| `assign r = &X;`                       | 归约操作符：对 `X` 的所有位进行按位与操作，结果为 1 位。     |
| `assign R = (a == 1'b0) ? X : Y;`      | 条件运算符：当 `a` 为 0 时，`R` 等于 `X`，否则等于 `Y`。     |
| `assign P = 8'hff;`                    | 常量赋值：将 8 位常量 `ff` 赋值给 `P`。                      |
| `assign P = X * Y;`                    | 算术运算符：将 `X` 和 `Y` 进行乘法运算，注意硬件资源消耗。   |
| `assign P[7:0] = {4{X[3]}}, X[3:0];`   | 拼接与符号扩展：将 `X[3]` 扩展 4 位，并与 `X[3:0]` 拼接。    |
| `assign {cout, R} = X + Y + cin;`      | 加法运算符：实现带进位的加法运算，输出结果和进位。           |
| `assign Y = A << 2;`                   | 位移操作符：将 `A` 的所有位左移 2 位。                       |
| `assign Y = {A[1], A[0], 1'b0, 1'b0};` | 等效位移：通过拼接实现与左移等效的结果。                     |

### 说明：
- **按位布尔操作符**：用于逐位操作信号，如按位与（`&`）、按位或（`|`）、按位取反（`~`）。
- **归约操作符**：将多个位通过布尔运算归约为单个位（如 `&X`）。
- **条件运算符**：简洁地表达条件分支逻辑。
- **算术运算符**：在信号上执行加法、乘法等操作，注意硬件代价。
- **拼接与符号扩展**：使用大括号 `{}` 进行位的拼接，或者符号扩展。

---

# Continue Verilog

## Non-Continuous Assignments

在 Verilog 中，**非连续赋值（Non-Continuous Assignment）** 使用 `always` 块来实现，它显示了 Verilog 作为仿真语言的根源。这种赋值方式在硬件设计的角度上有些特殊，因为它并非持续监控信号，而是根据触发条件进行操作。

![image-20240905104556024]({{ site.baseurl }}/docs/assets/image-20240905104556024.png)

###  `always` 块示例代码

```verilog
module and_or_gate (out, in1, in2, in3);
    input in1, in2, in3;
    output out; 
    reg out;  // 使用 reg 声明但不是寄存器，仅表示可以在 always 块内赋值
    
    always @(in1 or in2 or in3) begin  // 在信号 in1, in2, in3 改变时触发
        out = (in1 & in2) | in3;       // 逻辑操作：先与后或
    end
endmodule
```

### 关键点：

- **reg 类型声明**：`out` 被声明为 `reg` 类型，因为它需要在 `always` 块中被赋值。这里的 `reg` 并不表示物理寄存器，而是 Verilog 中的一个习惯用法。
- **敏感列表（sensitivity list）**：`always` 块的执行取决于敏感列表中的信号变化。当 `in1`、`in2` 或 `in3` 发生变化时，`always` 块内部的逻辑会被执行。
- **赋值方式**：在 `always` 块内部，使用等号 `=` 进行阻塞赋值（blocking assignment），这种赋值方式在 `always` 块内的执行顺序严格按照代码顺序。

![image-20240905104723086]({{ site.baseurl }}/docs/assets/image-20240905104723086.png)

### 与连续赋值的比较：

与连续赋值的 `assign` 语句相比，`always` 块的赋值在硬件设计中稍显复杂。`assign` 语句适用于组合逻辑的建模，始终保持信号同步，而 `always` 块更适合用于模拟时序逻辑或复杂的状态机。

尽管在简单情况下，`always` 块的赋值方式和 `assign` 语句看起来类似，但 `always` 提供了更灵活的逻辑控制，适合于条件复杂或多步操作的情况。而在组合逻辑建模时，`assign` 更为直接和高效。



## Always Blocks in Verilog

在 Verilog 中，**always 块** 允许我们实现一些在连续赋值语句中难以完成的逻辑构造，特别是在处理复杂条件判断或时序逻辑时。`always` 块提供了更大的灵活性，使得设计者可以使用 `case` 或 `if-else` 结构进行更细粒度的控制。

## Case Statement Example

![image-20240905105031597]({{ site.baseurl }}/docs/assets/image-20240905105031597.png)

在这个例子中，`always` 块结合 `case` 语句，实现了一个 4:1 多路复用器（MUX）的逻辑。根据 `select` 信号的不同值，`out` 会选择 `in0` 到 `in3` 中的一个作为输出。

```verilog
module mux4 (in0, in1, in2, in3, select, out);
    input in0, in1, in2, in3;
    input [1:0] select;
    output reg out;  // 需要使用 reg 类型，因为在 always 块中赋值
    
    always @(in0 or in1 or in2 or in3 or select) begin
        case (select)    // 根据 select 的值选择输出
            2'b00: out = in0;
            2'b01: out = in1;
            2'b10: out = in2;
            2'b11: out = in3;
        endcase
    end
endmodule
```

### 关键点：

- **敏感列表（sensitivity list）**：`always` 块内的逻辑在 `select` 或 `in0` 到 `in3` 发生变化时执行。
- **case 语句**：根据 `select` 的值选择不同的输入信号，将其赋值给 `out`。每个 `case` 分支都代表一种输入组合。
- **reg 声明**：`out` 被声明为 `reg` 类型，因为在 `always` 块中需要进行赋值。

**为什么不直接用嵌套的 `if-else`？**

虽然嵌套的 `if-else` 语句可以实现类似的逻辑，但 `case` 语句更为简洁和清晰。在硬件电路中，`case` 语句会被综合成平衡的多路选择器，而 `if-else` 语句有时可能被综合成带优先级的逻辑，导致不同输入信号的延迟不一致。

## Nested if-else Example

接下来，我们看看使用嵌套的 `if-else` 语句来实现同样的多路复用器逻辑。`if-else` 语句可以用来逐个检查条件，并根据匹配的条件执行相应的赋值操作。

![QQ_1725504879154]({{ site.baseurl }}/docs/assets/QQ_1725504879154.png)

```verilog
module mux4 (in0, in1, in2, in3, select, out);
    input in0, in1, in2, in3;
    input [1:0] select;
    output reg out;  // reg 类型，因为需要在 always 块中赋值

    always @(in0 or in1 or in2 or in3 or select) begin
        if (select == 2'b00)
            out = in0;
        else if (select == 2'b01)
            out = in1;
        else if (select == 2'b10)
            out = in2;
        else
            out = in3;
    end
endmodule
```

### 关键点：

- **嵌套 `if-else`**：通过多级条件检查来实现逻辑判断，每个 `else if` 分支用于处理一种情况。
- **优先级逻辑（priority logic）**：嵌套的 `if-else` 语句会导致优先级逻辑结构，即不同的输入信号会有不同的传输延迟。比如 `in0` 的传输延迟会比 `in3` 短，因为它更早被检查到。

### 总结 Case vs If-else

- **`case` 语句**：所有输入信号的延迟是相同的，信号被并行地处理。这种平衡的逻辑适合实现多路复用器等场景，且代码更易于维护。
- **嵌套 `if-else` 语句**：这种结构会产生优先级逻辑，可能导致某些输入的传输延迟较大，尤其在复杂的条件判断中。例如，`in3` 的延迟会比 `in0` 长，因为它在最后一个条件中被检查到。

在 Verilog 设计中，`always` 块提供了极大的灵活性，允许我们使用 `case` 和 `if-else` 结构进行逻辑判断。使用 `case` 语句时，所有输入信号的传输延迟相同，更适合多路复用器的实现。而 `if-else` 结构则适用于需要优先级逻辑的场景，但要注意不同条件下的延迟差异。



## Ripple-Carry Adder Example

上节课提到的**Ripple-Carry Adder（波纹进位加法器）** 是一种常见的多位二进制加法器，通过将多个 **全加器（Full Adder, FA）** 级联，每个全加器处理一位的加法并将进位传递到下一位。这种设计比较简单，但由于进位需要逐位传递，可能会导致延迟较大，特别是在位数较多时。

![image-20240905105959775]({{ site.baseurl }}/docs/assets/image-20240905105959775.png)

### Full Adder 模块

```verilog
module FullAdder(a, b, ci, r, co);
    input a, b, ci;      // 输入 a, b 和进位 ci
    output r, co;        // 输出 r（和）和 co（进位）

    // 逻辑操作：求和和进位
    assign r = a ^ b ^ ci;       // r 是 a, b 和 ci 的异或结果
    assign co = (a & b) | (a & ci) | (b & ci);  // co 是进位逻辑
endmodule
```

**FullAdder** 模块是基本的 1 位加法单元，输入为 `a`、`b` 和 `ci`（进位输入），输出为 `r`（结果）和 `co`（进位输出）。

### Adder 模块

```verilog
module Adder(A, B, R);
    input [3:0] A, B;   // 输入4位二进制数 A 和 B
    output [4:0] R;     // 输出 5 位结果（包括进位）
    
    wire c1, c2, c3;    // 内部连线，用于存储各个位的进位信号
    
    // 实例化 4 个全加器来处理 4 位加法
    FullAdder add0 (.a(A[0]), .b(B[0]), .ci(1'b0), .co(c1), .r(R[0]));
    FullAdder add1 (.a(A[1]), .b(B[1]), .ci(c1), .co(c2), .r(R[1]));
    FullAdder add2 (.a(A[2]), .b(B[2]), .ci(c2), .co(c3), .r(R[2]));
    FullAdder add3 (.a(A[3]), .b(B[3]), .ci(c3), .co(R[4]), .r(R[3]));  // 最后的进位输出到 R[4]
endmodule
```

**Adder** 模块实例化了四个 `FullAdder` 模块，每个模块处理一位的加法，进位信号从低位传递到高位。最终结果包含 5 位，其中最后一位是进位输出。

## Ripple Adder Generator Example

为了更灵活地处理不同位宽的加法器设计，Verilog 提供了 **参数化设计（Parameterized Design）**，允许通过参数来控制设计中的位宽等关键因素。通过 `generate` 语句，我们可以利用 `for` 循环自动生成多个 `FullAdder` 实例，从而动态地创建不同位宽的加法器。

![image-20240905110029895]({{ site.baseurl }}/docs/assets/image-20240905110029895.png)

### 参数化的 Adder 模块

```verilog
module Adder #(parameter N = 4) (A, B, R);
    input [N-1:0] A, B;  // N 位输入
    output [N:0] R;      // N+1 位输出
    wire [N:0] C;        // 进位信号数组
    
    genvar i;            // 生成变量
    generate
        for (i = 0; i < N; i = i + 1) begin : bit
            FullAdder add (.a(A[i]), .b(B[i]), .ci(C[i]), .co(C[i+1]), .r(R[i]));
        end
    endgenerate
    
    assign C[0] = 1'b0;  // 初始进位为 0
    assign R[N] = C[N];  // 最后进位赋值给输出的最高位
endmodule
```

### 关键点：
- **parameter 声明**：`parameter N = 4` 声明了一个参数 `N`，默认值为 4。这不是端口，而是在综合（synthesis）时使用的常量，用于控制设计中的位宽。
- **genvar 和 generate 块**：`generate` 块通过 `for` 循环来创建多个 `FullAdder` 实例，参数 `N` 控制生成的数量。`genvar i` 是生成块中的变量，用于遍历不同位的位置。
- **动态生成实例**：在 `generate` 块中，`FullAdder` 的每个实例都有独立的索引 `i`，生成 `N` 个加法器实例。

### 使用示例：

```verilog
Adder #(N=64) adder64 (.A(A64), .B(B64), .R(R64));  // 64 位的加法器实例
```

在实际使用时，可以通过在实例化时覆盖参数 `N` 的默认值，生成适合当前需求的加法器。例如上面的代码生成了一个 64 位的加法器。

### 总结：

- 通过参数化设计，我们可以编写具有更高灵活性和可复用性的 Verilog 模块，避免手动编写重复的代码。
- `generate` 块允许动态生成多个实例，对于需要大量相似逻辑单元的设计非常有用，如加法器、乘法器和其他算术单元。



## Simplified Verilog Guidelines for Combinational Logic

总结，在 Verilog 中，设计组合逻辑有两种常见的方法：**连续赋值（Continuous Assignment）**和**`always` 块**。这两种方法在处理组合逻辑时都很常见，但它们的适用场景和语法稍有不同。

### Continuous Assignment

```verilog
assign a = b & c;
```
- **语法**：`assign` 用于定义组合逻辑的持续赋值操作。它监控右侧表达式中的信号 `b` 和 `c`，一旦其中任何一个信号发生变化，表达式就会重新计算，结果赋值给 `a`。
- **适用场景**：适合简单的组合逻辑，如逻辑运算、加法等。
- **输出类型**：`assign` 通常作用于 **`wire`** 类型的信号。

### Always Block with `@(*)`

```verilog
always @(*) begin
    a = b & c;  // 阻塞赋值
end
```
- **语法**：`always @(*)` 定义了一个组合逻辑块，其中的逻辑会在输入信号发生变化时执行。`@(*)` 是敏感列表中的通配符，表示所有输入信号。
- **适用场景**：适用于更复杂的组合逻辑，比如包含条件判断或多信号的逻辑。
- **输出类型**：`always` 块中的赋值一般作用于 **`reg`** 类型的信号。

### 关键区别
- **`assign`** 语句用于简单的组合逻辑，通常对 `wire` 类型信号进行赋值。
- **`always` 块** 则用于复杂的逻辑判断或组合逻辑操作，通常对 `reg` 类型信号赋值。
  
## Are These Combinational Circuits Correct?

### Example A: 3-Input Adder

```verilog
always @(a or b) begin
    out = a + b + c;
end
```

### 问题：
1. 这个代码的问题在于敏感列表没有包含信号 `c`。在组合逻辑设计中，敏感列表应该包含所有的输入信号，否则可能导致仿真中信号的更新不完整或出错。

### 修正：
```verilog
always @(a or b or c) begin
    out = a + b + c;
end
```
或者更简洁地使用 `@(*)` 来表示所有输入信号：
```verilog
always @(*) begin
    out = a + b + c;
end
```

### Example B: Continuous Assignment

```verilog
assign out = in & out;
```

### 问题：
1. 这里存在一个直接的反馈路径，即 `out` 同时作为输入和输出。这在组合逻辑中会导致逻辑环路（combinational loop），可能会产生不稳定的结果或仿真错误。
2. **逻辑环路** 应避免，因为信号的变化可能会无限循环，导致无法确定最终的输出值。

### 修正：
通常不应有这样的自反馈路径，具体修正取决于设计需求。你需要重新思考这个逻辑结构，确保没有自反馈。例如：

```verilog
assign out = in & some_other_signal;
```

### Example C: Case Statement in Always Block

```verilog
always @(*) begin
    case (select)
        2'b00: out = in0;
        2'b01: out = in1;
        2'b10: out = in2;
    endcase
end
```

### 问题：
1. 这个例子的问题是没有覆盖所有的 `select` 可能的值。对于 2 位的 `select` 信号，应该有 4 个可能值（`00`, `01`, `10`, `11`），但这里只处理了 3 个情况。如果 `select` 为 `2'b11` 时，没有明确的输出，会导致不确定行为。
   
### 修正：
需要为未指定的 `case` 分支添加默认值（`default`）来处理未覆盖的情况。

```verilog
always @(*) begin
    case (select)
        2'b00: out = in0;
        2'b01: out = in1;
        2'b10: out = in2;
        default: out = 1'b0;  // 默认情况处理，例如输出 0
    endcase
end
```

### 总结

1. **敏感列表应包含所有相关信号**：确保 `always` 块的敏感列表包括所有输入信号，以避免仿真中的更新不完整。
2. **避免反馈环路**：组合逻辑中，信号反馈会导致逻辑环路，必须避免。
3. **确保覆盖所有条件**：在 `case` 语句或条件判断中，应确保所有可能的信号值都被处理，否则可能导致不确定行为。



## Administrivia

- **Homework 1**：今天发布，需要及时开始处理。
- **讨论课**：本周讨论课开始，建议积极参与，与同学们互动。
- **FPGA 实验课**：
  - 如果课表允许，建议学生切换到周一的实验课程，以帮助排队等候的学生获得实验机会。
  - 所有实验将统一评分，避免学生因课程安排不同而产生差异。

---





# Sequential Circuits

<div style="display: flex; align-items: center;">
  <div style="flex: 60%;">
<h3>时序逻辑概要</h3>

<p>时序逻辑不仅依赖于当前输入，还依赖于电路的状态。在同步电路中，状态由时钟驱动更新。Verilog 支持使用锁存器（Latches）和触发器（Flip-Flops）来实现状态存储。重要概念包括：</p>

<ul>
    <li><strong>锁存器和触发器（Latches and Flip-Flops）</strong>：用于存储电路状态。</li>
    <li><strong>Always 块</strong>：在时序逻辑中，<code>always</code> 块用于描述触发器，敏感列表包括时钟信号。</li>
    <li><strong>敏感列表（Sensitivity List）</strong>：定义在信号何时发生变化时执行 <code>always</code> 块。</li>
    <li><strong>阻塞与非阻塞赋值（Blocking vs Nonblocking Assignment）</strong>：用于区分组合逻辑和时序逻辑的不同赋值方式。</li>
</ul>

  </div>

  <div style="flex: 40%;">
   <img src="{{ site.baseurl }}/docs/assets/image-20240905111306590.png" alt="image-20240905111306590" style="zoom:80%;" />
  </div>

</div>



## Sequential Logic

**时序逻辑（Sequential Logic）** 是芯片设计中的一种逻辑类型，其中输出不仅取决于当前输入，还取决于电路的状态。这种状态存储了电路的历史输入信息。

### 关键概念：

1. **状态与输出的关系**：输出是当前输入和电路状态的函数。状态由电路的历史输入决定，是电路中的记忆单元。
   
2. **状态的功能**：
   - 状态表示电路中的存储器。
   - 状态是之前输入信号的函数，电路通过存储前一个状态信息来决定当前行为。

3. **时钟驱动**：在同步数字系统中，状态的更新由时钟信号控制。每当时钟信号发生变化时，电路会根据当前输入和状态进行更新。

![image-20240905112052487]({{ site.baseurl }}/docs/assets/image-20240905112052487.png)

如图所示，电路的输入为 `A`, `B`, `C`，输出是 `F(A, B, C, State)`，这表明输出不仅取决于当前的输入，还取决于电路的状态。状态（State）由电路中的触发器或锁存器存储，并在时钟脉冲到来时更新。

时序逻辑通过存储历史信息来影响电路行为，适用于需要存储数据或历史状态的场景。通过锁存器和触发器，电路可以记忆之前的输入，并在下一个时钟周期更新状态。这种机制是现代数字电路设计的基础。

## Timing

**触发器时序（Flip-flop Timing）** 是时序逻辑设计中的关键概念，它决定了数据在时钟信号下的流动。在这里，我们讨论的是 **D 触发器**，这种触发器的输出 `Q` 会在时钟上升沿（或下降沿）时更新为输入 `D` 的值。

![image-20240905112242769]({{ site.baseurl }}/docs/assets/image-20240905112242769.png)

### Flip-flop 时序：

- **D 触发器**：当时钟信号 `Clk` 触发时，输入信号 `In` 会传递到输出 `Out`。
- **时序延迟**：在时钟信号的上升沿之后，存在一个 `Tclk-Q` 的延迟，即从时钟上升沿到输出稳定的时间。

时序延迟非常重要，因为它影响了电路的最大时钟频率。要确保电路能够在给定的时钟频率下正确工作，触发器的时序特性需要在设计中被准确计算和考虑。

## Register

**寄存器（Register）** 是由多个触发器组成的存储单元，它用于同步存储多位数据。典型的寄存器由一组 D 触发器组成，每个位对应一个触发器，通过时钟信号控制数据的存储和更新。

### 4-bit Register

![image-20240905112415787]({{ site.baseurl }}/docs/assets/image-20240905112415787.png)

- 该寄存器包含 **4 个 D 触发器**，每个触发器存储一位数据（In[0] 到 In[3]）。
- **时钟信号 Clk** 控制所有触发器同步更新输入数据，并将其输出到 Out[0] 到 Out[3]。
- 在时钟上升沿时，寄存器会将输入的 4 位数据同步存储，并在下一时钟周期之前保持输出不变。

### Accumulator

![QQ_1725506860912]({{ site.baseurl }}/docs/assets/QQ_1725506860912.png)

**累加器（Accumulator）** 是一种特殊类型的寄存器，它的作用是在时钟的每个周期内将输入值与寄存器中的当前值进行相加，并将结果存储回寄存器。

- **加法逻辑**：累加器将输入 `A[2:0]` 与之前累积的值 `Sum[3:0]` 进行加法运算，结果存储在 `Out[3:0]` 中。
- **时钟同步**：每当时钟信号上升沿到来时，累加器会将输入与累积值相加，并将和输出作为下一时钟周期的输入。

累加器常用于数字信号处理和控制系统中，用来实现累加、积分等功能。



## State Elements in Verilog

在 Verilog 中，**状态元素**（如触发器和锁存器）的行为只能通过 `always` 块来定义。这是因为 `always` 块提供了指定电路时序行为的灵活性，而综合工具则会根据这些行为推导出相应的状态元素实例。

### D 触发器与同步设置和复位（D-flip-flop with synchronous set and reset）

以下是一个带有同步设置（set）和复位（reset）的 D 触发器的 Verilog 实现：

![image-20240905113158699]({{ site.baseurl }}/docs/assets/image-20240905113158699.png)

```verilog
module dff(q, d, clk, set, rst);
    input d, clk, set, rst;
    output q;
    reg q;

    always @(posedge clk)
        if (rst)
            q <= 1'b0;  // 复位时，q 设置为 0
        else if (set)
            q <= 1'b1;  // 设置时，q 设置为 1
        else
            q <= d;     // 正常情况下，q 赋值为 d
endmodule
```

- **关键字 `posedge`**：该语句表示在时钟的上升沿触发行为。`always @(posedge clk)` 意味着该块将在每次时钟上升沿时执行。
- **优先级逻辑**：在这个实现中，复位信号 `rst` 优先级最高，接着是设置信号 `set`，最后是输入信号 `d`。这意味着当 `rst` 为高时，q 无论其他信号为何，都会被置为 0；当 `rst` 为低且 `set` 为高时，q 被置为 1。

## The Sequential always Block

Verilog 中的 `always` 块可以用于描述两类逻辑：**组合逻辑** 和 **时序逻辑**。这两种逻辑的区别在于是否依赖时钟信号。

![image-20240905113248316]({{ site.baseurl }}/docs/assets/image-20240905113248316.png)

### 组合逻辑 always 块

在组合逻辑设计中，`always` 块基于输入信号的变化来执行，而不依赖时钟信号。以下是一个多路选择器的例子，使用 `always @(*)` 来实现组合逻辑。

```verilog
module comb(input a, b, sel, output reg out);
    always @(*) begin
        if (sel)
            out = b;
        else
            out = a;
    end
endmodule
```

- **`always @(*)`**：`(*)` 表示该 `always` 块对所有输入信号的变化敏感，任何信号变化都会触发该块执行。这里的输出 `out` 是组合逻辑的结果，它没有任何存储状态。

![image-20240905113643842]({{ site.baseurl }}/docs/assets/image-20240905113643842.png)

### 时序逻辑 always 块

时序逻辑依赖时钟信号，在每次时钟边沿时执行逻辑。例如：

```verilog
module seq(input a, b, sel, clk, output reg out);
    always @(posedge clk) begin
        if (sel)
            out <= b;
        else
            out <= a;
    end
endmodule
```

- **`always @(posedge clk)`**：时序逻辑通常基于时钟信号的上升沿或下降沿触发。此处的 `out` 会在每次时钟上升沿时根据选择信号 `sel` 决定是否赋值为 `a` 或 `b`。这种类型的逻辑会存储先前的状态。

### 组合逻辑与时序逻辑的比较

- **组合逻辑（Combinational Logic）**：输出仅取决于当前输入，不涉及时钟或存储。
  - 使用 `always @(*)` 监控所有输入变化。
  - 适用于多路选择器等没有状态的逻辑。
  
- **时序逻辑（Sequential Logic）**：输出不仅取决于当前输入，还取决于存储的状态（如触发器或锁存器）。
  - 使用 `always @(posedge clk)` 在时钟边沿执行逻辑。
  - 适用于存储器件和具有状态的电路，如寄存器。

通过 `always` 块，设计者可以灵活地描述不同类型的逻辑，综合工具会根据这些描述生成相应的硬件结构。



## Blocking vs. Nonblocking Assignments

在 Verilog 中，`always` 块内的赋值可以通过两种方式完成：**阻塞赋值（Blocking Assignment）** 和 **非阻塞赋值（Nonblocking Assignment）**。这两种赋值方式虽然语法上相似，但在执行顺序和仿真行为上有显著区别。

### Blocking Assignment (`=`)

- **行为**：阻塞赋值会立即进行评估和赋值操作。在一个 `always` 块中，当前语句的执行会阻塞后续语句，直到完成赋值。
- **语法**：使用 `=` 进行赋值。

示例：

```verilog
always @(*) begin
    x = a | b;       // 立即评估 a | b，并将结果赋值给 x
    y = a ^ b ^ c;   // 评估 a ^ b ^ c，并将结果赋值给 y
    z = b & ~c;      // 评估 b & ~c，并将结果赋值给 z
end
```

在这个例子中，Verilog 会按顺序依次计算 `x`、`y` 和 `z`，每个赋值完成后，才会继续执行下一行语句。这会导致变量 `x` 的更新立即影响后续语句中的计算结果。

### Nonblocking Assignment (`<=`)

- **行为**：非阻塞赋值会在当前时间步（time step）结束后，统一更新所有的赋值操作。这意味着所有右侧的表达式会在同一时间点进行评估，而赋值则在整个时间步结束时同步完成。
- **语法**：使用 `<=` 进行赋值。

#### 示例：

```verilog
always @(*) begin
    x <= a | b;      // 评估 a | b，但推迟赋值给 x，直到当前时间步结束
    y <= a ^ b ^ c;  // 评估 a ^ b ^ c，但推迟赋值给 y
    z <= b & ~c;     // 评估 b & ~c，但推迟赋值给 z
end
```

在这个例子中，所有的右侧表达式会立即被评估，但赋值操作会推迟到时间步结束时同时进行。因此，`x`、`y`、`z` 的赋值不会相互干扰。

## Assignment Styles for Sequential Logic

**阻塞赋值和非阻塞赋值** 在时序逻辑设计中有重要的应用场景，特别是在触发器（flip-flop）的设计中。正确使用这两种赋值方式可以避免时序问题和逻辑错误。

### 阻塞赋值（Blocking Assignment）

在时序逻辑中使用阻塞赋值会导致先赋值的信号立即影响后续信号。以下是使用阻塞赋值的例子：

```verilog
module blocking(
    input in, clk,
    output reg out
);
    reg q1, q2;
    always @(posedge clk) begin
        q1 = in;       // 立即将 in 赋值给 q1
        q2 = q1;       // q2 使用更新后的 q1
        out = q2;      // out 使用更新后的 q2
    end
endmodule
```

- 这种赋值方式可能导致在同一个时钟周期内，变量的值变化会影响后续的变量。对于时序逻辑，使用阻塞赋值时需要非常小心，以避免逻辑错误。

### 非阻塞赋值（Nonblocking Assignment）

在时序逻辑中，非阻塞赋值更常用，因为它能避免同一时间步内的干扰，确保赋值顺序的一致性：

```verilog
module nonblocking(
    input in, clk,
    output reg out
);
    reg q1, q2;
    always @(posedge clk) begin
        q1 <= in;      // 推迟到时间步结束时赋值 q1
        q2 <= q1;      // q2 推迟赋值，q1 不会立即影响 q2
        out <= q2;     // out 推迟赋值，q2 不会立即影响 out
    end
endmodule
```

- 非阻塞赋值保证了所有赋值在时钟周期结束时同时进行。这避免了赋值链中的先后关系问题，使得设计更具时序稳定性。

在时序逻辑设计中，推荐使用**非阻塞赋值**来避免时序上的不一致性，确保所有寄存器在同一时间步内同步更新。

## Use Nonblocking for Sequential Logic

在时序逻辑设计中，推荐使用**非阻塞赋值（Nonblocking Assignment, `<=`）**，因为它能正确反映多级时序逻辑的行为。阻塞赋值在多级逻辑中的使用可能导致逻辑错误，因为它立即更新值，从而干扰其他逻辑信号的预期行为。非阻塞赋值确保所有赋值在时钟上升沿之后同步完成。

### Nonblocking Assignment Example

```verilog
always @(posedge clk) begin
    q1 <= in;       // 在时钟上升沿，将 in 的值赋给 q1
    q2 <= q1;       // 将 q1 的值赋给 q2，但使用的是旧的 q1 值
    out <= q2;      // 将 q2 的值赋给 out，使用的是旧的 q2 值
end
```

在该示例中，所有赋值操作在时钟上升沿时同时进行，但实际赋值的信号是上一个时钟周期的状态（旧值）。这与时序逻辑的物理行为保持一致：输入信号依次通过各级寄存器同步传递。

### 重要特性

- **旧值与新值**：在时钟上升沿，`q1` 立即获得输入信号 `in`，但 `q2` 和 `out` 仍然使用旧的 `q1` 和 `q2` 值。所有赋值将在时间步结束时同步更新，因此设计行为在时钟边沿之间是确定的。
- **多级时序逻辑的正确性**：非阻塞赋值能保持多级寄存器电路的正确行为，确保每一级只在下一个时钟周期时接收前一层的更新值。

### 阻塞赋值的潜在问题

```verilog
always @(posedge clk) begin
    q1 = in;        // q1 立即更新为 in
    q2 = q1;        // q2 使用新的 q1 值，而不是旧的 q1 值
    out = q2;       // out 使用新的 q2 值，而不是旧的 q2 值
end
```

在这种阻塞赋值中，`q1` 和 `q2` 的值会立即更新，导致 `q2` 和 `out` 在同一时钟周期内同时使用新值。这种行为与实际硬件中的时序逻辑不符，可能会导致仿真结果与硬件实现不一致。

### Guideline

在**时序逻辑**中，应始终使用 **非阻塞赋值 (`<=`)**，避免使用阻塞赋值。非阻塞赋值可以正确模拟寄存器级联的行为，并保持逻辑的同步性。

## Simplified Verilog Guidelines2

在设计 Verilog 代码时，以下是两类逻辑的简化指南：

### Combinational Logic (组合逻辑)

组合逻辑的输出只依赖当前输入，不涉及时钟信号。可以通过连续赋值（`assign`）或组合逻辑的 `always` 块来实现。

- **Continuous Assignment**：使用 `assign` 定义组合逻辑。
  
  ```verilog
  assign a = b & c;
  ```

- **Always Block**：使用 `always @(*)` 定义更复杂的组合逻辑。

  ```verilog
  always @(*) begin
      a = b & c;  // 阻塞赋值，用于组合逻辑
  end
  ```

### Sequential Logic (时序逻辑)

时序逻辑依赖时钟信号，使用 `always @(posedge clk)` 实现。必须使用 **非阻塞赋值 (`<=`)** 来确保多级寄存器和时序电路的正确性。

- **Always Block with `@posedge clk`**：

  ```verilog
  always @(posedge clk) begin
      a <= b & c;  // 非阻塞赋值，用于时序逻辑
  end
  ```

### 总结

- 组合逻辑：使用 `assign` 语句或 `always @(*)`，使用阻塞赋值 `= `。
- 时序逻辑：使用 `always @(posedge clk)`，必须使用非阻塞赋值 `<=`。



## Verilog in EECS 151/251A

在 EECS 151 和 251A 课程中，Verilog 主要用于描述硬件行为，尤其是在层次结构的底部（最细粒度的部分）。这种行为建模能够帮助学生和工程师用代码高效描述复杂电路的行为。

### 主要指导原则：

1. **使用实例化（Instantiation）**：
   - 实例化用于构建层次化的设计（如子模块）以及映射到 FPGA 和 ASIC 资源中。
   
2. **优先使用连续赋值（Continuous Assign）**：
   - 除非遇到特殊情况（如需要处理状态元素、使用 `case` 语句等），否则尽量避免使用 `always` 块。连续赋值能提高代码的可读性和清晰度。
   - 避免 `always` 块能使代码结构更加简洁，特别是在处理大型的嵌套 `if-else` 语句时。
   
3. **使用命名端口（Named Ports）**：
   - 这种方法有助于提高代码的可维护性，特别是在模块实例化时。

### Verilog 是一门复杂的语言

- 本课程中的 Verilog 只是入门。推荐参考 **Harris & Harris** 的书籍第 4 章。
- **小心网络上的资源**：互联网上的 Verilog 示例不一定可靠，很多错误的例子可能导致误解。
- 在整个学期中，会逐步引入更多有用的 Verilog 语法和设计技巧，帮助学生深入了解硬件设计。

## Final Thoughts on Verilog Examples

数字设计中的重要部分是学会如何用 Verilog 代码表达你所需的电路。

- **首先理解电路**：在编写 Verilog 代码之前，确保你对所需的电路结构有清晰的理解。
- **Verilog 只是工具**：用 Verilog 描述硬件时，编码只是其中的一部分，关键是对电路行为和逻辑的理解。如果没有清晰的电路模型和设计思路，编写 Verilog 代码时会遇到困难。

## Review

### Verilog 关键概念

- **硬件描述语言（HDL）**：Verilog 是一种用于描述硬件结构和行为的语言。
- **逻辑综合（Logic Synthesis）**：将 Verilog 代码转换为门级网表（Gate-level Netlists），并用于实际的硬件实现。

### Verilog 的应用

- **模块（Modules）**：Verilog 中的基本构建块，可以表示不同级别的硬件逻辑。
- **结构化和行为化描述**：Verilog 支持结构化建模和行为建模，允许用户精确控制电路的物理连接或描述电路的功能。
- **操作符和逻辑值**：Verilog 中的各种操作符（如 `&`、`|`、`^`）用于定义逻辑运算，支持对位和布尔值的操作。

### 组合逻辑（Combinational Circuits）

- **Assign 语句**：用于定义简单的组合逻辑。
- **Always 块**：用于处理更复杂的组合逻辑，通常结合 `if` 或 `case` 语句。

### 时序逻辑（Sequential Circuits）

- **Always 块**：在时钟边沿处理逻辑，定义存储状态的行为。
- **阻塞与非阻塞赋值（Blocking vs Nonblocking Assignment）**：
  - **阻塞赋值**用于组合逻辑，顺序执行赋值操作。
  - **非阻塞赋值**用于时序逻辑，确保赋值在每个时间步结束后同步完成。



