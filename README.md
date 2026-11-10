# ICCAD2026-FLASH-DRAM

## Summary

We quantify *total carbon* footprint (tC) (in kilograms of CO<sub>2</sub> equivalent) of next directions in 3D NAND flash and DRAM integrated circuits (ICs) that are actively being explored to improve memory density and energy efficiency. tC comprises both: *operational carbon* (C<sub>operational</sub>), due to hardware use, and *embodied carbon* (C<sub>embodied</sub>), due to hardware manufacturing. Toward filling this gap, this paper primarily focuses on quantifying C<sub>embodied</sub>, while using detailed information from >500 individual fabrication steps, accounting for energy, materials, and gas use during IC fabrication.

We develop detailed models to quantify C<sub>embodied</sub> for each direction, accounting for energy, material, and gas use from more than 500 individual process steps in complete fabrication processes. For 3D NAND flash, in which tC is dominated by C<sub>embodied</sub>, we present results for 4 different configurations of flash ICs, compared to a commercial 176-layer 512-Gigabit implementation as a baseline [1]. For DRAM, in which C<sub>operational</sub> constitutes a larger fraction of tC than in flash, we leverage DreamRAM and SCALE-Sim tools to estimate energy consumption, and quantify C<sub>operational</sub> for 2 representative application scenarios: GPT-2 and ResNet-18 inference [2][3]. We present results for 9 different configurations (D1–D9) of DRAM, compared to baseline commercial implementations at a “1a” DRAM technology node [4]. See `technologies/README.md` for full descriptions of next directions.

## Repository layout

| Path | Contents |
| --- | --- |
| `technologies/flash/` | The process flows of the 3D NAND flash technologies: `baseline` (176 layers), `<N>L` (F1, 32 to 1024 layers), `cryo_<N>L` (F2, cryogenic etching, 128 to 1024 layers), `176L_mo_wl` (F3, molybdenum wordline), and `176L_dwb` (F4, dual wafer bonding). Each folder holds the process flow CSV (`<tech>_final.csv`, one row per process step with its process area), a layout `.gds` (a generated test pattern), and a `tech.yaml`. |
| `technologies/dram/` | The process flows of the DRAM technologies, in the same format: `BG1` (1a baseline), `BG2`, `VG1`, `VG2`, `VG3`, `3D1`, `3D2`, and `IGZO_AMPCELL` (IGZO). |
| `config/` | `carbon_config.yaml` (tool energy, input gases, and gas emissions per process area) and `materials.yaml` (density and GWP of each material, wafer size, yield): the CarbonEDA configuration the outputs were computed with. |
| `outputs/[dram/flash]/<tech>/carbon/` | The CarbonEDA carbon results of each technology: `steps.csv` (EPW, MPW, and GPW per process step), `gases.csv` (per gas and step), `by_module.csv`, `by_area.csv`, and `totals.csv`. |
| `outputs/[dram/flash]/<tech>/report/` | `<tech>_design_review.pdf`: the design review of each technology, with one page per process step showing its 3D structure from the SPX simulation, cross-sections, mask, materials, and EPW, MPW, and GPW, plus summaries by module and process area. |
| `embodied_carbon_per_bit/` | C<sub>embodied</sub> per bit: die area, array density, and fill factor of each flash and DRAM configuration, with the array layouts in `img/`. |

## Embodied carbon

C<sub>embodied</sub> per wafer is EPW + MPW + GPW, in kgCO<sub>2</sub>e per 300 mm wafer before yield. As in the paper, EPW here excludes the 40% facility overhead (`tool_epw_kgco2e` in `totals.csv`; `epw_kgco2e` includes it).

3D NAND flash:

| | Baseline | F1 | F2 | F3 | F4 |
| --- | --- | --- | --- | --- | --- |
| EPW without fab overhead | 431.91 | 1337.69 | 386.01 | 430.97 | 582.67 |
| MPW | 55.90 | 132.50 | 44.45 | 55.87 | 87.05 |
| GPW | 237.91 | 934.60 | 201.40 | 237.91 | 296.77 |
| **C<sub>embodied</sub> per wafer** | **725.72** | **2404.79** | **631.86** | **724.75** | **966.49** |

Results for every layer count of F1 and F2 (32 to 1024 layers) are in `outputs/flash/`.

DRAM:

| | BG1 | BG2 | VG1 | VG2 | VG3 | 3D1 | 3D2 | IGZO |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| EPW without fab overhead | 224.02 | 238.93 | 219.45 | 381.10 | 380.63 | 552.33 | 443.27 | 381.80 |
| MPW | 35.92 | 35.92 | 35.40 | 67.59 | 67.58 | 80.40 | 65.83 | 42.74 |
| GPW | 67.01 | 67.01 | 63.22 | 129.29 | 129.29 | 213.46 | 167.03 | 151.19 |
| **C<sub>embodied</sub> per wafer** | **326.95** | **341.87** | **318.07** | **577.97** | **577.50** | **846.19** | **676.12** | **575.73** |

