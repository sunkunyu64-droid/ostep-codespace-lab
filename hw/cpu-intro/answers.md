# OSTEP Ch.4 Homework Answers

## Q1
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5        RUN:cpu         READY             1          
  6           DONE       RUN:cpu             1          
  7           DONE       RUN:cpu             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1          

Stats: Total Time 10
Stats: CPU Busy 10 (100.00%)
Stats: IO Busy  0 (0.00%)
```
- Reasoning / 理由:
```
在使用 -l 5:100,5:100 创建的两个进程中，由于指令类型被设定为100%的CPU运算，排除了I/O等待带来的空闲时间，使得CPU能够持续不间断地执行指令，从而保持100%的利用率。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
因为两个进程只包含 CPU 指令，完全不涉及 I/O 操作，所以系统中没有任何会导致 CPU 空闲的等待环节。CPU 始终保持忙碌，利用率自然达到 100%。
```

## Q2
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5           DONE        RUN:io             1          
  6           DONE       BLOCKED                           1
  7           DONE       BLOCKED                           1
  8           DONE       BLOCKED                           1
  9           DONE       BLOCKED                           1
 10           DONE       BLOCKED                           1
 11*          DONE   RUN:io_done             1          

Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
```
- Reasoning / 理由:
```
完成这两个进程共需 11 个时间单位，执行过程可分为三个阶段：
1. CPU 计算阶段：PID 0 率先运行，连续执行 4 条 CPU 指令，耗时 4 个时间单位。
2. I/O 等待阶段：PID 0 结束后，PID 1 发出 I/O 请求并进入阻塞状态，此时 CPU 因无就绪进程而空闲。
3. 结束阶段：I/O 完成后，PID 1 执行 RUN:io_done 指令，进程正式结束。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
整个过程耗时 11 个时间单位。关键在于，当 PID 1 发起 I/O 请求后，由于没有其他进程可以接力执行，CPU 只能被动等待 I/O 完成。这段无法被利用的空闲时间，直接导致了 CPU 整体利用率的下降。
```

## Q3
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          

Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
```
- Reasoning / 理由:
```
交换顺序重要
命令 -l 1:0,4:100 的执行过程体现了 CPU 与 I/O 的重叠工作，总耗时仅为 7：
1. I/O 启动：PID 0 首先执行并发起 I/O 请求，进入阻塞状态。
2. CPU 并行计算：在 PID 0 等待 I/O 期间，CPU 并未闲置，而是立即切换去执行 PID 1 的 4 条指令。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
进程的执行顺序至关重要。若能合理安排 I/O 密集型进程与 CPU 密集型进程交替运行，就能在 I/O 等待期间让 CPU 继续处理其他任务，从而提高资源利用率并缩短总耗时。
```

## Q4
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7*   RUN:io_done         READY             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1          
 11           DONE       RUN:cpu             1          

Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
```
- Reasoning / 理由:
```
使用 -S SWITCH_ON_END 参数后，进程执行顺序如下：
1. PID 0 独占：PID 0 开始执行，即使中途发起 I/O 请求，CPU 也不会切换上下文。
2. CPU 闲置：在 PID 0 等待 I/O 完成期间，CPU 保持空闲，不执行 PID 1 的指令。
3. 顺序执行：直到 PID 0 彻底结束，PID 1 才会被调度并开始执行。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
SWITCH_ON_END 的核心逻辑是 不结束不切换 。因此，即使当前进程正在等待 I/O 完成，CPU 也不会转去执行其他进程，而是被迫空闲。这种机制使得 I/O 等待时间无法被利用，直接造成了 CPU 资源的浪费和系统效率的下降。
```

