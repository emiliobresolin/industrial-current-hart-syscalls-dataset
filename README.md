# Industrial Current, HART and Syscall Dataset

Version: 2026-11-04. Author: Emilio Bresolin.

Paired instrumental observations and Linux syscall sequences for studying
supervisory consistency and replay. Instrumental signals originate from an
emulated compressor and instruments; syscall names were captured during Linux
execution with strace. The four instruments represent temperature, pressure,
rotational speed and mass flow. The mass-flow instrument provides HART
Totalizer 1 in kilograms.

## Files

| File | Records | Contents |
|---|---:|---|
| `data/acquisitions.jsonl` | 1,380 | Current reference observations, recorded HART fields and acquisition context |
| `data/supervisory_views.jsonl` | 1,200 | Paired current or historical supervisory observations |
| `data/syscalls.jsonl` | 1,380 | Syscall-name sequences associated with acquisition intervals |

UTF-8 JSON Lines: each line is one JSON object. Join files using
`acquisition_id`. Syscall sequences contain 1,498,037 entries in total.

## Fields

- `acquisition_id`: unique identifier of the current acquisition.
- `profile`: `unmodulated` or `modulated`; 690 acquisitions per profile.
- `phase`: `train`, `calibration`, `settle`, `source`, `stable` or `transition`.
  Per profile, these contain 120, 60, 60, 150, 150 and 150 acquisitions.
- `block`, `offset`: episode/block number and position within its phase segment.
- `timestamp_utc`: observation timestamp. `acquisition_start_unix_s` and
  `acquisition_end_unix_s`: actual capture interval, in Unix seconds.
- `currents_ma`: recorded current for `temperature`, `pressure`, `rpm` and
  `mass_flow`, in mA. `totalizer1_kg`: cumulative HART mass totalizer, in kg.
- `hart_observations`: instrument fields as recorded: PV, optional SV, current,
  percentage of range and diagnostics. Values include their original units.
- `view_id`: unique supervisory-view identifier. `condition` and `is_replay`
  describe the view. `supervisory_source_acquisition_id` identifies the
  acquisition supplying its instrumental values.
- `tokens`: ordered syscall names whose entry timestamps fall within the
  acquisition interval: start inclusive, end exclusive. The capture covers
  the shared acquisition process and its threads. Individual token timestamps
  and separate per-instrument traces are not included.

## Conditions and use

The two profiles belong to one collection session, with five evaluation blocks
per profile. The nominal instrumental step is five seconds; use recorded Unix
intervals for syscall association. Phase/episode boundaries identify separate
contexts for temporal preprocessing.

The four conditions are `normal_stable`, `legitimate_transition`,
`replay_same_regime` and `replay_hiding_change`, each with 150 views per profile.
Each stable or transition acquisition has two alternative supervisory views:
current values and a historical normal sequence from the preceding reserved
source block. Replay is applied to the logical supervisory view used for
evaluation. Reference observations remain current. Paired views share the
same acquisition and syscall sequence; they are dependent observations.
Build temporal features separately within each partition, profile and episode.

Public files retain recorded values, labels and joins. Model outputs, execution
paths and application-specific metadata have been omitted from this data-only
edition. This release is a fixed snapshot; later collections may have their
own versions.

## Citation and reuse

Suggested citation: Bresolin, Emilio. *Industrial Current, HART and Syscall
Dataset*. Version 2026-11-04.

Copyright 2026 Emilio Bresolin. Licensed under Creative Commons Attribution
4.0 International (CC BY 4.0): https://creativecommons.org/licenses/by/4.0/
