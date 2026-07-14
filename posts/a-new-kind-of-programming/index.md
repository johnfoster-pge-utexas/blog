<!--
.. title: A new kind of programming
.. slug: a-new-kind-of-programming
.. date: 2026-07-14 11:00:00 UTC-05:00
.. tags: programming, education, agents, artificial intelligence
.. category: Education
.. link:
.. description: Teaching programming concepts in an age where agents can make contextual decisions, manage information, and carry out recurring work.
.. type: text
-->

# A new kind of programming

For decades, when we taught programming, we began with a reassuringly concrete picture of computation. A program was a sequence of instructions. An `if` statement asked whether something was true or false and then followed one branch or another. A `for` loop executed a known block of work a known number of times. A `while` loop continued until some condition changed. These ideas were, and still are, powerful because they separate the structure of thought from the details of a particular programming language.

But the programming environment around us is changing quickly. We now have computational agents that can read files, inspect context, make judgments, call tools, write code, revise their own plan, and come back later to continue a task. These agents do not remove the need for programming fundamentals. In fact, they make those fundamentals more important. What changes is where the fundamental concepts show up.

An `if` statement no longer has to mean only:

```python
if pressure > fracture_pressure:
    reduce_timestep()
else:
    continue_simulation()
```

That is still programming. It is precise, deterministic, and often exactly what we want. But in an agentic workflow, the decision point may look more like this:

```text
If this request is about manuscript notation, read main.tex and defs.tex.
If it is about implementation, read the MOOSE traceability file.
If it is about validation, read the validation matrix.
Then proceed with only the context needed for the task.
```

The underlying idea is the same: evaluate a condition and choose a path. The difference is that the condition may not be a single Boolean expression. It may be a judgment about intent, scope, risk, or relevance. The action may not be one function call, but a controlled expansion of context. The branch may decide which files to read, which tools to use, which checks to run, or which expert workflow to activate.

This is not merely a convenience. It is becoming one of the central problems in programming with large language models: context management. An agent can be surprisingly capable when it has the right information in front of it, and surprisingly confused when it has too much irrelevant information mixed with the important parts. Traditional programs manage memory and control flow. Agentic programs must also manage attention.

In one of our current research repositories on multicomponent reactive flow, for example, we have started encoding project knowledge in files that are meant to be read by agents: `AGENTS.md`, decision trees, checklists, framework notes, validation matrices, and runbooks. These are not just documentation in the old sense. They are part of the execution environment. They tell an agent what to inspect first, how to classify a task, when to ask for clarification, what source of truth controls a decision, and how to validate a change before calling it done.

That makes an agent instruction file feel very much like a higher-level control-flow structure. A decision tree is an `if` statement written for a system that can interpret language. A checklist is a guard against skipping necessary state. A runbook is a structured exception handler. A validation matrix is a set of assertions about what must be true before we trust the result.

The analogy extends to loops. In introductory programming, we might write:

```python
for assignment in assignments:
    grade(assignment)
```

or:

```python
while not converged:
    update_solution()
```

These examples teach the key idea that some work is repeated under a rule. In agentic systems, repetition may not live inside a single running process. It may be scheduled:

```text
Every morning, check the calendar, weather, and urgent messages.
Every five minutes, inspect the status of a long-running simulation.
Every day, summarize new repository activity and flag anything that needs attention.
```

A cron job or scheduled agent task is, conceptually, a loop with a clock attached. It repeatedly wakes an agent, gives it a bounded job, and lets it decide what to do with the current state of the world. A `while` loop appears when the agent keeps checking until a condition is satisfied:

```text
While the simulation is still running, check the logs every five minutes.
When it finishes, summarize the result and remove the scheduled check.
```

That last step matters. The loop has its own termination condition. Once the task is complete, the agent removes the recurring job and the cycle ends. This is not identical to a `while` loop in Python or C, but pedagogically it is the same idea: repeat work until the condition that justified the repetition is no longer true.

This suggests a useful shift in how we teach programming. We should still teach syntax. Students still need to know what an `if` statement, a `for` loop, a function, a variable, and a data structure look like in an actual language. But syntax should not be mistaken for the concept. The concept behind an `if` statement is a decision point. The concept behind a loop is repeated work under a rule. The concept behind a function is a named unit of reusable behavior. The concept behind a module is a boundary around related knowledge and capability.

Once students understand those ideas in simple code, we can show them how the same ideas appear in agentic systems:

- A branching statement becomes an instruction that routes the agent to the right context.
- A loop becomes a scheduled task, a monitoring process, or a repeated evaluation until completion.
- A function becomes a tool the agent can call with a clear contract.
- A module becomes a directory of prompts, schemas, examples, tests, and source files that define a capability.
- A test becomes not only an assertion about code, but evidence that the agent's work preserved the intended behavior.

This matters for educators because our students are likely to write less code from a blank screen and more code in collaboration with machines. They will still need to reason clearly. They will still need to decompose problems, define interfaces, recognize edge cases, and verify results. But they will also need to learn how to shape the environment in which an agent works. They will need to decide what context belongs in the prompt, what belongs in a file, what should be retrieved only when needed, and what should never be placed in context at all.

In that sense, prompt engineering is too narrow a phrase. The more durable skill is context engineering: designing the information, tools, constraints, and feedback loops that allow an agent to work reliably. A good agent workflow is not just a clever prompt. It is a small operating system for a task. It has routing rules, sources of truth, permissions, validation steps, memory, and cleanup.

There is an important caution here. We should not teach students that agents are magic decision makers. When a conventional `if` statement evaluates `x > 0`, we know exactly what is being tested. When an agent decides whether a request is about notation, implementation, or validation, that decision is probabilistic and interpretive. It can be wrong. Therefore agentic control flow needs stronger guardrails than ordinary control flow. We need explicit sources of truth, narrow task scopes, checklists, tests, and human review for consequential actions.

But that caution is not a reason to ignore the shift. It is a reason to teach it well. If we pretend programming is only the syntax of a single language, we will prepare students for a world that is already passing away. If we teach the deeper computational ideas, then show how those ideas reappear in agentic workflows, students can carry their understanding forward.

The future programming course might begin exactly as it does now:

```python
if condition:
    do_this()
else:
    do_that()
```

Then it might ask:

```text
If the task is ambiguous, identify what information would change the answer.
If the task is well-scoped, read only the files needed to complete it.
If the task changes user-visible behavior, run the relevant tests before reporting success.
```

The first example teaches syntax. The second teaches judgment. Both are programming.

Likewise, we might begin with:

```python
for i in range(10):
    print(i)
```

Then move to:

```text
Every hour, check whether the experiment has produced new output.
If new output exists, summarize it and update the lab notebook.
If the experiment is complete, stop checking.
```

Again, the concept is repetition under a rule. The implementation has changed because the computational substrate has changed. The program is no longer only a process running in memory. It may be a set of files, tools, scheduled jobs, agent instructions, and review steps distributed across time.

I do not think this makes traditional programming obsolete. Quite the opposite. The more powerful our tools become, the more important it is to understand the structures underneath them. We should keep teaching `if` statements and loops, but we should teach them as instances of broader ideas: decision, repetition, abstraction, state, verification, and termination.

Programming with agents is still programming. It is just programming at a different level of abstraction, where the objects being controlled are not only numbers and strings, but context, attention, tools, and time. If we want students to be effective in that world, we should start showing them the connection now.
