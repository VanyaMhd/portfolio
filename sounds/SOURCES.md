# Sound asset sources

## Why these aren't downloaded Foley recordings

This environment has no general internet access — verified directly, twice,
across two rounds of this request (DNS lookups and HTTPS requests to
Pixabay, Freesound, and plain test domains all fail from the sandbox).
There is no way to download and embed a third-party audio file here, no
matter how well-matched it might be.

Rather than leave the interactions silent, all six files are **synthesized
from scratch** (Python + NumPy for the sound design, FFmpeg/LAME for MP3
encoding) — not sampled, sliced, or derived from any existing recording.
Because nothing is copied from anywhere, there's no license to track and no
attribution owed.

This second pass rebuilds the sound design with more physically-grounded
techniques than the first attempt, aimed specifically at the "quiet, warm,
analog desk" brief:

- **Karplus-Strong plucked-string synthesis** for the vinyl music, instead
  of plain sine chords — this is the same basic algorithm real guitar/piano
  physical-modeling synths use, and reads as a plucked instrument rather
  than an electronic pad.
- **Resonant "body" layers** mixed under the paper/envelope/folder textures
  (a short decaying tone at a material-appropriate frequency) so each one
  has a bit of the object's character, not just filtered noise.
- **A proper vinyl startup sequence** — a soft motor engage, crackle fading
  in, a subtle needle-drop tick, then a settled crackle bed — instead of a
  single burst of pops.
- **Material differentiation**: paper (thin, bright, 1.4–5kHz-weighted),
  envelope (thicker cardstock — lower flap + a paper "peel" transient),
  and folder (cardboard — the lowest, longest, softest of the three, with
  a descending-frequency friction sweep) are now deliberately distinct from
  each other rather than three versions of the same rustle.

**A bug fixed this round:** the envelope and archive-folder click handlers
were each playing their dedicated file *and then also* layering the
generic `paper-rustle.mp3` on top a moment later. That's very likely why
they kept sounding "the same generic paper sound" no matter how the
dedicated files were improved — the shared sound was quietly muddying both
of them every time. `envelope-open.mp3` and `folder-open.mp3` are now
complete, self-contained sequences with the whole flap→slide→settle
action baked in at the right relative timing, and the code no longer
layers `paper-rustle.mp3` on top of either one. `paper-rustle.mp3` itself
is untouched and still available for the journal/general paper cue if you
add an interaction there later.

The slide portion of both — the part meant to be most audible — now uses a
denser, continuous micro-grain friction model (roughly 200–240 short grains
per second with a rise-then-fall envelope) instead of the sparser
crinkle-style grains from earlier rounds, aiming for something closer to
an actual "shhhk" than a series of little pops.

**A scope note on round 5 (bookshelf + journal):** neither of these had a
real interaction to attach sound to before this round. Only 3 of the many
"book" elements on the shelf were ever clickable (CivicNature, Suli Bakery,
PAW — they're project-jump links; the skill-tag books are decorative divs
with no click behavior), and the journal spread had no open/closed state
or page-turning at all — both pages are always shown at once. Sound was
added to the 3 real book links as-is, no new animation. For the journal,
a small "settle" micro-animation (a barely-there scale/rotate, ~0.6s,
respecting `prefers-reduced-motion`) was added specifically so
`journal-open.mp3` and `page-turn.mp3` would have something real to
sync to — this was a deliberate scope decision, confirmed before building.

**Round 6:** `vinyl-music.mp3` was recomposed from scratch in a bossa nova /
café style — same synthesis toolkit (Karplus-Strong plucked strings), new
arrangement: a 12-bar loop over a G–Em–Am–D progression, guitar "shell"
chords (root + 5th only, no full triads, for an open, uncluttered sound)
played on the classic 3-3-2 bossa clave rhythm, alternating root/fifth bass
on beats 1 and 3, and a few sparse high notes for sparkle.

**Round 7:** after hearing a few short style previews side by side (lo-fi
piano, acoustic guitar fingerstyle, music box/bells, and the round-6 bossa
nova), you picked the music box. `vinyl-music.mp3` was rebuilt again as a
delicate music-box melody: a C major pentatonic (C-D-E-G-A) tune with a
gentle up-down-up-down phrase shape, played on high-brightness Karplus-Strong
strings (brightness 0.86) with a short bell-like "ting" transient layered on
each note's attack, a very soft low pad underneath for warmth, and the same
light vinyl hiss bed used throughout. Bossa nova and every earlier version
have been fully replaced by this one. All other files (crackle, typewriter,
envelope, folder, book-pull, journal-open, page-turn, paper-rustle) are
untouched.

