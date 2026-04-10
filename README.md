<img src="images/logo.png" alt="Lambda Heat Pump Logo" width="120" align="left" style="margin-right:16px;"/>

### Lambda EU-L Series
**Home Assistant Modbus TCP**

<br clear="left"/>

**YAML package to integrate Lambda EU-L series air/water heat pumps into Home Assistant via native Modbus TCP — no custom component required.**

---

## Compatible Heat Pumps

This package was developed and validated on the **Lambda EU20L**, but the
Modbus register map is identical across the entire Lambda EU-L series:

- EU08L
- EU13L
- EU15L
- EU20L
- EU30L

Only the physical heating capacity differs — the register block layout,
scaling, data types and enumerations are shared between all models. Renaming
the Modbus hub name (and optionally the `EU20L …` sensor name prefixes) is all
that is needed to run the same package on a different EU-L model.

Based on the official Lambda Modbus specification dated **2025-02-13**.

## Features

- **Full register coverage of 6 Lambda modules** — Ambient, E-Manager, Heat Pump 1, Boiler 1, Buffer 1, Heating Circuit 1
- **61 Modbus sensors** with correct `device_class`, `state_class`, `unit_of_measurement`, `scale` and `precision`
- **8 undocumented register sensors** (hot gas, subcooling, suction gas, condensation, evaporation, expansion valve opening angle, VdA max rating, EqM rating) — identified by Modbus scan and matched against the Lambda control panel
- **9 state-mapping template sensors** rendering the numeric state registers as plain German panel text plus matching MDI icons
- **4 calculated template sensors**: SCOP, flow/return ΔT, calculated heating power (`flow × ΔT × 1.163`), and the deviation between reported and calculated heating power
- **3 climate entities** exposing the heating curve offset, domestic hot water thermostat and room thermostat as regular HA climate tiles
- **Energy Dashboard ready** — accumulated energy counters (registers 1020 and 1022) use `total_increasing` and are already scaled to kWh
- **Separate optional helpers** (`helpers.yaml`) for compressor speed in rpm, IMP NMT MAX II circulation pump control percentage, and a collective fault binary sensor
- **Ready-to-use Lovelace dashboard card** with 6 gauges, heat pump state badge and an SVG buffer-tank visualization

## Entity Quality

All sensors are defined with correct and complete HA metadata: `unique_id`,
`device_class`, `state_class`, `unit_of_measurement`, `scale` and `precision`
are carefully set for every entity. Long-term statistics, Energy Dashboard
integration and history graphs work out of the box, without any manual
configuration in the HA UI.

Accumulated energy counters (registers 1020 and 1022) are converted from Wh to
kWh at the scale level so the Energy Dashboard does not have to deal with
seven-digit Wh readings. Power consumption from the frequency inverter
(register 1012) is also rescaled from W to kW to match the unit the Lambda
control panel and every downstream system (evcc, Power Flow Card) expect.

## Requirements

