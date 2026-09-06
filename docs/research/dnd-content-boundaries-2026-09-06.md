# D&D content boundaries for qfjk

Date: 2026-09-06  
Status: research findings and proposed decisions; no product-policy decision made.  
Scope: public, open-source qfjk; D&D 5e 2014; supplemental options; homebrew; GM-hosted LAN play.

## Finding

qfjk can ship a useful openly licensed D&D foundation using SRD 5.1. Supporting the group's characters is a separate requirement from redistributing every book's explanations, artwork, or option catalog. The practical first-release proposal is a versioned SRD content pack plus authoring and manual-resolution facilities for options outside that pack. Whether that meets this group's needs is a human decision, especially for Echo Knight and fully automatic encounters.

This report identifies primary-source permissions and gaps. It does not determine the legality of a particular implementation, private copy, or contract in every jurisdiction.

## Open reference documents

Wizards currently lists SRD 5.1 under both OGL 1.0a and CC BY 4.0, with a choice of license. Its FAQ explicitly allows SRD 5.1 use in VTTs. SRD 5.1 is a subset, including only one background and one feat; it is not the full 2014 catalog. The latest listed reference is SRD 5.2.1, published May 1, 2025. The 5.2 family updates wording and mechanics to the revised rules, currently called 5.5e on the site. Wizards permits mixing the SRDs but leaves compatibility to the publisher. The download page also lists German SRD 5.1. [Wizards SRD page](https://www.dndbeyond.com/srd)

Recommendation: use a distinct `dnd5e-2014` rules identity with SRD 5.1 as its initial content source. Do not automatically upgrade its exhaustion, class advancement, spells, or other behavior to the latest SRD. A later revised-rules pack can coexist. Record the exact source document and language when transforming content, because edition compatibility and permission are different checks.

SRD 5.1's legal preamble supplies a specific attribution statement naming Wizards, the document, and its source and license URLs. It also permits the compatibility phrases “compatible with fifth edition” and “5E compatible.” Implement attribution from the preamble itself when producing the pack; this note intentionally does not substitute a shortened attribution for it. [SRD 5.1, legal information, page 1](https://media.dndbeyond.com/compendium-images/srd/5.1/SRD_CC_v5.1.pdf)

CC BY 4.0 permits reproduction, adaptation, and sharing in different media. On sharing, retain supplied attribution and notices, identify modifications, and include the license or its link. The license disallows additional restrictions that prevent recipients exercising the licensed rights, grants no trademark or patent rights, and conveys no endorsement. It does not require all surrounding application code to use CC BY. [CC BY 4.0, sections 2–3](https://creativecommons.org/licenses/by/4.0/legalcode.en)

Recommendation: separate software licensing from content and asset licensing. Include attribution with distributed packs and an accessible credits view; preserve it on exports. Mark extracted, reorganized, translated, and corrected content appropriately. Choosing the code license remains a later decision; this research does not select MIT, GPL, or another license.

## What the requested supplements establish

Tasha's Cauldron of Everything is a distinct sourcebook, with optional character customization among its offerings. “Mordenkainen's” is ambiguous: the publisher has both Mordenkainen's Tome of Foes and Mordenkainen Presents: Monsters of the Multiverse. The group should identify which versions it uses. [Tasha product page](https://marketplace.dndbeyond.com/beyond-digital/tashas-cauldron-of-everything), [Tome of Foes](https://www.dndbeyond.com/sources/dnd/mtof), [Monsters of the Multiverse](https://www.dndbeyond.com/sources/dnd/motm)

Echo Knight comes from Explorer's Guide to Wildemount. The publisher's own overview describes an independently positioned echo, attacks through it, and position swapping. These are useful requirements to test against a character/encounter model. The article's public availability does not itself grant republication rights. [Official Echo Knight overview](https://www.dndbeyond.com/posts/1152-fighter-101-a-guide-to-the-echo-knight-from)

The grants reviewed do not establish permission to bundle those entire books, Echo Knight's feature descriptions, supplement artwork, or exhaustive derivative catalogs. An item appearing in an openly licensed SRD can be sourced there; matching a name in another book does not make that book's version open. Entry-level provenance is therefore preferable to labels such as “all 2014 content.”

## Mechanics, explanations, and assets

The US Copyright Office distinguishes game ideas and playing methods from potentially protected expressive text and artwork. This supports treating numerical procedures and implementation code separately from copied descriptions, illustrations, and setting material. It is US guidance, not a global clearance of every D&D implementation; it does not settle trademark, contract, or database-right questions. [US Copyright Office: Games](https://www.copyright.gov/register/tx-games.html)

Engineering proposal: make generic primitives such as resource counters, movement budgets, modifiers, linked tokens, and triggered effects available to an author. Allow a user-created option to reference a book and page without embedding its prose. Use independently authored interface language and licensed/original portraits and models. A renamed or paraphrased complete supplement is not a proposed workaround.

Wizards' Fan Content Policy is not a blanket permission for a free rules database. It excludes verbatim copying/reposting of rules and books, imposes restrictions on game uses and branding, and is revocable. Its private-group FAQ permits requiring login to play; that statement does not grant unrestricted copying or onward transfer of sourcebooks. [Fan Content Policy](https://company.wizards.com/en/legal/fancontentpolicy)

DMsGuild is a separate publishing program. Its licensing information describes program-specific rights and an exclusive right to sell published products. Its terms do not establish a general license to redistribute that content from qfjk's public repository. Any proposed licensed pack or marketplace integration requires its own review. [DMsGuild licensing information](https://help.dmsguild.com/hc/en-us/articles/12776887523479-Dungeon-Masters-Guild-Licensing-Information)

## User content and LAN transfer

Original homebrew can be represented and, where its author grants suitable rights, distributed. Calling a copied commercial subclass “homebrew” does not supply those rights. Public pack contributions should identify the author, source, license, and any third-party dependencies.

Book ownership, lawful private entry, public redistribution, and transfer to another player's device are separate questions. D&D Beyond's terms describe purchased digital content as a limited, non-transferable personal license rather than ownership. They do not establish permission for qfjk to scrape or export the purchased catalog. No approved importer/API route was established by this investigation. [D&D Beyond terms, sections 1 and 5](https://www.dndbeyond.com/en/terms-conditions)

For the user's German context, UrhG §53 allows certain private copies subject to conditions, but contains special provisions for substantially complete books, electronic database works, and onward distribution/public use. Applying these rules to selected personal notes versus a synchronized compendium requires the actual workflow and circumstances. Neither “LAN only” nor “private use” resolves the question by itself. [German UrhG §53](https://www.gesetze-im-internet.de/urhg/__53.html)

Proposed system boundary: distinguish session state from the source library. Players need approved rolls, resource values, visible token state, and condition effects; they do not inherently need the GM's entire explanatory-text collection. Let packs declare provenance and intended distribution scope, and let the GM choose what is transmitted. Such metadata records a policy; it does not prove rights or make an otherwise unauthorized transfer lawful.

## Concrete options and next questions

Three feasible planning options are:

1. Bundle SRD 5.1 and original qfjk material; provide custom fields, counters, source references, and GM-approved manual actions for missing features.
2. Add independently implemented automation for selected extra mechanics after reviewing the exact data/code and its provenance, with explanatory content separately sourced.
3. Pursue explicit licensed supplemental packs. Cost, availability, redistribution terms, offline rights, and client access remain unknown.

Option 1 is the clearest initial public-content baseline. It must not be marketed as complete automated support for every 2014 supplement. Option 2 may improve the group's experience without a full commercial compendium, but feasibility must be assessed feature by feature. Option 3 is not an assumed dependency.

Decisions now sharp enough to ticket:

- What minimum non-SRD authoring/manual workflow makes the group's actual characters playable, including Echo Knight?
- Which exact supplement versions and character features constitute the first-release acceptance roster?
- When an ability lacks complete automation, does automatic encounter resolution pause for GM input, and how is that communicated?
- What content may a GM session transfer, and what remains a local reference?
- Will qfjk distribute only reviewed packs initially, and what provenance must a contributed pack carry?
- Which software, original-content, and visual-asset licenses should the public repository adopt?

The natural-1 skill-check house rule is a useful original configuration requirement: scope it to the selected check type and define precedence over other abilities. It does not require copying supplemental explanations. These findings clarify the available route; none of the above choices has been made on the user's behalf.
