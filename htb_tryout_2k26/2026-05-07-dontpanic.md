first i ran the rust binary `./dontpanic` and typed some random string ... then it threw this weird custom panic message .. 
`🤖💬 < Have you got a message for me? > 🗨️ 🤖: hello`
`😱😱😱 You made me panic! 😱😱😱`

so i opened it in gdb to see what was going on ... tried breaking at the offset but it didnt work because of aslr/pie .. 
so i set the breakpoint on the actual mangled name `b _ZN3src10check_flag17h397d174e03dc8c74E` ..

now if you see the disassembly of that function .. 
`cmp $0x1f,%rsi`
you'll find this line .. this ultimately means the binary is checking if the input length is exactly 31 (0x1f) .. if its not 31 it just jumps straight to the panic block ..

so you can try passing exactly 31 chars ..
like `HTB{0123456789abcdef0123456789a}`

use the above given payload to bypass the outer gate .. then step forward .. you will fall into a loop ...
find the instructions inside the loop :  
it fetches characters one by one and uses an indirect call `call *0x10(%rsp,%rax,8)` ..

by looking at this we confirmed that the binary builds an array of 31 functions (closures in rust, specifically `FnOnce`) .. and each function checks exactly one character .. 
so first it checks index 0 .. goes into the closure .. and does a check like `cmp dil, 0x48` (which is 'H') .. if it doesnt match it triggers the panic hook ..

so exploit idea is ... you write a python script using `pexpect` to automate gdb ...
then you feed the 31 char dummy flag ... script steps into the closure ... reads the correct byte from the `cmp` instruction before it panics ...
updates the dummy flag with the correct char ... and restarts ...
okay so lets try

wrote the python script but it threw a timeout error .. 
`pexpect.exceptions.TIMEOUT: Timeout exceeded.`
looked at the buffer and saw gdb was pausing to ask `Enable debuginfod for this session? (y or [n])` .. 
so modified the spawn command to run completely headless using `-q -ex "set debuginfod enabled off" -ex "set pagination off"`

and here we go!!  
payload executed ... the script stepped through all 31 functions and pulled the right bytes from the registers ...

also i opened it in cutter later to see the graph ... 
and look what we found !!  
it builds the array using `lea` instructions at the start of the function .. but not all 31 functions were there .. missing some chars ..
turns out the rust compiler optimizes it .. so for repeated chars like `_` it just generates one closure and reuses the register pointer to fill the array ..
so we just trace the pointers or use the decompiler tab to see the array order .. and convert the hex values in the closures to ascii ..

and here we gooo!! .. we got the flag ...

also you can run the script as well   

```python
import pexpect
import re
import string
import random

# Initial random flag (HTB format + 27 random letters)
flag = "HTB{111111111111111111111111111"
ansi_escape = re.compile(r'\x1B\[[0-?]*[ -/]*[@-~]')
# Regex to extract character from disassembled cmp line
cmp_re = re.compile(r"cmp\s+\$?0x([0-9a-fA-F]+).*%dil", re.IGNORECASE)
for i in range(31):
    gdb = pexpect.spawn("gdb -q ./dontpanic", encoding="utf-8", timeout=3)
    gdb.expect_exact("(gdb)")
    gdb.sendline("b src::check_flag")
    gdb.expect_exact("(gdb)")
    gdb.sendline("run")
    gdb.expect(r"Have you got a message for me\?")
    gdb.sendline(flag)  # Send current flag
    gdb.expect_exact("Breakpoint 1")  # Wait until we hit the breakpoint
    gdb.expect_exact("(gdb)")
    steps = 63 + i * 12
    for j in range(steps):
        gdb.sendline("si")
        gdb.expect("(gdb)")
    gdb.sendline("disas")
    gdb.expect_exact("(gdb)")
    output = gdb.before
    output = ansi_escape.sub('', output)
    match = cmp_re.search(output)
    if match:
        byte = int(match.group(1), 16)
        flag_list = list(flag)
        flag_list[i] = chr(byte)
        flag = "".join(flag_list)
        print(f"Found char at position {i}: {flag[i]}")
    else:
        print(f"Failed to extract char at position {i}")

    gdb.close()

print("Recovered flag:", "".join(flag))
```

