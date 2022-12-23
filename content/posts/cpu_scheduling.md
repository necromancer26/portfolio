---
title: "Cpu_scheduling"
date: 2022-12-24T00:47:10+01:00
# draft: true
---

# CPU Scheduling

- multiprogramming is giving cpu to to processes when the current process needs to wait for I/O operation
- when one process has to wait the os takes the cpu from that process and giving it to another process

# CPU scheduling alogrithms

## CPU and I/O Burst Cycles

- process execution consists of a cycle of ==cpu execution== and ==I/O wait==.
- process alternates between these 2 states
- process execution begins with :
  - _CPU burst_
  - _I/O burst_
  - _CPU burst_
  - _I/O burst_
  - ...
    _CPU burst is when the process is being executed in the CPU_
    _I/O burst is when the cpu is waiting for I/O for further execution_

1. preemptive scheduling : a high priority process can be given the CPU
   - when a process switches from _running state_ to _ready state_ (interruption)
   - when a process switches from _waiting state_ to _ready state_ (completion of I/O)
2. non preemptive scheduling : a process will not be distrubed until it is complete
   - when a process switches from _running_ to _waiting state_ (waiting for I/O)
   - when a process _terminates_

## Scheduling criteria 📋

1. CPU utilization :
   - we want to keep the cpu as busy as possible. cpu utlization conceptually ranges from 0 to 100%
2. Throughput :
   - measures the number of processes that are being completed by time unit
3. Turnaround time :
   - the interval from the time of submission of a process to the time of completion/
   - **turnaround time is the sum of the periods spent waiting to get into memory, waiting in the ready queue, executing on the cpu and doing I/O**
4. Waiting time :
   - waiting time is the sum of the periods spent waiting in the ready queue.
5. Response time :
   - time from the submission of a request until the first response is produced.

### FCFS (First Come First Served) 🚦

- Simplest CPU-scheduling algorithm
- the process that requests the cpu first is allocated the cpu first
- implmentation of fcfs policy is easily managed with a **FIFO queue**
- the average waiting time under the FCFS policy is quite long

| Process | Burst Time (ms) |
| ------- | --------------- |
| P1      | 24              |
| P2      | 3               |
| P3      | 3               |

P1----------------------P2-P3
|------------------------|---|---|
0-----------------------24-27-30

- waiting time for P1=0
- waiting time for P2=24 |---> **Avg waiting time** = (0+24+27)/3 = **17 ms**
- waiting time for P3=27
  **another case**
  P2-P3-P1
  |---|---|------------------------|
  0--3--6-----------------------30
- waiting time for P1= 6
- waiting time for P2= 0 |---> **Avg waiting time** = (0+3+6)/3 = **3 ms**
- waiting time for P3= 3
  **! depends on the cpu burst time**
  **! FCFS is non preemptive**
- Process keeps the cpu until termination or requesting I/O

### SJFS (Shortest Job First Scheduling) 🚀

- This alogrithm associates with each process the length of **the process's next cpu burst**
- when the cpu is available, it is assigned to the process **that has the smallest next CPU burst**
- if the burst is the same, FCFS scheduling is used to break the tie
- **SJFS algorithm can be preemptive or non preemptive**

#### Example of non preemptive SJFS

| Process | Burst Time (ms) |
| ------- | --------------- |
| P1      | 6               |
| P2      | 8               |
| P3      | 7               |
| P4      | 3               |

**Gant chart**
P4-P1----P3-----P2
|---|------|-------|--------|
0--3-----9------16------24
**waiting time for P1 = 3ms**}
**waiting time for P2 = 16ms** }==> average waiting time = (3+16+9)/4= _7 ms_
**waiting time for P3 = 9ms**}=> with fcfs the avg wait would be _10.25 ms_
**waiting time for P4= 0ms**}

#### Example of preemptive SJFS

| Process | Arrival Time | Burst Time (ms) |
| ------- | ------------ | --------------- |
| P1      | 0            | 8               |
| P2      | 1            | 4               |
| P3      | 2            | 9               |
| P4      | 3            | 5               |

