# OSTEP Ch.7 Homework Answers

## Q1
- Prediction / 预测:
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
- Reasoning / 理由:
```
由于三个作业的运行时间相同，FIFO 和 SJF 的调度顺序一致，因此它们的平均周转时间和平均响应时间也相同。
```
- Verified result / 验证结果:
- Analysis / 分析:

## Q2
- Prediction / 预测:
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
- Reasoning / 理由:
```
虽然在此特定排序下两种算法效果一致，但这只是特例。若将作业顺序调整为从长到短（300, 200, 100），FIFO必须等待长作业执行完毕才能处理短作业，导致整体效率下降；相比之下，SJF能优先处理短任务，因此在响应时间和周转时间上会更具优势。
```
- Verified result / 验证结果:
- Analysis / 分析:

## Q3
- Prediction / 预测:
```
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p RR -q 1 
  Job   0 -- Response: 0.00  Turnaround 298.00  Wait 198.00
  Job   1 -- Response: 1.00  Turnaround 499.00  Wait 299.00
  Job   2 -- Response: 2.00  Turnaround 600.00  Wait 300.00
  Average -- Response: 1.00  Turnaround 465.67  Wait 265.67
```
- Reasoning / 理由:
```
系统采用时间片轮转调度策略，时间片大小为1个单位。在此机制下，各作业交替执行，其响应时间依次为1、2、3；而各自的周转时间则分别为300、500和600。
```
- Verified result / 验证结果:
- Analysis / 分析:

## Q4
- Prediction / 预测:
```
SJF 与 FIFO 周转时间相同，当且仅当 FIFO 的执行顺序恰好就是“按运行时间从小到大”的顺序。
```
- Reasoning / 理由:
```
SJF 的优势在于"把短作业排到前面"；如果 FIFO 本来就按这个顺序执行，那 SJF 就没有额外优势了。
```
- Verified result / 验证结果:
- Analysis / 分析:

## Q5
- Prediction / 预测:
```
当RR调度的时间片长度不小于作业集中最长的运行时间，且作业按运行时间递增的顺序到达时，RR的调度行为将完全等同于SJF，从而使得两者的响应时间一致。
```
- Reasoning / 理由:
```
当RR的时间片足够大时，每个作业都能在分配的时间片内执行完毕，因此不会发生进程切换。在这种情况下，只要作业是按‘先短后长’的顺序到达的，RR的处理逻辑就和SJF一模一样。
```
- Verified result / 验证结果:
- Analysis / 分析:

## Q6
- Prediction / 预测:
```
python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p SJF
  Job   0 -- Response: 0.00  Turnaround 100.00  Wait 0.00
  Job   1 -- Response: 100.00  Turnaround 300.00  Wait 100.00
  Job   2 -- Response: 300.00  Turnaround 600.00  Wait 300.00
  Average -- Response: 133.33  Turnaround 333.33  Wait 133.33

python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 200,300,400 -p SJF
  Job   0 -- Response: 0.00  Turnaround 200.00  Wait 0.00
  Job   1 -- Response: 200.00  Turnaround 500.00  Wait 200.00
  Job   2 -- Response: 500.00  Turnaround 900.00  Wait 500.00
  Average -- Response: 233.33  Turnaround 533.33  Wait 233.33

python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 300,400,500 -p SJF
  Job   0 -- Response: 0.00  Turnaround 300.00  Wait 0.00
  Job   1 -- Response: 300.00  Turnaround 700.00  Wait 300.00
  Job   2 -- Response: 700.00  Turnaround 1200.00  Wait 700.00
  Average -- Response: 333.33  Turnaround 733.33  Wait 333.33

由于SJF总是优先处理短作业，较长的作业往往需要等待更多短作业先执行完毕。因此，作业本身的运行时间越长，其排队等待的时间也越久，导致响应时间随作业长度线性上升。
```
- Reasoning / 理由:
```
由于SJF是非抢占式的，作业一旦开始就不会被打断。在全部作业同时到达的情况下，系统会优先执行最短的任务。这就意味着第一个（最短的）作业可以立即运行（响应时间为0），而排在后面的作业必须等待前面所有更短的作业全部跑完，所以越靠后的作业，其响应时间就越大。
```
- Verified result / 验证结果:
- Analysis / 分析:

## Q7
- Prediction / 预测:
```
在RR调度中，响应时间与时间片（量子）长度呈正相关。假设N个作业同时到达且每个作业至少需要一个时间片，则最坏情况下的响应时间可近似表示为R=(N−1)×Q ，其中N为就绪队列中的作业数，Q为时间片长度。
```
- Reasoning / 理由:
```
假设所有作业在时刻0同时到达，且每个作业至少需要一个时间片。此时，最坏响应时间出现在就绪队列末尾的作业。以N=5、Q=10 为例，其最坏响应时间Rmax=(5−1)×10=40 。这表明该作业必须等待前4个作业各执行一个时间片后，才能首次获得CPU调度。
```
- Verified result / 验证结果:
- Analysis / 分析: