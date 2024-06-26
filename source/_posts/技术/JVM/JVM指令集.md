---
title: JVM指令集
date: 2018/02/01 21:05:14
updated: 2018/02/01 21:05:14
categories:
- [技术, JVM]
tags:
- JVM
- Java
---

## JVM指令助记符

1. 变量到操作数栈：

   ```
   iload,iload_<n>,lload,lload_<n>,fload,fload_<n>,dload,dload_<n>,aload,aload_<n>
   ```

2. 操作数栈到变量：

   ```
   istore,istore_<n>,lstore,lstore_<n>,fstore,fstore_<n>,dstore,dstor_<n>,astore,astore_<n>
   ```

3. 常数到操作数栈：

   ```
   bipush,sipush,ldc,ldc_w,ldc2_w,aconst_null,iconst_ml,iconst_<i>,lconst_<l>,fconst_<f>,dconst_<d>
   ```

4. 加：iadd,ladd,fadd,dadd

5. 减：isub,lsub,fsub,dsub

6. 乘：imul,lmul,fmul,dmul

7. 除：idiv,ldiv,fdiv,ddiv

8. 余数：irem,lrem,frem,drem

9. 取负：ineg,lneg,fneg,dneg

10. 移位：ishl,lshr,iushr,lshl,lshr,lushr

11. 按位或：ior,lor

12. 按位与：iand,land

13. 按位异或：ixor,lxor

14. 类型转换：i2l,i2f,i2d,l2f,l2d,f2d(放宽数值转换)     
            i2b,i2c,i2s,l2i,f2i,f2l,d2i,d2l,d2f(缩窄数值转换)

15. 创建类实便：new

16. 创建新数组：newarray,anewarray,multianwarray

17. 访问类的域和类实例域：getfield,putfield,getstatic,putstatic

18. 把数据装载到操作数栈：baload,caload,saload,iaload,laload,faload,daload,aaload

19. 从操作数栈存存储到数组：bastore,castore,sastore,iastore,lastore,fastore,dastore,aastore

20. 获取数组长度：arraylength

21. 检相类实例或数组属性：instanceof,checkcast

22. 操作数栈管理：pop,pop2,dup,dup2,dup_xl,dup2_xl,dup_x2,dup2_x2,swap

23. 有条件转移：ifeq,iflt,ifle,ifne,ifgt,ifge,ifnull,ifnonnull,if_icmpeq,if_icmpene, 
    if_icmplt,if_icmpgt,if_icmple,if_icmpge,if_acmpeq,if_acmpne,
    lcmp,fcmpl fcmpg,dcmpl,dcmpg

24. 复合条件转移：tableswitch,lookupswitch

25. 无条件转移：goto,goto_w,jsr,jsr_w,ret

26. 调度对象的实便方法：invokevirtual

27. 调用由接口实现的方法：invokeinterface

28. 调用需要特殊处理的实例方法：invokespecial

29. 调用命名类中的静态方法：invokestatic

30. 方法返回：ireturn,lreturn,freturn,dreturn,areturn,return

31. 异常：athrow

32. finally关键字的实现使用：jsr,jsr_w,ret



## JVM字节码指令集

