# Contributing

Contributions are welcome across every repository in the
[Open Game Standards Alliance](https://github.com/Open-Game-Standards-Alliance)
organization, from game developers, hardware vendors, tool authors and players.

A repository with its own `CONTRIBUTING.md` overrides this page. Read that one
first; this is the default for repositories that have none.

## Before you start

- **Ask first if you are unsure.** The [Discord](https://discord.gg/tseF4Q6U99)
  is the fastest way to find out whether something is a bug, a gap in a
  standard or a configuration problem.
- **Open an issue for anything non-trivial.** A short discussion before the work
  saves rework, and for a standard it is required: changes to a standard are
  agreed in an issue before a pull request.
- **Read the standard first.** For OGSA-TS,
  [IMPLEMENTING.md](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/blob/main/IMPLEMENTING.md)
  sets out the reading order, and
  [CONVENTIONS.md](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/blob/main/CONVENTIONS.md)
  fixes the units, frames and signs every value follows.

## Which repository

| You want to change | Go to |
| ------------------ | ----- |
| The OGSA-TS schema, conventions, encoding rules or conformance corpus | [OGSA-Telemetry-Standard](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard) |
| A channel or event name (add or ratify) | [CHANNELS.md](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/blob/main/CHANNELS.md) in the standard |
| The .NET core, the reference receiver or the code generator | [ogsa-ts-dotnet](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet) |
| The Unity package | [ogsa-ts-unity](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unity) |
| The Unreal plugin or the C++ core | [ogsa-ts-unreal](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unreal) |
| The Godot addon | [ogsa-ts-godot](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-godot) |
| A new standard | An issue on this repository, or the Discord, to start the conversation |

The Unity package and the Godot addon vendor their core from ogsa-ts-dotnet.
Change the core there, then sync it; edits made directly to a vendored copy are
overwritten by the next sync.

## Changing a standard

- **Additions are the norm.** A v1 standard grows by adding optional fields,
  channels and clauses that existing receivers can ignore. Anything that would
  break a deployed sender or receiver needs a strong case and a new major
  version.
- **Evidence over opinion.** A ratified channel needs the concept in at least
  two shipping games; cite them in the pull request.
- **The schema is the single source of truth.** After a schema change,
  regenerate the bindings in every SDK. Generated code is never edited by hand.
- **The conformance corpus is the contract.** Every implementation must still
  byte-match it, and a change to the corpus says why in its commit.

## Sending a change

- Branch from `main` unless the repository says otherwise.
- Write a commit subject that says what changed, and a body that says why.
- Add tests where the repository has them, and say why if you have not. Engine
  plugins also run their conformance checks against the corpus.
- Keep the change focused. An unrelated fix in the same pull request is harder
  to review and harder to revert.
- Contributions are licensed under the repository's license (MIT for the
  standards and SDKs).

## Reporting a bug

Open an issue on the repository the bug is in, and include the SDK and engine
versions (or commits) you are using, your OS, and the steps to reproduce. For
wire problems, the output of ogsa-ts-dotnet's
[TelemetryReceive](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet)
while the problem happens is the most useful thing you can attach.

Security problems are the exception: do not open a public issue. Follow
[SECURITY.md](SECURITY.md).

## Code of conduct

Participation is covered by the [Code of Conduct](CODE_OF_CONDUCT.md).
