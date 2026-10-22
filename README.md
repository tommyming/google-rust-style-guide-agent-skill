# Google Rust Style Guide Agent Skill

Agent skill that teaches AI coding agents how to apply the [Google Rust Style Guide](https://google.github.io/styleguide/rustguide.html).

It covers code organization, API design, naming, documentation, unsafe code, concurrency, macros, and implementation concerns, with notes for Chrome, Android, Fuchsia, and other Google codebases. It distinguishes library vs. application code and stays compatible with the official Rust style and API guidelines where they do not conflict.

## Install

Requires Node.js 22.20 or later.

```bash
npx skills add tommyming/google-rust-style-guide-agent-skill
```

Or install only this skill:

```bash
npx skills add tommyming/google-rust-style-guide-agent-skill --skill rust-style-guide
```

Target a specific agent (examples):

```bash
npx skills add tommyming/google-rust-style-guide-agent-skill --skill rust-style-guide -a claude-code
npx skills add tommyming/google-rust-style-guide-agent-skill --skill rust-style-guide -a cursor
npx skills add tommyming/google-rust-style-guide-agent-skill --skill rust-style-guide -a codex
```

Install globally (available across projects):

```bash
npx skills add tommyming/google-rust-style-guide-agent-skill -g
```

After installation the skill is available to the agent. It activates when you ask about Google Rust style, review code for compliance, design APIs, or generate code that should follow the guide.

## Repository Structure

```
skills/
└── rust-style-guide/
    ├── SKILL.md              # Main instructions
    ├── references/           # Detailed rules (organization, API design, naming/docs/impl)
    ├── evals/                # Test cases and triggers
    └── CHANGELOG.md
```

## License

The skill content is provided under the Apache License 2.0. The original Google Rust Style Guide is © Google and is subject to its own terms.

See [LICENSE](LICENSE).
