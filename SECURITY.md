# Security Policy

This policy covers every repository in the
[Open Game Standards Alliance](https://github.com/Open-Game-Standards-Alliance)
organization: the standards and their SDKs. A repository with its own
`SECURITY.md` overrides this one.

## Reporting a vulnerability

Please **do not** open a public issue for a security vulnerability, in any
repository.

Report it through GitHub's private vulnerability reporting on the standard's
repository, whichever project the problem is in:

**[github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/security/advisories/new](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/security/advisories/new)**

That form is private. It is the right place even when the affected code lives
in an SDK or engine plugin, because it is the channel the maintainers watch.

If GitHub Security Advisories are unavailable to you, find a maintainer through
the community [Discord](https://discord.gg/tseF4Q6U99) and continue in a direct
message. Do not post vulnerability details in a public channel.

Include if you can:

- What the issue is and what an attacker could do with it
- Steps to reproduce, or a proof of concept (a captured datagram is ideal)
- Which versions or commits are affected
- Any mitigation you have found

You should get a first response within 72 hours. Coordinated disclosure is
appreciated: please give the maintainers time to assess and patch before public
discussion.

## What counts

OGSA standards connect games to devices on the player's own network. Telemetry
is read-only, from the game to the software around it, and stays on the local
subnet by default. Reports that interest us most:

- A datagram that crashes, hangs or corrupts memory in a receiver or decoder,
  such as `Ogsa.Telemetry.Receiving` or the C++ core in `ogsa-ts-unreal`
- A way for a sender or an SDK default to put telemetry beyond the local
  subnet, or to leak anything other than the telemetry the game chose to send
- Anything in a standard's text that, implemented as written, would let one
  device on the network make a game or another device act on its behalf
- Build, release or supply-chain issues in these repositories, such as a
  tampered dependency or a workflow that could publish untrusted code

A device that behaves unsafely on valid telemetry (a motion rig moving too far
or too fast) is a problem for that device's software, not the standard. Report
it to the software's maintainers; if you think the standard should say more,
open a normal issue on the standard.

## Versions

The standards are versioned (OGSA-TS is v1), and their SDKs are pre-1.0. Only
the current `main` of each repository receives fixes.
