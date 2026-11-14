# Smidge

The daily game of getting close. Five gut calls a day: cut a line, count a flash of dots, stop a hidden clock, open an angle, pour to a level, find a balance point, match a length, line two things up. Each call scores 0 to 100, the day's score is the average, and a curve shows where you land against everyone else.

Play: https://sauv2616-hub.github.io/smidge/

## How it works

- Everything is in `index.html`. No build step, no dependencies, no sound or image files (sounds are synthesised in the browser).
- Each day's five challenges come from a seeded random generator keyed to the day number, so every player gets the same five. Smidge #1 is 14 November 2026.
- Progress, streaks and stats are saved in the player's browser (local storage). One go per day per device.
- A round's score is `100 * exp(-error / tau)`; each challenge has its own `tau` in the `CH` table, which is the one number to change if a challenge turns out too harsh or too easy.

## The curve

The "sharper than X% of players" figure and the curve blend two things:

1. **Live counts.** When someone finishes, the page adds 1 to an anonymous counter for their 5-point score band on the free [Abacus](https://abacus.jasoncameron.dev) counting API, then reads the day's 20 bands back.
2. **A baseline model.** A simulated crowd of 8,000 players, so the curve is sensible before many real people have played. It counts as 40 players (`CONFIG.priorWeight`), so real scores take over as the day fills up.

The caption under the curve always says which of the two the player is looking at. Abacus is a free hobby service with a limit of 30 requests per 10 seconds per visitor; if it is slow or down, the game falls back to the baseline on its own. Once the game has real traffic, move the counters to something you control (a Cloudflare Worker or Supabase table) by changing `Curve.submit` and `Curve.fetch`. Set `CONFIG.live` to `false` to turn live counts off.

To see how many people played on a given day, add up the 20 counters for that day, for example `https://abacus.jasoncameron.dev/get/smidge-daily/d0001-b17` (day 1, band 85 to 90).

## Settings

All in the `CONFIG` block near the top of the script in `index.html`: launch date, site URL used in share links, live counts on or off, and the two AdSense values.

## Turning on ads

1. Buy a domain and point it at GitHub Pages (repo Settings, Pages, Custom domain). Ad networks generally will not approve a `github.io` address.
2. Update `CONFIG.siteUrl` and the `og:image` URL in `index.html` to the new domain.
3. Apply to Google AdSense with the domain. When approved, put the `ads.txt` file AdSense gives you in this folder.
4. Create one display ad unit, then fill in `CONFIG.adsenseClient` and `CONFIG.adsenseSlotResult`. The ad appears under the results, never during play.
5. Update `privacy.html` to name the ad provider, and turn on AdSense's consent message for visitors in Europe and the UK.
