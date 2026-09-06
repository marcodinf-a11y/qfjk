# iOS delivery without owned Apple hardware

Date: 2026-09-06  
Status: researched options; no stack, spending, enrollment, or device-access decision made.

## Finding

qfjk can plausibly ship a native iOS player client while its sole developer works on Windows/Linux: use hosted macOS builds, Apple signing, and TestFlight. The practical constraint is access to physical iOS testing, especially LAN permission and recovery behavior. Four iOS players make this an early feasibility gate for the first playable release.

Godot remains a candidate. Its stable documentation requires macOS with Xcode for iOS export; matching export templates and application identifiers are needed. C# export is described as experimental. Thus cloud macOS replaces ownership of a Mac, while an engine switch would not by itself establish a working Apple delivery route. [Godot iOS export](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_ios.html)

This is documentary feasibility, not a completed build experiment. No account, pipeline, signing identity, or physical device was available to validate.

## Build and signing routes

| Route | Recurring cost basis | Fit for this project |
| --- | --- | --- |
| GitHub Actions standard macOS runner | Standard hosted compute is free for public repositories; storage quotas still apply | Recommended first candidate if qfjk is hosted publicly on GitHub |
| Codemagic individual account | 500 free macOS M2 minutes/month; additional M2 minutes currently $0.095/minute when billing enabled | Alternative with documented signing setup and a bounded free allowance |
| Borrowed or rented Mac access | Access arrangement or provider cost remains unspecified | Useful for interactive Xcode debugging; does not supply an iPhone on the GM's LAN |

