# OSTF Change Log

## 3.2.0 — Localization, credits, chord voicing snapshots

- Adds optional chord `voicing.pitches` and positional `voicing.velocities`, without changing harmonic intervals or performed tracks.
- Retains 3.1 per-note `velocity` for primary melody, instruments and drums.
- Adds optional song-local contributors, ordered credits, alternative titles, subtitle and description.
- Adds standard localization namespace with an open language-tag dictionary and stable target/field addressing.
- Keeps original 3.1 names and six versioned optional profile payload schemas unchanged.
- Corrects an inherited numeric-token example containing invalid JSON `Infinity`.
- Adds 3.2 specification, migration and sample coverage documentation; user runtime verification remains out of scope.

## Prior release history

# Changelog — OSTF 3.1.0

## 3.1.0 — 2026-10-26 (official specification publication)

- Maintained the original 3.0.0 Core schema, while defining 3.1.0 as a backward-reading, additive version.
- Added optional note-on velocity (integer 1–127) only to performed Core notes; absence remains meaningful.
- Defined track classification, preferred view, explicit GM1/custom sound-slot mapping, and advisory sound-source types without preset/program restoration.
- Added multilingual source-preserving labels and variable drum kits with stable sound-slot membership.
- Clarified that OSTF's scope is composition interchange, not full DAW project interchange. Main melody still uses one designated track; other parts are normal tracks.
- Consolidated complete manuals, normative specifications, exhaustive bilingual parameter references, and full static sample witness mappings into at most 100 GitHub-uploadable files.
- Preserved 76 standalone OSTF 3.1 example documents, three original OSTF 3.0 reference documents, and the bundled version-specific schemas.
- Fixed duplicate amendment section numbers and stale documentation links in the compact publication set.

No runtime validation, MIDI exchange test, application or plugin operation was executed for this publication.

## 3.0.0 — prior published release

Source repository: <https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0>. Its historical manuals, code, full corpus and schemas remain available there. This changelog does not retroactively change the 3.0 format.

## 3.2.0 documentation clarification — independent percussion preview and data exchange

- Specifies that Sound Slots represent separate semantic note targets, not MIDI key/preset identities, even if roles or mappings collide.
- Defines GM1 versus Custom percussion preview fallback without adding a source audio engine or program changes.
- Makes the per-note event, MIDI gate versus sample-tail and lossy export boundaries explicit in bilingual normative documentation.
- Extends existing samples 61 and 64 without increasing the GitHub file count or changing supported JSON fields.
- Preserves 3.0/3.1 schemas, six optional profile versions and the original 3.2.0 format version.

## 3.2.0 documentation clarification — Mixed track routing and preview-only state (2026-10-27)

- Clarifies that pitched note 36 and GM1 drum slot note 36 are different logical targets even when they share one MIDI key number.
- Specifies separate destination routing or explicit information-loss disclosure on MIDI export, plus same-pitch overlapping Note Off ambiguity.
- Confirms no `soundSlot.enabled`: pad mute/solo and tone assignments belong to the Player, with document-scoped identities.
- Clarifies primary-melody reference guidance without rejecting previously valid OSTF 3.0/3.1 songs.
- Updates existing examples 63 and 69 and documents only, retaining the 3.2.0 wire version, schemas, feature boundaries and 100-file publication cap.

## 3.2.0 documentation-only consistency revision — 2026-10-27

- Corrected bundled-reference-library version/API descriptions copied from the 3.1 historical manual. `ostf3.py` does not include old Draft converters or CLI utilities.
- Disclosed the boundary of `choose_display_name`: 3.1 object-local names only; 3.2 common Localization needs document-aware resolution in each consumer.
- Added normative Section 18.10 distinguishing non-performing chord voicing visualizations from adopted track Notes, including song/track views, explicit audition and no duplicated playback.
- Reiterated that custom Sound Slot IDs and free-text roles do not encode portable acoustic drum timbres.
- No new Core/schema field or required Player/DAW/audio operation; 100-file cap retained.

### Official GitHub packaging revision (2026-10-27)

- Recomputed complete 3.2 schema field/enum/sample coverage, replacing historical 3.1-only counts in the primary coverage index.
- Added a `drumKit` reference example to sample 43 (appearance targets).
- Added a contributor of `kind: other` to sample 71, completing enumerated contributor kinds.
- Rebuilt full English/Japanese parameter references from the 3.2 schema with actual source witnesses, required flags and constraints.
- Preserved the 100-file limit, historical schemas, original 3.0 examples, and prior production/data boundaries.
- No application runtime or device tests.
