# FIK-4 Kiruna

Firmware and data-analysis notebooks for **FIK-4**, a cosmic-radiation measurement carried by a stratospheric balloon launched from Esrange (Kiruna, Sweden) on **5 September 2019** as part of the **HEMERA** balloon programme. The experiment was led by group at the Nuclear Physics Institute of the Czech Academy of Sciences (NPI CAS).

The payload measured radiation with three independent detectors, so that count rates and dose could be compared across the whole ascent. The main scientific topic is the altitude profile of the radiation field, in particular the **Regener–Pfotzer maximum**.

## Flight

| | |
|---|---|
| Name | FIK-4 (also "Fik 4"), part of HEMERA 2019 |
| Launch site | Esrange Space Center, Kiruna, Sweden |
| Date | 5 September 2019 |
| Balloon | Airstar 150Z |
| Follow-up flights | FIK-5 and FIK-6 (Czechia), see the publication below |

Other experiments on the same flight (e.g. the SOCRAT SPACEDOS unit and a Belgian G-M counter) are used in the notebooks as reference data.

## Instruments

| ID in code | Instrument | Notes |
|---|---|---|
| `CF` | AIRDOS-C | CRY-19 scintillator read out by a SiPM, records energy spectra |
| `FF` | SPACEDOS02A | semiconductor dosimeter / spectrometer with RTC |
| `GM` | Geiger–Müller counter | G-M tube with an AIRDOS logger; also logs GNSS position, temperature, humidity and pressure |
| – | SPACEDOS01A (SOCRAT) | second SPACEDOS unit from the SOCRAT experiment, reference data |
| – | G-M counter (BE) | independent G-M counter from the Belgian experiment |

All three firmware variants run on an ATmega1284P ("Mighty 1284p") board, log to an SD card and write a text `DATALOG.TXT`.

## Repository layout

```
sw/
  AIRDOS_CF/   firmware for AIRDOS-C (scintillator + SiPM), with GNSS time sync
  AIRDOS_FF/   firmware for SPACEDOS02A (AIRDOS with RTC, low-power)
  GM/          firmware for the G-M counter logger
notebooks/
  fik.py, fik2.py       parsers for DATALOG.TXT (fik2 is the newer, simpler API)
  Flight*.ipynb         analysis of the flight data
  Preflight.ipynb       pre-flight tests
  CalibCF.ipynb         thermal calibration of AIRDOS-C
  SpectraDemos.ipynb    examples of working with the logged spectra
  SPACEDOS_HEMERA.ipynb SPACEDOS flux and dose calculation
  Publication.ipynb     figures prepared for the publication
  hemera_upload/        self-contained sample for the HEMERA data portal
```

### Firmware (`sw/`)

Arduino sketches for the Mighty 1284p core. Each sketch has the pinout in its header comment, and writes its firmware ID and git hash into the first line of every log (`$AIRDOS,<FW>,<hash>,...`). Build with the Arduino IDE; the bundled `RTCx` library lives in each sketch's `src/` folder.

### Notebooks (`notebooks/`)

- Start with **`hemera_upload/Parsing Sample.ipynb`**. It is self-contained (it defines its own parsing functions) and shows how to read the three log files and plot the whole flight.
- `fik2.py` provides `read_airdos_cf_log`, `read_airdos_ff_log` and `read_airdos_gm_log`. Each returns pandas data frames (navigation data from `$GPRMC`, measurements from `$CANDY` records for the spectrometers). Pass `mergeruns=True` to merge multiple power-on sessions into one series.
- `fik.py` is the older `DATALOG.split_runs` API used by the earlier notebooks (`Flight.ipynb`, `FlightCF.ipynb`, `Preflight.ipynb`, ...).

Most notebooks read data from hard-coded paths such as `/storage/experiments/2019/09_HEMERA/FLIGHT/{GM,CF,FF}/DATALOG.TXT`. The raw logs are not stored in this repository, so change `logs_dir` or the file paths to your own copy of the data. The HEMERA sample notebook uses the file names `DATALOG-GM.TXT`, `DATALOG-AIRDOS_C.TXT` and `DATALOG-SPACEDOS.TXT`.

Python dependencies: `numpy`, `pandas`, `matplotlib`, `jupyter`; the map plots additionally need `cartopy` (some older notebooks use `basemap`).

## Publication

If you use this code or data, please cite:

> Ambrožová I., Kákona M., Dvořák R., Kákona J., Lužová M., Povišer M., Sommer M., Velychko O., Ploc O. *Latitudinal effect on the position of Regener–Pfotzer maximum investigated by balloon flight HEMERA 2019 in Sweden and balloon flights FIK in Czechia.* Radiation Protection Dosimetry 199(15–16), 2041–2046 (2023). https://doi.org/10.1093/rpd/ncac299

Figure 1 of the paper shows count rates versus altitude from the G–M tube, AIRDOS-C and SPACEDOS, i.e. the data processed in this repository.

## Further references

- Publication record in ASEP (Czech Academy of Sciences repository): record 0577601, <https://hdl.handle.net/11104/0577601>
- [SSC (Swedish Space Corporation) – HEMERA flight page](https://sscspace.com/hemera) (flight listed as "Fik 4")
- [StratoCat – HEMERA-1, 5 September 2019](https://stratocat.com.ar/fichas-e/2019/KRN-20190905.htm)

