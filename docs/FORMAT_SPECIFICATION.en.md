# OSTF 3.2.0 — Normative File Format Specification

**The 3.2 amendment in Section 18 has precedence over any conflicting inherited 3.0/3.1 text below.** This is one self-contained publication: the inherited 3.1 material remains normative where unchanged, with the Section 18 changes explicitly overriding it. The authoritative 3.2 structure is [`ostf-3.2.0.schema.json`](../schema/ostf-3.2.0.schema.json). The original [3.1 release](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.1) remains separately archived. Reference validation/DAW execution was not performed.

## Inherited 3.0 + 3.1 chapters (historical version references retained)

# OSTF 3.1.0 — Format Specification

**3.1.0 amendment precedence:** This document retains all 3.0 Core musical semantics. The new normative Section 17 overrides the earlier Sections 2, 6 and 7 only where explicitly stated. The 3.0.0 original is archived as [published 3.0.0 repository](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0). A reference to an original 3.0 release activity is historical, not a claim about 3.1 execution.

3.1 amendment prepared on 26 October 2026. Core wire version: `3.1.0`.
Status: initial stable Core specification, with six provisional optional profiles
at their existing exact version `1.0.0-draft.1`.

**This is the stable Core 3.1.0 specification; the optional profiles remain provisional.** [LICENSE.md](../LICENSE.md) defines the release terms and [release readiness](../RELEASE_NOTES.md) records the distribution scope. No runtime verification was performed while preparing this package. Earlier Draft results are not passing evidence for 3.0.0.

This is the English implementer edition of the
[Japanese specification](FORMAT_SPECIFICATION.ja.md). Its structural definitions
are provided by the [3.1.0 JSON Schema](../schema/ostf-3.1.0.schema.json). The
[earlier Draft 1 schema (in 3.1 release)](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.1) and
[earlier Draft 2 schema (in 3.1 release)](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.1) are retained unchanged.
Read the prose and schema together: JSON Schema alone does not express every
wire-format, ordering, identity, timing, or reference requirement.

OSTF 3.0 exchanges song structure, harmony, lyrics, and the performance currently
adopted by the author. A performance note contains a pitch or percussion target,
an onset position, and a gate duration. **3.0 Core did not define velocity; 3.1 adds optional integer `note.velocity` to performed Core notes only (Section 17.12).**

The requirements below are normative for Core 3.0.0 and for implementations
claiming the exact provisional profile or operation support described here.
Existing Core fields, limits, and performance meanings are the initial stable
baseline. A profile's `current` status describes correspondence with a saved
document, not the maturity of its specification. Later profile versions must not
be silently interpreted as the same version. Conformance concerns structure,
time, references, and supported optional profiles; it does not certify musical
quality, identical timbre, or operation in an existing product.

3.0.0 retains the musical meanings of Draft 2 Revision 2 and adds a stable root
and schema. Reusable concrete phrases, performance-clip choices, whole-template
replacement with refresh of all linked instances, detachment, and protection
comparisons remain explicit operations. Reading performs no automatic generation
or synchronization. The reference implementation retains both draft roots, added explicit conversion among the three original roots, and kept the 2.3 migration target at 3.0.0. The 3.1.0 amendment adds a fourth root for new readers.

The [four-part example 01](../samples/01_four_part_sketch.ostf), [linked-phrase example 31](../samples/31_linked_phrase_before.ostf), and [clip-choice example 25](../samples/25_choice_shared_muted.ostf) are among 76 authored 3.1.0 examples (60 inherited and 16 new). Three verbatim 3.0 inputs are preserved separately in `samples/legacy_3_0` and `samples/legacy_2_3`. The [sample index](../samples/INDEX.md), [catalogue](../samples/INDEX.md), and [coverage inventory](COVERAGE.md) identify their scope and provenance. Authored examples are not executed conversion outputs or passing tests.

**MUST** and **MUST NOT** indicate requirements. **SHOULD** indicates a recommended choice from which an implementation may depart for a justified reason. A required field must be present; an optional field may be absent. Optional does not imply that `null` is accepted. The schema defines permitted value types.

First implementers can start with the bilingual [quickstart](COMPLETE_MANUAL.en.md), [complete manual](COMPLETE_MANUAL.en.md), and [parameter reference](PARAMETER_REFERENCE.en.md). [Melody example 04](../samples/04_melody_only.ostf) and [harmony example 03](../samples/03_harmony_only.ostf) use Core without optional profiles. These informative guides do not add fields or change normative requirements.

## 1. Scope and three layers

| Layer | Content | Reader behavior |
|---|---|---|
| Core | Song information, timeline, harmony, adopted notes, lyrics, percussion targets, and resource references | Interpret the current song content |
| Standard optional profiles | Relative-note relationships, generation inputs, protected notes, appearance, reusable phrases, and clip choices | Interpret additional meaning when the profile and version are supported |
| Vendor extensions | Algorithm-specific settings, presets, models, and other proprietary information | Preserve unsupported data without guessing its meaning |

Core MUST contain the concrete notes of the finite arrangement currently adopted
by the author. When relative phrases, repetition, transposition, or probabilistic
choices affect that performance, their adopted results MUST be reflected in Core.
A reader MUST NOT run generation automatically on load, reroll a saved random
choice, or play both a candidate representation and its adopted Core notes.

Incomplete documents are valid. A document may contain only harmony, only a
melody, or only lyrics, while still supplying the required empty containers and
timeline maps. Completion of all musical layers is not required. A document with
no notes must not be represented as containing a completed note performance.

Core 3.1 includes only optional per-note velocity (Section 17.12) and excludes MIDI CC, pitch bend, aftertouch, instrument and effect state, audio
track playback, indefinitely running generation, and complete score engraving.
Future features may be considered as optional profiles that preserve Core meaning.

## 2. File and top-level structure

### 2.1 Strict JSON input

The file extension is `.ostf`. A file MUST contain one UTF-8 JSON object. A reader
MUST reject a byte-order mark, invalid UTF-8, duplicate keys within an object,
unpaired Unicode surrogates, `NaN`, `Infinity`, mathematically non-finite numbers,
and additional non-whitespace content after the root object.

Fields specified as Core integers MUST use JSON integer tokens. `1` is valid;
`1.0`, `1e0`, and `true` are not valid representations of an integer field.
Implementations MUST check this input rule in addition to ordinary JSON Schema
integer validation. Supported profile fields defined as integers likewise retain
their declared integer requirements.

### 2.2 Required root members

| Member | Type and meaning |
|---|---|
| `format` | Exact string `OSTF` |
| `version` | Exact string `3.1.0` (this specification; legacy documents retain their declared version) |
| `ppq` | Integer ticks per quarter note, from 1 through 960000 |
| `song` | Song information object |
| `timeline` | Object containing `sections`, `tempoChanges`, and `meterChanges` |
| `harmony` | Object containing `chords` and `scales` |
| `tracks` | Array of tracks |
| `soundSlots` | Array of percussion targets |
| `lyrics` | Object containing `documents`, `phrases`, `units`, and `alignments` |
| `resources` | Array of resource references, such as generation source material |
| `extensions` | Object of namespaced extension envelopes |

The reference reader dispatches on the exact root version, supporting
`3.0.0-draft.1`, `3.0.0-draft.2`, `3.0.0`, and `3.1.0` with their respective schemas and
profile sets. Other root versions are unsupported and are rejected. Reading MUST
NOT rewrite the version. Draft 1 interprets only relative, generation, appearance,
and legacy-import. Draft 2 and 3.0.0 interpret those four plus phrase-library and
clip-choices, with identical profile meanings. The latter two remain opaque in a
Draft 1 document even when their shape is recognizable. All six profile versions
remain provisional `1.0.0-draft.1`, independently of stable Core status. Schema
retrieval and the schema-check option default to 3.1.0; all older schemas remain
explicitly selectable.

Core objects are closed: an implementation MUST NOT accept undeclared members as
Core fields. Additional information belongs in `extensions`. For example, a
misspelled `duration` is an error. For root `3.1.0`, `velocity` is declared only on Core performed notes; elsewhere it remains an undeclared member. For root `3.0.0` any Core `velocity` remains invalid.

Arrays may generally be empty. `tempoChanges` and `meterChanges` are exceptions:
each MUST contain an entry at tick 0. Object key order has no meaning. Array order
MUST be preserved, subject to the specific temporal ordering requirements below.

### 2.3 Numeric preservation

A finite decimal JSON value MUST NOT be rejected as malformed solely because its
conversion to binary64 overflows or underflows. For example, opaque extension
values `1e400` and `1e-400` are finite decimal values. Their numeric value and their
real-token category must be preserved. Core integers remain subject to the
field-specific bounds and the common limit in Section 3.

If an input exceeds a reference implementation's numeric representation limits,
that condition must be reported as an implementation support limit, not as a JSON
grammar violation.

A reader/writer claiming lossless preservation of unknown extensions MUST also
preserve the precision of numbers in JSON values it does not understand. It MUST
NOT silently change an integer token into a real token or vice versa. Parsing all
numbers into ordinary floating-point values and printing them again is not, by
itself, a lossless strategy. Preserve original numeric tokens or sufficient
precision and verify the result of writing them back. Byte-for-byte preservation
of JSON whitespace and object key order is a separate requirement.

## 3. Time, identity, and references

### 3.1 One timebase

All Core objects and standard profiles in this specification use the document's `ppq`.
The recommended value is 960, but readers MUST honor the declared value. There is
no per-track PPQ and no implicit multiplier.

The common absolute integer limit is:

```text
MAX = 9007199254740991
```

Time positions, durations, and computed end positions MUST remain within their
specified bounds. Unsigned positions are at least 0. Durations are at least 1.
Signed note-local positions range from `-MAX` through `MAX`; their resulting
absolute positions are checked separately.

A meter change does not alter the meaning of a tick. An implementation converting
to another PPQ MUST disclose any timing loss caused by rounding. At PPQ 960, a
quarter note cannot be divided into seven equal integer tick durations; a suitable
PPQ such as 6720 can represent that division exactly.

### 3.2 Stable IDs

A Core `id` is an ASCII string of 1 to 128 characters. It begins with a letter or
digit and then contains only letters, digits, `.`, `_`, `:`, or `-`:

```text
^[A-Za-z0-9][A-Za-z0-9._:-]{0,127}$
```

IDs MUST be unique across **all Core object kinds in one document**. Display names,
array indices, and track positions are not identities. Moving an object SHOULD
retain its ID. An independent copy MUST have a distinct ID. IDs local to an
extension use the uniqueness scope defined by that profile; they do not become
Core IDs merely because they have the same spelling.

### 3.3 Typed references

A `Ref` has exactly `type` and `id`. Its `type` is one of:

```text
section, track, clip, note, chord, scale, soundSlot,
lyricDocument, lyricPhrase, lyricUnit, lyricAlignment, resource
```

A Core reference MUST resolve to the correct object kind. Finding the same string
under a different kind does not satisfy the reference. An implementation MUST NOT
recursively scan opaque extension `data` and register coincidental `id` members as
Core objects.

### 3.4 Ranges

Where a `Range` is used, it has exactly `start` and `duration` on the global song
timeline. `start` is a nonnegative integer, `duration` is a positive integer, and
`start + duration` MUST be at most `MAX`. Checking the two operands separately is
not sufficient.

## 4. Song information and timeline

### 4.1 `song`

`title` is a required string. It may be empty for an unnamed song.
`primaryMelodyTrackId` is optional and, when present, MUST identify an existing
track. It designates the primary melody; it does not produce an additional
performance event.

### 4.2 `timeline`

| Array | Required members of each element | Optional members |
|---|---|---|
| `sections` | `id`, nonempty `name`, `start`, `duration` | `role` |
| `tempoChanges` | `at`, `bpm` | None |
| `meterChanges` | `at`, `numerator`, `denominator` | None |

Sections MUST be ordered by start and MUST NOT overlap. Gaps and an empty section
array are allowed. `role` is an open string rather than a fixed enumeration of
song-section categories. Sections need not cover every note and MUST NOT truncate
notes at their boundaries.

`tempoChanges` MUST begin with `at: 0`, and subsequent `at` values MUST strictly
increase. `bpm` is a finite number greater than 0 and at most 1000. It always means
**quarter notes per minute**, independent of meter. Each value remains constant
until the next tempo change. Continuous tempo curves are not defined in 3.0.0.

`meterChanges` follows the same tick-0 and strictly increasing position rules.
`numerator` is an integer from 1 through 64. `denominator` is one of `1`, `2`, `4`,
`8`, `16`, `32`, or `64`. A reader MUST NOT move a declared change to an inferred
bar boundary.

