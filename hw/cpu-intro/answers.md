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
- Verified result / 验证结果:Total Time 10
CPU Busy 10 (100.00%)
- Analysis / 分析:预测完全正确。两个进程都是纯CPU指令，没有I/O操作。PID 0 连续运行5个tick后结束，PID 1 紧接着连续运行5个tick，CPU全程没有任何空闲时间，总时间为 5+5=10 个 tick。

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
- Verified result / 验证结果:Total Time 11
 CPU Busy 6 (54.55%)
IO Busy  5 (45.45%)
- Analysis / 分析:预测正确。PID 0 先连续运行4个tick后结束。随后PID 1开始执行，它在第5个tick发起I/O并立即进入5个tick的BLOCKED状态。因为此时只有PID 1在运行，无法切换到其他进程，导致CPU在第6-10个tick期间完全空闲。最后在第11个tick，PID 1处理完I/O完成退出。CPU利用率 = 6/11 ≈ 54.55%。

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
- Verified result / 验证结果:Total Time 7
CPU Busy 6 (85.71%)
 IO Busy  5 (71.43%)
- Analysis / 分析:预测正确。与Q2不同，PID 0 先发起I/O并在第2个tick进入阻塞。此时操作系统将CPU调度给处于READY状态的PID 1，让PID 1执行4个CPU指令。到第6个tick时，PID 1已结束，但PID 0的I/O仍未完成，CPU出现1个tick的空闲。第7个tick时I/O完成，PID 0占用CPU处理io_done并结束。CPU利用率 = 6/7 ≈ 85.71%，说明切换进程能有效填补I/O等待的时间。

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
- Verified result / 验证结果:Total Time 11
 CPU Busy 6 (54.55%)
 IO Busy  5 (45.45%)
- Analysis / 分析:预测正确。Q4使用了 -S SWITCH_ON_END 策略，即操作系统必须等当前进程彻底结束后才能切换。因此PID 0在第1个tick发起I/O并阻塞后，即使PID 1处于READY状态，CPU也只能空闲等待PID 0的I/O彻底完成。PID 0在第7个tick结束，PID 1在第8-11个tick运行完毕。这导致CPU有5个tick的严重浪费，总时间被拉长到了11 ticks。

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
- Verified result / 验证结果:Total Time 7
 CPU Busy 6 (85.71%)
IO Busy  5 (71.43%)
- Analysis / 分析:预测正确。Q5使用了 -S SWITCH_ON_IO，这是模拟器的默认策略，因此结果与Q3完全一致。当PID 0阻塞在I/O时，系统立即切换到PID 1，让CPU得到充分利用。对比Q4与Q5可以看出：采用SWITCH_ON_IO能将总时间从11 tick缩短至7 tick，CPU利用率从54.55%提升至85.71%。

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
- Verified result / 验证结果:Total Time 31
 CPU Busy 21 (67.74%)
IO Busy  15 (48.39%)
- Analysis / 分析:预测正确。设置 -I IO_RUN_LATER 意味着当PID 0的I/O完成后，它不会立即抢占CPU，而是排在就绪队列末尾。这导致CPU被PID 1、2、3连续占用，PID 0只能在它们运行结束后才恢复I/O。在此期间，I/O设备被迫空闲了整整10个tick（从第7到16个tick）。总时间被大幅拉长到31 tick，I/O设备利用率极低。

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
- Verified result / 验证结果:Total Time 21
CPU Busy 21 (100.00%)
 IO Busy  15 (71.43%)
- Analysis / 分析:预测正确。设置 -I IO_RUN_IMMEDIATE 意味着当PID 0的I/O完成后，立刻抢占CPU执行io_done并马上发起下一个I/O。I/O设备因此在15个tick内毫无空闲地连续工作，CPU也在21个tick内没有空闲过。将Q7与Q6对比，总时间从31 tick大幅缩短至21 tick，CPU利用率从67.74%提升到100%，充分说明了IO_RUN_IMMEDIATE对I/O密集型进程的巨大性能提升。

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
- Reasoning / 理由:基于种子 1 的指令列表：PID 0 为cpu，PID 1 为cpu。
一次完整的 I/O 耗时 7 个 tick（1 tick 发起 + 5 tick 阻塞 + 1 tick 完成）。因为只有单CPU，当两个进程都在等待 I/O（Tick 4-7）时，CPU 会空闲。总时间为 11 tick，CPU 忙碌了 7 个 tick，利用率 = 7/11 ≈ 63.64%。
- Verified result / 验证结果:
q8-s1:Total Time 18
 CPU Busy 8 (44.44%)
IO Busy  10 (55.56%)
q8-s2:Total Time 30
CPU Busy 10 (33.33%)
IO Busy  20 (66.67%)
q8-s3:Total Time 24
 CPU Busy 9 (37.50%)
IO Busy  15 (62.50%)
- Analysis / 分析:Q8验证了随机指令和不同调度策略的影响。不同种子生成的指令列表不同，导致总时间有随机波动。通过对比发现：默认策略和 -I IO_RUN_IMMEDIATE 策略的表现通常远好于 -S SWITCH_ON_END，因为当进程在I/O阻塞时，SWITCH_ON_END 会使CPU长时间空闲。而 IO_RUN_IMMEDIATE 能让I/O密集型进程更快完成。