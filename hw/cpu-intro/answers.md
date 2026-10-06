# OSTEP Ch.4 Homework Answers

## Q1
- Prediction / 预测:Total time: 10 ticks
CPU utilization: 100%

Time   PID 0   PID 1   CPU   IOs
1      RUN     READY   1
2      RUN     READY   1
3      RUN     READY   1
4      RUN     READY   1
5      RUN     READY   1
6      DONE    RUN     1
7      DONE    RUN     1
8      DONE    RUN     1
9      DONE    RUN     1
10     DONE    RUN     1
- Reasoning / 理由:两个进程都有 5 条 100% 的 CPU 指令。PID 0 先占用 CPU 5 个 tick 并结束，然后 PID 1 接着使用 CPU 5 个 tick。因为没有任何 I/O 操作，所以 CPU 不会空闲，总时间为 5+5=10。
- Verified result / 验证结果:
- Analysis / 分析:

## Q2
- Prediction / 预测:Total time: 11 ticks
  CPU utilization: 54.55%
  Time PID 0 PID 1 CPU IOs
1 RUN READY 1
2 RUN READY 1
3 RUN READY 1
4 RUN READY 1
5 DONE RUN 1
6 DONE BLOCKED 1
7 DONE BLOCKED 1
8 DONE BLOCKED 1
9 DONE BLOCKED 1
10 DONE BLOCKED 1
11 DONE RUN 1
- Reasoning / 理由:PID 0 有 4 条 100% 的 CPU 指令，它先连续运行 4 个 tick 然后结束。PID 1 只有 1 条 I/O 指令，它在等 PID 0 结束后才开始。在 tick 5 发起 I/O，随后进入 5 个 tick 的 BLOCKED 状态等待 I/O 完成，最后再花 1 个 tick 完成 I/O。因为 PID 1 等待时只有它一个进程，CPU 只能空闲（IDLE），所以总时间是 4 + 1 + 5 + 1 = 11 tick。CPU 忙碌了 6 个 tick，利用率是 6/11 ≈ 54.55%。
- Verified result / 验证结果:
- Analysis / 分析:

## Q3
- Prediction / 预测:Total time: 7 ticks
  CPU utilization: 85.71%
Time PID 0 PID 1 CPU IOs
1 RUN:io READY 1 1
2 BLOCKED RUN 1 1
3 BLOCKED RUN 1 1
4 BLOCKED RUN 1 1
5 BLOCKED RUN 1 1
6 BLOCKED DONE 1
7 RUN:io_done DONE 1
- Reasoning / 理由:Q3 与 Q2 的进程顺序相反。PID 0 先运行并立即发起 I/O，占用 1 个 tick，随后进入 5 个 tick 的 BLOCKED 状态。在 PID 0 等待期间，CPU 并没有空闲，而是切换给 PID 1 运行。PID 1 的 4 个 CPU 指令在第 2 到第 5 个 tick 内完成。第 6 个 tick 时，PID 0 仍在等待 I/O，CPU 空闲。第 7 个 tick 时，I/O 完成，PID 0 运行 io_done 处理完成。总时间 = 1(发起) + 4(PID1) + 1(空闲) + 1(完成) = 7 tick。CPU 忙碌了 6 个 tick，利用率 = 6/7 ≈ 85.71%。
- Verified result / 验证结果:
- Analysis / 分析:

## Q4
- Prediction / 预测:Total time: 11 ticks
  CPU utilization: 54.55%
