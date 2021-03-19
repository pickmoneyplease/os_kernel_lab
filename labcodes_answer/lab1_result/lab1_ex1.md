# lab1_ex1
## kernel的编译链接
>编译
>CC kern/ 
>        init/init.c、stdio.c、readline.c、panic.c    
>        debug/kdebug.c、kmonitor.c、clock.c、console.c、picirq.c、intr.c
>        trap/trap.c、vector.S、trapentry.S
>        mm/pmm.c
>        libs/string.c、 printfmt.c、<br>
>链接
>ld -m    elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/stdio.o obj/kern/libs/readline.o obj/kern/debug/panic.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/picirq.o obj/kern/driver/intr.o obj/kern/trap/trap.o obj/kern/trap/vectors.o obj/kern/trap/trapentry.o obj/kern/mm/pmm.o  obj/libs/string.o obj/libs/printfmt.o<br>
## bootblock编译链接
>编译
>CC boot/boot/bootasm.S、bootmain.c
>CC tools/sign.c<br>
>链接ld bin/bootblock<br>

>ld -m    elf_i386 -nostdlib -n -e start -ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
## img创建生成
>dd if=/dev/zero of=bin/ucore.img count=10000
>记录了10000+0 的读入
>记录了10000+0 的写出
>5120000 bytes (5.1 mb, 4.9 mib) copied, 0.0184065 s, 278 MB/s
>dd if=bin/bootblock of=bin/ucore.img conv=notrunc
>记录了1+0 的读入
>记录了1+0 的写出
>512 bytes copied, 7.8304e-05 s, 6.5 mb/s
>dd if=bin/kernel of=bin/ucore.img seek=1 conv=notrunc
>记录了154+1 的读入
>记录了154+1 的写出

## 一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？
>