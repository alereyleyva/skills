---
name: auto-grill
description: "Run the installed GrillMe workflow with one independent Decider per round, auto-resolving agreement and escalating exceptions."
disable-model-invocation: true
argument-hint: "<idea>"
---

# AutoGrill

Run the current GrillMe workflow without copying or replacing it. GrillMe owns the design tree, frontier, rounds, questions, recommendations, fact-finding, dependencies, and completion criteria. AutoGrill only chooses who answers each question.

## Setup

1. Load the current `grill-me` skill by calling the Skill tool with `grilling`. Follow its current workflow and recommendations as the source of truth; this skill only adds the delegation and resolution steps below.
2. Confirm the `auto-grill-decider` agent is available. If it is not, stop and tell the user to install `pi-subagents` (`pi install npm:pi-subagents`) and place `agents/auto-grill-decider.md` in `~/.pi/agent/agents/`.
3. Treat the text after `/skill:auto-grill` as the design goal and begin GrillMe with it.

## Each GrillMe round

1. Let GrillMe discover facts and generate its complete current frontier, including its recommended answer for every question. Keep those recommendations private from the Decider.
2. Launch exactly one fresh `auto-grill-decider` child for the whole round. Supply only the goal, relevant settled context, and the complete numbered GrillMe questions with their choice options and necessary question details. Omit recommendations, recommendation rationale, the parent transcript, unrelated context, and tools. Treat round questions as independent unless supplied context establishes a dependency.
3. Compare each Decider decision with GrillMe's private recommendation:
   - If they materially imply the same choice and the Decider says `HUMAN_JUDGMENT_REQUIRED: no`, resolve the question automatically with that choice.
   - Otherwise, ask the user. This includes materially different choices, unclear answers, missing answers, and `HUMAN_JUDGMENT_REQUIRED: yes`.
4. Batch every unresolved question from this round into one user prompt. Include its original question and choices, GrillMe's recommendation, the Decider's decision, and a brief explanation of why the choices differ or why human judgment is needed. Do not ask about automatically resolved questions.
5. Combine automatic and human answers into a complete answer to the original round, preserving its numbering and semantics, and give that to GrillMe. Continue with GrillMe's next frontier. Launch a new Decider for that round; never reuse a child.

If no questions need the user, immediately return the complete automatic answers to GrillMe and continue. If the Decider fails or its output cannot be compared confidently, escalate affected questions to the user rather than guessing.

## Completion

Stop only when GrillMe says its design tree is complete. Preserve GrillMe's requirement to wait for confirmation before acting on the design. Present its final consolidated proposal; do not add a separate AutoGrill completion criterion.
