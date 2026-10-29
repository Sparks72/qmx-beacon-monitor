# Comparing antennas with the QMX Beacon Monitor

The Beacon Monitor can compare two antennas. It labels every result with the antenna in use, works out the SNR difference between them beacon by beacon, and shows a waterfall you can save as a picture.

## What's new

- **Antenna in use**: a name box at the top of the Waterfall panel. Every result is saved with the name that was in use when its slot began.
- **Waterfall**: the last 3 minutes of audio around your CW pitch, with each beacon slot labelled and its SNR written above it. It can be paused and saved as a PNG.
- **Antenna comparison**: a table per beacon and band, with an overall difference in dB between two antennas, and its own CSV export.
- **CSV export**: the main log has a new last column, `antenna`.
- **Now panel**: the "tone near pitch" bar also shows its value in dB.

## How to run a comparison

1. Start audio, connect CAT if you use it, and choose **Stay on one band**. With this setting, each of the 18 beacons comes round every 3 minutes, so both antennas hear the same beacons.
2. Type a name under **Antenna in use**, for example `Loop`, and press Enter.
3. Start monitoring and let it run for at least one full 3-minute cycle.
4. Swap the antenna, type its name, for example `2.5 m wire`, and press Enter.
5. Swap back and forth every 3 minutes or so, a few times over. Conditions change from minute to minute, so alternating is much fairer than one long run on each antenna.
6. Read the result under **Antenna comparison**. Choose the two antennas and the period to look at, from the last hour to any time.

When you change the name, the slot in progress is skipped and stacking starts again. The moment of swapping counts for neither antenna, and no stacked result mixes the two. In the waterfall, a skipped slot is marked `swap`.

## Reading the comparison

The summary line reads like this:

> **Loop** is **3.1 dB better than** **2.5 m wire**: the average over 16 beacons heard on both (give or take 0.4 dB).
> Heard: Loop 43 of 55 tries (78%), 2.5 m wire 30 of 67 (45%), on the 19 beacon/band pairs both have tried.

- **The dB figure** is the average of the per-beacon differences. It uses only beacons heard on both antennas, and compares each beacon with itself. A strong beacon that only one antenna happened to catch does not sway it.
- **Give or take** is the standard error of that average. When it is small next to the difference, the difference is real. With fewer than 3 beacons, the page says there are too few to go by.
- **Heard** counts how often each antenna caught a beacon out of its tries. It counts only beacon/band pairs that both antennas have tried. It shows what the SNR alone can miss: an antenna that hears more beacons, even weak ones, is doing better.
- **SNR** is that of the 100 W dash in 2.5 kHz, the same figure as in the grid, the Catches list and WSJT-X.
- In the table, green means the second antenna did better on that beacon, and red means worse. A `–` means the beacon was not heard on one of the antennas.

**Export comparison CSV** saves the table. Columns: `beacon, band_m, antenna_a, a_heard, a_tries, a_snr_db, antenna_b, b_heard, b_tries, b_snr_db, b_minus_a_db`. The last row holds the overall average.

## The waterfall

- It holds 180 seconds, one full beacon cycle, with the newest audio on the right.
- **Width**: ±250 Hz around the pitch, in 4 Hz strips, or ±1000 Hz in 8 Hz strips. The wider view also shows other signals near the beacon.
- The colour is **dB above the noise**, from −3 to +30 dB. The noise floor is followed over a few seconds. An antenna that brings up the noise as much as the signal therefore looks no better, just as its SNR is no better.
- Above each 10-second slot is the beacon due and, once analysed, its SNR. A `–` means not heard, and `…` means not yet analysed.
- The strip along the bottom shows which antenna was in use, with times in UTC every 30 seconds.
- **Pause** holds the picture while monitoring carries on. **Save picture** saves a PNG named after the antenna and time, for example `beacon-waterfall-Loop-2026-10-29-14-32-05.png`. The date, time, locator, band and antenna are written on the picture.

## Good to know

- The NCDXF/IARU beacons transmit on 20, 17, 15, 12 and 10 m only. The comparison tells you how the antennas do on those bands, not on 80, 40 or 30 m.
- Keep the **CLIP** light off. A clipped slot is less reliable and is never used for stacking.
- RF gain and AGC change the level of signal and noise together, so they hardly change the SNR. Leave them alone during a comparison anyway.
- Results saved before this version have no antenna name. They stay in the log, but they don't appear in the comparison.
