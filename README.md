# QMX Beacon Monitor

A browser tool that listens to the **NCDXF/IARU International Beacon Project** with a QRP Labs **QMX**, tunes the radio by CAT, and shows which of the 18 beacons you hear on 20, 17, 15, 12 and 10 m, and how strongly. It works down into the noise: a beacon too weak for one 10-second transmission can still be found by **stacking** several of its cycles.

One HTML file, nothing to install. Open it in Chrome or Edge.

---

## What it does

- **Follows the beacon schedule.** 18 beacons, 5 bands, a new transmission every 10 seconds, the full cycle every 3 minutes. The display shows which beacon is on air on each band right now.
- **Tunes the QMX for you** over USB CAT (Web Serial). It sets the VFO the radio is actually receiving on (A or B), switches to CW, reads the frequency back to confirm it, and checks again every slot. A result is only recorded while the radio is confirmed on the beacon frequency.
- **Detects each beacon in the audio**, without decoding Morse letter by letter: see *How a beacon counts as heard* below.
- **Stacks weak beacons.** If a slot gives no catch, it is combined with the same beacon's last 2–8 cycles on that band, and the same tests are run on the result (shown as **×N**).
- **Propagation grid.** Each beacon × band cell keeps its **strongest catch** until you reset it, coloured by strength:

  | Colour | Meaning |
  |---|---|
  | Red **–** | not heard |
  | Pink | below −18 dB |
  | Orange | −18 to −11 dB |
  | Green | above −11 dB |

  The number is the SNR of the 100 W dash in 2.5 kHz, the same scale as WSJT-X and WSPR. Click a cell for details: time, weakest dash heard (100 W / 10 W / 1 W / 0.1 W), tone, letters confirmed, how often heard, latest try.
- **Map.** A world map with the night side shaded, or centred on you with bearing and distance. Shows the best of all bands or one band.
- **Catches list and CSV export.** Every beacon heard, newest first, plus a full log for spreadsheets.
- **Levels panel.**
  - A level meter for the audio arriving at the PC, with a **CLIP** light and a warning in the header.
  - Volume and RF gain controls for the QMX, with ±1 dB buttons, and a readout of how much the AGC is turning the gain down.
- **Clock check.** It shows where the callsigns start relative to the slot and suggests a correction if your PC clock is off. Nothing changes until you click it.
- **CAT log** of every command sent and every reply, for troubleshooting.
- Light and dark theme.

## Requirements

- QRP Labs **QMX** connected by USB, giving the USB sound card and the CAT serial port.
- **Chrome or Edge** on a PC. Web Serial and Web Audio are needed. A recent Firefox has also been seen working on Linux; older Firefox versions have no Web Serial.
- An accurate PC clock. Windows: *Settings → Time & language → Sync now*. If WSJT-X shows DT near 0, the clock is fine.

CAT is optional. Without it, tune the radio yourself and use **Stay on one band**.

## Getting started

1. Open `qmx-beacon-monitor.html` in Chrome or Edge.
2. **Start audio** and pick the QMX's audio input.
3. **Connect CAT** and pick the QMX's COM port. Close WSJT-X or any other program using that port first: Windows lets only one program open it.
4. Optional: **Detect bands** reads the QMX's band configuration (read-only) and ticks the beacon bands your radio covers.
5. Enter your **locator**, and set **CW pitch** to the same value as the QMX's CW pitch (sidetone). The detector searches ±200 Hz around it, so a pitch or calibration a little off still works.
6. Set **Volume** so the level meter stays out of the red and the CLIP light stays off. On the QMX, Volume also sets the level sent to the PC.
7. Choose a mode, then **Start monitoring**:
   - **Stay on one band:** each beacon comes round every 3 minutes. This is the best choice for stacking.
   - **Sweep ticked bands:** each band in turn, for a full round of all 18 beacons. A visit takes 3 min 10 s: the extra 10 s slot is used for retuning, so no beacon is skipped.

The first stacked results need a few cycles, so give it 10–15 minutes.

## How a beacon counts as heard

Each beacon sends its callsign at 22 WPM and 100 W, then four 1-second dashes at 100 W, 10 W, 1 W and 0.1 W. The schedule says which beacon to expect, so the program **checks for that callsign** rather than decoding Morse. It slides the callsign's on/off pattern across the slot, at every tone within ±200 Hz of the pitch and every start time from 0.5 s early to 0.7 s late. Using the whole callsign at once is far more sensitive than copying it letter by letter.

A slot counts as a catch only if all of these hold:

