# Phase 2

Due dates are posted on the [class schedule](../README.md).

Extend your compiler to find, report, and recover from semantic errors in Decaf programs. Most semantic errors can be checked by testing the rules enumerated in the "Semantic Rules" section of the [Decaf spec](../materials/handouts/01-decaf-spec.pdf). However, you should read the spec in its entirety to make sure that your compiler catches all semantic errors implied by the language definition. We have attempted to provide a precise statement of the semantic rules. If you feel some semantic rule in the language definition may have multiple interpretations, you should work with the interpretation that sounds most reasonable to you and clearly list your assumptions in your project documentation (you can also ask the TAs for clarification).

This part of the project includes the following tasks:

1. Create a high-level intermediate representation (IR) tree. You can do this either by adding actions to your grammar to build a tree, or by generating a generic tree with the parser generator and walking that to construct a new tree. The problem of designing an IR will be discussed in the lectures; some hints are given in the final section of this handout.

	When running in debug mode, your compiler should pretty-print the constructed IR tree in some easily-readable form suitable for debugging. This will not graded, but is highly recommended by the course staff.
1. Build symbol tables for the methods.  (A symbol table is an environment, i.e. a mapping from identifiers to semantic objects such as variable declarations.  Environments are structured hierarchically, in parallel with source-level constructs, such as method-bodies, loop-bodies, etc.)
1. Perform all semantic checks by traversing the IR and accessing the symbol tables. __Note:__ the run-time checks are not relevant for this phase.

Reach out to the TAs if you're not sure what to do, find yourself struggling to make progress, or encounter problems relating to team dynamics.

## Getting started

Each team will be given their own private GitHub repository. Teams should take care to avoid accidentally (or intentionally) sharing their code with other teams. Such actions will constitute cheating.

Use the procedure described in [Setting up the class tools](../phase-1/class-tools.md) to initialize a new local Git repository with the skeleton repository and tests, then copy in the relevant parts of each team member's scanner and parser. This time, set the `origin` remote to your team repository instead of your personal phase 1 repository.

You should be able to run your compiler from the command line with:

```
./run.sh --target=inter <filename>
```

The resulting output to the terminal should be a report of all errors (printed to stderr) encountered while compiling the file. Your compiler should give reasonable and specific error messages (with line numbers, column numbers and identifier names) for all errors detected. It should avoid reporting multiple error messages for the same error. For example, if `y` has not been declared in the assignment statement `x=y+1;`, the compiler should report only one error message for `y`, rather than one error message for `y`, another error message for the `+`, and yet another error message for the assignment.

After you implement the static semantic checker, your compiler should be able to detect and report _all_ static (i.e., compile-time) errors in any input Decaf program, including lexical and syntax errors detected by previous phases of the compiler. In addition, your compiler should not report any messages for valid Decaf programs.

As suggested, when run in debug mode, your compiler should print the constructed IR and symbol tables in some form. You can run in debug mode using the command:

```
./run.sh --target=inter --debug <filename>
```

The test cases for this phase have been added to the `tests` repository on Github. They can be added to your repo by going into the `tests` directory and running (as always):

```
git pull origin master
```

Read the comments in the test cases to see what we expect your compiler to do. Points will be awarded based on how well your compiler performs on these tests cases.

## Documentation / Write-up

The writeup for this phase will not be graded. It serves more as a status report and an opportunity to communicate issues to the TAs. Include the following information:

- __Status:__ Is your implementation complete? Are there tests you are failing or bugs you are aware of?
- __Questions / Concerns:__ Is there anything in your project that you want TAs to review or address? Are there problems or difficulties you want us to be aware of?
- __Team Dynamics:__ How did your group divide the work? What issues did you have working as a team, and how do you plan to resolve them going forward?

In future phases, the writeup will also serve as a design document help us understand and grade your submissions. Note that you will need to submit documentation about this phase later, in the phase 3 writeup.

## Submission

Submitted repositories should have the following structure:

```txt
<repo name>
├── build.sh
├── run.sh
├── doc/
│   └── phase2.pdf  // phase 2 writeup
└── ...
```

