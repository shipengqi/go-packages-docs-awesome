# 卸载

要卸载 `dep` 本身，请遵循这些说明，具体取决于你的原来安装 `dep` 的方式。

## 如果是 curl 通过执行 `install.sh` 脚本来安装 `dep`

如果你使用 `install.sh` 安装了 `dep`，只需删除已安装的二进制文件即可。

在 Linux 和 MacOS 上，`install.sh` 脚本安装预编译的二进制文件 `$GOPATH/bin/dep`。简单就是安全的 `rm` 已安装 `$GOPATH/bin/dep` 文件:

```sh
$ rm $GOPATH/bin/dep
```

在 Windows 上，`install.sh` 脚本安装预编译的二进制文件 `$GOPATH/bin/dep.exe`。只需删除此文件即可安全卸载 `dep`。

## 在 MacOS 上，如果你使用 Homebrew 安装了 `dep`

在 MacOS 上，如果你使用 Homebrew 安装了 `dep`。 卸载 `dep` 也使用 Homebrew:

```sh
$ brew uninstall dep
```

## 在 Arch Linux 上， 如果你运用 `pacman` 安装了 `dep`

卸载 `dep` 像这样:

```sh
$ pacman -R dep
```
