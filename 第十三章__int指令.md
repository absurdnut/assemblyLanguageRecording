# 第十三章__int指令  

## 13.1 int指令  

> int 指令
> 格式：
> int n  n为中断类型码
> 功能是引发中断过程  

> 执行过程
> 1. 取中断类型码n
> 2. 标志寄存器入栈，IF=0，TF=0
> 3. CS、IP入栈
> 4. (IP)=(n×4)，(CS)=(n×4+2)

## 13.2 编写供应用程序调用的中断例程  

> 问题 
> 编写安装中断7ch的中断例程

> 功能 
> 求word型数据的平方
> 参数
> ax(16位)
> 返回值
> ax+bx

> 应用举例 安装过程

```asm
assume cs:code

code segment
start:  mov ax,cs
		mov ds,ax
		mov si,offset sqr
		mov ax,0
		mov es,ax
		mov es,ax
		mov cx,offset sqrend-offset sqr
		cld
		rep movsb
		
		mov ax,0
		mov es,ax
		mov word ptr es:[7ch*4]，200h
		mov word ptr es:[7ch*4+],0
		
		mov ax,4c00h
		int 21h
		
	sqr:mul ax
	sqrend:nop

code ends
end start

```

> 注意，在中断例程sqr的最后，要使用iret指令
> 用汇编语法描述，iret的功能是
> pop IP
> pop CS
> popf

> 所以int和iret配合使用类似于之前的call和ret指令

## 13.3 对int、iret和栈的深入理解  

> 用7ch中断例程实现loop的功能  

## 13.4 BIOS和DOS所提供的中断例程  

> 	BIOS中主要包含以下几部分内容

1. 硬件系统的检测和初始化程序
2. 外部中断和内部中断的中断例程
3. 用于对硬件设备进行I/O操作的中断例程
4. 其他和硬件系统相关的中断例程

## 13.5 BIOS和DOS中断例程的安装过程  

> 而BIOS和DOS提供的中断例程是如何安装到内存中的呢

![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221012163953.png)

## 13.6 BIOS中断例程应用  

> int 10h中断例程是BIOS提供的中断例程
> 其中包含了多个和屏幕输出相关的子程序

![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221012165357.png)
![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221012165453.png)
![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221012165527.png)

## 13.7 DOS中断例程应用

![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221012165639.png)
![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221012165655.png)
![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221012165708.png)