For a constant-tempo segment, elapsed seconds are:

```text
seconds = ticks * 60 / ppq / bpm
```

A note crossing tempo changes must be converted to time segment by segment.
Displayed bar numbers, including whether an anacrusis is called bar 0, are
presentation conventions separate from the tick coordinate system.

## 5. Harmony

`harmony` requires the arrays `chords` and `scales`. Both may be empty. Each array
MUST be in nondecreasing `start` order; overlapping harmonic contexts are allowed.
If several contexts are available, a relative-note assignment selects its context
by ID, not by incidental array order.

### 5.1 Spelled pitch classes

A spelled pitch class contains `step` and `alter`. `step` is one of `A` through
`G`; `alter` is an integer from -2 through 2. Natural pitch classes are `C=0`,
`D=2`, `E=4`, `F=5`, `G=7`, `A=9`, and `B=11`. Add `alter` and normalize modulo 12.

The spelling MUST be retained: `{ "step": "C", "alter": 1 }` and
`{ "step": "D", "alter": -1 }` have the same pitch class but different spellings.
This object contains no octave.

### 5.2 Chords

A chord requires `id`, `start`, `duration`, and a nonempty `label`. `root`,
`intervals`, and `bass` are optional. However, `root` and `intervals` MUST either
both be present or both be absent.

`root` and `bass` are spelled pitch classes. `intervals` is a nonempty array of
integer semitone distances from the root, from 0 through 35. It MUST contain 0,
MUST be strictly increasing, and MUST contain no duplicates. An extension such as
14 MUST NOT be automatically reduced to 2. These intervals declare a harmonic
interpretation, not a performed voicing.

A label-only chord is valid. A reader's private parsing of that label is not
portable structured information shared with other readers. Specifying `bass` does
not generate a bass track.

### 5.3 Scales

A scale requires `id`, `start`, `duration`, `tonic`, and `intervals`. `label` is
optional. `tonic` is a spelled pitch class. `intervals` is a nonempty, strictly
increasing array of distinct integers from 0 through 11 and MUST contain 0.

Chords and scales do not restrict which pitches may occur in Core notes. Passing
tones, dissonances, and intermediate editing states are not format errors. A
reader MUST NOT generate performance events from `harmony` alone.

## 6. Tracks, clips, and notes

### 6.1 Tracks

A track requires `id`, `name`, and `clips`. Optional members are `role`,
`instrument`, and `enabled`. `instrument`, when present, requires a string `name`
and may contain a string `family`. `enabled` defaults semantically to `true` when
absent; the schema's default annotation does not itself insert a field.

Melody, bass, accompaniment, and percussion use the same track structure. `role`
describes a musical function, and `instrument` describes an instrument. Neither
executes an instrument plug-in. A playing method such as arpeggiation is not the
same concept as an instrument name. Track array order is the display order and
MUST be retained.

### 6.2 Clips

A clip requires `id`, `start`, `duration`, and `notes`. `name` and `enabled` are
optional. `start` is a nonnegative global tick position. `duration` is the length
of a placement/editing reference span. `enabled` defaults semantically to `true`.

Clips in a track MUST be in nondecreasing `start` order. Equal starts and overlap
are allowed. A clip's reference span is **not a playback truncation mask**.

### 6.3 Notes

A note requires `id`, `start`, `duration`, and exactly one of `pitch` or
`soundSlotId`. Specifying both targets or neither target is invalid.

| Member | Rule |
|---|---|
| `pitch` | Integer semitone number from 0 through 127; 60 is C4 and 69 is A4; reference tuning uses A4 = 440 Hz |
| `soundSlotId` | Reference to an existing percussion sound slot |
| `start` | Signed integer tick offset relative to the clip's reference start |
| `duration` | Positive integer ticks for which the note is held, up to its gate end |
| `id` | Document-wide unique note identity |

Notes in a clip MUST be in nondecreasing local `start` order. Equal-start notes
retain their array order. Compute absolute positions as follows:

```text
onset   = clip.start + note.start
gateEnd = onset + note.duration
```

`onset` MUST be nonnegative. Both `onset` and `gateEnd` MUST be at most `MAX`.
Implementations MUST check the sums, not just the individual field values.

Negative local starts, starts after the clip's reference span, gates extending
beyond that span, and overlapping notes of the same pitch are valid. Each note
remains a separate identity. Readers MUST NOT merge or shorten these notes on
load. Negative absolute song positions are not allowed; an anacrusis can instead
be represented by placing the first section later on the timeline.

`duration` is not the time until a synthesizer's release or reverberation becomes
silent. It is also distinct from notated duration. Notated values, ties, voices,
and detailed articulations are subjects for future optional profiles.

### 6.4 Enabled performance and deterministic events

A note produces events only when both its track and its clip are enabled.
Disabled tracks and clips MUST still be preserved and structurally validated.

Produce a `noteOn` at `onset` and a `noteOff` at `gateEnd`. Both events carry the
same `noteId`, `trackId`, and `clipId`. Sort events first by tick. At a shared tick,
all `noteOff` events precede all `noteOn` events. Events with the same tick and
event kind are ordered lexicographically by `noteId`.

The performance end is the maximum `gateEnd` of enabled notes, or 0 if there are
none. Section and clip ends alone do not determine when note playback ends.
Transport stop and seek behavior belongs to the host.

An exporter with solo states or per-note mute MUST materialize the adopted
performance through Core `enabled` states or the adopted note arrays. If an output
device cannot independently render overlapping notes of the same pitch, that is
an output conversion constraint; it does not authorize rewriting the stored notes.

## 7. Percussion targets and resource references

### 7.1 `soundSlots`

A sound slot requires `id` and `name`. `role` and `midiNote` are optional.
`midiNote` is an integer from 0 through 127 and is only a mapping hint for a
particular output. It is not the slot's identity and does not declare General MIDI
conformance.

Two slots may both have role `kick` or the same `midiNote` and still remain
independent. They MUST NOT be merged for that reason. A reader without a suitable
sound mapping preserves and displays the events and lets the host assign sounds.
Timbre equivalence is not guaranteed. A pitched 808 bass is represented with
pitched notes.

### 7.2 `resources`

A resource requires `id`, `kind`, and `uri`. `kind` is `audio`, `midi`, `text`, or
`other`. `uri` is a nonempty string. Optional members are `name` and `sha256`, where
`sha256` is exactly 64 lowercase hexadecimal characters.

A resource is a reference to material, such as generation input; it is not an
audio-track playback instruction. Loading a document MUST NOT fetch or execute a
referenced resource. An unavailable resource does not by itself invalidate the
stored note structure. Resolving a relative URI against a base location and
checking the actual resource against its hash belong to an explicit resource
operation.

## 8. Lyrics

`lyrics` requires four arrays: `documents`, `phrases`, `units`, and `alignments`.
Each may be empty.

| Element | Required members | Optional members |
|---|---|---|
| Lyric document | `id` | `title`, `language` |
| Lyric phrase | `id`, `documentId`, `text` | `pronunciation`, `start`, `duration` |
| Lyric unit | `id`, `phraseId`, `text` | `pronunciation`, `start`, `duration` |
| Lyric alignment | `id`, `unitIds`, `noteIds` | None |

`documentId` and `phraseId` MUST resolve to the correct kinds. `text` is display
text; `pronunciation` is a string annotation for pronunciation. This definition
does not standardize language-specific segmentation, phoneme alphabets, or a
singing-synthesis engine. `language` is an auxiliary string.

A phrase's `text` is its displayed text. Units are annotations for segmentation
and assignment. Concatenating unit texts need not exactly reproduce the phrase
text. A pronunciation spelling that differs from display spelling is preserved.

If a phrase or unit has timing, it MUST specify both `start` and `duration` on the
global song timeline. Start is nonnegative, duration is positive, and the computed
end MUST not exceed `MAX`.

Both `unitIds` and `noteIds` in an alignment MUST be nonempty. Each list MUST be
duplicate-free and ordered, and every ID MUST resolve to its declared kind. An
alignment can associate multiple units with multiple notes. It can represent one
syllable extended across several notes or several units associated with one note
without forcing either case into a one-to-one model. An alignment does not
generate notes.

## 9. Common extension contract

### 9.1 Namespaces and envelopes

Extension keys use reverse-domain-style namespaces such as
`org.ostf.relative-phrase.v1`. The grammar is:

```text
^[a-z][a-z0-9-]*(?:\.[a-z][a-z0-9-]*){2,}$
```

There must be at least three dot-separated components, each beginning with a
lowercase ASCII letter. A namespace is an identity convention, not authentication
of the author.

Every extension has exactly four required outer members:

| Member | Meaning |
|---|---|
| `version` | Nonempty version string |
| `status` | `current`, `stale`, or `unverified` |
| `dependencies` | Dependency declaration described below |
| `data` | Arbitrary JSON value, constrained further only for a supported profile version |

The envelope is closed. Vendor-specific outer members MUST NOT be added.

### 9.2 Status and dependencies

| Status | Meaning |
|---|---|
| `current` | The producer declares that the recorded relationship matches the current document |
| `stale` | A relevant change is known to have broken the previous relationship |
| `unverified` | The relationship has not been checked, or the current editor cannot check it |

Status does not switch the Core performance. A `current` declaration is not proof
that a validator has reproduced an unknown generation engine.

`dependencies` has exactly one of these forms:

```json
{ "scope": "document" }
```

```json
{ "scope": "none" }
```

```json
{ "scope": "objects", "refs": [{ "type": "note", "id": "note-example" }] }
```

For `objects`, `refs` MUST be nonempty and duplicate-free. `none` declares no
dependency on current Core objects. A `current` dependency reference MUST resolve
to the correct kind. Missing counterparts for `stale` or `unverified` extensions
are preserved with warnings. This relaxation MUST NOT be applied to broken Core
references.

For a `current` known profile that contains direct Core references, use
`scope: document` or include **every direct Core reference in its data** in
`objects.refs`. This includes relative-note targets and contexts, generation
inputs and outputs, protected notes, and appearance targets. Extension-local IDs
are not Core dependencies. If there are no Core dependencies, `scope: none` is
permitted unless the individual profile requires document scope.

Dependency meaning includes relationships inherited through the Core hierarchy,
not just the referenced object's direct JSON members. Moving a parent clip changes
its note's absolute onset; disabling a parent changes the note's effective enabled
state. An implementation unable to enumerate the necessary dependencies must use
document scope.

After a relevant edit, an editor MUST either update the relationships it
understands and the adopted notes, or change the status to `stale` or `unverified`.
It MUST preserve unknown `data` without speculative modifications and use the
outer status to express uncertainty. Merely recording a new status is not another
musical edit that recursively invalidates extension statuses again.

### 9.3 Exact-version dispatch and fallback

Unknown namespaces and unknown versions of known namespaces are preservable by a
Core reader. The common envelope rules still apply. A reader MUST NOT validate a
future version's `data` against an older known data schema and reject it for that
reason. Profile interpretation is selected by the namespace **and exact supported
version**, not by namespace alone.

Conversely, malformed data for an exact known version is an error even if the
extension is `stale` or `unverified`. Those statuses relax correspondence and
reference checks; they do not relax a known data structure.

## 10. Standard optional profiles

The following five profiles and the legacy source archive in Section 11 are
bundled at provisional version `1.0.0-draft.1`. Relative phrases, generation
context, appearance, and legacy-import retain their Draft 1 meanings; phrase
library and clip choices retain their Draft 2 meanings. Stable Core does not
change these profile versions or make them stable. An implementation claiming
support follows the exact version's rules. A reader that understands none of
them can still read the adopted performance from Core. Their data objects and
nested records are closed according to the schema.

### 10.1 Relative phrases

Namespace: `org.ostf.relative-phrase.v1`.

`data` requires an `assignments` array. Each assignment requires:

| Member | Rule |
|---|---|
| `noteId` | A pitched Core note; each target note appears at most once in the array |
| `basis` | `chordTone` or `scaleDegree` |
| `contextId` | Chord or scale ID, according to `basis` |
| `anchorPitch` | Integer 0–127: an actual octave-specific root or tonic pitch |
| `index` | One-based position in the context's ordered `intervals` array |
| `chromaticOffset` | Integer semitone adjustment from -24 through 24 |

The structural maximum for `index` is 36 for `chordTone` and 12 for
`scaleDegree`. For a `current` assignment, it MUST also be within the actual
context array. This index is a position in that array, not a chord-symbol label
such as “5th.”