GitHub offers hosted macOS machines and distinguishes free public-repository standard runners from chargeable larger runners. Retain only useful artifacts and avoid choosing larger runners accidentally. [GitHub runner reference](https://docs.github.com/en/actions/reference/runners/github-hosted-runners), [Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions)

Codemagic's free individual plan supports a solo project; its current pricing excludes free minutes for teams. The documented Godot example demonstrates command-line build orchestration, but is an older example, not proof that current qfjk iOS export will work unchanged. [Codemagic pricing](https://docs.codemagic.io/billing/pricing/), [Godot build example](https://blog.codemagic.io/godot-games-cicd/)

Recommended future experiment: pin Godot, export templates, runner image, and Xcode; import project assets; export an Xcode project; archive/sign an iOS build; upload it to App Store Connect. Keep unsigned checks separate from trusted release jobs. GitHub documents importing certificates and provisioning profiles from secrets into a temporary keychain. Signing keys must remain outside the public repository. [GitHub signing guide](https://docs.github.com/en/actions/how-tos/deploy/deploy-to-third-party-platforms/sign-xcode-applications)

Initial signing setup need not require a locally owned Mac. Apple's API can issue a certificate from a certificate signing request. Codemagic documents generating distribution certificates using an App Store Connect API key, and uploading or fetching provisioning profiles. The exact GitHub-only bootstrap remains an implementation task; Codemagic provides a documented alternative if that setup becomes the bottleneck. Budget for certificate/profile renewal and record their actual expiry dates. [Apple certificate API](https://developer.apple.com/documentation/appstoreconnectapi/post-v1-certificates), [Codemagic signing](https://docs.codemagic.io/yaml-code-signing/signing-ios/)

## Installation choices and expiry

Apple Developer Program membership costs USD 99 per membership year, with local currency pricing where available. Open-source publication does not establish fee-waiver eligibility; Apple's listed waiver categories include qualifying nonprofits, educational institutions, and government bodies. Enrollment and accepting this recurring cost remain user choices. [Membership and enrollment](https://developer.apple.com/programs/enroll/)

TestFlight is the preferred early group-delivery candidate. Players install TestFlight and accept invitations; external testing avoids giving ordinary players App Store Connect roles. The first external build requires review and later builds may require review. Each build becomes unavailable after 90 days, so an unattended beta is unsuitable for a campaign expected to keep working indefinitely. Schedule replacement builds ahead of expiry. Apple allows internal testers only as eligible App Store Connect users, so they are not simply a synonym for invited friends. [TestFlight overview](https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview)

Ad hoc distribution is another paid-membership testing route: collect and register each device identifier, generate a profile containing the intended devices, and distribute the signed build. Apple's limit is 100 devices per product family per membership year. Its distribution guidance describes annual profile expiry and eventual re-signing. This can suit four known testers, but onboarding and renewal add maintenance; do not assume players consent to device registration. [Ad hoc profiles](https://developer.apple.com/help/account/provisioning-profiles/create-an-ad-hoc-provisioning-profile), [Device limits](https://developer.apple.com/help/account/devices/devices-overview/), [Apple distribution session](https://developer.apple.com/videos/play/wwdc2019/304/)

Free Personal Team provisioning is a poor group-distribution fit: Xcode manages it, profiles expire after seven days, and a team can register only three devices at a time. It would not directly cover four iOS players, and rebuilding/reinstalling every week is a substantial burden. [Personal Team limits](https://developer.apple.com/help/account/basics/about-your-developer-account)

For a durable public release, plan an App Store distribution decision after the beta. Publishing an open-source repository does not itself distribute installable iOS software. Internet access for initial installation and updates is separate from the requirement that gameplay work over an offline LAN; test that requirement on the actual selected distribution build.

## LAN and application lifecycle

On iOS, direct LAN access requires user permission. Include a meaningful `NSLocalNetworkUsageDescription`; declare Bonjour service types in `NSBonjourServices` when using Bonjour. Raw multicast/broadcast requires the multicast entitlement; ordinary declared Bonjour browsing and direct unicast have different requirements. Manual host-address entry still requires LAN permission. Apple's simulator does not implement local-network privacy testing, so a successful simulator connection cannot close this gate. [Apple local-network privacy](https://developer.apple.com/documentation/technotes/tn3179-understanding-local-network-privacy)

Recommendation: make manual IP/port entry available early; consider QR entry and ordinary Bonjour discovery later. Prove the unicast connection before adding discovery. Apple peer-to-peer Wi-Fi works between Apple devices, so it is not the Windows-host/Android-client foundation. Standard LAN sockets and interoperable service discovery are candidates. [Apple networking guidance](https://developer.apple.com/documentation/technotes/tn3151-choosing-the-right-networking-api)

iOS normally suspends applications after a brief background period. Treat lock-screen, switching apps, and termination as ordinary client lifecycle events. [Apple background lifecycle](https://developer.apple.com/documentation/uikit/preparing-your-ui-to-run-in-the-background)

Design inference: the Windows GM keeps authoritative state while a phone is suspended. On return, a player reconnects, restores identity/character ownership, and receives current state. Use command identities or equivalent deduplication so reconnecting cannot apply damage or a dice roll twice. Display uncertain submission status until the GM confirms it. This is a proposed protocol requirement, not a claim that Godot supplies it automatically.

## Proposed physical acceptance gate

Do this early with a minimal player client and host harness, before committing to the full application:

1. A willing tester installs the signed build through the chosen route. Record phone/tablet model, iOS version, and distribution build.
2. On the GM's actual Windows notebook and a shared Wi-Fi network, grant LAN access and join by address. Test denied permission and recovery through Settings.
3. Repeat with the intended discovery method, if included, and document Windows firewall and router/client-isolation failure messages.
4. Disconnect the router's internet uplink while preserving Wi-Fi. Cold-launch the already installed client, join, read a sheet, and exchange dice commands. This establishes the offline behavior that CI cannot demonstrate.
5. Lock the phone, switch applications, interrupt Wi-Fi, and terminate/relaunch during an unacknowledged roll. Confirm authoritative resynchronization and no duplicate committed action.
6. Before group acceptance, run four actual iOS clients plus Android together. Check readable sheets, touch targets, map transfer, battery/thermal behavior, and repeated reconnection through a session.

These are proposed acceptance checks, not completed tests. A willing player's device can cover them during an agreed session; ownership and availability are not assumed. Otherwise a borrowed/purchased test device or another explicit physical testing arrangement is needed. Hosted CI is sufficient for build checks, but it cannot reproduce this household's complete Windows-to-iOS network and interaction path.

## Implication for modding

Apple's review guideline 2.5.2 restricts downloading or executing code that introduces app functionality; guideline 4.7 provides specific software categories with additional conditions. Do not promise arbitrary downloaded GDScript/native plugins on iOS. A candidate boundary is data-driven player content with extensible rules executed by the desktop GM, but approval and exact capability limits remain a separate decision. Supply reviewers an accessible demonstration of LAN-dependent functionality. [Apple review guidelines](https://developer.apple.com/app-store/review/guidelines/)

## Decisions this research exposes

- Accept annual Apple membership and beta build renewal, or choose a different first-release delivery scope?
- Who can explicitly provide recurring physical iOS testing, on which models and OS versions?
- Prefer public GitHub macOS CI or Codemagic's signing workflow, and what spend ceiling applies?
- Is manual host entry enough initially, or is automatic LAN discovery mandatory?
- When should beta distribution become durable App Store distribution?
- What mod capability must run on iOS itself versus the authoritative desktop GM?

Recommended next decision: settle budget and real-device access, then require the small signed LAN experiment as a stack-acceptance gate. Moving web forward is a possible change of scope if native delivery is unacceptable; this report does not assume that change.
