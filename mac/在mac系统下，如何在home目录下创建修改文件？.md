# 在mac系统下，如何在/home目录下创建修改文件？

> macOS是基于Unix的系统，所以默认会存在/home目录，但是/home的所属用户组是wheel，root用户的默认用户组是admin，所以哪怕有root权限还是不能在/home下做一些操作。那么我们该怎么做呢？
1. 修改/etc/auto_master文件

```bash
#
# Automounter master map
#
+auto_master            # Use directory service
/net                    -hosts          -nobrowse,hidefromfinder,nosuid
# /home                 auto_home       -nobrowse,hidefromfinder      //将该行注释掉
/Network/Servers        -fstab
/-                      -static
```

2. 使修改生效

```bash
$ cd / //切换到根目录
$ sudo automount // 在根目录下执行
```

3. 在/home下通过sudo进行操作

```bash
$ sudo mkdir /home/admin
```

