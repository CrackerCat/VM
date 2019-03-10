首先寄存器都是 32 位
opcode 小于 256 个

code 每个字节都是 32 位

寄存器的代号  都是 32 位

从右到左 除了最后一位

指令结构 opcode + rs + rt + ri

设计原则。所有的计算在虚拟机的stack上进行，这是最后的原子指令的设计原则。

设计一个stack，用来计算，stack中不做任何的存储，仅用做存储临时变量。

# MIPS指令

## 寄存器

```
enum regs {

```

## 指令系统

### add & addu & addi & addiu

```
    AddReg4Imm2Reg = 0x10,
```
```
0x400d60:       addiu   $v1, $fp, 0x30
							AddReg4Imm2Reg,RFP,0x30,RBX,
```

### sub & subu

```
    SubReg4Imm2Reg = 0x26,
```
### mul & mulu

```
    MulReg4Imm2Reg = 0x30,
```

### and & andi

```
    AndReg4Imm2Reg = 0x70,
```
```
0x400d74:       andi    $v0, $v0, 0xff
							AndReg4Imm2Reg,RAX,0xff,RAX,
```

### or & ori

```
    OrReg4Imm2Reg  = 0x80,
```
 
### xor & xori

```
    XorReg4Imm2Reg = 0x66,
```
```
0x400d80:       xor     $v0, $v1, $v0
							XorReg4Reg2Reg,RBX,RAX,RAX,
```

### nor 

```
    NorReg4Imm2Reg = 0xa0,
```


### sll & sla & sllv

```
    SllReg4Imm2Reg = 0x50,
```
```
0x400d78:       sll     $v0, $v0, 0x18
							SllReg4Imm2Reg,RAX,0x18,RAX,
```

### srl & sra & srlv & srav

```
    SraReg4Imm2Reg = 0x46,
```
```
0x400d7c:       sra     $v0, $v0, 0x18
						SraReg4Imm2Reg,RAX,0x18,RAX,
```
###  lui

```

```

### lw & lb & lbu

```
    MovImm2Reg = 0x01,
```
```
0x400d5c:       lbu     $v0, 0x1a($fp)
						MovImm2Reg,0x1a,RFPO,
						MovMem2Reg,RFP,RAX,
```

### sw & sb

```
	MovImm2Reg = 0x01,
    MovReg2Mem = 0x04,
```
```
``` 

```e.g
0x400d48:       sb      $zero, 0x19($fp) 
						MovImm2Reg,0x19,0x00,0x00,0x00,RFPO,
						MovReg2Mem,ZERO,RFP,
```

### slt & sltu

```
```

### slti & sltiu

```
	 SetLessThanImm      = 0x86,
```
```
0x400da0:       sltiu   $v0, $v0, 6
							SetLessThanImm,RAX,0x06,RAX,
```

### beq

```
```

### bne

```
    BranchNotEquelZero  = 0x88,
```
```
0x400da4:       bnez    $v0, 0x400d5c
						BranchNotEquelZero,RAX,Gloable Offset,
```

### b

```
    Jmp = 0x90,
```

```e.g
0x400d54:       b       0x400d9c
						Jmp,gloable offset,
```

### nop

```
	Nop = 0x91,
```

### ret

```
    Ret = 0xFF,
```

---

