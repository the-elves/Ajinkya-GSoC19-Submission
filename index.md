# Ajinkyas Submission to GSoC'19
## Project: Operating System Fuzzing using Hypervisor
## Introduction: 
This project aims at fuzzing an operating system kernel using hypervisor. Project [*Drakvuf*](https://drakvuf.com) provides a framework for stealthy, blackbox malware analysis. Building on top of it we developed libhijack that can inject arbitrary function calls inside the guest os. In the next step we integrated AFL with libhijack.
### Link to the [repository](https://github.com/the-elves/drakvuf/)

### Instruction to build and run are [here](https://the-elves.github.io/drakvuf/)

## Important commits:
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
   7. All of this is implemented in src/libhijack/* and src/ozzer.cpp
-   [3d755b1](https://github.com/the-elves/drakvuf/commit/3d755b1569d890e8ea06ed69e83fca78b463f19a): 
    1. Now libhijacker also take arguments in json format.
    2. Before calling the function libhijacker parses the arguments from json to format required by drakvuf's libinjector and sets up the stack
    3. Calls the function
    4. After returning restores the register state. 
    5. Drakvuf's Blue Screen of Death Monitor, (BSODMon) is called when guest bsods. In this commit bsod is modified to report the crashing input
    6. We need to hijack the target process in kernel mode. Kernel Virtual Adress (KVA) Shaddowing is windows implementation of kernel page table isolation. It maintains two DTBs for translation, user DTB and kernel DTB. In this commit checks were added to differentiate between these two DTBs
    7. All of this is implemented in src/libhijack/* and src/ozzer.cpp
-   [79504d4](https://github.com/the-elves/drakvuf/commit/79504d47cc54e9499a9cdec0614ff6064b15c70d):
    1. Integrated this with AFL by mannually coding the things that afl does by instrumentation, as following
       1. AFL instruments target to start do some initialization and running in infinite loop
       2. In loop it expects the program to wait for comon on file descriptor 196
       3. Once it receives the command, the program should fork
          - Child should break and execute the program
          - Parent should wait for child to finish executing, and report its status back to AFL
          - Parent then repeats step 2
    2. Work mentioned here is done in afl_injector.cpp
-  [eed6284](https://github.com/the-elves/drakvuf/commit/eed6284ec352b25a7c2d8efcc7914c1e62a2c152):
      1. AFL performs coverage guided fuzzing. i.e. It measures path coverage by instrumenting the branch instructions. 
      2. AFL performs coverage measurement by either instrumenting or running the program under modified qemu. both of these options were not possible for us.
      3. We used capstone to accomplish this task for us. From the injection function we read instructions and inserted breakpoints at branch instructions instructions and call instructions. 
      4. At those respective breakpoints we perform the coverage measurement and place breakpoints at both, the branch/call target and at the fall through location of the branch.
-  [d2fe545](https://github.com/the-elves/drakvuf/commit/d2fe545c4507bc61894d229ab6ab655007f92c78): This is the final gsoc commit

