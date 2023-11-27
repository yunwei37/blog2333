---
title: 使用 Intel Control-flow Enforcement Technology (CET) 加强 C 程序的安全性

date: 2023-11-27T08:41:06-08:00
tags: notes
---

#### 简介

Intel 的 Control-flow Enforcement Technology (CET) 是一种旨在提高软件安全性的硬件级特性，用于防止控制流劫持攻击，如返回导向编程（ROP）和跳转导向编程（JOP）。CET 主要通过两种机制实现：间接分支跟踪（IBT）和阴影栈（Shadow Stack）。

#### CET 实现原理

1. **间接分支跟踪 (IBT)**：要求所有间接跳转（如函数指针调用）必须跳转到用 `ENDBRANCH` 指令标记的目标。这阻止了通过篡改内存来创建恶意跳转的尝试。

2. **阴影栈 (Shadow Stack)**：为每个线程独立存储返回地址的副本。当函数返回时，处理器会比较当前的返回地址和阴影栈中的地址。如果它们不匹配，处理器会阻止返回操作，防止ROP攻击。

#### 示例代码

下面的示例展示了一个简单的C程序，其中包含函数指针的使用和尝试非法访问的场景。

```c
#include <stdio.h>
#include <stdlib.h>

void safe_function() {
    printf("Safe function called.\n");
}

void unsafe_function() {
    printf("Unsafe function called.\n");
}

int main() {
    // 正常的函数指针使用
    void (*func_ptr)() = safe_function;
    func_ptr();

    // 尝试非法修改函数指针
    // 在一个CET支持的环境中，这种修改会被检测并阻止
    func_ptr = (void (*)())((char*)safe_function + 2);
    func_ptr(); // 这里的调用在CET环境下应该会失败

    return 0;
}

```

在这个例子中，`func_ptr` 最初指向 `safe_function`。然后，代码尝试将 `func_ptr` 修改为 `safe_function` 地址的偏移，模拟一个非法的控制流修改尝试。在启用了 CET 的环境中，这种尝试应该会导致程序崩溃或异常终止。

#### 编译和运行

使用支持 CET 的 GCC 编译器，编译上述代码：

```bash
gcc -fcf-protection=full -o cet_demo cet_demo.c
```

然后运行编译出的程序：

```bash
./cet_demo
```

#### 测试 CET

在支持 CET 的环境中，程序中的第二次 `func_ptr` 调用（非法调用）将会失败，因为它尝试执行一个没有用 `ENDBRANCH` 指令标记的地址。这正是 CET 防止控制流劫持攻击的方式。

#### 结论

CET 是在深度防御策略中的一个重要层面，但它并不是万能的。良好的编程实践和其他安全措施仍然是必要的。CET 提供了一个硬件级别的保护层，使得控制流劫持攻击更加困难，从而提高了软件的整体安全性。
