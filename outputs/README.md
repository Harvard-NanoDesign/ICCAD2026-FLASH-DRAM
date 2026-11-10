# CarbonEDA outputs

Everything in this folder is output from [CarbonEDA](https://github.com/Harvard-NanoDesign/CarbonEDA) (v0, commit `310762a`), run on the process flows in `../technologies/dram/` and `../technologies/flash/` with the configuration in `../config/`. It is copied unchanged from CarbonEDA's `outputs/dram/` and `outputs/flash/` folders.

Each technology has two folders:

| Path | Contents |
| --- | --- |
| `[dram/flash]/<tech>/carbon/` | Carbon results from `make carbon`: `steps.csv` (EPW, MPW, and GPW per process step), `gases.csv` (per gas and step), `by_module.csv`, `by_area.csv`, and `totals.csv`. All values are per 300 mm wafer, with the unit in each column name. |
| `[dram/flash]/<tech>/report/` | `<tech>_design_review.pdf` from `make all`: one page per process step with its 3D structure from the Sentaurus Process Explorer simulation, cross-sections, mask, materials, and EPW, MPW, and GPW, plus summaries by module and process area. |

The technologies are described in `../technologies/README.md`.
