# Assignment 3 - Complete Documentation

**Student Name**: [layan alharbi]  
**Student ID**: [445052068]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [may 3 2026, 6;00pm]
**What I implemented**: 
set my student id 
**Challenges encountered**: 
nothing
**How I solved it**: 
didnt face any challenges
**Testing approach**: 
ran the code  to verify that the simulation header correctly displayed my student ID
**Time spent**: 
10 minutes
---

### Entry 2 - [may 6 2026, 1;30 am]
**What I implemented**: 
added reentrantLock to protect your counter variables
**Challenges encountered**: 
nothing
**How I solved it**: 
didnt face any
**Testing approach**: 
Verified that the totalWaitingTime and contextSwitchCount were consistent across runs, ensuring no increments were "lost" due to race conditions.
**Time spent**: 
30 minutes
---

### Entry 3 -[may 6 2026, 2;30 am]
**What I implemented**: 
added accuire and release cpu semaphore in the run method
**Challenges encountered**: 
nothing
**How I solved it**: 

**Testing approach**: 
Checked for "Deadlocks" by ensuring the program always reached the "ALL PROCESSES COMPLETED" message without hanging.
**Time spent**: 
30 minutes
---

### Entry 4 - [may 6 2026, 3;00am]
**What I implemented**: 
implement synchronizaition in runToCompletion method
**Challenges encountered**: 
nothing
**How I solved it**: 

**Testing approach**: 
Confirmed that the "Total Completed Processes" displayed in the final summary table matched the total number of processes generated at the start of the simulation.
**Time spent**: 
30 minutes
---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[The first race condition affects shared counters like contextSwitchCount, where concurrent read-modify-write operations by multiple threads lead to "lost updates". The second affects the executionLog because ArrayList is not thread-safe; concurrent access causes data corruption or ConcurrentModificationException. These issues result in inconsistent final statistics, such as incorrect average waiting times or incomplete execution logs. Without synchronization, the scheduler produces unreliable, non-deterministic outputs that fail to reflect the true state of the simulation.

code example
ublic static void incrementContextSwitch() {
    contextSwitchCount++; // RACE CONDITION: Multiple threads read/write at once!
}

public static void logExecution(String message) {
    executionLog.add(message); // RACE CONDITION: Can crash or lose log entries
}]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock (The "Lock"): This is a mutual exclusion mechanism. It ensures that only one thread can access a specific piece of data at a time. I used it in SharedResources to protect the counters and the execution log because these involve individual data updates that must be completed without interference.

