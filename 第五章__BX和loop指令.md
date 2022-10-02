# 第五章  [BX]和loop指令  

## 1. [BX]和内存单元的描述  

> 内存单元使用[数字]来表示偏移地址，[bx]使用寄存器来表示偏移地址
> 例如

	mov ax,[0]
	mov ax,[bx]

> 完整的描述一个内存单元，需要两点
>  1. 内存单元的地址  
>  2. 内存单元的长度  

>  **地址**由偏移地址和段地址指出
>  内存单元的长度由具体指令的操作对象指出(比如说寄存器)
>  例如  
>   1. mov ax,[0]  存入2个字节长度
>   2. mov al ,[0]  存入1个字节长度

## 2. loop  

## 5.1 [BX]  

## 5.2 Loop指令  


1. loop命令的执行原理  
![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221002150042.png)

> 通常使用loop实现循环功能，使用cx存储循环次数  

> 程序如下  

```assembly
assume cs:code  
code segment  
	mov ax,2

	mov cx,11
s:  add ax,ax
	loop s
	mov ax,4c00h
	int 21h
code ends
end

```

> 分析  
> ![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221002152945.png)

### 注意  

> 编译器认为mov al,[0] 中的[idata]为idata 
> 所以想要实现mov al,[0]有两种方法  
>  1. 可以在[]前显式地给出段地址所在的段寄存器  
> 		mov al , ds:[0]  
>  2. 还可以用寄存器间接地给出偏移地址
> 		mov bx , 0000H
> 		mov al ,  [bx]

## 5.5 loop和[bx]的联合应用  

> 如下 

assume cs:code
code segment

		mov ax,0ffffh
		mov ds,ax
		mov bx,0
		
		mov dx,0
		
		mov cx,12
		
	s:mov al,[bx]
		mov ah,0
		add dx,ax
		inc bx
		loop s
		
		mov ax,4c00h
		int 21h

code ends
end

## 段前缀  

> 出现在访问内存单元的指令中，用于显式的指明内存单元的段地址
> 称为段前缀  

1.方法一

assume cs:code  
code segment  

		mov bx,0       ;(bx)=0，偏移地址从0开始
		mov cx,12      ；(cx)=12，循环12次
		
	s:mov ax,0ffffh  
		mov ds,ax      ;(ds)=0ffffh
		mov dl,[bx]    ;(dl)=((ds)*16+(bx)),将ffff:bx中的数据送入dl
		
		mov ax,0020h
		mov ds,ax      ;(ds)=0020h 
		mov [bx],dl    ;((ds)*16+(bx))=(dl),将dl的数据送入0020h:bx
		
		inc bx         ;(bx)=(bx)+1
		loop s
		mov ax,4c00h
		int 21h

code ends
end

2. 方法二  

assume cs:code
code segment

		mov ax,0ffffh
		mov ds,ax       ;(ds)=0ffffh
	
		mov ax,0020h
		mov es,ax       ;(es)=0020h
	
		mov bx,0        ;(bx)=0,此时ds:bx指向ffff:0,es:bx指向0020:0
	
		mov cx,12
		
	s:mov dl,[bx]
		mov es:[bx],dl
		inc bx
		loop s
		
		mov ax,4c00h
		int 21h

code ends
end