- Home Assistant (any current version with the built-in [Modbus integration](https://www.home-assistant.io/integrations/modbus/))
- A Lambda EU-L series heat pump with firmware that exposes the Modbus TCP interface
- Network connection between HA and the Lambda controller (Ethernet)
- Default Modbus TCP port: **502**

> **Firmware note (V1.1.3):** Owners with firmware V1.1.3 have reported that
> the word order of `int32` registers has been changed. If your accumulated
> energy counters (1020, 1022) show nonsense values, add `swap: word` to those
> sensor definitions. See the inline comment at the top of
> `lambda_heatpump.yaml`.

## Installation

1. Copy `lambda_heatpump.yaml` (and optionally `helpers.yaml`) to `/config/packages/`
2. Ensure packages are enabled in `configuration.yaml`:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
3. Adjust the Lambda controller IP address in the YAML:
   ```yaml
   host: 192.168.2.6  # ← CHANGE: IP of your Lambda controller
   ```
4. Optionally rename the Modbus hub name (`EU20L`) and the `EU20L …` sensor
   name prefix if you run a different EU-L model
5. Restart Home Assistant

> **Language:** Entity names, comments and template descriptions are in
> English. Panel-facing terminology (state mappings, climate entity names) is
> deliberately kept in German because the Lambda control panel is German-only.

## Dashboard Card (optional)

<p align="center">
  <img src="images/dashboard_panel.png" alt="Lambda Heat Pump Dashboard" width="400"/>
</p>

The file `dashboard_card.yaml` contains a ready-to-use Lovelace card with
gauges for heating power, electrical power consumption, COP, compressor speed,
heat-load-pump (HLP) control percentage and HLP flow rate. A custom
button-card renders the buffer tank as an SVG with live temperature readings.

<p align="center">
  <img src="images/dashboard_buffer_tank.png" alt="Buffer tank SVG" width="220"/>
  &nbsp;
  <img src="images/dashboard_all_entities.png" alt="All entities view" width="220"/>
  &nbsp;
  <img src="images/dashboard_undocumented_registers.png" alt="Undocumented registers" width="220"/>
</p>

**Usage:** Paste the contents of `dashboard_card.yaml` as a manual card (YAML)
in the dashboard editor.

> **Helper dependency:** The gauges for compressor speed (rpm) and HLP control
> percentage reference template sensors that live in `helpers.yaml`. Install
> `helpers.yaml` together with the dashboard card or remove those two gauges.

### Required HACS Cards

| HACS Card | Purpose | HACS Search |
|---|---|---|
| [**Vertical Stack In Card**](https://github.com/ofekashery/vertical-stack-in-card) | Outer container without borders | `vertical-stack-in-card` |
| [**card-mod**](https://github.com/thomasloven/lovelace-card-mod) | CSS styling (remove borders, colors) | `card-mod` |
| [**button-card**](https://github.com/custom-cards/button-card) | SVG buffer-tank visualization | `button-card` |

Without these cards the dashboard card will not render correctly. The Modbus
YAML itself works independently.

## Register Overview

### Ambient (Register Block 0000)

| Register | Sensor | Unit |
|---|---|---|
| 0000 | Error Number | — |
| 0001 | Operating State | — |
| 0002 | Temperature Actual | °C |
| 0003 | Temperature Average 1h | °C |
| 0004 | Temperature Calculated | °C |

### E-Manager (Register Block 0100)

| Register | Sensor | Unit |
|---|---|---|
| 0100 | Error Number | — |
| 0101 | Operating State | — |
| 0102 | Power Actual | W |
| 0103 | Power Consumption Actual | W |
| 0104 | Power Consumption Setpoint | W |

### Heat Pump 1 — documented (Register Block 1000)

| Register | Sensor | Unit |
|---|---|---|
| 1000 | Heat Pump Error State | — |
| 1001 | Heat Pump Error Number | — |
| 1002 | Heat Pump State | — |
| 1003 | Operating State | — |
| 1004 | Flow Line Temperature | °C |
| 1005 | Return Line Temperature | °C |
| 1006 | Volume Flow Heat Sink | L/h |
| 1007 | Energy Source Inlet Temperature | °C |
| 1008 | Energy Source Outlet Temperature | °C |
| 1009 | Volume Flow Energy Source | L/min |
| 1010 | Compressor Rating | % |
| 1011 | Actual Heating Capacity | kW |
| 1012 | Inverter Power Consumption | kW |
| 1013 | COP | — |
| 1019 | Relais state 2nd heating stage | 0/1 |
| 1020 | Compressor Power Consumption Accumulated | kWh |
| 1022 | Compressor Thermal Energy Output Accumulated | kWh |

### Heat Pump 1 — undocumented (detected by Modbus scan)

> ⚠️ These registers are **not** documented in the official Lambda Modbus
> specification. Use at your own risk.

<p align="center">
  <img src="images/dashboard_undocumented_registers.png" alt="Undocumented registers tile" width="400"/>
</p>

| Register | Sensor | Unit | Panel Label |
|---|---|---|---|
| 1025 | VdA Max Rating | % | Verdichteranlage Max. Rating |
| 1026 | Hot Gas Temperature | °C | Heißgas |
| 1027 | Subcooling Temperature | °C | Unterkühlung |
| 1028 | Suction Gas Temperature | °C | Sauggas |
| 1029 | Condensation Temperature | °C | Kondensation |
| 1030 | Evaporation Temperature | °C | Verdampfung |
| 1031 | EqM Rating | % | Energiequellen-Modulation |
| 1032 | Expansion Valve Opening Angle | % | Öffnungswinkel |

### Boiler 1 (Register Block 2000)

| Register | Sensor | Unit |
|---|---|---|
| 2000 | Error Number | — |
| 2001 | Operating State | — |
| 2002 | Temperature Actual High | °C |
| 2003 | Temperature Actual Low | °C |
| 2050 | Temperature Max (RW) | °C |

### Buffer 1 (Register Block 3000)

| Register | Sensor | Unit |
|---|---|---|
| 3000 | Error Number | — |
| 3001 | Operating State | — |
| 3002 | Temperature Actual High | °C |
| 3003 | Temperature Actual Low | °C |
| 3004 | Modbus Buffer Temperature High (RW) | °C |
| 3005 | Request Type (RW) | — |
| 3006 | Request Flow Line Temperature Setpoint (RW) | °C |
| 3007 | Request Return Line Temperature Setpoint (RW) | °C |
| 3008 | Request Heat Sink Temperature Difference (RW) | K |
| 3009 | Modbus Request Heating Capacity (RW) | kW |
| 3050 | Set Maximum Buffer Temperature (RW) | °C |

### Heating Circuit 1 (Register Block 5000)

| Register | Sensor | Unit |
|---|---|---|
| 5000 | Error Number | — |
| 5001 | Operating State | — |
| 5002 | Flow Line Temperature | °C |
| 5003 | Return Line Temperature | °C |
| 5004 | Room Device Temperature (RW) | °C |
| 5005 | Flow Line Temperature Setpoint (RW) | °C |
| 5006 | Operating Mode (RW) | — |
| 5050 | Set Flow Line Temperature Offset (RW) | K |
| 5051 | Set Heating Mode Room Temperature (RW) | °C |
| 5052 | Set Cooling Mode Room Temperature (RW) | °C |

### Climate Entities

| Entity | Read Register | Write Register | Range |
|---|---|---|---|
| λ Heizkurve Solltemperatur Offset | 5050 | 5050 | −5.0 … +5.0 K |
| λ Brauchwasser-Thermostat | 2002 | 2050 | 45 … 54 °C |
| λ Raum-Thermostat | 5004 | 5051 | 15 … 35 °C |

## Entity Reference

See [entities.md](entities.md) for a complete mapping of registers to entity
names, units, device classes and state mappings.

## Optional Helpers

`helpers.yaml` contains three template helpers that are commonly useful but
kept out of `lambda_heatpump.yaml` because they depend on specific hardware
choices or display preferences:

1. **Compressor speed (rpm)** — derived from register 1010, assumes 5500 rpm = 100 %
2. **Heat charge pump (HLP) control %** — piecewise-linear interpolation from
   register 1006 (heat sink volume flow) to the control percentage of an
   **IMP NMT MAX II** circulation pump. The calibration points were measured
   empirically and do **not** apply to other pump models.
3. **`Lambda Sammelstörung`** — a `binary_sensor` of `device_class: problem`
   that turns on if any of the six Lambda error number registers reports a
   non-zero value.

The dashboard card references the first two helpers. If you do not use an
IMP NMT MAX II pump, either remeasure the curve or remove the HLP gauge.

### Planned

Template sensors for **JAZ / MAZ / TAZ** (yearly / monthly / daily COP) are
planned but not yet included because they depend on a longer chain of
`utility_meter` helpers and integration helpers that are not part of this
package. Contributions welcome.

## Documentation

- Official Lambda Modbus specification (shipped with the controller) — dated 2025-02-13
- Reverse-engineered register mapping for the undocumented registers 1025-1032 is documented inline in `lambda_heatpump.yaml`

## Related Projects

Other Lambda heat pump projects for Home Assistant:

- [`GuidoJeuken-6512/lambda_heat_pumps`](https://github.com/GuidoJeuken-6512/lambda_heat_pumps) — HACS custom component
- [`RalfWinter/lambda-heatpump-modbus-tcp-HA`](https://github.com/RalfWinter/lambda-heatpump-modbus-tcp-HA) — earlier YAML package
- [`floriansProjects/lambda-heatpump-homeassistant`](https://github.com/floriansProjects/lambda-heatpump-homeassistant)
- [`route662/Lambda-Heatpump`](https://github.com/route662/Lambda-Heatpump)

This project is an independent, from-scratch YAML package with the explicit
goal of **correct HA metadata on every entity** (device/state class, unit,
scale, precision) and **full coverage including the undocumented 1025-1032
range**.

## License

[MIT](LICENSE)
