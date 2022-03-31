# Phase 4 -- Dataflow Optimizations

## Overview

For this phase, you will add dataflow optimizations to your compiler. __At the very least, you must implement one of these three global dataflow optimizations:__

1. __Global CSE (Common Subexpression Elimination)__: Identification and elimination of redundant expressions using the algorithm described in lecture (based on available-expression analysis). See 8.3 and 13.1 of the Whale book, 10.6 and 10.7 in the Dragon book, and 17.2 in the Tiger book.
1. __Global Copy Propagation__: Given a `copy` assignment like `x = y`, replace uses of `x` by `y` when legal (the use must be reached by only this def, and there must be no modification of `y` on any path from the def to the use).  See 12.5 of the Whale book and 10.7 of the Dragon book.
1. __Dead Code Elimination__: Dead code elimination is the detection and elimination of code which computes values that are not subsequently used. This is especially useful as a post-pass to constant propagation, CSE, and copy propagation. See the 18.10 ofthe Whale book and 10.2 of the Dragon book.

All other optimizations listed below are optional. We list them here as suggestions since past winners of the compiler derby typically implement each of these optimizations in some form. You are free to implement any other optimizations you wish. Note that you will be implementing register allocation and other assembly level optimizations for the next phase, so you don't need to concern yourself with it now.

The following are other dataflow optimizations that are useful to implement, but not required for this phase.

1. __Global Constant Propagation and Folding__: Compile-time interpretation of expressions whose operands are compile time constants. See the algorithm described in 12.1 of the Whale book.
1. __Loop-invariant Code Motion (code hoisting)__: Moving invariant code from within a loop to a block prior to that loop. See 13.2 of the Whale book and 10.7 of the Dragon book.
1. __Unreachable Code Elimination__: Unreachable code elimination is the conversion of constant conditional branches to equivalent unconditional branches, followed by elimination of unreachable code. See 18.1 of the Whale book and 9.9 of the Dragon book.

The following are not generally considered dataflow optimizations. However, they can increase the effectiveness for the transformations listed above.

1. __Algebraic Simplification and Reassociation__: Basic algebraic simplifications described in class. This includes simplifying the following rules:
    ```hs
    a + 0      -> a
    a - 0      -> a
    a * 1      -> a
    b == true  -> b
    b != false -> b
    ...
    ```
    You may find it useful to canonicalize expression orders, especially if you choose to implement CSE. See 12.3 of the Whale book and 10.3 of the Dragon book.
1. __Strength reductions__: Algebraic manipulation of expressions to use less expensive operations. This includes the following transformations that convert multiplying constants into bit shifts i.e.: `a * 4 -> a << 2` and include turning multiplication operations from within a loop into sum operations.
1. __Inline Expansion of Calls__: Replacement of a call to procedure `P` by inline code derived from the body of `P`. This can also include the conversion of tail-recursive calls to iterative loops. See 15.1 and 15.2 of the Whale book.

You will want to think carefully about the order in which optimizations are performed. You may want to perform some optimizations more than once.

All optimizations (except inline expansion of calls) should be done at the level of a single method. Be careful that your optimizations do not introduce bugs in the generated code or break any previously-implemented phase of your compiler. Needless to say, it would be foolish not to do regression testing using your existing test cases. __Do not underestimate the difficulty of debugging this phase__.

As in phase 3, your generated code must include instructions to perform the runtime checks listed in the language specification. It is desirable to optimize these checks whenever possible, e.g. using CSE to eliminate array bounds tests. Note that the optimized program must report a runtime error for exactly those program inputs for which the corresponding unoptimized program would report a runtime error (and the runtime error message must be the same in both cases). However, we allow the optimized program to report a runtime error earlier than it might have been reported in the unoptimized program.

## What to Hand In

You don't need to submit a full writeup for this phase; just give a short status report of how you're doing. You'll be submitting design documentation about the optimizations you've implemented in this phase in the phase 5 writeup.

Your compiler's command line interface must provide the following interface for turning on each optimization individually. Something similar should be provided for every optimization you implement. Document the names given to each optimization not specified here.

1. `--opt=cse` turns on common subexpression elimination only
1. `--opt=cp` turns on copy propagation optimization only
1. `--opt=cp,cse` turns on copy propagation optimization and common subexpression elimination only
1. `--opt=all` turns on all optimizations
1. `--opt=all,-cse` turns on all optimizations except common subexpression elimination

This is the interface provided by CLI.opts / optnames facility in the skeleton repositories. See the source for more details. For the full command-line specification, see the project overview handout.

You should be able to run your compiler from the command line with:

```bash
./run.sh --target=assembly $FILE_NAME           # no optimizations
./run.sh --target=assembly --opt=all $FILE_NAME # all optimizations
```

Your compiler should then write a x86-64 assembly listing to standard output, or to the file specified by the `-o` argument.

Use the same procedure as in [phase 1](../phase-1/README.md#submission) to submit, but this time to the branch `phase4-submission`.

## FAQ

> How do I test my compiler for correctness? i.e. test if my generated assembly works

1. Use [ideas from 6.031](https://web.mit.edu/6.031/www/fa19/classes/03-testing/).
1. Use `./tests/test.py codegen` like before.

> How do I check that my optimizations are working?

1. Use [ideas from 6.031](https://web.mit.edu/6.031/www/fa19/classes/03-testing/).
1. Write __SMALL__ decaf programs and visually inspect to see if your optimizations took place. We've done some of these for you in the tests repository. Pull them like before:

	```bash
	cd tests
	git pull origin master
	```

	Note that these tests will still pass even if you do not implement any optimizations. You should check that your optimizations are actually being performed by compiling them with and without a given optimization and manually inspecting the generated assembly code or debug CFG output. As before, use `-D` to force the script to write the assembly and compiled executable into your filesystem:

	```bash
	./tests/test.py dataflow -f cp-01 -D test-workspace
	```

1. Benchmark for performance: the test script includes benchmarking functionality using [hyperfine](https://github.com/sharkdp/hyperfine). You will need to follow the steps below in order to make it work: 

	a) Hypefine is not installed on athena, so you will have to download and install it locally:

	```bash
	wget https://github.com/sharkdp/hyperfine/releases/download/v1.13.0/hyperfine_1.13.0_i686.deb
	dpkg -x hyperfine_1.13.0_i686.deb $HOME
	```

	This will create the hyperfine executable at location `$HOME/usr/bin/hyperfine`

	b) To run the tests with benchmarking, you need to append the hyperfine location to your path as well as pass the `-b` flag, e.g.:

	```bash
	PATH="$HOME/usr/bin/:$PATH" python3 tests/test.py dataflow -b
	```

	c) If you want to use hyperfine to benchmark only one specific example of your own, you will need to compile your decaf file to an assembly file, and assemble that into an executable. Hyperfine can then take the executable as argument for benchmarking it:
	```bash
	./run.sh --opt=all --target=assembly --output=out.S your-decaf-program.dcf
	gcc -no-pie -O0 your-decaf-program.S -o your-decaf-program
	$HOME/usr/bin/hyperfine ./your-decaf-program
	```

	1. Note that all tests that we have provided you thus far are super small, and they run in less than 5 milliseconds. This is too small for any differences to show during benchmarking. We encourage you to write and benchmark your own decaf programs that take a long time to run, on the order of seconds.
