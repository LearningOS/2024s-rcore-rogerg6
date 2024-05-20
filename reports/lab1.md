# lab1总结
我是通过在TaskControlBlock中加入syscall_times和load_time字段, 来实现syscall_taskinfo的系统调用的. 在
合适的节点更新这2个字段信息

# 简答题
## 1
ch2b_bad_address.rs: PageFault in application, bad addr = 0x0, bad instruction = 0x804003ac
ch2b_bad_instructions.rs和ch2b_bad_registers.rs: IllegalInstruction in application

rustSBI: RustSBI version 0.3.0-alpha.2, adapting to RISC-V SBI v1.0.0

## 2.1 
__restore主要用于以下2中情景:
- 刚开始运行新程序时,用于从S态到U态: a0是前一个app的TaskContext
- app调用syscall时, 从S态到U态: a0的值是内核栈顶

## 2.2
处理了sstatus, sepc, sscratch这3个CSR寄存器
sstatus中的SPP字段保存这CPU处于user态的状态
sepc保存着进入trap前的最后一条指令的地址, 当sret后会继续执行该指令后面的指令
sscratch保存这user statck的sp

## 3
跳过x2(sp)是因为: 如果恢复sp[2]到寄存器sp, 则后续的操作就会把trap之前的用户栈的内容恢复到寄存器中,相当于没有调用syscall
跳过x4(thread pointer)是因为在trap过程中没有用到x4, 在__alltraps中也没有保存x4, 故在__restore中也不用恢复

## 4
sp指向用户栈, sscratch指向内核栈

## 5
sret, cpu硬件执行sret会自动从superviser切换到user

## 6
sp指向内核栈, sscratch指向用户栈

## 7
ecall