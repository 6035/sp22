# 6.035 Computer Language Engineering SP22

__WE WILL MAKE ALL ANNOUNCEMENTS VIA [PIAZZA][piazza], SO MAKE SURE THAT YOU ENROLL__

If neither this site nor the Piazza has the information you need, you should [email the TA's directly](#course-staff)

## Table of Contents

1. [Schedule](#schedule-tentative)
1. [General Administrivia](#general-administrivia)
	1. [Course Staff](#course-staff)
	1. [MIT Catalog Description](#mit-catalog-description)
	1. [Recommended Texts](#recommended-texts)
	1. [Communication](#communication)
	1. [Grading](#grading)
	1. [Late policy](#late-policy)
	1. [Collaboration](#collaboration)
	1. [Compiler project](#compiler-project)
	1. [Class meetings](#class-meetings)
	1. [Contact](#contact)
	1. [Office Hours](#office-hours)
1. [Reference Materials](#reference-materials)
	1. [Official References](#official-references)
		1. [Provided During Exams](#provided-during-exams)
	1. [Unofficial References](#unofficial-references)

## Schedule (Tentative)

[Official MIT Calendar](https://registrar.mit.edu/calendar)

__!!! Note: This schedule is tentative and may change throughout the semester. !!!__


|                 | Monday          | Tuesday         | Wednesday       | Thursday        | Friday          |
| :-:             | :-:             | :-:             | :-:             | :-:             | :-:             |
| `01/31 - 02/04` |  __FIRST DAY__<br/>[LOGISTICS FORM][gform-1]<br/>[L: regex][lec02]  |  | [L: regex][lec02] | [L: top-down][lec03] | [P1 RELEASE][P1 RELEASE]<br/>[L: top-down][lec03] |
| `02/07 - 02/11` | P1 OH<br/>(during lecture slot) |                 |   [L: shift-reduce][lec04]    | [L: shift-reduce][lec04] | [L: shift-reduce][lec04]<br/>[L: IR][lec05]   |
| `02/14 - 02/18` | [L: IR][lec05] |  | [L: sem][lec06] | [L: codegen][lec07] | __P1 DUE__<br/>[P2 RELEASE][P2 RELEASE]<br/>[Quiz 1][]<br/> |
| `02/21 - 02/25` | Student holiday | [SUBMIT TEAM][SUBMIT TEAM]<br/> | [L: codegen][lec07] | [L: codegen$][lec07] | |
| `02/28 - 03/04` | | | | | ADD DATE<br/>__P2 DUE__<br/> |
| `03/07 - 03/11` | Student holiday | Student holiday | [L: dataflow][lec08] | [L: dataflow][lec08] | __PSET A DUE__ |
| `03/14 - 03/18` | [L: dataflow][lec09] | | [L: dataflow][lec09] | | |
| `03/21 - 03/25` |                 |                 |                 |                 | __P3 DUE__      |
| `03/28 - 04/01` | [L: loops][lec10] | [L: loops][lec10] | [L: lattice][lec12] | CPW<br/>[L: lattice47][lec12] | CPW<br/> |
| `04/04 - 04/08` | Patriots' Day | Student holiday | [L: lattice][lec12] | [L: reg][lec11] | __P4 DUE__<br/>[L: reg][lec11] |
| `04/11 - 04/15` | [L: lattice][lec12] | | [L: lattice$][lec12] | DROP DATE<br/>[L: schedule][lec15] | |
| `04/18 - 04/22` | __PSET B DUE__<br/>[L: sched][lec15] | [L: parallel][lec13] |                 | [L: parallel][lec13] | __CHECKPOINT__ |
| `04/25 - 04/29` |                 |                 |                 | L: research | L: research |
| `05/02 - 05/06` |                 |                 | __P5 DUE MIDNIGHT__      | __LAST DAY__<br/>__L: DERBY__ |      |



<!--- lecture slides --->
[lec01]: materials/lecture/lec01-f19-intro.pdf
[lec02]: materials/lecture/lec02-f19-regex-grammar.pdf
[lec03]: materials/lecture/lec03-f19-top-down-parsing.pdf
[lec04]: materials/lecture/lec04-f19-shift-reduce-parsing.pdf
[lec05]: materials/lecture/lec05-f19-intermediate-representation.pdf
[lec06]: materials/lecture/lec06-f19-semantic-analysis.pdf
[lec07]: materials/lecture/lec07-unoptimized-codegen.pdf
[lec08]: materials/lecture/lec08-s21-program-analysis.pdf
[lec09]: materials/lecture/lec09-program-analysis-2.pdf
[lec10]: materials/lecture/lec10-loop-optimization.pdf
[lec11]: materials/lecture/lec11-register-allocation.pdf
[lec12]: materials/lecture/lec12-foundations-of-dataflow.pdf
[lec13]: materials/lecture/lec13-parallelization.pdf
[lec14]: materials/lecture/lec14-mem.pdf
[lec15]: materials/lecture/lec15-sched.pdf


<!--- project phases --->
[P1 RELEASE]: phase-1/
[P2 RELEASE]: phase-2/
[P3 RELEASE]: phase-3/



<!--- others --->
[piazza]: https://piazza.com/mit/spring2022/6035
[gform-1]: https://forms.gle/4pHWL4QJXiEzjpUy6


<!--- handouts --->
[course tools]: materials/handouts/01-athena.pdf
[project info]: materials/handouts/01-project-overview.pdf
[decaf spec]: materials/handouts/01-decaf-spec.pdf



## General Administrivia

### Course Staff

- Faculty
    - Martin Rinard <rinard@csail.mit.edu>

- TA
    - Shivam Handa <shivam@mit.edu>
    - Alexandra Dima <adadima@mit.edu>

### MIT Catalog Description

- Prereq -- `6.004`, `6.031`
- Level -- `U`
- Units -- `4-4-4`

Analyzes issues associated with the implementation of higher-level programming languages. Fundamental concepts, functions, and structures of compilers. The interaction of theory and practice. Using tools in building software. Includes a multi-person project on compiler design and implementation.

### Recommended Texts

6.035 has no officially required textbook. All of the material you need is taught in class, with the exception of the documentation for your implementation language and associated libraries. However, the following books may be helpful in implementing various components of your compiler, and are available from MIT libraries.

```txt
Modern Compiler Implementation in Java (Tiger Book)
Andrew W. Appel and Jens Palsberg
Cambridge University Press, 2002
```

Many other resources such as technical papers, interesting and useful blog posts, and reference guides are available on the references page.

### Communication

We will distribute assignments here and make all announcements via [Piazza][piazza]. Important announcements will also be made via email.

Since lecture dates are not all finalized at the start of the semester, please check the schedule regularly.

### Grading

|Component             |Weight |
|----------------------|-------|
|Project phases 2 and 3|25%    |
|Project phases 4 and 5|50%    |
|Quiz 1                |9%     |
|Quiz 2                |10%    |
|Miniquizzes           |6%     |

For more information on the way the compiler project is graded, see the [project overview][project info].

### Late policy

We expect you to submit all components of the class on time. For extensions under extenuating circumstances (e.g., you are sick for a week, family emergencies), we require a letter from one of the student deans at S^3.

### Collaboration

Although you may discuss the projects with anybody, you must develop the code yourself. For the scanner/parser project, you must develop your code alone. On all subsequent projects, you should work with your team members, but you may not develop or share any code with other teams.

You may collaborate on the mini-quizzes and the problem sets, but you should write all solutions yourself.

Do not post your lab or homework solutions on publicly accessible web sites or file spaces; this enables cheating for students in future years.

### Compiler project

This class involves a group project, where you will build a compiler almost entirely from scratch. Details about the project can be found [here][project info]. Specific instructions for each phase of the project will be released later in the class.

### Class meetings

Lectures:

- 11am-12pm MTWRF in [32-124](http://whereis.mit.edu/?mapterms=32).

<!---
- 11am-12pm MTWRF on Zoom. The link will be distributed on [Piazza][piazza].

To find out whether there is lecture on a given day, check the calendar above. Lectures will be recorded, so don't worry if you can't make them.
-->

### Contact

For all general questions and/or concerns, please post on Piazza.

If the matter is private, please email the TA's directly.

### Office Hours

Refer to Piazza for the latest updates about office hours. We will have additional office hours before each quiz and each phase of the project.

## Reference Materials

This section contains a number of useful and/or interesting references selected by the staff. You are not expected to know most of the material on this page for the class; however, you may find it interesting and helpful.

### Official References

1. The complete [Intel x64 manual](http://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-manual-325462.pdf)
1. [Intel x64 Optimization Reference Manual](http://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-optimization-manual.pdf)
1. [x64 wiki](https://en.wikibooks.org/wiki/X86_Assembly/X86_Instructions)
1. [x64 cheat sheet](https://cs.brown.edu/courses/cs033/docs/guides/x64_cheatsheet.pdf) -- lists and tables detailing registers and assembly commands
1. [x86-64 architecture guide](http://6.035.scripts.mit.edu/fa18/x86-64-architecture-guide.html) -- a walkthrough with an example, and common commands
1. [Intel x64 manual](https://software.intel.com/en-us/articles/intel-sdm)
1. [Intel developer manual](https://software.intel.com/sites/default/files/managed/39/c5/325462-sdm-vol-1-2abcd-3abcd.pdf) -- detailed description of some assembly instructions

#### Provided During Exams

1. [x64 cheat sheet](https://cs.brown.edu/courses/cs033/docs/guides/x64_cheatsheet.pdf) -- lists and tables detailing registers and assembly commands

### Unofficial References

Interesting blog posts, papers, etc

1. Overviews
    1. [LLVM compiler architecture](http://www.aosabook.org/en/llvm.html)
    1. [GCC compiler architecture](http://en.wikibooks.org/wiki/GNU_C_Compiler_Internals/GNU_C_Compiler_Architecture)
1. Blogs
    1. [Russ Cox's Blog](http://research.swtch.com/) -- Russ is one of the developers of Google Go, a pretty interesting language.
    1. [Ian Wienand's Blog](http://www.technovelty.org/) -- Whoever he is, he writes about compiler and language internals, the magic black box that is the linker, and more
    1. [Matt Might's Blog](http://matt.might.net/articles/) -- Matt is a professor at the University of Utah and has written some very interesting articles (e.g. "Yacc is dead")
1. Papers
    1. [Register Allocation & Spilling via Graph Coloring](http://dl.acm.org/citation.cfm?id=806984) -- G.J. Chaitin / 1982. Great (short) paper on simple register allocation.
    1. [Linear Scan Register Allocation](https://dl.acm.org/citation.cfm?id=330250)
    1. [Iterated Register Coalescing](http://dl.acm.org/citation.cfm?id=229546) -- Lal George / 1996. Presents improvements/alternative to Chaitin's design. If Chaitin-style (+/-Briggs) register allocation isn't enough for you, this paper is a good read - actually, it's a good read anyway, to understand the tradeoffs
    1. [Superword Level Parallelism](http://dl.acm.org/citation.cfm?id=358438) combined with loop unrolling, a simple way to implement a vectorizing compiler
1. Miscellaneous
    1. [Scala Patterns for Compiler Design](https://gist.github.com/rcoh/4992969)
    1. `6.823 Advanced Computer Architecture` [lecture notes](http://csg.csail.mit.edu/6.823/lecnotes.html)