1. The expected callsign fits well, and **clearly better than all 17 other callsigns** over the same audio. Noise, clicks, birdies and other people's CW fit a wrong callsign just as well, so they fail here.
2. At least **two of its letters** are confirmed on their own.
3. The gap after the callsign is quiet, and a **100 W dash** follows that is about as strong as the callsign. It must be neither far weaker nor far stronger: callsign and first dash are sent at the same power.
4. The **power then steps down**. A steady carrier doesn't.

The 10 W, 1 W and 0.1 W dashes are then counted to give the weakest dash heard.

Two more things keep noise out:

- **Steady tones** (birdies, PC whistles) are found and removed before listening.
- **Wideband bursts** (clicks, AGC pumping) are divided out.

**Stacking:** each slot is kept as a compact copy, about 44 KB. A slot that gives no catch is lined up with the same beacon's earlier slots on that band, by time from the slot start and by tone, and their energies are averaged. The beacon is in the same place every cycle; the noise isn't. All the tests above are then run on the average, with the thresholds scaled for the number of cycles. One slot with someone else's strong signal can't carry a stack.

## How sensitive is it?

From simulated steady beacons in noise, with and without pops and clicks:

| Beacon SNR (2.5 kHz) | Caught in one slot | Caught by stacking |
|---|---|---|
| −14 dB | over 90% | |
| −16 dB | about 80% | |
| −18 dB | about 30% | |
| −20 dB | a few % | 97% within 4 cycles |
| −22 dB | 0% | about 90% within 8 cycles |
| −24 dB | 0% | about 10% within 8 cycles |

Fading beacons gain less, but still clearly.

**Strong beacons** are caught too, including after the receiver's AGC has turned the gain down to hold the level steady. In simulation, every beacon from −10 to +30 dB passed through an AGC was caught. Only the very loudest, around +30 dB with a very fast AGC or driven far into clipping, can still be missed, so keep the CLIP light off.

In the same tests, noise, clicks, AGC pumping, birdies, other CW and wrong beacons produced no false catches in thousands of slots and stacks. Real bands are rougher than simulated noise, so treat a single catch right at the limit with some caution. A catch you can trust repeats, and shows the same tone offset as other catches on that band.

**On the air:** with an indoor random wire, ZS6DN (Pretoria, 9,100 km) was caught on 17 m at **−23.2 dB from 8 stacked cycles**, with a steady tone across repeated catches.

## The log (CSV)

**Export CSV** writes every slot analysed:

| Column | Contents |
|---|---|
| `utc` | Slot time, UTC |
| `band_m` | Band, metres |
| `freq_mhz` | Beacon frequency, MHz |
| `beacon` | Callsign |
| `location` | Beacon location |
| `heard` | 1 = heard, 0 = not heard |
| `weakest_dash` | Weakest dash heard (100W / 10W / 1W / 0.1W) |
| `snr_db_2500hz` | SNR of the 100 W dash in 2.5 kHz |
| `onset_s` | Callsign start, seconds from the slot start |
| `tone_hz` | Tone, Hz from your CW pitch |
| `score` | Callsign score |
| `best_other_callsign`, `its_score` | Best rival callsign and its score |
| `reason` | Why a slot was not counted |
| `other_signal` | 1 = another signal was on the frequency |
| `clipped_samples` | Samples at or above −1 dBFS in the slot |
| `stacked_cycles` | Cycles stacked for this catch |
| `stacked_reason` | Why a stack was not counted |

## Tips and troubleshooting

- **Nothing heard at all?** Listen at 14.100 by ear, or check WSJT-X on 14.074. If it decodes few stations, the aerial is the limit, not the software. A few metres of wire outside makes a big difference.
- **Radio not on the beacon frequency?** The CAT pill shows the frequency the QMX reports. If it says "QMX on …, not …", check that band in the QMX's band configuration (**Detect bands** shows them).
- **CLIP light or "Audio CLIPPING":** turn Volume down. Slots recorded while clipping are marked and are not used for stacking.
- **All catches show a similar tone offset**, e.g. −60 Hz: set the monitor's CW pitch to match the QMX's. If the offset grows with frequency, the QMX's frequency calibration is off.
- **Clock:** callsigns are looked for from 0.5 s early to 0.7 s late, so keep the PC clock synced.
- **Reset grid** empties the grid but keeps the log and Catches list. **Clear results** deletes everything.

## Privacy

Everything runs in your browser. Audio, results and settings stay on your PC (browser storage). Nothing is sent anywhere.

## Credits

- Beacons: [NCDXF/IARU International Beacon Project](https://www.ncdxf.org/beacon/), run by the Northern California DX Foundation and the IARU.
- Map land outlines: Natural Earth (public domain).
- Radio: [QRP Labs QMX](https://qrp-labs.com/qmx.html).

## Author

Paul Harrison, DJ0CU / G4ADF.

## Licence

*(add your licence here)*
