<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/banner-dark.svg">
  <img src="assets/banner-light.svg" alt="Open Game Standards Alliance: open, royalty-free standards connecting games to motion rigs, haptics and dashboards">
</picture>

[![License: MIT](https://img.shields.io/badge/license-MIT-86b65a)](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/blob/main/LICENSE)
[![Discord](https://img.shields.io/badge/chat-Discord-5865F2?logo=discord&logoColor=white)](https://discord.gg/tseF4Q6U99)
[![Sponsor](https://img.shields.io/badge/sponsor-GitHub%20Sponsors-db61a2?logo=githubsponsors&logoColor=white)](https://github.com/sponsors/BrianGilbert)

Motion rigs, haptic vests and instrument dashboards all need the same thing
from a game: what the player's vehicle or character is doing, moment to
moment. Most games ship no telemetry at all, or tie it to one vendor's
ecosystem, so every device maker ends up writing a reader per game. One
motion-software project alone maintains 72 of them.

The **Open Game Standards Alliance** publishes open, royalty-free standards
so that a game describes itself once and every device on the player's
network understands it. No cloud, no account, no per-game integration.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#dcebd0', 'primaryTextColor': '#1d4730', 'primaryBorderColor': '#1d4730', 'lineColor': '#86b65a', 'clusterBkg': '#f1f7ec', 'clusterBorder': '#86b65a', 'edgeLabelBackground': '#f1f7ec'}}}%%
flowchart LR
    subgraph sources["Games"]
        P["Engine plugin<br/>Unity · Unreal · Godot · .NET"]
        M["Loader mod<br/>BepInEx · UE4SS · UEVR"]
    end
    P -- "OGSA-TS over UDP multicast<br/>(local network only)" --> L(("LAN"))
    M -- "OGSA-TS" --> L
    L --> R["Motion platforms"]
    L --> H["Haptics"]
    L --> D["Dashboards and cockpits"]
    L --> G["Loggers"]
```

## Find your starting point

| You are... | Start with |
| --- | --- |
| **Making a game in Unity** | [ogsa-ts-unity](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unity): add a publisher component, bind your values |
| **Making a game in Unreal** | [ogsa-ts-unreal](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unreal): a plugin with no third-party dependencies |
| **Making a game in Godot 4 (.NET)** | [ogsa-ts-godot](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-godot): an addon with a publisher node |
| **Running a custom .NET engine** | [ogsa-ts-dotnet](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet): the engine-agnostic core every C# plugin is built on |
| **Building hardware or receiver software** | [ogsa-ts-dotnet](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet) for the receiving library and a reference receiver, then [IMPLEMENTING.md](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/blob/main/IMPLEMENTING.md) |
| **Implementing in another language** | [The standard](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard): the schema, the encoding rules and a conformance corpus to test against |
| **Playing a game that has no telemetry** | A loader mod: [bepinex-ogsa-ts](https://github.com/BrianGilbert/bepinex-ogsa-ts) for Unity games, [ue4ss-ogsa-ts](https://github.com/BrianGilbert/ue4ss-ogsa-ts) for Unreal games, [uevr-ogsa-ts](https://github.com/BrianGilbert/uevr-ogsa-ts) for Unreal games in VR |

<details>
<summary><b>Quick start: Unity</b></summary>

Add the package to `Packages/manifest.json`, pinned to a release:

```json
"com.ogsa.telemetry.unity": "https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unity.git#v0.2.0"
```

Then add **OGSA-TS/Telemetry Publisher** to your vehicle and declare your
channels in code. The package is plain C# source with no precompiled
assemblies, and runs on Mono and IL2CPP.
</details>

<details>
<summary><b>Quick start: Unreal</b></summary>

Drop the plugin into your project's `Plugins/` folder, enable it, add
**TelemetryPublisher** to your vehicle, then in `BeginPlay`:

```cpp
auto Handle = Telemetry->DeclareChannel(TEXT("engine.rpm"), TEXT("rpm"), 0, 9000, true);
Telemetry->BindChannelRaw(Handle, [this]{ return Engine->Rpm; });
```
</details>

<details>
<summary><b>Quick start: Godot 4 (.NET)</b></summary>

Copy `addon/ogsa_telemetry/` into your project as `addons/ogsa_telemetry/`,
then enable **OGSA Telemetry (OGSA-TS)** under Project Settings > Plugins.
Nothing needs adding to your game's csproj.
</details>

<details>
<summary><b>Quick start: see the stream</b></summary>

With any OGSA-TS game running on your network:

```sh
git clone https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet
cd ogsa-ts-dotnet
dotnet run --project src/TelemetryReceive
```

The reference receiver prints the game's channel declarations, live motion
and events, and flags anything that doesn't conform.
</details>

## The Telemetry Standard

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/telemetry-standard-dark.svg">
  <img align="right" width="120" src="assets/telemetry-standard.svg" alt="OGSA Telemetry Standard logo">
</picture>

The [OGSA Telemetry Standard (OGSA-TS)](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard)
defines three frames on one UDP multicast group:

- **Discovery**, every two seconds: the game, its objects, and a dictionary
  of channels, each declared once with a name, type, unit and range.
- **Motion**, at the game's telemetry rate: the bare samples, keyed by the
  ids from discovery. A pose-only frame is about 70 bytes.
- **Events**, when they happen: impacts, gunfire and other stimuli, with
  replay and deduplication so a lossy network can't drop them.

It is **read-only**, so it can't be used to cheat, and it **never leaves
the local network**. It is **extensible**: any game can declare its own
channels, and receivers skip fields they don't recognise, so the schema
grows without breaking deployed devices. And it is **precise**: units,
axes and signs are settled once in
[CONVENTIONS.md](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/blob/main/CONVENTIONS.md),
not rediscovered per game.

### Built to be checked

- **Validated against real games.** The schema has been mapped against more
  than 100 documented game outputs across 16 exercises with no open gaps,
  including all 1,354 variables Microsoft Flight Simulator 2024 exposes.
- **One canonical encoding.** Every implementation produces byte-identical
  Cap'n Proto for the same frame, proven against a shared
  [conformance corpus](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/tree/main/conformance)
  that is independently checked with the official Cap'n Proto tools.
- **Proven in shipped games.** The loader mods have run in Subnautica Below
  Zero, The Gunk, Stray and RoboCop: Rogue City, with no changes to the
  games.

## Implementations

| Repository | What it is | CI |
| --- | --- | --- |
| [OGSA-Telemetry-Standard](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard) | The specification, schema, conventions, validation and conformance corpus | [![CI](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/actions/workflows/ci.yml/badge.svg)](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/actions/workflows/ci.yml) |
| [ogsa-ts-dotnet](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet) | .NET reference implementation: sender core, receiving library, reference receiver | [![CI](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet/actions/workflows/ci.yml/badge.svg)](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-dotnet/actions/workflows/ci.yml) |
| [ogsa-ts-unity](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unity) | Unity package (2022.3+ and Unity 6) | [![CI](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unity/actions/workflows/ci.yml/badge.svg)](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unity/actions/workflows/ci.yml) |
| [ogsa-ts-unreal](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unreal) | Unreal Engine plugin | [![CI](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unreal/actions/workflows/ci.yml/badge.svg)](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-unreal/actions/workflows/ci.yml) |
| [ogsa-ts-godot](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-godot) | Godot 4 (.NET) addon | [![CI](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-godot/actions/workflows/ci.yml/badge.svg)](https://github.com/Open-Game-Standards-Alliance/ogsa-ts-godot/actions/workflows/ci.yml) |
| [Motion-Telemetry-Documentation](https://github.com/Open-Game-Standards-Alliance/Motion-Telemetry-Documentation) | Background on motion telemetry concepts | |

Every SDK is plain source with no third-party runtime dependencies, so it
builds wherever its engine builds: Windows, Linux, macOS, ARM, and
ahead-of-time platforms such as IL2CPP.

## Background

[**A common language for game telemetry**](https://briangilbert.net/writings/local-telemetry-standard/):
where the standard came from, why it kept going quiet, and what exists now.

<table>
  <tr>
    <td width="50%" valign="top">
      <a href="https://www.youtube.com/watch?v=Jcs6R4Hk9Is"><img src="https://img.youtube.com/vi/Jcs6R4Hk9Is/hqdefault.jpg" alt="Video: Leveling Up Gaming: Building a Standard for Epic Gameplay"></a><br>
      <b>Leveling Up Gaming: Building a Standard for Epic Gameplay</b>
    </td>
    <td width="50%" valign="top">
      <a href="https://www.youtube.com/watch?v=Nq0YSA0Q6O4"><img src="https://img.youtube.com/vi/Nq0YSA0Q6O4/hqdefault.jpg" alt="Video: Overview of the iRacing API"></a><br>
      <b>Overview of the iRacing API</b>
    </td>
  </tr>
</table>

## Get involved

- **Talk to us** on [Discord](https://discord.gg/tseF4Q6U99), or the
  [mod support server](https://discord.gg/yyCp5D2Ccp) for the loader mods.
- **Propose a channel.** Shared channel names live in the
  [Common Channels annex](https://github.com/Open-Game-Standards-Alliance/OGSA-Telemetry-Standard/blob/main/CHANNELS.md);
  new entries need evidence from at least two games.
- **Contribute** issues and pull requests to any repository here.
- **Sponsor the work** through
  [GitHub Sponsors](https://github.com/sponsors/BrianGilbert). It funds the
  specification, validation and SDKs.

<sub>Founded by [Brian Gilbert](https://github.com/BrianGilbert) in 2024 and developed in the open. Everything here is MIT licensed and free for anyone to implement, from hobbyists to hardware vendors.</sub>
