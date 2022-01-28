# Setting up the class tools

Choose a language and its corresponding skeleton repo:

- [Java](https://github.com/6035/java-skeleton)
- [Scala](https://github.com/6035/scala-skeleton)
- [Go](https://github.com/6035/go-skeleton)
- Get in touch with the TAs if you want to use Haskell or any other language.

Create and navigate to a directory where you'd like your project files to be stored (`~/Documents/6.035-compiler-phase1` or `~/Desktop/6.035-compiler-phase1` is a good choice) and run the following commands.

```bash
git init

git remote add skeleton https://github.com/6035/<LANGUAGE>-skeleton.git
git pull skeleton master

git remote add origin git@github.com:6035/<YOUR KERB>-phase1.git
git push -u origin master

git clone git@github.com:6035/tests.git
```

This should initialize your phase 1 project directory with the skeleton code for your chosen language, and pull a copy of the tests repository into your project. The tests are managed as a separate Git project and should already be `gitignore`'d  in the provided skeleton code. If you get `Permission denied (publickey)`, make sure you have set up an SSH key with GitHub (see the [Git handout](git.md))

Make sure that your environment is set up correctly by running the parser tests: `./tests/test.py parser`. More information about the test framework can be found below. Note that you need `gcc` installed to run the test script. You can install it with the following command:

```
sudo apt install -y build-essential
```

While you are encouraged to use this infrastructure, you may also choose to modify it however you like, or even ignore it and design your own infrastructure from scratch. If you choose to do so, you will still need to replicate all command line options and the functionality required for the scripts that build and execute the compiler, as detailed in the [project specification][project info]. You should also still be able to clone the `tests` repository into your project directory and run the test harness as described in [Running your compiler](#running-your-compiler).

# Provided infrastructure

## Java/Scala

The Java and Scala skeletons rely on ANTLR 2 for LL(\*) parser generation and Apache Ant for build automation (don't mix them up!). Ant is configured by the `build.xml` file, which we mainly use to run ANTLR and recursively select Java files for compilation. You are encouraged to read and understand the provided `build.xml`.

The Java skeleton has the following structure:

```
.
|-- build.sh
|-- run.sh
|-- build.xml
`-- lib
    |-- antlr.jar
`-- src
    `-- edu
        `-- mit
            `-- compilers
                |-- Main.java
                `-- grammar
                    |-- scanner.g
                    |-- parser.g
                `-- tools
                    |-- CLI.java
    `-- decaf
        `-- Parallel
            |-- Analyze.java
```

The program entry point is located in `Main.java`. `CLI.java` implements the command-line interface described in the [project specification][project info]; you can modify it to add new command-line flags as needed. The ANTLR grammar files are `scanner.g` and `parser.g`. `Analyze.java` will only be used in phase 5; more information about it will be provided when phase 5 is released.

The Scala skeleton is similar, but with the source files in different locations. Specifically, `CLI.java` is located in `src/util/`, ANTLR and the ANTLR grammar files are located in `parser/`, and the equivalent of `Main.java` is `src/compile/Compiler.scala`. Also, we have included a sample unit test for convenience in the `unittests` directory, which may be run with `ant test`.

Be warned that ANTLR 2 is not the latest version of ANTLR, which works very differently from ANTLR 2. We do not allow using the latest version of ANTLR as it contains many advanced features like rule rewriting. Be careful when searching for information about ANTLR on the web as information pertaining to the latest version of ANTLR will likely be incompatible with ANTLR 2. The documentation for ANTLR 2 can be found here: <https://www.antlr2.org/doc/index.html>

ANTLR is invoked by the `scanner` and `parser` tasks of the Ant build file. The generated files, `DecafScanner.java`, etc, are placed in the `autogen` directory. Note that ANTLR merely generates a Java source file for a scanner class; it does not compile or even syntactically check its output. Thus, typos or syntactic errors in the scanner grammar file will be propagated to the output.

An ANTLR generated scanner produces a string of tokens as its output. Each token has the following fields:

- `type`: The integer type of the token.
- `text`: The text of the token.
- `line`: The line in which the token appears.
- `col`: The column in which the token appears.

Every distinguishable terminal in your Decaf grammar will have an automatically generated unique integer associated with it so that the parser can differentiate them. These values are created from your scanner grammar and stored in the generated `*TokenTypes.java` files.

**Note:** You might be familiar with regexes from other languages. ANTLR 2's lexer rule syntax might share some similarities, but is actually very different. Consult the ANTLR 2 documentation for how to write them correctly.

## Go

Go enforces a "monorepo" structure and tries to put everything in the same directory. To make things portable, the provided code provides an alternate Go workspace in the `workspace` directory and points `GOPATH` to that in `build.sh`. Make sure you specify `GOPATH` if you need to run Go commands beyond `build.sh`.

The Go skeleton project uses lexmachine for scanning and goyacc for LALR(1) parsing. The lexmachine source files are located in `workspace/src/github.com/timtadh/lexmachine`, while goyacc is provided precompiled in `lib/goyacc`.

The files that you will actually be creating and modifying live in `workspace/src/mit.edu/compilers/compiler`. You can access that directory quickly with the symbolic link `src` in the project root.

In the source file directory, `main.go` contains the program entry point, and `cli.go` implements the command-line interface described in the [project specification][project info]. Feel free to modify `cli.go` to add more command-line flags as needed. `grammar` contains files related to scanning and parsing:

- `parser.go` contains the Lex and Parse functions, as well as the lexmachine lexer rules.
- `golex.go` contains the glue code to make goyacc work with lexmachine.
- `parser.y` contains the goyacc parser rules.
- `ast.go` contains the definitions for the AST constructed by goyacc.

You should read these files and understand what they do. You can find documentation for lexmachine at <https://github.com/timtadh/lexmachine>. There isn't documentation for goyacc itself since it's a direct port of yacc, which has documentation at <http://dinosaur.compilertools.net/yacc/index.html>.

When goyacc is invoked in `build.sh`, the files `y.go` and `y.output` are generated in the `grammar` directory beside `parser.y`. `y.output` contains the full parse table generated by goyacc, and you should read it when debugging your grammars. `y.go` is the parser itself, and you might find it useful to refer to it to understand how the generation process works.

# Running your compiler

All skeleton projects come with a `build.sh` and `run.sh` in the project root. Use these to build and run your compiler respectively. They will also be used for grading, so if you make any changes to the build or run process, make sure to modify these files to reflect them.

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
