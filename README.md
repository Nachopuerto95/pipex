<p align="end">
   <strong>🌐 Change language:</strong><br>
   <a href="README.es.md">
    <img src="https://github.com/Nachopuerto95/multilang/blob/main/ES.png" alt="Español" width="50">
  </a>&nbsp;&nbsp;&nbsp;
  <a href="/README.md">
    <img src="https://github.com/Nachopuerto95/multilang/blob/main/EN.png" alt="English" width="50">
  </a>
</p>

# Pipex (42cursus)

<img src="https://github.com/Nachopuerto95/multilang/blob/main/42-Madrid%20-%20Edited.jpg">

## 📜 About Project

> Recreate the behaviour of a shell pipeline: `< infile cmd1 | cmd2 > outfile`.

```html
	🚀 This project is a hands-on dive into UNIX process creation,
	file descriptors and inter-process communication. Fork, execve,
	pipe and dup2 are the four system calls you'll really get to know.
```

> [!NOTE]
> Because of 42 School norm requirements:
> * Each function can't have more than 25 lines of code.
> * All variables are declared and aligned at the top of each function.
> * Only a limited set of allowed functions can be used.

### 📌 Description

Pipex reads from an input file, runs it through a first command, pipes the output to a second command, and finally writes the result to an output file — exactly what the shell does when you type:

```bash
< infile cmd1 | cmd2 > outfile
```

The program has to build that pipeline from scratch, without relying on the shell to do it for you. This repo implements the mandatory part of the project in C.

### 🚀 What I worked on

- Parsing `argv` to locate input file, two commands and output file.
- Resolving each command against the `PATH` environment variable.
- Creating a single pipe and forking one child process per command.
- Duplicating file descriptors with `dup2` so each command reads from and writes to the right place.
- Handling errors for missing files, commands not found and invalid PATH.

### 🔧 How to Use

Compilation:

```bash
make
```

Execution:

```bash
./pipex infile "cmd1" "cmd2" outfile
```

Example — equivalent of `< file.txt grep hello | wc -l > out.txt`:

```bash
./pipex file.txt "grep hello" "wc -l" out.txt
```

### 🧠 How it works

```
           ┌──────────┐     pipe     ┌──────────┐
 infile ──►│  child 1 │──────────────►│  child 2 │──► outfile
           │  cmd1    │   fd[1]→fd[0]│  cmd2    │
           └──────────┘               └──────────┘
```

1. `main` opens `infile` for reading and `outfile` for writing (creating/truncating it).
2. `pipe()` creates the communication channel between both processes.
3. The first child redirects its `stdin` to `infile` and its `stdout` to the write end of the pipe, then `execve`s `cmd1`.
4. The parent redirects its `stdin` to the read end of the pipe and its `stdout` to `outfile`, then `execve`s `cmd2`.
5. `waitpid` waits for the first child before exiting.

### 📂 Project layout

```
pipex/
├── pipex.c         # main, fork/exec logic
├── pipex_utils.c   # PATH resolution, error handling
├── pipex.h
├── libft/          # required helpers (split, strjoin, strdup…)
└── Makefile
```

### ⚠️ Scope

This implementation covers the **mandatory** part only. It takes exactly two commands: no chained pipes (`cmd1 | cmd2 | cmd3 | …`) and no `here_doc` bonus.