**P1**-P2----P4------**P1**----------P3
| **-** | - - - - | - - - - - | **- - - - - - -** | - - - - - - - - - |
0-1-------5-------10----------17-------------26
**waiting time for P1 = (0+10) - 1 - 0 = 9ms**}
**waiting time for P2 = 1- 0 -1 = 0 ms** }==> average waiting time = (26)/4= _6.5 ms_
**waiting time for P3 = 17 - 0 - 2 = 15 ms**}=>
**waiting time for P4= 5 - 0 - 3 = 2 ms**}

- ==waiting time = total waiting time - nb of ms process executed - arrival time==
- Although the SJF algorithm is optimal, it cannot be implemented at the level of short-term CPU scheduling.
- The real difficulty with the SJF algorithm is knowing the length of the next CPU request.
- There is no way to know the length of the next CPU burst.
  One approach is:
- To try to approximate SJF scheduling.
- We may not know the length of the next CPU burst, but we may be able to predict its value.

### PS (Priority Scheduling) 📋

- A priority is associated with each process, and the CPU is allocated to the process with the highest priority.
- Equal-priority processes are scheduled in FCFS order.
- An SJF algorithm is simply a priority algorithm where the priority is the inverse of the (predicted) next CPU burst.
- The larger the CPU burst, the lower the priority, and vice versa.
- Priority scheduling can be either **preemptive** or **nonpreemptive**
- A preemptive priority scheduling algorithm will preempt the CPU if the priority of the newly arrived process is higher than the priority of the currently running process.
- A nonpreemptive priority scheduling algorithm will simply put the new process at the head of the ready queue.

| PID | Burst Time (ms) | **Priority** |
| --- | --------------- | ------------ |
| P1  | 10              | **3**        |
| P2  | 1               | **1**        |
| P3  | 2               | **4**        |
| P4  | 1               | **5**        |
| P5  | 5               | **2**        |

P2-P5-----P1--------------P3--P4
| - | - - - - - | - - - - - - - - - - | - - | - |
0-1--------6----------------16-18-19
**waiting time for P1 = 6 ms**}
**waiting time for P2 = 0 ms** }==> average waiting time = 41/5= _8.2 ms_
**waiting time for P3 = 16 ms**}=>
**waiting time for P4= 18 ms**}
**waiting time for P5= 1 ms**}

#### Problem with Priority Scheduling algorithm ⚠️

- in heavily loaded computer system with a stady steam of high priority processes can prevent a low priority process of ever getting to use the cpu
- can leave some low priority processes waiting indefinetely
- a major problem of priority scheduling algorithm is **indefinite blocking** or **starvation**

#### Solution to the problem ✅

- a solution of indifinite bloquage of low priority processes is **aging**
- gradually increasing the priority of processes that wait in the system for a long time

### RR (Round Robin Scheduling) ⏲️

- Round Robin algorithm is specifically designed for time sharing systems
- It is similar to FCFS scheduling but premption is added to switch between processes
- A small unit of time called time quantum or time slice is defined (_generally from 10 to 100 ms_)
- we keep the ready queue as FIFO queue
- different scenarios
  - CPU burst < 1 time quantum :
    - the process will release the cpu voluntarily
    - the cpu scheduler will go to the next ready process in the ready queue
  - CPU burst > 1 time quantum :
    - interruption of the os
    - a context switch will be executed and the process will be put at the end of the tail
    - cpu scheduler will select the next process in the ready queue

time quantum is at 4ms

| Process | Burst Time (ms) | TAT         | AWT         |
| ------- | --------------- | ----------- | ----------- |
| P1      | 24              | 30 - 0 = 30 | 30 - 24 = 6 |
| P2      | 3               | 7 - 0 = 7   | 7 - 3 = 4   |
| P3      | 3               | 10 - 0 = 10 | 10 - 3 = 7  |

**P1**--P2-P3-**P1--P1--P1--P1--P1**
|**----**|---|---|**----|----|----|----|----**|
0 4 7 **10 14 18 22 26 30**
**Turn around time = completion time - arrival time**
**Wait time = tat - burst time **