**Round 15 — `vinyl-stop.mp3` removed.** A needle-lift/spin-down cue was added
in round 14 and then taken back out per request; pausing the vinyl is silent
again. The file and its wiring (audio tag, volume entry, play call) are gone.
`book-tick.mp3` — a 55 ms dry wooden tick played on hovering a shelf book,
throttled to one per 55 ms — is the only sound added since round 13.

**Round 14 — `vinyl-music.mp3` is a REAL piano recording, processed to sound pressed on vinyl.**

Source: `storegraphic-soft-peaceful-piano-melody-309269.mp3` (Pixabay, ID 309269),
supplied by you. **Confirm the licence before publishing.** Replaces the
synthesised music-box loop, archived as `vinyl-music-synth-archived.mp3`.

The source is clean, quiet digital piano. Seven stages turn it into a record:

1. **Seamless loop** — the piece ended 6 dB louder than it began, so looping it
   would have thumped every 22 s. The tail is crossfaded over the head across
   2 s: 22.07 s → 20.07 s, head and tail now within 1.6 dB.
2. **Wow and flutter** — turntable speed error. Slow drift (0.42 % at 0.72 Hz),
   fast flutter (0.09 % at 7.3 Hz) and a bounded random walk, applied as a
   time-varying read position. This is what stops it sounding digital.
3. **Band-limited** — high-passed at 70 Hz, low-passed at 10.5 kHz with soft
   skirts. Deep bass and air are gone: 0.003 % of energy now sits below 70 Hz
   and 0.165 % above 10.5 kHz.
4. **Bass mono-ed** — the side channel is high-passed at 150 Hz. A real lacquer
   cannot track wide low frequencies without the stylus jumping.
5. **Stereo narrowed** — side level at 68 %. Correlation rose 0.535 → 0.701.
6. **Soft saturation** — tanh at 1.30 drive.
7. **Surface noise** — hiss bed (900 Hz–7 kHz) with a slow 0.13 Hz wander so the
   floor breathes, plus ~34 crackles and ~1.1 larger pops per second with
   heavy-tailed amplitudes, decorrelated across channels so it surrounds the
   music rather than sitting inside it.

RMS-matched to the loop it replaced (within 0.8 dB), so **playback volume stays
0.26**. Two alternative intensities are kept in `../vinyl-music-options/`:
`vinyl-light.mp3` (subtle) and `vinyl-heavy.mp3` (worn, noisy).

**Round 13 — `folder-close.mp3` added: the archive drawer now closes properly.**

