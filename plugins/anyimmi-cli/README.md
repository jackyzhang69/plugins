# AnyImmi 🇨🇦

> **The Canadian Immigration Intelligence & Practical Tools Plugin**  
> Powered by the ImmiCore Knowledge Engine. Zero external dependencies. Zero backend AI cost.

---

## 🌟 Core Features

- ⚖️ **Precedent Caselaws**: Canadian Federal Court precedent retrieval with exact legal ratio excerpts and neutral citations.
- 📚 **Official IRCC Policy & Q&A**: Direct access to Program Delivery Instructions (PDI) and Help Centre guidance.
- 💡 **Practitioner Field Insights**: Synthesized operational intelligence, IRCC portal notes, and practical case handling consensus.
- 🧮 **Statutory Calculators**: Instant CLB benchmark conversion for IELTS, CELPIP, PTE, TEF, and TCF exams.
- 🤖 **Agent-First Architecture**: Standardized `immi-tools` interface for Codex, Claude Code, and Gemini.

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

#### 📚 Search Official IRCC Policy & Guidelines
```bash
anyimmi policy "maintained status travel outside canada" --top 3
anyimmi policy "学签 续签" --lang zh --top 3
```

#### 🤖 Agent Tool Router (JSON Interface)
```bash
anyimmi query --action caselaw --input "refusal financial sufficiency" --top 2
```

---

## 📁 Repository Structure

```
~/anyimmi/
├── Cargo.toml                 # Rust package configuration
├── AGENTS.md                  # Project rules & non-negotiable principles
├── README.md                  # Documentation & usage guide
├── bin/
│   └── anyimmi                # Standalone native Rust binary executable
├── src/
│   ├── main.rs                # Entry point & CLI argument parsing (Clap)
│   ├── auth.rs                # Platform credential management & token masking
│   ├── sanitize.rs            # Fail-closed two-phase PII & forum scrubbing engine
│   ├── projection.rs          # Typed output structs (Anti-Leakage Projections)
│   ├── client.rs              # High-performance ureq HTTP client
│   └── commands/              # Subcommand handlers (caselaw, policy, notes, clb)
├── tests/
│   └── security_boundary_test.rs # Security & negative assertion tests
├── skills/
│   ├── immi-tools/            # Flagship AI agent skill
│   ├── immi-caselaw/          # Precedent retrieval skill
│   ├── immi-policy/           # IRCC policy search skill
│   ├── immi-fieldnotes/       # Practitioner notes skill
│   └── immi-clb/              # CLB conversion skill
├── scripts/
│   └── verify-install         # Automated smoke test suite
└── manifest.json              # Plugin distribution manifest
```
