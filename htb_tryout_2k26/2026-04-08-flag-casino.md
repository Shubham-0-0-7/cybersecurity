```bash
neocipher27@fedora$ gdb ./casino   
GNU gdb (Fedora Linux) 17.1-4.fc43
Copyright (C) 2025 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./casino...

This GDB supports auto-downloading debuginfo from the following URLs:
  <ima:enforcing>
  <https://debuginfod.fedoraproject.org/>
  <ima:ignore>
Enable debuginfod for this session? (y or [n]) y
Debuginfod has been enabled.
To make this setting permanent, add 'set debuginfod enabled on' to .gdbinit.
Downloading separate debug info for /home/neocipher27/Downloads/rev_flagcasino/casino
(No debugging symbols found in ./casino)                                                                                                                                                                            
(gdb) disas main
Dump of assembler code for function main:
   0x0000000000001185 <+0>:	push   %rbp
   0x0000000000001186 <+1>:	mov    %rsp,%rbp
   0x0000000000001189 <+4>:	sub    $0x10,%rsp
   0x000000000000118d <+8>:	lea    0xf24(%rip),%rdi        # 0x20b8
   0x0000000000001194 <+15>:	call   0x1030 <puts@plt>
   0x0000000000001199 <+20>:	lea    0xe80(%rip),%rdi        # 0x2020 <banner>
   0x00000000000011a0 <+27>:	call   0x1030 <puts@plt>
   0x00000000000011a5 <+32>:	lea    0xf2c(%rip),%rdi        # 0x20d8
   0x00000000000011ac <+39>:	call   0x1030 <puts@plt>
   0x00000000000011b1 <+44>:	movl   $0x0,-0x4(%rbp)
   0x00000000000011b8 <+51>:	jmp    0x1258 <main+211>
   0x00000000000011bd <+56>:	lea    0xf35(%rip),%rdi        # 0x20f9
   0x00000000000011c4 <+63>:	mov    $0x0,%eax
   0x00000000000011c9 <+68>:	call   0x1040 <printf@plt>
   0x00000000000011ce <+73>:	lea    -0x5(%rbp),%rax
   0x00000000000011d2 <+77>:	mov    %rax,%rsi
   0x00000000000011d5 <+80>:	lea    0xf20(%rip),%rdi        # 0x20fc
   0x00000000000011dc <+87>:	mov    $0x0,%eax
   0x00000000000011e1 <+92>:	call   0x1060 <__isoc99_scanf@plt>
   0x00000000000011e6 <+97>:	cmp    $0x1,%eax
   0x00000000000011e9 <+100>:	je     0x11f5 <main+112>
   0x00000000000011eb <+102>:	mov    $0xffffffff,%edi
   0x00000000000011f0 <+107>:	call   0x1070 <exit@plt>
   0x00000000000011f5 <+112>:	movzbl -0x5(%rbp),%eax
   0x00000000000011f9 <+116>:	movsbl %al,%eax
   0x00000000000011fc <+119>:	mov    %eax,%edi
   0x00000000000011fe <+121>:	call   0x1050 <srand@plt>
   0x0000000000001203 <+126>:	call   0x1080 <rand@plt>
   0x0000000000001208 <+131>:	mov    -0x4(%rbp),%edx
   0x000000000000120b <+134>:	movslq %edx,%rdx
   0x000000000000120e <+137>:	lea    0x0(,%rdx,4),%rcx
   0x0000000000001216 <+145>:	lea    0x2e63(%rip),%rdx        # 0x4080 <check>
   0x000000000000121d <+152>:	mov    (%rcx,%rdx,1),%edx
   0x0000000000001220 <+155>:	cmp    %edx,%eax
   0x0000000000001222 <+157>:	jne    0x1232 <main+173>
   0x0000000000001224 <+159>:	lea    0xed5(%rip),%rdi        # 0x2100
   0x000000000000122b <+166>:	call   0x1030 <puts@plt>
   0x0000000000001230 <+171>:	jmp    0x1254 <main+207>
   0x0000000000001232 <+173>:	lea    0xed6(%rip),%rdi        # 0x210f
   0x0000000000001239 <+180>:	call   0x1030 <puts@plt>
   0x000000000000123e <+185>:	lea    0xee3(%rip),%rdi        # 0x2128
   0x0000000000001245 <+192>:	call   0x1030 <puts@plt>
   0x000000000000124a <+197>:	mov    $0xfffffffe,%edi
   0x000000000000124f <+202>:	call   0x1070 <exit@plt>
   0x0000000000001254 <+207>:	addl   $0x1,-0x4(%rbp)
--Type <RET> for more, q to quit, c to continue without paging--
   0x0000000000001258 <+211>:	mov    -0x4(%rbp),%eax
   0x000000000000125b <+214>:	cmp    $0x1c,%eax
   0x000000000000125e <+217>:	jbe    0x11bd <main+56>
   0x0000000000001264 <+223>:	lea    0xef5(%rip),%rdi        # 0x2160
   0x000000000000126b <+230>:	call   0x1030 <puts@plt>
   0x0000000000001270 <+235>:	mov    $0x0,%eax
   0x0000000000001275 <+240>:	leave
   0x0000000000001276 <+241>:	ret
End of assembler dump.

```

corresponding C code:  

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/52fbdc27-9ab2-4945-a3d5-c59402c97667" />

and yeah we got the flag!!  
