# 系统Framework

# Lanucher
# aosp

## android.bp和init.rc
`android.bp` 和 `init.rc` 是两个在 Android 系统构建过程中具有重要作用的配置文件，它们之间的关系是相互独立的，分别承担不同的职责。

1. `android.bp`：
`android.bp` 是 Android Build System 的构建脚本文件，使用名为 Blueprint 的 DSL（Domain Specific Language）编写。Android 开源项目（AOSP）底层构建工具叫做 Soong，它会解析 `android.bp` 文件，并将相关的源代码编译成不同类型的目标（如共享库、可执行文件等）。`android.bp` 主要用于声明编译组件的名称、类型、源文件等，以及定义相关的依赖关系等。它与项目构建和编译相关，为 Android 系统生成模块。

2. `init.rc`：
`init.rc` 是 Android 系统的基本初始化脚本，使用名为 Init Language 的简单脚本编写。在 Android 系统启动时，会首先运行 `init` 进程，作为 PID（Process ID）为 1 的进程，它负责启动所有其他进程。`init.rc` 脚本由 `init` 进程解析，在系统启动期间执行一系列命令，例如设置权限、启动服务、设置属性等。这个文件是 Android 系统启动时的核心控制者，用于初始化和启动系统服务，以保证 Android 设备成功启动到用户界面。

总结一下，`android.bp` 和 `init.rc` 的关系在于它们都是 Android 系统的重要组成部分，一个用于模块构建和编译，另一个用于系统启动时的初始化，它们既相互独立又共同支持 Android 系统的正常运行