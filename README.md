# omo-vs-opencode-benchmark

[中文说明 / Chinese Version](./README_CN.md)

## Project Overview

This repository compares `oh-my-opencode` (omo) with bare `OpenCode` on research-oriented tasks. It contains 3 real test runs and focuses on how different agent setups affect token usage, capture of critical hidden facts, source quality, and the practical usefulness of final conclusions under the same model and task conditions.

## Core Conclusion

`bare OpenCode + Librarian prompt` did not participate in all three rounds. It was only tested in `T2` and `T3`. However, in both rounds it matched or outperformed `omo` in research quality, while using only about `1/6` to `1/2` of `omo`'s tokens.

The older bare OpenCode run in `T2` was very token-cheap, but it still missed the most important hidden fact. That makes the core takeaway clear: in research tasks, low token usage alone does not mean better quality.

## Summary Table

| Test Topic | omo Token Usage | Bare OC Token Usage | Who Found the Critical Hidden Fact |
| --- | ---: | ---: | --- |
| T1 Tokio vs async-std | 325k | 172k | omo |
| T2 Bun vs Node vs Deno | 335k | 30k (older bare OC) / 228k (bare OC + Librarian) | omo, bare OC + Librarian |
| T3 Redis vs Valkey vs KeyDB | 432k | 26k (bare OC + Librarian) | bare OC + Librarian |

## Key Numbers

- T1: `omo 325k` vs `bare OC 172k`
- T2: `omo 335k` vs `older bare OC 30k` vs `bare OC + Librarian 228k`
- T3: `omo 432k` vs `bare OC + Librarian 26k`

## Hidden Facts That Mattered

- T1 hidden fact: `async-std` has been officially discontinued, with maintainers recommending `smol` instead.
- T2 hidden fact: `Deno LTS` will end on `2026-04-30`.
- T3 hidden fact: the `KeyDB` founder publicly suggested moving to `Valkey` in `issue #895`.

### Hit Matrix

- `T1`: `omo` found the critical hidden fact; `older bare OC` did not; `bare OC + Librarian` did not participate.
- `T2`: `omo` found the `Deno LTS end date`; `older bare OC` only noticed the less precise `Deno 2.0 shift`; `bare OC + Librarian` found the more complete official strategy-change context.
- `T3`: `omo` missed `KeyDB issue #895`; `bare OC + Librarian` found the founder's public recommendation to move to `Valkey`; `older bare OC` did not participate.

## Per-Test Comparison

### T1: Tokio vs async-std

This round only compared `omo` with `older bare OC`.

- `omo`: ✅ Found that `async-std` is officially discontinued
- `older bare OC`: ❌ Completely missed it
- `bare OC + Librarian`: Did not participate in this round

### T2: Bun vs Node vs Deno

All three setups participated in this round.

| Metric | Bare OpenCode (older) | omo | Bare OpenCode + Librarian Prompt |
| --- | --- | --- | --- |
| Token usage | ~30k | ~335k | ~218-238k |
| Final context | 30,125 | 74,873 | 69,672 |
| Number of calls | 1 | 6 | 4-5 |
| Number of secondary sources | 5 | 6 | 7 |
| Found exact Deno LTS end date | ❌ | ✅ 2026-04-30 | ❌ |
| Found official Deno strategy blog post | ❌ | ❌ | ✅ Deploy/KV strategy shift + "greatly-exaggerated" blog post |
| Rejected low-quality secondary articles | ❌ Mixed them in | ⚠️ Partially | ✅ Strictly enforced |

Conclusion:

- `omo`: ✅ Found the `Deno LTS` end date `2026-04-30`
- `older bare OC`: ❌ Only noticed the `Deno 2.0 shift`, which was not precise enough
- `bare OC + Librarian`: ✅ Found more complete information, including the official Deno `"greatly exaggerated"` blog post and the `Deploy/KV` strategy shift

### T3: Redis vs Valkey vs KeyDB

This round only compared `omo` with `bare OpenCode + Librarian prompt`.

| Metric | omo | Bare OpenCode + Librarian Prompt |
| --- | --- | --- |
| Token usage | ~432k | ~26k (16x difference) |
| centminmod benchmark | ✅ | ✅ |
| Redis license change | ✅ | ✅ |
| Valkey 1.2M req/s official data | ❌ | ✅ |
| KeyDB issue #895 (founder says "move to Valkey") | ❌ | ✅ |
| Benchmark detail quality | Medium | More complete |

Conclusion:

- `omo`: ❌ Missed `KeyDB issue #895`
- `bare OC + Librarian`: ✅ Found the `KeyDB` founder's public suggestion to move to `Valkey`
- `older bare OC`: Did not participate in this round

## Interpretation

These results suggest that research-task quality is not determined by the base model alone. Retrieval strategy and prompt design matter a lot. Once the `Librarian`-style prompt pushed bare OpenCode toward official docs, version status, issue/PR history, and verifiable evidence, its ability to capture high-value hidden facts improved substantially.

More precisely, `bare OC + Librarian` only participated in the last two rounds, but in both `T2` and `T3` it matched or outperformed `omo` in research quality while using only `1/6` to `1/2` of the tokens.

## Limitations

- Only research tasks were tested; coding, bug-fixing, refactoring, and longer tool-heavy workflows were not evaluated.
- All runs used `GPT-5.4`, so the results should not be automatically generalized to other models.
- The sample size is only `3` tests, which is useful for observational conclusions but not for strict statistical proof.

## Repository Structure

```text
.
├── README.md
├── README_CN.md
├── methodology.md
├── methodology_CN.md
├── prompts/
│   ├── librarian.md
│   └── librarian_CN.md
└── results/
    ├── T1-tokio-vs-async-std/
    ├── T2-bun-vs-node-vs-deno/
    └── T3-redis-vs-valkey-vs-keydb/
```

## About `results/`

- `results/` contains the organized raw experiment outputs.
- These initialization and documentation updates do not modify any file under `results/`.

## Suggested Uses

This repository is suitable for:

- a public experiment record
- an agent design case study
- sharing `OpenCode` research workflow experience
- adding more benchmark samples over time
