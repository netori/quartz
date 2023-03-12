---
title: "System Virtual Machine"
aliases: System VM
---
## Abstract
System is a process virtual machine designed by AliceSoft to increase accessibility between different computer platforms. 

> [!quote] Brief by [AliceSoft Fandom](https://alicesoft.fandom.com/wiki/System)
>
> **System** is a series of OS/VM hybrids that was designed by [Alice Soft](https://www.alicesoft.com/) to cope with porting their games across different computer platforms. This was especially important in the early days of Alice Soft's history, when there were multiple prevalent families of computers ([PC-98](http://en.wikipedia.org/wiki/NEC_PC-9801 "wikipedia:NEC PC-9801"), [x68k](http://en.wikipedia.org/wiki/x68k "wikipedia:x68k"), [FM-Towns](http://en.wikipedia.org/wiki/FM-Towns "wikipedia:FM-Towns") etc) competing on the Japanese PC market. A similar strategy was used by other developers at the time, such as [Infocom](http://en.wikipedia.org/wiki/Infocom "wikipedia:Infocom")'s famous Z-Machine interpreter.

## Environment
System is a virtual machine that interprets Alicesoft's proprietary game files (*.ain*, *.jaf*, *.jam*, etc.).

![System-Interprepter](images/system42-interprepter.png)*A graph disassembly produced by IDA of a portion of the System VM.*

The above graph is a portion of the System interpreter; it's a gargantuan switch-case statement (with over 100 cases), and each case pertains to a particular bytecode that the System interpreter will execute. The execution units that are assigned to a specific bytecode are commonly referred to as handlers.  

A common practice in reverse-engineering virtualized malware, or virtual machines is to follow a process called _lifting, translating, and repackaging_; whereby, the reverse engineer determines exactly what each handler does (_lifting_), somehow convert the information into a language that the CPU and disassemblers understand (_translating_), and finally repackaging the binary (_repackaging_). It's tedious and complicated, but there already are existing tools for the System VM.

## System4 Analysis
System4 is a model of heap virtual machine architecture. It has an entry and an exit, calculations that are applied to datum located inside its memory heap, and a dispatcher to individual handlers for some inputted bytecode. These core concepts can be explained [here](https://www.youtube.com/watch?v=b6udPT79itk).

### `CJaffaVM`
`CJaffaVM` (denoted by RTTI) is an object that governs the System virtual machine. There is only **one** instance of this object; moreover, there is always a static address that points to this instance.

### `CPage`
`CPage` (denoted by RTTI) is an object that contains game variables and in-game values. There are at least a hundred of these instances.

### `CPage` Array
The `CPage` Array contains all `CPage` instances.

## Resources
The following hyperlinks lead to some insightful references to the System VM.

### Tools
- [alice-tools](https://github.com/nunuhara/alice-tools) - Incredibly useful tool for extracting/editing files from AliceSoft games.
- [ain decompiler](https://github.com/UserUnknownFactor/AinDecompiler) -  Fantastic tool developed by **SomeLoliCatGirl** that decompiles *ain* file formats into native System bytecode.

### General
- [haniwa.technology](https://haniwa.technology/) - This site collects technical information and resources relating to games and game engines developed by AliceSoft.
- [xsystem4](https://github.com/nunuhara/xsystem4) - System4 for UNIX-like operating systems

### Uses System
- [[node/Beat Angel Escalayer R]]
- The Rance Series
	- [[node/Rance IX |Rance IX]]