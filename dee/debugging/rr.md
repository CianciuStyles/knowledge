---
description: https://rr-project.org
---

# rr

### Basic Recording

```bash
# Record a program with no arguments
rr record ./my_program
 
# Record a program with arguments and environment variables
rr record ./my_program arg1 arg2
 
# Record and pass stdin to the recorded process
echo "input data" | rr record ./my_program
 
# Record with extra environment variables set for the child process
rr record --env MY_VAR=value -- ./my_program
 
# Record, giving the trace a human-readable directory name
rr record --output-trace-dir /tmp/my_trace_dir -- ./my_program
 
# Record a program that needs to run as root (use sudo INSIDE rr, not outside)
rr record -- sudo ./my_program
 
# Record a program that forks / spawns children (rr follows all children)
rr record ./server_with_workers
 
# Record a test suite binary (e.g., Google Test); useful to pin a flaky test
rr record ./my_test_binary --gtest_filter=MySuite.MyTest
 
# Record with chaos mode enabled: randomises scheduling to expose races
rr record --chaos -- ./my_program
 
# Record with a specific number of CPUs visible to the tracee (default: 1)
rr record --num-cores=4 -- ./my_program
 
# Record and save to a specific trace directory explicitly
rr record -o /tmp/traces/run1 -- ./my_program
```

### Listing & Inspecting Traces

```shellscript
# List all locally saved rr traces (newest first)
rr ls
 
# Show detailed info about the most recent trace
rr dump
 
# Show detailed info about a specific trace directory
rr dump /tmp/traces/run1
 
# Dump raw event log for offline analysis (pipe to less / grep)
rr dump --raw /tmp/traces/run1 | less
 
# Show the command line that was recorded
rr dump | grep "^args"
```

### Replay

```bash
# Replay the most recent trace (runs to completion without stopping)
rr replay
 
# Replay a specific trace directory
rr replay /tmp/traces/run1
 
# Replay and confirm it exits with the same code as the original run
rr replay --check-exit-status

# Open the most recent trace in GDB for interactive time-travel debugging
rr replay -d gdb
 
# Open a specific trace in GDB
rr replay -d gdb /tmp/traces/run1
 
# Pass extra GDB flags at launch (e.g., load a .gdbinit script)
rr replay -d gdb -- -x /home/user/.gdbinit_rr /tmp/traces/run1
```

### Time Travel GDB Commands

#### Navigation

```
# Run forward to the next breakpoint / end of trace
(gdb) continue
 
# Step one source line forward
(gdb) next
 
# Step into a function call (forward)
(gdb) step
 
# Step one machine instruction forward
(gdb) stepi
 
# Run BACKWARDS to the previous breakpoint or start of trace
(gdb) reverse-continue
 
# Step one source line BACKWARDS (undo the last line)
(gdb) reverse-next
 
# Step BACKWARDS into the caller of the current function
(gdb) reverse-step
 
# Step one machine instruction BACKWARDS
(gdb) reverse-stepi
 
# Run until the current function returns (forward)
(gdb) finish
 
# Run BACKWARDS until the current function was called
(gdb) reverse-finish
```

#### Breakpoints & Watchpoints

```
# Set a normal breakpoint at a function
(gdb) break my_function
 
# Set a breakpoint at a specific file/line
(gdb) break src/main.c:42
 
# Set a hardware watchpoint — stop when the value of a variable CHANGES
(gdb) watch my_variable
 
# Set a read watchpoint — stop when a variable is READ
(gdb) rwatch my_variable
 
# Set a watchpoint on a memory address
(gdb) watch *(int *)0x7fffffffd000
 
# Set a conditional breakpoint
(gdb) break my_function if x == 0
```

#### Event / Tick Navigation

```
# Show the current replay "event" number (logical timestamp)
(gdb) rr when
 
# Show the current "tick" (hardware performance counter step, finer grain)
(gdb) rr when-ticks
 
# Jump directly to a specific event number (fast seek)
(gdb) rr goto-event 1234
 
# Jump to a specific tick within the current event
(gdb) rr goto-ticks 5678
 
# Show all recorded events for the current process
(gdb) rr dump-on-exit
```

#### Checkpoints

```
# Save a checkpoint at the current point in the replay
(gdb) checkpoint
 
# List all saved checkpoints
(gdb) info checkpoints
 
# Restore execution to a specific checkpoint (by id)
(gdb) restart 1
 
# Delete a checkpoint to free memory
(gdb) delete checkpoint 1
```

#### Inspecting State

```
# Print a variable (works both forward and backward)
(gdb) print my_var
 
# Examine 16 words of memory at an address
(gdb) x/16xw 0x7fffffffd000
 
# Print a backtrace at the current replay point
(gdb) backtrace
 
# Switch to a specific thread
(gdb) thread 2
 
# List all threads in the recording
(gdb) info threads
 
# Display the assembly around the current instruction
(gdb) disassemble
```

### Using rr with Rust

```bash
# Record a Rust binary built by cargo
rr record ./target/debug/my_crate
 
# Record cargo test execution (single test thread to keep trace deterministic)
rr record cargo test -- --test-threads=1
 
# Replay with rust-gdb for better Rust type pretty-printing
rr replay -d rust-gdb
```

### Performance & Tracing Options

```bash
# Disable syscall buffering (makes recording slower but more accurate for
# debugging issues with the syscall buffer itself)
rr record --no-syscall-buffer -- ./my_program
 
# Set a maximum recording time in seconds (stops the trace after N seconds)
rr record --timeout=30 -- ./my_program
 
# Record only the first N events then stop (useful for huge long-running traces)
rr record --max-events=100000 -- ./my_program
 
# Enable verbose output from rr itself (for diagnosing rr internals)
rr record -v -- ./my_program
 
# Measure overhead: compare wall-clock time of native vs recorded run
time ./my_program
time rr record ./my_program
```
