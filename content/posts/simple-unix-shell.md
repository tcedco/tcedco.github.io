---
title: "Codects: Simple UNIX Shell"
layout: post
outputs: [html, json]
categories: [linux, systems programming]
date: 2024-08-24
---

Welcome back to *Codects*, where I dive into coding projects each week to learn something new or just explore cool tech. This week’s project took me on a journey into the heart of the UNIX operating system: building a Unix shell from scratch using C. The project was both challenging and rewarding, offered me a deeper understanding of how command-line interpreters work.

## Why Build a UNIX Shell?

Building a UNIX shell is like constructing the bridge between the user and the operating system. The shell allows us to interact with the OS by typing commands, which the shell interprets and executes. Using the command line is also the best alternative to GUIs, since the command line is very versatile when it comes to basically anything realted to system administration, programming, and operating systems. This project was not only about writing code but also about understanding the under-the-hood operations of an OS. Plus, it’s a great way for me to sharpen my C programming skills while learning about process management, system calls, and more.

## The Plan

I broke down the project into a few key steps:

1. **Reading Commands**: The shell needs to take input from the user.
2. **Parsing Commands**: After reading, the shell needs to break down the input into commands and arguments.
3. **Executing Commands**: Finally, the shell must execute the parsed commands and loop back for the next one.

I aimed to keep the shell simple but functional, supporting basic commands like **`ls`**, **`pwd`**, and **`echo`**, along with a few built-in commands like **`cd`**, **`exit`**, and **`help`**.

### Step 1: Reading Commands

The first challenge was to get the shell to read user input. This sounds simple, but there’s more to it than just grabbing a string. I needed to ensure the shell could handle large inputs and trim off any unnecessary characters like newlines. Here’s how I tackled it:

```c
void read_command(char *command)
{
    if (fgets(command, MAX_COMMAND_LENGTH, stdin) == NULL) {
        perror("fgets failed");
        exit(EXIT_FAILURE);
    }

    command[strcspn(command, "\n")] = '\0';
}
```

The **`fgets`** function reads the input, but it includes the newline character, which I removed using **`strcspn`**. This was a small but crucial step to ensure that commands are processed correctly.

### Step 2: Parsing Commands

Next up was parsing the command. The goal here was to break the user input into the command itself and its arguments. I used **`strtok`** to split the input string:

```c
void parse_command(char *command, char **args)
{
    int index = 0;
    args[index] = strtok(command, " ");

    while (args[index] != NULL) {
        args[++index] = strtok(NULL, " ");
    }
}
```

This step was tricky because I had to think about edge cases, like what happens when the user enters multiple spaces or no arguments at all. Parsing correctly is vital since the shell needs to know exactly what the user wants to do.

### Step 3: Executing Commands

The heart of the shell is in executing commands. This is where the magic happens. I used the **`fork`** and **`execvp`** system calls to create a child process for running commands, while the parent process waited for the command to finish:

```c
void execute_command(char **args)
{
    pid_t pid = fork();

    if (pid == -1) {
        perror("fork failed");
        exit(EXIT_FAILURE);
    }

    else if (pid == 0) {
        if (execvp(args[0], args) == -1) {
            perror("execvp failed");
        }

        exit(EXIT_FAILURE);
    }

    else {
        wait(NULL);
    }
}
```

Handling process creation and execution was a big learning curve. Understanding how **`fork`** creates a new process and how **`execvp`** replaces the current process image with a new one was a deep dive into OS concepts. One of the most challenging parts was ensuring that the shell correctly waited for child processes to finish, which involved careful management of **`wait()`**.

## Adding Built-in Commands

In a real shell, commands like **`cd`** and **`exit`** aren’t external programs but are built right into the shell. I added these commands to my shell to give it more functionality:

```c
if (strcmp(args[0], "cd") == 0) {
    if (chdir(args[1]) != 0) {
        perror("helioshell");
    }
}

else if (strcmp(args[0], "exit") == 0) {
    exit(0);
}

else if (strcmp(args[0], "help") == 0) {
    printf("Supported commands:\n");
    printf("  cd <directory>\n  exit\n  help\n");
}
```

The **`cd`** command was particularly interesting because it directly interacts with the shell’s process. Unlike other commands that are executed as separate processes, **`cd`** changes the current directory of the shell itself. This required handling the command internally rather than through **`execvp`**.

## The Challenges

The biggest challenge was managing edge cases and ensuring the shell handled unexpected inputs gracefully. For example, what happens when the user tries to change to a directory that doesn’t exist? Or what if they run a command without any arguments? These were scenarios I had to account for to make the shell robust.

Another challenge was understanding C all together. I had some experience before had with languages similar to C like Java and C++, but C was more different between the three, as Java and C++ were built off of C. I also needed to understand how **`fork`** and **`execvp`** work together. Initially, I had issues with the child process not executing properly or the shell hanging because it wasn’t waiting correctly. Debugging these issues taught me a lot about process control in UNIX.

## What I Learned?

Although I have experience with command lines for years now as a Sysadmin, building a UNIX shell from scratch gave me a much deeper understanding of how command-line interfaces work. I gained hands-on experience with process management, system calls, and the works of C programming. It was also a lesson in debugging and testing, as ensuring the shell behaved correctly in all situations was no small task.

## The Next Steps

There’s so much more that can be added to this shell. Features like piping, input/output redirection, and background execution are all on the table for future updates. But for now, I’m happy with the progress and the solid foundation I’ve built.

You can find the project [here](https://github.com/Tcedco/simple-unix-shell)... and that's it for this week, until next time!