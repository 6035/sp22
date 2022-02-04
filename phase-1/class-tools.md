# Setting up the class tools

Choose a language and its corresponding skeleton repo:

- [Java](https://github.com/6035/java-skeleton)
- [Scala](https://github.com/6035/scala-skeleton)
- [Go](https://github.com/6035/go-skeleton)
- Get in touch with the TAs if you want to use Haskell or any other language.

Create and navigate to a directory where you'd like your project files to be stored (`~/Documents/6.035-compiler-phase1` or `~/Desktop/6.035-compiler-phase1` is a good choice) and run the following commands.

```bash
git init

git remote add skeleton https://github.com/6035/<LANGUAGE>-skeleton-sp22.git
git pull skeleton master

git remote add origin git@github.com:6035/<YOUR KERB>-phase1.git
git push -u origin master

git clone git@github.com:6035/tests-sp22.git tests
```

This should initialize your phase 1 project directory with the skeleton code for your chosen language, and pull a copy of the tests repository into your project. The tests are managed as a separate Git project and should already be `gitignore`'d  in the provided skeleton code. If you get `Permission denied (publickey)`, make sure you have set up an SSH key with GitHub (see the [Git handout](git.md))

Make sure that your environment is set up correctly by running the parser tests: `./tests/test.py parser`. More information about the test framework can be found below. Note that you need `gcc` installed to run the test script. You can install it with the following command:

```
sudo apt install -y build-essential
```

While you are encouraged to use this infrastructure, you may also choose to modify it however you like, or even ignore it and design your own infrastructure from scratch. If you choose to do so, you will still need to replicate all command line options and the functionality required for the scripts that build and execute the compiler, as detailed in the [project specification][project info]. You should also still be able to clone the `tests` repository into your project directory and run the test harness as described in [Running your compiler](#running-your-compiler).

# Provided infrastructure

## Java/Scala

The Java and Scala skeletons rely on Apache Ant for build automation. Ant is configured by the `build.xml` file, which we mainly use to recursively select Java files for compilation. You are encouraged to read and understand the provided `build.xml`.

The Java skeleton has the following structure:

```
.
|-- build.sh
|-- run.sh
|-- build.xml
`-- src
    `-- edu
        `-- mit
            `-- compilers
                |-- Main.java
                `-- grammar
                    |-- DecafParser.java
                    |-- DecafScanner.java
                    |-- Token.java
                `-- tools
                    |-- CLI.java
    `-- decaf
        `-- Parallel
            |-- Analyze.java
```

The program entry point is located in `Main.java`. `CLI.java` implements the command-line interface described in the [project specification][project info]; you can modify it to add new command-line flags as needed. There are some dummy parser, scanner, and token classes defined in the `grammar` folder. `Analyze.java` will only be used in phase 5; more information about it will be provided when phase 5 is released.

The Scala skeleton is similar, but with the source files in different locations. Specifically, `CLI.java` is located in `src/util/`, dummy parser files are located in `edu/mit/compilers/parser/`, and the equivalent of `Main.java` is `src/compile/Compiler.scala`. Also, we have included a sample unit test for convenience in the `unittests` directory, which may be run with `ant test`.

## Go

Go enforces a "monorepo" structure and tries to put everything in the same directory. To make things portable, the provided code provides an alternate Go workspace in the `workspace` directory and points `GOPATH` to that in `build.sh`. Make sure you specify `GOPATH` if you need to run Go commands beyond `build.sh`.

The files that you will actually be creating and modifying live in `workspace/src/mit.edu/compilers/compiler`. 

In the source file directory, `main.go` contains the program entry point, and `cli.go` implements the command-line interface described in the [project specification][project info]. Feel free to modify `cli.go` to add more command-line flags as needed. `parser` contains dummy files related to scanning and parsing: `scanner.go`, `parser,go`, and `token.go`

# Running your compiler

All skeleton projects come with a `build.sh` and `run.sh` in the project root. Use these to build and run your compiler respectively. They will also be used for grading, so if you make any changes to the build or run process, make sure to modify these files to reflect them. In general, these two files are the only restriction we impose on the structure of your projects. If you decide to change the repository structure, or even use a langiage for which we have no template, you just need to modify `build.sh` and `run.sh` to correctly build and run your system. 

Arguments passed to `run.sh` are passed straight to your compiler. Read the [project specification][project info] for more information about the command-line arguments that your compiler should accept.

You should have cloned the `tests` repository as part of the setup process. You will need to pull from it at the start of each phase as more tests are released throughout the semester. It comes with a file `test.py` to help you (and the grading server) run tests easily.

Run `./tests/test.py -h` (from your project root) to see what arguments you can pass in. Here are some examples:

- `./tests/test.py scanner -l` lists the scanner tests.
- `./tests/test.py scanner -j4` runs all scanner tests using four threads.
- `./tests/test.py scanner -f id*` runs all scanner tests starting with `id`.
- `./tests/test.py scanner -f id1 -v` runs the `id1` scanner test, but also prints everything to console, including the input, compiler output and stderr, and expected output.

Note that `-v` without `-d` only prints your compiler output after it has finished running, so nothing will show if your compiler enters an infinite loop. For real-time output, you should include the `-d` flag (which also passes `--debug` into your compiler).

Most of the other flags are only used in phase 3 and onwards. We will release more information about them when they become relevant.

[project info]: ../materials/handouts/01-project-spec.md
