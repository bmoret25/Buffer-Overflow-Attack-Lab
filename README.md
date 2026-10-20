## Overview
Exploiting a 64-bit stack buffer overflow in `stack-L3-dbg` to gain a shell.

## Prerequisites
- Linux (x86_64)
- `gdb` with pwndbg or gef
- Python 3
- Binary compiled without stack canaries / with executable stack as required by the lab

## Step 1 — Find the buffer address
Run gdb on the debug binary and inspect the stack to locate the buffer:


```text
$ gdb ./stack-L3-dbg
(gdb) break main
(gdb) run
(gdb) p &buffer      # note the address
  ```

![gdb buffer address](<img width="898" height="732" alt="Screenshot 2026-09-22 200056" src="https://github.com/user-attachments/assets/e62a7114-92f6-4a40-8e7f-1519f5169c46" />)

## Step 2 — Update `exploit.py`
Modify the exploit with the 64-bit shellcode. Key parameters:

- **Shellcode length:** 517 bytes
- **Offset:** 216 bytes
  - Calculated as `(buffer_address - rbp/ret_address)` — the hex difference came out to 208, plus 8 for the 64-bit saved frame pointer alignment.

![exploit.py shellcode](<img width="876" height="868" alt="Screenshot 2026-09-22 200947" src="https://github.com/user-attachments/assets/3b6d7477-fbf4-4444-afee-9b0a464afc98" />
)

## Step 3 — Set return address and NOP sled
Update `exploit.py` with:
- The **return address** = buffer address found in Step 1
- **Offset** = 216
- Small padding/NOP sled offset before the shellcode so the return lands cleanly in the sled

![exploit.py retaddr](<img width="732" height="765" alt="Screenshot 2026-09-22 201024" src="https://github.com/user-attachments/assets/5210e0a4-e9d5-4984-970b-05f4ddc4d318" />
)

## Step 4 — Run the exploit

```
./exploit.py ./stack-L3
```

You should drop into a shell:

![shell obtained](<img width="796" height="230" alt="Screenshot 2026-09-22 201242" src="https://github.com/user-attachments/assets/6fb5a780-a420-44d7-9591-c1f5feec74ee" />
)

> ⚠️ **Known issue:** In my environment the spawned shell is a **seed shell, not root** —
> my machine still doesn't play nicely with zsh. I'm actively working on fixing the
> shell/environment setup. See `notes.md` for progress.

## Notes
See [notes.md](notes.md) for debugging notes and the zsh/shell issue.
EOF
