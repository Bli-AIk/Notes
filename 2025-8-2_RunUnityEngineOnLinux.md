# 在 Arch Linux 运行 Unity 踩坑记录

简单的记录一下在 Arch Linux 上折腾 Unity Engine 的一点心得（

## 安装 Unity Hub

可以装AUR包或Flatpak。

我目前用的是AUR包，感觉良好，安装 Hub 和 下载 Editor 都非常顺利，这部分没啥好说的；

查询资料时看有人说 Flatpak 由于是沙箱 没法配置到本地的编辑器——我没试过，见仁见智吧

## Unity Engine 加载项目时卡死

最折腾我的是这一点。其表现为新建项目 / 加载现有的项目时，如果 Unity Engine 需要创建 **Library** 目录，那么进度条跑一半就会卡住不动……

最后在 Unity Engine 的 **Editor.log** 文件中发现了这么一条重要的信息：

```
[             ] Require frontend run.  Library/Bee/2400b0aE.dag couldn't be loaded
```

这一条出现后log很快就停住了。

我的解决方案来自于

https://discussions.unity.com/t/question-what-is-that-bee-stuff/844386 

和

https://discussions.unity.com/t/linux-editor-stuck-on-loading-because-of-bee-backend-w-workaround/854480

(↑主要是这个)

根据链接2的方式处理即可：

- 进入 Unity 编辑器文件夹，然后进入 Data 文件夹
- 将 `bee_backend` 重命名为 `bee_backend_real`
- 创建一个名为 `bee_backend` 的文件，`chmod`赋予其可执行权限
- 在新创建的 `bee_backend` 文件中写入以下内容

```bash
#! /bin/bash

args=("$@")
for ((i=0; i<"${#args[@]}"; ++i))
do
    case ${args[i]} in
        --stdin-canary)
            unset args[i];
            break;;
    esac
done
${0}_real "${args[@]}"
```

## 报错：No usable version of libssl was found

```
yay -S openssl-1.1
```

装上就完事了