```mips指令第一层转换 demo2
0x400d48:       sb      $zero, 0x19($fp) 
										MovImm2Reg,0x19,RFPO,
										MovReg2Mem,ZERO,RFP,
0x400d4c:       sb      $zero, 0x1a($fp)
										MovImm2Reg,0x1a,RFPO,
										MovReg2Mem,ZERO,RFP,
0x400d50:       sb      $zero, 0x1a($fp)
										MovImm2Reg,0x1a,RFPO,
										MovReg2Mem,ZERO,RFP,
0x400d54:       b       0x400d9c
										Jmp,gloable offset,
0x400d58:       nop
										Nop,
0x400d5c:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,0x1a,RFPO,
										MovMem2Reg,RFP,RAX,
0x400d60:       addiu   $v1, $fp, 0x30
										AddReg4Imm2Reg,RFP,0x30,RBX,
0x400d64:       addu    $v0, $v1, $v0
										AddReg4Reg2Reg,RBX,RAX,RAX,
0x400d68:       lb      $v1, -0xc($v0)
										MovImm2Reg,-0xc,RFPO,
										MovMem2Reg,RAX,RBX,
0x400d6c:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,0x1a,RFPO,
										MovMem2Reg,RFP,RAX,
0x400d70:       addiu   $v0, $v0, 0x31
										AddReg4Imm2Reg,RAX,0x31,RAX,
0x400d74:       andi    $v0, $v0, 0xff
										AndReg4Imm2Reg,RAX,0xff,RAX,
0x400d78:       sll     $v0, $v0, 0x18
										SllReg4Imm2Reg,RAX,0x18,RAX,
0x400d7c:       sra     $v0, $v0, 0x18
										SraReg4Imm2Reg,RAX,0x18,RAX,
0x400d80:       xor     $v0, $v1, $v0
										XorReg4Reg2Reg,RBX,RAX,RAX,
0x400d84:       sll     $v0, $v0, 0x18
										SllReg4Imm2Reg,RAX,0x18,RAX,
0x400d88:       sra     $v0, $v0, 0x18
										SraReg4Imm2Reg,RAX,0x18,RAX,
0x400d8c:       sb      $v0, 0x19($fp)
										MovImm2Reg,0x19,RFPO,
										MovReg2Mem,RAX,RFP,
0x400d90:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,0x1a,RFPO,
										MovMem2Reg,RFP,RAX,
0x400d94:       addiu   $v0, $v0, 1
										AddReg4Imm2Reg,RAX,0x01,RAX,
0x400d98:       sb      $v0, 0x1a($fp)
										MovImm2Reg,0x1a,RFPO,
										MovReg2Mem,RAX,RFP,
0x400d9c:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,0x1a,RFPO,
										MovMem2Reg,RFP,RAX,
0x400da0:       sltiu   $v0, $v0, 6
										SetLessThanImm,RAX,0x06,RAX,
0x400da4:       bnez    $v0, 0x400d5c
										BranchNotEquelZero,RAX,Gloable Offset,
0x400da8:       nop
										Nop,

```


``` mips指令第二层转换

MovImm2Reg
MovMem2Reg
MovReg2Reg
MovReg2Mem
MovImm2Mem
MovMem2Mem

AddReg4Imm2Reg
AddReg4Reg2Reg
AddReg4Mem2Reg
AddReg4Imm2Mem
AddReg4Reg2Mem
AddReg4Mem2Mem

SubReg4Imm2Reg
SubReg4Reg2Reg
SubReg4Mem2Reg
SubReg4Imm2Mem
SubReg4Reg2Mem
SubReg4Mem2Mem

MulReg4Imm2Reg
MulReg4Reg2Reg
MulReg4Mem2Reg
MulReg4Imm2Mem
MulReg4Reg2Mem
MulReg4Mem2Mem

SraReg4Imm2Reg
SraReg4Reg2Reg
SraReg4Mem2Reg
SraReg4Imm2Mem
SraReg4Reg2Mem
SraReg4Mem2Mem

SllReg4Imm2Reg
SllReg4Reg2Reg
SllReg4Mem2Reg
SllReg4Imm2Mem
SllReg4Reg2Mem
SllReg4Mem2Mem

XorReg4Imm2Reg
XorReg4Reg2Reg
XorReg4Mem2Reg
XorReg4Imm2Mem
XorReg4Reg2Mem
XorReg4Mem2Mem

AndReg4Imm2Reg
AndReg4Reg2Reg
AndReg4Mem2Reg
AndReg4Imm2Mem
AndReg4Reg2Mem
AndReg4Mem2Mem

NorReg4Imm2Reg

SetLessThanImm
SetLessThanReg
BranchNotEquelZero
BranchNotEquel
BranchIfEquelZero
BranchIfEquel

Jmp
Nop
Ret


```

说明：
在代码测试的过程中，发现同一条指令中不能同出现对mem区域的两次操作，因此取消了所有关于`Addreg4mem2mem`之类的指令，代码以及指令进行重新调整。根据mips指令集同时增加了Nor运算指令。

---

