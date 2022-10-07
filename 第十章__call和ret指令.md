# 第十章__call和ret指令  

## 10.1 ret和retf  

> ret指令用栈中的数据，修改IP中的内容，从而实现近转移
> retf指令使用栈中的数据，修改CS和IP的内容，从而实现远转移

CPU执行ret指令时，进行下面两步操作：

![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221006203738.png)

CPU执行retf指令时，进行下面四步操作：

![](https://gitee.com/absurdnut/tuchuang/raw/master/img/20221006203711.png)

> ret 指令示例代码  

	assume cs:code

	stack segment
		db 16 dup (0)
	stack ends

	code segment
		mov ax,4c00h
		int 21h
	start:  mov ax,stack
					mov ss,ax     ;将栈段地址置于ss
					mov sp,16     ;栈顶地址置于sp
					mov ax,0      
					push ax       ;将ip将要继承的地址置于栈中
					mov bx,0
					ret
	code ends
	end start

> retf 指令示例代码

	assume cs:code

	stack segment
		db 16 dup (0)
	stack ends

	code segment 
				mov ax,4c00h
				int 21h
	start:mov ax,stack
				mov ss,ax
				mov sp,16
				mov ax,0
				push cs       ;将代码段地址置于栈中
				push ax       ;将ip地址置于栈中
				mov bx,0
				retf
	code ends

	end start

> CS和IP配合使用，数据更新为X000：0000H后，重新指向代码段的首行


## 10.2 call指令  

> call指令的内容
> 1. 将当前的IP或CS和IP压入栈中
> 2. 转移

## 10.3 根据位移进行转移的call指令  

> 格式：call 标号 
> 内容：将当前的IP压栈后，转到标号处执行指令(16位位移)

## 10.4 转移的目的地址在指令中的call指令  

> 格式：call far ptr 标号  
> 内容：push CS
>            push IP
>            jmp far ptr  标号

## 10.5 转移地址在寄存器中的call指令  

> 格式：call 16位 reg  
> 内容：push IP
> 		   jmp 16位 reg

> 将IP内容入栈后，跳转至16位寄存器内容所指位置

## 10.6 转移地址在内存中的call指令

1. 
> 格式：call word ptr 内存单元地址
> 内容：push IP
> 		   jmp word ptr 内存单元地址

2. 
> 格式：call dword ptr 内存单元地址
> 内容：push CS
> 		   push IP
> 		   jmp dword ptr 内存单元地址

## 10.7 call和ret的配合使用  

> 示例程序

	assume cs:code
	stack segment
		start: mov ax,1
					  mov cx,3
					  call s
					  mov bx,ax
					  mov ax,4c00h
					  int 21h
				s: add ax,ax
					  loop s
					  ret
		code ends
		end start

> 使用call和ret的配合，可以实现子程序的机制
> 框架如下  

> 标号：
> 	指令
> 	ret

```asm
assume cs:code
code segment
	main::
		 :
		 call sub1   ;调用子程序subl
		 :
		 :
		 mov ax,4c00h
		 int 21h
		 
	sub1::            ;sub1开始
		 :
		 call sub2    ;调用子程序sub2
		 :
		 :
		 ret          ;子程序返回
		 
	sub2::            ;子程序sub2开始
		 :
		 :
		 ret          ;子程序sub2开始
		 :
		 :
		 ret          ;子程序返回
code ends
end main
```

## 10.8 mul指令  

> mul是乘法指令
> 1. 两个相乘的数要保持位数一致，都是8位或16位
> 2. 第一个数16位时默认存放在ax中，8位时默认存放在al中
> 3. 8位乘法结果默认放在ax中，16位乘法结果默认高位在**dx**中存放低位在**ax**中存放

> 格式：

	mul reg
	mul 内存单元

> 实例：

	mov al 100
	mov bl 10
	mul bl

## 10.11 批量数据的传递  

> 当出现多参数需要传递时，寄存器的数量便不足以传递参数，这时候需要使用内存空间保存数据  

> 下面以一个批量更改大小写的实例来说明  

```asm
assume cs:code 
data segment 
	db 'conversation'
data ends

code segment
	mov ax,data
	mov ds,ax
	mov si,0
	mov cx,12
	call capital
	mov ax,4c00h
	int 21h

capital:    and byte ptr [si],11011111b
			inc si
			loop capital
			ret
code ends
end start
```







