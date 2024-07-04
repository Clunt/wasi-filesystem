# WASI文件系统路径解析（WASI filesystem path resolution）

wasi-filesystem使用文件系统路径沙盒方案，改方案模仿[CloudABI]中使用的系统，也类似于[Capsicum]中使用的系统。

在Linux上，它对应于[Linux's `openat2`]中的`RESOLVE_BENEATH`行为。
在FreeBSD上，它对应于[FreeBSD's `open`]中的`O_RESOLVE_BENEATH`行为。
然而，路径解析也可以使用`openat`和`readlinkat`或类似原语手动实现。

## 沙盒概述（Sandboxing overview）

wasi-filesystem中所有 操作文件系统路径的函数都接受一对值：基目录句柄和相对路径。
不允许绝对路径，也没有全局命名空间。
所有路径访问都相对于基目录句柄。

路径解析被限制在基础句柄引用的子文件系统内进行。
基目录句柄外的文件系统信息不可见。
特别是，不允许使用临时超出沙盒范围的路径，例如“../../../stuff/here”，即使最终解析的路径回到了沙盒内，
因为这会泄漏沙盒外部目录的实体信息。

重要的是，沙盒设计为即使在外部进程访问同一文件系统的情况下也可实现，
包括重命名(renaming)、取消链接(unlinking)和创建新文件和目录。

## 符号链接（Symlinks）

使用绝对路径字符串创建符号链接会导致“not permitted”(不允许)的错误。

除此之外，只要底层文件系统实现支持，就可以使用任何字符串创建符号链接。

符号链接字符串的沙盒化是在访问时执行的，也就是在路径被解析的时候，而不是在创建或移动符号连接时。
这确保了即使有其他实体访问文件系统并创建或重命名符号链接，沙盒化也被遵守。

## 宿主实现（Host Implementation）

### 手动实现路径解析（Implementing path resolution manually）

普通的`openat`不执行任何沙盒化；它会迅速的打开包含“..”或以“/”开头的路径，或包含“..”或以“/”开头的符号连接。
它有`O_NOFOLLOW`标志，然而此标志仅适用于路径最后的构件（例如，“a/b/c”中的“c”）。
因此实现`openat`沙盒化的策略是将路径拆分为构件（例如“a”、“b”、“c”），并且每次打开一个构件，以便每个构件都可以用`O_NOFOLLOW`打开。

如果`openat`调用失败，并且操作系统错误码(OS error code)指示其*为*符号连接（例如`ELOOP`），
则调用`readlinkat`读取链接内容，将内容拆分为构件，并将新构件添加到构件列表前。
如果它以绝对路径开头，则试图跳出沙盒，所以路径解析应失败并显示“access denied”(访问被拒绝)的错误消息。

如果路径构件为“..”，则不会打开它，而是从构件列表中弹出(pop)一项。
如果该列表为空，则表示试图使用“..”移出沙盒，所以路径解析应失败并显示“access denied”(访问被拒绝)的错误消息。

### 实现说明（Implementation notes）

在Linux上，可以使用带有`RESOLVE_BENEATH`的`openat2`作为优化，
通过利用Linux的`O_PATH`和"/proc/self/fd"特性，来实现除“open”外的许多系统调用。

在Windows上，[`NtCreateFile`]函数可以接受一个目录句柄且行为类似于`openat`函数，这可在[manual algorithm](implementing-path-resolution-manually)中使用。

Rust库[cap-std]实现了WASI的文件系统(filesystem)沙盒化语义，但除此之外独立于WASI或Wasm，因此可以在其他环境中复用。
它使用了`openat2`和`NtCreateFile`以及其他优化。

cloudabi-utils拥有[implementation of the manual technique in C]，尽管该库已不再维护。

[implementation of the manual technique in C]: https://github.com/NuxiNL/cloudabi-utils/blob/master/src/libemulator/posix.c#L1205
[cap-std]: https://github.com/bytecodealliance/cap-std
[Linux's `openat2`]: https://man7.org/linux/man-pages/man2/openat2.2.html
[CloudABI]: https://github.com/NuxiNL/cloudabi
[Capsicum]: https://wiki.freebsd.org/Capsicum
[FreeBSD's `open`]: https://man.freebsd.org/cgi/man.cgi?sektion=2&query=open
[`NtCreateFile`]: https://learn.microsoft.com/en-us/windows/win32/api/winternl/nf-winternl-ntcreatefile