Use the same procedure as in [phase 1](../phase-1/README.md#submission) to submit, but this time to the branch `phase2-submission`. Again, check that your submission gives the correct score on the grading server, but keep in mind that nothing on the grading server represents final scores.

Note: please don't spam the grading server! Only submit when you're ready to submit, and **make sure that your compiler doesn't get caught in an infinite loop when running any of the tests**. Your order in the grading queue is determined by the total amount of time your team has consumed in previous submissions.

## Implementation Suggestions

- You'll probably need to declare classes for each of the nodes in your IR. In many places, the hierarchy of IR node classes will resemble the language grammar. For example, a part of your inheritance tree might look like this (where indentation represents inheritance):

	```
	abstract class Ir
	abstract class     IrExpression
	abstract class         IrLiteral
	         class             IrIntLiteral
	         class             IrBooleanLiteral
	         class         IrCallExpr
	         class             IrMethodCallExpr
	         class             IrCalloutExpr
	         class         IrBinopExpr
	abstract class     IrStatement
	         class         IrAssignStmt
	         class         IrPlusAssignStmt
	         class         IrBreakStmt
	         class         IrContinueStmt
	         class         IrIfStmt
	         .
	         .
	         .
	abstract class     IrMemberDecl
	         class         IrMethodDecl
	         class         IrFieldDecl
	         .
	         .
	         .
	         class     IrVarDecl
	         class     IrType
	```

	Classes such as these implement the _abstract syntax tree_ of the input program. In its simplest form, each class is simply a tuple of its subtrees, for example:

	```
	public class IrBinopExpr extends IrExpression
	{
	    private final int          operator;                      |
	    private final IrExpression lhs;                           +
	    private final IrExpression rhs;                          / \
	}                                                         lhs   rhs
	```

	or:

	```
	public class IrAssignStmt extends IrStatement
	{                                                             :=
	    private final IrLocation   lhs;                          /  \
	    private final IrExpression rhs;                       lhs    rhs
	}
	```

	In addition, you probably want to define classes for the semantic entities of the program, which represent abstract properties (e.g. expression types, method signatures, etc.) and to establish the correspondences between them.  Some examples: every expression has a type; every variable declaration introduces a variable; every block defines a scope.  Many of these properties are derived by recursive traversals over the tree.

- **Don't be afraid to refactor "working" code.** As you progress through the project, you will gain a better understanding of what you need to do and how you want your data structures to look like. Sometimes it makes sense to stick with early design decisions; sometimes refactoring them would save you time in the long run.

	If you're worried about losing progress, save your working state with Git. If you don't know how to do this or how to recover a previous state once you've saved it, reach out on Piazza. Being able to use Git is one of the most important skills you can have as a software developer.

- All error messages should be accompanied by the filename, line number and column number of the token most relevant to the error message (use your judgement here). This means that, when building your abstract-syntax tree (or AST), you must ensure that each IR node contains sufficient information for you to determine its line number at some later time.

	It is _not_ appropriate to throw an exception when encountering an
error in the input: doing so would lead to a design in which at most
one error message is reported for each run of the compiler.  A good
front-end saves the user time by reporting multiple errors before
stopping, allowing the programmer to make several corrections before
having to restart compilation.

- Semantic checking should be done __top-down__. While the type-checking component of semantic checking can be done in bottom-up fashion, other kinds of checks (for example, detecting uses of undefined variables) can not.

	There are two ways of achieving this. The first is to perform the checks at the same time as parsing, e.g. using parser actions in the middle of productions. This approach may require less code but can be more complex, because more work needs to be done directly within the parser generator.

	A cleaner approach is to invoke your semantic checker on a complete AST after parsing has finished.  The pseudocode for `block` in this approach would resemble this:

	```
	   void checkBlock(EnvStack envs, Block b) {
	       envs.push(new Env());
	       for each statement in b.statements
	           checkStatement(envs, statement);
	       envs.pop();
	   }
	```

	In this pseudocode, a new environment is created and pushed on the environment stack, the body of the block is checked in the context of this environment stack, and then the new environment is discarded when the end of the block is reached.

	The semantic check, just like code generation and certain optimizations, can often be expressed as a _visitor_ (which should be familiar from _Design Patterns_ or 6.031) over the AST. However, the visitor pattern may be most useful in imperative programming, and is just one design. For languages that support first-class functions, there are certainly many other interesting designs. Whatever your language and design, we strongly recommend you separate your logic for performing generic AST operations (traversing, searching, etc) from the logic of your compiler functions (e.g. semantic checks). Think carefully about what designs will work well for performing many analyses and operations on an AST!

- One last note: the treatment of negative integer literals requires some care. Recall from the previous handout that negative integer literals are in fact two separate tokens: the positive integer literal and a preceding `-`.  Whenever your top-down semantic checker finds a unary minus operator, it should check if its operand is a positive integer literal, and if so, replace the subtree (both nodes) by a single, negative integer literal.
