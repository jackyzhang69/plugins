---
name: immi-clb
description: Convert language test scores (IELTS General, CELPIP-G, PTE Core, TEF Canada, TCF Canada) into Canadian Language Benchmark (CLB) levels.
---

# Immi-CLB Skill

Use this skill when you need to calculate CLB levels for Express Entry, PNP, or Study/Work permit eligibility.

## Command

```bash
anyimmi clb --test <ielts|celpip|pte|tef|tcf> -l <L> -r <R> -w <W> -s <S>
```

## Example

```bash
anyimmi clb --test ielts -l 8.0 -r 7.0 -w 7.0 -s 7.5
```
