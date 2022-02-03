# Phase 1

This project consists of two segments: lexical analysis (scanning, aka lexing) and syntactic analysis (parsing).

Due dates are posted on the [class schedule](../README.md).

## Table of Contents

1. [Getting Started](#getting-started)
1. [Scanner](#scanner)
1. [Parser](#parser)
1. [Additional Notes](#additional-notes)
1. [Project evaluation](#project-evaluation)
1. [Submission](#submission)

## Getting Started

1. Read the [project specification](../materials/handouts/01-project-spec.md). __Pay special attention to the policy on third-party libraries.__
1. Follow [these instructions](setup.md) to set up a programming environment.
1. Follow [these instructions](class-tools.md) to set up and understand the class tools.
1. Read the [Decaf spec][decaf spec], specifically the first two sections.

	Note that the first section alone does not fully describe the language, and you will need to refer to the Reference Grammar when implementing the scanner and parser. Also note that the reference grammar does not specify what goes into the scanner and what goes into the parser; you will have to determine this split yourself.

## Scanner

Your scanner must be able to identify tokens of the Decaf language, the simple imperative language we will be compiling in 6.035. The language is described in the [Decaf spec][decaf spec]. Your scanner should note illegal characters, missing quotation marks, and other lexical errors with reasonable error messages. The scanner should find as many lexical errors as possible, and should be able to continue scanning after errors are found. The scanner should also filter out comments and whitespace not in string and character literals.

When `-t scan` is specified, the output of your compiler should be a scanned listing of the program with one row for each token in the input. Each line will contain the following information: the line number (starting at 1) on which the token appears, the type of the token (if applicable), and the token's text. Please print only the following strings as token types: `CHARLITERAL`, `INTLITERAL`, `BOOLEANLITERAL`, `STRINGLITERAL` and `IDENTIFIER`.

For `STRINGLITERAL` and `CHARLITERAL`, the text of the token should be the text, as appears in the original program, including the quotes and any escaped characters.

Each error message should be printed on its own line, _before_ the erroneous token, if any. Such messages should include the file name, line and column number on which the erroneous token appears.

Here is an example table corresponding to `print("Hello, World!");`:

```
1 IDENTIFIER print
1 (
1 STRINGLITERAL "Hello, World!"
1 )
1 ;
```

The `tests` repository contains both a set of test files on which to test your scanner and the expected output for these files. The output of your scanner should match the provided output exactly on all valid files. For invalid files, we will only check that your compiler returns a non-zero exit code; the output does not need to match the provided output exactly.

## Parser

Your parser must be able to correctly parse programs that conform to the grammar of the Decaf language. Any program that does not conform to the language grammar must be flagged with at least one error message. As with the scanner, all error messages must be printed to standard error.

Your parser does not have to, and should not, recognize context-sensitive errors, e.g. using an integer identifier where an array identifier is expected. Such errors will be detected by the static semantic checker. _Do not_ try to detect context-sensitive errors in your parser. However, you might need to create syntactic actions in your parser to check for some context-free errors.

You might want to look at Section 3 in the Tiger book or Sections 4.3 and 4.8 in the Dragon book for tips on getting rid of shift/reduce and reduce/reduce conflicts from your grammar.

When `-t parse` is specified, any syntactically incorrect program should be flagged with at least one error message, and the program should exit with a non-zero exit code. Multiple error messages may be printed for programs with multiple syntax errors that are amenable to error recovery. Given a syntactically valid program, your parser should produce no output, and exit with the value zero (success). The exact format for parse error messages is not stipulated.

## Additional Notes
In the past we used to allow the use of a parser generator. However, this year you should write a recursive descent parser by hand.

One reason for this is that it will be a good learning experience, and you'll get to have a more intimate understanding of parsers and languages in general. In fact, many modern parsers (including the one used by GCC!) use recursive descent since it's a lot more powerful than parser generators.

A second and the more important reason is that you will have to choose teammates shortly after the completion of this phase. This is for you to be able to get a sense of other students' capabilities as software engineers before having to commit an entire semester to being their teammate. How well someone writes ANTLR grammars and lexer rules is not really indicative of how they write software. A recursive descent parser will be a nontrivial amount of code, and will be just about enough code to gauge someone's programming skills and for you to show off your own.

Lastly, a third reason is that it's more fun!

## Project evaluation

This phase will not be officially graded. However, each student will still be required to write and evaluate their own scanner and parser. There are several reasons for this shape of the project:

- Writing (and debugging!) a front-end for a non-trivial language is a useful skill. Think about being able to quickly prototype a domain specific language in a few hours once you master the parsing techniques and tools.
- The following projects in this class will require a front-end for the Decaf language.  Typically, this front-end will be constructed by combining the parsers written by the individual students.
- Your parser serves as an advertisement to the potential group mates. It gives a good indicator for how much your group can expect from you in the follow-up projects.

To catalyze the group formation process, we will make all students' repositories visible to all other students in the class on the due date. This will also be an opportunity for the students to assess the similarities and differences in their approaches and how to combine the scanners/parsers together.

However, copying code between the projects is strictly forbidden. While the students are allowed to inspect and discuss other students' solutions, copying code will be considered cheating. We will be strict in enforcing this policy!

## Submission

To submit this phase, commit all work and run the following commands:

```
git checkout -b phase1-submission
git push origin phase1-submission
git switch master
```

This creates a new branch called `phase1-submission`, pushes it to Github, then returns back to the `master` branch. You can resubmit using the following commands:

```
git switch phase1-submission
git rebase master
git push origin phase1-submission
git switch master
```

This pulls the commits from `master` into `phase1-submission`, pushes them to Github, then returns back to the `master` branch. You can resubmit as many times as you like.

Even though this phase isn't officially graded, please submit your work by the deadline to ensure that your setup is compatible with the grading server's. We will communicate to you the number of tests passed on the grading server. 

Note that we will read your code and may, for example, give a lower grade (than dictated by the number of tests passed) for bad-faith projects (e.g. using non-approved third-party libraries or writing code specific to particular tests in the test suites).

You don't have to submit any documentation/writeups for this phase.

## Appendix: Why we defer integer range checking until the next project

When considering the problem of checking the legality of the input program, there is no fundamental separation between the responsibilities of the scanner, the parser and the semantic checker. Often, the compiler designer has the choice of checking a certain constraint in a particular phase, or even of dividing the checking across multiple phases. However, for pragmatic reasons, we have had to divide the complete scan/parse/check unit into two parts simply to fit the course schedule better.

As a result, we will have to mandate certain details about your implementations that are not necessarily issues of correctness. For example, one cannot completely check whether integer literals are within range without constructing a parse tree.

Consider the input:

```
x+-9223372036854775808
```

This corresponds to a parse tree of:

```
    +
   / \
  x   -
      |
  INT(9223372036854775808)
```

We cannot confirm in the scanner that the integer literal -9223372036854775808 is within range, since it is not a single token. Nor can we do this within the parser, since at this stage we are not constructing an abstract syntax tree. Only in the semantic checking phase, when we have an AST, are we able to perform this check, since it requires the unary minus operator to modify its argument if it is an integer literal, as follows:

```
    +                         +
   / \                       / \
  x   -           ---->     x   INT(-9223372036854775808)
      |
  INT(9223372036854775808)
```

Of course, if the integer token was clearly out of range (e.g. 999999999999999999999) the scanner could have rejected it, but this check is not required since the semantic phase will need to perform it later anyway.

Therefore, rather than do some checking earlier and some later, we have decided that ALL integer range checking must be deferred until the semantic phase. So, your scanner/parser must not try to interpret the strings of decimal or hex digits in an integer token; the token must simply retain the string until the semantic phase.

When printing out the token table from your scanner, do not print the value of an `INTLITERAL` token in decimal. Print it exactly as it appears in the source program, whether decimal or hex.

[decaf spec]: ../materials/handouts/01-decaf-spec.pdf
