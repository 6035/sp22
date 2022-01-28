# Setting up a programming environment

You will need a UNIX environment to run the class tools and ensure that we can build your compiler on the grading server. If you're not already on Linux, you can [set up a virtual machine](vm.md), but note that you'll be doing a substantial amount of coding throughout the semester and a VM might not be the best environment for that. If you're comfortable with a different UNIX environment (e.g. WSL/MinGW/MacOS/Arch), we encourage you to try setting up there instead. but while we'll try our best to help you with any setup issues, we might not be able to help you as effectively if you're using an environment we're unfamiliar with.

Since you'll be doing a lot of coding in your chosen environment, it's worth investing effort into customizing it to be comfortable and productive for you. This might include installing your favorite text editor, tweaking your VM settings, or figuring out how to make your favorite IDE work with the class tools.

While the class was traditionally run on Athena, we recommend against using Athena this semester. The dialup can be slow and isn't suited for computationally intensive work like optimizing programs.

Subsequent instructions assume that you're on a default Ubuntu installation. If you're completely new to Linux, read [Using the Ubuntu Terminal](cmd.md). You will need to use the terminal at least to run the test scripts.

We will be using Git to distribute test scripts and handle code submission. You should also use Git to collaborate with your teammates. If you're not familiar with basic Git usage like resolving merge conflicts, read [Using Git](git.md). That also includes instructions for setting up Git.

Finally, set up the environment for your chosen language. If you're using Java or Scala, install the JDK and Apache Ant:

```
sudo apt install -y openjdk-11-jdk ant
```

If you're using Scala, also install Scala:

```
sudo apt install -y scala=2.11.12-4
```

**Important note:** Ant support has been removed from the latest version of Scala. Make sure that you're using at most Scala 2.11. If you'd like to use the latest Scala, feel free to update the build scripts to support it. With your permission, we might even be able to use them in later class offerings!

If you're using Go, follow [the installation instructions on Go's website](https://golang.org/doc/install).

You shouldn't need to use version-dependent language features, but if it helps, these are the version numbers of software currently installed on the grading server:

- JDK: 11.0.9.1
- Scala: 2.11.12
- Go: 1.15.7
- Ant: 1.10.5

Note that software on the grading server might get upgraded throughout in the semester.
