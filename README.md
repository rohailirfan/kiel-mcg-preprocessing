# Kiel MCG Preprocessing

A small Python/Jupyter reproduction of the Kiel Cardio Database's documented MCG preprocessing, using **one real raw/processed pair: subject 1, trial 01**. The notebook has saved outputs and five comparison figures. It implements the stated preprocessing stages, but **does not reproduce the original output exactly** because important acquisition and filter settings are unspecified.

## Start here

- `replicate_v1.ipynb`: self-contained analysis, explanations, code and saved outputs.
- `requirements.txt`: pinned analysis and notebook dependencies.
- `figures/`: five PNG plots, including raw, comparison, alignment, filter/edge and spectrum views.
- `data/`: exactly four original WFDB files: `.hea` and `.dat` for the selected raw and supplied processed recording.
- `metadata/`: original release README, license, record list, subject metadata and published checksums.
- `metrics.csv`, `results.json`, `data_manifest.json`: machine-readable scores, settings and provenance.

About **43.2 MB** of original files are included, rather than the full 6.8 GB database. The first listed pair was selected before calculating results. All eight channels are processed; channel 0 (sensor 0, −Y) is the plotted example.

## Local setup

Use Python 3.10–3.12; the verified run used **Python 3.12.14** on macOS. In a terminal, enter the extracted project folder, then run:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m jupyter lab replicate_v1.ipynb
```

On Windows, activate with `.venv\Scripts\activate`. In Jupyter, select the environment's Python kernel and choose **Restart Kernel and Run All Cells**. Starting Jupyter from the project folder keeps figures and results beside the notebook. With the included data, analysis needs no network access. A CPU runtime is sufficient; channels are processed one at a time to limit memory use.

To execute without opening the editor:

```bash
python - <<'PYTHON'
from pathlib import Path
import nbformat
from nbclient import NotebookClient
path = Path("replicate_v1.ipynb").resolve()
notebook = nbformat.read(path, as_version=4)
NotebookClient(notebook, timeout=600, kernel_name="python3",
               resources={"metadata": {"path": str(path.parent)}}).execute()
