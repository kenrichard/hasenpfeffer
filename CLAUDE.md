# Hasenpfeffer

The Pennsylvania Dutch double-deck euchre game: you and a partner against two computer players,
with bidding, hoss / double hoss, bowers, a kitty, hints, and three difficulty levels.

Started in Claude Duet and moved here on 2026-11-22. The source was the claude.ai artifact
https://claude.ai/artifact/SBwdpEKMBHpjePLvpWzVon (version of 2026-11-21).

## Files

- `index.html`: the whole game in one page. The first `<script>` is the engine (pure game logic
  and computer players); it exports through `module.exports` when loaded in Node, so a server or
  tests can reuse it. The second script is the browser client.

## Deck

Ken's family plays with **only J, Q, K, A, from four decks**: 64 cards, four of every card,
15 each, 15 tricks, 4 left over as the kitty, game to 64. That is the default (`deck: 'J4'`,
`target: 64`). No low cards: don't switch the default back to 7–A. Settings also offer 64 cards
from two decks of 7–A, 48 cards (9–A, 12 each) and 32 cards (J–A from two decks, 8 each).

The number of decks is `copies` in the `DECKS` table in `derive()`. The computer players' bidding
thresholds are set per deck there too (`ai: { hoss, dhoss, safety }`). The J4 and 32-card numbers came from simulating games: on the 32-card deck the original numbers made computers
hoss on about 60% of hands and fail most of them, so scores sank and games never ended. If you
change bidding or the deck, rerun a simulation that plays full games and check that they finish.

## Words

The family calls the bowers **bars** (right bar, left bar). Use "bar" in everything the player
sees; the code keeps the `bowers` setting name.

## Tip and keeping count

Ken wants the game to train him to keep track of the right bars, the left bars, trumps played and
who has shown out of a suit. Two parts, both in the solo controller:

- **Tip** (header button) is a one-off "help me with this decision", not a mode: it shows the
  hint for the current choice and clears when you act. Tips used are counted.
- **Keep-count questions** pause the game and ask how many right bars / left bars / trumps are
  still out, or who has shown out of a suit. They come right after a trick with a bar, a
  trump-in or a trump lead, or when it's your lead (if you won the trick, the question waits for
  your lead). At most one per trick; how often is the `memory` setting. A miss shows the
  count-down (start, minus your hand, minus each trick), because counting down is the habit
  being taught. "Still out" means not yet seen by you (`HP.unseen`). The engine records who
  played each card (`playSeat`, `playTrick`) and when each player showed out (`voidAt`).
- **Count** (button under the table, next to Tip) opens a screen with what's still out and the
  count-down that got there. The game pauses while it's open. Count checks are counted, so Ken can
  watch himself need it less.
- **Practice counting** (`focus` setting) limits questions and the Count screen to one thing at a
  time: right bars only, right and left bars (default), bars and trumps, or everything including
  who's out of a suit. Ken asked to start with the bars.
- The results screen shows "Keeping count: N of M questions right, C count checks, K tips" and the
  last-10-hands rate (`hp.memstats` in localStorage).

## Hand review

At the end of each hand, "Review my hand" lists the choices where you differed from the tutor
(which plays like the Normal computer). The solo controller records each of your decisions with
the tutor's pick (`g.log`; card plays keep a snapshot of the game). For card plays,
`HP.compareCards` plays both cards against 60 sampled deals of the unseen cards and the review
lists the play only if the tutor's card did clearly better: at least 0.25 tricks, or 8 points
better odds of making the bid (or setting the other team). Bids, trump and discards show the
tutor's reasoning with no score. Solo only: online games run on the server.

## Deploy

Live at https://hasenpfeffer.vercel.app (Vercel team "Ken Richard's projects", project
`hasenpfeffer`). The project is connected to the GitHub repo, so every push to `main` deploys to
production. No build step: Vercel serves the files as they are.

## State

- `window.HP_STANDALONE = true` puts the page in solo-only mode. Open `index.html` in a browser
  to play. Saved games and settings live in localStorage (`hp.game`, `hp.settings`, `hp.level`,
  `hp.hints`).
- The two-player table mode (`OnlineController`) connects over WebSocket to `/ws` on the same
  host. That server is not in this folder yet: it lived in Claude Duet and has to be brought over
  or rewritten before two-player play works.