## Q5
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1          

Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%) 
  ```
- Reasoning / 理由:
```
使用 -S SWITCH_ON_IO 参数后，进程执行逻辑如下：
1. 触发切换：当前进程发出 I/O 请求。
2. 状态变更：该进程进入等待状态，CPU 控制权被释放。
3. 并行工作：操作系统立即调度另一个就绪进程占用 CPU。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
采用 SWITCH_ON_IO 后，CPU 利用率和总运行时间均得到优化。这是因为该策略遵循‘I/O 阻塞即切换’的原则：一旦进程因 I/O 阻塞，CPU 立刻转去执行其他就绪任务，而不是被动等待 I/O 结束。这种机制最大限度地减少了 CPU 的空闲时间。
```

## Q6
- Prediction / 预测:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*         READY          DONE       RUN:cpu         READY             1          
  8          READY          DONE       RUN:cpu         READY             1          
  9          READY          DONE       RUN:cpu         READY             1          
 10          READY          DONE       RUN:cpu         READY             1          
 11          READY          DONE       RUN:cpu         READY             1          
 12          READY          DONE          DONE       RUN:cpu             1          
 13          READY          DONE          DONE       RUN:cpu             1          
 14          READY          DONE          DONE       RUN:cpu             1          
 15          READY          DONE          DONE       RUN:cpu             1          
 16          READY          DONE          DONE       RUN:cpu             1          
 17    RUN:io_done          DONE          DONE          DONE             1          
 18         RUN:io          DONE          DONE          DONE             1          
 19        BLOCKED          DONE          DONE          DONE                           1
 20        BLOCKED          DONE          DONE          DONE                           1
 21        BLOCKED          DONE          DONE          DONE                           1
 22        BLOCKED          DONE          DONE          DONE                           1
 23        BLOCKED          DONE          DONE          DONE                           1
 24*   RUN:io_done          DONE          DONE          DONE             1          
 25         RUN:io          DONE          DONE          DONE             1          
 26        BLOCKED          DONE          DONE          DONE                           1
 27        BLOCKED          DONE          DONE          DONE                           1
 28        BLOCKED          DONE          DONE          DONE                           1
 29        BLOCKED          DONE          DONE          DONE                           1
 30        BLOCKED          DONE          DONE          DONE                           1
 31*   RUN:io_done          DONE          DONE          DONE             1          

Stats: Total Time 31
Stats: CPU Busy 21 (67.74%)
Stats: IO Busy  15 (48.39%)
```
- Reasoning / 理由:
```
该组合策略的执行逻辑如下：
1. I/O 触发切换（SWITCH_ON_IO）：进程发出 I/O 请求后阻塞，CPU 立即切换至其他就绪进程，实现计算与 I/O 重叠。
2. I/O 完成不抢占（IO_RUN_LATER）：I/O 结束后，原进程仅进入就绪队列，不强制中断当前进程。
3. 等待调度：原进程需排队等待，直到当前进程运行结束或主动释放 CPU，才能再次被调度执行。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
执行过程显示，三个 CPU 密集型进程率先完成，而 PID 0 的 I/O 完成后却未能立即抢占 CPU。受 IO_RUN_LATER 机制影响，PID 0 被延迟调度，错失了连续发起 I/O 请求的最佳时机，导致 CPU 与 I/O 无法有效并行。结果是系统出现了‘CPU 等待 I/O’的空闲窗口，整体资源利用不充分，CPU 利用率低至 67.74%，约有 1/3 的时间被浪费。
```

## Q7
- Prediction / 预测:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*   RUN:io_done          DONE         READY         READY             1          
  8         RUN:io          DONE         READY         READY             1          
  9        BLOCKED          DONE       RUN:cpu         READY             1             1
 10        BLOCKED          DONE       RUN:cpu         READY             1             1
 11        BLOCKED          DONE       RUN:cpu         READY             1             1
 12        BLOCKED          DONE       RUN:cpu         READY             1             1
 13        BLOCKED          DONE       RUN:cpu         READY             1             1
 14*   RUN:io_done          DONE          DONE         READY             1          
 15         RUN:io          DONE          DONE         READY             1          
 16        BLOCKED          DONE          DONE       RUN:cpu             1             1
 17        BLOCKED          DONE          DONE       RUN:cpu             1             1
 18        BLOCKED          DONE          DONE       RUN:cpu             1             1
 19        BLOCKED          DONE          DONE       RUN:cpu             1             1
 20        BLOCKED          DONE          DONE       RUN:cpu             1             1
 21*   RUN:io_done          DONE          DONE          DONE             1          

