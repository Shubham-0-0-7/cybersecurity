so this is a rust binary 

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

