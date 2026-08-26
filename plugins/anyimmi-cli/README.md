# AnyImmi 🇨🇦

> **The Canadian Immigration Intelligence & Practical Tools Plugin**  
> Powered by the ImmiCore Knowledge Engine. Zero external dependencies. Zero backend AI cost.

---

## 🌟 Core Features

- ⚖️ **Precedent Caselaws**: Canadian Federal Court precedent retrieval with exact legal ratio excerpts and neutral citations.
- 📚 **IRCC Help Centre Q&A**: Direct access to official Help Centre guidance (`policy`).
- 📖 **Program Delivery Instructions**: Search IRCC operational manuals (`manual`). An empty result is not evidence of absence, and coverage may be indeterminate — that is not a finding of no risk.
- 🗺️ **Live coverage**: Ask what is covered *now* (`coverage`). Never a bundled catalog.
- 💡 **Practitioner Field Insights**: Synthesized operational intelligence, IRCC portal notes, and practical case handling consensus.
- 🧮 **Statutory Calculators**: Instant CLB benchmark conversion for IELTS, CELPIP, PTE, TEF, and TCF exams.
- 🤖 **Agent-First Architecture**: Standardized `anyimmi` interface for Codex, Claude Code, and Gemini.

---

## 🚀 Quick Start

### 1. Installation
Add `anyimmi` to your `PATH` or install the plugin directly:
```bash
export PATH="./bin:$PATH"
```

### 2. Verify Installation
```bash
./scripts/verify-install
```

### 3. Usage Examples

#### 🇨🇦 CLB Benchmark Calculation
```bash
anyimmi clb --test ielts -l 8.0 -r 7.0 -w 7.0 -s 7.5
```

#### ⚖️ Search Case Law Precedents
```bash
anyimmi caselaw "study permit dual intent section 22(2)" --top 3
anyimmi caselaw "procedural fairness extrinsic evidence" --top 3
```

#### 📚 Search Official IRCC Help Centre Q&As
```bash
anyimmi policy "maintained status travel outside canada" --top 3
anyimmi policy "学签 续签" --lang zh --top 3
```

#### 📖 Search IRCC Program Delivery Instructions
```bash
anyimmi manual "study permit dual intent" --mode hybrid --top 5
anyimmi coverage
```

#### 🤖 Agent Tool Router (JSON Interface)
```bash
anyimmi query --action caselaw --input "refusal financial sufficiency" --top 2
anyimmi query --action manual --input "study permit dual intent" --top 5
anyimmi query --action coverage
```

---

## 📁 Repository Structure

```
~/anyimmi/
├── Cargo.toml                 # Rust package configuration
├── AGENTS.md                  # Project rules & non-negotiable principles
├── README.md                  # Documentation & usage guide
├── runtime-manifest.json      # Packaged runtime metadata
├── bin/
│   └── anyimmi                # Standalone native Rust binary executable
├── src/
│   ├── main.rs                # Entry point & CLI argument parsing (Clap)
│   ├── lib.rs                 # Library target (shared modules + tests)
│   ├── auth.rs                # Platform credential management & token masking
│   ├── sanitize.rs            # Fail-closed two-phase PII & forum scrubbing engine
│   ├── projection.rs          # Typed output structs (Anti-Leakage Projections)
│   ├── client.rs              # High-performance ureq HTTP client
│   ├── config.rs              # Host, token paths, and local table version
│   ├── error.rs               # Typed CLI errors
│   ├── signals.rs             # Product Signals emitters
│   └── commands/              # Subcommand handlers
├── tests/                     # Security, projection, and version-agreement tests
├── skills/
│   └── anyimmi/               # Product router skill and playbooks
├── scripts/
│   ├── verify-install         # Automated smoke test suite
│   ├── verify-package         # Source/pack shape checks
│   ├── verify-release-assets  # Release binary presence checks
│   └── assert-skill-surface.py
├── .claude-plugin/
│   └── plugin.json            # Claude host plugin metadata
└── .codex-plugin/
    └── plugin.json            # Codex host plugin metadata
```
