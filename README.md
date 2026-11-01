# Priority Scheduling and Priority Donation in Pintos

An individual Operating Systems project implementing priority-based thread scheduling and lock priority donation in Pintos.
Individual project by Jiya Bajaj, built on the JHU Pintos distribution.

**Author — Jiya Bajaj**  
**Registration number — 24BKT0066**  
**Department — Department of Computer Sciences**

## Project Overview

This project extends the Pintos thread subsystem so that the highest-priority runnable thread receives the processor. It also updates synchronization behavior to account for thread priorities when waking blocked threads.

Priority donation addresses the situation where a high-priority thread waits for a lock held by a lower-priority thread. The implementation temporarily raises the lock holder’s effective priority and recalculates it when lock ownership changes.

## Implemented Features

- Highest-effective-priority selection from the ready queue.
- FIFO ordering among ready threads with equal priorities.
- Preemption at safe scheduling points.
- Separate base and effective thread priorities.
- Priority donation from multiple waiting threads.
- Nested donation through chains of lock dependencies.
- Priority restoration after lock release.
- Priority-aware semaphore and condition-variable wakeups.

The implementation scans current priorities when selecting threads. Donation is recalculated from base priorities and propagated through lock dependencies until priorities stop changing.

## Main Source Changes

| File | Changes |
|---|---|
| `src/threads/thread.c` | Ready-thread selection, preemption checks and priority recalculation |
| `src/threads/thread.h` | Thread state for base priority and lock dependencies |
| `src/threads/synch.c` | Lock donation and priority-aware synchronization wakeups |
| `src/Make.config` | Compiler compatibility flags |

The compatibility adjustment adds `-fno-pie` and `-fno-pic`. The baseline used for testing includes this adjustment while retaining the original scheduling behavior.

## Development Environment

| Component | Version or platform |
|---|---|
| Host operating system | Windows 11 |
| Development environment | Ubuntu 24.04 through WSL2 |
| Compiler | GCC 13.3.0 |
| Emulator | QEMU 8.2.2 |
| Kernel architecture | 32-bit x86 |
| Project branch | `priority-scheduling` |

## Build and Run

The commands below assume the development tools are installed. Run them from the repository root.

```bash
make -C src/utils
export PATH="$PWD/src/utils:$PATH"

make -C src/threads
cd src/threads/build

pintos --qemu -- -q run alarm-zero
```

To run a priority test through its checker from the build directory:

```bash
make tests/threads/priority-preempt.result
cat tests/threads/priority-preempt.result
```

Make can reuse existing test outputs. Use a fresh build when checking results after code changes.

## Recorded Test Results

The saved project results cover twelve priority tests and six alarm regression tests.

| Test group | Baseline | Modified kernel |
|---|---|---|
| Priority scheduling and donation | 0/12 passed | 12/12 passed |
| Alarm regression tests | 6/6 passed | 6/6 passed |
| Total selected tests | 6/18 passed | 18/18 passed |

The priority tests cover preemption, priority changes, FIFO ties, semaphore and condition-variable ordering, single and multiple donations, nested donation, donation chains, and priority restoration.

These results apply to the selected tests. MLFQS is outside the project scope.

## Design Limitations

Ready-thread selection and priority-aware wakeups scan their queues. Donation recalculation repeatedly scans live threads with interrupts disabled, so larger workloads and longer dependency chains may increase interrupt latency.

Strict priority scheduling can starve lower-priority threads. Priority donation does not prevent deadlock. The implementation targets the single-CPU Pintos environment.

## Upstream Source and Acknowledgements

This project builds on the JHU Pintos distribution. Pintos was originally developed by Ben Pfaff and other contributors at Stanford University, with adaptations for the JHU course.

My project changes focus on scheduling, synchronization and compiler compatibility. The repository retains the upstream code and commit history.

- [JHU Pintos source distribution](https://github.com/jhu-cs318/pintos)
- [Stanford Pintos thread-project documentation](https://www.scs.stanford.edu/10wi-cs140/pintos/pintos_2.html)
- [GCC 13.3 code-generation options](https://gcc.gnu.org/onlinedocs/gcc-13.3.0/gcc/Code-Gen-Options.html)

Refer to the license notices included in the source distribution for licensing terms.
