# Smidge

The daily baking game you play by feel. Four bakes a day, drawn from a pool of eight. Best on a phone. On a computer, or if motion access is refused, the motion games switch to mouse and touch versions, and the home screen says the phone version is better.

Motion games (use the phone's tilt and shake sensors): Olive oil (tip past sideways to pour), Oven dial (turn the phone like a knob), Whisk (shake in time with a beat), Flour (tip and shake like a bag), Level the cake (hold the phone flat).
Touch games: Cut the dough, Oven timer. Proof the dough is lift-to-raise on a phone and stop-the-rise with touch.

Play: https://sauv2616-hub.github.io/smidge/

## How it works

- Everything is in `index.html`. No build step, no dependencies, no sound or image files (sounds are synthesised in the browser).
- Each day's four bakes come from a seeded random generator keyed to the day number, so every player gets the same four, always with at least one motion game and one touch game. Smidge #1 is 14 November 2026.
- Progress, streaks and stats are saved in the player's browser (local storage). One go per day per device.
- A round's score is `100 * exp(-error / tau)`; each game has its own `tau` in the `CH` table, which is the one number to change if a game turns out too harsh or too easy. Flow rates for oil and flour are in `CH.pour` and `CH.flour`.
- Motion needs HTTPS and, on iPhone, the player tapping Allow when asked. The `Sense` object reads the sensors.

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
