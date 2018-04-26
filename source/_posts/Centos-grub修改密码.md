---
title: Centos grub修改密码
date: 2018-04-02 15:24:54
tags: linux
categories: linux
---
在`linux`中，`grub`是用来驱动系统内核的。有时候，我们也可以通过`grub`来修改`root`密码，只要在内核启动方式选择单用户启动就可以了（init 1）。
为了保险起见，我们可以设置`grub`密码，来防止别人通过以上途径修改`root`密码。更改`grub`配置文件,
```linux
vim /etc/grub.conf
```
找到文件里面的`title`关键字，在`title`上面加个`password`就可以加密`grub`，在下面价格`password`就可以在进入系统的时候加密。
如果我们需要`md5`加密这些密码的话，可以使用
```linux
grub-md5-crypt
```
输出一串加密后的字符串，复制，然后在`/etc/grub.conf`中就是
`password --md5 字符串`