| 十进制 | 十六进制 | 指令集          | 含义 |
| ------ | -------- | --------------- | ---- |
| 0      | 0x00     | nop             |      |
| 1      | 0x01     | aconst_null     |      |
| 2      | 0x02     | iconst_m1       |      |
| 3      | 0x03     | iconst_0        |      |
| 4      | 0x04     | iconst_1        |      |
| 5      | 0x05     | iconst_2        |      |
| 6      | 0x06     | iconst_3        |      |
| 7      | 0x07     | iconst_4        |      |
| 8      | 0x08     | iconst_5        |      |
| 9      | 0x09     | lconst_0        |      |
| 10     | 0x0a     | lconst_1        |      |
| 11     | 0x0b     | fconst_0        |      |
| 12     | 0x0c     | fconst_1        |      |
| 13     | 0x0d     | fconst_2        |      |
| 14     | 0x0e     | dconst_0        |      |
| 15     | 0x0f     | dconst_1        |      |
| 16     | 0x10     | bipush          |      |
| 17     | 0x11     | sipush          |      |
| 18     | 0x12     | ldc             |      |
| 19     | 0x13     | ldc_w           |      |
| 20     | 0x14     | ldc2_w          |      |
| 21     | 0x15     | iload           |      |
| 22     | 0x16     | lload           |      |
| 23     | 0x17     | fload           |      |
| 24     | 0x18     | dload           |      |
| 25     | 0x19     | aload           |      |
| 26     | 0x1a     | iload_0         |      |
| 27     | 0x1b     | iload_1         |      |
| 28     | 0x1c     | iload_2         |      |
| 29     | 0x1d     | iload_3         |      |
| 30     | 0x1e     | lload_0         |      |
| 31     | 0x1f     | lload_1         |      |
| 32     | 0x20     | lload_2         |      |
| 33     | 0x21     | lload_3         |      |
| 34     | 0x22     | fload_0         |      |
| 35     | 0x23     | fload_1         |      |
| 36     | 0x24     | fload_2         |      |
| 37     | 0x25     | fload_3         |      |
| 38     | 0x26     | dload_0         |      |
| 39     | 0x27     | dload_1         |      |
| 40     | 0x28     | dload_2         |      |
| 41     | 0x29     | dload_3         |      |
| 42     | 0x2a     | aload_0         |      |
| 43     | 0x2b     | aload_1         |      |
| 44     | 0x2c     | aload_2         |      |
| 45     | 0x2d     | aload_3         |      |
| 46     | 0x2e     | iaload          |      |
| 47     | 0x2f     | laload          |      |
| 48     | 0x30     | faload          |      |
| 49     | 0x31     | daload          |      |
| 50     | 0x32     | aaload          |      |
| 51     | 0x33     | baload          |      |
| 52     | 0x34     | caload          |      |
| 53     | 0x35     | saload          |      |
| 54     | 0x36     | istore          |      |
| 55     | 0x37     | lstore          |      |
| 56     | 0x38     | fstore          |      |
| 57     | 0x39     | dstore          |      |
| 58     | 0x3a     | astore          |      |
| 59     | 0x3b     | istore_0        |      |
| 60     | 0x3c     | istore_1        |      |
| 61     | 0x3d     | istore_2        |      |
| 62     | 0x3e     | istore_3        |      |
| 63     | 0x3f     | lstore_0        |      |
| 64     | 0x40     | lstore_1        |      |
| 65     | 0x41     | lstore_2        |      |
| 66     | 0x42     | lstore_3        |      |
| 67     | 0x43     | fstore_0        |      |
| 68     | 0x44     | fstore_1        |      |
| 69     | 0x45     | fstore_2        |      |
| 70     | 0x46     | fstore_3        |      |
| 71     | 0x47     | dstore_0        |      |
| 72     | 0x48     | dstore_1        |      |
| 73     | 0x49     | dstore_2        |      |
| 74     | 0x4a     | dstore_3        |      |
| 75     | 0x4b     | astore_0        |      |
| 76     | 0x4c     | astore_1        |      |
| 77     | 0x4d     | astore_2        |      |
| 78     | 0x4e     | astore_3        |      |
| 79     | 0x4f     | iastore         |      |
| 80     | 0x50     | lastore         |      |
| 81     | 0x51     | fastore         |      |
| 82     | 0x52     | dastore         |      |
| 83     | 0x53     | aastore         |      |
| 84     | 0x54     | bastore         |      |
| 85     | 0x55     | castore         |      |
| 86     | 0x56     | sastore         |      |
| 87     | 0x57     | pop             |      |
| 88     | 0x58     | pop2            |      |
| 89     | 0x59     | dup             |      |
| 90     | 0x5a     | dup_x1          |      |
| 91     | 0x5b     | dup_x2          |      |
| 92     | 0x5c     | dup2            |      |
| 93     | 0x5d     | dup2_x1         |      |
| 94     | 0x5e     | dup2_x2         |      |
| 95     | 0x5f     | swap            |      |
| 96     | 0x60     | iadd            |      |
| 97     | 0x61     | ladd            |      |
| 98     | 0x62     | fadd            |      |
| 99     | 0x63     | dadd            |      |
| 100    | 0x64     | isub            |      |
| 101    | 0x65     | lsub            |      |
| 102    | 0x66     | fsub            |      |
| 103    | 0x67     | dsub            |      |
| 104    | 0x68     | imul            |      |
| 105    | 0x69     | lmul            |      |
| 106    | 0x6a     | fmul            |      |
| 107    | 0x6b     | dmul            |      |
| 108    | 0x6c     | idiv            |      |
| 109    | 0x6d     | ldiv            |      |
| 110    | 0x6e     | fdiv            |      |
| 111    | 0x6f     | ddiv            |      |
| 112    | 0x70     | irem            |      |
| 113    | 0x71     | lrem            |      |
| 114    | 0x72     | frem            |      |
| 115    | 0x73     | drem            |      |
| 116    | 0x74     | ineg            |      |
| 117    | 0x75     | lneg            |      |
| 118    | 0x76     | fneg            |      |
| 119    | 0x77     | dneg            |      |
| 120    | 0x78     | ishl            |      |
| 121    | 0x79     | lshl            |      |
| 122    | 0x7a     | ishr            |      |
| 123    | 0x7b     | lshr            |      |
| 124    | 0x7c     | iushr           |      |
| 125    | 0x7d     | lushr           |      |
| 126    | 0x7e     | iand            |      |
| 127    | 0x7f     | land            |      |
| 128    | 0x80     | ior             |      |
| 129    | 0x81     | lor             |      |
| 130    | 0x82     | ixor            |      |
| 131    | 0x83     | lxor            |      |
| 132    | 0x84     | iinc            |      |
| 133    | 0x85     | i2l             |      |
| 134    | 0x86     | i2f             |      |
| 135    | 0x87     | i2d             |      |
| 136    | 0x88     | l2i             |      |
| 137    | 0x89     | l2f             |      |
| 138    | 0x8a     | l2d             |      |
| 139    | 0x8b     | f2i             |      |
| 140    | 0x8c     | f2l             |      |
| 141    | 0x8d     | f2d             |      |
| 142    | 0x8e     | d2i             |      |
| 143    | 0x8f     | d2l             |      |
| 144    | 0x90     | d2f             |      |
| 145    | 0x91     | i2b             |      |
| 146    | 0x92     | i2c             |      |
| 147    | 0x93     | i2s             |      |
| 148    | 0x94     | lcmp            |      |
| 149    | 0x95     | fcmpl           |      |
| 150    | 0x96     | fcmpg           |      |
| 151    | 0x97     | dcmpl           |      |
| 152    | 0x98     | dcmpg           |      |
| 153    | 0x99     | ifeq            |      |
| 154    | 0x9a     | ifne            |      |
| 155    | 0x9b     | iflt            |      |
| 156    | 0x9c     | ifge            |      |
| 157    | 0x9d     | ifgt            |      |
| 158    | 0x9e     | ifle            |      |
| 159    | 0x9f     | if_icmpeq       |      |
| 160    | 0xa0     | if_icmpne       |      |
| 161    | 0xa1     | if_icmplt       |      |
| 162    | 0xa2     | if_icmpge       |      |
| 163    | 0xa3     | if_icmpgt       |      |
| 164    | 0xa4     | if_icmple       |      |
| 165    | 0xa5     | if_acmpeq       |      |
| 166    | 0xa6     | if_acmpne       |      |
| 167    | 0xa7     | goto            |      |
| 168    | 0xa8     | jsr             |      |
| 169    | 0xa9     | ret             |      |
| 170    | 0xaa     | tableswitch     |      |
| 171    | 0xab     | lookupswitch    |      |
| 172    | 0xac     | ireturn         |      |
| 173    | 0xad     | lreturn         |      |
| 174    | 0xae     | freturn         |      |
| 175    | 0xaf     | dreturn         |      |
| 176    | 0xb0     | areturn         |      |
| 177    | 0xb1     | return          |      |
| 178    | 0xb2     | getstatic       |      |
| 179    | 0xb3     | putstatic       |      |
| 180    | 0xb4     | getfield        |      |
| 181    | 0xb5     | putfield        |      |
| 182    | 0xb6     | invokevirtual   |      |
| 183    | 0xb7     | invokespecial   |      |
| 184    | 0xb8     | invokestatic    |      |
| 185    | 0xb9     | invokeinte***ce |      |
| 187    | 0xbb     | new             |      |
| 188    | 0xbc     | newarray        |      |
| 189    | 0xbd     | anewarray       |      |
| 190    | 0xbe     | arraylength     |      |
| 191    | 0xbf     | athrow          |      |
| 192    | 0xc0     | checkcast       |      |
| 193    | 0xc1     | instanceof      |      |
| 194    | 0xc2     | monitorenter    |      |
| 195    | 0xc3     | monitorexit     |      |
| 196    | 0xc4     | wide            |      |
| 197    | 0xc5     | multianewarray  |      |
| 198    | 0xc6     | ifnull          |      |
| 199    | 0xc7     | ifnonnull       |      |
| 200    | 0xc8     | goto_w          |      |
| 201    | 0xc9     | jsr_w           |      |

