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
>CC boot/boot/bootasm.S、bootmain.c<br>
>CC tools/sign.c<br>
>链接ld bin/bootblock<br>
>ld -m    elf_i386 -nostdlib -n -e start -ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o<br>
## sign.c的作用
>根据编译命令,是没有生成sign.o的可执行文件，在 Makefile:第151-157行：<br>
>$(bootblock): $(call toobj,$(bootfiles)) | $(call totarget,sign)<br>
	>@echo + ld $@<br>
	>$(V)$(LD) $(LDFLAGS) -N -e start -Ttext 0x7C00 $^ -o $(call toobj,bootblock)<br>
	>@$(OBJDUMP) -S $(call objfile,bootblock) > $(call asmfile,bootblock)<br>
	>@$(OBJDUMP) -t $(call objfile,bootblock) | $(SED) '1,/SYMBOL TABLE/d; s/ .* / /; /^$$/d' > $(call symfile,bootblock)<br>
	>@$(OBJCOPY) -S -O binary $(call objfile,bootblock) $(call outfile,bootblock)<br>
	>@$(call totarget,sign) $(call outfile,bootblock) $(bootblock)<br>
>根据sign的设置将bootblock部分写入img中<br>
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
>自sign.c第23-40行<br>
>memset(buf, 0, sizeof(buf));<br>
    >FILE *ifp = fopen(argv[1], "rb");<br>
    >int size = fread(buf, 1, st.st_size, ifp);<br>
    >if (size != st.st_size) {<br>
        >fprintf(stderr, "read '%s' error, size is %d.\n", argv[1], size);<br>
        >return -1;<br>
    >}<br>
>fclose(ifp);<br>
    >buf[510] = 0x55;<br>
    >buf[511] = 0xAA;<br>
    >FILE *ofp = fopen(argv[2], "wb+");<br>
    >size = fwrite(buf, 1, 512, ofp);<br>
    >if (size != 512) {<br>
        >fprintf(stderr, "write '%s' error, size is %d.\n", argv[2], size);<br>
        >return -1;<br>
    >}<br>
    >fclose(ofp);<br>
    >printf("build 512 bytes boot sector: '%s' success!\n", argv[2]);<br>
-----
bootblock的大小为512个字节，第511和第512位为 **0x55**和**0xAA**,第1到510位填充0<br>