# Security Policy

## Scope

Security issues relevant to TerrainOS include:

- Privilege escalation via the shell or gatekeeper
- Unsafe update mechanisms (manifest validation, hash verification)
- Network attack surface in `datum` (WinHTTP, manifest parsing)
- Unsigned binary execution in the boot chain

## Reporting a Vulnerability

**Do not open a public issue for security vulnerabilities.**

Report privately via GitHub's [Security Advisories](https://github.com/andreasfilimon/TerrainOS/security/advisories/new).

Include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

Response time: within 7 days.

## Known Limitations

- TerrainOS optionally removes Windows Defender — users who do this accept full responsibility for endpoint security
- The update mechanism requires a trusted update server; self-hosting is the user's responsibility
- Shell registration grants the binary full user-level access at logon