```
0x400d48:       sb      $zero, 0x19($fp)
0x400d4c:       sb      $zero, 0x1a($fp)
0x400d50:       lui     $v0, 0x40
0x400d54:       addiu   $v1, $v0, 0x11c0
0x400d58:       lwl     $v1, 3($v1)
0x400d5c:       lwr     $v1, 0x11c0($v0)
0x400d60:       sw      $v1, 0x1c($fp)
0x400d64:       addiu   $v0, $v0, 0x11c0
0x400d68:       lbu     $v1, 4($v0)
0x400d6c:       sb      $v1, 0x20($fp)
0x400d70:       lbu     $v0, 5($v0)
0x400d74:       sb      $v0, 0x21($fp)
0x400d78:       sb      $zero, 0x1a($fp)
0x400d7c:       b       0x400dc8
0x400d80:       nop
0x400d84:       lbu     $v0, 0x1a($fp)
0x400d88:       addiu   $v1, $fp, 0x38
0x400d8c:       addu    $v0, $v1, $v0
0x400d90:       lb      $v1, -0xc($v0)
0x400d94:       lbu     $v0, 0x1a($fp)
0x400d98:       addiu   $a0, $fp, 0x38
0x400d9c:       addu    $v0, $a0, $v0
0x400da0:       lbu     $v0, -0x1c($v0)
0x400da4:       sll     $v0, $v0, 0x18
0x400da8:       sra     $v0, $v0, 0x18
0x400dac:       xor     $v0, $v1, $v0
0x400db0:       sll     $v0, $v0, 0x18
0x400db4:       sra     $v0, $v0, 0x18
0x400db8:       sb      $v0, 0x19($fp)
0x400dbc:       lbu     $v0, 0x1a($fp)
0x400dc0:       addiu   $v0, $v0, 1
0x400dc4:       sb      $v0, 0x1a($fp)
0x400dc8:       lbu     $v0, 0x1a($fp)
0x400dcc:       sltiu   $v0, $v0, 6
0x400dd0:       bnez    $v0, 0x400d84
```

```vm_codeTest

    /*demo 1
   
   /*
    MovImm2Reg,0x19,RFPO,
    */
	
	
```

```test demo

    uint8_t v1 = 0;
    
    //$fp+0x30-0xc = $fp+0x24
    //result_v0 = $fp+0x19
    
0x400e34:       sb      $zero, 0x19($fp)
0x400e38:       sb      $zero, 0x1a($fp)
0x400e3c:       sb      $zero, 0x1a($fp)
0x400e40:       b       0x400e94
0x400e44:       nop
0x400e48:       lbu     $v0, 0x1a($fp)
0x400e4c:       addiu   $v1, $fp, 0x30
0x400e50:       addu    $v0, $v1, $v0
0x400e54:       lb      $v1, -0xc($v0)
0x400e58:       lbu     $v0, 0x1a($fp)
0x400e5c:       addiu   $v0, $v0, 0x31
0x400e60:       andi    $v0, $v0, 0xff
0x400e64:       sll     $v0, $v0, 0x18
0x400e68:       sra     $v0, $v0, 0x18
0x400e6c:       xor     $v0, $v1, $v0
0x400e70:       sll     $v0, $v0, 0x18
0x400e74:       sra     $v0, $v0, 0x18

0x400e78:       andi    $v1, $v0, 0xff
0x400e7c:       lbu     $v0, 0x19($fp)
0x400e80:       addu    $v0, $v1, $v0

0x400e84:       sb      $v0, 0x19($fp)
0x400e88:       lbu     $v0, 0x1a($fp)
0x400e8c:       addiu   $v0, $v0, 1
0x400e90:       sb      $v0, 0x1a($fp)
0x400e94:       lbu     $v0, 0x1a($fp)
0x400e98:       sltiu   $v0, $v0, 6
0x400e9c:       bnez    $v0, 0x400e48
0x400ea0:       nop

    MovImm2Reg,0x19,RFPO,
    
```

### 第二层到第三层原子指令转换

```
    PushImm = 0xb0,
    
    AtomAdd = 0xc0,
```
```
    MovImm2Reg,0x19,RFPO,
    	PushImm,0x19,
    	PopReg,RFPO,
    	PushReg,ZERO,
    	PopMem,RFP,
    	PushImm,0x1a,
    	PopReg,RFPO,
    	PushReg,ZERO,
    	PopMem,RFP,
    	PushImm,0x1a,
    	PopReg,RFPO,
    	PushReg,ZERO,
    	PopMem,RFP,
    	PushImm,0x1a,
    	PopReg,RFPO,
    	PushMem,RFP,
    	PopReg,RAX,
		PushImm,0xc0,
		AtomAdd,
		PopReg,RBX,
    	PushReg,RAX,
    	PushImm,0x04,
    	AtomMul,
    	PopReg,RAX,
    	PushReg,RBX,
    	PushReg,RAX,
    	AtomAdd,
    	PopReg,RAX,
    	PushImm,-0x1c,
    	PopReg,RFPO,
    	PushMem,RAX,
    	PopReg,RBX,
    	PushImm,0x1a,
    	PopReg,RFPO,
    	PushMem,RFP,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0x31,
    	AtomAdd,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0xff,
    	AtomAnd,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0x18,
    	AtomSll,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0x18,
    	AtomSra,
    	PopReg,RAX,
    	PushReg,RBX,
    	PushReg,RAX,
    	AtomXor,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0x18,
    	AtomSll,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0x18,
    	AtomSra,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0xff,
    	AtomAnd,
    	PopReg,RBX,
    	PushImm,0x19,
    	PopReg,RFPO,
    	PushMem,RFPO,
    	PopReg,RAX
    	PushReg,RBX,
    	PushReg,RAX,
    	AtomAdd,
    	PopReg,RAX,
    	PushImm,0x19,
    	PopReg,RFPO,
    	PushReg,RAX,
    	PopMem,RFP,
    	PushImm,0x1a,
    	PopReg,RFPO,
    	PushMem,RFP,
    	PopReg,RAX,
    	PushReg,RAX,
    	PushImm,0x01,
    	AtomAdd,
    	PopReg,RAX,
    	PushImm,0x1a,
    	PopReg,RFPO,
    	PushReg,RAX,
    	PopMem,RFP,
    	PushImm,0x1a,
    	PopReg,RFPO,
    	PushMem,RFP,
    	PopReg,RAX,
```