Time PID 0 PID 1 CPU IOs
1 RUN:io READY 1 1
2 BLOCKED READY 1
3 BLOCKED READY 1
4 BLOCKED READY 1
5 BLOCKED READY 1
6 BLOCKED READY 1
7 RUN:io_done READY 1
8 DONE RUN 1
9 DONE RUN 1
10 DONE RUN 1
11 DONE RUN 1
- Reasoning / 理由:因为加了 `-S SWITCH_ON_END`，操作系统只有在进程**完全结束**时才会切换进程。PID 0 发起 I/O 后进入 5 个 tick 的 BLOCKED 状态，此时虽然 PID 1 处于 READY 状态，但系统不允许切换，CPU 只能空闲（IDLE）干等。直到第 7 个 tick，PID 0 完成 I/O 彻底结束，PID 1 才能开始运行。总时间 = 7（PID 0 全程） + 4（PID 1） = 11 tick。CPU 忙碌了 1(发起) + 1(完成) + 4(PID1) = 6 tick，利用率 = 6/11 ≈ 54.55%。
- Verified result / 验证结果:
- Analysis / 分析:

## Q5
- Prediction / 预测:Total time: 7 ticks
  CPU utilization: 85.71%
Time PID 0 PID 1 CPU IOs
1 RUN:io READY 1 1
2 BLOCKED RUN 1 1
3 BLOCKED RUN 1 1
4 BLOCKED RUN 1 1
5 BLOCKED RUN 1 1
6 BLOCKED DONE 1
7 RUN:io_done DONE 1
- Reasoning / 理由:因为 `SWITCH_ON_IO` 是默认的调度策略，所以 Q5 的结果与 Q3 完全一致。当 PID 0 发起 I/O 并进入阻塞状态时，系统允许切换到处于就绪状态的 PID 1，让 CPU 继续工作，避免了空闲。将 Q5 与 Q4 对比可以看出：如果采用 `SWITCH_ON_END`（等进程结束才切换），总时间会膨胀到 11 tick，CPU 利用率只有 54.55%；而采用 `SWITCH_ON_IO`（I/O 阻塞时就切换），总时间只需 7 tick，CPU 利用率提升至 85.71%。
- Verified result / 验证结果:
- Analysis / 分析:

## Q6
- Prediction / 预测: Total time: 31 ticks
  CPU utilization: 67.74%
Time PID 0 PID 1 PID 2 PID 3 CPU IOs
1 RUN:io READY READY READY 1 1
2 BLOCKED RUN READY READY 1 1
3 BLOCKED RUN READY READY 1 1
4 BLOCKED RUN READY READY 1 1
5 BLOCKED RUN READY READY 1 1
6 BLOCKED RUN READY READY 1 1
7 READY DONE RUN READY 1
8 READY DONE RUN READY 1
9 READY DONE RUN READY 1
10 READY DONE RUN READY 1
11 READY DONE RUN READY 1
12 READY DONE DONE RUN 1
13 READY DONE DONE RUN 1
14 READY DONE DONE RUN 1
15 READY DONE DONE RUN 1
16 READY DONE DONE RUN 1
17 RUN:io_done DONE DONE DONE 1
18 RUN:io DONE DONE DONE 1 1
19 BLOCKED DONE DONE DONE 1
20 BLOCKED DONE DONE DONE 1
21 BLOCKED DONE DONE DONE 1
22 BLOCKED DONE DONE DONE 1
23 BLOCKED DONE DONE DONE 1
24 RUN:io_done DONE DONE DONE 1
25 RUN:io DONE DONE DONE 1 1
26 BLOCKED DONE DONE DONE 1
27 BLOCKED DONE DONE DONE 1
28 BLOCKED DONE DONE DONE 1
29 BLOCKED DONE DONE DONE 1
30 BLOCKED DONE DONE DONE 1
31 RUN:io_done DONE DONE DONE 1
- Reasoning / 理由:因为设置了 `-I IO_RUN_LATER`，当 PID 0 完成 I/O 时，它不会立即抢占 CPU，而是排到就绪队列末尾。所以在 tick 1 时 PID 0 发起 I/O 并阻塞，tick 2-6 设备工作期间，CPU 被 PID 1 占用。tick 7 时 PID 0 的 I/O 完成，但此时 CPU 正在被 PID 2 占用，PID 0 只能 READY 排队。这导致 I/O 设备在 tick 7-16 期间完全空闲！直到 PID 1、2、3 都运行结束，PID 0 才在 tick 17-31 恢复执行后续的 I/O 指令。由于 I/O 设备被闲置了 10 个 tick，总时间被拉长到 31 tick。CPU 忙碌了 21 个 tick，利用率 = 21/31 ≈ 67.74%。
- Verified result / 验证结果:
- Analysis / 分析:

