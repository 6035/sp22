# Project specification

There are few restrictions on how the project should be structured, except that it should be self-contained (apart from the allowed libraries and programming environment), and contain executables called `build.sh` and `run.sh` in the root directory.

`build.sh` and `run.sh` should build and run the compiler respectively. `run.sh` should implement the command-line interface described in the next section. These files are provided for you in the skeleton code, but you can modify them if you need to.

Projects should also contain a `doc/` directory in the project root, where the writeups will be located.

## Third-party Libraries

In phase1 you may not use any libraries beyond parser generators and your chosen language's basic standard libraries for working with collections and data types. For example, Scala users may not use the PackratParsers package, and Haskell users may not use Data.Graph, Compiler, Parsec, or Control.Lens.Plated. Keep in mind that, while we allow the use of parser generators like Java/Scala's ANTLR or Go's goyacc, we strongly encourage you to implement the scanner and parser from scratch. 

If you are unsure of whether or not you're allowed to use a piece of software, ask the TAs.

## Command-line Interface

We will run your compiler with the following command line interface.

```
./run.sh [options] filename
```

|Flag|Description|
|----|-----------|
|`-t <stage>` or `--target=<stage>`|`<stage>` is one of scan, parse, inter, or assembly.  Compilation should proceed to the given stage.|
|`-o <outname>` or `--output=<outname>`|Write output to `<outname>`|
|`-O <optimizations>` or `--opt=<optimizations>`|Perform the (comma-separated) listed optimizations.<br/>`all` stands for all supported optimizations.<br/>`-<optimization>` removes optimizations from the list.
|`-d` or `--debug`|Print debugging information. If this option is not given, there should be no output to the screen on successful compilation.|

The command line arguments you must implement are listed here. Exactly one filename should be provided, and it should not begin with a `-`. The filename must not be listed after the `-O / --opt=` flag, since it will be assumed to be an optimization.

The default behavior is to compile as far as the current assignment of the project and print the output to standard output unless different output is specified with `-o / --output=`. All error messages should be printed to standard error.

By default, no optimizations are performed. The list of optimization names will be provided in the optimization assignments.

The provided skeleton code for each language is sufficient to implement this interface. You are free to use additional flags to add features, but they will not be used for grading.
