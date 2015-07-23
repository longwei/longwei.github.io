---
layout: post
title: How to make a debugger
---

When we debug, we want to forgot about all security issue, and just want to peek the state of running applicaion. well, first thing not to do is to put debugger and debuggie to same memory space:) so they have to in different process.

but kernel is the big brother watching and know everything, and it has a syscall ptrace.


the black magic is ptrace
---
Remember, gdb don't work like valgrind, which reading and interpreting binary instruction and simulate execution.
but this way it will slow the application down.

the key point here is in linux:
parent process can get additional information about their children, and can ptrace it. so the debugger will be the debugger process, and processes and adpot a child.

what if the process if not debugger's children, reparent it :)
so the magic is the ptrace on its children process.


if we attach to existing process it receives SIGSTOP and we receive SIGCHLD once it actually stops. If we spawn a new process and it did ptrace( PTRACE_TRACEME ) it will receive SIGTRAP signal once it attempts to exec() or execve(). We will be notified with SIGCHLD about this, of course.

```
ptrace(P_TRACE_TRACEME,pid,0,0)
....
ptrace(P_TRACE_ATTACH,pid,0,0)
int status;
waitpid(pid, &status, WSTOPPED);
while (...) {
    ptrace(PTRACE_SINGLESTEP, pid, 0, 0);
    // do something here
}
ptrace(PTRACE_DETACH, pid, 0, 0);

```
ptrace allow parent to rw momory, cpu register, system events, and control of its execution and signal handling.

 * read/write to memory of the target process with PTRAC_PEEKDATA and PTRACE_POKEDATA
 * read/write to register of the target process with PTRACE_GETREGSET and PTRACE_SETREGS
 * fake notified of system events PTRACE_O_TRACEEXEC, PTRACE_SYSCALL
 * control its execution: PTRACE_SINGLESTEP, PTRACE_KILL, PTRACE_INTERRUPT, PTRACE_CONT
 * alter its signal handling: PTRACE_GETSIGINFO, PTRACE_SETSIGINFO
 

Q:why ptrace can do it?
A: because Ptrace is part of kernel, it have access to all information about this process.

Q: can I debugg without symbol tables?
A: yes, but you will only see what CPU see, set of assembly instruction and bit array. ```-g``` will mapping address to line, data type definition, and bookkeeping variabes's name ```dwarfdump``` and ```addr2line```


Q: how is next implementated?
A: it set a breakpoint at the address of next line, then print variables in right type


Q: how about break point? Is it part of ptrace?

A: No.

	1. debugger write a invalid instruction at this location
	2. when the application reach the invalid instruction, it will not crashed but give a control back via a interrupt, 
	3. then in the SIGTRAP signal is sent to process, then to its parent, our debuger
	4. then if the ip is breakpoint list, then we got to a break point.
	5. write correct instruction back and single step it.


real code example [ref](http://www.alexonlinux.com/how-debugger-works)

```
int main()
{
	pid_t child;

	// Creating child process. It will execute do_debuggie().
	// Parent process will continue to do_debugger().
	child = fork();
	if (child == 0)
		do_debuggie();
	else if (child > 0)
		do_debugger(); 
	else
	{
		perror( "fork" );
		return -1;
	}

	return 0;
}
```

```
void do_debuggie( void )
{
	char* argv[] = { "/tmp", NULL };
	char* envp[] = { NULL };
	
	printf( "In debuggie process %ld\n", (long)getpid() );

	if (ptrace( PTRACE_TRACEME, 0, NULL, NULL ))
	{
		perror( "ptrace" );
		return;
	}

    //this is real debugger do to spawn the process to be debugged
    //execve() replace current executable imge and memory of current process with execve()d
    //once done, it send SIGTRAP to calling process(ls) and SIGCHLD to debugger
	execve( "/bin/ls", argv, envp );
}
```


```
void do_debugger( void )
{
	int status = 0;
	pid_t child;

	printf( "In debugger process %ld\n", (long)getpid() );

	if (signal( SIGCHLD, signal_handler ) == SIG_ERR) 
	{
		perror( "signal" );
		exit( -1 );
	}

	do {
		child = wait( &status );
		printf( "Debugger exited wait()\n" );
		if (WIFSTOPPED( status ))
		{
			printf( "Child has stopped due to signal %d\n", WSTOPSIG( status ) );
		}

		if (WIFSIGNALED( status ))
		{
			printf( "Child %ld received signal %d\n", (long)child, WTERMSIG(status) );
		}
	} while (!WIFEXITED( status ));
}
```
output

```
longwei@ubuntu:~/ClionProjects/build$ ./debugger 
In debugger process 18294
In debuggie process 18295
Process 18294 received signal 17
Debugger exited wait()
Child has stopped due to signal 5
^C
```


