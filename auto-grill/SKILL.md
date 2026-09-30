---
name: auto-grill
description: Run the installed GrillMe workflow with one independent subagent per round, automatically resolving agreement and escalating exceptions.
disable-model-invocation: true
argument-hint: "<idea>"
---

# AutoGrill

Run the currently installed GrillMe workflow without copying or replacing it. GrillMe owns the design tree, frontier, rounds, questions, recommendations, fact-finding, dependencies, and completion criteria. AutoGrill only changes who answers each question.

## Setup

1. Load the current `grill-me` skill using the host's skill-invocation mechanism. Follow its current workflow as the source of truth; this skill only adds delegation and answer resolution.
2. Use the idea supplied with this skill as GrillMe's goal.
3. Check that the host provides a way to start a fresh subagent or child session with a bounded prompt. If it does not, explain that automatic answering is unavailable and continue only with GrillMe's normal user-answer workflow.

## Each GrillMe round

1. Let GrillMe discover facts and generate its complete current frontier, including a recommendation for every question. Keep recommendations and recommendation rationale private from the subagent.
2. Start exactly one fresh subagent for the entire round using the host's native delegation capability. Start a new child for every later round. Pass only the goal, relevant settled context, and the complete numbered round with necessary question details and choices. Do not include GrillMe's recommendations, the parent transcript, irrelevant context, or tool access. Configure the child for fresh context without inherited conversation, skills, or tools wherever the host supports those controls. Treat questions as independent unless supplied context explicitly establishes a dependency.
3. Load the bundled `references/decider.md` file from this skill and use it as the child's role instructions. Pass only the goal, relevant settled context, and round as task data. Adapt the prompt to the host's native delegation interface without adding platform-specific requirements.
4. Compare the answers question by question:
   - If the Decider's answer materially implies the same choice as GrillMe's recommendation and says `HUMAN_JUDGMENT_REQUIRED: no`, resolve the question automatically.
   - Escalate materially different choices, unclear or missing answers, and any question marked `HUMAN_JUDGMENT_REQUIRED: yes`. Neither recommendation wins automatically.
5. Batch all escalated questions into one user prompt. Include each original question and its choices, GrillMe's recommendation, the Decider's answer, and a brief explanation of why human input is needed. Do not ask about automatically resolved questions.
6. Combine automatic and human answers into a complete response to the original round, preserving its numbering and semantics, and return it to GrillMe. Continue with GrillMe's next frontier.

If the bundled Decider prompt is unavailable, the host cannot provide a fresh child with sufficiently isolated context, or the child fails, escalate the affected questions rather than claiming independent answers or guessing. If no questions need the user, return the complete automatic answers to GrillMe immediately.

## Completion

Stop only when GrillMe says its design tree is complete. Preserve GrillMe's requirement to wait for confirmation before acting on the design, then present its final consolidated proposal. Do not add a separate AutoGrill completion criterion.
