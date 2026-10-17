# 结果概述
## Q1
- 结果:
```
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 200,200,200 -p FIFO
  Job   0 -- Response: 0.00  Turnaround 200.00  Wait 0.00
  Job   1 -- Response: 200.00  Turnaround 400.00  Wait 200.00
  Job   2 -- Response: 400.00  Turnaround 600.00  Wait 400.00
  Average -- Response: 200.00  Turnaround 400.00  Wait 200.00

python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 200,200,200 -p SJF
  Job   0 -- Response: 0.00  Turnaround 200.00  Wait 0.00
  Job   1 -- Response: 200.00  Turnaround 400.00  Wait 200.00
  Job   2 -- Response: 400.00  Turnaround 600.00  Wait 400.00
  Average -- Response: 200.00  Turnaround 400.00  Wait 200.00
```

## Q2
- 结果:
```
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p FIFO
  Job   0 -- Response: 0.00  Turnaround 100.00  Wait 0.00
  Job   1 -- Response: 100.00  Turnaround 300.00  Wait 100.00
  Job   2 -- Response: 300.00  Turnaround 600.00  Wait 300.00
  Average -- Response: 133.33  Turnaround 333.33  Wait 133.33

python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 300,200,100 -p FIFO
  Job   0 -- Response: 0.00  Turnaround 300.00  Wait 0.00
  Job   1 -- Response: 300.00  Turnaround 500.00  Wait 300.00
  Job   2 -- Response: 500.00  Turnaround 600.00  Wait 500.00
  Average -- Response: 266.67  Turnaround 466.67  Wait 266.67

python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p SJF
  Job   0 -- Response: 0.00  Turnaround 100.00  Wait 0.00
  Job   1 -- Response: 100.00  Turnaround 300.00  Wait 100.00
  Job   2 -- Response: 300.00  Turnaround 600.00  Wait 300.00
  Average -- Response: 133.33  Turnaround 333.33  Wait 133.33

python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 300,200,100 -p SJF
  Job   2 -- Response: 0.00  Turnaround 100.00  Wait 0.00
  Job   1 -- Response: 100.00  Turnaround 300.00  Wait 100.00
  Job   0 -- Response: 300.00  Turnaround 600.00  Wait 300.00
  Average -- Response: 133.33  Turnaround 333.33  Wait 133.33
```

## Q3
- 结果:
```
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p RR -q 1 
  Job   0 -- Response: 0.00  Turnaround 298.00  Wait 198.00
  Job   1 -- Response: 1.00  Turnaround 499.00  Wait 299.00
  Job   2 -- Response: 2.00  Turnaround 600.00  Wait 300.00
  Average -- Response: 1.00  Turnaround 465.67  Wait 265.67
```

## Q4
- 结果:
```
SJF 与 FIFO 周转时间相同，当且仅当 FIFO 的执行顺序恰好就是“按运行时间从小到大”的顺序。
```

## Q5
- 结果:
```
当RR调度的时间片长度不小于作业集中最长的运行时间，且作业按运行时间递增的顺序到达时，RR的调度行为将完全等同于SJF，从而使得两者的响应时间一致。
```

## Q6
- 结果:
```
由于SJF总是优先处理短作业，较长的作业往往需要等待更多短作业先执行完毕。因此，作业本身的运行时间越长，其排队等待的时间也越久，导致响应时间随作业长度线性上升。 
```

## Q7
- 结果:
```
在RR调度中，响应时间与时间片（量子）长度呈正相关。假设N个作业同时到达且每个作业至少需要一个时间片，则最坏情况下的响应时间可近似表示为R=(N−1)×Q ，其中N为就绪队列中的作业数，Q为时间片长度。
```
