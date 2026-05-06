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

[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 

**Why they need protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