Stats: Total Time 21
Stats: CPU Busy 21 (100.00%)
Stats: IO Busy  15 (71.43%)
```
- Reasoning / 理由:
```
1. 这种行为有什么不同？
IO_RUN_LATER 与 IO_RUN_IMMEDIATE 的核心区别在于 I/O 完成后的调度策略。采用 IO_RUN_LATER 时，I/O 完成的进程仅被置为就绪状态，CPU 继续执行当前进程，不触发抢占；而采用 IO_RUN_IMMEDIATE 时，I/O 完成的进程会立即抢占 CPU 获得执行权，当前正在运行的进程则被暂停并放回就绪队列。
2. 为什么立即运行刚完成 I/O 的进程是个好主意？
优先调度刚完成 I/O 的进程主要基于以下三点考量：
(1). 最大化 I/O 设备利用率：I/O 密集型进程往往需要连续发起多次 I/O 请求。立即恢复其执行能使其迅速发出下一条 I/O 指令，从而让 I/O 设备保持忙碌，实现 CPU 计算与 I/O 操作的高度重叠。
(2). 避免 CPU 资源闲置：若推迟该进程的执行，当其后续 I/O 请求被延迟发出时，可能导致其他 CPU 密集型进程执行完毕后，CPU 陷入无任务可执行的空闲状态。立即运行可有效规避此类“CPU 等待 I/O”的低效情况。
(3). 提升系统整体吞吐量：通过确保 I/O 密集型进程及时推进，系统能更有效地并行处理计算与 I/O 任务，从而显著缩短所有进程完成的总周转时间，提升整体吞吐量。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
IO_RUN_IMMEDIATE 的好处是‘即完即跑’：I/O 一结束，进程马上接着干，这样就能赶紧把下一个 I/O 任务发出去。这并不是让 CPU 算得更快了，而是通过更聪明的排队方式，让 CPU 和 I/O 设备都能尽量同时工作，少一点空转，多一点并行。
```

## Q8
- Prediction / 预测:
```
s1-1. python3 process-run.py -s 1 -l 3:50,3:50
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7        BLOCKED          DONE                           1
  8*   RUN:io_done          DONE             1          
  9         RUN:io          DONE             1          
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14        BLOCKED          DONE                           1
 15*   RUN:io_done          DONE             1          

Stats: Total Time 15
Stats: CPU Busy 8 (53.33%)
Stats: IO Busy  10 (66.67%) 

s1-2. python3 process-run.py -s 1 -l 3:50,3:50 -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7        BLOCKED          DONE                           1
  8*   RUN:io_done          DONE             1          
  9         RUN:io          DONE             1          
 10        BLOCKED          DONE                           1
 11        BLOCKED          DONE                           1
 12        BLOCKED          DONE                           1
 13        BLOCKED          DONE                           1
 14        BLOCKED          DONE                           1
 15*   RUN:io_done          DONE             1          

Stats: Total Time 15
Stats: CPU Busy 8 (53.33%)
Stats: IO Busy  10 (66.67%) 

s1-3. python3 process-run.py -s 1 -l 3:50,3:50 -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7        BLOCKED         READY                           1
  8*   RUN:io_done         READY             1          
  9         RUN:io         READY             1          
 10        BLOCKED         READY                           1
 11        BLOCKED         READY                           1
 12        BLOCKED         READY                           1
 13        BLOCKED         READY                           1
 14        BLOCKED         READY                           1
 15*   RUN:io_done         READY             1          
 16           DONE       RUN:cpu             1          
 17           DONE       RUN:cpu             1          
 18           DONE       RUN:cpu             1          

Stats: Total Time 18
Stats: CPU Busy 8 (44.44%)
Stats: IO Busy  10 (55.56%)

s2-1. python3 process-run.py -s 2 -l 3:50,3:50
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7*   RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9*       BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14*   RUN:io_done       BLOCKED             1             1
 15        RUN:cpu       BLOCKED             1             1
 16*          DONE   RUN:io_done             1          

Stats: Total Time 16
Stats: CPU Busy 10 (62.50%)
Stats: IO Busy  14 (87.50%)

s2-2. python3 process-run.py -s 2 -l 3:50,3:50 -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7*   RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9*       BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14*   RUN:io_done       BLOCKED             1             1
 15        RUN:cpu       BLOCKED             1             1
 16*          DONE   RUN:io_done             1          

Stats: Total Time 16
Stats: CPU Busy 10 (62.50%)
Stats: IO Busy  14 (87.50%)

