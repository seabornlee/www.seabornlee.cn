Date: 2013-08-02 10:10:07
Title: VIM练习-录制和播放宏（macro）
Url: /vim-practice-record-macro-and-play
Tags: Vim
Toc: no

![Vim Logo](/_image/2014-05-11/14-54-46.jpg)

### 关于宏
VIM用户可以使用宏来录制编辑动作过程，然后再播放以减少重复操作。
具体命令如下：

    qa：开始录制宏，并将宏保存到寄存器a中（你也可以把“a”想象成宏的名称），开始后会在状态栏显示“recording”
    q：停止录制
    @a：播放寄存器a中的宏
    
### 练习一
#### 编辑前
```
# Appending text:
The name "Vim" is an acronym for "Vi IMproved"
The name "Vim" is an acronym for

# Editing text:
Vim is a text editor originally released by Bram Moolenaar in 1991 for the Amiga
Trivia: Vim is a text editor released by Bram Moolenaar in 1991 for the Amiga

# Deleting text:
Vim has a vi compatibility mode
Vim has a vi compatibility mode but when not in this mode Vim has many enhancements over vi
```
#### 编辑后

将文件中的三对文本中的第二行都变得跟第一行一样。结果如下：
```
# Appending text:
The name "Vim" is an acronym for "Vi IMproved"
The name "Vim" is an acronym for "Vi IMproved"

# Editing text:
Vim is a text editor originally released by Bram Moolenaar in 1991 for the Amiga
Vim is a text editor originally released by Bram Moolenaar in 1991 for the Amiga

# Deleting text:
Vim has a vi compatibility mode
Vim has a vi compatibility mode
```
#### 操作解释
>**qa 开始录制宏**
j 下移一行
yy 复制一行
p 粘贴
j 下移一行
dd 删除一行
**q 停止录制**
**2@a 播放两次**

如果你这个比较简单，也没有什么实用价值，那就看下面这个吧。

---
### 练习二
#### 编辑前
```
Annette
Warren
Anthony
Preston
Kelly
Taylor
Stiller
Dennis
Schwartz
```
#### 编辑后
```
ALTER USER Annette IDENTIFIED BY 'Annette';
ALTER USER Warren IDENTIFIED BY 'Warren';
ALTER USER Anthony IDENTIFIED BY 'Anthony ';
ALTER USER Preston IDENTIFIED BY 'Preston';
ALTER USER Kelly IDENTIFIED BY 'Kelly ';
ALTER USER Taylor IDENTIFIED BY 'Taylor';
ALTER USER Stiller IDENTIFIED BY 'Stiller';
ALTER USER Dennis IDENTIFIED BY 'Dennis';
ALTER USER Schwart IDENTIFIED BY 'Schwart';
```
#### 操作解释
>q a: 开始录制宏，存储到寄存器a中
I “ALTER USER ”: 在行首插入“ALERT USER ”
Esc w yw：复制光标后的用户名
A “IDENTIFIED BY '”：到行尾输入IDENTIFIED BY ‘
Esc p：粘贴之前复制的用户名
A “';”：在行尾关闭引号
Esc j q：下移一行并结束录制
8@a：播放宏8次

---
参考资料：[Vi and Vim Macro Tutorial: How To Record and Play](http://www.thegeekstuff.com/2009/01/vi-and-vim-macro-tutorial-how-to-record-and-play/)