---
title: Rance IX
---
## Abstract
Rance IX is a game made by AliceSoft, and its instructions are interpreted using Alicesoft's proprietary virtual machine (VM): [[writeup/System Virtual Machine|System4]]. 

I crafted an exploit for Rance IX by determining where and how the System interpreter fetches instructions, how these instructions are decoded, and finally writing my own scripts to rewrite how these instructions are executed.

## Binary Analysis
After reading through [[writeup/System Virtual Machine#System4 Analysis|System4 Analysis]], I went to reverse-engineer the binary.

### Inside a Handler
Using [Cheat Engine](https://cheatengine.org/), I scanned for basic character properties (health, damage, etc.) and set watchpoints on those properties to alert any access. Changing those properties in the game triggered the watchpoint and led me to the subroutine that accessed those properties.

![rance-vm-access](ranceix-vm-access.png)
*A snippet of the subroutine that accessed the character properties.*

To determine how and where the subroutine belonged, I used IDA's graph analysis to display the control-flow of the function that it belonged to. 

![System-Interprepter](images/system42-interprepter.png)
*The subroutine that accessed those character properties was found somewhere in the middle.*

The function is actually a gargantuan switch-case statement (with over 200 cases), and the subroutine is part of a case. After reading through some [[writeup/System Virtual Machine#Resources|resources]], this function is actually the dispatcher for the inputted bytecode from an *ain* script. The case that I was in was just a handler for the bytecode.

### Finding `VM Entry`
In general, virtualized functions will have their own instruction pointer and stack space. For the virtualized function to interface with these components, it can utilize hardware registers, stack, and heap to communicate changes. Therefore, to prevent any corruption, a virtualized function must preserve the context of the function that called it, and restore it. Preservation is usually found at the function prologue; on the other hand, restoration is usually found at the function epilogue.
Respectively, they are called `VM Entry` and `VM Exit`.

In this case, finding `VM Entry` is relatively easy; trace upwards to the beginning of the function.

![Rance-VM-Entry](images/ranceix-vm-entry.png)
*A snippet of `VM Entry.`*

### Execution Cycle
After finding `VM Entry`, I was curious as to how this function was called.

![[images/ranceix-vm-loop.png]]
*A loop that calls `VM Entry`*

After some debugging, this loop inputs the bytecode from the *ain* script as an argument to `VM Entry`, increments the virtual machine instruction pointer, and calls `VM Entry`. This execution continues indefinitely until there is a signal from `*(CJaffaVM + 0x234)` to exit.

In C terms, this is how the execution cycle would look like....
```c
do
{
  vm_ip = *(CJaffaVM + 0x224);
  bytecode = *vm_ip;
  *(CJaffaVM + 0x224) = vm_ip + 1;
  vm_entry(CJaffaVM, a2, a3, bytecode);
}
while ( !*(CJaffaVM + 0x234) );
```

### Bytecode Storage
After some more reverse-engineering, I found out where the bytecode was being stored.

## Exploit Development
I didn't want to hook the execution cycle needlessly, since the game will lag. So, I created a thread to interface with these components, and if needed, locks the execution cycle to prevent any access shared resource.

The script can be found [here](https://fearlessrevolution.com/viewtopic.php?f=2&t=23630&sid=9dd754c98ad76236735299b9f017dbae&start=15), just look a few posts down.

## Credits
-   **nunuhara, kichikuou, OPNA2608, and toufuguy** - Thank you all for your work regarding Alicesoft's proprietary engine and file formats. I would not have been able to make any key analyses without the use of your tools. Thank you so much for sharing your work since I have definitely learned a lot about virtual machine architecture and the System engine. 
-   **SomeLoliCatGirl** - Thank you for the wonderful AIN to bytecode decompiler. Your decompiler helped me tremendously with my analysis because I was able to locate where the bytecode was being interpreted and where it was being stored. As a result, I was able to craft my own custom bytecode injections, and effectively localize myself within the System VM architecture without doing much guessing.
-   **Tim Blazytko** - Great educational video regarding analysis on VM architecture! 
-   **FamousAnos** - Thank you for the save!
-   **Darkbyte and contributors** - Obviously...