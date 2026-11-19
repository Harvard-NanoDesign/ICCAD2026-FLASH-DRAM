# Technologies

The [CarbonEDA](https://github.com/Harvard-NanoDesign/CarbonEDA) technology inputs for the DRAM and 3D NAND Flash technologies. Copy `dram/` or `flash/` into CarbonEDA's `technologies/` folder to run them (see [Reproducing the results](../README.md#reproducing-the-results)).

## 3D NAND Flash
| Folder | Technology |
| --- | --- |
| `flash/baseline/` | Baseline technology, based on SK Hynix 176-layer 3D NAND Flash [1]|
| `flash/#L/` | Direction 1: Increasing number of memory layers (32-1024), in stacks of up to 150 layers each |
| `flash/cryo_#L/` | Direction 2: Increasing number of memory layers (32-1024), in stacks of up to 220 layers each, using cryogenic etching to define charge-trap transistor channels [2] |
| `flash/176L_mo_wl/` | Direction 3: Advancing wordline metal (tungsten --> molybdenum) for lower resistivity [3]|
| `flash/176L_dwb/` | Direction 4: FinFET CMOS periphery bonded to 3D NAND Flash memory array using dual wafer bonding (DWB) |

## DRAM
| Folder | Technology |
| --- | --- |
| `dram/BG1/` | Baseline technology, based on SK Hynix 1a DRAM [4] |
| `dram/BG2/` | Additional EUV patterning and wordline/bitline pitch tightening |
| `dram/VG1/` | Vertical gate (VG) DRAM |
| `dram/VG2/` | VG DRAM with DWB periphery |
| `dram/VG3/` | VG DRAM with DWB periphery and molybdenum wordline |
| `dram/3D1/` | 3D DRAM implementation through fabricating VG DRAM cells in the vertical direction with horizontal capacitors |
| `dram/3D2/` | 3D1 using cryogenic etching to etch through Si/SiGe bilayers |
| `dram/IGZO_AMPCELL/` | IGZO amplifier-cell DRAM: four IGZO transistor tiers, each with a MIM capacitor, built monolithically over 7 nm FinFET CMOS |


Each folder holds three files:

| File | Contents |
| --- | --- |
| `<tech>_final.csv` | The process flow, one row per module header (`row_type` = `module`) or process step (`row_type` = `step`). `module`, `step`, and `step_type` name the step, and `carboneda_process_area` is the process area that sets its tool energy and gases in `../config/carbon_config.yaml`. The remaining columns (`MaterialFilter`, `Thickness`, `Rate`, `Time`, `BondTopStep`, `Flip`, …) are the Sentaurus Process Explorer (SPX) parameters of the step and the deposited thickness and materials used for MPW. 
| `<tech>_layout.gds` | A generated test-pattern layout for the SPX 3D process simulation. |
| `tech.yaml` | The CarbonEDA settings for the technology: the flow CSV and layout, the carrier-wafer routes, the simulation clip, and the cut planes for the design-review renders. |

The carbon results computed from these flows are in `../outputs/dram/<tech>/` and `../outputs/flash/<tech>/`.

# Citations

[1] TechInsights. 2022. AME-2206-801 SK hynix H25T2TC88C (H25FTC0 Die) 176-Layer 3D NAND Flash 19 nm Process Node. https://library.techinsights.com/my-library/analysis-view/PFF-2206-802

[2] Kihara, Y., Tomura, M., Sakamoto, W., Honda, M., & Kojima, M. (2023, June). Beyond 10 μm depth ultra-high speed etch process with 84% lower carbon footprint for memory channel hole of 3D NAND flash over 400 layers. In 2023 IEEE Symposium on VLSI Technology and Circuits (VLSI Technology and Circuits) (pp. 1-2). IEEE.

[3] Ajaykumar, A., Breuil, L., Katcko, K., Schleicher, F., Sebaai, F., Oniki, Y., ... & Rosmeulen, M. (2021, June). First demonstration of ruthenium and molybdenum word lines integrated into 40nm pitch 3D-NAND memory devices. In 2021 Symposium on VLSI Technology (pp. 1-2). IEEE.

[4] TechInsights. 2023. SK hynix MDC5Q19002 D1a 8 Gb LPDDR4X DRAM Memory Process Flow Full. https://library.techinsights.com/my-library/analysis-view/PFF-2211-802