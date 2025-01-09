+++
title='Coq 安装'
date = '2025-01-09'
+++

Coq 提供了Linux、Windows和MacOS 在内的多系统安装。 虽然 Coq 官网提供了多中安装方式，我们更推荐使用 opam 方式安装（OCaml 包管理器）。接下来我们将演示如何在不同系统下 使用opam 安装 Coq。 读者亦可访问~\url{https://coq.inria.fr/download} 选择自己喜欢的安装方式。

## 安装 opam 
本小节内容亦可访问 [https://opam.ocaml.org/doc/Install.html](https://opam.ocaml.org/doc/Install.html) 查看。

Linux 和MacOs 可以使用 opam 官网提供的脚本一键安装。
```bash
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh)"
```
注意： 脚本会将 opam 默认安装到 `/usr/local/bin`，如不修改请确保当前用户该路径有读写权限。 

Windows 系统可以使用下面脚本在powershell中安装
```powershell
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) }"
```

亦可使用系统的包管理安装，但其无法保证安装的版本是最新的。
```bash
# MacOS
## Homebrew
brew install opam
## MacPort
port install opam

# Windows
winget install Git.Git OCaml.opam

# Alpine Linux 
pacman -S opam
## Alpine Linux  或
yay -S opam-git
# Debian Ubuntu
apt-get install opam
# Exherbo
cave resolve -x dev-ocaml/opam
# Exherbo 或
cave resolve -x repository/ocaml-unofficial
# Fedora CentOS 和 RHEL
dnf install opam
# Mageia
urpmi opam
# OpenBSD
pkg_add opam
# FreeBSD
pkg install ocaml-opam
```

安装完成后可以使用下面命令确认是否安装成功。如果成功将返回 opam的版本号，为确保Coq 安装成功，请安装最新版 opam。
```bash
opam --version
```
安装成功后需要执行下面命令 初始化 opam 状态：
```bash
opam init
```

至此 opam 安装配置完成。

## 使用 opam 安装 Coq
本小节内容亦可访问 [官方安装指南](https://coq.inria.fr/opam-using.html)。

首先确保 opam 安装成功并初始化，接下来可以使用下面命令自动安装，由于 Coq 需要从源码编译所以安装时间较长：
```bash
opam pin add coq 8.20.0
```
注意：Coq 最新版本可能已更新，如果想查看最近版本请访问 [Coq官网](https://coq.inria.fr/) 或者其 [github仓库](https://github.com/coq/coq)

以上述命令安装 Coq 不会自动更新，若更新请执行：
```bash
opam pin add coq <版本号>
```bash

使用下面命令可以查看 Coq 是否被正确安装：
```bash
coqc --version
```

## IDE 安装
上一节只安装了 Coq 命令行工具，为方便使用，我们需要安装CoqIDE 或其他编辑器插件。 这里我们演示 CoqIDE安装和 Visual Studio Code 插件安装。

首先请确保 opam 和 Coq 已正确安装。
### CoqIDE 安装
使用下面命令安装 CoqIDE:
```bash
opam install coqide
```

### Visual Studio Code 插件安装
本小节亦可访问 [vscoq仓库](https://github.com/coq/vscoq) 获取最新信息。

根据下列命令安装语言服务：
```bash
opam install vscoq-language-server
```
在 Visual Studio Code 扩展中搜索 **maximedenes.vscoq** 并安装。