## JVM快速操作码	

| 203  | 0xcb | ldc_quick                 |      |
| ---- | ---- | ------------------------- | ---- |
| 204  | 0xcc | ldc_w_quick               |      |
| 205  | 0xcd | ldc2_w_quick              |      |
| 206  | 0xce | getfield_quick            |      |
| 207  | 0xcf | putfield_quick            |      |
| 208  | 0xd0 | getfield2_quick           |      |
| 209  | 0xd1 | putfield2_quick           |      |
| 210  | 0xd2 | getstatic_quick           |      |
| 211  | 0xd3 | putstatic_quick           |      |
| 212  | 0xd4 | getstatic2_quick          |      |
| 213  | 0xd5 | putstatic2_quick          |      |
| 214  | 0xd6 | invokevirtual_quick       |      |
| 215  | 0xd7 | invokenonvirtual_quick    |      |
| 216  | 0xd8 | invokesuper_quick         |      |
| 217  | 0xd9 | invokestatic_quick        |      |
| 218  | 0xda | invokeinte***ce_quick     |      |
| 219  | 0xdb | invokevirtualobject_quick |      |
| 221  | 0xdd | new_quick                 |      |
| 222  | 0xde | anewarray_quick           |      |
| 223  | 0xdf | multianewarray_quick      |      |
| 224  | 0xe0 | checkcast_quick           |      |
| 225  | 0xe1 | instanceof_quick          |      |
| 226  | 0xe2 | invokevirtual_quick_w     |      |
| 227  | 0xe3 | getfield_quick_w          |      |
| 228  | 0xe4 | putfield_quick_w          |      |

(完)

