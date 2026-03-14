# Security Policy

## Scope

ZLAR-LT is a zero-config governance layer for AI coding agents. It generates Ed25519 keys, signs a default policy, and configures hook-based interception — all in a single command.

Any vulnerability that allows an agent to bypass policy enforcement, forge signatures, tamper with the audit trail, or execute actions without gate evaluation is in scope.

---

## Reporting a Vulnerability

**Do not open a public issue for security vulnerabilities.**

Use GitHub's private vulnerability reporting:

1. Go to [ZLAR-AI/ZLAR-LT Security Advisories](https://github.com/ZLAR-AI/ZLAR-LT/security/advisories)
2. Click **"Report a vulnerability"**
3. Provide a clear description, reproduction steps, and affected components

We will acknowledge receipt within 48 hours and provide a timeline for remediation. We aim to resolve critical issues within 14 days.

If you cannot use GitHub's reporting tool, email **hello@zlar.ai** with the subject line "Security: ZLAR-LT" and we will establish a private channel.

---

## Threat Model

ZLAR-LT inherits the ZLAR Gate engine and its threat model. Key attack surfaces:

**Policy bypass.** Newline injection, argument encoding, or tool-call manipulation to evade pattern matching. The gate engine sanitizes all inputs and evaluates against signed policy.

**Signature forgery.** Attempts to modify the policy file without re-signing. Ed25519 verification rejects any alteration.

**Audit trail manipulation.** The hash-chained JSONL audit log makes silent modification detectable.

### Out of Scope

ZLAR-LT does not protect against compromise of the host operating system or IDE. For OS-level containment, see [ZLAR-OC](https://github.com/ZLAR-AI/ZLAR-OC).

---

## Supported Versions

| Version | Supported |
|---------|-----------|
| main (HEAD) | Yes |
| Tagged releases | Yes, current and previous minor |

---

## Disclosure Policy

We practice coordinated disclosure. If you report a vulnerability, we will:

1. Acknowledge within 48 hours
2. Confirm the issue and assess severity within 7 days
3. Develop and test a fix
4. Release the fix and publish a security advisory
5. Credit you in the advisory (unless you prefer anonymity)

We ask that reporters refrain from public disclosure until a fix is available, or until 90 days have elapsed — whichever comes first.

---

## Security Design Principles

ZLAR-LT follows the same invariant as all ZLAR products: **intelligence above, enforcement below, human authority over both.**

For the complete ZLAR security architecture, see [ZLAR-OC SECURITY.md](https://github.com/ZLAR-AI/ZLAR-OC/blob/main/SECURITY.md).