A `current` chord-based assignment requires a chord with `root` and `intervals`.
The pitch class of `anchorPitch` MUST match the context's root or tonic. The
following result MUST be within 0–127 and MUST equal the target Core note's pitch:

```text
pitch = anchorPitch + intervals[index - 1] + chromaticOffset
```

An explicitly referenced pickup note is not invalid merely because its onset is
outside the context's time span.

For `stale` or `unverified` data, reference and pitch mismatches are retained as
warnings. A reader MUST NOT overwrite Core pitches from outdated relative
information on load.

This profile version does not imply an optimal register after reharmonization,
voice-leading rules, selection by input-note rank, or complex Figure
interpretation. Those are separate profiles or generator behavior.

### 10.2 Generation context

Namespace: `org.ostf.generation-context.v1`.

`data` requires a `processes` array. Each process requires `id`, `generator`,
`inputs`, `outputs`, and `protectedNoteIds`. `targetRange` and `vendorExtension`
are optional. Process IDs are unique within this extension.

`generator` contains a nonempty `id` and a nonempty `version`. Each input contains
a nonempty string `role`, a typed `ref`, and optionally a `readRange`. Inputs may
be empty or contain multiple distinct sources; they must not be collapsed into a
single presumed source. `outputs` is a nonempty array of typed references.
`protectedNoteIds` is a duplicate-free array of Core note IDs.

For `current` data, all inputs, outputs, and protected notes MUST resolve to the
correct kinds. Unresolved references in `stale` or `unverified` data are retained
with warnings.

`readRange` and `targetRange` use the global `Range` definition. `readRange`
describes the context read. `targetRange` describes the range requested for
modification. Neither is automatically the complete output span: pickups and
extended gates may lie outside it.

For `current` data, `vendorExtension`, when present, MUST reference another
existing extension in the same document and requires
`dependencies.scope: document`. A reference to this generation-context extension
itself is an error regardless of status. For `stale` or `unverified` data, a
missing referenced extension or non-document dependency scope is retained with a
warning.

An application claiming support for protected notes and executing a generation
operation MUST preserve each protected note's ID, pitch or sound target, global
onset, gate, and effective enabled state during that operation. Moving its parent
clip without changing local note values, disabling its parent, or deleting the
note also changes protected content. A validator can check references in a saved
document; without before-and-after data it cannot establish that an earlier
generation operation respected protection.

Settings such as `seed`, `complexity`, or `humanize` do not have universal,
engine-independent behavior. Store proprietary settings in vendor extensions.
This profile records generation context; it is not an instruction to execute
generation on load.

### 10.3 Appearance and section roles

Namespace: `org.ostf.appearance.v1`.

`data` requires `roleDefinitions`, `sectionRoleAssignments`, and `appearance`.
Each role definition has an extension-local unique `id`, a nonempty `name`, and
optionally a string `baseRole`. `sectionRoleAssignments` maps Core section IDs to
custom-role IDs.

`appearance` requires all three members below, even when they are empty:

| Member | Meaning |
|---|---|
| `roleColors` | Map from role strings to colors |
| `customRoleColors` | Map from custom-role IDs to colors |
| `objectColors` | Array of objects containing a typed `target` and `color` |

Colors have the exact `#RRGGBB` form; hexadecimal letters may be upper- or
lowercase. Multiple `objectColors` entries for the same typed target are invalid.
For `current` data, target references and custom roles MUST resolve.

Resolve color using the first available choice in the applicable row:

| Object kind | Priority, highest to lowest |
|---|---|
| Section | Its own object color; assigned custom role's custom color; role color for that custom role's `baseRole`; role color for Core `section.role`; host default |
| Track or sound slot | Its own object color; role color for its Core `role`; host default |
| Clip | Its own object color; its parent track's resolved color |
| Note | Its own object color; its parent clip's resolved color |
| Any other Core target | Its own object color; host default |

A reader MUST NOT infer colors from a section merely because it overlaps an
object in time. Resolvable color references in `stale` or `unverified` data may be
displayed, but unresolved references MUST NOT be redirected to different objects.
Appearance metadata does not create or modify performance events.

This namespace is distinct from OSTF 2.3's
`org.ostf.section-role-appearance.v1`. An implementation MUST NOT silently redefine
the old namespace's target kinds or meaning.

### 10.4 Phrase library

Namespace: `org.ostf.phrase-library.v1`. Profile version: `1.0.0-draft.1`.
Interpret this profile in Draft 2 and 3.0.0 documents.

`data` has exactly the required arrays `templates` and `instances`; both may be
empty. Every ID uses the grammar in Section 3. The records are closed objects.

A template requires `id`, `duration`, and `notes`; `name` is an optional display
string. Template IDs are unique within `templates`. `duration` is positive integer
ticks in the document timebase and defines the **repeat period**.

Template notes use the Core Note field shape: `id`, `start`, `duration`, and
exactly one of `pitch` or `soundSlotId`. However, their IDs are local to that
template, unique within it, and MUST NOT be registered as Core note IDs. Notes
are ordered by nondecreasing `start`. Signed starts, pickups, starts after the
period, and gates beyond the period are allowed. Template notes do not play by
themselves. A template's `soundSlotId` is a direct Core reference.

An instance requires exactly these members:

| Member | Rule |
|---|---|
| `id` | Local ID unique within `instances` |
| `templateId` | Local template ID in this profile |
| `clipId` | Core clip ID |
| `repeatCount` | Integer from 1 through `MAX` |
| `transpose` | Integer semitone offset from -127 through 127 |
| `noteMap` | Array of occurrence mappings |

A mapping contains exactly `templateNoteId`, `repeatIndex`, and `noteId`.
`templateNoteId` identifies a note in the referenced template; `repeatIndex` is an
integer from 0 through `MAX`; `noteId` identifies a Core note. Mapping array order
has no semantic effect. Template IDs and instance IDs have separate local scopes.

One instance owns one Core clip. Two instances MUST NOT name the same `clipId`.
One template may have many instances. Duplicate template IDs, instance IDs, note
IDs within one template, occurrence keys `(repeatIndex, templateNoteId)` within
an instance, mapped Core note IDs anywhere in this profile, and clip ownership
conflicts are intrinsic errors in every status.

For `current` data, each template reference MUST resolve and every `repeatIndex`
MUST be less than `repeatCount`. The map must cover exactly the product of the
template's notes and repetitions, with one occurrence for every pair. Mapped
Core notes MUST belong to the instance's clip; the map MUST cover all of that
clip's notes and no others. Unmapped manual additions to the same clip are not
consistent with a `current` exact realization.

The correspondence equations are:

```text
clip.duration = template.duration * repeatCount
note.start = repeatIndex * template.duration + templateNote.start
note.duration = templateNote.duration
pitched note.pitch = templateNote.pitch + transpose
unpitched note.soundSlotId = templateNote.soundSlotId
```

Transposition does not affect sound slots. Calculated pitch MUST be in 0–127.
`clip.start` supplies global placement, and Core equations determine absolute
onset and gate end. Computed clip duration, local starts, and global endpoints
must fit Core bounds. No first-repetition pickup or final tail is omitted or
truncated. Each occurrence retains a separate Core note identity.

Validation MUST NOT generate all expected occurrences. Integer cardinality,
validity of each supplied mapping key, and uniqueness establish complete coverage
without expanding the Cartesian product. An explicit materialization operation
may have a separate implementation resource limit; that is not a wire-format
repeat limit.

Missing references or templates, incomplete or excessive coverage, wrong mapped
parents, and target, time, duration, computed-pitch, or range mismatches are errors
for `current` and warnings for `stale` or `unverified`. Known structural errors,
intrinsic identity ambiguity, and invalid Core content remain errors. One outer
status applies to the entire library and all its instances.

Direct dependency coverage includes every referenced sound slot, clip, and mapped
Core note, not local IDs. Document scope is recommended. A library with no Core
dependency may use `none`. Loading MUST NOT materialize or refresh instances.
Nested templates, time scaling, probability, harmony fitting, and overrides are
outside this profile version. Relative and generation relationships remain
separate profiles; when both are current, both must agree with the same Core.

### 10.5 Clip choices

Namespace: `org.ostf.clip-choices.v1`. Profile version: `1.0.0-draft.1`.
Interpret this profile in Draft 2 and 3.0.0 documents.

`data` has exactly a required `groups` array, which may be empty. A group requires
`id`, `candidates`, and `selectedCandidateId`; `name` is optional. Group IDs are
unique across groups, and `candidates` is nonempty. A candidate requires `id` and
`clipIds`; `name` is optional. Candidate IDs are local and unique within their
group. Names are display strings; local IDs do not become Core IDs.

`clipIds` is an ordered, duplicate-free array of Core clip IDs. An empty array
represents a silent candidate. `selectedCandidateId` is a candidate ID in that
group or `null`; null explicitly selects no candidate.

The managed set of a group is the union of all its candidates' clip IDs. A clip
may occur in several candidates of one group, but MUST NOT be managed by two
different groups. Duplicate group IDs, duplicate candidate IDs within a group,
duplicate IDs within one candidate's clip list, and conflicting ownership are
errors in every status.

For `current` data, the selected candidate, when nonnull, and every candidate's
Core clip references MUST resolve. Each managed clip's own `enabled` value,
defaulting to true when absent, MUST equal its membership in the selected
candidate. With a null selection, all managed clips MUST be disabled.

Compare the **clip's own enabled flag**, not the effective track-and-clip enabled
state. `track.enabled` is independent and MUST NOT be changed by candidate
activation. A selected clip can therefore remain silent because its parent track
is muted. Unmanaged clips are unaffected. Distinct clips may overlap in time as
Core allows; group ownership is not inferred from time ranges or song sections.

A missing local selection or Core clip reference and a mismatch with clip enabled
states are current errors and stale/unverified warnings. All candidate clip
references, including unselected clips, count toward dependency coverage. A reader
MUST NOT apply a selection or repair Core enabled flags on load.

This profile selects combinations of **already materialized performance clips**.
It does not select harmony, sections, lyric documents, routing, or transport
scenes. It does not add an enabled state to Core harmony or claim a whole-song
scene model.

### 10.6 Explicit operations and snapshot protection

The [operation guide](COMPLETE_MANUAL.en.md), [phrase and choice profile guide](FORMAT_SPECIFICATION.en.md), and
[reference implementation guide](COMPLETE_MANUAL.en.md) describe the
operation APIs. Operations return copies, do not mutate caller inputs, and
validate before and after. A failed operation returns no partially edited
document. Replacement templates and supplied ID lists are also caller-owned and
MUST NOT be mutated. Unsupported root, namespace, and profile versions are not interpreted
speculatively. Unknown payload values and numeric token distinctions survive
copying and strict serialization.

`convert_version(document, target_version=VERSION)` defaults to
`VERSION = "3.0.0"` and explicitly converts among all three exact supported roots.
It preserves Core content and events, changing the root version and necessary
extension statuses only. A current extension unknown to either side is marked
unverified when its dependency scope is not `none`. Profiles understood on both
sides keep their meanings and versions; stale or unverified claims are not
promoted automatically. Conversion can fail when formerly opaque data becomes
known and violates its structural schema. Reading does not perform this operation.

The compatibility API `convert_draft_version(document, target_version)` retains
its required target argument and restricts both source and target to Draft 1 or
Draft 2. CLI `convert SOURCE OUTPUT --to-version VERSION` supports all three
roots and requires the target option. Legacy `upgrade` targets Draft 2 and legacy
`downgrade` targets Draft 1, accepting only draft inputs. Use `convert` for any
conversion involving stable 3.0.0.

`materialize_phrase` requires a Draft 2 or 3.0.0 document and an exact known,
current phrase profile. It adds a new clip and instance and never overwrites an existing clip,
instance, or Core ID. It rejects a template-note count times repetition count
above `max_notes` before expansion; the reference default of 100000 is an explicit
implementation resource limit. The reference operation derives each note ID as
`note.` followed by the SHA-256 of UTF-8 compact JSON
`[instance_id, repeatIndex, templateNoteId]`; any existing ID collision is rejected.
Notes are sorted by `(start, repeatIndex, template array index)`, and mappings by
`(repeatIndex, template array index)`. Empty templates can create empty clips.
The parent track's enabled state is unchanged. Affected other extensions are
invalidated; the entire updated phrase profile is certified current only after
validation. This creation operation does not rebuild existing instances. Revision 1
defines a separate explicit replacement operation below; automatic propagation on
load remains prohibited.

