---
title: bye~~
description: "A short write-up on tooling, lessons learned, and next steps."
comments: true

publish: true
draft: false

tags:
  - pwn
  - reversing
  - obsidian

created: 2026-02-10
modified: 2026-02-18
published: 2026-02-19
---
 
# Reverse-Engineering Notes: Week 7

dasfasfd
This week I focused on debugging a two-stage exploit for a 32-bit ELF binary:

- NX enabled  
- Canary present  
- Partial RELRO  
- No PIE  

The goal was to pivot the stack into `.data`, leak `puts@libc`, calculate `system`, and return with `/bin/sh`.

---

## 1. First Attempt — Stack Pivot to `.data`

The vulnerable function:

```asm
push 0x30
lea eax, [ebp+buf]
push eax
push 0
call _read
leave
ret
```