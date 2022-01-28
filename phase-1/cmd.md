# Using the Ubuntu Terminal

Open the terminal by pressing `Ctrl + Alt + T`, or by clicking the nine dots at the bottom-left, typing "terminal", and clicking on the app icon. From the file explorer, you can also open the terminal in the current directory by right-clicking and clicking "Open in Terminal". Useful commands:

- `pwd`: Get the current directory.
- `cd <dir>`: Change to directory `dir`. For example, `cd Desktop` enters the `Desktop` directory.
- `ls -a`: List all files/subdirectories in the current directory, including hidden files.
- `./<file>`: Runs the executable `file`. For example, `./build.sh` runs the shell script `build.sh`.
- `xdg-open <file>`: Simulate double-clicking on `file` in the file explorer. For example, `xdg-open quiz.pdf` opens `quiz.pdf` with your PDF viewer.

The tilde (`~`) is a special path referring to your home directory (`/home/<username>`). Your desktop is located at `~/Desktop`.

Each directory also has two special paths: `.`, which points back to the same directory, and `..`, which points to the parent directory. So, for example, you can "go out" of a directory by typing `cd ..`.

Here’s an example of basic command line usage:

```
user@ubuntu:~$ ls -a
.              .bashrc  Documents  Music     .ssh
..             .cache   Downloads  Pictures  .sudo_as_admin_successful
.bash_history  .config  .gnupg     .profile  Templates
.bash_logout   Desktop  .local     Public    Videos
user@ubuntu:~$ cd Documents
user@ubuntu:~/Documents$ xdg-open .
```

In this example, the user starts out in the home directory (as seen by the tilde in the prompt), lists all files/subdirectories (`.` and `..` are the two special paths), changes into the Documents directory, then opens `.` (i.e. the current directory, `~/Documents`) in the file explorer. Try it yourself!

You can copy and paste in the terminal by selecting with the mouse and right-clicking. A keyboard shortcut for pasting is `Ctrl-Shift-V`.
