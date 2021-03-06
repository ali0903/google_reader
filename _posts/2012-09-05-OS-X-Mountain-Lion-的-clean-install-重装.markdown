---
layout: post
title:  "OS X Mountain Lion 的 clean install 重装"
date:   2012-09-05 15:38:51
author: Xin LI
categories: program
---

## OS X Mountain Lion 的 clean install 重装
### by Xin LI
### at 2012-09-05 15:38:51
### original <https://blog.delphij.net/2012/08/os-x-mountain-l.html>

<p>没什么特别，这里记一下大致过程，以及一些想到的值得 FreeBSD 学习，以及 OS X 应该改进的地方。</p>

<p>我选择的方法是借助 USB 安装盘来做重装的操作。在 App Store 下载 Mountain Lion 之后，不要选择继续安装（继续安装的话，在安装结束后会删掉安装的文件）。</p>

<p>在 Finder 中找到 /Applications/Install Mac OS X Mountain Lion.app，在其上按右键，Show Package Contents，这会将 Bundle 内容打开，在其中找到 Contents/SharedSupport，其中有一个叫 InstallESD.dmg 的文件。双击 InstallESD.dmg 挂载它。</p>

<p>接着，启动 Disk Utility，选择 U 盘（需要至少 5GB 的空间），将其中内容擦除；接着选择刚才挂载的 InstallESD，在 Restore 标签页中，将 U 盘拖拽到 Destination 的位置，然后按 Restore。</p>

<p>这个操作将会把 InstallESD.dmg 写入 U 盘。重启系统时按住 Option，选择刚刚制作的 U 盘，就进入了 OS X 的恢复界面。</p>

<p>在这里有四个选项，使用 Disk Utility （最后一个）清除硬盘或 SSD 内容（此处可以选择Mac OS Extended (Case sensitive, Journaled) ）。如果原先的硬盘使用了 File Vault，可能需要先输入任意用户的口令来解锁。完成后退出 Disk Utility，选择 Reinstall OS X Mountain Lion。</p>

<p>重装的过程大致需要20分钟左右。然后是一些基本的配置，完成后，重新进入 App Store 安装 Garage Band、iMovie 和 iPhoto。</p>
        <p>一些想法：</p>

<p>OS X 的 ESD 会自动地导入之前的无线网络和蓝牙鼠标的配置，这一点非常方便。</p>

<p>Disk Utility 似乎不允许直接擦除加密硬盘 (?)，当然，这样做是有理由的，不过似乎意义不太大，因为硬盘还是可以接到别的地方去擦除。</p>

<p>安装程序非常依赖高速的 Internet 连接或本地的 cache，总共的下载数据量大约为 7 GiB，如果需要安装 XCode，还需要更多的流量。</p>

<p>替换硬盘后， OS X 可以通过 Internet 恢复，换言之，系统的 UEFI firmware 包含了足够完成配置网络和下载安装所需数据的全部逻辑。</p>

<p>启用 FileVault 的过程，SSD 会比传统硬盘快得多。</p>