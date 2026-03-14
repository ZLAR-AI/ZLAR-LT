# Contributing to ZLAR-LT

ZLAR-LT is zero-config governance for AI coding agents. It is maintained by [ZLAR Inc.](https://zlar.ai) and welcomes contributions from anyone who cares about agent governance.

---

## How to Contribute

### Reporting Issues

If you find a bug, a gap in documentation, or have a suggestion, open an issue. Be specific — include what you expected, what happened, and steps to reproduce if applicable.

### Security Disclosures

If you discover a security vulnerability, **do not open a public issue.** Use [GitHub's private vulnerability reporting](https://github.com/ZLAR-AI/ZLAR-LT/security/advisories) instead. See [SECURITY.md](SECURITY.md) for our full disclosure policy.

### Pull Requests

1. Fork the repository
2. Create a branch from `main` for your change
3. Make focused changes — one concern per PR
4. Run `bash -n bin/zlar-gate` and `bash -n install.sh` to syntax-check
5. Write a clear description of what the change does and why
6. Submit the PR

### Code Standards

ZLAR-LT's codebase is shell scripts designed for zero-config deployment. Contributions should:

- Maintain bash-3.x compatibility (macOS ships bash 3.2 by default)
- Follow existing patterns and naming conventions
- Preserve the "one command, done" install experience
- Document security implications if the change touches enforcement logic

### Documentation

Documentation improvements are always welcome. If the install experience was confusing on your platform, a PR that clarifies it helps everyone.

---

## What We're Looking For

- **Platform testing** — testing the installer on different macOS and Linux versions
- **Editor detection** — improving auto-detection for Claude Code, Cursor, and Windsurf configurations
- **Policy templates** — domain-specific default policies
- **Shell compatibility** — ensuring bash-3.x compatibility across environments

---

## Code of Conduct

Be respectful, be specific, be constructive. Engage with the substance of ideas, not the identity of contributors.

---

## License

By contributing to ZLAR-LT, you agree that your contributions will be licensed under the [Apache License 2.0](LICENSE).