---

```mips指令第一层转换 demo1 错误的 未修正的
0x400d48:       sb      $zero, 0x19($fp) 
										MovImm2Reg,0x19,0x00,0x00,0x00,RFPO,
										MovReg2Mem,ZERO,RFP,
0x400d4c:       sb      $zero, 0x1a($fp)
										MovImm2Reg,0x1a,0x00,0x00,0x00,RFPO,
										MovReg2Mem,ZERO,RFP,
0x400d50:       sb      $zero, 0x1a($fp)
										MovImm2Reg,0x1a,0x00,0x00,0x00,RFPO,
										MovReg2Mem,ZERO,RFP,
0x400d54:       b       0x400d9c
										Jmp,gloable offset,
0x400d58:       nop
										Nop,
0x400d5c:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,0x1a,0x00,0x00,0x00,RFPO,
										MovMem2Reg,RFP,RAX,
0x400d60:       addiu   $v1, $fp, 0x30
										AddReg4Imm2Reg,RFP,0x30,0x00,0x00,0x00,RBX,
0x400d64:       addu    $v0, $v1, $v0
										AddReg4Reg2Reg,RBX,RAX,RAX,
0x400d68:       lb      $v1, -0xc($v0)
										MovImm2Reg,-0xc,0x00,0x00,0x00,RFPO,
										MovMem2Reg,RAX,RBX,
0x400d6c:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,-0xc,0x00,0x00,0x00,RFPO,
										MovMem2Reg,RFP,RAX,
0x400d70:       addiu   $v0, $v0, 0x31
										AddReg4Imm2Reg,RAX,0x31,0x00,0x00,0x00,RAX,
0x400d74:       andi    $v0, $v0, 0xff
										AndReg4Imm2Reg,RAX,0xff,0x00,0x00,0x00,RAX,
0x400d78:       sll     $v0, $v0, 0x18
										SllReg4Imm2Reg,RAX,0x18,0x00,0x00,0x00,RAX,
0x400d7c:       sra     $v0, $v0, 0x18
										SraReg4Imm2Reg,RAX,0x18,0x00,0x00,0x00,RAX,
0x400d80:       xor     $v0, $v1, $v0
										XorReg4Reg2Reg,RBX,RAX,RAX,
0x400d84:       sll     $v0, $v0, 0x18
										SllReg4Imm2Reg,RAX,0x18,0x00,0x00,0x00,RAX,
0x400d88:       sra     $v0, $v0, 0x18
										SraReg4Imm2Reg,RAX,0x18,0x00,0x00,0x00,RAX,
0x400d8c:       sb      $v0, 0x19($fp)
										MovImm2Reg,0x19,0x00,0x00,0x00,RFPO,
										MovReg2Mem,RAX,RFP,
0x400d90:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,0x1a,0x00,0x00,0x00,RFPO,
										MovMem2Reg,RFP,RAX,
0x400d94:       addiu   $v0, $v0, 1
										AddReg4Imm2Reg,RAX,0x01,0x00,0x00,0x00,RAX,
0x400d98:       sb      $v0, 0x1a($fp)
										MovImm2Reg,0x1a,0x00,0x00,0x00,RFPO,
										MovReg2Mem,RAX,RFP,
0x400d9c:       lbu     $v0, 0x1a($fp)
										MovImm2Reg,0x1a,0x00,0x00,0x00,RFPO,
										MovMem2Reg,RFP,RAX,
0x400da0:       sltiu   $v0, $v0, 6
										SetLessThanImm,RAX,0x06,0x00,0x00,0x00,RAX,
0x400da4:       bnez    $v0, 0x400d5c
										BranchNotEquelZero,RAX,Gloable Offset,
0x400da8:       nop
										Nop,

```