nbformat.write(notebook, path)
PYTHON
```

The computation uses NumPy 2.2.6, SciPy 1.15.3, Matplotlib 3.10.3 and WFDB 4.3.0. Pandas 2.2.3 is pinned as a WFDB dependency: an unpinned Pandas 3 installation caused a WFDB 4.3.0 import failure during setup. No application, backend, or model training is involved.

## Google Colab

1. Open [Google Colab](https://colab.research.google.com/) and upload `replicate_v1.ipynb` through **File → Upload notebook**.
2. Use a fresh Python CPU runtime, then choose **Runtime → Run all**. The first code cell installs the five pinned analysis packages before analysis imports. The pinned analysis packages have Python 3.13 Linux wheels as well as support for the locally tested Python 3.12.
3. Only the notebook is required: it fetches the fixed list of original files, about 43.2 MB, and verifies their SHA-256 checksums. No Drive mount or PhysioNet account is needed. Some connections may take several minutes.
4. Download `figures/`, `metrics.csv`, `results.json`, and the executed notebook from Colab's Files pane before the runtime is discarded.

If access or checksum verification fails, execution stops with the real URL and error. It never creates substitute recordings. Retry after resolving access; valid cached files are reused. The original download allowlist is embedded in the notebook so the Colab upload needs no helper Python module or requirements file.

**Verification scope:** the notebook was run from start to finish locally, with saved outputs. A separate local run exercises the Colab installation branch and starts with no cached data. A hosted Google Colab runtime was not used; this is not a claim of a remotely verified Colab session.

## Source, license and inspection

Source: [Kiel Cardio Database v1.0.0](https://physionet.org/content/kiel-cardio/1.0.0/), including its [README](https://physionet.org/files/kiel-cardio/1.0.0/README.md), [file listing](https://physionet.org/files/kiel-cardio/1.0.0/), headers, metadata, license and checksum file. Inspected on **2026-10-30**.

Citation: Engelhardt, E., Frey, N., & Schmidt, G. (2023). *Kiel Cardio Database* (version 1.0.0). PhysioNet. RRID:SCR_007345. [doi:10.13026/q6r2-zz68](https://doi.org/10.13026/q6r2-zz68).

The source page also requests the platform citation: Pollard, T., et al. (2026). *PhysioNet as a global platform for biomedical research*. Nature Health. [doi:10.1038/s44360-026-00096-z](https://doi.org/10.1038/s44360-026-00096-z).

The source data are **CC BY-SA 4.0**; the full original license is in `metadata/LICENSE.txt`. The notebook's original analysis, explanations and derived figures are also made available under CC BY-SA 4.0. Changes consist of Python reimplementation, conditional physical scaling, filtering, resampling, plots and agreement calculations. Original source files remain unchanged and are attributed above.

No original v1.0.0 preprocessing implementation was located in the inspected release or linked sites. The search covered the [continuous database page](https://biomagnetic-sensing.de/index.php/data-bases/datasets/kiel-cardio-database), [KiRAT](https://kirat.de/), its public download pages, and [CRC repositories](https://biomagnetic-sensing.de/index.php/data-bases/repositories). Some KiRAT subpages timed out. The official [2022 MCG Quality Classes code](https://github.com/CRC1261/BMT-2022-MCG-Quality-Classes) filters synthetic signals and is not evidence of this release's processing settings. The notebook does not present that code as the original pipeline.

## Method and assumptions

The source documents scaling by OPM sensitivity, 100 Hz low-pass, 1 Hz high-pass and 50 Hz band-stop filtering. Before comparing signals, the notebook verifies channel names/order, eight-channel layout, units, ADC calibration, header comments, missing values and matching record names. Each selected original file is pinned by SHA-256; the four WFDB checksums are also checked against the preserved upstream list.

| Item | Evidence or explicit assumption |
|---|---|
| Stored calibration | WFDB physical value = (digital sample − baseline) / ADC gain. Raw units are V; supplied units are pT. This file scaling is applied once. |
| Channel order | Headers match: sensor 0 −Y/Z; sensor 1 −Y/Z; sensor 2 −Y/X; sensor 3 −Y/X. No reordering or polarity fitting. |
| Sampling discrepancy | Both headers declare 200 Hz, but raw has 3,574,784 samples versus 13,964 supplied: exactly 256:1. Literal raw duration is 17,873.92 s. |
| Raw clock | **Assume 51,200 Hz**, giving 69.82 s, matching supplied duration. A 50 Hz spectral peak and aligned cardiac features support this interpretation; acquisition metadata do not confirm it. Headers are preserved unchanged. |
| Sensor sensitivity | **Assume 2.7 V/nT**, the [manufacturer's default](https://quspin.com/products-zf-opm/qzfm-command-list/). Other selectable settings exist and the session setting is unknown. Conversion is V × 1000 / 2.7 → pT. No fitted amplitude correction. |
| Filter design | At assumed raw rate: Butterworth low-pass 100 Hz, order 4; high-pass 1 Hz, order 4; band-stop 49–51 Hz, order 2 prototype (4th-order band-stop). Family, orders and stop width are assumptions. |
| Phase/edges | Concatenated SOS in source-listed order; forward/backward `sosfiltfilt`, odd reflection, 5-second padding. Zero phase is assumed. Forward/backward filtering squares magnitude; nominal Butterworth cutoffs are about −6 dB overall. |
| Rate conversion | After filtering, `resample_poly` downsamples by 256, Kaiser β=5, constant padding. Its antialias response adds attenuation near 100 Hz. Original resampling is unspecified. |
| Alignment | No absolute start timestamps. Check integer lags ±0.5 s using temporary 5–30 Hz views, plus early/late windows; report zero-lag and aligned scores. These views only estimate timing, not RMSE. |
| Scoring | Pearson correlation and RMSE in pT; both contributing signals exclude at least five seconds at each boundary. Also report a stricter ten-second trim and the combined impulse tail. |
| Spectra | Welch PSD: 4-second Hann windows, 50% overlap, constant detrending, density units pT²/Hz; 0.25 Hz bins at both rates. Raw PSD uses the full assumed native rate. |

A 100 Hz digital low-pass cannot be designed directly at 200 Hz: it is the Nyquist limit. The notebook therefore applies it before the assumed rate conversion and clearly discloses the unresolved raw-clock issue.

## Results of the saved run

Every channel's estimated lag was **zero samples**, including early 10–30 s and late 35–55 s checks. Channel 0 was scored on **5.00–64.82 s**, containing **11,964 samples**, after excluding five seconds from both ends.

| Channel | Correlation, zero lag and aligned | RMSE (pT), zero lag and aligned | RMSE with 10 s trim (pT) |
|---|---:|---:|---:|
| 0 | 0.76104 | 1.47604 | 1.36157 |
| 1 | 0.83611 | 1.67216 | 1.53616 |
| 2 | 0.70742 | 1.99140 | 1.85952 |
| 3 | 0.61692 | 2.38326 | 2.17705 |
| 4 | 0.54749 | 2.22823 | 2.13321 |
| 5 | 0.69983 | 2.84987 | 2.64817 |
| 6 | 0.74050 | 1.93464 | 1.81604 |
| 7 | 0.77248 | 2.60732 | 2.38554 |

**This is a partial match.** Lower-frequency cardiac features line up, but the reproduced output retains much more energy near **100 Hz**, while the supplied spectrum rolls off more strongly above about **80 Hz**. The notch profiles also differ. Unknown original low-pass/rate-conversion details could explain these differences, but the exact cause is not established. Filter choices were not tuned to maximize the scores.

Channel 0 integrated **49–51 Hz** power was **1426.76 pT² raw**, **0.000365405 pT² reproduced**, and **0.000590783 pT² supplied**. Relative to raw, these are **-65.92 dB** and **-63.83 dB**. This demonstrates strong mains-band suppression, not superior physiological fidelity; the band can contain useful signal too. The supplied-to-raw comparison also inherits the unconfirmed raw sensitivity.

The peak normalized combined impulse response beyond ±5 s was **4.84e-08** for our implementation. Channel 0 RMSE with ten-second trimming was **1.36157 pT**. The shorter scoring interval contains different data, so that change cannot by itself be attributed to edge artifacts.

![Signal comparison](figures/04_signal_comparison.png)

![Frequency spectra](figures/05_spectra.png)

## What was reproduced, and what remains approximate

**Completed:** real pair acquisition and verification; header/calibration/channel checks; explicitly conditional volts-to-pT scaling; the three documented filter stages; rate conversion; raw and shared-window comparison plots; spectra and 50 Hz assessment; alignment and edge checks; all-channel correlation/RMSE; executable notebook with outputs.

**Unresolved:** authoritative raw sample rate, actual sensor gain, original filter coefficients/order/bandwidth, phase/padding and decimation settings. The next step toward exact reproduction is obtaining those original acquisition/processing settings from the authors, not choosing undocumented settings solely to improve a score.

## Limitations and interpretation

- These data come from healthy volunteers; only one subject/trial is examined. There is no disease-detection, heart-attack-prediction, wearable-validation, or clinical-accuracy claim.
- Correlation describes waveform co-variation; RMSE describes physical-amplitude disagreement under the stated gain assumption. A correlation of 0.761 is not “76.1% accuracy.” The supplied recording is a reference output, not physiological ground truth.
- Alignment is reference-assisted and uses the same data as the descriptive metrics. There is no held-out validation. Integer lag resolution is 5 ms; no gain, offset, polarity or time-warp fitting is performed.
- A wrong raw clock would change the interpretation of every cutoff and time axis. The duration, mains line and waveform alignment are supporting evidence, not independent acquisition confirmation.
- Both exports use packed 12-bit WFDB format 212. Quantization and original export scaling can contribute to differences. Export rail counts do not establish sensor saturation.
- Filter-edge checks concern this implementation; unknown original transients may remain. Absolute numerical identity across different software/runtime versions is not guaranteed, so the notebook records package versions and pins the analysis dependencies.

## A short explanation to present

“I took one real raw and processed MCG recording from the Kiel Cardio Database. I checked its units, channel order and timing before filtering. The raw and processed files had different sample counts, so I stated a sampling-rate assumption instead of hiding the mismatch. Then I applied the documented high-pass, low-pass and mains-band filtering, compared the signals and spectra, and measured their agreement away from the boundaries. The cardiac timing agrees and the 50 Hz interference is strongly reduced, but the result is approximate because the original filter and acquisition settings are incomplete. This is a preprocessing reproduction, not a clinical validation.”
