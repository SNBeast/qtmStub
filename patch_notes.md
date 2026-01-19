# Patch Notes

qtm's decompressed .code is placed at 0x100000.

## First patch

The first patch is to make the cited branch in the error report unconditional. This branch if taken skips over a branch to 0x115964.

Given the presence of a bunch of checks for top bit != 0 before calling it, 0x115964 is probably a general error-handling function, and we are simply skipping one of many calls to it.

```diff
FirstPatchedFunction:
    0x101234  10 40 2D E9 ; push  {r4, lr}
    0x101238  68 03 00 EB ; bl    #0x101fe0
    0x10123c  90 00 9F E5 ; ldr   r0, [pc, #0x90] ; [0x1012d4] -> 0x1eadd82e
    0x101240  DA 51 00 EB ; bl    #0x1159b0
    0x101244  00 20 A0 E1 ; mov   r2, r0
    0x101248  84 10 9F E5 ; ldr   r1, [pc, #0x84] ; [0x1012d4] -> 0x1eadd82e
    0x10124c  84 00 9F E5 ; ldr   r0, [pc, #0x84] ; [0x1012d8] -> 0x1eb634dc
    0x101250  00 30 A0 E3 ; mov   r3, #0
    0x101254  F5 4E 00 EB ; bl    #0x114e30
    0x101258  0F 10 A0 E1 ; mov   r1, pc          ; 0x101260
    0x10125c  A0 2F B0 E1 ; lsrs  r2, r0, #0x1f
    0x101260  BF 51 00 1B ; blne  #0x115964
    0x101264  70 10 9F E5 ; ldr   r1, [pc, #0x70] ; [0x1012dc] -> 0x1eadd8a8
    0x101268  70 00 9F E5 ; ldr   r0, [pc, #0x70] ; [0x1012e0] -> 0x1eb63450
    0x10126c  E4 4E 00 EB ; bl    #0x114e04
    0x101270  0F 10 A0 E1 ; mov   r1, pc          ; 0x101278
    0x101274  A0 2F B0 E1 ; lsrs  r2, r0, #0x1f
    0x101278  B9 51 00 1B ; blne  #0x115964
    0x10127c  8B 4F 00 EB ; bl    #0x1150b0
    0x101280  4E 03 00 EB ; bl    #0x101fc0
    0x101284  00 40 A0 E1 ; mov   r4, r0
    0x101288  69 4F 00 EB ; bl    #0x115034
    0x10128c  50 10 9F E5 ; ldr   r1, [pc, #0x50] ; [0x1012e4] -> 0x1eb63420
    0x101290  05 00 54 E3 ; cmp   r4, #5
    0x101294  00 00 A0 03 ; moveq r0, #0
    0x101298  01 00 A0 13 ; movne r0, #1
    0x10129c  12 00 C1 E5 ; strb  r0, [r1, #0x12] ; [0x1eb63432]
    0x1012a0  FF 00 10 E3 ; tst   r0, #0xff
    0x1012a4  08 00 C1 E5 ; strb  r0, [r1, #8]    ; [0x1eb63428]
    0x1012a8  08 00 00 0A ; beq   #0x1012d0       ; .skip
    0x1012ac  34 10 9F E5 ; ldr   r1, [pc, #0x34] ; [0x1012e8] -> 0x1eadd828
    0x1012b0  34 00 9F E5 ; ldr   r0, [pc, #0x34] ; [0x1012ec] -> 0x1eb634c8
    0x1012b4  00 20 A0 E3 ; mov   r2, #0
    0x1012b8  6C 04 00 EB ; bl    #0x102470
    0x1012bc  0F 10 A0 E1 ; mov   r1, pc          ; 0x1012c4
    0x1012c0  A0 2F B0 E1 ; lsrs  r2, r0, #0x1f
-   0x1012c4  01 00 00 0a ; beq   #0x1012d0       ; .skip
+   0x1012c4  01 00 00 ea ; b     #0x1012d0       ; .skip
    0x1012c8  10 40 BD E8 ; pop   {r4, lr}
    0x1012cc  A4 51 00 EA ; b     #0x115964
.skip:
    0x1012d0  10 80 BD E8 ; pop   {r4, pc}        ; return
.constants:
    0x1012d4  2E D8 AD 1E ; dw    #0x1eadd82e
    0x1012d8  DC 34 B6 1E ; dw    #0x1eb634dc
    0x1012dc  A8 D8 AD 1E ; dw    #0x1eadd8a8
    0x1012e0  50 34 B6 1E ; dw    #0x1eb63450
    0x1012e4  20 34 B6 1E ; dw    #0x1eb63420
    0x1012e8  28 D8 AD 1E ; dw    #0x1eadd828
    0x1012ec  C8 34 B6 1E ; dw    #0x1eb634c8 
```

## Second patch

The second patch is to simply nop out an svcBreak in a function whose entirety is to svcBreak and return. This function is hit on a system experiencing qtm error 0xf9605002 if the first patch is used to bypass the initial error.

This is probably heavy-handed, but it gets the job done. A service as unimportant as qtm probably shouldn't be allowed to hold the system hostage anyway.

```diff
SvcBreak:
-   0x107960  3c 00 00 ef ; svc   #0x3c           ; Break
+   0x107960  00 00 a0 e1 ; mov   r0, r0          ; nop
    0x107964  1e ff 2f e1 ; bx    lr              ; return
```
