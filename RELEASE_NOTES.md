# OSTF 3.2.0 — Official Specification Release Notes / リリース判定

**Specification package:** GO — reviewable complete documents, schema, examples, and explicit semantic constraints.

**DAW/Max for Live/MIDI/runtime interoperability:** NOT TESTED, NOT CLAIMED.

## Main features

1. Optional `harmony.chords[].voicing` with ordered concrete pitches and optional aligned 1–127/null Velocity; no implicit performed MIDI notes, additional melody track, or automatic chord playback.
2. Preserve original `tracks[].clips[].notes[].velocity` on the main melody and any arrangement track, without backfilling missing values.
3. Stable song-local `contributors[]`, ordered `song.credits[]`, Unicode subtitle, description and alternate titles.
4. Optional `org.ostf.localization.v1` extension for open BCP47-language-tag translations targeting stable song and entity IDs, including credits and profile-local names; all pre-existing 3.1 localizedName/title fields remain valid.
5. Proper version negotiation with 3.0/3.1, explicit user-approved lossy downgrade only.
6. Preserve six original draft optional-profile versions (`1.0.0-draft.1`) and original 3.0/3.1 JSON schemas.

## Static issues found and corrected / 発見した考慮漏れ

- The **OSTF 3.1 release's `18_opaque_precision.ostf` included non-JSON `Infinity`** from numeric token coercion. The 3.2 example restores valid finite JSON real tokens `1e400`, `1e-400`, and long exact decimals, without binary-float round trips.
- Required target IDs in `localizationTarget` must be enforced by a single paired JSON Schema `if/then/else` (corrected); an orphan `else` silently does nothing.
- Parallel voicing pitch and Velocity arrays need an exact equal-length **semantic** rule; JSON Schema 2020-12 cannot generally assert array-length equality to a sibling property.
- Duplicate pitches are valid: never match Velocity by pitch number or sort pitch arrays independently.
- Core chord voicings are informational and never auto-create notes; otherwise preview + arrangement notes would double play.
- Source names, contributor IDs, credit display orders and scoped profile-local IDs must not be confused with translations or GUI locale settings.
- Old 3.1 text in inherited reference chapters retains historical numbers; the 3.2 amendment and 3.2 schema are authoritative in conflicts.

## Distribution and status

This package is supplied as **at most 100 GitHub files** including a manifest, an English/Japanese normative specification, comprehensive manuals/parameters, and `.ostf` examples. The exact file total and checksums are recorded in `PACKAGE_MANIFEST.json`.

Static syntax/schema/reference checks are preparation-quality checks, not full application runtime test results. No actual playback, GUI interactions, file-import integration, plugin-to-plugin round-trip or independent implementation conformance were verified. No commits/releases were made to GitHub on behalf of the user. Suggested tag `v3.2.0`.

**日本語要約：** コードボイシングの個別Velocity、主旋律との分離、クレジットの安定ID管理、固定言語一覧を持たない多言語拡張を確定しました。3.1の数値サンプル不備も修正しました。動作検証は行わず、仕様公開資料としてのGO判定です。

## Additional 3.2 drum / Sound Slot semantic clarification — static review

**Change scope:** no extra Core fields, no change to `version: "3.2.0"`, no change to former 3.0/3.1 schemas or any optional profile version. English/Japanese normative sections 18.6–18.7 now explicitly distinguish logical Sound Slot identity, MIDI output key mapping, and player-local acoustic preview assignments. Samples 61 and 64 were amended in place: simultaneous Snare + Clap and Ride as independent GM1 slots, and a Custom note 36 that MUST NOT be treated as a GM1 Bass Drum. Sample count stays unchanged.

**Considerations found and resolved in documentation:** same-role/same-key slot collisions; same-tick and overlapping drum notes; GM1 vs Custom with equal key numbers; note gate vs one-shot audio tail; local preview choice vs source data; unresolved MIDI targets; multi-track kit semantics; MIDI export information loss. Explicit external/plugin sound reconstruction, choke/articulation, and built-in sound quality remain out of scope and cannot be claimed as verified.

**Release decision:** GO for publication of the 3.2.0 format specification and static example distribution after static artifact consistency review; NOT a claim that any OSTF Player version actually renders individual GM1 sounds, or that a DAW/plugin import worked. At user request, no schema-validator execution, playback, MIDI round-trip or host operation was conducted.

## Addendum: Mixed routing and local-preview boundaries — 2026-10-27

Reviewed remaining static interoperability edge cases and clarified them without changing 3.2 wire fields or schema acceptance. `pitch:36` and GM1 drum slot key 36 in one Mixed track **must remain separate OSTF targets**; conversion may require destination routing separation. Repeated overlapping same-pitch events cannot always preserve per-voice MIDI1 Note Off behavior. Slot-level mute is player-local; only `track.enabled` and `clip.enabled` are Core saved enable values. Sound preview assignments must be scoped to the loaded document and slot ID, not global note/key/name matches. Discourage assigning drum-only tracks as the primary pitched melody without retrospectively invalidating old files. Updated samples 63 and 69 and English/Japanese documentation.

**Static package release decision: GO** for publishing this amended 3.2.0 specification set; **not a runtime interoperability certificate**. No schema validator, M4L device, DAW, MIDI export, audio-playback or third-party plugin execution was performed. Any external implementation's actual conformance remains unverified.

## Static editorial audit addendum — 2026-10-27 (no runtime tests)

Two remaining documentation/implementation boundaries were clarified:

1. **Bundled API mismatch:** inherited 3.1 manual text wrongly stated the current `VERSION` and old draft-conversion/error classes for the present `ostf3.py`. Corrected both languages to declare its actual supported 3.0.0/3.1.0/3.2.0 roots, and to mark historical tools as absent.
2. **Common 3.2 localization display:** `choose_display_name` only reads `localizedNames`/`localizedTitles`, not `org.ostf.localization.v1`. The bundled reference reader does not promise a ready-made localized UI; document-aware extension resolution is an implementer responsibility.
3. **Voicing view/performance boundary:** Section 18.10 makes precise that visible chord voicing pitch/Velocity arrays are never automatically performed Note events, and identifies explicit audition/materialization as application operations.
4. **Drum timbre ambiguity:** role/custom mapping cannot faithfully identify arbitrary acoustic drum sounds across products. The format preserves identifiers, the host owns preview timbres and unmapped warnings.

The addendum changes **documentation only**, not root version, schema acceptance, optional profile versions or the published 3.0/3.1 source copies. A specification/documentation release is suitable for publication after a static-package integrity check, but no functionality, audio reproduction, DAW compatibility, cross-application round-trip or validator execution has been verified.

## GitHub 100-file publication package — final editorial review

The parameter index is now sourced from 3.2 schema rather than a carried-over 3.1 count. There are 242 property declaration locations; 229 are serialized fields with at least one complete `.ostf` example and 13 are duplicate selector/conditional schema declarations. All named definition sections are indexed. Sample 43 and 71 supply two previously missing finite enum examples without changing the set of filenames. Refer to `docs/COVERAGE.md` for independent file pointers.

The three original 3.0 sample files, historical 3.0/3.1 JSON schemas and 3.1 source example 75 have not been reversioned. No independent interoperability, DAW playback, plugin round-trip or M4L functional verification was performed.
