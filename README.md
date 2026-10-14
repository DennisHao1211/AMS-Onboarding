# AMS Onboarding: Current-Starved Ring VCO

5-stage current-starved ring oscillator designed in SkyWater 130nm (sky130)
with Cadence Virtuoso, for the SiliconJackets Analog Mixed-Signal onboarding project.

## Specs and results (tt corner)

| Spec        | Target        | 27C      | 0-70C           |
|-------------|---------------|----------|-----------------|
| Frequency   | 450-550 MHz   | 500.2 MHz | 492.4-501.9 MHz |
| Rise time   | < 250 ps      | 189 ps   | 189-190 ps      |
| Fall time   | < 250 ps      | 178 ps   | 168-192 ps      |
| Duty cycle  | 45-55 %       | 50.3 %   | 49.8-50.8 %     |
| Vmin / Vmax | < 0.1 / > 1.7 V | -9 mV / 1.82 V | pass      |
| Core power  | < 500 uW      | 31 uW    | 30-33 uW        |

Vin = 1.44 V, VDD = 1.8 V, 1 pF load. The Vin bias branch adds about 8.5 uW.

## Design

- 5 current-starved inverter stages, current sources at L = 0.5 um
- Bias: Vin -> 111 kOhm poly resistor (res_high_po_0p35) -> diode-connected NMOS,
  mirrored to all NMOS and PMOS current sources
- 3-stage tapered output buffer driving the 1 pF load

Biasing the NMOS gates directly from Vin gave 129 MHz of frequency drift over 0-70C.
Converting Vin to a current through a resistor reduced the drift to about 10 MHz.

## Cells

| Cell               | Description                                      |
|--------------------|--------------------------------------------------|
| cs_inv, cs_inv_tap | Current-starved inverter (stages 1-4, stage 5)   |
| ring_osc_core_ib   | Final oscillator core with resistor current bias |
| ring_osc_core      | Baseline core with direct voltage bias           |
| buffer             | Output buffer                                    |
| tb_ring_osc_ib     | Testbench for the final design                   |
| tb_ring_osc        | Testbench for the baseline                       |
| tb_res             | Resistor model check                             |
| *_ideal            | Diagnostic cells using ideal current sources     |

## Opening the design

The sky130 PDK is not included. Clone the SiliconJackets analog-onboarding-F26
repo (PDK and cds.lib), place this library under Cadence/Virtuoso/, and add to cds.lib:

    DEFINE AMS_Onboarding_Hanxiang_Hao ./AMS_Onboarding_Hanxiang_Hao