`activate_candidate` requires Draft 2 or 3.0.0 and the exact supported clip-choice profile.
It updates the selected ID and all managed clip enabled flags in one operation.
It may accept a structurally valid current, stale, or unverified
choice profile, but required managed references must resolve. It does not repair
other groups. If all groups cannot validate as current afterward, the entire
operation fails. Repeating an already-current identical selection MUST NOT
invalidate unrelated extensions. An optional fixed `protected_note_ids` list is
checked afterward; a mismatch does not yield a partially applied result.

`check_protected_notes` compares a fixed, duplicate-free supplied note ID list
between valid before and after documents. It includes inactive notes and compares
identity presence, pitch or sound-slot identity, global onset, duration/gate end,
and effective enabled state. Local coordinate representation and parent identity
need not be identical. Different PPQ yields `PROTECTION_PPQ_MISMATCH` and an invalid
comparison. An empty supplied list is visibly reported as `checkedNoteIds: []`.

`check_generation_protection` fixes the protected ID list from one process in
before's exact supported, current generation profile, then delegates to the
comparison. Removing or altering after's process or protection list cannot remove
protection from this check. The result establishes only equality of the defined
snapshot fields, not actual engine execution or historical truth. Tempo changes
that alter elapsed seconds, timbre, and audio results are outside this musical-tick
protection contract.

### 10.7 Template replacement and detachment — Revision 1

These are explicit editing operations over the existing phrase-library format.
They introduce no profile fields or changed wire meanings. Both require the exact
Draft 2 or 3.0.0 root and supported phrase-library version. They validate before and after,
leave all caller-owned inputs unchanged, and expose no partial result on failure.
The optional `protected_note_ids` is a fixed, duplicate-free list or tuple selected
before the operation. A failed comparison raises
`OPERATION_PROTECTION_VIOLATION` before a result is exposed.

#### Replace one template and refresh every linked instance

`replace_phrase_template(document, template_id, replacement, *, protected_note_ids=(), max_notes=100000)`
requires a valid document whose **entire phrase profile is current**. It must not
overwrite a manual edit hidden in a stale realization. The target template must
exist. `replacement` is a complete template object under the existing schema, with
the same ID as `template_id`; it is not a patch or an implicit rename. Invalid
structure, duplicate local note IDs, and unresolved sound slots are rejected.

The operation replaces that template and refreshes all instances referencing it
in one transaction. It retains every instance's ID, template ID, clip ID, repeat
count, and transpose, and retains the parent track and each Core clip's ID, start,
name, and own enabled state. Track enabled state is also retained. Clip duration,
notes, and mapping are recomputed with the unchanged phrase equations. Unrelated
templates, instances, and Core clips remain as stored.

For a surviving `(repeatIndex, templateNoteId)` key, the Core note ID comes from
the **original valid map**, including custom non-hash IDs assigned by another
writer. Only new occurrences use the reference materializer's deterministic hash
policy. Reserve all original Core IDs, including those removed in this transaction,
and all newly allocated IDs. A collision rejects the whole operation. Renaming a
source note is removal plus addition; identical pitch or timing does not establish
identity.

Refresh sorts Core notes by `(local start, repeatIndex, source array index)` and
mappings by `(repeatIndex, source array index)`. Pickups, tails, late starts, and
overlaps remain. Before expansion, bound the **total resulting notes across every
affected instance** by `max_notes`. The default 100000 is an operation resource
limit, not a wire maximum. Empty templates must not enumerate huge empty repeat
loops. An invalid pitch, span, local start, or absolute endpoint in any affected
instance rejects the entire operation.

If a Core lyric alignment still references a removed note, fail with
`OPERATION_REFERENCED_NOTE_REMOVAL` and diagnostics identifying the reference.
Never silently delete a lyric unit, alignment, or reference. References to surviving
notes retain their IDs. Retained identity does not itself freeze pitch or gate;
the supplied protection set checks those properties where required.

Other extension payloads are preserved. Core edits trigger conservative
invalidation using changed clip and removed note references. Removal can broaden
invalidation when deleted ancestry cannot be determined; invalidation is not
limited to the immediately edited objects. A nontrivial profile-only edit also
conservatively marks other non-`none` current claims unverified. The updated phrase
profile uses document dependencies and is certified current only after complete
post-validation. Protection must also pass before returning the result.

An identical validated replacement is a no-op copy, including status,
dependencies, and array order; requested protection is still checked. If duration
and notes are identical and only the template name changes, preserve Core and
the existing note-map order while applying the metadata/status/dependency update.
Loading never performs this synchronization, and unrelated stale relationships
are not silently repaired.

#### Detach selected instance relationships

`detach_phrase_instances(document, instance_ids, *, protected_note_ids=())`
removes only the selected instance records. The ID list or tuple must be
duplicate-free and every requested instance must exist. A mixed existing/missing
request fails atomically. An empty list returns a validated no-op copy, with
requested protection still checked.

The input phrase profile may be current, stale, or unverified, but the whole
document must remain valid under its structural and Core requirements. Every
template and every Core object remains unchanged, including notes and IDs, clips,
timing, lyric alignments, harmony, array order, clip enabled states, and track mute.
A manually edited clip with a stale link can therefore be detached without
regenerating it.

This profile edit conservatively invalidates other non-`none` current extension
claims, but preserves the phrase envelope's **original status**. Removing the last
mismatch does not promote stale or unverified to current. Existing phrase
dependencies are retained; over-declaration is allowed. Validate the resulting
whole document and the fixed protection list before returning it. The operation
does not repair unrelated relationships or delete Core notes or lyric references.

#### File-input protection

For `replace-template`, both the source document and the `--replacement` template
JSON file are protected inputs. Existing output requires `--force`, which never
permits overwriting either input through its path, hard link, or symbolic link.
The replacement file is one strict UTF-8 JSON object. `detach` uses the same atomic
output policy. CLI `--protect-process` obtains its IDs only from before's current
generation process. Complete output is staged before publication. Validation,
operation, protection, staging, or publication failure before commit leaves the
final destination unchanged. If the required atomic publication is unavailable,
the writer fails rather than using a non-atomic fallback. This does not promise
rollback of failures after publication.

## 11. Migration from OSTF 2.3

The published 2.3 profile does not fully standardize all nested pitch and lyric
alignment semantics. Changing the version string therefore does not convert a
document to 3.0. The source baseline and the bounded converter are documented in
the [migration guide](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0), with links to the upstream
specification and schema at the reviewed commit.

### 11.1 Default migration and source archive

The supplied converter transfers recognized, correctly interpretable title and
timeline structure. It MUST NOT assume general meanings for opaque performance,
parse arbitrary chord labels into structure, or infer lyric references. It records
reasons for material that cannot be transferred into valid target structures.
The specifically labeled historical 2.3→3.0 converter defaults to root version `3.0.0`. Its API keyword
`target_version=VERSION` and CLI `--target-version` also accept an explicit
`3.0.0-draft.2` target; Draft 1 is not a target of this 2.3 adapter. Its conservative
source interpretation policy is unchanged. It does not infer reusable templates or
candidate groups from legacy material.

The complete original source is stored as UTF-8 text in the archive profile:

```text
namespace: org.ostf.legacy-import.v1
version:   1.0.0-draft.1
status:    unverified
dependencies: { scope: document }
data:
  sourceVersion: 2.3.0
  sourceSha256: SHA-256 of the original UTF-8 bytes
  sourceText: complete original source text
  conversionReport: JSON object
```

These four data fields are required. `sourceSha256` uses 64 lowercase hexadecimal
characters. The report records transferred material, uninterpreted material,
assumptions, warnings, and ID mappings. Re-encoding `sourceText` as UTF-8 MUST
reproduce the original bytes for which the hash was computed, including original
line endings and numeric spelling.

The archive is historical source evidence, **not another playback authority**.
It MUST NOT be automatically updated to match subsequent 3.0 edits or played on
load. Source preservation and complete migration of musical meaning are distinct
claims.

### 11.2 Explicit sample-convention interpretation

Only explicit selection of `interpret-sample-notes` authorizes the adapter to
interpret sample `pitch` values as integer pitches with C4 = 60, note `start` as
clip-relative ticks, and note `duration` as a gate. Mapping sample `symbol` to a
display-only chord `label` is likewise an explicit reported assumption.

If unknown attributes could change mute, transposition, dynamics, or other
meaning, an object MUST NOT be silently activated as a successfully converted
performance or chord. The explicit adapter uses the strict sample shapes described
in the migration guide, archives unsupported objects, and records the reason.
Explicit adapters for identified application dialects may be added later.

Cross-kind legacy ID collisions are deterministically remapped to globally unique
Core IDs. Ambiguous source references MUST NOT be guessed or redirected.

### 11.3 Appearance and limits

Known legacy roles and colors may be transferred to the new appearance profile
where target ID correspondence is established. If a corresponding clip or other
object was not transferred, its color MUST NOT be assigned to a different object.
Unconverted information remains in the original source and report. Identical
appearance may be claimed only after checking the legacy precedence rules as well
as the color values.

3.0.0 does not guarantee a completely reversible 3.0-to-2.3 conversion. New 3.0
performance has one authoritative representation in `tracks`. The supplied
converter performs bounded source checks and validates its generated target; this
is not complete 2.3 conformance testing or a claim of complete semantic migration.
The ability to represent lyric relationships and chord labels in 3.0 does not
establish the meaning of arbitrary opaque 2.3 data.

## 12. Worked timing and relative-note example

