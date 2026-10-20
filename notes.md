## Current issue: seed shell instead of root
- Shell spawns successfully, but privileges/environment are wrong.
- Cause appears related to zsh not functioning on this machine.
- Workarounds being tested:
  - [ ] Force `/bin/bash` in the exploit's execve payload
  - [ ] Set `SHELL=/bin/bash` before running
  - [ ] Check `getuid()`/`setuid(0)` in shellcode — ensure shellcode calls `setuid(0)` before `execve`
  - [ ] Verify binary has SUID bit if root is expected
