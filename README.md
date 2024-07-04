# WASI filesystem

[WebAssembly系统接口(WebAssembly System Interface)](https://github.com/WebAssembly/WASI)API提案。

### 当前阶段（Current Phase）

WASI-filesystem当前处于[第3阶段(Phase 3)][Phase 3]。

[Phase 3]: https://github.com/WebAssembly/WASI/blob/main/Proposals.md#phase-3---implementation-phase-cg--wg

### 拥护者（Champions）

- Dan Gohman

### 可移植性标准（Portability Criteria）

WASI filesystem必须有至少可以在Windows、macOS和Linux上通过测试套件(testsuite)的主机实现。

WASI filesystem必须至少有两个完整独立的实现。

## Table of Contents

- [介绍（Introduction）](#介绍introduction)
- [目标（Goals）](#目标goals)
- [非目标（Non-goals）](#非目标non-goals)
- [API详解（API walk-through）](#API详解api-walk-through)
  - [打开文件（Opening-a-file）](#打开文件opening-a-file)
  - [流式读取文件（Streaming-read-from a file）](#流式读取文件streaming-read-from-a-file)
  - [从目录中读取（Reading from a directory）](#从目录中读取reading-from-a-directory)
- [详细设计讨论（Detailed design discussion）](#详细设计讨论detailed-design-discussion)
  - [WASI filesystem应该大小写敏感、大小写不敏感还是依赖于平台? （Should WASI filesystem be case-sensitive, case-insensitive, or platform-dependent?）](#wasi-filesystem应该大小写敏感大小写不敏感还是依赖于平台should-wasi-filesystem-be-case-sensitive-case-insensitive-or-platform-dependent)
- [考虑替代方案（Considered alternatives）](#考虑替代方案considered-alternatives)
  - [完全确定性的文件系统（Fully deterministic filesystem）](#完全确定性的文件系统fully-deterministic-filesystem)
- [项目相关方利益 & 反馈（Stakeholder Interest & Feedback）](#项目相关方利益--反馈stakeholder-interest--feedback)
- [参考文献 & 致谢（References & acknowledgements）](#参考文献--致谢references--acknowledgements)

## 目录（Table of Contents）

WASI filesystem主要用于访问宿主文件系统的WASI。它具有打开、读取和写入文件以及处理目录的功能。

不同于许多文件系统API，WASI filesystem是面向能力的。
WASI文件系统API不是隐式引用文件系统命名空间的函数，
而是通过给定目录句柄(directory handle)和路径(path)，
然后相对于给定句柄查找路径，并在该目录内进行沙盒化。
关于沙盒的更多信息，参见[WASI filesystem路径解析(path resolution)](path-resolution.md)。

WASI filesystem隐藏了Windows和Unix风格文件系统之间的表面差异，
然而其大多行为都依赖于宿主，包括路径查找语义，文件、目录和符号连接的语义，以及对文件系统路径的限制。

WASI filesystem并不旨在用于访问任意资源的虚拟API。
Unix的“一切皆文件(everything is a file)”理念与支持模块化(modularity)和最小权限(least authority)原则的目标存在冲突。

许多关于如何使用`openat`进行基于能力的文件系统沙盒化的想法都来源于[CloudABI](https://github.com/NuxiNL/cloudabi)和[Capsicum](https://wiki.freebsd.org/Capsicum)。

### 目标（Goals）

WASI filesystem的主要目标是允许用户使用WASI程序以直接和高效的方式访问其现有的文件系统。

### 非目标（Non-goals）

WASI filesystem并不以确定性语义为目标。
这要么需要将其限制为完全受控的私有文件系统，这会与让用户访问其现有文件系统的目标相冲突，
要么需要实现做大量额外工作来模拟特定定义的行为，这会与高效的目标相冲突。

### API详解（API walk-through）

#### 打开文件（Opening a file）

```rust
/// 将"Hello, World"写入`dir`目录下名为"greeting.txt"的文件中。
fn write_hello_world_to_a_file(dir: Descriptor) -> Result<(), Errno> {
    let at_flags = AtFlags::FollowSymlinks;
    let o_flags = OFlags::Create | OFlags::Trunc;
    let descriptor_flags = DescriptorFlags::Write;
    let mode = Mode::Readable;
    let file =
        dir.openat(at_flags, "greeting.txt", o_flags, descriptor_flags, mode)?;
    let message = b"Hello, World\n";
    let mut view = &message[..];
    let mut offset = 0;
    while !view.is_empty() {
        let num_written = file.pwrite(view.to_owned(), 0)?;
        offset += num_written;
        view = &view[num_writen..];
    }
    // 文件描述符(file descriptor)在被删除(dropped)时被关闭(closed)！
}
```

相比于openat的preview1版本（称为`path_open`），最大的变化或许是移除了*rights*标志(flags)。
Preview1将一组标志(flags)与每个文件描述符关联，枚举可以对其执行的操作，例如读取(reading)、写入(writing)、附加(appending)、截断(truncating)和许多其他操作。
实际上，这在如何映射到POSIX语义方面造成了许多歧义，因为它不直接对应于POSIX或Windows中的任何功能。

与preview1相比，另一个重大变化是引入了模式参数(mode argument)，该参数控制所生成文件的权限。
在preview1中无法控制权限，因此这是新功能。

#### 流式读取文件（Streaming read from a file）

TODO

#### 从目录中读取（Reading from a directory）

fn read_entries(dir: Descriptor) -> Result<(), Errno> {
    // TODO: Implement this example.
}

[etc.

### 详细设计讨论（Detailed design discussion）

#### WASI filesystem应该大小写敏感、大小写不敏感还是依赖于平台? （Should WASI filesystem be case-sensitive, case-insensitive, or platform-dependent?）

即使在流行平台中，也有广泛使用大小写敏感和大小写不敏感的文件系统。

如果有一个API可以在各个平台上保持一致的行为将会非常好，这样应用程序就不必担心细微的差异，以及由于这些差异而产生的细微错误。

然而，要在大小写不敏感的文件系统上实现大小写敏感，或者在大小写敏感的文件系统上实现不大小写不敏感，都是很棘手的。

一个问题是，大小写不敏感依赖于Unicode版本，因此不同大小写不敏感的平台之间的细节可能会有所不同。
另一个问题是，WASI filesystem通常不能假设它对文件系统具有独占访问权限，所以涉及到检查只有大小写不同的文件名的方法，可能会与其他创建新文件进程产生冲突。

### 考虑替代方案（Considered alternatives）

#### 完全确定性的文件系统（Fully deterministic filesystem）

完全确定性的主要权衡在于，它使得Wasm运行时无法完全控制的现有文件系统的存续变得困难。这个提案旨在解决用户希望访问现有文件系统的用例。

### 项目相关方利益 & 反馈（Stakeholder Interest & Feedback）

进入第3阶段之前的TODO。

Preview1有一个类似的文件系统API，它在工具链中广泛公开。

### 参考文献 & 致谢（References & acknowledgements）

非常感谢以下人员提供的宝贵反馈和建议：

- [Person 1]
- [Person 2]
- [etc.]
