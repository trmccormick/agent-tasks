What we've seen with Qwen suggests that agent-tasks needs to guide not just what an agent should build, but how it should execute a task, recognize completion, and stop. This is a useful learning cycle: the task system should improve in response to observed agent behavior, rather than assuming a sufficiently detailed prompt will always produce reliable execution.

The key lesson from the recent Asset/UI work is that more instructions don't necessarily create more control. A long prompt can still leave an agent unsure which action comes next, whether the work is already complete, or whether it should keep investigating Git state instead of delivering the result.

I'd focus the next adjustment on a few concrete behaviors:

One objective per task. Define the specific change or verification being requested, not an entire downstream workflow.

Explicit completion evidence. Name the file, test result, report, or remote commit that proves the task is done.

A finite execution sequence. Give the agent a short ordered checklist, with a clear stopping point.

Bounded recovery. If a command fails, a file's state is contradictory, or the agent cannot establish the expected state, it should report the evidence and stop—not repeatedly retry the same checks or invent a resolution.

Separate implementation from closeout. Completing code, recording the task summary, updating lifecycle status, committing, and pushing are distinct operations. The workflow should say which are required and how to verify each one.

Preserve unrelated work. In our setup, agents work directly on main; they must not reset, stash, or commit unrelated transit-engine changes just to make the working tree clean.

One thing stands out in the current repository

I looked at the public agent-tasks repository
. Its README already has a substantial workflow, including a mandatory synthesis gate, task lifecycle steps, reports, and Git approval requirements. 
GitHub

That suggests the problem may not be a lack of guidance. It may be too much workflow for the agent to hold together at once, with no sufficiently simple rule for recovering when the workflow gets confused.

The README asks the executor to create a synthesis report, post it, wait for approval, implement, move the task, check for duplicates, update status, save a report, commit, wait for approval, and push. Those are individually understandable steps, but our recent Qwen session struggled to determine whether a report existed and whether the Git work was already complete. 
GitHub

I would not rewrite the entire repository based on one failure. I'd make the next improvement evidence-driven.

My proposed next step

Create a small, focused task in agent-tasks to audit task construction and executor recovery behavior, using the recent Asset/UI work as the case study.

Its deliverable should be a short set of recommendations—not an immediate rewrite of every template or workflow.

The audit should answer four questions:

Task construction: Does TASK_TEMPLATE.md encourage too many independent objectives, excessive context, or unnecessary deliverables?

Execution control: Does the task clearly tell Qwen what to do first, what evidence establishes completion, and exactly when to stop?

Recovery behavior: What should the agent do when it encounters contradictory Git state, a report that may already exist, or an unsuccessful command? Can we explicitly prevent repetitive checks?

Closeout: Are implementation completion, task-file updates, commits, and remote pushes distinct, verifiable stages with clear ownership and approval requirements?

The result should identify the smallest useful changes to the relevant templates or rules. We should avoid adding another layer of process unless the evidence shows it is needed.

A principle worth adopting

I'd make this the guiding rule for future task creation:

Every task must have one primary objective, an explicit completion test, a bounded recovery path, and a defined stopping point.

A task can still have multiple implementation steps. The distinction is that every step must contribute to the same objective, and the agent must not interpret uncertainty as permission to keep exploring indefinitely.

For Qwen specifically, I would also test a compact execution contract:

Objective: What one thing must be accomplished?

Allowed scope: Which files and behaviors may change?

Steps: What is the shortest reasonable execution sequence?

Verification: What commands or evidence prove success?

Stop conditions: What failures or ambiguities require reporting a blocker?

Closeout: Which task records, commits, and pushes are required?

Final response: What evidence must be reported, without claiming unverified success?

These are proposed improvements, not claims that the current templates lack all these elements.

My recommendation is to audit the existing task template, session-closeout guidance, and recent Asset/UI task together before changing anything. We should use the actual Qwen failure as the test case and make only the changes that address demonstrated weaknesses. That lets agent-tasks evolve from real experience without destabilizing the workflow that is already working.