Semaphore (The "Signaler"): This is a resource controller that uses permits. It is used to manage access to a limited resource. I used a binary semaphore in the Process class to represent the CPU, ensuring that only one process can "run" its execution logic at any given moment.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[deadlock is a "traffic jam" in your code where two or more threads are stuck forever because each is holding a resource (like a lock) that the other needs. Neither can move forward, and the program freezes.

Technique 1: Guaranteed Release: I used try-finally blocks for every lock and semaphore. By putting .unlock() or .release() in the finally block, I ensured the resource is always freed, even if the code crashes or is interrupted.

Technique 2: Fine-Grained Locking: Instead of using one big lock for everything, I used separate locks for different variables (e.g., contextSwitchLock for the counter and logLock for the list). This prevents threads from blocking each other when they are working on unrelated tasks..]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[Choice: Fine-Grained Locking: I implemented separate locks (contextSwitchLock, completedProcessLock, and waitingTimeLock) for each individual counter instead of using a single global lock for all of them.

Reasoning: Since these counters are independent and updated at different times, there is no reason for one process updating the "waiting time" to block another process that is simply trying to increment the "context switch" count.

Trade-offs:

Coarse-grained (One Lock): Simpler to write but creates a bottleneck where threads wait unnecessarily.
Fine-grained (Separate Locks): Slightly more complex but significantly faster in a multithreaded environment.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, and totalWaitingTime.
**Why they need protection**: 

hese variables are shared across multiple process threads. In Java, operations like ++ or += are not atomic; they involve reading, modifying, and writing the value back. Without protection, two threads could read the same value simultaneously, causing one update to overwrite the other, leading to "lost updates" and incorrect final statistics.
**Synchronization mechanism used**: 
ReentrantLock (Fine-grained approach with separate locks for each variable
contextSwitchLock, completedProcessLock, and waitingTimeLock).
**Code snippet**:
```java
public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}
```

**Justification**: 
I used the ReentrantLock to ensure mutual exclusion, meaning only one thread can modify a specific counter at any given time. I chose separate locks (fine-grained) for each counter to allow higher concurrency; this ensures that a thread updating the waiting time doesn't unnecessarily block a different thread that needs to increment the context switch count.
---

### Critical Section #2: Execution Log

**What resource**: 
The executionLog, which is a shared ArrayList<String>.
**Why it needs protection**: 
The ArrayList class is not thread-safe. When multiple process threads attempt to call the .add() method simultaneously, it can lead to a ConcurrentModificationException or data corruption where log entries are overwritten or lost. Without protection, the final history of the simulation would be incomplete or the program could crash during execution.
**Synchronization mechanism used**: 
ReentrantLock (specifically the logLock defined in SharedResources).
**Code snippet**:
```java
public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}
```

**Justification**: 
I used a dedicated ReentrantLock to ensure that only one thread can modify the list at any given time. By wrapping the .add() operation in a try-finally block, I guarantee that the lock is released even if an error occurs. Using a specific lock for the log (fine-grained locking) ensures that logging activities do not block other threads that are merely updating numerical counters.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
The semaphore acts as a gatekeeper for the CPU resource. It ensures that although multiple process threads are active, only a specific number can "execute" their code simultaneously, simulating the physical limitation of processor cores.
**Number of permits and why**: 
1 permit (Binary Semaphore). This is used to simulate a single-core CPU environment, ensuring that only one process can occupy the "running" state at any given time, preventing overlapping execution logs and maintaining the logic of the round-robin scheduler.
**Where implemented**: 
It is implemented in the Process class within both the run() and runToCompletion() methods.
**Code snippet**:
```java
@Override
public void run() {
    try {
        SharedResources.cpuSemaphore.acquire(); // Acquire permit before execution
        try {
            // ... process execution logic ...
            Thread.sleep(runTime); 
        } finally {
            SharedResources.cpuSemaphore.release(); // Ensure permit is released
        }
    } catch (InterruptedException e) {
        System.out.println(name + " was interrupted.");
    }
}
```

**Effect on program behavior**: 
The semaphore forces threads to wait in a blocked state until the CPU permit becomes available. This eliminates overlapping terminal output where multiple processes might otherwise print their progress bars simultaneously, resulting in a clean, sequential execution that accurately reflects a real-world scheduling algorithm.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results
Running the program multiple times to verify that final statistics (total waiting time, average waiting time, and completed process count) remain consistent and accurate across different executions.
**Testing procedure**: 
```bash
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(In all 5 test runs, the "Total Completed Processes" consistently matched the initial number of processes generated (based on the student ID seed). The "Average Waiting Time" remained identical across runs with the same parameters, and the terminal output showed a clean, sequential execution without overlapping progress bars or scrambled log entries.)

**Why synchronization is necessary**: 
(Without synchronization the shared counter completedProcessCount and the accumulator totalWaitingTime are vulnerable to lost updates Because operations like ++ are not atomic, two threads could read the same value and write back the same increment resulting in a final count that is lower than the actual number of processes finished. Additionally the executionLog (ArrayList) would eventually throw a ConcurrentModificationException if two threads tried to add a log entry at the exact same millisecond. Synchronization ensures that these shared memory locations are accessed by only one thread at a time, maintaining data integrity)

**Conclusion**: 
The implementation of ReentrantLock and Semaphore successfully eliminated non-deterministic behavior. The program now produces reliable, repeatable results, proving that the race conditions have been resolved and the shared resources are properly protected.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException
Verified the stability of the executionLog (ArrayList) under high-concurrency conditions to check for ConcurrentModificationException or data loss.
**Testing procedure**: 
Modified the SchedulerSimulationSync.java temporarily to increase the number of processes to the maximum range allowed by the simulation (around 20-25 processes).

Reduced the Thread.sleep intervals in the Process class to force faster, overlapping log entries.

Ran the simulation and checked the console for any runtime exceptions during the final "Execution Log Summary" printing phase.
**Results**: 
The program executed without any ConcurrentModificationException. The "Total log entries" count in the final statistics was consistent with the expected number of start,yield, and completion events for the given number of processes.
**What this proves**: 
This proves that the logLock (ReentrantLock) is successfully providing mutual exclusion for the executionLog. In the original unsynchronized code, multiple threads calling .add() on a shared ArrayList would likely cause the internal structure of the list to become corrupted or throw an exception during iteration. By protecting the list with a lock, I ensured that every log entry is recorded safely
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)
Verified that the final numerical statistics (Total Completed Processes, Total Context Switches, and Average Waiting Time) accurately reflect the simulated workload and adhere to the scheduling logic
**Expected values**: 
Total Completed Processes: Must exactly equal the number of processes initially generated (determined by the Student ID seed).

Total Context Switches: Should match the total number of times processes yielded or were swapped out before finishing.

Total Waiting Time: Should be a positive sum representing the time processes spent in the queue, divided by the number of processes for the average.
**Actual values**: 
Total Completed Processes: [12]

Total Context Switches: [29]

Average Waiting Time: [64814ms]
**Analysis**: 
The actual values match the expected logical outcomes of the simulation  Because the Total Completed Processes count matches the 12 processes generated it proves that no thread was lost due to race conditions. The 29 context switches were captured correctly because the fine-grained contextSwitchLock prevented overlapping writes. the 64814ms total waiting time is consistent across multiple runs confirming that the synchronization implementation for the totalWaitingTime accumulator is robust and mathematically precise. This data proves that the scheduler is both thread-safe and logically sound.
---

### Test 4: Different Scenarios
**Scenario tested**: [Semaphore(1) to Semaphore(2) temporarily.]

**Purpose**: Observe effect of allowing two concurrent processes

**Results**: With 2 permits, execution overlapped (interleaving in output). Still no race
conditions because counters remained protected.
What I learned: Semaphores are extremely flexible – they control the degree of
concurrency without changing the core logic

**What I learned**: 
[Semaphores are extremely flexible – they control the degree of
concurrency without changing the core logic]
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[Race conditions are subtle: the code can run correctly many times and then suddenly
fail. Synchronisation makes concurrent programs predictable.
Fine‑grained locking is powerful: protecting independent resources with separate
locks unlocks real parallelism.
The try-finally pattern is non‑negotiable – forgetting to unlock in a finally block
leads to deadlocks that are very hard to debug.
A binary semaphore ( Semaphore(1) ) is functionally similar to a mutex, but a mutex
(ReentrantLock) is usually preferred for mutual exclusion because it provides
ownership and reentrancy.
Synchronisation adds overhead, but the safety it buys is essential for any
multithreaded program
]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
1. Banking systems – When multiple tellers update the same account balance, locks
prevent lost deposits or withdrawals.
**Example 2**: 
2. Print spooler – A semaphore with a limit equal to the number of printers controls
access to physical printers
---

### How I would explain synchronization to others:

[ When you have multiple threads running at the same time  they all try to grab and change the same data at once This causes race conditions where the numbers get messed up because two threads colided

To keep things organized I used:
Locks: Only one thread can hold the key at a time to change a counter so no one else can mess with it until they're done.

Semaphores:  It limits how many threads can use the CPU at once so the program doesn't crash or overlap.

Basically synchronization makes sure that no matter how fast or messy the threads are the final answer is always correct and consistent. ]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/layanalharbi068/OS-Assignment3-Starter_layan_alharbi

**Number of commits**: 7

**Commit messages**: 
1. set my student ID:445052068
2. Added reentrantlock toprotect counter variables
3. added acuire and release CPU semaphore in run method
4. implement synchornizaiton in runToCompletion method

---

## Summary

**Total time spent on assignment**: 
4 hours
**Key takeaways**: 
1. . Fine‑grained locking improves performance for independent resources
2. try-finally is the only safe way to release locks
3. . A semaphore can control both mutual exclusion and resource limits

**Most challenging aspect**: 
: Deciding on lock granularity and proving that separate locks
are safe 
**What I'm most proud of**: 
 The final program runs deterministically, with no exceptions,
and the code clearly shows why each synchronisation mechanism is used
---

**End of Documentation**
