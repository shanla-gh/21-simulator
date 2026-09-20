# Assets

Screenshots for the 21-simulator product page.

Every image here was captured from the **published build** — the archive on the
releases page, extracted and run — rather than from a development tree, so what
the product page shows is what a download actually looks like. Recapture them
whenever the interface changes in a way a reader would notice, and take the new
images from the published build of that release.

## What is here

| File | Shows | State captured |
| --- | --- | --- |
| `screenshots/action-comparison.png` | Every legal action with EV, cost against the best one, win / push / loss, and the decision margin | Hard 16 vs 10, six decks |
| `screenshots/mathematical-trace.png` | The derivation behind the recommendation, from the shoe through to the self-checks | The same state, "Show full mathematics" opened |
| `screenshots/research-view.png` | Policies scored against the Oracle: agreement, mean EV regret, regret by decision band | Research view, read from published artifacts |
| `screenshots/live-reconstruction.png` | Rebuilding a round one observation at a time | Live Reconstruction, before the first card |
| `screenshots/phone.png` | The action comparison on a narrow screen | Hard 16 vs 10 at 412 px wide |

## Not captured yet

| File | Would show |
| --- | --- |
| `screenshots/payoff-distribution.png` | The payoff distribution of each action, from the upgraded mathematics |
| `screenshots/same-count.png` | Two shoes with the same count and different best actions — the soft 18 vs 4 example |
| `demo/live-reconstruction.gif` | Live Reconstruction in motion, 10–20 s of one short round |

The README embeds only files that exist, so there are no broken images.

## Guidelines

- Capture the real application, not mock-ups. Do not retouch numbers.
- Capture from the published build of the release being documented.
- Use a light, uncluttered window; crop to the application.
- Show states with visible **exact** / **estimated** labels where relevant.
- Avoid anything that suggests card prediction, betting advice or winnings.
- Keep images reasonably small: PNG under ~500 KB, GIF under ~5 MB.
- Use lowercase, hyphenated filenames.

## Embedding

```markdown
![Every legal action compared](assets/screenshots/action-comparison.png)
```

Alt text describes the state and what the image shows, not the filename: the
page is read by people who cannot see the picture too.
