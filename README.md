# Contribution [#538]: [Music: Add Bossa Nova drum Patterns]

**Contribution Number:** 1  
**Student:** [Maeva Innocent]  
**Issue:** [https://github.com/Babali42/DrumBeatRepo/issues/538]  
**Status:** Phase IV complete

---

## Why I Chose This Issue

[1-2 paragraphs explaining why this issue interests you, how it matches your skills/learning goals, what you hope to learn]

This issue interests me because as soon as I read it, I could already picture how I'd approach it — not with code but by ear, tracing the rhythm from the reference tutorial into the kick/ride/rim-click pattern the app would need. I've been wanting to get more hands-on with how data and content (rather than just UI) drive an app's features, and this issue fit that well: adding a missing genre meant understanding how the beat library is structured and registered, not just writing new logic.
Through this issue, I hoped to learn how genre/pattern data flows through this app (from JSON files to the Angular frontend), how Scala's Circe library validates that data via decoding tests, and the general contribution workflow here — from confirming a genre is genuinely missing, to building its pattern data, to submitting a clean PR.



## Understanding the Issue

### Problem Description

The website comes with different genres and my issue is looking for Bossa Nova. It is as simple as adding Bossa Nova as a new Option.

### Expected Behavior

The User should be able to see Bossa Nova as an option and also select it and work with it just fine

### Current Behavior

Bossa Nova does not appear anywhere in the genre selector, either on the live site (drumbeatrepo.com) or in a local build. No corresponding file exists in assets/beats/, and no entry exists in beats-metadata.json.

### Affected Components

frontend/src/assets/beats/ — pattern data files
frontend/src/assets/beats/beats-metadata.json — genre registration/manifest
engine/src/test/scala/com/drumbeatrepo/library/BeatMetadataCirceSpec.scala — Circe decoding tests for beat metadata


---

## Reproduction Process

### Environment Setup

Setting up locally on macOS, I hit a few errors along the way:

| Error | Cause | Fix |
|---|---|---|
| `bash: cd: engine/: No such file or directory` | Ran `cd engine/` from inside `frontend/` — `engine` and `frontend` are sibling folders, not nested | `cd ..` first, then `cd engine/` |
| `bash: sbt: command not found` | sbt wasn't installed on my machine yet | Installed via Homebrew: `brew install sbt` |
| `brew install sbt` failed building `openjdk` from source with `configure: error: XCode tool 'metal' neither found in path nor with xcrun` | Homebrew tried to build a new JDK (`openjdk 27`) from source as a dependency, which needs a full Xcode install. My macOS (14) is also unsupported by Homebrew for prebuilt bottles ("Tier 3"), forcing a source build | Already had Java 20 installed separately, so skipped the dependency: `brew install sbt --ignore-dependencies` |
| Accidentally created a nested clone inside `engine/` | Ran `git clone` again from inside the project folder instead of a fresh location | Deleted the nested folder, re-cloned from the correct location |

Once sbt was properly installed, the rest of the setup worked as documented:
1. `cd engine/` → `sbt fastLinkJS`
2. `cd ../frontend/` → `npm run start`
3. Verified app running at `http://localhost:4200`

### Steps to Reproduce

Issue #538 is a feature request, not a bug, so "reproducing" means confirming the gap the issue describes:
1. Run the app locally (or visit www.drumbeatrepo.com)
2. Open the genre/pattern selector
3. Observe available genres: Rock, Samba, Funk, etc.
4. Confirm Bossa Nova is not present as a selectable genre
5. Expected per issue #538: Bossa Nova should be a selectable genre with an authentic pattern (reference: https://shedrums.de/bossa-nova-drum-beat/)
6. Confirmed consistent by checking both the live site and the local build, and by searching the codebase for an existing Bossa Nova file (none found)


### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/maevainnocent/DrumBeatRepo/commit/a8a6e1d98570a5b133c9aa5e017a9c406b45b222
- **Screenshots/logs:** maevainnocent@Maevas-MacBook-Air DrumBeatRepo % git log -1 > commit-log.txt
maevainnocent@Maevas-MacBook-Air DrumBeatRepo % cat commit-log.txt
commit a8a6e1d98570a5b133c9aa5e017a9c406b45b222
Author: maevalabelle <maevainnocent8@gmail.com>
Date:   Thu Sep 24 17:11:32 2026 -0400

    docs: document reproduction of missing Bossa Nova genre (#538)
- **My findings:** Confirmed the app's genre system is entirely data-driven (no hardcoded enum or type list), which meant "missing" simply meant no JSON file and no metadata entry existed yet. Also learned how to commit and log changes through the terminal.
---

## Solution Approach

### Analysis

The root "cause" isn't a bug in logic — it's simply that no one has contributed Bossa Nova pattern data yet. The app's genre list is driven entirely by what's registered in `beats-metadata.json`, so any genre not listed there (and lacking a corresponding pattern file) won't appear, regardless of frontend code.

### Proposed Solution

Add a new Bossa Nova pattern file modeled on the reference tutorial's rhythm (syncopated/tresillo-style kick, steady eighth-note ride, rim-click/clave), register it in the metadata manifest, and add a test confirming the new metadata decodes correctly.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** The app offers a fixed set of drum genres/patterns to select and play. Bossa Nova is missing. The issue asks for it to be added as a new genre with an authentic pattern.

**Match:** Samba, drum and bass, and dancehall are structurally similar existing genres — `dancehall/standard.json` was used as the closest template for the new pattern's data shape.

**Plan:** [Step-by-step implementation plan]
1. Add `frontend/src/assets/beats/bossa-nova/bossa-nova.json` defining kick, ride, and rim-click tracks
2. Register the new pattern in `frontend/src/assets/beats/beats-metadata.json`
3. Add a Scala test in `BeatMetadataCirceSpec.scala` verifying the new metadata decodes correctly via Circe
4. Verify the Angular frontend picks up the new genre automatically via the shared, data-driven genre list

**Implement:** https://github.com/Babali42/DrumBeatRepo/pull/589/commits

**Review:** Checked `CONTRIBUTING.md` for Scala style and commit message conventions before opening the PR.

**Evaluate:** Added a Scala test asserting the new pattern's metadata decodes correctly; ran `sbt testFull` (27/27 passing); manually confirmed Bossa Nova appears and plays correctly in the running web.

---

## Testing Strategy

### Unit Tests

- [x ] Test case 1: Circe decoding test: valid Bossa Nova metadata JSON decodes into the correct BeatMetadata case class
- [x ] Test case 2: Ran full existing suite (`sbt testFull`) to confirm no regressions — 27/27 tests passed
- [ ] Test case 3:  Manually inspected the generated pattern data against the reference tutorial's rhythm to confirm kick, ride, and rim-click placements matched the intended groove

### Integration Tests

- [ ] no integration test layer exists for this change; covered by the manual test below
- [ ] Integration scenario 2

### Manual Testing

Ran `sbt fastLinkJS` to rebuild the engine, then `npm run start` in the frontend. Verified in the browser at localhost:4200 that Bossa Nova appears in the genre selector and plays the expected pattern correctly.
---

## Implementation Notes

### Week 5 Progress

Implemented the Bossa Nova genre end-to-end. Since this app is fully data-driven for genres (no hardcoded enum or type list), the fix came down to three pieces:
Added frontend/src/assets/beats/bossa-nova/bossa-nova.json, a new pattern using a syncopated kick (tresillo-style), steady eighth-note ride, and rim click — the core rhythmic signature of bossa nova — reusing existing audio samples from techno/.
Registered the new pattern in frontend/src/assets/beats/beats-metadata.json.
Added a Scala test in BeatMetadataCirceSpec.scala verifying the new metadata decodes correctly via Circe.
Before writing any code, I checked frontend/src/types/engine.d.ts, frontend/src/app/domain/beat.ts, and the i18n files (en.json) to see whether genre names needed to be added anywhere else (a TypeScript union type or a translation map). They didn't — genre is typed as a plain string everywhere, and genre names aren't localized. This confirmed the app's genre system is fully data-driven, which simplified my original UMPIRE plan (I had assumed I'd need to add a case to a Scala enum, which doesn't exist).

### Week 6 Progress

Opened PR #589 against `main`. Addressing any review feedback as it comes in (see Maintainer Feedback below).

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
- [09/29/2026]: [I couldn't open your branch in Codespaces like I usually do because I've run out of credits, but from what I could see, it looks really good!
I think this might even be the first Scala contribution to the project. 😊
I'd be interested in learning more about how you worked on it. Could you tell me a bit about your setup and workflow? For example:
How did you set up your environment?
Did you use a local git clone, GitHub Codespaces, or something else?
Was anything difficult or confusing during the onboarding process?
I'm trying to provide the best possible contributor experience for OSS contributors, so any feedback on what worked well or what could be improved would be greatly appreciated.]
- [10/01/2026]: [Thanks so much! I used a local git clone on my Mac rather than Codespaces — ran sbt fastLinkJS in the engine folder and npm run start in frontend, following the Quick Start in the README. Setup itself was pretty smooth; the trickiest part was just git workflow hiccups on my end (I accidentally nested a second clone inside engine/ early on, nothing to do with your docs!). The README was clear enough to get both the Scala engine and Angular frontend running without issues. Happy to share more detail if it'd help you refine the onboarding docs!]

**Status:** [Approved]

---

## Learnings & Reflections

### Technical Skills Gained

[Learned how to read and extend a Circe-based Scala data model, how this project's data-driven genre system avoids hardcoded enums entirely, and how to validate a JSON asset change with a corresponding decoding test rather than relying only on manual UI checks. Also got comfortable with basic git workflow from the terminal (branching, committing, logging).]

### Challenges Overcome

[The trickiest part wasn't the code itself but the local environment setup — particularly getting `sbt` installed on macOS without triggering a from-source `openjdk` build that failed due to Xcode/Command Line Tools issues. Working around that with `brew install sbt --ignore-dependencies` (since I already had a working JDK) unblocked everything else.]

### What I'd Do Differently Next Time

[I'd check the codebase's existing patterns (like confirming genres are data-driven) *before* writing my initial UMPIRE plan, rather than after — I originally assumed I'd need to touch a Scala enum that didn't actually exist, which I only discovered once I started implementing.]

---

## Resources Used

- [Bossa Nova rhythm reference: https://shedrums.de/bossa-nova-drum-beat/]
- [Project's `CONTRIBUTING.md` for Scala style and commit conventions]
- [Circe documentation for JSON decoding in Scala]
