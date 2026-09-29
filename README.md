# Contribution [#511]: [UI : add a svg icon for the crash cymbal]

**Contribution Number:** [1 / 2 / 3]  
**Student:** [Maeva Innocent]  
**Issue:** [https://github.com/Babali42/DrumBeatRepo/issues/511]  
**Status:** [Phase I complete / Phase II complete / Phase III / Phase IV] [In Progress / Complete]

---

## Why I Chose This Issue

[1-2 paragraphs explaining why this issue interests you, how it matches your skills/learning goals, what you hope to learn]

--- This issue interests me because as soon as I read the problem, I could already picture how I'd approach fixing it not with code but with words. which tells me I understand the problem well enough to get started. I've been wanting to work more on the design/UI side of projects, and this issue (mapping a missing icon to the crash cymbal) fits that curiosity nicely since it involves both asset work and understanding how the drum-image pipe connects data to visuals.
Through this issue, I hope to learn how icon-to-data mapping is handled in the drum-image.pipe.ts logic, how tests are structured to validate that mapping, and the general contribution workflow for this repo from finding the right asset, to writing a test, to submitting a clean PR.

## Understanding the Issue

### Problem Description

[In your own words, what's broken or missing?]

### Expected Behavior

[What should happen?]

### Current Behavior

[What actually happens?]

### Affected Components

[Which parts of the codebase are involved?]

---

## Reproduction Process

### Environment Setup

[Setting up locally on macOS, I hit a few errors along the way:

| Error | Cause | Fix |
|---|---|---|
| `bash: cd: engine/: No such file or directory` | Ran `cd engine/` from inside `frontend/` — `engine` and `frontend` are sibling folders, not nested | `cd ..` first, then `cd engine/` |
| `bash: sbt: command not found` | sbt wasn't installed on my machine yet | Installed via Homebrew: `brew install sbt` |
| `brew install sbt` failed building `openjdk` from source with `configure: error: XCode tool 'metal' neither found in path nor with xcrun` | Homebrew tried to build a new JDK (`openjdk 27`) from source as a dependency, which needs a full Xcode install. My macOS (14) is also unsupported by Homebrew for prebuilt bottles ("Tier 3"), forcing a source build | Already had Java 20 installed separately, so skipped the dependency: `brew install sbt --ignore-dependencies` |

Once sbt was properly installed, the rest of the setup worked as documented:
1. `cd engine/` → `sbt fastLinkJS`
2. `cd ../frontend/` → `npm run start`
3. Verified app running at `http://localhost:4200`]

### Steps to Reproduce

Issue #538 is a feature request, not a bug, so "reproducing" means confirming the gap the issue describes:
-Run the app locally (or visit www.drumbeatrepo.com)
-Open the genre/pattern selector
-Observe available genres:  Rock, Samba, Funk, etc.
-Confirmed Bossa Nova is not present as a selectable genre
-Expected per issue #538: Bossa Nova should be a selectable genre with an authentic pattern (reference: https://shedrums.de/bossa-nova-drum-beat/)
-Confirmed consistent by checking both the live site and the local build. I also looked for a file in my code for Bossa Nova

### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/maevainnocent/DrumBeatRepo/commit/a8a6e1d98570a5b133c9aa5e017a9c406b45b222
- **Screenshots/logs:** maevainnocent@Maevas-MacBook-Air DrumBeatRepo % git log -1 > commit-log.txt
maevainnocent@Maevas-MacBook-Air DrumBeatRepo % cat commit-log.txt
commit a8a6e1d98570a5b133c9aa5e017a9c406b45b222
Author: maevalabelle <maevainnocent8@gmail.com>
Date:   Thu Sep 24 17:11:32 2026 -0400

    docs: document reproduction of missing Bossa Nova genre (#538)
- **My findings:** I learned how to commit through the terminal and also log.

---

## Solution Approach
Implementation Plan (UMPIRE)
  -Understand: The app offers a fixed set of drum genres/patterns to select and play. Bossa Nova is missing. The issue asks for it to be added as a new genre with an authentic       pattern.
Match: Samba. drum and base or dancehall would fit 
Plan:
  -Add a BossaNova case to the genre enum/sealed trait
  -Define its kick/snare/hi-hat pattern data from the reference groove
  -Register it in the beat manifest/library
  -Verify the Angular frontend picks it up via the shared genre list with no extra wiring
  -Add a label/translation string if genre names are localized
Implement: (placeholder — Phase III)
Review: Check CONTRIBUTING.md for Scala style and commit conventions before opening the PR.
Evaluate: Add a Scala test asserting the new pattern's step count/hit positions; run sbt testFull; manually confirm Bossa Nova appears and plays in the running app.

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [x ] Test case 1: Circe decoding test: valid Bossa Nova metadata JSON decodes into the correct BeatMetadata case class
- [x ] Test case 2: Ran full existing suite (`sbt testFull`) to confirm no regressions — 27/27 tests passed
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] no integration test layer exists for this change; covered by the manual test below
- [ ] Integration scenario 2

### Manual Testing

Ran `sbt fastLinkJS` to rebuild the engine, then `npm run start` in the frontend. Verified in the browser at localhost:4200 that Bossa Nova appears in the genre selector and plays the expected pattern correctly.
---

## Implementation Notes

### Week 29 Progress

Implemented the Bossa Nova genre end-to-end. Since this app is fully data-driven for genres (no hardcoded enum or type list), the fix came down to three pieces:
Added frontend/src/assets/beats/bossa-nova/bossa-nova.json, a new pattern using a syncopated kick (tresillo-style), steady eighth-note ride, and rim click — the core rhythmic signature of bossa nova — reusing existing audio samples from techno/.
Registered the new pattern in frontend/src/assets/beats/beats-metadata.json.
Added a Scala test in BeatMetadataCirceSpec.scala verifying the new metadata decodes correctly via Circe.
Before writing any code, I checked frontend/src/types/engine.d.ts, frontend/src/app/domain/beat.ts, and the i18n files (en.json) to see whether genre names needed to be added anywhere else (a TypeScript union type or a translation map). They didn't — genre is typed as a plain string everywhere, and genre names aren't localized. This confirmed the app's genre system is fully data-driven, which simplified my original UMPIRE plan (I had assumed I'd need to add a case to a Scala enum, which doesn't exist).

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** Files modified: frontend/src/assets/beats/bossa-nova/bossa-nova.json (new), frontend/src/assets/beats/beats-metadata.json, engine/src/test/scala/com/drumbeatrepo/library/BeatMetadataCirceSpec.scala
- **Key commits:** - b163f20 feat: add Bossa Nova beat pattern (#538)
  - 1caf584 feat: register Bossa Nova in beats metadata (#538)
  - eaa9b0a test: add decoding test for Bossa Nova metadata (#538)
- **Approach decisions:** Reused existing techno/ audio samples rather than adding new ones, to keep the change minimal and unblock the genre being playable immediately. Modeled the pattern's data shape directly on dancehall/standard.json for consistency with existing conventions.

---

## Pull Request

**PR Link:** https://github.com/Babali42/DrumBeatRepo/pull/589

**PR Description:** Title: Add Bossa Nova genre
Description:
Closes #538
What this does
Adds Bossa Nova as a selectable genre with an authentic drum pattern, per the request in #538 (and the related genre backlog in #270).
Changes
Added frontend/src/assets/beats/bossa-nova/bossa-nova.json — a new pattern featuring a syncopated kick (tresillo-style), steady eighth-note ride, and rim click, which together form the core rhythmic signature of bossa nova
Registered the new pattern in beats-metadata.json
Added a unit test verifying the new metadata decodes correctly via Circe
Why this approach
Genres in this app are fully data-driven — there's no hardcoded enum, type, or i18n mapping to extend. I verified this by checking engine.d.ts, beat.ts, and the i18n files before writing any code, since genre is typed as a plain string throughout and genre names aren't localized. So the fix is scoped to just the pattern data, the manifest entry, and a test — no changes to application logic.
I reused existing audio samples from techno/ (kick, snare, hat) rather than adding new ones, to keep this PR minimal and get the genre playable immediately. Happy to swap in dedicated bossa nova samples if the project has a preference or existing asset pipeline for that.
Testing
sbt testFull — all 27 tests pass, including the new Bossa Nova decoding test
Manually verified in the running app (npm run start): Bossa Nova appears in the genre selector and plays correctly

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
