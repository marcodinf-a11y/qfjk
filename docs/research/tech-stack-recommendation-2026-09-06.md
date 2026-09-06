# Preliminary technical stack for qfjk

**Project:** open-source, system-neutral tabletop RPG / virtual tabletop  
**Checked:** 6 September 2026  
**Status:** preliminary direction; validate with the Phase 0 platform spikes before treating it as final  
**Targets:** Android, iOS, Linux, Windows 11; web potentially later  
**Recommendation:** Godot 4, primarily statically typed GDScript, with a
peer-hosted authoritative LAN session and SQLite-backed local documents

The capitalization previously written as “ANdroid” was only a typo; the target
is the ordinary Android platform.

## Decision in one page

Build qfjk as **one Godot 4 project and one application**, with player and GM
roles selecting different capabilities and responsive screens. Use:

- the latest stable Godot 4 release, pinned rather than silently upgraded;
- statically typed GDScript for the application and game/domain logic;
- Godot Control nodes for sheets, inventory, encounter controls, and other UI;
- Godot's 2D/3D scene system for maps, tokens, avatars, cameras, picking, and
  animation;
- SQLite, hidden behind repository interfaces, for local character and
  campaign state;
- JSON for declarative rules/content packs and a versioned JSON message
  envelope for the first network protocol;
- an authoritative session hosted by the GM's device over ENet/UDP on the LAN;
- DNS-SD/mDNS for discovery, plus manual IP and QR-code join as fallbacks; and
- glTF 2.0 binary (`.glb`) as the interchange and user-import format for 3D
  models, with Blender source files converted and validated in the asset
  pipeline.

