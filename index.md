# Ajinkyas Submission to GSoC'19
## Project: Operating System Fuzzing using Hypervisor
## Introduction: 
This project aims at fuzzing an operating system kernel using hypervisor. Project [*Drakvuf*](https://drakvuf.com) provides a framework for stealthy, blackbox malware analysis. Building on top of it we developed libhijack that can inject arbitrary function calls inside the guest os. In the next step we integrated AFL with libhijack.
### Link to the [repository](https://github.com/the-elves/drakvuf/)

### Important commits:
-  [95ae6b6](https://github.com/the-elves/drakvuf/commit/95ae6b644be5b3d2518c66636b5b3fc9747b0757): This commit completes setup of of libhijacker, 
   1. libhijacker is provided a function name and the rekall profile of the module in which the function is present.
   2. libhijacker first determines the address of function 
   3. libhijacker hijacker waits for a process to be scheduled
   4. When scheduled, 
      1. It hijacks the control flow of the process 
      2. Saves the register state of the process
   5. Makes a function call
   6. After returning from function call
      1. It restores the saved state
      2. Resumes normal execution of the process
-   [3d755b1](https://github.com/the-elves/drakvuf/commit/3d755b1569d890e8ea06ed69e83fca78b463f19a): 
    1. Now libhijacker also take arguments in json format.
    2. Before calling the function libhijacker parses the arguments from json to format required by drakvuf's libinjector and sets up the stack
    3. Calls the function
    4. After returning restores the register state. 
    5. Drakvuf's Blue Screen 
  
-  [d2fe545](https://github.com/the-elves/drakvuf/commit/d2fe545c4507bc61894d229ab6ab655007f92c78): This is the final gsoc commit
