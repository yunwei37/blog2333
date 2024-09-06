---
title: 'Using `setjmp`, `longjmp`, and Signals to Catch Page Faults and Measure Performance'

date: 2024-05-30T08:41:06-08:00
tags: Notes
---


In C, catching a page fault using signals like `SIGSEGV` (Segmentation Fault) is a powerful technique, especially when combined with `setjmp` and `longjmp` for error recovery. These functions allow us to implement non-local jumps in the program, saving and restoring the state of the execution context.

This approach is particularly useful when a segmentation fault occurs, and you want to avoid a complete program crash. In this blog, I’ll explain how `setjmp` and `longjmp` work, how they can be used with signal handling to catch page faults, and I'll include some benchmarking code to measure the performance of these functions.

#### How `setjmp` and `longjmp` Work

- **`setjmp`**: Saves the current program state, including registers, program counter, and stack pointer, into a `jmp_buf` buffer. When called, it returns 0.
- **`longjmp`**: Jumps back to the state saved by `setjmp`. When it jumps back, `setjmp` will return the value passed to `longjmp` instead of 0.

The combination of these functions allows a program to handle errors and recover from certain kinds of faults, such as illegal memory access or page faults.

#### Example Code: Using `SIGSEGV` to Catch Page Faults

Here’s a basic example of using `SIGSEGV` to catch a page fault and recover the program by using `setjmp` and `longjmp`.

```c
#include <stdio.h>
#include <setjmp.h>
#include <signal.h>

jmp_buf buf;

void sigsegv_handler(int signum) {
    printf("Caught SIGSEGV, returning to safe point\n");
    longjmp(buf, 1);  // Jump back to checkpoint
}

int main() {
    signal(SIGSEGV, sigsegv_handler);

    if (setjmp(buf) == 0) {
        // Set a checkpoint
        printf("Setting checkpoint\n");

        // Cause a segmentation fault
        int *p = (int *)0xdeadbeef;
        *p = 42;
    } else {
        // We are back here after the fault
        printf("Recovered from segmentation fault!\n");
    }

    return 0;
}
```

**Explanation**:
- We use `signal(SIGSEGV, sigsegv_handler)` to register the signal handler.
- In the signal handler, we use `longjmp(buf, 1)` to jump back to the saved state.
- The program deliberately causes a segmentation fault by accessing invalid memory, but instead of crashing, the handler recovers by jumping back to the checkpoint set by `setjmp`.

#### Benchmarking the Performance of `setjmp` and `longjmp`

To understand the cost of using `setjmp` and `longjmp`, we can measure their performance over multiple iterations. Below is a benchmarking example that tests both `setjmp/longjmp` and `setjmp` alone.

```c
#include <stdio.h>
#include <setjmp.h>
#include <time.h>
#include <signal.h>

jmp_buf buf;

void benchmark_setjmp(long iterations) {
    struct timespec start, end;
    double elapsed_time;

    // Benchmark setjmp/longjmp
    clock_gettime(CLOCK_MONOTONIC, &start);
    for (long i = 0; i < iterations; i++) {
        if (setjmp(buf) == 0) {
            longjmp(buf, 1);  // Return to checkpoint
        }
    }
    clock_gettime(CLOCK_MONOTONIC, &end);
    elapsed_time = (end.tv_sec - start.tv_sec) * 1e9 + (end.tv_nsec - start.tv_nsec);
    printf("Benchmarking setjmp/longjmp over %ld iterations, each 1 took %.3f ns\n", iterations, elapsed_time / iterations);

    // Benchmark setjmp alone
    clock_gettime(CLOCK_MONOTONIC, &start);
    for (long i = 0; i < iterations; i++) {
        setjmp(buf);
    }
    clock_gettime(CLOCK_MONOTONIC, &end);
    elapsed_time = (end.tv_sec - start.tv_sec) * 1e9 + (end.tv_nsec - start.tv_nsec);
    printf("Benchmarking setjmp over %ld iterations, each 1 took %.3f ns\n", iterations, elapsed_time / iterations);
}

int main() {
    long iterations = 1000000;
    benchmark_setjmp(iterations);
    return 0;
}
```

**Performance Results**:
After running this code with 1 million iterations, here are the results:

```
Benchmarking setjmp/longjmp over 1000000 iterations, each 1 took 12.293 ns
Benchmarking setjmp over 1000000 iterations, each 1 took 4.478 ns
```

This shows that while `longjmp` adds overhead, the performance is still acceptable for many use cases where non-local jumps are required for error handling.

#### Practical Considerations

Using `setjmp` and `longjmp` comes with caveats:
1. **Signal Safety**: These functions should be used carefully within signal handlers. Specifically, non-local jumps can complicate the flow of the program and leave resources in an inconsistent state.
2. **Portability**: Behavior might vary slightly between platforms. On some systems, `sigsetjmp` and `siglongjmp` are preferable for more precise control over signal masks【16†source】【17†source】.

#### Conclusion

By combining `setjmp`, `longjmp`, and signals, you can catch and recover from page faults in your C programs. While `setjmp/longjmp` may add some overhead, they provide a powerful mechanism for handling errors gracefully without crashing the program. If you need to benchmark or optimize this approach, the above code can be a great starting point to understand the performance trade-offs.