Source: `freesound_community-cutlery-drawer-closing-01-86279.mp3` (Freesound
community #86279, via Pixabay). **Confirm the licence before publishing.**

This is a *new* sound name, not a replacement — previously closing an archive
folder played `paper-rustle`, so the drawer slid open and then paper rustled
shut. Open and close are now a matched pair from the same drawer.

The source is unusually quiet (peak −28 dBFS, mono) and had roughly 0.4 s of
near-silence between the slide and the final latch click, which would have felt
like a stall. Segments 0.00–0.90 s and 1.30–1.74 s were joined with a 50 ms
crossfade to close that gap: 1.87 s → 1.29 s, slide straight into latch. Gain
+9.5× to RMS-match `folder-open` (landed within 0.1 dB, peak still only 0.368,
so there is plenty of headroom left). Playback volume **0.10**, same as the
opening.

`paper-rustle.mp3` is now used by the **CV envelope closing only**.

**Round 12 — `paper-rustle.mp3` (the closing sound) is now a REAL recording.**

Cut from the same source as `envelope-open.mp3` (Freesound community #81855),
deliberately from a *different part* of it — the recording runs 0.21–1.17 s and
the open uses the whole gesture, while the close uses only **0.58–1.17 s**: the
firm hit near 0.90 s and its decay. Using the identical clip for both would
have made opening and closing sound like the same event replayed.

It is also slowed **6 %** (resampled, so it drops slightly in pitch too),
which reads as the heavier, more settled motion of a flap dropping shut.
0.63 s long, 5 ms fade-in, 100 ms fade-out, RMS-matched to the synth it
replaced.

At the matched level it measured 7.5 dB below the envelope opening — close to
inaudible in context — so its playback volume went **0.10 → 0.16**. Final
balance, relative to the envelope opening: close −3.4 dB, archive drawer
−2.0 dB, book pull −3.1 dB. A close being a little softer than an open is
correct; being 7.5 dB softer was not.

This file plays on **both** the envelope closing and an archive folder closing
(lines calling `play('paper-rustle')`). Previous synth version kept as
`paper-rustle-synth-archived.mp3`.

**Round 11 — `folder-open.mp3` (the archive folders) is now a REAL recording.**

Source: `jarasnat-open-the-drawer-sound-227896.mp3`, supplied by you (Pixabay,
ID 227896). **Confirm the licence on the source page before publishing.**

The original is 4.63 s of continuous drawer movement containing about ten
separate onsets — far too long and too busy for a click response. Three 1.4–1.5 s
excerpts were cut and level-matched; the one used is the segment at
**2.80–4.30 s**, chosen because it's the only one that ends in a real settle
(its tail falls 27 dB below its peak, versus 17 dB for the other two, which
just cut off mid-slide). The rejected excerpts are kept in
`../archive-sound-options/` if you'd rather swap one in.

Processing: 6 ms fade-in, 55 ms fade-out, RMS level-matched to the synthesised
file it replaced (landed within 0.1 dB, so **its playback volume stays at
0.10** — no code change needed, unlike the envelope). Resampled 48 kHz →
44.1 kHz for consistency. No EQ or compression. Previous synth version kept as
`folder-open-synth-archived.mp3`.

Note it is a *drawer*, not a paper folder — which suits the archive section
well, but it now differs in material from `paper-rustle.mp3`, still used when
a folder is closed.

**Round 10 — `envelope-open.mp3` is now a REAL recording, not synthesis.**

After three synthesis attempts (filtered noise, then a snap-through buckling
model) none of them read as paper, so this one is a genuine field recording
you supplied: `freesound_community-paper-rustle-81855.mp3`, a Freesound
community upload (ID 81855) obtained via Pixabay. **Confirm the licence terms
on the source page before publishing** — I could not verify them from here.

Processing applied (nothing else — no EQ, no compression, transients intact):

- Trimmed 0.23 s of dead silence off the head, plus the dead tail: 1.20 s → 0.97 s
- Gain +1.41×, peak-limited to 0.88 (RMS-matching wanted +3.1× but that would
  have clipped — a real recording has a far higher crest factor than the synth)
- 4 ms fade-in, 30 ms fade-out so it can't click
- Re-encoded 24 kHz → 44.1 kHz stereo MP3 for consistency with the other files

Because the peak clamp stopped it reaching the old file's RMS, it measured
6.5 dB quieter than the synth it replaced, so its playback volume was raised
**0.11 → 0.23** to sit at the same perceived level as the folder and book
sounds. The previous synthesised version is kept as
`envelope-open-synth-archived.mp3` if you ever want to compare or revert.

**Round 9 — two playback bugs fixed (no audio files changed):**

1. *First click on any sound was silently dropped.* `play()` rewound the clip
   (`currentTime = 0`) **before** calling `play()`. With `preload="none"` the
   element has no buffered data yet, and rewinding an unloaded element throws
   `InvalidStateError` in several browsers — that throw happened before
   `play()` was reached and got swallowed by the surrounding `try/catch`. The
   seek is now guarded on `readyState`, and a failed seek can no longer block
   playback. Short clips are also warmed into memory on first use.

2. *Envelope and folder were silent until the vinyl had been played.* Every
   one-shot sound was gated behind an `enabled` flag that only ever became
   true inside the vinyl's click handler — a leftover from the removed global
   sound toggle. Opening the CV envelope or an archive folder is itself the
   user gesture browsers require, so those sounds no longer wait for the
   vinyl. **Only the looping `vinyl-music.mp3` still waits to be asked for**;
   nothing auto-plays on page load.

This is still synthesis, not a field recording — it will not sound
identical to a real Foley library. If that gap still matters once you hear
it in context, the fastest path to something more authentic is for you (or
someone with browser/download access) to grab real files from one of the
sources below and drop them in with the same filenames — no code changes
needed.

## Files

| Filename | Description | Duration | Notes |
|---|---|---|---|
| `vinyl-crackle.mp3` | Turntable startup: soft motor engage → crackle fades in → subtle needle-drop tick → settling crackle bed | ~2.5s | Plays once when the vinyl is clicked to start. **Unchanged since round 2.** |
| `vinyl-music.mp3` | **Replaced (round 7).** Music box / bells: a C major pentatonic (C-D-E-G-A) melody with a gentle rise-and-fall shape, bright high-brightness Karplus-Strong plucked tone with a short bell "ting" on each note's attack, a very soft low pad underneath, and a light vinyl hiss bed | ~28.8s, loops | Chosen after comparing 4 short style previews (lo-fi piano, acoustic guitar, music box, bossa nova) side by side — replaces the round-6 bossa nova version entirely |
| `typewriter-key.mp3` | One soft key press: brief metallic ring, muted clack, light low thock | ~0.18s | Unchanged since round 2 |
| `paper-rustle.mp3` | Thin, bright, close-mic page-turn texture | ~1.1s | Used for the journal/general paper cue. Unchanged since round 2 |
| `envelope-open.mp3` | **Rebuilt (round 4).** Soft flap crease → the CV paper sliding out (a continuous "shhhk" friction texture, thin and bright — this is the dominant element) → a couple of quiet settle grains as the sheet clears the envelope | ~1.4s | This is now one complete, self-contained Foley sequence — see note below on the layering bug this round fixed |
| `folder-open.mp3` | **Rebuilt (round 4).** Light cardstock folder flex → the printed poster/artwork sliding out (a continuous "shhhk," thicker and lower than the envelope's CV slide, and slightly longer since it's a bigger sheet) → a soft placement thud as it settles | ~1.4s | Same self-contained-sequence treatment as the envelope; deliberately pitched lower/longer than the CV slide so the two feel like different objects |
| `book-pull.mp3` | **New (round 5).** Subtle contact against the shelf lip → sliding friction against the cover/neighboring books (cloth/cardboard texture, not paper) → a barely-there low settle | ~1.0s | Plays on the 3 clickable project books (CivicNature, Suli Bakery, PAW) only — the decorative skill-tag books have no click behavior and stay silent |
| `journal-open.mp3` / `page-turn.mp3` | **Removed (round 8).** The journal sound was taken back out per request — the click interaction and its little "settle" animation are still there, they just don't play audio anymore. Files are left in this folder unused in case you want them back later. |

## Volume hierarchy (set in the site's `SoundSystem`)

| Sound | Level |
|---|---|
| Vinyl music | 0.26 |
| Vinyl crackle / needle | 0.13 |
| Typewriter key | 0.14 |
| Paper rustle | 0.10 |
| Envelope open | 0.11 |
| Folder open | 0.10 |

## License

100% original, generated for this project. No attribution required, no
restrictions on use, modification, or redistribution.

## If you want real field recordings later

Search terms that should get better results than generic queries, on
sources with clear commercial-use licensing (check the license on the
specific asset page, not just the site's general policy):

- Pixabay Sound Effects (pixabay.com/sound-effects) — "vintage typewriter
  single key press," "vinyl record needle drop subtle," "soft paper page
  turn foley," "envelope opening paper foley," "cardboard folder opening
  foley"
- Freesound.org — filter by CC0 or CC-BY, check attribution requirements
  per file
- Zapsplat.com — free tier requires attribution or an account; paid tier
  doesn't

Keep the filenames identical and drop them into this folder — nothing else
needs to change.

## Round 16 — light switch (user-supplied recordings)

Two real switch recordings supplied by Vanya, wired to the light-bulb theme toggle.

| file | source | processing |
|---|---|---|
| `light-on.mp3` | `dragon-studio-light-switch-382712.mp3` | trimmed 0.045–0.300s to the click; brighter of the two (5849 Hz centroid), so it takes the "on" direction; RMS-matched to `typewriter-key`, peak clamped to 0.85; 3 ms in / 45 ms out |
| `light-off.mp3` | `freesound_community-light-switch-off-86314.mp3` | mono source upmixed to stereo at 44.1 kHz; trimmed 0.310–0.610s; same RMS target and clamp; 3 ms in / 45 ms out |

Playback volumes are deliberately unequal — `light-on` .16, `light-off` .26. Both files
clamp at the same 0.85 peak, but the off recording has a higher crest factor, so at equal
volume it reads about 4 dB quieter. The gain difference makes them land the same.

Fired from `apply()` in the dark-mode block, before the flick animation starts, and only
when `animate` is true — so restoring a saved theme on page load stays silent.
