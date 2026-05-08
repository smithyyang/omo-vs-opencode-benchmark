# Methodology

[中文说明 / Chinese Version](./methodology_CN.md)

## Test Setup

The goal of this experiment was to control as many variables as possible and isolate the impact of agent organization on research-task outcomes.

Each run used the same:

- task prompt
- model: `GPT-5.4`
- MCP configuration
- research-oriented question style
- evaluation approach combining answer quality and token cost

## Compared Setups

- `omo`
- `bare OpenCode (older)`
- `bare OpenCode + Librarian prompt`

Here, `Librarian` refers to the research-oriented retrieval prompt extracted from `oh-my-opencode` and migrated into bare OpenCode.

## Token Accounting

The reported token usage is not a naive sum of one dashboard column. It is computed from the actual cost of each call in the run:

- input tokens
- output tokens
- reasoning tokens

The final number therefore represents the accumulated full-run cost across all calls, rather than a direct copy of a single token field.

## Evaluation Dimensions

This repository evaluates results along four primary dimensions:

1. `token usage`
2. `capture of critical hidden facts`
3. `source quality`
4. `usability of the conclusion`

### 1. Token Usage

This measures the total token cost required to complete a research task, rather than simply whether an answer is longer or shorter.

### 2. Capture of Critical Hidden Facts

This focuses on whether the system found the facts that truly changed the decision outcome, such as:

- a project being discontinued
- an official LTS or support policy being scheduled to end
- a founder or core maintainer publicly recommending migration to an alternative project

These details usually do not appear in surface-level benchmark comparisons, but they can directly determine whether a research conclusion is reliable.

### 3. Source Quality

The general priority order is:

- official documentation
- official issues / PRs / releases / roadmaps
- verifiable statements inside the project repository
- independent benchmarks with methodology and raw data
- secondary articles or SEO summaries as the lowest-priority source type

### 4. Usability of the Conclusion

This asks whether the final answer can actually support a technical decision, instead of merely accumulating references. A usable conclusion should:

- provide a clear direction
- state the conditions under which that direction applies
- explain risks and boundaries
- allow the reader to continue validating the claim

## Interpretation Principle

This experiment does not treat “fewer tokens” as automatically equivalent to “better results.”

- If token usage is very low but a critical hidden fact is missed, the result can still mislead a decision.
- If token usage is somewhat higher but it significantly improves hidden-fact capture and source quality, that extra cost is often worthwhile in research tasks.

The core question of this repository is therefore: `who is more likely to produce decision-useful research conclusions per unit of token cost?`