The complete JSON example in the
[Japanese specification, Section 12](FORMAT_SPECIFICATION.ja.md#12-最小構成を拡張した記述例)
has PPQ 960, a clip beginning at 0 with duration 3840, and the following notes:

| Pitch | Local start | Gate duration | Absolute gate end |
|---|---:|---:|---:|
| C4, 60 | 0 | 720 | 720 |
| E4, 64 | 960 | 720 | 1680 |
| G4, 67 | 1920 | 2400 | 4320 |

The final gate ends at tick 4320 although the clip's reference span ends at 3840.
The E4 assignment uses a C anchor of 60 and the second element, 4, of the declared
chord intervals: `60 + 4 + 0 = 64`.

A reader without the relative-phrase profile still reads pitches 60, 64, and 67
and their timing directly from Core. The profile contributes no additional notes.
The [four-part sample](../samples/01_four_part_sketch.ostf) and
[sample index](../samples/INDEX.md) provide additional complete files.

## 13. Verification and conformance claims

The original 39 acceptance conditions remain in the [Japanese specification, Section 13](FORMAT_SPECIFICATION.ja.md#13-適合試験の基準). Together with D201–D230 and R101–R120, the [consolidated conformance table](FORMAT_SPECIFICATION.en.md) records 89 acceptance viewpoints. These describe required behavior and do not claim executed passes. Earlier Draft 2 Revision 1 reports remain in earlier development packages; test runners and historical execution reports are not included in this curated release.

No runtime verification was performed for this release preparation, and additional runtime verification is not a publication prerequisite. The [sample catalogue](../samples/INDEX.md) contains authored examples. [Coverage](COVERAGE.md) records field and enumerated-value presence, not runtime or semantic validation.

Keep these verification layers distinct:

1. **Strict input:** UTF-8 and JSON ingestion, forbidden representations, integer
   token form, and unknown numeric precision.
2. **Structure:** Core schema and exact known profile data schemas.
3. **Semantics:** time sums and ordering, global IDs, typed references, profile
   correspondence, and dependency coverage.
4. **Adopted performance:** deterministic event lists and performance end against
   independently specified expected results.
5. **Migration:** conservative default behavior, opt-in assumptions, exact source
   reconstruction, ID mapping, and handling of ambiguity.
6. **Explicit operations:** root-version conversion without changed Core events,
   phrase materialization, atomic candidate activation, and fixed before/after
   protection comparisons against independently specified expectations.

JSON Schema success alone is not success at every layer. Optional-profile editing
behavior is required only of implementations that claim to perform that behavior.
Testing regeneration with protected notes requires before-and-after data. Actual
interchange between external applications is a separate interoperability test and
must be reported separately from format verification.

## 14. Support declarations and implementation sequence

An implementation should describe Core reading, Core writing, editing that
preserves unknown information, display/editing of each profile, and proprietary
regeneration separately. A single undifferentiated “OSTF support” claim is
insufficient to communicate these capabilities.

### Initial release scope

The initial 3.0 scope consists of stable Core, the existing six provisional
profile namespaces at `1.0.0-draft.1`, current explicit operations, and conservative
migration.
Optional profiles and editing behavior apply only to implementations claiming
that support. Chord-aware fitting, partial overrides, time scaling, whole-song
scenes, notation models, additional expression, and external adapters are deferred
and are not completion conditions for the initial stable Core distribution.

Existing types, required fields, and numeric bounds form the initial baseline.
A later incompatible change must identify its effect on previously valid files
or meaning and use an appropriate version. The [license notice](../LICENSE.md) defines the release scopes. [Source attribution](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0) records upstream 2.3 materials and their preserved provenance.

### Implementation and verification units

The units below explain implementation support and required behavior, not new
execution evidence. This stable preparation adds no runtime verification
requirement to its publication decision; earlier results remain historical records.

The implementation and verification units are:

1. Strict JSON input, Core schema, and timing/identity/reference validation.
2. A Core event extractor with independent expected-event tests.
3. Root-version dispatch and preservation of the original relative, generation,
   appearance, and legacy archive profile definitions.
4. Conservative 2.3 migration separated from explicit sample interpretation.
5. Phrase-library and clip-choice validation, explicit operations, and snapshot
   protection checks with independent expectations.
6. Revision 1 replacement across all linked instances with surviving identity,
   lyric integrity, fixed protection, atomic failure, and Core-preserving detachment.
7. Explicit conversion among the three supported roots with unchanged Core
   content, while retaining the restricted legacy draft conversion API. Identify
   the version covered by every schema, authored example, and historical result;
   do not count this unexecuted update as part of an earlier passing run.

Verification reports must identify commands actually run, the environment,
versions tested, positive and negative results, and unsupported scope. Unexecuted
tests MUST NOT be counted as passing.

### Deferred extensions and maintenance policy

| Topic | Initial release treatment | Later work |
|---|---|---|
| Source phrases and placement instances | Existing concrete templates, transposition, and repeats, plus explicit whole-template replacement/all-instance refresh and detachment | Automatic resynchronization, partial overrides, time scaling, and repeat phase |
| Candidates and whole-song scenes | Multi-part Core-clip choices reflected in clip enabled states | Harmony, lyrics, structure, routing, and real-time scene launch |
| Note protection | Fixed before/after identity, target, absolute ticks, gate, optional velocity (including absence), and effective enabled state | Integration with generators and protection of seconds, timbre, or audio |
| Input-note rank selection | Not included in the current relative profile | Distinguish pitch order and input order from harmonic degree |
| Notation and performance | Gate duration and lyric alignment defined | Notated values, ties, voices, enharmonic spelling, and tuplets |
| Detailed percussion | Slot IDs, roles, and output hints | Instrument-specific maps, choke behavior, and articulations |
| Expression | Optional Core note-on velocity 1–127 only (3.1); absent remains absent | CC, bend, aftertouch, note-off velocity and full expression streams remain outside Core |
| Multiple harmonic contexts | Overlap and explicit context IDs allowed | Lanes, display precedence, and local contexts |
| Numeric limits | Existing bounds adopted as the initial baseline | Revisit in a later version with explicit compatibility consequences |
| External products | No support is presumed | Actual adapters and interoperability tests |

The goal is to let a small generator participate by writing adopted notes,
while allowing a more capable implementation to add compositional relationships
on the same foundation.

## 15. Design references

The source-backed rationale is listed in the
[Japanese specification, Section 15](FORMAT_SPECIFICATION.ja.md#15-設計判断の根拠).
It covers relative phrases versus realized notes, pickup and tail preservation,
protected regeneration, input-note rank versus harmonic degree, independent chord
and accompaniment cycles, percussion mapping, gate versus notation duration,
melody-first workflows, and multiple generation inputs.

Those references inform the design. They do not claim that any named product
implements OSTF or has been tested with this package.

## 16. Change record

Draft 1 places the authoritative adopted performance in `tracks` and defines
pitch or percussion target, onset, and gate duration. It defines declared PPQ,
notes crossing placement boundaries, same-pitch overlaps, stable IDs, structured
harmony, and many-to-many lyric alignment. It introduces extension status and
dependencies, three standard optional profiles, and conservative migration with
original-source preservation.

Draft 2 retains those Core semantics and adds phrase-library and clip-choice
profiles. It defines finite repetition and transposition of concrete templates,
complete Core-note correspondence, candidate selection reflected in `clip.enabled`,
explicit materialization and selection, and fixed before/after protection checks.
Root-version dispatch and explicit conversion preserve the original profile
versions. The conservative 2.3 converter advances its output root version to
Draft 2. Automatic regeneration, whole-song scenes, and audio or MIDI engine
execution are not introduced.

Draft 2 Revision 1 retains the wire version and both schemas while adding
`replace_phrase_template` and `detach_phrase_instances`. Replacement refreshes all
linked instances of a current template in one transaction, retains existing Core
IDs for surviving occurrences, and checks Core lyric references to removed notes
and supplied protection. Detachment removes relationships while preserving Core,
lyrics, identities, mute states, array order, and the phrase envelope's original
status. Both operations leave caller inputs unchanged and expose no partial result
on failure. Automatic synchronization, time scaling, harmony fitting, and an
override layer are not added.

Draft 2 Revision 2 closes the initial feature scope, separates later extensions,
and records public-draft readiness and remaining stable-release tasks. It aligns
the languages on strict integers in supported profiles and non-invalidation for
an identical current candidate selection. Code, schemas, samples, and historical
execution results are retained; no additional runtime verification is performed.

3.0.0 adds the stable Core root and schema while retaining both historical
schemas unchanged. All six provisional profile versions and meanings, and all
existing editing safeguards, are preserved. Phrase and choice operations accept
Draft 2 and stable documents. `convert_version` now supports all four roots; the
legacy `convert_draft_version`, `upgrade`, and `downgrade` remain draft-only.
2.3 migration and default schema selection now target 3.0.0. The formal distribution organizes 60 stable examples and two legacy inputs, complete manuals, parameter references, coverage and license notices. This documentation work does not change the established Core/schema/reference-code semantics. No runtime verification was performed. [LICENSE.md](../LICENSE.md) states the release terms.

Subsequent changes must identify whether they affect Core, profiles, migration, or
verification. Incompatible changes require an appropriate version change.



## 17. OSTF 3.1.0: track semantics, editor hints and portable MIDI routing (normative)

This section extends and, where explicitly stated, supersedes Sections 2, 6 and 7 for
**root `version: "3.1.0"` only**. All other 3.0.0 Core rules, all six
profile namespaces and their exact provisional `1.0.0-draft.1` versions are
preserved. These optional annotations NEVER replace stored Core notes. The
[3.1.0 JSON Schema](../schema/ostf-3.1.0.schema.json) is normative for structure;
this text supplies the additional semantic checks and host behavior.

### 17.1 Wire-version negotiation and compatibility

A 3.1 reader MUST support valid 3.0.0 documents without silently changing the
root version. A Writer MUST emit exactly the version it conforms to. A 3.0-only
reader need not accept 3.1.0, because root versions are exact. To upgrade, first
validate the 3.0.0 source, then explicitly replace its root version with 3.1.0;
all other Core content can remain byte-equivalent in *value*. New fields are
optional. The migrator MUST NOT invent `gm1` mappings from `midiNote`, role,
instrument names or a DAW's drum editor state. Converting a 3.1 document with
3.1-only fields to 3.0 is lossy and MUST require explicit opt-in and a field-loss
report. Unknown extensions and exact numeric token kinds MUST remain preserved.

### 17.2 Track category (new optional `track.trackType`)

| Value | Permitted note targets | Intended meaning |
|---|---|---|
| `melodic` | `pitch` only | Pitched instruments, including pitched percussion or tuned 808 bass |
| `drum` | `soundSlotId` only | Discrete percussion targets, independent of the renderer |
| `mixed` | Either `pitch` or `soundSlotId`, including both | Intentionally combined content; not a promise that one host track can play it |

The declaration is not a DAW track type. It MUST agree with **every stored note**,
including disabled tracks/clips. An empty track MAY declare any type; declaring
`mixed` does not require both event kinds to be present. If `trackType` is absent,
readers MUST infer from **all** note targets: only pitched = `melodic`, only
slots = `drum`, both = `mixed`, and no notes = `unknown`. Inference MUST NOT use
track name, role, instrument name, `midiNote`, MIDI channel or the plug-in preset.
The inferred category is not automatically inserted into the saved document.
Existing 3.0 `pitch` notes are never retrospectively changed into drums merely
because their values match GM percussion numbers. A GM drum MIDI importer SHOULD
create `soundSlotId` events and declare explicit mappings where it knows the
actual source mapping.

### 17.3 Editor selection (new optional `track.preferredView`)

`preferredView` is one of `auto`, `pianoRoll`, `drumGrid`; omitted = `auto` as a
semantic fallback, not as an inserted key. It is a *non-binding presentation
hint*. For `auto` the recommended display is `drumGrid` for drum tracks and
`pianoRoll` for melodic, mixed and unknown tracks. An editor MAY offer a combined
view for mixed content. `drumGrid` requested for a mixed track MUST NOT hide or
silently discard pitched notes: show both safely or fall back to `pianoRoll`.
A host lacking a requested view MUST use a safe fallback; it MUST NOT reinterpret
note targets, alter pitches or modify `trackType`. User-specific view overrides
need not be serialized. The format does not instruct a particular DAW to switch
its UI automatically.

### 17.4 Explicit percussion mapping (new `soundSlot.midiMapping`)

```json
"soundSlots": [
  {"id":"kick", "name":"Bass Drum 1", "midiMapping":{"standard":"gm1","note":36}},
  {"id":"snare", "name":"Acoustic Snare", "midiMapping":{"standard":"gm1","note":38}},
  {"id":"fx", "name":"Custom FX", "midiMapping":{"standard":"custom","note":12}}
]
```

The `midiMapping` object has exactly `standard` and `note`. `standard="gm1"`
identifies the GM Level 1 *percussion key map*, with `note` 35 through 81;
`standard="custom"` accepts MIDI 1.0 note numbers 0 through 127 and makes no
GM claim. The key-map source is the [GM Level 1 specification](https://midi.org/general-midi-level-1).
Its notes are conventionally sent on **MIDI channel 10** (1-based; channel index
9 on the MIDI wire). OSTF does not prescribe a DAW's track type or MIDI port.
GM2, GS, XG and manufacturer-specific articulations and choke groups are NOT
inferred from a GM1 declaration.

`soundSlot.id` remains the stable musical/percussion target identity; mapping
note numbers are output keys, never IDs. Multiple slots MAY map to the same MIDI
note and MUST remain distinct in OSTF; a MIDI-only round trip may then lose
identity and requires a loss report. `soundSlot.role` and `name` are descriptive;
readers MUST NOT infer an unprovided `midiMapping` from them.

The pre-existing `midiNote` remains only a **legacy hint**. When both
`midiMapping` and `midiNote` are present in a 3.1.0 file, their note numbers
MUST match or the document is invalid. If only `midiNote` is present, a portable
MIDI exporter MUST treat the output as **unresolved**, unless a user explicitly
opts into legacy-hint routing and records this assumption. Missing mapping does
NOT make the OSTF document invalid or make the slot disappear. A host SHOULD
ask the user for a mapping or report `UNMAPPED_SOUND_SLOT` rather than invent a
kick, snare, note number, or sound.

### 17.5 Instrument semantics versus sound source (new `track.soundSource`)

The existing `track.instrument = {name, family?}` describes the *musical intent*
of an instrument. It does not select or reproduce a plug-in. The optional
`track.soundSource` hints at the *target for rendering* and is independent of
`instrument`. It contains a required `kind` and optional permitted members:

| `kind` | Meaning | Fields |
|---|---|---|
| `unassigned` | No selected renderer | No source-specific fields |
| `gm1` | Informational GM Level 1 renderer class only; NO voice or program number | No additional fields |
| `externalMidi` | Destination outside the host or plug-in | Optional opaque `identifier` |
| `plugin` | Requested software plug-in identity | Optional opaque `identifier` |
| `hostManaged` | Host-selected source, not portable | Optional opaque `identifier` |

`identifier` is prohibited with `gm1` and `unassigned`. **OSTF 3.1 does not
serialize MIDI Program Change, Bank Select, GM program numbers or a standardized
plug-in preset.** The `gm1` kind merely states a renderer class, not a timbre.
The exact permissible structure is in the 3.1 schema. A plug-in
identifier is *not* its binary plug-in state, sound bank, licensed preset,
parameter automation or a request to install it. Vendor settings MAY be
preserved in namespaced `extensions` as opaque data. Unsupported readers MUST
not execute, fetch or overwrite local devices from such data. The adapter
MUST report an unavailable plug-in/port or missing preset as an unresolved
rendering constraint rather than claim timbre equivalence.

### 17.6 Host-neutral MIDI expansion contract

An OSTF-to-MIDI/DAW adapter MUST first validate the exact supported root and its
semantics. Its authoritative note source is `tracks[].clips[].notes[]` only:
never synthesize extra notes from chord labels, candidates or phrase templates.
Each active note has absolute tick coordinates `clip.start + note.start` and
`+note.duration`, with independent identity, even if it overlaps the same MIDI
pitch. Track/clip `enabled` flags are applied independently; the adapter SHOULD
preserve inactive clips as editable muted content when its host supports that.
When it cannot, a loss report is REQUIRED. Clip reference duration MUST NOT
silently truncate pickups, late starts or gate tails.

The adapter maps each OSTF track to one logical destination group, retaining
source `trackId`, `clipId`, `noteId` in sidecar/host metadata when possible.
Pure pitched notes map to MIDI note numbers equal to `pitch`. Sound-slot notes
use only the explicit `midiMapping.note` (or individually disclosed opt-in legacy
routing). If a track is `mixed`, the adapter SHOULD split the pitched and drum
events into two output streams with the same source track provenance unless the
host supports distinct independent routing within one track. Neither kind may
be dropped merely because a requested editor view is unavailable.

For GM1 percussion the suggested MIDI 1 channel is 10 (1-based). Other MIDI channels, ports and hardware destinations remain adapter choices.
When `track.drumKitId` exists, the declared kit membership and order MUST be
retained as independent document metadata. Mapping a kit to a host drum instrument
or pad layout is an opt-in adapter concern. OSTF 3.1 Core MAY contain explicit per-note velocity. An adapter MUST preserve
that value on note-on events, and MUST disclose the fallback policy for notes
that omit it (for example MIDI velocity 100). This fallback MUST NOT be written
back to missing source fields. CC, bend, aftertouch and note-off velocity remain
outside Core. No adapter may claim full expressive or audio-state reproduction.
OSTF carries **no Program Change or Bank Select values or events**. Importing an
OSTF track MUST NOT cause automatic PC, CC0, CC32 or plug-in preset changes.
MIDI routing is a host-/user-controlled step, never an implicit source of hidden events.

The native OSTF PPQ is authoritative. Converting to a target PPQ MUST preserve
all absolute onsets and gate ends when exactly representable; otherwise the
adapter MUST refuse by default or require explicit quantization and report
per-note rounding and any gate collapse. Source metadata in OSTF should remain
unchanged. The Standard MIDI File metrical PPQ division uses a 15-bit field,
so not every OSTF `ppq` fits directly into an SMF header; adapters must select
and disclose a conversion. Same-pitch overlaps, slot collisions, disabled clips,
source IDs and plug-in state do not generally survive an ordinary MIDI-only
round trip; such losses must be stated. No DAW, VST/AU/CLAP or Max for Live
implementation is guaranteed by this wire specification.

### 17.7 Release conformance boundary

Three distinct claims are permitted: (1) a correctly authored OSTF 3.1.0 file,
(2) a reader/exporter implementing exactly the documented fields and checks,
and (3) tested interoperability with named host applications. Publishing a
schema and reference source establishes **neither** (2) nor (3) without the
corresponding verification. New 3.1-only fields are optional, and a 3.0.0
file accepted by a 3.1 reader retains its declared version. The six 3.0
optional profile versions remain provisional; no audio or plugin state parity
is promised. A 3.1.0 document may be content-valid even with unresolved MIDI
sound slots or unavailable sound sources.


### 17.8 Portable multilingual names (new `localizedNames`, `localizedTitles`)

The original UTF-8 `track.name` is REQUIRED in 3.0 and 3.1, and **MUST remain the
original stable fallback**. It may contain Japanese, Korean, Chinese, Arabic, RTL
scripts, diacritics or mixed-language text. Track IDs are independent ASCII
identifiers and MUST NOT be derived from translated or displayed text.

3.1 adds an OPTIONAL `localizedNames` object to `track`, `instrument`, `soundSlot`,
`clip`, and `section`; `song` instead uses OPTIONAL `localizedTitles` alongside
its existing required `title`. A locale map uses language tags as keys and
nonempty Unicode strings as values. Tags use a conservative well-formed BCP 47
subset: a 2–3 or 5–8 letter primary language followed by zero or more 2–8
alphanumeric subtags (`ja`, `en`, `en-US`, `zh-Hans`, `zh-Hant-TW`, `ko`, `ar`,
`sr-Latn-RS`). Grandfathered and private-use-only tags are deliberately outside
3.1 Core; an implementer requiring them can use a versioned extension.

Tag matching is ASCII case-insensitive; tags with the same case-insensitive
spelling MUST NOT coexist in one locale map. Original tag spelling is preserved.
A localized value MUST NOT be interpreted as an ID, program, instrument category,
translator command or MIDI target. Empty maps SHOULD be omitted; an empty original
`name` remains permitted for 3.0 compatibility. UTF-8 and JSON Unicode validity
rules from Section 2 still apply. Neither NFC normalization nor case folding of
name **values** is required or allowed as a silent destructive rewrite.

For a requested display locale, choose: **exact tag → progressively remove the
rightmost subtag → original `name`/`title`**. For example, `zh-Hant-TW` checks
`zh-Hant-TW`, then `zh-Hant`, then `zh`; if none exist, use the original value.
Do not silently select `zh-Hans`, `en-GB`, or another region merely because the
primary language is similar. An unsupported or absent locale preference yields
the original. Automatic translation is NOT part of OSTF. Language switches are
presentation changes and MUST NOT alter stored names, IDs, notes or kit routing.

Host adapters SHOULD create native tracks in `tracks[]` order using the exact
original `name` by default. They MAY offer a user-selected localized display
name, but MUST retain original names and available translations in persistent
OSTF/sidecar metadata where possible. If host metadata cannot preserve them,
any claimed OSTF round-trip MUST disclose `LOCALIZED_NAMES_LOST`; an ordinary
MIDI file is never sufficient to guarantee name translations or source IDs.
Editors SHOULD render untrusted text as text (not markup or executable code),
apply bidi isolation for directionality, allow scrolling or accessible full-name
inspection when clipped, and avoid replacing unsupported glyphs in the file.

Example (one logical track, multiple display forms):

```json
{
  "id":"trk-taiko", "name":"和太鼓",
  "localizedNames":{"en":"Taiko Drums","zh-Hant":"太鼓","ko":"다이코"},
  "trackType":"drum", "drumKitId":"kit-taiko", "clips":[]
}
```

### 17.9 Variable drum tracks and kit membership (new `drumKits`, `drumKitId`)

A song MAY have any number of drum, pitched-percussion, melodic or mixed tracks,
including zero. Multiple independently named percussion tracks (e.g. kit drums,
Latin percussion, auxiliary/electronic percussion) are normal `tracks[]` entries.
Track order is the array order. Creating/removing a track never implicitly
creates/removes a global Sound Slot or deletes its notes from unrelated tracks.

`soundSlots[]` remains the single **global registry of stable targets**. It has no
fixed count, requires no GM conversion, and may contain unused slots. A new
OPTIONAL root `drumKits[]` array defines zero or more kits; each kit has a globally
unique Core `id`, a required source `name`, OPTIONAL `localizedNames`, and a
required ordered, unique `soundSlotIds[]` array of existing global slot IDs.
Empty kits are valid. Slots MAY belong to multiple kits; kit names and slot names
are not identifiers. A kit is a semantic editor grouping, **not** a virtual
instrument, audio file, GM Program, or MIDI channel.

`track.drumKitId`, if provided, MUST reference a kit and MUST NOT occur on an
explicitly `melodic` track or a track inferred to contain pitched events only.
Every stored `soundSlotId` event in that track, including disabled events, MUST
be a member of the referenced kit. Pitched notes in a `mixed` track remain
permitted, but are not members of a kit. No kit reference is REQUIRED on any
track; kitless legacy 3.0 slot notes remain valid in 3.1. For kitless tracks,
the global `soundSlots[]` order is the fallback lane order.

In 3.1 the common typed Core `ref.type` enumeration also includes `drumKit`, so optional extension dependency envelopes may identify kit records directly; the 3.0 enum remains unchanged. A kit ID is only a link, never an instruction to create a host drum rack or to
load samples. The default kit order is `soundSlotIds[]`; the editor SHOULD show
only slots referenced by notes within **that track** (including notes in muted
clips) when first opening its drum grid, in kit order, and SHOULD support a
scrollable lane view and a `Show unused` control that reveals all kit slots.
For a kitless track, the same UI rule uses the global slot order. Unused slots
MUST remain in the file and MUST NOT be garbage-collected on UI hide, mute,
track deletion or source-selection changes. Output MIDI mapping is still per
Sound Slot (`midiMapping`), NOT per display row, translated name or kit index.

Adding/removing kit membership or a track is an **explicit editor operation**.
Removing a Sound Slot referenced by any Core note or kit is invalid until those
references are explicitly resolved; the reader MUST NOT redirect to a name-
equivalent or MIDI-equivalent slot. Replacing a kit with a different kit MUST NOT
silently reassign stored events; an editor can instead create a new track, copy
or explicitly remap events and report any resulting changes. If different host
kits need different MIDI note routing for a similar percussion sound, use
distinct global slot IDs or explicitly configured host routing; 3.1 does not
standardize kit-specific mapping overrides.

### 17.10 Minimal sound-reconstruction boundary and round-trip identity

OSTF is an **arrangement interchange format**, not a DAW project. Track `name`,
`localizedNames`, `role`, `instrument`, `trackType`, `drumKitId`, `clips`, stable
IDs, note targets and array order are sufficient to reconstruct a logical
**named arrangement**. A host may choose an instrument manually or provide its
own default, but it MUST NOT assert that the OSTF supplied the original preset
or timbre. `soundSource` is an optional inert host-routing *hint* and is not
required for a faithful structural import.

No standard 3.1 field represents Program Change, Bank Select, instrument preset,
plug-in state, audio samples, CC, pitch bend, aftertouch or audio effects. Optional note-on velocity is a Core note attribute, not a sound-state guarantee. Reproducing the
original sound without external assets is outside Core, and any lost metadata
MUST be noted before claiming lossless re-export. A host unable to preserve
`drumKits` or localized labels may still import and play notes using a
user-approved mapping, but the resulting project is **not** a full reversible
OSTF conversion without a sidecar.

### 17.11 Compatibility and publication scope of this amendment

The required Core 3.0 fields and their semantics remain unchanged; all new 3.1
fields are optional, including `note.velocity` on performed Core notes. Explicit `3.0.0` reads remain legal without adding
`drumKits` or translations. Upgrading a file MUST NOT invent translations,
drum kits or program numbers. Downgrading from 3.1 to 3.0 requires an explicit
field-loss report for all 3.1-only fields at song, section, track, instrument,
clip, performed-note (velocity), slot and kit scopes (including an empty but explicitly present kit list).
All six optional standard profiles retain their original exact versions.

The included samples and bilingual parameter inventory are *static authoring
evidence*, not passing runtime tests. This specification does not claim host
support or an executed converter; see [release readiness](../RELEASE_NOTES.md).


### 17.12 Optional performed-note Velocity (normative 3.1 amendment)

OSTF's primary purpose remains exchanging **composition information** between
independent software, rather than reproducing a DAW project. Only a *performed*
note in `tracks[].clips[].notes[]` MAY have an OPTIONAL `velocity` property.
The exact allowed type is a JSON **integer token** with inclusive bounds **1–127**.
`0`, negative values, `128`, `null`, `true`, `1.0` and `1e0` are not valid
Velocity representations. `velocity` is neither mandatory nor defaulted by the
3.1 schema. **Absent MUST remain absent in a source-preserving save**; zero is
not a synonym for an omitted note or Note Off.

The same rule applies to the designated primary melody, other melodic tracks,
percussion, and `mixed` tracks. `song.primaryMelodyTrackId` still designates
at most one primary melody; it does not create additional melody structures.
`harmony.chords` and chord-tone / relative-note profiles describe *harmonic
meaning* and MUST NOT acquire Velocity properties. Lyrics/pronunciation and
note associations remain independent. Neither a chord nor a pitch-class
profile is an instruction to manufacture audible notes or expression.

- A reader MUST preserve every explicit source velocity (including for disabled
  clips/notes), and MUST NOT manufacture values during import, editing, or save.
  A note-level edit, clip move/split/clone or arrangement rewrite MUST preserve
  the explicit value on retained performed notes; new notes without a chosen
  velocity MAY omit it.
- A MIDI 1 exporter MUST use the stored 1–127 Velocity as **Note On velocity**.
  When absent, an adapter MAY supply an explicitly disclosed **export-time**
  fallback (this distribution's reference planner uses 100); it MUST distinguish
  source-specified values from fallback values in its output or loss report.
  Export-time defaults do not mutate OSTF. Note Off velocity is not specified.
- If an importer cannot capture source velocity, it MUST omit the field and
  disclose the information loss; it MUST NOT represent an invented value as
  the recorded original. If a target cannot reproduce the original envelope,
  sample velocity layers or articulation, timbral equivalence is not claimed.
- Core deliberately has NO automation streams, per-note expression curves,
  MIDI CC, Pitch Bend, Aftertouch, MPE, Note Off velocity, program change,
  synth patch state, audio data or sound-engine preset identity.
- A `3.0.0` file remains a `3.0.0` file on read. An explicit 3.0→3.1 conversion
  MUST leave its notes without velocity. A 3.1→3.0 converter MUST list and
  **refuse without explicit loss approval** when any Core velocity would be
  discarded. If approved, only the unsupported field is removed; onset,
  duration, pitch, ID and slot references survive unchanged.

**Independent exact-version optional profile:** Phrase-library
`org.ostf.phrase-library.v1` at `1.0.0-draft.1` retains its **original source
note shape** without Velocity. This version is NOT silently revised by 3.1.
Thus `phraseTemplate.notes[]` uses `phraseSourceNote` (not the revised Core
`note` schema), whereas already materialized notes under `tracks[].clips[]`
may carry their own Velocity. For a current phrase relationship, onset, gate,
target, transpose, occurrence mapping and IDs retain the prior exact contract;
Velocity is an independent realized Core note attribute, not a template
correspondence field. When an explicit linked-template refresh retains a
Core note ID, its explicit Velocity MUST survive; brand-new occurrence IDs
have unspecified Velocity. Editors SHOULD report when a replace/delete cannot
preserve that value. If storing velocities in reusable phrase *templates* is
eventually needed, it requires a separately versioned optional profile.

**Minimal writing example (only one note is shown):**

```json
{"id":"snare-hit","start":960,"duration":120,"soundSlotId":"snare","velocity":41}
```

Examples [74](../samples/74_velocity_melody_optional.ostf),
[75](../samples/75_velocity_drum_accents.ostf), and
[76](../samples/76_velocity_phrase_instances.ostf) are static authoring
witnesses, NOT execution results. [3.0 baseline](https://github.com/yoh-nak/Open-Song-Timeline-Format-3.0)
remains byte-unchanged for historical interpretation.

## 18. OSTF 3.2.0 normative amendment (supersedes conflicting 3.1 statements)

**Version:** the exact new root version is `3.2.0`; all eleven 3.1 required root fields remain required, while `contributors` is optional. The six existing optional profile versions remain exactly `1.0.0-draft.1`; none has been silently redefined. Existing `3.0.0` and `3.1.0` files are valid inputs to 3.2-capable readers without changing their original wire versions. Unknown future versions are not silently relabeled. No M4L/DAW playback or program-level behavior is required by this format.

### 18.1 Chord voicing and note velocity: meaning, location, scope

A 3.2 chord in `harmony.chords[]` MAY contain an optional `voicing` object with a required nonempty `pitches` array and optional `velocities` array. The array of `pitches` contains integer absolute MIDI-compatible pitches 0–127 and preserves order and duplicates; 60 denotes middle C as in Core 3.1. The optional `velocities` array MUST have exactly one entry for every entry in `pitches`, **positionally**. Each entry MUST be either an integer-token Note On velocity 1–127 or JSON `null` for *unspecified*. The array itself may be omitted; `null` in this array is not 0, Note Off, or deletion. `1.0`, `true`, and `1e0` are not legal velocity tokens. An empty `voicing.pitches` or mismatched `velocities` length is invalid. A writer MUST preserve pitch multiplicity, pitch/velocity pairing and received order. When a pitch is inserted/deleted/reordered by an editor, its paired optional velocity MUST follow that pitch instance; do not reassign by note number because duplicate pitches are legal.

This is the informational **chord-voicing snapshot** formerly represented by `voicing.pitches` in OSTF 1.3, newly made explicit in the 3.2 Core chord structure. It is neither a note-on/off sequence nor a replacement for the harmonic `root`/`intervals`/`bass` model. Its notes carry **no independent onset, gate or instrument**, and the chord `start`/`duration` states harmonic context, not per-voice performance. These velocities indicate suggested attack strengths only for a consumer that deliberately previews or materializes that voicing. Loading the song MUST NOT create another performed track, clip or MIDI notes, and a renderer MUST NOT sound both the snapshot and adopted Core notes without a separately requested user action. `harmony.chords[].intervals` remains unvoiced semantic pitch-class/interval information and MUST NOT gain velocity.

Voicing pitches may contain duplicate pitches, omissions, added color tones or notes outside the theoretical chord. A mismatch with chord label or intervals MAY be reported but MUST NOT cause automatic rewriting. A chord edit MUST NOT silently transpose, regenerate or invalidate voicing. Voicing extraction to actual performed notes is an explicit software operation with consumer-selected timing, gate and fallback velocity policies, not part of opening or reading OSTF. No identities or automatic voice-leading relationships are inferred from array position.

The actual main melody and every other melodic, mixed or percussion arrangement note ALREADY uses `tracks[].clips[].notes[].velocity` (optional 1–127) from 3.1. That mechanism is unchanged in 3.2. An absent Core velocity remains absent on save. Pitch/velocity in a chord snapshot and Velocity on Core performed notes do **not** synchronize automatically and do not imply that a harmonic symbol is performed. The existing phrase-template source note profile stays frozen without velocity; the actual adopted Core notes can still carry velocity.

### 18.2 Contributors and credits (optional Core song metadata)

`contributors` is an OPTIONAL root array of `contributor` objects: `id` (required Core-wide stable ID), `name` (required, nonempty original Unicode source display name), and optional `kind` (`person`, `organization`, `other`) and the existing `localizedNames`. IDs identify song-local records, not globally verified real-world persons; matching names do not establish identity.

`song.credits` is an OPTIONAL ordered list of `credit` objects: `id` (required Core-wide ID), `contributorId` (required reference to a `contributor`, never a name), `role` (required open, lower-case semantic token such as `composer`, `lyricist`, `arranger`, `performer`, `producer`, `engineer`, `publisher`; not a closed taxonomy), and optional source-language display `label`. One contributor MAY be referenced by multiple credit assignments, including multiple roles. The list order preserves intended public display order; duplicated names are permitted. A source record can be uncredited. A credit that references a missing or different-kind contributor is semantically invalid. Do not infer that translated names are separate people.

`song.subtitle` and `song.description` are optional original Unicode text. `song.alternateTitles` is an optional array of stable-ID `alternateTitle` objects with required nonempty `title`. New IDs for contributors, credits and alternate titles MUST be unique with all existing Core `id` values; old optional profiles are not redefined to accept them as typed `Ref` targets.

### 18.3 One independent Localization Extension

`extensions["org.ostf.localization.v1"]` is optional and uses the existing envelope `{version,status,dependencies,data}`. The only recognized exact profile version is `1.0.0`. Its `data` has the required `entries[]` array. Each entry is `{target,field,values}`, where `target.kind` plus stable `target.id` identifies a concrete object and `values` is an open language-tag → Unicode text map. Only the singleton `song` target omits `id`. A profile-local target additionally specifies the exact recognized `target.profile` namespace; missing/ambiguous profile-local identities cannot be guessed. Names, mutable array indexes and paths through arbitrary vendor JSON are NOT valid target references. The `dependencies` scope SHOULD be `document` because localization entries may reference contributor, credit and other kinds outside the unchanged 3.1 typed `Ref` vocabulary.

**Allowed target kind : field**

| Kind | Allowed source field | Notes |
|---|---|---|
| `song` | `title`, `subtitle`, `description` | Singleton; title always exists, optional fields must exist to be translated |
| `alternateTitle` | `title` | Stable Core alternate-title ID |
| `contributor` | `name` | Identity never depends on display spelling |
| `credit` | `label` | Only if an original optional label exists; the semantic `role` is not translated |
| `section` | `name` | Core timeline section |
| `track` | `name`, `instrument.name` | `instrument.name` only if the track has an instrument |
| `clip` | `name` | Only if name exists |
| `soundSlot` / `drumKit` | `name` | Core percussion labels |
| `lyricDocument` | `title` | **Metadata title only**; not lyric content or pronunciation |
| `resource` | `name` | Only if the resource has a name |
| `phraseTemplate` | `name` | Profile-local; `org.ostf.phrase-library.v1` exact supported version |
| `clipCandidate` / `clipChoiceGroup` | `name` | Profile-local; `org.ostf.clip-choices.v1`, context must not be ambiguous |
| `roleDefinition` | `name` | Profile-local; `org.ostf.appearance.v1` |

The source field MUST exist for a `current` localization; an entry cannot create absent Core fields. There MUST NOT be two entries with the same `(kind,id,profile,field,language-tag)` when tags are compared case-insensitively. BCP 47 language tags are **open**, never restricted to a fixed list of supported languages; script/region variants (`zh-Hant`, `pt-BR`) and private-use tags may be used. JSON Schema deliberately checks only loose syntax; a full BCP 47 well-formedness check, language tag normalization, and case-insensitive duplicate resolution require semantic processing. The schema alone does not certify full BCP 47 compliance. Translation strings can be empty to represent an intentionally blank translation, distinct from an absent translation.

The original Core source string remains the authoritative fallback. A host MAY select an arbitrary display language independent of its UI language. Display selection SHOULD use case-insensitive exact tag match, then a recognized parent-language fallback, then the source field without changing source data. For `current` extensions, localized text MUST NOT change IDs, chord notes, lyric-to-note alignments, or playback. Unknown or stale extension data MUST be preserved but MUST NOT silently override current Core data. Original Unicode sequence (including normalization form and line breaks) SHOULD be retained on a lossless round-trip; no automatic translation/transliteration is claimed.

The existing 3.1 Core `localizedTitles` and `localizedNames` remain accepted **without removal**. When the common extension specifies the same object, source field and case-insensitively identical language key as a legacy localized map, the two values MUST be identical, otherwise a current document is invalid due to a conflict. New files SHOULD put translations in the common extension and SHOULD NOT duplicate the same translation in legacy Core maps. A 3.2 reader MUST still understand legacy Core localized maps if no extension exists. A non-supporting 3.1 application is not obliged to interpret the new 3.2 document, and an explicit downgrade MUST disclose loss of all 3.2-only data.

Lyrics themselves are **NOT** automatically translated through this profile. A translated/rewritten lyric may need different words, syllables, units and alignments and must use an independent lyric document/workflow. The song's playback pitch, timing, word timing, source credits and original song metadata MUST NOT be rewritten just because display language changes. No UI language picker, font substitution, automatic language detection, machine translation or international recording/rights identifier is required.


**Schema target enforcement:** the `localizationTarget` condition requires `id` for every non-`song` target, and prohibits both `id` and `profile` on the `song` target. Profile-local kinds must provide their permitted namespace. The requirements are not merely reader UI conventions.




### 18.4 Preservation, migration, reporting and compatibility

A 3.2 reader MUST be able to read valid 3.1 and 3.0 documents using the corresponding version-specific definitions; reading MUST NOT silently upgrade or insert velocity, voicing, translation, credit or title fields. An explicit 3.1→3.2 upgrade may copy all original data and set version 3.2 without inventing any 3.2 values. A 3.2→3.1 downgrade MUST require loss acknowledgement for `voicing`, contributor/credit/alternate-title records, subtitle, description, the localization profile or any other 3.2 fields not supported by 3.1, and MUST report dropped 3.2-only data; conversely, unchanged 3.1 fields survive. A 3.2→3.0 downgrade additionally reports all 3.1 fields (including performed velocity) as before.

A writer that cannot interpret a supported extension MAY hold its envelope as opaque data; it MUST NOT claim to have recomputed valid `current` references after destructive edits. Edits invalidating a target SHOULD mark its localization envelope `stale`, preserve unknown original data, or ask the user before a lossy export. Supported `current` localization entries with missing targets, invalid fields, conflicted values or ambiguous identity are semantic errors. Imported legacy OSTF 1.3 `voicing.pitches` can be mapped to this new field only as an **explicit migration** from the older version's different document structure; changing the version string alone is not a 1.3→3.2 conversion.

### 18.5 Scope and release gates

This is still a *composition-information interchange format*, not a DAW project. New voicing velocities do NOT imply MIDI channel, note-off velocity, CC, bend, AT, MPE, program changes, preset state, mixing, audio, or detailed vocal performance. Cross-app behavior, actual voice rendering, UI and DAW adapters are separate implementation concerns. Publication as a **specification** is independent of executable validator tests and DAW testing; any such execution requires separate evidence and is not claimed here.

### 18.6 Independent Sound Slot playback and preview contract (3.2 normative interpretation; no new wire fields)

This section defines the boundary between **logical percussion events** in OSTF and **audible sound generation** in each application. It does not make audio samples, synthesis engines, drum-rack presets or DAW track configuration part of OSTF Core. A reader without audio playback remains a valid OSTF consumer.

1. `soundSlots[]` is the song-document-local registry of percussion targets. Every performed `note.soundSlotId` refers to a slot **by stable ID**, not by `name`, `role`, localized label, legacy `midiNote`, MIDI mapping number, kit position, or DAW pad position. Even two slots with identical names, roles or MIDI note numbers remain separate. Equal IDs in different song documents do not imply equal acoustic samples.
2. Each saved performed note is a separate event. Two different slots can trigger at the same tick; notes on the same slot may overlap or rapidly retrigger; different tracks can refer to the same slot. A shared slot ID refers to the **same logical percussion target** across tracks; authors needing distinct targets should create distinct slot IDs. Readers and converters MUST NOT silently deduplicate these events. Muted tracks/clips remain saved, and actual playback respects Core track/clip `enabled` semantics.
3. `drumKits[]` and `track.drumKitId` define **membership and display order**, not sample loading, program selection or a kit-local remapping of MIDI note numbers. A global slot may belong to multiple kits. Assigning a local preview tone MUST NOT change the OSTF note or kit membership.
4. `soundSlot.midiMapping` describes an **explicit external MIDI key mapping**, not a sample reference. An expressly declared `standard:"gm1"` mapping in 35–81 MAY be used by a preview renderer to choose an approximate GM1 percussion timbre; it does not promise an exact sound. `standard:"custom"`, even when `note` is 36, MUST NOT silently be interpreted as GM1 kick. Legacy `midiNote` alone is not a GM1 declaration. Free-text `name` and `role` never establish a unique, standard acoustic identity.
5. A renderer claiming **per-Sound-Slot playback** SHOULD keep host-side assignments independently indexed by slot and document. It MAY share actual synthesizer voices or timbres but MUST preserve the distinct event identities. Recommended preview resolution order: (a) explicit user assignment for this document/slot; (b) retained renderer-local document/slot assignment; (c) approximate built-in GM1 timbre only for explicit GM1 mapping; (d) unresolved. Unresolved slots MAY be silent or use an explicitly disclosed generic preview sound; the original OSTF data MUST remain. A plugin hint never authorizes automatic installation, loading or fetching external samples.
6. `note.start` and `duration` are **onset and MIDI-style gate**, not a universal measured audio-tail duration. A one-shot sample's release, open/closed hi-hat choke, voice stealing, round-robin, rolls and articulations are renderer-specific. A renderer can choose a runtime fallback Velocity for absent `note.velocity`, but MUST NOT insert that guessed value into the source document.
7. The format adds no fixed slot count or prohibition on simultaneous/retriggered events. Audio engines MAY have voice-count or performance limits; those limitations SHOULD be disclosed and MUST NOT be misrepresented as missing source notes.

**Conformance boundary:** format support requires preserving slot identity and timed events, not shipping 47 GM1 samples or guaranteeing identical audio. A player advertising per-slot playback distinguishes unresolved and approximate timbres and does not merge independent notes during its data interpretation.

### 18.7 Lossless boundaries for DAWs and drum plugins (3.2 normative interpretation; no new wire fields)

- OSTF-aware tools can exchange document-local `trackId`, `clipId`, `noteId`, `soundSlotId`, exact ticks, authored Velocity, and kit membership **without first reducing slots to MIDI keys**.
- For generic MIDI1/SMF, resolve the explicitly declared (`standard`, `note`) pair. `gm1` is a GM1 percussion key map; `custom` requires the destination's intended mapping even if its key happens to match a GM1 number. A destination-specific remapping is an explicit adapter/user choice and MUST NOT rewrite the OSTF source mapping. The conventional GM1 percussion channel 10 is only a routing recommendation, not a mandated Core channel.
- Disclose lost information when two distinct slots collapse to one output key/channel/port, when a destination pad/key is missing, when PPQ conversion rounds timestamps, or when overlaps, tails, disabled events, clip metadata, source IDs or kit metadata cannot be preserved. Two OSTF slots with one MIDI key remain valid in OSTF, but an ordinary `.mid` cannot necessarily reconstruct their original IDs. Use sidecar metadata where feasible; never claim lossless interchange without evidence.
- A note with a `soundSlotId` absent from the declared `soundSlots[]` is invalid. A declared slot **without** MIDI mapping is valid but unresolved for automatic external MIDI conversion. A preview renderer MAY assign it a local tone while an exporter MUST NOT fabricate a GM1 note number without explicit user action.
- Drum-phrase-generating plugins can exchange realized notes via their **MIDI input/output** or a product-specific OSTF adapter. The Core does not guarantee reconstruction of proprietary sequencer patterns, sample layers, articulations, choke groups, probabilities or plugin preset state.

**Scope:** existing `trackType`, `drumKits`, `soundSlotId`, `midiMapping` and optional performed `velocity` are sufficient for this contract. No new Core keys, program changes, CC, sample audio or mixer parameters are introduced. A future optional versioned profile may standardize more detailed percussion semantics if justified. Relevant examples: [61 GM1](../samples/61_gm1_drum_grid.ostf), [64 custom](../samples/64_custom_drum_mapping.ostf), [66 unresolved](../samples/66_unresolved_sound_slot.ostf), [71 multiple kits](../samples/71_multilingual_tracks_and_multiple_drum_kits.ostf), [73 unused slots](../samples/73_unused_sound_slots_preserved.ostf).

### 18.8 Mixed tracks, overlapping notes and destination routing (normative clarification; no new fields)

**Different target domains remain different.** In a `mixed` track, `note.pitch:36` is a pitched MIDI-compatible note, while `note.soundSlotId:"kick"` whose slot declares `midiMapping:{"standard":"gm1","note":36}` is a logical percussion event. They do **not** become equal merely because their eventual MIDI1 note number can be 36. The note-target union (`pitch` XOR `soundSlotId`) is authoritative. A consumer MUST preserve this distinction when reading or serializing OSTF.

An exporter that routes pitched notes and drum slots to a MIDI-only destination SHOULD use **separate, explicitly chosen destinations** (for example different ports, channels or destination tracks) when needed for correct timbres. If the destination combines them into the same MIDI key/channel/port, it MUST disclose the semantic and potential audible collision; it MUST NOT silently change either OSTF target or assert that the resulting MIDI is lossless. The conventional GM percussion channel is a *destination convention*, not a new OSTF `channel` field. Never assign every event in a `mixed` track to a percussion channel solely because one note uses a Sound Slot. If independent routing is impossible, the exporter may decline that mapping or require explicit user approval for loss.

Overlapping notes with the same pitched key **remain separate OSTF note IDs** even if a destination encodes multiple MIDI1 Note Ons on the same pitch and channel. Such a stream can be ambiguous when pairing Note Off events with distinct voices in a receiving instrument. A converter MUST preserve source event start/gate/velocity and identities in the retained OSTF source, report any limitation, and SHOULD select separate routing or an explicitly documented resolution policy when lossless performance is unavailable. Automatic deduplication, shortening or event disappearance is not an acceptable silent conversion. Simultaneous slot notes, short retriggers and disabled clip notes obey the same identity and retention policy. See [mixed example 63](../samples/63_mixed_track.ostf) and [same-key overlap example 69](../samples/69_muted_and_overlapping.ostf).

**Pad mute is not a Core Sound Slot property.** OSTF 3.2 has `track.enabled` and `clip.enabled`, but no `soundSlot.enabled`, per-lane solo or audio-mixer state. A Player MAY offer local per-Slot mute/solo during preview without writing it into the source document or dropping source notes. To exchange an intentionally disabled rhythmic part, use the existing track/clip enabled semantics with an appropriate track/clip structure or an explicitly adopted edit. No implicit per-Slot mute is invented by the reader.

### 18.9 Document identity, primary melody and release boundary (normative clarification; no new fields)

OSTF Core has document-local IDs but does not require a globally unique song-document ID. A sound-preview preset cache keyed solely by a `soundSlotId`, `trackId`, `song.title`, file basename or translated name is **not** safe across different documents. A renderer MAY associate user-approved preview assignments with a host-owned document instance/identity plus that document's Slot ID; such assignments are external application data and MUST NOT be represented as source OSTF timbres or automatically propagated across unrelated documents. Reopening a copied/renamed document is an application-level mapping decision.

`song.primaryMelodyTrackId` identifies **at most one designated existing track**, not a second special melody collection. A track explicitly declared `trackType:"drum"` or containing exclusively Sound Slot notes is not a useful pitched primary melody source: producers SHOULD NOT designate it as the primary melody; consumers MAY report the contradiction without converting percussion to pitch, manufacturing melody notes or deleting the original designation. An otherwise valid `mixed` track MAY be designated as primary when its pitched note material truly represents the melody. The schema retains the inherited reference constraints; this guidance does not impose a new rejection rule on 3.0/3.1 inputs.

These Sections 18.8–18.9 supplement the existing 3.2 interpretation. They add **zero wire keys**, do not modify the JSON Schema, do not promise acoustic equivalence, and do not introduce CC, program change, plug-in presets, mixer state or a DAW session model. The consumer's audio voice allocation, GM timbre approximations, Note Off delivery, DAW/plug-in routing and UI behavior remain implementation responsibilities. A spec package can be published without operational testing, but such publication is **not evidence that an OSTF Player or MIDI exporter implements the behavior**.

### 18.10 Chord-voicing display, adopted-note separation and cross-tool replay (normative clarification; no wire changes)

**A displayed pitch is not necessarily an authored Note event.** For a 3.2 song, a `harmony.chords[].voicing` snapshot and `tracks[].clips[].notes[]` are two independent sources with different semantics. A reader or Player MAY show the chord snapshot's ordered `pitches` and aligned `velocities` in a chord-oriented piano-roll panel or a separate non-performing overlay. Such a visualization MUST remain distinguishable from real note events; each voicing pitch has no independent note ID, onset or gate. A UI may use the chord's harmonic `start`/`duration` to locate the annotation, but MUST NOT silently claim those bounds as performed note-on/off times.

The only authoritative source for an automatically realized arrangement/performance remains the **adopted Core notes** under tracks and clips. Reading, changing display mode or selecting a chord MUST NOT materialize or double-sound the voicing as another arrangement clip. A user explicitly requesting chord audition MAY trigger an application-defined preview with its own fallback Velocity and gate policy; those defaults MUST NOT be written into missing `voicing.velocities` or Core Note `velocity` fields. When a user explicitly commits a voicing to a track, the application chooses new Note IDs, placement and gate, retains the source chord unchanged by default, and reports any different interpretation of null/missing Velocity. It MUST NOT overwrite an existing adopted note merely because pitch or timestamp is equal.

The main melody is an ordinary performed Core track designated by `song.primaryMelodyTrackId`, not a second copy of the same notes. Both Song and Track views SHOULD derive actual pitch, gate and Velocity from that same Note ID and track content, independently of any chosen preview instrument, drum pad, mixer state or localized displayed name. A source-only chord snapshot without an adopted track is valid: consumers which have no chord-preview synth MAY display it silently without fabricating notes. A Core reader that preserves the source unchanged does not need a piano roll, drum synth, localization UI, chord audition or MIDI exporter.

**Drum sample and program interchange remains intentionally limited.** The combination of a custom Sound Slot ID, a descriptive free-text `role`, and an optional `custom` MIDI key does not uniquely identify a physical timbre across unrelated plug-ins. A standardized acoustic identity, sample transport, velocity-layer model, choke and articulation profiles are not part of Core 3.2. When a named Slot's human description appears to conflict with its explicit GM1 note mapping, an adapter MAY warn the user but MUST NOT silently rewrite the mapping or create a new sound-source identity. All mapping, preview and export limitations are reported at the consumer/adapter boundary as covered in 18.6–18.9.
