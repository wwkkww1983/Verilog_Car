# Verilog_Car

## 整体概述及功能说明
* 本程序是在 nexys2 开发板上外接 LED 点阵屏(16*16)和蜂鸣器,实现一个多模块的赛车游戏。
* 游戏中设有暂停键,加速键,显示控制键,重启按钮,确定按钮,左移右移按钮七个控制开关。
* LED 点阵显示屏上可以显示菜单栏,设置界面,以及游戏界面三个部分。初始进入游戏时为心形待机
界面,按下左右移动按钮切换到不同的菜单选项,共有开始游戏,难度设置,音量设置三个菜单选项。其
中,难度设定和音量设定均为三档可选,默认为中等难度和中音量大小。
* 选择开始游戏选项后进入游戏,游戏中玩家控制赛车左右移动躲避障碍物,同时可以选择加速键以获
得更高的分数,游戏中可以选择暂停键暂停游戏,取消暂停键后游戏继续,从游戏开始到玩家“撞车”游
戏失败的整个过程的分数都在七段数码管上记录。
* 游戏结束时点阵屏上滑出”OVER”的信息表明游戏结束,同时蜂鸣器响,数码管上得分停止。
* 关于地图。地图非随机数产生,而是事先在设计好内置在程序中,且到达设定路线的末尾后循环走预
设路线。

## 平台说明
* EDA 工具为 Xilinx ISE Design Suite 12.3 版本。
* 开发板型号为 nexys2 Spartan3E XC3S500E FG320

## 具体实现及程序框架
### 动态扫描显示模块
* 该模块负责实现 LED 点阵屏每一屏的显示,点阵屏只有一个数据通路,即每次时钟上升沿时只能传
输一个数据(0/1)到点阵屏上,一个数据传入后放入移位寄存器,下一个数据到来时数据依次往后传递。当
16 个位数据从 DATA 脚在 CLK 脉冲的作用下移入寄存器后,将 ST 电平拉高使寄存器中的数据存入锁存器,
因使能脚接 0 使能,数据直接输出 Q 端,Q 端数据经限流电阻接入点阵 COL 脚位,即一行的数据显示。
* 点阵屏有 ABCD 四位行选通信号,形成 4-16 译码器,对应到 16 行的选择,整个过程是先移出 16 个
数据信号,选通行信号显示一行,重复完成 16 行扫描。
### 去抖动模块
* 该程序有四个按钮(其中一个为复位键),故对于确定按钮和左右移动按钮要进行去抖动处理,并且
对应按钮按下后产生对应的脉冲信号以作用到状态机,实现控制游戏。
### 状态机模块
* 状态机模块为程序的控制中心,按下按钮并根据当前的状态决定下一状态的输出。该程序中共有
meu_state,voc_state,set_state,mov_state 四个状态变量,分别表示菜单状态,音量状态,难度状态,赛
车位置状态。op1(按下左移按钮),op2(按下右移按钮),op3(按下确定按钮),crash(游戏结束信号)控制状态的转移方向。

### 点阵显示模块
* 该模块根据状态机中 menu 状态选择一屏的数据(即 256 位)
,然后交给动态扫描模块输出这一屏。
### 蜂鸣器模块
* 蜂鸣器发声共有三个可能的情况,按下左右按钮发出声音 1,按下确定按钮发出声音 2,游戏结束时
响音乐(ps;本来想加入赛车运动时的引擎声,增强游戏效果,然而没找到模拟引擎声的音调及其频率)
### 数码管模块
* 该模块根据玩家在游戏中的表现进行评估得分,然后在数码管上四位进行编码输出。
### 显示开关模块
* 该模块根据按键的选择从而控制 LED 屏幕的亮与不亮。
## 使用说明
* 烧写到 nexys2 开发板之后,首先要保证 LED 屏幕是处于开启状态(即显示开关要拨上去表示打开屏
幕)这样才会显示出来,然后进入到的界面是一个心形界面(待机),然后在按钮区域按下左移右移,切换
菜单选项,当要进入这个选项时,按下确定按钮进入设置,然后按左右移动按钮选择你要设置的档次,选
好之后再次按下确定按钮保存你的选择并回到菜单栏。
* 要开始游戏时,选到开始游戏的选项并按下确定按钮进入,注意此时要保证游戏没有被暂停(即暂
停开关没有拨上去,暂停键没有生效),然后玩家按下左右移动按钮控制赛车绕过障碍物并前行。游戏中如
果前方障碍较少,玩家想要快速通过可以将加速键拨上去,这样对赛车开启加速效果,拨下去则恢复正常
行驶速度。撞上障碍物之后游戏结束,出现“OVER”界面,按下确定键回到菜单栏可以选择重新开始游戏。

