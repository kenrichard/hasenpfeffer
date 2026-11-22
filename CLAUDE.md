# Hasenpfeffer

The Pennsylvania Dutch double-deck euchre game: you and a partner against two computer players,
with bidding, hoss / double hoss, bowers, a kitty, hints, and three difficulty levels.

Started in Claude Duet and moved here on 2026-11-22. The source was the claude.ai artifact
https://claude.ai/artifact/SBwdpEKMBHpjePLvpWzVon (version of 2026-11-21).

## Files

- `index.html`: the whole game in one page. The first `<script>` is the engine (pure game logic
  and computer players); it exports through `module.exports` when loaded in Node, so a server or
  tests can reuse it. The second script is the browser client.

## State

- `window.HP_STANDALONE = true` puts the page in solo-only mode. Open `index.html` in a browser
  to play. Saved games and settings live in localStorage (`hp.game`, `hp.settings`, `hp.level`,
  `hp.hints`).
- The two-player table mode (`OnlineController`) connects over WebSocket to `/ws` on the same
  host. That server is not in this folder yet: it lived in Claude Duet and has to be brought over
  or rewritten before two-player play works.