s2-3. python3 process-run.py -s 2 -l 3:50,3:50 -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7*   RUN:io_done         READY             1          
  8         RUN:io         READY             1          
  9        BLOCKED         READY                           1
 10        BLOCKED         READY                           1
 11        BLOCKED         READY                           1
 12        BLOCKED         READY                           1
 13        BLOCKED         READY                           1
 14*   RUN:io_done         READY             1          
 15        RUN:cpu         READY             1          
 16           DONE       RUN:cpu             1          
 17           DONE        RUN:io             1          
 18           DONE       BLOCKED                           1
 19           DONE       BLOCKED                           1
 20           DONE       BLOCKED                           1
 21           DONE       BLOCKED                           1
 22           DONE       BLOCKED                           1
 23*          DONE   RUN:io_done             1          
 24           DONE        RUN:io             1          
 25           DONE       BLOCKED                           1
 26           DONE       BLOCKED                           1
 27           DONE       BLOCKED                           1
 28           DONE       BLOCKED                           1
 29           DONE       BLOCKED                           1
 30*          DONE   RUN:io_done             1          

Stats: Total Time 30
Stats: CPU Busy 10 (33.33%)
Stats: IO Busy  20 (66.67%)

s3-1. python3 process-run.py -s 3 -l 3:50,3:50
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7        BLOCKED       BLOCKED                           2
  8*   RUN:io_done       BLOCKED             1             1
  9*       RUN:cpu         READY             1          
 10           DONE   RUN:io_done             1          
 11           DONE        RUN:io             1          
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16           DONE       BLOCKED                           1
 17*          DONE   RUN:io_done             1          
 18           DONE       RUN:cpu             1          

Stats: Total Time 18
Stats: CPU Busy 9 (50.00%)
Stats: IO Busy  11 (61.11%)

s3-2. python3 process-run.py -s 3 -l 3:50,3:50 -I IO_RUN_IMMEDIATE
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7        BLOCKED       BLOCKED                           2
  8*   RUN:io_done       BLOCKED             1             1
  9*         READY   RUN:io_done             1          
 10          READY        RUN:io             1          
 11        RUN:cpu       BLOCKED             1             1
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16*          DONE   RUN:io_done             1          
 17           DONE       RUN:cpu             1          

Stats: Total Time 17
Stats: CPU Busy 9 (52.94%)
Stats: IO Busy  11 (64.71%)

s3-3. python3 process-run.py -s 3 -l 3:50,3:50 -S SWITCH_ON_END
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7        BLOCKED         READY                           1
  8*   RUN:io_done         READY             1          
  9        RUN:cpu         READY             1          
 10           DONE        RUN:io             1          
 11           DONE       BLOCKED                           1
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16*          DONE   RUN:io_done             1          
 17           DONE        RUN:io             1          
 18           DONE       BLOCKED                           1
 19           DONE       BLOCKED                           1
 20           DONE       BLOCKED                           1
 21           DONE       BLOCKED                           1
 22           DONE       BLOCKED                           1
 23*          DONE   RUN:io_done             1          
 24           DONE       RUN:cpu             1          

Stats: Total Time 24
Stats: CPU Busy 9 (37.50%)
Stats: IO Busy  15 (62.50%)
```
- Reasoning / 理由:
```
1. IO_RUN_IMMEDIATE 与 IO_RUN_LATER 的区别
IO_RUN_IMMEDIATE 与 IO_RUN_LATER 的核心差异在于是否触发抢占。前者在 I/O 完成时具有最高优先级，会立即抢占 CPU 运行原进程；后者则采取非抢占策略，仅将原进程置为就绪态，维持当前进程的运行。这种策略差异会导致同一组进程在执行轨迹、CPU 利用率及总周转时间上产生显著不同。
2. SWITCH_ON_IO 与 SWITCH_ON_END 的区别
SWITCH_ON_IO 与 SWITCH_ON_END 决定了 CPU 在进程阻塞时的行为。SWITCH_ON_IO 允许在进程发起 I/O 时立即进行上下文切换，实现计算与 I/O 的并行；而 SWITCH_ON_END 实行严格的串行执行，即使当前进程因 I/O 阻塞，CPU 也会保持空闲直到该进程彻底结束，这通常会导致系统资源的严重浪费。
```
- Verified result / 验证结果:
- Analysis / 分析:
```
对于随机生成的进程，SWITCH_ON_IO 的重要性往往更高，因为它解决了‘进程等待时 CPU 干什么’的问题，避免了资源浪费。IO_RUN_IMMEDIATE 则解决了‘I/O 做完后谁先跑’的问题。这两个设置一前一后，共同决定了 CPU 和 I/O 设备能多大程度上并行工作，直接影响系统效率。
```
