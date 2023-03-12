---
title: "Application Virtualization"
---
## Abstract
This page collects relevant information regarding programs that run through a virtual machine.

## Bytecode Handler
The execution units that are assigned to a specific bytecode are commonly referred to as handlers.  
  
A common practice in reversing virtualised malware, or virtual machines is to follow a process called _lifting, translating, and repackaging_; whereby, the reverse engineer determines exactly what each handler does (_lifting_), somehow convert the information into a language that the CPU and disassemblers understand (_translating_), and finally repackaging the binary (_repackaging_). It's tedious and complicated, but there already are existing tools for the System interpreter like the tools that I had mentioned in my post.