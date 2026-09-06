# RPG extension needs for qfjk

Date: 2026-09-06. Research for **Compare RPG mechanics to bound the extension model**. These are architectural recommendations and evidence, not an accepted design or a commitment to ship additional systems.

## Recommendation

Build the first system for the group's D&D 5e 2014 campaign, while keeping session authority, persistence, permissions, map presentation, and asset delivery independent of its rules. Give the rules module responsibility for character fields, progression, roll interpretation, effects, action eligibility and movement costs. The comparisons below justify these boundaries; they do not justify implementing a universal RPG engine.

Preserve a path for both authored content and executable rule extensions. A homebrew description, a numerical modifier, a change to natural-one precedence, and an Echo Knight's alternative attack origin are materially different extension needs. A data-only catalogue cannot comfortably express all four. The execution format and mobile constraints require a separate decision.

## Editions and evidence limits

- **D&D:** 5e 2014, anchored in SRD 5.1; not the 2024 rules. The user's wider supplement collection and Echo Knight exceed the SRD baseline.
- **Das Schwarze Auge:** DSA5 as documented by Ulisses' live rule wiki. Its references to a fourth revised printing are not DSA fourth edition. This is a complexity sample, not a proposed second supported system.
- **Star Wars:** the representative investigated is FFG's narrative-dice family: Edge of the Empire, Age of Rebellion and Force and Destiny share core mechanics. The similarly named West End Games game is a different six-sided-dice system, as its official anniversary reissue explains. Wizards' older d20/Saga editions are another clarification candidate; a usable first-party online rules reference was not established in this bounded investigation, so their mechanics are not compared here. Ask for publisher and edition before any implementation. [FFG family overview](https://www.fantasyflightgames.com/en/starwarsrpg/), [WEG reissue](https://www.fantasyflightgames.com/en/products/star-wars-the-roleplaying-game-30th-anniversary-edition/).
- **Legend of the Five Rings:** FFG's 2018 edition, commonly called fifth edition, sampled through its publisher's release overview. Do not conflate it with earlier editions' roll-and-keep mechanics. The overview itself contrasts its opportunities with earlier raises. Peripheral mechanics below are explicitly sampled from the official **2017 beta**, not certified against the final commercial rulebook. [Publisher overview](https://www.fantasyflightgames.com/en/legend-of-the-five-rings-roleplaying-game/).
- **Free systems:** Fate Core and Cairn first edition SRD v1.0. Both offer reusable rules text under explicit licenses, making them more useful extension experiments than merely free quickstarts.

## Mechanics that challenge a D&D-shaped core

### D&D 2014: the shipping baseline

D20 checks, level/class progression, carried equipment, spell slots and rest-based features suit a specialized character workflow. An action, conditional bonus action, reaction and movement are distinct resources. Speed, difficult terrain, movement modes and conditions interact. Ordinary conditions do not become stronger simply by applying the same condition twice; exhaustion instead has six cumulative levels. Effects may expire at a turn boundary, after time, or when their source ends. These details belong to a versioned D&D rules implementation. [SRD 5.1, character advancement, combat and conditions](https://media.wizards.com/2023/downloads/dnd/SRD_CC_v5.1.pdf).

The user's natural-one skill-check critical failure is an explicit campaign override, not the ordinary ability-check rule. Preserve original die faces independently of totals and record the rule that produced the outcome. “Regardless of other abilities” establishes intended priority, but interactions with rerolls and replaced dice still need an example from the GM before precise automation is specified.

### DSA5: thresholds, accumulation and interruptible work

A skill check involves three attribute checks; skill points compensate for overshoots. A single “roll total versus difficulty” field loses the relevant intermediate evidence. [Ulisses skill checks](https://dsa.ulisses-regelwiki.de/grundregeln/fertigkeitsproben.html).

Progression spends adventure points with activation costs, cost categories and limits. This calls for a general progression entry point whose actual choices and validation come from the system, rather than making “increase character level” a mandatory core operation. [Ulisses advancement](https://dsa.ulisses-regelwiki.de/Heldenerschaffung/schritt-8-steigerungen-vornehmen.html).

Condition levels accumulate; penalties from different conditions also accumulate, with an overall cap and an aggregate incapacitation threshold. Duration depends on the source. The wiki cautions that temporary changes do not automatically cause all derived values to be recalculated. Consequently, neither universal “strongest effect wins” nor universal recursive recalculation is safe. [Ulisses conditions](https://dsa.ulisses-regelwiki.de/GR_Zustand.html).

An actor receives an action, defenses and a free action. Movement can use action/free-action capacity. Casting or reloading can require several actions; electing to defend may interrupt that work after an incoming attack has been rolled. Store pending procedures and decision points, not only completed attacks. [Ulisses actions](https://dsa.ulisses-regelwiki.de/Nah-_und_Fernkampf/handlungen.html).

Inventory and magical-resource details were not exhaustively audited. Their exact capacities and recovery rules should be researched when DSA becomes a concrete target; the evidence already defeats fixed D&D slots and action economy without that additional work.

### Star Wars narrative dice: results are not one number

Success/failure and advantage/threat are independent axes; special symbols add further consequences. Let a roll yield structured results and proposed choices. [Designer explanation](https://www.fantasyflightgames.com/en/news/2013/2/27/that-star-wars-feeling/).

The official abbreviated rules demonstrate range bands, actions and maneuvers, wounds/strain, consumable gear and credits. Initiative slots belong to sides rather than being irrevocably assigned to individual actors. Minion groups share an initiative slot and wound pool; they act as one adversary. Thus neither one token = one turn nor HP = the only damage resource generalizes. These are samples from introductory rules, not an exhaustive full-rulebook conformance audit. [Under a Black Sun, rules summary and character sheets](https://images-cdn.fantasyflightgames.com/filer_public/18/ff/18ff8afe-bf19-47a3-97e5-a313ded3d6b3/under_a_black_sun_lores.pdf).

Critical injuries can have different persistence, including encounter effects and injuries needing healing. Experience can be spent between sessions. Keep injury records and advancement transactions extensible. [Injury explanation](https://www.fantasyflightgames.com/en/news/2015/6/4/hard-knocks/), [official continuation adventure](https://images-cdn.fantasyflightgames.com/filer_public/b1/aa/b1aaa46b-70ad-4f27-bd4d-930c245db53d/a-call-for-heroes.pdf).

The publisher explicitly retains GM final say while encouraging player narrative input. Host authority should govern acceptance and storage without implying that every result is mechanically predetermined. [GM guidance](https://www.fantasyflightgames.com/en/news/2017/7/31/gm-guide-the-heros-journey/).

### L5R: player choice after the roll

Players construct ring/skill dice pools and choose which dice to keep. Success, opportunity and strife interact without becoming one scalar score. A roll therefore needs a pending-choice state before resolution; automatically selecting the numerically strongest result would remove play decisions. Ring selection also expresses approach, with GM involvement. [Publisher rules overview](https://www.fantasyflightgames.com/en/legend-of-the-five-rings-roleplaying-game/).

The official beta supplies further design probes: range bands rather than equal grid distances, stance-dependent actions, XP-priced techniques and school prerequisites, named conditions and source-specific durations, equipment carrying judgments, and Void points. These are evidence that such seams are worth preserving, **not final-edition requirements**. Final progression, condition stacking, action timing and inventory rules remain unverified here. [Official beta, techniques and scenes/conflicts](https://images-cdn.fantasyflightgames.com/filer_public/dc/2f/dc2f92bb-bfd2-4f12-99eb-1d084491de65/l5r00_beta_rulebook.pdf).

### Fate Core: narrative state and progression without levels

Checks total four Fate dice (positive, negative or blank) and a skill rating; invoking an aspect can alter or reroll the result. Preserve post-roll interventions as well as initial rolls. [Official actions and outcomes](https://fate-srd.com/fate-core/actions-outcomes).

Fate distinguishes physical and mental conflict, uses aspects and zones, and organizes exchanges into turns with responses. Stress and consequences have different recovery semantics. A freeform aspect can matter mechanically without being a predefined condition enum. [Conflicts](https://fate-srd.com/fate-core/conflicts), [scene geometry](https://fate-srd.com/fate-core/setting-scene), [stress/consequences](https://fate-srd.com/fate-core/stress-consequences).

Milestones support changes to skills/aspects and increases in capability. Extras can represent equipment and other special elements. Neither an item-weight ledger nor class-level advancement is a universal sheet requirement. [Advancement](https://fate-srd.com/fate-core/advancement-change), [extras](https://fate-srd.com/fate-core/extras).

The GM may offer success at a cost and adapt mechanics to intent. Automation should support recording negotiated outcomes, not only applying a fixed combat formula. [GM procedures](https://fate-srd.com/fate-core/what-do-during-play).

### Cairn first edition: inventory is also a condition resource

Saves roll under an ability, with one succeeding and twenty failing. Attacks roll damage directly rather than making a to-hit roll. Characters are classless; growth follows experience in the fiction. Ten inventory slots include cumulative fatigue, and filling them reduces hit protection to zero. A turn permits movement and an action, with uncertain order resolved through a Dexterity save. The Warden interprets fictional positioning and reasonable actions. These provide compact counterexamples to natural-one failure, mandatory attack checks, fixed level-up and inventory containing only physical objects. [Cairn first edition SRD](https://cairnrpg.com/first-edition/cairn-srd/).

## Echo Knight as a concrete acceptance probe

The publisher preview confirms a separately positioned echo, attacks through it, position swapping, limited extra attacks, sensory projection that affects the character, and higher-level support for two echoes. It does not settle every targeting/timing edge case. [Official Echo Knight guide](https://www.dndbeyond.com/posts/1152-fighter-101-a-guide-to-the-echo-knight-from?_pxhc=1662854400561&page=2).

Design inference: distinguish the character who spends an action from its spatial origin, the entity selected as target, its map representation, and its controller. A character can control several representations without copying the character sheet or granting each representation an independent turn. Couple lifecycle/effect sources explicitly so removing an echo can resolve dependent state. Confirm detailed implementation against the group's licensed text and rulings; community FAQs are not normative evidence.

## Small core proposal and deferred generalization

Recommended shared responsibilities:

1. Stable identities, ownership and visibility for campaign entities and assets.
2. GM-authoritative commands, validated state changes, persistence and an inspectable history.
3. A versioned system/content identity, with namespaced extension data and explicit compatibility checks.
4. Presentation surfaces for sheets, prompts, rolls, maps and effects; system-owned interpretation and validation.

D&D supplies its progression workflow, resource names, dice evaluation, conditions, turn sequence and movement rules. Store effect source, subject and lifecycle information now; let the system decide stacking and expiry. Map coordinates describe presentation, while a rules query decides movement cost and reach. Keep dice faces and interpretation separately inspectable.

For the requested mixed automation, resolve policy through **global → creature type → specific creature**, with the most specific configured value winning. This is a proposed policy, awaiting decision. Separate whether approval is required from whether the module knows how to resolve an action. Unresolved player choices, ambiguous rules and unsupported homebrew require a defined pause/manual path even in automatic mode.

Defer shipping alternate RPG modules, a universal formula language, arbitrary runtime plugins, universal condition stacking, spaceship combat and a mod marketplace. Before freezing a public extension API, a small later conformance prototype could exercise a DSA triple check, a Star Wars symbolic outcome, an L5R keep choice and Cairn fatigue. Research alone does not prove an interface.

## Content availability is not redistribution permission

SRD 5.1 is explicitly CC BY 4.0; its license does not grant the group's entire purchased supplement library. [Official SRD licensing](https://www.dndbeyond.com/srd). Fate offers official downloadable CC-BY SRDs; use those for incorporation, as its licensing page specifically distinguishes them from edited website text. [Fate official licensing](https://fate-srd.com/official-licensing-fate). Cairn text is CC BY-SA 4.0. [Cairn publisher site](https://cairnrpg.com/).

The DSA wiki and FFG quickstarts being readable without charge do not establish permission to redistribute or modify their content. No open redistribution license was established for them here. Keep code licensing, rules text licensing, setting/IP rights and artwork licensing separate. Choose packaged content in the dedicated content decision, especially for Tasha's, Mordenkainen's and Echo Knight.

## Follow-up decisions

- Specify the minimal modding promise: personal content entry, declarative overrides, authored scripts, or a combination.
- Define “automatic resolution” boundaries and GM correction/undo behavior, including reactions and mixed NPC policies.
- Confirm how the natural-one house rule handles rerolls, substituted rolls and features that impose minimum results.
- Decide how unavailable licensed content is represented and supplied without bundling it.
- Leave exact alternate-system editions and final-rulebook conformance for when support becomes real.
