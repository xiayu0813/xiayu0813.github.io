---
title: "manjaro配置"
date: 2020-11-22T22:59:17+08:00
draft: false
tags: 
- manjaro
- linux
categories:
- 学习
- linux
---

* 安装vim

`pacman -S vim`

* 安装AUR源管理工具

`sudo pacman -S yay`

* 配置archlinuxcn aur源

编辑`/etc/pacman.conf`，在最后添加

```
 [archlinuxcn]
# The Chinese Arch Linux communities packages.
# SigLevel = Optional TrustedOnly
SigLevel = Optional TrustAll
# 清华大学
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```
安装keyring: `sudo pacman -S archlinuxcn-keyring`

* 更新源排名（不知道作用）
`sudo pacman-mirrors -i -c China -m rank`
`sudo pacman-mirrors -g`

* 更新系统

`sudo pacman -Syyu`


* 安装git

`sudo pacman _-S git`

* 安装vscode 
  
`yay -S visual-studio-code-bin`

* 设置代理
  * 安装qv2ray

    `yay -S qv2ray`

  * 安装v2ray

    `yay -S v2ray`

  * 具体配置见[qv2ray start](https://qv2ray.net/getting-started/)

  * 设置shell代理

    export HTTP_PROXY="http://127.0.0.1:8889"
    export HTTPS_PROXY="http://127.0.0.1:8889"

    这种配置目前对git有效，其他未测试

* 安装oh-my-zsh
  
  * 安装zsh
    
    `sudo pacman -S zsh`
  * 设置默认shell为zsh
    
    `chsh -s /bin/zsh`
  * 安装oh-my-zsh

    `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

    安装时会用到git，因此需先安装git并设置代理。

    使用该脚本自动安装时不需要上一步设置默认shell的操作，脚本会询问该设置。

    另一种安装方式是使用AUR库`yay -S oh-my-zsh-git`，优点是可以随系统自动更新，缺点是安装目录为*user/share*，安装主题及相关插件不方便。最终选择使用脚本的安装方式。

  * 安装zsh-autosuggesiton插件
  `git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
  
  * 安装zsh-syntax-highlighting插件
  `git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`

  * 安装主题powerlevel10k
  `git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k`

  * 安装zsh代理插件zhs-proxy
  `git clone https://github.com/sukkaw/zsh-proxy.git ~/.oh-my-zsh/custom/plugins/zsh-proxy`
  安装完成后配置插件，运行`init_proxy`初始化，运行`config_proxy`配置代理IP，运行`proxy`启动代理，运行`noproxy`关闭代理

  * 安装autojump
  `yay -S autojump`

  * 安装zsh-completions
  `  git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions`

  * 安装字体yay -S nerd-fonts-complete
  `yay -S nerd-fonts-complete`

  * 配置*.zshrc*，主要配置主题和插件
  ZSH_THEME="powerlevel10k/powerlevel10k"
  plugins=(git autojump zsh-autosuggestions zsh-syntax-highlighting zsh-completions zsh-proxy)

  重新加载配置`autoload -U compinit && compinit`

* 安装fcitx5，rime输入法

  * 安装的是archlinuxcn源中的fcitx5
  `yay -S fcitx5-git fcitx5-configtool-git fcitx5-rime-git fcitx5-chinese-addons-git fcitx5-qt4-git fcitx5-gtk-git fcitx5-qt5-git`

  * 查看桌面系统是X11还是wayland，
  `echo ${XDG_SESSION_TYPE}`

针对X11桌面系统，编辑~/.xprofile
  export GTK_IM_MODULE=fcitx5
  export XMODIFIERS=@im=fcitx5
  export QT_IM_MODULE=fcitx5
  fcitx5 &


针对wayland编辑~/.pam_environment
  INPUT_METHOD  DEFAULT=fcitx5
  GTK_IM_MODULE DEFAULT=fcitx5
  QT_IM_MODULE  DEFAULT=fcitx5
  XMODIFIERS DEFAULT=\@im=fcitx5

   * 安装皮肤
  `sudo pacman -S fcitx5-material-color`

  然后修改配置文件 ~/.config/fcitx5/conf/classicui.conf：

    # 垂直候选列表
    Vertical Candidate List=False

    # 按屏幕 DPI 使用
    PerScreenDPI=True

    # Font (设置成你喜欢的字体)
    Font="思源黑体 CN Medium 13"

    # 主题
    Theme=Material-Color-Pink

   * 修改为单行模式

新建/修改 ~/.config/fcitx5/conf/rime.conf

    # 可用时在应用程序中显示预编辑文本
    PreeditInApplication=True


* 安装中文字体
`sudo pacman -S ttf-roboto noto-fonts ttf-dejavu`
`sudo pacman -S wqy-bitmapfont wqy-microhei wqy-microhei-lite wqy-zenhei`
`sudo pacman -S noto-fonts-cjk adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts`

* 挂载vmware的共享目录
  
  安装好manjaro linux后，在mnt下不会出现共享目录hgfs。需要用vmhgfs-fuse命令手动挂载，为了便于操作，将该命令作为系统服务。新建文件vm-fuse-hgfs.service，内容如下，添加到`/etc/systemd/system`目录下。

      [Unit]
      Description=Load VMware shared folders
      Requires=vmware-vmblock-fuse.service
      After=vmware-vmblock-fuse.service
      ConditionVirtualization=vmware

      [Service]
      Type=oneshot
      RemainAfterExit=yes
      ExecStart=
      ExecStart=/usr/bin/vmhgfs-fuse -o allow_other -o auto_unmount /mnt/hgfs

      [Install]
      WantedBy=multi-user.target

    然后使用命令`systemctl enable vm-fuse-hgfs.service`设置开机启动，
    用`systemctl start vm-fuse-hgfs.service`启动服务，用`systemctl status vm-fuse-hgfs.service`查看状态。用命令`vmware-hgfsclient`可查看共享的目录。

* 解决win10与虚拟机无法复制粘贴文件问题
  可以复制粘贴文本和单个文件，文件夹及压缩包不行，需启动服务vmware-vmblock-fuse
  `systemctl enable vmware-vmblock-fuse`

  目前还存在粘贴时报“格式错误的URL..”，不影响使用。


Q: 使用yay -S 时报错：Cannot find the strip binary required for object file stripping

A: 缺少基础工具，安装之`sudo pacman -Sy base-devel` 