---
date: 2007-07-22 23:13
status: public
title: 'VeryPDF PDF2Word v2.0算法分析'
---

 X档案第三期的“从零开始学破解之（六）”用“VeryPDF PDF2Word v2.0”向大家演示了如何破解软件的次数限制，方法简单且实用，但我发现了更为简单的方法，经过我的观察，软件的安装目录下有个config.配置文件，用记事本打开后发现最后3行为：
```
[VeryPDF_PDF2Word]
Used=100
Regcode=
```
这里记录软件试用的次数，当软件试用100次后无法继续使用时，只需将这里的`Used=100`改为`Used=0`即可再用100次，更绝的是，如果你改为Used=-2就能无限的使用，次数不会减少！但这样做仍有不足，首先软件标题栏上会有“Trial version”字样，其次功能上也有限制，未注册版本每次只能转换一个文件并且只能转5页，这显然不能满足我们的要求，当然你也可以采取暴力手段破除它的功能限制，但作为一名破解爱好者，当然是希望能找出合法的注册码，像使用正版软件一样使用它。所以，兄弟们，操家伙......  
上期的光盘里自带了脱壳后的程序，我这里就不演示脱壳了。
用OD载入unpack.exe，在命令行插件里输入`bp MessageBoxA`，按F9运行程序，程序会自动停止，从状态栏看到是程序出现异常，根据提示按Shift+F9跳过异常继续运行，这时弹出了注册窗口，输入你的E-mail地址，在Registration Key框中随意输入假注册码87654321，点OK后程序被中断，按CTRL+F9执行到返回，这时弹出“注册码错误，请检查并重试！”的对话框。
点确定按钮，程序又一次中断，按F8返回到程序空间，在注释栏可以看到很多字符串，向上不远可以看到注册成功的提示字符串“Thank you registered VeryPDF PDF2Word v2.0.”。
按照程序的注册流程，必然在“成功提示”前面有个关键跳，跳向“错误提示”，我们在`00405D60   /74 44                            je short unpack.00405DA6`这行代码上点击一下鼠标左键，很清楚的看到它恰恰是跳到“错误提示”的前面。我们可以肯定这里就是关键跳了，如果要爆破的话只需将这里的je改为jne。
我们今天主要是研究算法，不能停留于此。大家都知道关键跳前面一般都是关键比较，也就是关键CALL，这里有两个CALL，哪个是关键比较呢？其实很简单，看代码：
```
00405D4B    FF15 DC555B00                    call dword ptr ds:[<&user32.GetDlg>; USER32.GetDlgItemTextA
00405D51    68 CC2C6700                      push unpack.00672CCC               ; ASCII "87654321"
```
从注释栏我们可以很清楚的看到前面一个CALL是获取我们输入注册码的函数。于是我们可以将目标锁定在后面一个CALL了
```
00405D56    E8 95F8FFFF                      call unpack.004055F0
```
在此行代码上下断，按CTRL+F2重新载入程序，F9运行，还是输入刚才的e-mail和假码87654321，点OK，程序中断在刚刚下的断点，按F7进入CALL，算法代码如下，请看注释：
```
004055F0    83EC 18                          sub esp,18
004055F3    83C9 FF                          or ecx,FFFFFFFF
004055F6    33C0                             xor eax,eax
004055F8    53                               push ebx
004055F9    56                               push esi
004055FA    8B7424 24                        mov esi,dword ptr ss:[esp+24]
004055FE    57                               push edi
004055FF    8BFE                             mov edi,esi
00405601    F2:AE                            repne scas byte ptr es:[edi]
00405603    F7D1                             not ecx
00405605    49                               dec ecx
00405606    83F9 14                          cmp ecx,14                         ; 判断输入的注册码是否为20位
00405609    74 07                            je short unpack.00405612           ; 是的话就继续，不是就玩儿完
0040560B    5F                               pop edi
0040560C    5E                               pop esi
0040560D    5B                               pop ebx
0040560E    83C4 18                          add esp,18
00405611    C3                               retn
00405612    8A06                             mov al,byte ptr ds:[esi]       ;取第1位放入al
00405614    8A4E 01                          mov cl,byte ptr ds:[esi+1]        ;取第2位放入cl
00405617    8D5424 0C                        lea edx,dword ptr ss:[esp+C]
0040561B    32DB                             xor bl,bl
0040561D    52                               push edx
0040561E    884424 1C                        mov byte ptr ss:[esp+1C],al
00405622    885C24 1D                        mov byte ptr ss:[esp+1D],bl
00405626    884C24 10                        mov byte ptr ss:[esp+10],cl
0040562A    885C24 11                        mov byte ptr ss:[esp+11],bl
0040562E    E8 4E811800                      call unpack.0058D781
00405633    8BF8                             mov edi,eax
00405635    8D4424 1C                        lea eax,dword ptr ss:[esp+1C]
00405639    50                               push eax
0040563A    E8 42811800                      call unpack.0058D781
0040563F    03F8                             add edi,eax
00405641    83C4 08                          add esp,8
00405644    83FF 0A                          cmp edi,0A                         ; 判断前两位之和是否为10
00405647    74 09                            je short unpack.00405652           ; 等于继续，不等玩儿完，请重新运行后使输入的注册码的前2位之和为10，如5，5
00405649    5F                               pop edi
0040564A    5E                               pop esi
0040564B    33C0                             xor eax,eax
0040564D    5B                               pop ebx
0040564E    83C4 18                          add esp,18
00405651    C3                               retn
00405652    8A4E 12                          mov cl,byte ptr ds:[esi+12]        ; 取第19位放入cl
00405655    8A56 13                          mov dl,byte ptr ds:[esi+13]        ; 取第20位放入dl
00405658    8D4424 0C                        lea eax,dword ptr ss:[esp+C]
0040565C    884C24 18                        mov byte ptr ss:[esp+18],cl
00405660    50                               push eax
00405661    885C24 1D                        mov byte ptr ss:[esp+1D],bl
00405665    885424 10                        mov byte ptr ss:[esp+10],dl
00405669    885C24 11                        mov byte ptr ss:[esp+11],bl
0040566D    E8 0F811800                      call unpack.0058D781
00405672    8D4C24 1C                        lea ecx,dword ptr ss:[esp+1C]
00405676    8BF8                             mov edi,eax
00405678    51                               push ecx
00405679    E8 03811800                      call unpack.0058D781
0040567E    03F8                             add edi,eax
00405680    83C4 08                          add esp,8
00405683    83FF 0E                          cmp edi,0E                         ; 判断最后2位之和是否为14
00405686    74 09                            je short unpack.00405691           ; 等于继续，不等玩儿完，请重新运行后使输入的注册码的最后2位之和为14，如77
00405688    5F                               pop edi
00405689    5E                               pop esi
0040568A    33C0                             xor eax,eax
0040568C    5B                               pop ebx
0040568D    83C4 18                          add esp,18
00405690    C3                               retn
00405691    8A56 05                          mov dl,byte ptr ds:[esi+5]         ; 取第6位放入dl
00405694    8A46 0D                          mov al,byte ptr ds:[esi+D]         ; 取第14位放入al
00405697    8D4C24 0C                        lea ecx,dword ptr ss:[esp+C]
0040569B    885424 18                        mov byte ptr ss:[esp+18],dl
0040569F    51                               push ecx
004056A0    885C24 1D                        mov byte ptr ss:[esp+1D],bl
004056A4    884424 10                        mov byte ptr ss:[esp+10],al
004056A8    885C24 11                        mov byte ptr ss:[esp+11],bl
004056AC    E8 D0801800                      call unpack.0058D781
004056B1    8D5424 1C                        lea edx,dword ptr ss:[esp+1C]
004056B5    8BF8                             mov edi,eax
004056B7    52                               push edx
004056B8    E8 C4801800                      call unpack.0058D781
004056BD    03F8                             add edi,eax
004056BF    83C4 08                          add esp,8
004056C2    83FF 09                          cmp edi,9                          ; 判断第6位和第14位之和是否为9
004056C5    74 09                            je short unpack.004056D0           ; 等于继续，不等玩儿完，请重新运行后使输入的注册码的第6位和第14位之和为9，如45
004056C7    5F                               pop edi
004056C8    5E                               pop esi
004056C9    33C0                             xor eax,eax
004056CB    5B                               pop ebx
004056CC    83C4 18                          add esp,18
004056CF    C3                               retn
004056D0    807E 0C 44                       cmp byte ptr ds:[esi+C],44         ; 判断第13位是否为“D”，16进制的44就是“D”
004056D4    74 09                            je short unpack.004056DF           ; 等于继续，不等玩儿完，请重新运行后使输入的注册码的第13位为“D”
004056D6    5F                               pop edi
004056D7    5E                               pop esi
004056D8    33C0                             xor eax,eax
004056DA    5B                               pop ebx
004056DB    83C4 18                          add esp,18
004056DE    C3                               retn
004056DF    8A4E 0E                          mov cl,byte ptr ds:[esi+E]         ; 将第15位放入cl
004056E2    33C0                             xor eax,eax                        ; 清空eax
004056E4    80F9 43                          cmp cl,43                          ; 判断第15位是否为“C”，16进制的43就是“C”
004056E7    5F                               pop edi
004056E8    5E                               pop esi
004056E9    5B                               pop ebx
004056EA    0F94C0                           sete al                            ; 等于的话将al置1，不等eax仍为零，返回后在关键跳前面test eax,eax看eax是否为零，为零跳向错误提示，不为零则成功，所以第15位必须为“C”
004056ED    83C4 18                          add esp,18
004056F0    C3                               retn                               ; 返回
```

至此，我们可以总结出：
1.注册码必须是20位
2.前两位之和为10
3.最后两位之和为14
4.第6位和第14位之和为9
5.第13位必须为“D”
6.第15位必须为“C”
7.其它位是无关项，程序不进行判断，可以置任意值 
由此，我得出注册码为：550004000000D5C00077（无关项都置0)。关闭OD，运行程序，注册码框内填入550004000000D5C00077，果然弹出注册成功的提示！

是不是很有成就感呢？在算法分析的过程中，需要按N次CTRL+F2，所以考验的是大家的耐心，想做黑客怎么能没有耐心呢？
此时已过三更，话不多说了！有什么疑问的请加我QQ：28874840，互相学习，共同进步！  
此文发表于一蓑烟雨论坛：[http://www.unpack.cn/thread-15022-1-1.html](http://www.unpack.cn/thread-15022-1-1.html)