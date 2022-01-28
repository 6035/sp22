# Phase 5

Optimizations!

## Overview

In this final phase your goal is to reduce the execution time of your generated code while preserving the semantics of the given application. At the end of the class, we will hold the Compiler Derby where your compiler implementation will compete against the implementations of your fellow classmates on a hidden program (the derby program). 20% of the grade will be determined by your ranking in the derby.

The x86-64 architecture is very complex and aggressive. A substantial portion of this phase is to determine which optimizations will provide a benefit given the programs of the provided benchmark suite and the target architecture. Like in the previous phase, __you must implement at least one of the optimizations listed below__:

1. __Register Allocation__ -- Your compiler can implement a graph-coloring-based register allocator. See Chapter 16 of the Whale book and 9.7 of the Dragon book. Your register allocator should take advantage of the full set of general-purpose registers provided by the x86-64 ISA. It should also respect the Linux calling convention (e.g. caller-save/callee-save registers) when making external function calls.
1. __Instruction Selection and Scheduling__ -- So far, we have been using a restricted subset of the x86-64 ISA. As a peephole optimization, you might replace a sequence of simple instructions with a single, complex instruction that performs the same operation in fewer cycles. Instruction scheduling minimizes pipeline stalls for long latency operations such as loads, multiplies, and divides. See Chapter 17 of Whale book.
1. __Data Parallelization__ -- Computation executed in different iterations of a loop may be independent of each other. See the section on parallelization below.

In order to identify and prioritize optimizations, we have provided you with a benchmark suite of image-processing programs. These programs are more complex than the code that we provided during the previous phases, so your first priority is to produce correct unoptimized assembly code for each benchmark.

Once compiler produces correct unoptimized code, you should begin to study the applications of the benchmark suite. You will use these programs to determine which optimizations to implement and in what order to apply them. You are expected to analyze the assembly code produced by your compiler to classify the effectiveness of each proposed optimization, perhaps first applying the optimization manually and empirically measuring the benefit. Your writeup should include evidence for the effectiveness of each optimization you considered.

After implementing one of the required optimizations, you are free to implement optimizations not covered in class, including optimizations that you come across in your reading or come up with on your own. However, your writeup must demonstrate that each optimization is effective and semantics-preserving. Furthermore, you must argue that each optimization is general, meaning that it is not a special-case optimization that works only for the derby program or a specific application in the benchmark suite.

You should consult Intel's documentation for details regarding our target architecture. The documentation is linked from the class website. To benchmark your generated assembly code, provide the `-pg` option to `gcc` or `cc` while assembling in order to generate profiling information. After the code is executed, you can use `gprof` to examine the generated profile statistics. Alternatively, you can use the Unix command `time` focusing on the `user` time. A more accurate timing mechanism is included in the provided test framework.

## Writeup
 
The writeup accounts for 20% of the grade and it will also be used to determine your score for the Implementation aspect of the project (60%).

Your written documentation must discuss each optimization that you considered. For each optimization that you implement, your writeup must convince the reader that the optimization was beneficial, general, and correct. You should include assembly or IR code examples for each optimization, showcasing a before and after file for each one in the `doc` directory. Highlight the benefit of each optimization on the assembly or IR code. Discuss exactly how the benefit was achieved given the characteristics of our target architecture. If applicable, include empirical evidence that proves your conclusion for the optimization.

Your compiler should include a "full optimizations" command-line option (see below). Your written documentation should present a detailed discussion of this option including how you determined the order in which your optimizations are performed and how many times you apply the optimizations.

Finally, describe any hacks or solutions to tricky problems that you encountered, as well as how the group divided the work. Excluding code examples, the writeup should come up to at most 8 pages.

## Register Allocation / Other Papers

Beyond the normal course materials, we have provided links on the course website to useful and well-known papers on register allocation. Reading these before designing and writing your register allocator may be useful. You can find these links under the "Reference Materials" section.

## Parallelization

Tip: Historically, parallelization is the most difficult to get right; without a good implementation and a lot of work, you might end up with a buggy compiler, or worse, generating slower assembly code. Unless you're feeling ambitious, we recommend focusing on other optimizations.

The Java/Scala skeleton includes a simple parallelization analysis library (`decaf/Parallel/Analyze.java`) to help you find parallelism in programs being compiled. The library computes the `distance vector`. For more information you can read 9.3 in the Whale book. While a Go version does not currently exist, it is not difficult for you to port it to Go. You may find this library useful, or you may do your own analysis.

__Important__: If you choose to do this, talk to the TAs. The autograder benchmark estimates may be very different from the final results. Stay tuned for updates on Piazza.

## Submission

Your compiler must provide a command-line option for each optimization. Your project writeup should include documentation for each command-line option. For example:

1. `--opt=regalloc` -- turns on register allocation
1. `--opt=instsched` -- turns on instructions selection peephole optimizations and list scheduling 
1. `--opt=parallel` -- turns on data parallelization of loops

You must provide an `--opt=all` flag to turn on all optimizations and apply them in the order you have determined ("full optimizations"). This option should consider how many times each optimization is applied and the application order of the optimizations.

As before, your generated code must perform the runtime checks listed in the language specification. These may be optimized (or removed in some cases) as long as they report a runtime error whenever the unoptimized program reports an error.

Use the same procedure as in [phase 1](../phase-1/README.md#submission) to submit, but this time to the branch `derby` (__NOT__ `phase5-submission`).

Midway through phase 5 (the "checkpoint"), we will start benchmarking your submissions against the derby program. Everyone's performance on the derby program will be visible through a live leaderboard on the autograder portal. Take these numbers as estimates, however; there's a 5-10% variance in the benchmarking results, and we may decide to e.g. rerun everyone's submissions on a new machine. The final results will be announced at the end of class.

## Testing

We have provided sample programs that perform image processing and filtering. Pull them like before:

```bash
cd tests
git pull origin master
```

As usual, run the tests using the following command:

```bash
./tests/test.py optimizer
```

Note that these programs must be linked against the library provided in `optimizer/lib/` directory. If you're using the test script, this linking is already done for you.

You should also make sure that any valid program provided during previous phases continues to run correctly.

## References

1. [Linear Scan Register Allocation](https://dl.acm.org/citation.cfm?id=330250)