Godot exports to the required desktop, mobile, and web targets and is MIT
licensed. Its own documentation lists Windows, Linux, Android, iOS, and web
exports, while its license permits use, modification, and redistribution and
does not impose the engine's license on game content
([export documentation](https://docs.godotengine.org/en/stable/tutorials/export/),
[license](https://godotengine.org/license/)). More importantly, it is already a
3D game engine. The project therefore gets an editor, scene graph, animation,
input, picking, rendering, and game-oriented networking without assembling a
second engine inside an application UI framework.

This recommendation does **not** imply that a small team should start by making
“Baldur's Gate 3 with a GM button.” The useful product path is a good local
character manager, then an offline LAN tabletop, then progressively richer 3D.
The BG2/BG3 comparison is a long-term interaction and presentation direction,
not a viable first-release content or fidelity budget.

## Why Godot is the best fit

The hard requirement is not the character form UI. Flutter, Tauri, and many
desktop frameworks can build that. The architectural constraint is that the
same product must eventually render and manipulate animated characters in a 3D
map on mobile and desktop, offline, while retaining ordinary application UI.
Godot covers both sides with one runtime.

Use the **Mobile renderer** as the initial native baseline, even on desktop,
and keep scenes within mobile budgets. Use Forward+ only for optional desktop
quality features. Maintain a Compatibility-renderer test profile from an early
stage if web remains a serious goal. Godot describes Mobile as intended for
mobile and desktop 3D, Forward+ as desktop-oriented, and Compatibility as the
only web renderer; switching between Compatibility and the other renderers can
require scene, lighting, and environment adjustments
([renderer comparison](https://docs.godotengine.org/en/stable/tutorials/rendering/renderers.html)).
That makes “we will export to web at the end” an unsafe assumption unless the
compatibility profile is continuously exercised.

Choose **GDScript rather than C#** for the primary implementation. Statically
typed GDScript is close to the engine API, keeps the toolchain small, and
supports every target. Godot's current feature list says C# mobile support is
experimental and C# projects cannot export to web
([Godot feature list](https://docs.godotengine.org/en/stable/about/list_of_features.html)).
C++ GDExtensions should be limited to narrow platform or performance adapters,
not used as a premature second application language.

Godot UI is less convenient than Flutter or HTML/CSS for dense, text-heavy
forms and accessibility. This is the principal cost of the recommendation.
Mitigate it with a small design system of reusable Control scenes, keyboard and
screen-size testing, and strict separation between UI and domain state. Do not
place character rules directly in scene scripts.

## Candidate comparison

| Candidate | Required native targets | 3D/game capability | Web path | Open-source/toolchain fit | Verdict |
|---|---|---|---|---|---|
| **Godot 4 + GDScript** | Yes | Full game engine, editor, animation, scene graph, input, networking | WebAssembly/WebGL 2 via Compatibility renderer | Engine is MIT; no royalties or revenue tier | **Recommended** |
| **Tauri 2 + TypeScript/Rust + Babylon.js** | Yes, with platform-specific edges | Capable web 3D engine, but application must assemble game/editor workflows | Excellent code-reuse story | Tauri is MIT/Apache-2.0; Babylon.js is Apache-2.0 | Strong runner-up if qfjk remains mostly a form/editor app with modest 3D |
| **Flutter + Dart** | Yes | Excellent app UI; core rendering model is application/2D-oriented, so serious 3D needs a young low-level API, third-party engine, or embedded native view | Strong for app UI | BSD-3-Clause | Good for a character manager, poor fit for the stated VTT end state |
| **Unity 6 + C#** | Yes | Most mature candidate here for high-end 3D production | Supported Web platform | Proprietary editor/runtime terms and revenue-tier policy | Technically capable, but conflicts with an open toolchain goal and adds business-policy risk |
| **Bevy + Rust** | Claimed for all required targets | Modern 2D/3D ECS engine, code-first | WebGL/WebGPU/Wasm | MIT or Apache-2.0 | Credible future alternative, but too much platform/editor work for the first product |

### Tauri 2 plus web 3D

Tauri 2 combines a Rust backend with HTML rendered in the operating system's
webview and is licensed under MIT or Apache-2.0
([architecture](https://v2.tauri.app/concept/architecture/),
[repository](https://github.com/tauri-apps/tauri)). Its mobile support is real,
but Tauri's own release discussion acknowledges that not every desktop feature
or plugin has mobile parity and that the mobile developer experience still
needs work
([Tauri 2 release-candidate assessment](https://tauri.app/blog/tauri-2-0-0-release-candidate/)).

Babylon.js supplies an Apache-2.0 scene engine with WebGL 1/2 and WebGPU, glTF,
animation, picking, cameras, and other relevant features
([engine specifications](https://www.babylonjs.com/specifications/),
[license](https://github.com/BabylonJS/Babylon.js/blob/master/license.md)).
This combination is viable and has much better form/layout ergonomics than
Godot.

Its disadvantages are material for qfjk: the team must integrate the editor,
game loop, native networking, lifecycle, and asset workflow itself; and the 3D
runtime differs by installed webview. Tauri uses WebView2 on Windows, the
system Android WebView, and WebKit/WKWebView on Apple platforms and WebKitGTK
on Linux. Android capability consequently follows the device's selected
webview, and Linux WebKit versions vary by distribution
([Tauri webview versions](https://v2.tauri.app/reference/webview-versions/)).
Choose this stack only if a prototype establishes that the product is at least
roughly 80% document/editor UI and its 3D map can remain a bounded viewer.

### Flutter

Flutter officially supports Android, iOS, Windows, Linux, and web from one
codebase and is BSD-3-Clause licensed
([supported platforms](https://docs.flutter.dev/reference/supported-platforms),
[license](https://github.com/flutter/flutter/blob/master/LICENSE)). It is the
best candidate here for character sheets and responsive, accessible app UI.

It is not a game engine. Flutter's documented architecture centers on a widget
and render-object tree, with most widgets laid out as `RenderBox` objects in a
2D Cartesian space
([architecture](https://docs.flutter.dev/resources/architectural-overview)).
Its public `flutter_gpu` API is a low-level API for building rendering packages
from scratch, and Flutter documentation still labels the package experimental
([API](https://api.flutter.dev/flutter/flutter_gpu/),
[status reference](https://docs.flutter.dev/release/breaking-changes/opengles-render-to-texture-top-down)).
Embedding a separate 3D engine would create two rendering, input, lifecycle,
and testing stacks. Flutter becomes the better choice only if 3D is removed or
reduced to a small optional viewer.

### Unity

Unity supports the required mobile and desktop players and a web target
([Unity 6 system requirements](https://docs.unity3d.com/6000.0/Documentation/Manual/system-requirements.html)).
It has the most mature commercial ecosystem in the comparison and is a sound
technical choice if high-end 3D production and access to Unity specialists
dominate every other concern.

It is not an open-source engine and its use is governed by commercial terms and
financial tiers. At the check date, Unity Personal is free below USD 200,000 in
applicable revenue/funding and Pro is required above that threshold; Unity has
cancelled its former Runtime Fee
([current pricing](https://unity.com/products),
[Runtime Fee cancellation](https://unity.com/products/pricing-updates)). Those
terms may change independently of qfjk. An open-source application can be made
with Unity, but contributors cannot inspect, redistribute, or fork the complete
toolchain as they can with Godot. That is enough to reject it for this project
unless the project's open-toolchain priority changes.

### Bevy

Bevy is the strongest credible open-source alternative. It is a Rust,
MIT/Apache-2.0, code-first engine with 2D/3D rendering, animation, glTF loading,
UI, and stated Windows, Linux, web, iOS, and Android support
([Bevy feature overview](https://bevy.org/),
[repository and license](https://github.com/bevyengine/bevy)). Rust would make
an excellent domain and networking language.

The Bevy project itself warns that important features are missing,
documentation is sparse, and breaking releases occur approximately every three
months. Its mobile examples require explicit Android Gradle/NDK and iOS Xcode
integration rather than a mature editor export workflow
([official examples](https://github.com/bevyengine/bevy/blob/main/examples/README.md)).
Bevy would trade licensing certainty for substantially more engine, UI,
tooling, and mobile-integration work. Revisit it if the team already has deep
Rust/game-engine expertise or Godot fails a concrete prototype, not merely as
an architectural preference.

## Proposed application architecture

Use one repository, one Godot project, and one domain model. Keep the important
boundaries explicit:

```text
Player UI       GM UI       Shared 2D/3D table UI
     \            |             /
        application commands/queries
                    |
     system-neutral domain model + dice
          /             |             \
 persistence adapter  session adapter  content/asset adapter
      (SQLite)        (ENet/WebSocket)   (JSON + GLB)
```

The domain layer should not depend on scene paths or individual Control nodes.
It should describe concepts such as Character, Item, Resource, Roll,
Participant, Encounter, Combatant, Map, Token, SceneObject, Campaign, and
Session. Ruleset-specific terms such as a class, ancestry, spell slot, or
advantage belong in versioned content/rule packs, not in the universal core.

Rules and content packs should initially be declarative JSON with JSON Schema,
stable IDs, pack/version dependencies, and deterministic expressions for dice
and calculated fields. Do **not** allow packs to execute arbitrary GDScript in
the first releases. A data-only extension format is easier to validate, sync,
license, and safely load from other users.

Use explicit application commands (`MoveToken`, `EquipItem`, `ApplyDamage`,
`EndTurn`) rather than synchronizing the visual scene tree as the source of
truth. This makes undo/history, permissions, tests, persistence, reconnects,
and a later web client tractable. Rendered nodes are projections of domain
state.

### One application, not two

Ship one application with role/capability-based workspaces:

- a player workspace optimized for phones, tablets, and quick character/dice
  actions;
- a GM workspace optimized for desktop and large tablets; and
- a shared tabletop workspace with controls appropriate to the current role.

Two independent apps would duplicate store releases, migrations, onboarding,
network compatibility, assets, and bug fixes. It would also make it harder for
a GM to join as a player or for ownership to change. If download size or store
positioning later demands separate packages, produce **two export flavors from
the same project and modules**, not separate codebases.

The GM host should normally be Windows or Linux. An iPad can host while the app
is foregrounded, but it should not be the reliability baseline: iOS normally
suspends an app shortly after it enters the background and grants only limited
completion time for ordinary foreground work
([Apple background execution](https://developer.apple.com/documentation/uikit/extending-your-app-s-background-execution-time)).
Android may also reclaim application processes; a persistent Android host would
require additional foreground-service/lifecycle work
([Android process lifecycle](https://developer.android.com/guide/components/activities/process-lifecycle)).
Clients must reconnect from a fresh snapshot after suspension or network
changes.

## Local persistence

Use **SQLite per character library or campaign**, accessed only by the local
application. SQLite is a stable, cross-platform, single-file format with atomic
transactions and is explicitly suitable as an application file format
([single-file format](https://www.sqlite.org/onefile.html),
[application-file rationale](https://www.sqlite.org/appfileformat.html)). Keep
large maps, audio, and 3D models as files in a content-addressed asset directory
and store their hashes and metadata in SQLite; putting every large asset in the
database makes import, caching, and partial transfer less convenient.

Important persistence rules:

- only the authoritative host writes the canonical live session database;
- never open the same SQLite file directly from multiple devices or a network
  share;
- use schema migrations and transactional saves from the first prototype;
- autosave after accepted commands and keep rotating local backups;
- export/import a documented bundle containing a manifest, a clean database
  snapshot, and referenced assets; and
- expose a human-readable JSON character export for interoperability even if
  SQLite remains the working store.

Godot does not provide SQLite as a core GDScript API. The current community
Godot-SQLite GDExtension is MIT licensed and publishes Windows, Linux, Android,
iOS, and web entries, but its own documentation calls browser stability
dependent on the browser
([Godot-SQLite repository](https://github.com/2shady4u/godot-sqlite)). Pin its
source and exact binary version, build it in CI for every native target, and
wrap it in a repository interface. Before adopting it, the platform spike must
prove migrations, crash recovery, backup/restore, and mobile export on physical
devices. If that spike fails, use versioned JSON files for the first milestone
rather than delaying the product while writing a database extension. A future
web build can supply IndexedDB-backed persistence through a different adapter.

## LAN discovery and session protocol

“Peer to peer” should mean **peer-hosted with no cloud or Internet dependency**,
not a fully connected mesh. The GM's instance is an authoritative server; all
other devices are clients. This matches the social authority of the tabletop,
makes secret GM state possible, and avoids CRDT/conflict-resolution machinery.
If the host leaves, pause and save. Host migration can be added much later if
real usage proves it necessary.

Use Godot's ENet-based multiplayer transport for the first native session.
Godot's high-level networking supports ENet, WebRTC, and WebSocket peers; ENet
uses UDP and supports LAN clients connecting to an internal address
([high-level networking](https://docs.godotengine.org/en/stable/tutorials/networking/high_level_multiplayer.html)).
Use reliable messages for commands, chat, dice results, ownership changes, and
snapshots. Use unreliable-ordered messages only for transient cursor or token
drag previews; Godot documents these transfer modes and independent channels
([MultiplayerPeer](https://docs.godotengine.org/en/stable/classes/class_multiplayerpeer.html)).

Do not make raw Godot RPC node paths the durable protocol. Put an explicit
application envelope inside the transport:

```json
{
  "protocol": 1,
  "session": "opaque-id",
  "message_id": 1042,
  "kind": "move_token",
  "payload": {}
}
```

On join, negotiate protocol and content-pack versions, authenticate with a
short-lived join code, send a permission-filtered snapshot, then ordered
commands/events from a sequence number. Validate sizes, IDs, permissions, and
numeric bounds on the host. Never deserialize arbitrary objects or accept file
paths from a peer. A LAN is not automatically trusted; plan DTLS or another
authenticated encrypted transport before handling private data on shared
networks. Godot exposes DTLS support for UDP/ENet at its lower network layer
([ENetConnection](https://docs.godotengine.org/en/stable/classes/class_enetconnection.html)).

### Discovery

Advertise the host as a narrowly scoped DNS-SD service such as
`_qfjk._udp.local`, with the selected port and minimal TXT data (protocol
version and display name, not campaign secrets). DNS-SD is designed for local
service discovery: Android's official NSD API implements DNS-SD and explicitly
identifies multiplayer gaming as a use case
([Android NSD](https://developer.android.com/develop/connectivity/wifi/use-nsd));
Apple Bonjour likewise combines mDNS and DNS-SD for advertise, browse, and
connect operations
([Apple networking guidance](https://developer.apple.com/documentation/technotes/tn3151-choosing-the-right-networking-api)).

Implement discovery behind a platform adapter. It will require a small Android
plugin using `NsdManager`, an Apple plugin using Network/Bonjour APIs, and a
desktop mDNS implementation. Always offer:

1. discovered sessions;
2. a QR code carrying host address, port, protocol version, and a short-lived
   join token; and
3. manual IP/hostname and port entry.

Those fallbacks are essential because guest Wi-Fi client isolation, multicast
filtering, VPNs, firewalls, and permission denial can all make discovery fail
even while direct LAN connectivity works. Let the host choose an available port
rather than assuming a globally free one; Android's NSD guidance recommends
advertising a dynamically selected port to avoid conflicts.

### Mobile permissions that must be designed, not patched in later

On iOS/iPadOS, include `NSLocalNetworkUsageDescription` and list the qfjk
Bonjour service type in `NSBonjourServices`. The first local operation can
prompt the user; denial must produce a useful offline/manual-join explanation.
Apple requires local-network disclosure for direct unicast, multicast, and
Bonjour access
([privacy key](https://developer.apple.com/documentation/BundleResources/Information-Property-List/NSLocalNetworkUsageDescription),
[local-network technote](https://developer.apple.com/documentation/technotes/tn3179-understanding-local-network-privacy)).
If qfjk implements raw multicast or broadcast instead of service-specific
Bonjour, iOS additionally requires the restricted multicast entitlement. Avoid
that fallback unless it proves necessary.

On Android, enable the `INTERNET` permission; Godot notes that Android blocks
all network communication without it
([PacketPeerUDP](https://docs.godotengine.org/en/stable/classes/class_packetpeerudp.html)).
Also prepare now for the current Android local-network privacy transition:
Android 16 offers opt-in restrictions, while Android 17 makes local access a
runtime `ACCESS_LOCAL_NETWORK` permission for apps targeting SDK 37 or higher.
The restrictions cover TCP, UDP, mDNS, and `NsdManager`, and denial must be
handled gracefully
([Android local-network permission](https://developer.android.com/privacy-and-security/local-network-permission)).
If raw multicast/broadcast is used on older devices, some devices also require
`CHANGE_WIFI_MULTICAST_STATE`, as Godot's UDP documentation notes. Prefer
platform DNS-SD to a home-grown broadcast protocol.

Windows Defender Firewall may prompt when the host first listens. Provide a
clear host-status screen showing bind address, port, discovery state, and a
copyable manual endpoint. Bind only to LAN interfaces where practical and do
not add UPnP port forwarding: Internet exposure is outside the stated scope.

## The later web client

A web export is possible, but it is a separate product milestone, not a free
consequence of cross-platform code. Godot 4 web exports require WebAssembly and
WebGL 2 and can use only the Compatibility renderer. Native Android/iOS exports
perform substantially better. Low-level TCP/UDP is unavailable in browsers;
Godot web supports HTTP, WebSocket client, and WebRTC instead
([Godot web export limitations](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_web.html)).

Therefore a browser client cannot discover or join the native ENet session
directly and should not be expected to host it. Add a transport adapter on the
native GM host:

- **WebSocket** is the simplest first browser bridge and is adequate for a VTT's
  command/state traffic. Godot supports it in native and web exports
  ([Godot WebSockets](https://docs.godotengine.org/en/stable/tutorials/networking/websocket.html)).
- **WebRTC data channels** offer reliable or partially reliable direct data,
  but still require offer/answer and candidate signaling. The W3C specification
  explicitly leaves that out-of-band exchange to the application
  ([WebRTC](https://www.w3.org/TR/webrtc/)). Use it only if measurement shows
  WebSocket latency to be a problem.

Discovery will need a typed address, QR/deep link, or a page served by the GM
host. Modern browsers may also prompt before a public site accesses private
network addresses; Chrome's Local Network Access permission launched for this
class of request
([Chrome LNA](https://developer.chrome.com/blog/local-network-access)).
Threaded Godot web exports require secure-context and cross-origin-isolation
headers, while single-threaded exports are easier to host but less performant.
These constraints argue strongly for shipping native apps first.

## 3D asset format and pipeline

Use Blender as the source authoring tool and **glTF 2.0 binary (`.glb`) as the
checked delivery/interchange format**. glTF is a royalty-free,
runtime-neutral format for compact transmission of scenes, meshes, PBR
materials, skins, and animations; `.glb` can contain the scene and binary
resources in one file
([Khronos overview](https://www.khronos.org/gltf/),
[specification](https://registry.khronos.org/glTF/specs/2.0/glTF-2.0.html)).
Godot recommends glTF 2.0, supports `.gltf` and `.glb`, and can load them at
runtime in exported projects
([Godot 3D formats](https://docs.godotengine.org/en/stable/tutorials/assets_pipeline/importing_3d_scenes/available_formats.html),
[runtime loading](https://docs.godotengine.org/en/stable/tutorials/io/runtime_file_loading_and_saving.html)).

Pipeline rules:

- retain `.blend` sources and licenses/attribution metadata in the asset-source
  area, but make CI invoke a pinned Blender version to produce GLB;
- validate GLB with Khronos glTF Validator and run an in-engine import smoke
  test;
- standardize meters, +Y up, material conventions, skeleton/animation names,
  avatar attachment points, and collision proxies;
- generate at least desktop and mobile quality tiers, with limits for triangle
  count, bones, material slots, texture dimensions, lights, and animation size;
- use baked lighting and restrained shadow-casting lights for maps; and
- hash delivery files so peers can skip assets they already possess and resume
  interrupted transfers.

Start with PNG/JPEG textures inside or alongside GLB and let Godot's import
pipeline create target-specific runtime compression. KTX 2/Basis Universal is
an attractive later optimization—the Khronos extension reduces transmission
and GPU memory costs—but Godot still has open limitations around full KTX2
handling, especially runtime loading
([Khronos extension](https://github.com/KhronosGroup/glTF/blob/main/extensions/2.0/Khronos/KHR_texture_basisu/README.md),
[Godot limitation proposal](https://github.com/godotengine/godot-proposals/issues/13660)).
Do not make it a required user-import format until physical-device tests prove
the exact engine version and workflow.

Treat downloaded/imported models as untrusted. Enforce byte, node, texture,
mesh, animation, and decompressed-memory limits before instancing them. Keep
copyright/license metadata next to every distributable asset; an open-source
engine does not make third-party models open content.

## Testing, build, and release

Pin Godot, Blender, native extensions, export templates, Android SDK/NDK, and
test tooling. Upgrade deliberately through a compatibility branch.

Use:

- GdUnit4 for GDScript unit and scene tests; its project supports assertions,
  mocking, and scene testing under MIT
  ([GdUnit4 repository](https://github.com/godot-gdunit-labs/gdUnit4));
- pure domain tests for dice parsing/evaluation, calculated fields, inventory,
  permissions, command validation, migrations, and deterministic snapshots;
- protocol golden tests and fuzz/property tests against malformed messages and
  imported content;
- headless integration tests running one host and multiple clients with packet
  loss, reorder, disconnect, reconnect, stale version, and unauthorized-command
  scenarios;
- renderer screenshots/performance scenes for Mobile and Compatibility; and
- physical Android and iOS smoke tests for discovery, permission denial,
  background/foreground transitions, runtime GLB import, and reconnect.

Godot supports headless operation and command-line release/debug export, making
CI exports practical
([headless server](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_dedicated_servers.html),
[command-line export](https://docs.godotengine.org/en/stable/tutorials/export/exporting_projects.html)).
Build Linux, Windows, and Android on appropriate CI runners, and build/sign iOS
on macOS. Godot requires macOS with Xcode for iOS export; the exported Xcode
project still needs a bundle ID, team, signing, and provisioning
([Godot iOS export](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_ios.html)).
iOS is consequently not a simple unsigned download: budget for Apple developer
credentials, App Store/TestFlight or another permitted distribution route, and
real-device testing. Keep signing credentials outside the repository.

Initial release artifacts should be a signed Windows installer or portable
archive, a Linux AppImage and/or Flatpak, an Android APK for direct LAN-table
testing plus an AAB for store distribution, and a signed iOS archive. Publishing
formats should follow proven user demand; producing every Linux packaging
format on day one adds maintenance without product value.

## Phased delivery and stop/go checks

### Phase 0: four risk spikes

Before committing the architecture, build disposable vertical slices that
prove:

1. one responsive sheet screen and one small animated GLB scene export and run
   on Windows, Linux, a low/mid-range Android device, and a physical iPhone or
   iPad;
2. a desktop host and Android/iOS clients discover, join, disconnect, and
   recover entirely without Internet access;
3. the pinned SQLite adapter migrates, saves, survives forced termination, and
   restores on every native platform; and
4. the same small 3D scene remains acceptable in the Compatibility renderer
   and a Godot web export.

If dense UI proves unacceptable, prototype the identical screen in Tauri plus
Babylon.js before changing stack. If Godot mobile export, SQLite, or native
discovery fails, fix or replace the narrow adapter rather than introducing a
second application framework immediately.

### Phase 1: local character manager

Deliver system-neutral character sheets, custom fields, inventory/equipment,
dice, local save/export/import, and responsive player/GM workspaces. No network,
map editor, automation language, or avatar creator is required.

### Phase 2: useful LAN tabletop

Add peer-hosted discovery/join, permissions, campaign/session persistence, a
2D map, tokens, fog/visibility, encounter order, dice events, reconnect, and
manual/QR fallback. This is the first real VTT milestone. Keep network messages
domain-oriented and versioned.

### Phase 3: bounded 3D tabletop

Add a 3D map camera, animated GLB miniatures, placement/picking, movement,
lighting presets, quality tiers, asset caching/transfer, and import limits.
Prefer a curated avatar-part system or external GLB import before attempting a
full character creator. Profile actual low-end phones before increasing visual
scope.

### Phase 4: richer GM authoring and optional web client

Add encounter templates, map authoring, reusable content packs, measured
automation, and desktop presentation features. Only then add a WebSocket bridge
and Compatibility-renderer web client if the browser constraints and user
demand justify the permanent second transport/persistence path.

## Final recommendation

Adopt **Godot 4 + statically typed GDScript** and prove it with the four Phase 0
spikes. Use one role-aware application, SQLite local documents, declarative JSON
rulesets, GLB assets, and a desktop-preferred authoritative GM host over
ENet/UDP with DNS-SD discovery and manual/QR fallbacks.

Tauri 2 + Babylon.js is the legitimate fallback if hands-on prototypes show
that document UI quality matters more than integrated game tooling and that the
3D tabletop can remain modest. Flutter is a good character-sheet stack but a
poor foundation for the stated end state. Unity is technically excellent but
does not match the open-toolchain objective. Bevy is promising, but asks this
project to mature too much of its mobile/editor stack itself.

The most important scope protection is architectural rather than technological:
first make an excellent offline character tool, then a reliable 2D LAN VTT,
then earn the complexity of 3D. Godot leaves that path open without requiring a
rewrite when the map becomes three-dimensional.
