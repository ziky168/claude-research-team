# Claude Research Team

This repository contains a Claude Code team prompt for running conservative, research-focused neural network experiments with a small multi-agent loop.

The core prompt lives in [claude-research-team.md](./claude-research-team.md). It defines a three-agent team that plans, executes, evaluates, and iterates on small algorithm research tasks.

## Architecture

The team uses three agents with distinct responsibilities:

- `research-team-leader` (`haiku`): coordinates the workflow, owns planning, keeps scope under control, and synthesizes the final result.
- `research-engineer` (`sonnet`): makes the minimum code changes required, runs approved experiments, and records commands, configs, and artifacts.
- `research-analyst` (`sonnet`): checks validity, compares against baselines, analyzes outcomes, and helps decide whether another revision cycle is needed.

## Workflow

The team is intentionally conservative:

1. Convert a user request into a research question and testable hypothesis.
2. Produce an experiment plan and wait for explicit human approval.
3. Implement only the minimum changes needed for the approved experiment.
4. Run the experiment and preserve reproducibility.
5. Analyze the results against the hypothesis and baseline.
6. Decide whether to finalize or send feedback back to the engineer for another targeted revision.
7. Deliver a structured research report.

## Feedback Loop

The team includes a built-in review loop:

- The engineer executes the approved experiment and reports artifacts.
- The analyst evaluates the evidence and discusses the outcome with the leader.
- The leader and analyst decide whether the result is ready, inconclusive but acceptable, or requires revision.
- If revision is needed, the leader sends precise feedback to the engineer for the smallest necessary update and rerun.

This loop is designed to improve scientific rigor without drifting into open-ended experimentation.

## Superpowers Integration

The workflow can selectively use methods from the `obra/superpowers` skill set. These skills are treated as stage-specific methods, not as a second orchestration layer.

- `brainstorming`
  - Used by the leader before drafting the approval packet.
- `writing-plans`
  - Used by the leader after human approval to create a concrete execution plan.
- `systematic-debugging`
  - Used by the engineer when scripts fail, runs behave abnormally, or metrics look suspicious.
- `requesting-code-review`
  - Used by the engineer before expensive reruns when implementation changes are non-trivial.
- `verification-before-completion`
  - Used by the leader and analyst before finalizing conclusions or reporting completion.
- `using-git-worktrees`
  - Optional for risky or parallel experiment branches.

The repository does not make every `superpowers` skill mandatory. In particular, `test-driven-development`, `subagent-driven-development`, and branch-finishing workflows are intentionally left out of the default research loop.

## Hooks

The prompt expects Claude Code team mode to enable:

- `TeammateIdle`
- `TaskCompleted`

These hooks are used as quality gates so agents do not mark work complete without enough evidence, documentation, and analysis.
