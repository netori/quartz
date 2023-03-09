---
title: Rance IX
---
# Abstract
Rance IX is a game made by AliceSoft, and its instructions are interprepted using Alicesoft's proprietary virtual machine (VM): [[System]]. 

I crafted an exploit for Rance IX by determining where and how the System interpreter fetches instructions, how these instructions are decoded, and finally writing my own scripts to rewrite how these instructions are executed.

# Context
System is a virtual machine that interprets Alicesoft's proprietary game files (ain, jaf, jam, etc,).

![[images/system42-interprepter.png]]

This entire graph is a portion of the System interpreter; it's a huge switch-case statement (with over 100 cases), and each case pertains to a particular bytecode that the System interpreter will execute. The execution units that are assigned to a specific bytecode are commonly referred to as handlers.  
  
A common practice in reversing virtualized malware, or virtual machines is to follow a process called _lifting, translating, and repackaging_; whereby, the reverse engineer determines exactly what each handler does (_lifting_), somehow convert the information into a language that the CPU and disassemblers understand (_translating_), and finally repackaging the binary (_repackaging_). It's tedious and complicated, but there already are existing tools for the System interpreter like the tools that I had mentioned in my post.  

# Credits
-   **nunuhara, kichikuou, OPNA2608 toufuguy** - Thank you all for your work regarding Alicesoft's proprietary engine and file formats. I would not have been able to make any key analyses without the use of your tools. Thank you so much for sharing your work since I have definitely learned a lot about virtual machine architecture and the System engine. 
-   **SomeLoliCatGirl** - Thank you for the wonderful AIN to bytecode decompiler. Your decompiler helped me tremendously with my analysis because I was able to locate where the bytecode was being interpreted and where it was being stored. As a result, I was able to craft my own custom bytecode injections, and effectively localize myself within the System VM architecture without doing much guessing.
-   **Tim Blazytko** - Great educational video regarding analysis on VM architecture! 
-   **FamousAnos** - Thank you for the save!
-   **Darkbyte and contributors** - Obviously...