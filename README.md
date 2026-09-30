# QMX Beacon Monitor

**Open it:** https://sparks72.github.io/qmx-beacon-monitor/

A propagation monitor for the NCDXF/IARU International Beacon Project, running
in a web page. It follows the beacon schedule, tunes a QRP Labs QMX over CAT,
listens to every 10-second transmission and records which beacons you hear and
how far down their power steps you can follow them. The results fill a grid of
18 beacons × 5 bands and a map centred on your location.

There is nothing to install. It works from GitHub Pages or straight from disk:
download `index.html` and double-click it.

## What you need

- Chrome or Edge on a desktop computer (Web Serial is needed for CAT).
- A QMX (or other rig) with its USB audio connected to the PC. CAT is
  optional: without it, tune the rig yourself and use **Stay on one band**.
- Your Maidenhead locator, for bearings, distances and the map.

## Using it

1. **Start audio** and choose the rig's USB audio input.
2. **Connect CAT** (optional), then **Detect radio bands**. This reads the QMX's
   band table, read-only, and ticks the beacon bands your radio covers.
3. Enter **your locator**.
4. Choose a mode:
   - **Sweep ticked bands**: three minutes on each band, so every beacon is
     heard once per band.
   - **Stay on one band**: follow all 18 beacons on a single frequency.
5. Check the **CW pitch** matches your rig's CW tone (700 Hz by default).
6. **Start monitoring.**

After a few strong beacons the **timing check** shows whether your PC clock
agrees with the beacons (they are GPS-timed). If it doesn't, **Apply
suggestion** corrects it.

Over CAT the monitor only sets the frequency (`FA`) and CW mode (`MD3;`). It
never transmits and never changes any menu setting.

## Reading the grid

Each beacon sends its callsign and a dash at 100 W, then dashes at 10 W, 1 W
and 0.1 W. Every cell shows the weakest dash heard:

| Colour | Heard down to | Path margin, roughly |
|---|---|---|
| red | 100 W | just open |
| orange | 10 W | 10 dB in hand |
| yellow-green | 1 W | 20 dB |
| green | 0.1 W | 30 dB |
| grey `–` | not heard | |
| grey `QRM` | interference on the frequency | |

The number is the SNR of the 100 W dash in a 2.5 kHz bandwidth, the way WSJT-X
and WSPR report SNR. Hover over a cell for details, including how far the tone
was from your pitch. Results older than 15 minutes are shown faded.

**Export CSV** saves every result, with time, band, beacon, weakest dash, SNR,
timing, tone offset and detection score. Results are kept in the browser
between sessions.

## How it detects weak beacons

The detector listens the way an experienced operator does. It knows from the
schedule which beacon must be transmitting in each slot, so it looks for that
beacon's own callsign at 22 WPM followed by its one-second dash. It measures in
narrow bandwidths: the length of a dit for the callsign, and 4 Hz for each
dash, where a simple tone filter would use about 70 Hz. It searches ±100 Hz
around your pitch, so a small frequency error in the rig or the beacon doesn't
lose the signal.

As it runs, it learns from the strong beacons:
- your clock offset (all beacons start on the same GPS tick);
- your rig's tone offset on each band;
- the spacing of the dashes.

It then searches only close to those values, which lets it pick out weaker
beacons and ignore more interference.

Before counting a beacon as heard, it checks that the frequency is quiet before
and after the transmission, that the callsign's gaps are quiet, and that the
power steps down after the 100 W dash. A carrier or another station's CW fails
these checks and is shown as **QRM**.

### Steady tones (birdies)

A steady tone near the pitch, such as a receiver birdie or a whistle from the
PC or USB lead, is on all the time at one strength, and is far narrower than a
beacon, which keys its callsign and steps its power down 30 dB within its
10 seconds. Left alone, such a tone makes beacons look like **QRM**, and with
noise on top it can even pass for a very weak beacon.

The monitor looks for these tones in every slot. A tone seen at the same audio
frequency in 3 slots is taken out of the audio before listening. Only about
±0.3 Hz around it is removed, so a beacon even 2 Hz away is heard normally.
The tones being removed are listed under the timing check. A tone that goes
away is forgotten after 3 minutes.

The analysis runs in a background worker, taking under a second per slot, so
the page stays responsive.

## Tested performance

Simulated beacons with random timing and tone offsets, SNR in 2.5 kHz, compared
with the previous version:

| Signal | Previous version | This version |
|---|---|---|
| −8 dB | 83% heard | 100% |
| −12 dB | 0% | 100% |
| −16 dB | 0% | 100% |
| −18 dB | 0% | 97% |
| −20 dB | 0% | 80–83% |
| −22 dB | 0% | about 40% |
| −10 dB with fading (0.5 Hz QSB) | 13% | 100% |

- **False detections:**
  - noise alone: none in 500 slots;
  - a steady carrier on the frequency: none;
  - strong, continuous CW on the frequency: 3 in 40 slots before the timing was learned, none after.
- **Accuracy:** SNR within about 1–2 dB; timing within 10 ms.

These are simulations. Real-world reports are very welcome, especially the CSV
export from a session where it reported something you disagree with.

## Changes in this version

- Steady tones (birdies, PC whistles) are found and removed before listening,
  so they no longer cause false QRM or false weak detections (see above).
  Tested on a real recording with a 706 Hz whistle from the PC/USB side: QRM
  verdicts caused by the whistle disappeared. On simulated beacons with a
  whistle like that 6 Hz away, beacons at −16 to −18 dB went from heard in
  about 1 slot in 6 back to about 9 in 10, the same as with no whistle. No
  simulated beacon, from −16 to +30 dB, was ever mistaken for a steady tone.

## Earlier changes

- New detector: about 12 dB more sensitive, with interference rejection
  (see above).
- SNR is now given in 2.5 kHz, so it reads about 15 dB lower than before.
  Saved results are converted automatically.
- Tone offset shown for each result, and included in the CSV.
- VE8AT moved to its new site at Inuvik, NT.

## Disclaimer

Experimental software, provided as is, without warranty of any kind. To the
extent permitted by law, no liability is accepted for any loss or damage from
its use. Not affiliated with the NCDXF, the IARU or QRP Labs.

## Credits

The International Beacon Project is run by the Northern California DX
Foundation (NCDXF) with the IARU: https://www.ncdxf.org/beacon/
