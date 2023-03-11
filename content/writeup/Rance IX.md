---
title: Rance IX
---
## Abstract
Rance IX is a game made by AliceSoft, and its instructions are interpreted using Alicesoft's proprietary virtual machine (VM): [[writeup/System Virtual Machine|System4]]. 

I crafted an exploit for Rance IX by determining where and how the System interpreter fetches instructions, how these instructions are decoded, and finally writing my own scripts to rewrite how these instructions are executed.

## Binary Analysis
Reading through [[writeup/System Virtual Machine#System4 Analysis|System4 Analysis]]

### Inside a Handler
Using [Cheat Engine](https://cheatengine.org/), I scanned for basic character properties and set watchpoints to determine any access. 

### Finding Entry

![Rance-VM-Entry](images/ranceix-vm-entry.png)

### Loop

```asm
00575D20 | mov eax,dword ptr ds:[esi+224]                     |
00575D26 | movzx ecx,word ptr ds:[eax]                        |
00575D29 | add eax,2                                          | breakif(ecx == 0x5C)
00575D2C | push ecx                                           |
00575D2D | mov ecx,esi                                        |
00575D2F | mov dword ptr ds:[esi+224],eax                     |
00575D35 | call <rance9.vm_entry>                             |
00575D3A | cmp dword ptr ds:[esi+234],0                       |
00575D41 | je <rance9.vm_loop>                                |
00575D43 | jmp rance9.575C50                                  |
```


## Exploit Development



## Credits
-   **nunuhara, kichikuou, OPNA2608, and toufuguy** - Thank you all for your work regarding Alicesoft's proprietary engine and file formats. I would not have been able to make any key analyses without the use of your tools. Thank you so much for sharing your work since I have definitely learned a lot about virtual machine architecture and the System engine. 
-   **SomeLoliCatGirl** - Thank you for the wonderful AIN to bytecode decompiler. Your decompiler helped me tremendously with my analysis because I was able to locate where the bytecode was being interpreted and where it was being stored. As a result, I was able to craft my own custom bytecode injections, and effectively localize myself within the System VM architecture without doing much guessing.
-   **Tim Blazytko** - Great educational video regarding analysis on VM architecture! 
-   **FamousAnos** - Thank you for the save!
-   **Darkbyte and contributors** - Obviously...