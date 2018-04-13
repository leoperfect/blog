# linux创建用户并添加到sudo用户组中

> ​	新服务器一般只有root用户，因为linux是一个多用户、多任务的操作系统，出于安全考虑，一般用户在操作系统时，不建议使用root用户，所以需要添加新用户。

## 1.  useradd adduser

1. useradd 本身是个linux脚本 在执行`useradd xxx`时，会创建一个用户名为`xxx`的三无用户

   a. 无密码 需要通过`passwd`来设置密码

   b. 无家目录 不会自动创建`/home/xxx`目录

   c. 无登录权限 不能关联`shell`进行

2. `adduser`会以人机交互的方式创建好用户，该用户有家目录并能够登录shell，当执行`adduser pengfei.li`时会有哪些变化呢？

   a. 会在`/etc/passwd`上新增一行记录 `pengfei.li:x:1001:1001:,,,:/home/pengfei.li:/bin/bash`

   b. 会创建`/home/pengfei.li`目录作为用户的家目录

## 2. 给用户赋予sudo权限

在root用户下执行

```sh
$ usermod -aG sudo pengfei.li
```

或在有sudo权限的用户下执行

```shell
$ sudo usermod -aG sudo pengfei.li
```

> usermod 修改用户
>
> -a 仅和-G一起使用，表示将用户添加到附属组中
>
> -G 修改用户的附属组
>
> 执行上面命令后，会在`/etc/group`文件中 将`sudo:x:27`改为`sudo:x:27:pengfei.li`，表示将用户`pengfei.li`加入到了`sudo`附属组中。

## 3. sudo不使用密码

在第二步之后，退出`shell`并重新登陆后，用户便可以执行`sudo`了，但是每次都要输入密码，那怎么可以不输入密码就能执行sudo呢？

```
// 在root用户下，或者使用sudo
$ visudo
// 修改为以下内容，并重新登录shell
%sudo   ALL=(ALL:ALL) NOPASSWD: ALL

```

