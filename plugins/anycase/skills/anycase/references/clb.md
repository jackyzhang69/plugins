# Immi-CLB Skill

Use this skill when you need to calculate CLB levels for Express Entry, PNP, or Study/Work permit eligibility.

## Command

```bash
anycase clb [--test <ielts|celpip|pte|tef|tcf>] [--listening (-l) <L>] [--reading (-r) <R>] [--writing (-w) <W>] [--speaking (-s) <S>]
anycase clb [--test <ielts|celpip|pte|tef|tcf>] [--skill <name>] [--score <n>]
```

Give all four skill scores, or one `--skill` with its `--score`; `--test` defaults to `ielts`. A short alias such as `-l` is the same flag as `--listening`, not a separate one. The example below uses the long names.

## Example

```bash
anycase clb --test ielts --listening 8.0 --reading 7.0 --writing 7.0 --speaking 7.5
```