### Updates since the ICCAD 2026 paper

Since the ICCAD 2026 paper, we have improved the process flows and carbon footprint modeling:

- **Word line patterning etches in flash.** At higher stack heights, the word line patterning (WLP) steps are concatenated in the flow. To account for etch depth, the `WLP3_etch` and `WLP4_etch` process areas (up to 72 layers) use 4× the energy and emissions of a HAR etch, `WLP5_etch` (up to 36 layers) uses 2×, and `WLP6_etch` to `WLP8_etch` (up to 18 layers) are one HAR etch each. This matches the assumptions in the paper and does not change its results.
- **More detailed process areas.** Process areas now distinguish ion implantation, diffusion, wafer cleans, resist steps, and CMP (`Ion_implantation`, `Diffusion`, `Wafer_clean`, `Resist`, and `CMP`), with the same energies as in the paper (see `config/carbon_config.yaml`).
- **Gas use in deposition and etch.** Following the baseline DRAM and flash references, only deposition and etch steps cited as CVD or RIE use gases in GPW; other deposition and etch steps are in the `Deposition`, `Dry_etch`, or `Wet_etch` process areas.
- **Byproduct gases in GPW.** We updated the GPW calculation to use total input gas to calculate the fraction of gas that leads to byproducts, in contrast to unutilized gas.
- **Metal nitridation.** Metal nitridation steps are now in the `Diffusion` process area instead of `Metallization`.
- **Bonding steps.** Bonding steps are more granular, using the new process areas, which increases EPW of the flows with dual wafer bonding (DWB) in both DRAM and flash.
- **IGZO DRAM.** We revised the IGZO DRAM to match the bit density of 1a DRAM (0.32 Gb/mm<sup>2</sup>). It is now monolithic (no DWB), with 4 tiers of 2 IGZO FETs + 1 MIM capacitor built on top of 1 Si FET for local read amplification (vs. 1 tier in the paper). The added tiers increase EPW and GPW, and using only one silicon wafer lowers MPW.

The tables below compare each fab process as published in the paper with the updated flows as of 11/9/2026.

| Flash baseline (`baseline`) |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 675.31 | 420.15 | 58.07 | 197.09 |
| **11/9/2026** | 725.72 | 431.91 | 55.90 | 237.91 |
| **Percent Change (%)** | 7.5 | 2.8 | \-3.7 | 20.7 |

| Flash F1: 1024 layers (`1024L`) |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 2233.28 | 1328.77 | 142.67 | 761.84 |
| **11/9/2026** | 2404.79 | 1337.69 | 132.50 | 934.60 |
| **Percent Change (%)** | 7.7 | 0.7 | \-7.1 | 22.7 |

| Flash F2: Cryogenic etching (`cryo_176L`) |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 634.89 | 427.54 | 35.34 | 172.00 |
| **11/9/2026** | 631.86 | 386.01 | 44.45 | 201.40 |
| **Percent Change (%)** | \-0.5 | \-9.7 | 25.8 | 17.1 |

| Flash F3: Molybdenum wordline (`176L_mo_wl`) |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 675.31 | 420.15 | 58.07 | 197.09 |
| **11/9/2026** | 724.75 | 430.97 | 55.87 | 237.91 |
| **Percent Change (%)** | 7.3 | 2.6 | \-3.8 | 20.7 |

| Flash F4: Dual wafer bonding (`176L_dwb`) |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 753.90 | 460.60 | 85.30 | 208.00 |
| **11/9/2026** | 966.49 | 582.67 | 87.05 | 296.77 |
| **Percent Change (%)** | 28.2 | 26.5 | 2.1 | 42.7 |

| DRAM BG1 (baseline) |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 357.52 | 254.23 | 37.38 | 65.90 |
| **11/9/2026** | 326.95 | 224.02 | 35.92 | 67.01 |
| **Percent Change (%)** | \-8.5 | \-11.9 | \-3.9 | 1.7 |

| DRAM BG2 |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 364.52 | 263.41 | 36.81 | 64.30 |
| **11/9/2026** | 341.87 | 238.93 | 35.92 | 67.01 |
| **Percent Change (%)** | \-6.2 | \-9.3 | \-2.4 | 4.2 |

| DRAM VG1 |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 356.91 | 253.84 | 37.32 | 65.74 |
| **11/9/2026** | 318.07 | 219.45 | 35.40 | 63.22 |
| **Percent Change (%)** | \-10.9 | \-13.5 | \-5.1 | \-3.8 |

| DRAM VG2 |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 589.30 | 395.01 | 71.05 | 123.23 |
| **11/9/2026** | 577.97 | 381.10 | 67.59 | 129.29 |
| **Percent Change (%)** | \-1.9 | \-3.5 | \-4.9 | 4.9 |

| DRAM VG3 |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 588.20 | 394.44 | 71.00 | 122.76 |
| **11/9/2026** | 577.50 | 380.63 | 67.58 | 129.29 |
| **Percent Change (%)** | \-1.8 | \-3.5 | \-4.8 | 5.3 |

| DRAM 3D1 |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 734.69 | 520.18 | 80.25 | 134.27 |
| **11/9/2026** | 846.19 | 552.33 | 80.40 | 213.46 |
| **Percent Change (%)** | 15.2 | 6.2 | 0.2 | 59.0 |

| DRAM 3D2 |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 594.84 | 432.11 | 65.71 | 97.01 |
| **11/9/2026** | 676.12 | 443.27 | 65.83 | 167.03 |
| **Percent Change (%)** | 13.7 | 2.6 | 0.2 | 72.2 |

| DRAM IGZO (`IGZO_AMPCELL`) |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
|  | **Total embodied carbon per wafer (kgCO<sub>2</sub>e)** | **EPW without fab overhead (kgCO<sub>2</sub>e/wafer)** | **MPW (kgCO<sub>2</sub>e/wafer)** | **GPW (kgCO<sub>2</sub>e/wafer)** |
| **ICCAD ‘26** | 390.91 | 249.58 | 63.20 | 78.13 |
| **11/9/2026** | 575.73 | 381.80 | 42.74 | 151.19 |
| **Percent Change (%)** | 47.3 | 53.0 | \-32.4 | 93.5 |

## Reproducing the results

The carbon results are computed with [CarbonEDA](https://github.com/Harvard-NanoDesign/CarbonEDA) v0 (commit `310762a`). The carbon CSVs need only Python 3.9 or later:

```bash
git clone https://github.com/Harvard-NanoDesign/CarbonEDA.git
cd CarbonEDA && git checkout 310762a && make venv
cp -r /path/to/ICCAD2026-FLASH-DRAM/technologies/{dram,flash} technologies/
cp /path/to/ICCAD2026-FLASH-DRAM/config/*.yaml config/
for t in $(cd /path/to/ICCAD2026-FLASH-DRAM/technologies && ls -d dram/* flash/*); do make carbon TECH=$t; done
```

This writes `outputs/[dram/flash]/<tech>/carbon/`, which matches `outputs/[dram/flash]/` here. To change an assumption, such as the fab carbon intensity or the facility overhead, edit `config/carbon_config.yaml` and rerun `make carbon`.

With Synopsys Sentaurus Process Explorer (SPX) and Sentaurus Visual installed, `make all TECH=[dram/flash]/<tech>` also runs the 3D process simulation of each flow on its layout and regenerates the design-review PDFs in `outputs/[dram/flash]/<tech>/report/`. See the CarbonEDA README for the setup.

## Citation

D. Grey-Stewart, M. Elgamal, D. Kong, Z. Chen, Z. Piao, Y. Yao, and G. Hills, “Quantifying Carbon Footprint of Next Directions in 3D NAND Flash and DRAM Fabrication,” *2026 IEEE International Conference on Computer-Aided Design (ICCAD)*, San Jose, CA, USA, 2026.

## References

[1] TechInsights. 2022. AME-2206-801 SK hynix H25T2TC88C (H25FTC0 Die) 176-Layer 3D NAND Flash 19 nm Process Node. https://library.techinsights.com/my-library/analysis-view/PFF-2206-802

[2] Victor Cai, Jennifer Zhou, Haebin Do, David Brooks, and Gu-Yeon Wei. 2026. DreamRAM: A Fine-Grained Configurable Design Space Modeling Tool for Custom 3D Die-Stacked DRAM. In 2026 Design, Automation & Test in Europe Conference (DATE). 1–7. doi:10.23919/DATE69613.2026.11539402

[3] Ritik Raj, Sarbartha Banerjee, Nikhil Chandra, Zishen Wan, Jianming Tong, Ananda Samajdhar, and Tushar Krishna. 2025. SCALE-Sim V3: a Modular Cycle-Accurate Systolic Accelerator Simulator for End-To-End System Analysis. In 2025 IEEE International Symposium on Performance Analysis of Systems and Software (ISPASS). 186–200. doi:10.1109/ISPASS64960.2025.00026

[4] TechInsights. 2023. SK hynix MDC5Q19002 D1a 8 Gb LPDDR4X DRAM Memory Process Flow Full. https://library.techinsights.com/my-library/analysis-view/PFF-2211-802

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE).
