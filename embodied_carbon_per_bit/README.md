# Embodied Carbon per Bit

## 3D NAND Flash

Die area for all flash chips was fixed to 50 sq mm, making 1130 good die per 300 mm wafer at 90% chip yield. Memory array density was calculated for each stack height within the array (area labeled on GDS below). C<sub>embodied</sub> per bit was calculated after applying a fill factor between 61-66% to account for non-array die area (e.g., peripheral circuits).

| 3D NAND Flash GDS |  |  |  |
| :---- | :---- | :---- | ----- |
| **Configuration** | **Array Density** | **Fill Factor** | **Embodied/Gb (kg CO2e/Gb)** |
| **32L** | 3.4 | 0.6176865978 | 0.002055892609 |
| **176L** | 18.7 | 0.6591912309 | 0.0006674290293 |
| **512L** | 54.3 | 0.6652439899 | 0.0004416815971 |
| **1024L** | 108.6 | 0.6668292363 | 0.0003817396872 |

<table>
  <tr>
    <td align="center"><img src="../img/NAND_array.png" width="170" alt="3D NAND Flash layout"><br><em>3D NAND Flash</em></td>
  </tr>
</table>

## DRAM

Die area for all DRAM chips was fixed to 100 sq mm, making 562 good die per 300 mm wafer at 90% chip yield. Memory array density was calculated for the BG, VG, and IGZO configurations using the number of wordline/bitline crossings in the array (area labeled on GDS images below). For 3D DRAM, memory array density was calculated from stack height (64 layers) and x/y capacitor density (labeled on GDS below). C<sub>embodied</sub> per bit was calculated after applying a fill factor between 48-84% to account for non-array die area (e.g., peripheral circuits).

| DRAM GDS |  |  |  |  |
| :---- | :---- | :---- | :---- | :---- |
| **Configuration** | **Array Density** | **Fill Factor** | **Wordline pitch** | **Bitline pitch** |
| **BG1** | 0.6613756614 | 0.48384 | 36 | 42 |
| **BG2** | 0.754147813 | 0.57018 | 34 | 39 |
| **VG1** | 1.282051282 | 0.624 | 26 | 30 |
| **VG2/3** | 1.748251748 | 0.88 | 22 | 26 |
| **3D1/2** | 2.285714286 | 0.8413461538 | 80 | 350 |
| **IGZO** | 0.4572473708 | 0.69984 | 54 | 162 |


<table>
  <tr>
    <td align="center"><img src="../img/BG1.png" width="170" alt="BG1 layout"><br><em>BG1</em></td>
    <td align="center"><img src="../img/VG1.png" width="170" alt="VG1 layout"><br><em>VG1</em></td>
    <td align="center"><img src="../img/VG23.png" width="170" alt="VG2/3 layout"><br><em>VG2/3</em></td>
    <td align="center"><img src="../img/3D.png" width="170" alt="3D1/2 layout"><br><em>3D1/2</em></td>
    <td align="center"><img src="../img/ampcell_igzo.png" width="170" alt="IGZO layout"><br><em>IGZO</em></td>
  </tr>
</table>


