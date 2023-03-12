---
title: "Rance IX"
---
## Abstract
Rance IX is a game made by AliceSoft, and its instructions are interpreted using Alicesoft's proprietary virtual machine (VM): [[node/System Virtual Machine|System4]]. 

I crafted an exploit for Rance IX by determining where and how the System interpreter fetches instructions, how these instructions are decoded, and finally writing my own [Cheat Engine](https://cheatengine.org/) scripts to rewrite how these instructions are executed.

## Binary Analysis
After reading through [[node/System Virtual Machine#System4 Analysis|System4 Analysis]], I know that I will be dealing with some form of virtual machine and virtualized functions. 

In general, virtualized functions will have their own instruction pointer and stack space. For a virtualized function to interface with these memory components, it can utilise hardware registers, stack, and heap to communicate changes. Therefore, to prevent any corruption, a virtualized function must preserve the context of the function that called it, and restore it. Preservation is usually found at the function prologue; on the other hand, restoration is usually found at the function epilogue.

Respectively, they are called `VM Entry` and `VM Exit`.

With this knowledge, I started to reverse-engineer the game binary.

### Inside a Handler
I scanned for basic character properties (health, damage, gold, etc.) and set watchpoints on those properties to alert any access. Changing those properties in the game triggered the watchpoint and led me to the subroutine that accessed those properties.

![[images/ranceix-vm-access.png]]
*A snippet of the subroutine with some modifications that accessed the character properties.*

To determine the subroutine's intention in its parent function, I used IDA's graph analysis to display the parent function's control-flow. 

![[images/system42-interprepter.png]]
*The subroutine is actually a case within this huge function's switch-case statement.*

The subroutine's parent function is actually the dispatcher for some input of bytecode from an *ain* script; it's a gargantuan switch-case statement (with over 200 cases) where each case is assigned to a specific bytecode that the System interpreter will handle.

###  Finding `VM Entry`
Realising that I am inside a virtualized function, finding `VM Entry` is relatively easy; trace upwards to the beginning of the function (i.e. the instructions before the dispatch).

![[images/ranceix-vm-entry.png]]
*A snippet of `VM Entry` with some modification.*

### Execution Cycle
Since the dispatcher takes bytecode as an argument, listing cross-references to `VM Entry` led me to a subroutine `loc_575D20`.

![[images/ranceix-vm-loop.png]]
*A subroutine that continuously calls `VM Entry`*

After some debugging, this subroutine is a loop that inputs bytecode from an *ain* script as an argument to `VM Entry`, increments the virtual machine instruction pointer, and calls `VM Entry`. This loop continues indefinitely until there is a signal from `*(CJaffaVM + 0x234)` to exit.

In C code, this is how the execution cycle would look like....
```c
do
{
  vm_ip = *(CJaffaVM + 0x224);
  bytecode = *vm_ip;
  *(CJaffaVM + 0x224) = vm_ip + 1;
  vm_entry(CJaffaVM, unknown1, unknown2, bytecode);
}
while ( !*(CJaffaVM + 0x234) );
```

### Bytecode Storage
After some more reverse-engineering, I found out where the bytecode was being stored.

## Craft Exploit
Since game values are stored in `CPage` instances, I created a thread to interface with these components and, if necessary, temporarily lock the execution cycle. I created a thread instead of directly hooking the execution cycle, since the game will lag.

The script can be found [here](https://fearlessrevolution.com/viewtopic.php?f=2&t=23630&sid=9dd754c98ad76236735299b9f017dbae&start=15), just look a few posts down.