## Q7
- Prediction / 预测:Total time: 21 ticks
  CPU utilization: 100%
Time PID 0 PID 1 PID 2 PID 3 CPU IOs
1 RUN:io READY READY READY 1 1
2 BLOCKED RUN READY READY 1 1
3 BLOCKED RUN READY READY 1 1
4 BLOCKED RUN READY READY 1 1
5 BLOCKED RUN READY READY 1 1
6 BLOCKED RUN READY READY 1 1
7 RUN:io_done DONE READY READY 1
8 RUN:io DONE READY READY 1 1
9 BLOCKED DONE RUN READY 1 1
10 BLOCKED DONE RUN READY 1 1
11 BLOCKED DONE RUN READY 1 1
12 BLOCKED DONE RUN READY 1 1
13 BLOCKED DONE RUN READY 1 1
14 RUN:io_done DONE DONE READY 1
15 RUN:io DONE DONE READY 1 1
16 BLOCKED DONE DONE RUN 1 1
17 BLOCKED DONE DONE RUN 1 1
18 BLOCKED DONE DONE RUN 1 1
19 BLOCKED DONE DONE RUN 1 1
20 BLOCKED DONE DONE RUN 1 1
21 RUN:io_done DONE DONE DONE 1
- Reasoning / 理由:因为设置了 `-I IO_RUN_IMMEDIATE`，当 PID 0 的 I/O 完成时，它会**立刻抢占 CPU** 执行 `io_done` 并马上发起下一个 I/O。这使得 I/O 设备几乎不停歇。在 tick 1 时 PID 0 发起 I/O 并阻塞，tick 2-6 设备工作期间，CPU 给 PID 1 跑。tick 7 时 PID 0 的 I/O 完成，立刻抢占 CPU 执行 `io_done`，tick 8 继续发起第二个 I/O。这种策略让 I/O 设备得以连续工作（I/O 忙碌时间 15 ticks），CPU 也被完美利用（没有任何空闲 tick）。总时间从 Q6 的 31 tick 大幅缩短为 21 tick。CPU 忙碌了 21 个 tick，利用率为 21/21 = 100%。
- Verified result / 验证结果:
- Analysis / 分析:

## Q8
- Prediction / 预测:Total time: 11 ticks
  CPU utilization: 63.64%
Time PID 0 PID 1 CPU IOs
1 RUN:cpu READY 1
2 RUN:io READY 1 1
3 BLOCKED RUN:io 1 2
4 BLOCKED BLOCKED 2
5 BLOCKED BLOCKED 2
6 BLOCKED BLOCKED 2
7 BLOCKED BLOCKED 2
8 RUN:io_done BLOCKED 1 1
9 READY RUN:io_done 1
10 RUN:cpu RUN:cpu 1
11 DONE DONE
- Reasoning / 理由:基于种子 1 的指令列表：PID 0 为 [填你终端看到的指令，如 cpu, io, cpu]，PID 1 为 [填你终端看到的指令，如 io, cpu, io]。
一次完整的 I/O 耗时 7 个 tick（1 tick 发起 + 5 tick 阻塞 + 1 tick 完成）。因为只有单 CPU，当两个进程都在等待 I/O（Tick 4-7）时，CPU 会空闲。总时间为 11 tick，CPU 忙碌了 7 个 tick，利用率 = 7/11 ≈ 63.64%。
- Verified result / 验证结果:
- Analysis / 分析: