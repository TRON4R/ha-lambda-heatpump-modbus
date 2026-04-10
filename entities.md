# Entity Reference

All entity IDs use the prefix `sensor.eu20l_…` / `climate.…`. The display names
inside `lambda_heatpump.yaml` start with the register block (`EU20L HP1 …`) so
that the autocomplete in Home Assistant filters quickly by Modbus section.

The German panel terminology (Wärmepumpe, Brauchwasser, Heizkreis, Puffer,
Heizkurve, Sommerbetrieb …) is deliberately kept in the state mappings and the
climate entity names because Lambda is an Austrian manufacturer and the control
panel is German-only.

---

## Ambient — Register block 0000

| Register | Entity Name                          | Unit | Device Class |
|---|---|---|---|
| 0000 | EU20L Ambient Error Number            | —    | — |
| 0001 | EU20L Ambient Operating State         | —    | — (0=OFF, 1=AUTOMATIC, 2=MANUAL, 3=ERROR) |
| 0002 | EU20L Ambient Temperature Actual      | °C   | temperature |
| 0003 | EU20L Ambient Temperature Average 1h  | °C   | temperature |
| 0004 | EU20L Ambient Temperature Calculated  | °C   | temperature |

## E-Manager — Register block 0100

| Register | Entity Name                           | Unit | Device Class |
|---|---|---|---|
| 0100 | EU20L EMgr Error Number                | —  | — |
| 0101 | EU20L EMgr Operating State             | —  | — (0=OFF, 1=AUTOMATIC, 2=MANUAL, 3=ERROR, 4=OFFLINE) |
| 0102 | EU20L EMgr Power Actual                | W  | power |
| 0103 | EU20L EMgr Power Consumption Actual    | W  | power |
| 0104 | EU20L EMgr Power Consumption Setpoint  | W  | power |

## Heat Pump 1 — Register block 1000 (documented)

| Register | Entity Name                                              | Unit  | Device Class |
|---|---|---|---|
| 1000 | EU20L HP1 Heat Pump Error State                           | —     | — (0=NONE, 1=MESSAGE, 2=WARNING, 3=ALARM, 4=FAULT) |
| 1001 | EU20L HP1 Heat Pump Error Number                          | —     | — |
| 1002 | EU20L HP1 Heat Pump State                                 | —     | — (0..40 — see state mapping) |
| 1003 | EU20L HP1 Operating State                                 | —     | — (0..18 — see state mapping) |
| 1004 | EU20L HP1 Flow Line Temperature                           | °C    | temperature |
| 1005 | EU20L HP1 Return Line Temperature                         | °C    | temperature |
| 1006 | EU20L HP1 Volume Flow Heat Sink                           | L/h   | volume_flow_rate |
| 1007 | EU20L HP1 Energy Source Inlet Temperature                 | °C    | temperature |
| 1008 | EU20L HP1 Energy Source Outlet Temperature                | °C    | temperature |
| 1009 | EU20L HP1 Volume Flow Energy Source                       | L/min | volume_flow_rate |
| 1010 | EU20L HP1 Compressor Rating                               | %     | — |
| 1011 | EU20L HP1 Actual Heating Capacity                         | kW    | power |
| 1012 | EU20L HP1 Inverter Power Consumption                      | kW    | power |
| 1013 | EU20L HP1 COP                                             | —     | — (dimensionless ratio) |
| 1019 | EU20L HP1 Relais state 2nd heating stage                  | —     | — (0/1) |
| 1020 | EU20L HP1 Compressor Power Consumption Accumulated        | kWh   | energy (total_increasing) |
| 1022 | EU20L HP1 Compressor Thermal Energy Output Accumulated    | kWh   | energy (total_increasing) |

Registers 1014-1018 exist in the Lambda specification but are commented out in
the YAML. See the inline comments in `lambda_heatpump.yaml` for the reasoning
(1014 is a password register, 1015-1018 consistently read 0 on EU20L).

## Heat Pump 1 — Undocumented (register block 1025-1032)

These registers are **not** documented in the official Lambda Modbus
specification. They were identified by a full Modbus scan and their meaning
was reverse-engineered by matching values against the Lambda control panel
(values in parentheses are the corresponding German panel labels, visible only
at user level 2 or 3).

> ⚠️ Use at your own risk. No guarantee that the register assignments are
> correct on every firmware version.

| Register | Entity Name                                   | Unit | Device Class |
|---|---|---|---|
| 1025 | EU20L HP1 VdA Max Rating (Verdichteranlage)    | %    | — |
| 1026 | EU20L HP1 Hot Gas Temperature (Heißgas)        | °C   | temperature |
| 1027 | EU20L HP1 Subcooling Temperature (Unterkühlung)| °C   | temperature |
| 1028 | EU20L HP1 Suction Gas Temperature (Sauggas)    | °C   | temperature |
| 1029 | EU20L HP1 Condensation Temperature (Kondensation) | °C | temperature |
| 1030 | EU20L HP1 Evaporation Temperature (Verdampfung)| °C   | temperature |
| 1031 | EU20L HP1 EqM Rating (Energiequellen-Modulation)| %   | — |
| 1032 | EU20L HP1 Expansion Valve Opening Angle (Öffnungswinkel) | % | — |

## Boiler 1 — Register block 2000

| Register | Entity Name                           | Unit | Device Class |
|---|---|---|---|
| 2000 | EU20L Boil1 Error Number               | —    | — |
| 2001 | EU20L Boil1 Operating State            | —    | — (0..12 — see state mapping) |
| 2002 | EU20L Boil1 Temperature Actual High    | °C   | temperature |
| 2003 | EU20L Boil1 Temperature Actual Low     | °C   | temperature |
| 2050 | EU20L Boil1 Temperature Max (RW)       | °C   | temperature |

Optional registers 2004 (circulation temperature) and 2005 (circulation pump
state) are commented out in the YAML — they only make sense if a domestic hot
water circulation pump with a temperature sensor is directly connected to the
Lambda controller.

## Buffer 1 — Register block 3000

| Register | Entity Name                                              | Unit | Device Class |
|---|---|---|---|
| 3000 | EU20L Buff1 Error Number                                  | —    | — |
| 3001 | EU20L Buff1 Operating State                               | —    | — (0..9 — see state mapping) |
| 3002 | EU20L Buff1 Temperature Actual High                       | °C   | temperature |
| 3003 | EU20L Buff1 Temperature Actual Low                        | °C   | temperature |
| 3004 | EU20L Buff1 Modbus Buffer Temperature High (RW)           | °C   | temperature |
| 3005 | EU20L Buff1 Request Type (RW)                             | —    | — |
| 3006 | EU20L Buff1 Request Flow Line Temperature Setpoint (RW)   | °C   | temperature |
| 3007 | EU20L Buff1 Request Return Line Temperature Setpoint (RW) | °C   | temperature |
| 3008 | EU20L Buff1 Request Heat Sink Temperature Difference (RW) | K    | temperature_delta |
| 3009 | EU20L Buff1 Modbus Request Heating Capacity (RW)          | kW   | power |
| 3050 | EU20L Buff1 Set Maximum Buffer Temperature (RW)           | °C   | temperature |

## Heating Circuit 1 — Register block 5000

| Register | Entity Name                                   | Unit | Device Class |
|---|---|---|---|
| 5000 | EU20L HC1 Error Number                         | —    | — |
| 5001 | EU20L HC1 Operating State                      | —    | — (0..20 — see state mapping) |
| 5002 | EU20L HC1 Flow Line Temperature                | °C   | temperature |
| 5003 | EU20L HC1 Return Line Temperature              | °C   | temperature |
| 5004 | EU20L HC1 Room Device Temperature (RW)         | °C   | temperature |
| 5005 | EU20L HC1 Flow Line Temperature Setpoint (RW)  | °C   | temperature |
| 5006 | EU20L HC1 Operating Mode (RW)                  | —    | — (0..7 — see state mapping) |
| 5050 | EU20L HC1 Set Flow Line Temperature Offset (RW) | K   | temperature_delta |
| 5051 | EU20L HC1 Set Heating Mode Room Temperature (RW)| °C  | temperature |
| 5052 | EU20L HC1 Set Cooling Mode Room Temperature (RW)| °C  | temperature |

Register 5007 (calculated flow line target temperature) exists in the spec but
cannot be read over Modbus despite being marked read-only. The sensor is
therefore commented out in the YAML.

---

## Climate entities

Three `modbus.climates` entries expose RW registers as HA climate entities so
that temperatures and offsets can be adjusted directly from the dashboard.

| Entity Name                        | Read Register | Write Register | Range              |
|---|---|---|---|
| λ Heizkurve Solltemperatur Offset  | 5050          | 5050           | −5.0 … +5.0 K      |
| λ Brauchwasser-Thermostat          | 2002          | 2050           | 45 … 54 °C         |
| λ Raum-Thermostat                  | 5004          | 5051           | 15 … 35 °C         |

The heating curve offset also writes HVAC modes via register 5001
(0=Heizen, 1=Auto, 2=Cool, 12=Off).

> **Note on `λ Raum-Thermostat`**: register 5004 must be written at least every
> 5 minutes, otherwise Lambda falls back to its internal default mode. Plan a
> corresponding HA automation if you intend to use this.

---

## Calculated template sensors

| Entity Name                                       | Source Registers          | Purpose |
|---|---|---|
| EU20L HP1 SCOP                                    | 1020 / 1022               | Seasonal COP from accumulated energy counters |
| EU20L HP1 Heat Pump Line/Return Flow Delta (dT)   | 1004, 1005                | Vorlauf/Rücklauf spreading in Kelvin |
| EU20L HP1 Heating Power calculated                | 1006, 1004, 1005          | `flow × ΔT × 1.163` — sanity check for register 1011 |
| EU20L HP1 Heating Power Difference                | 1011 vs. calculated power | Deviation between reported and calculated heating power |

## State-mapping template sensors

These produce a human-readable German text + MDI icon from the numeric state
registers. They are the entities that the dashboard badges and tile labels
reference, not the raw numeric sensors.

| Entity Name                                 | Source Register |
|---|---|
| EU20L Ambient Operating State 0001 map      | 0001 |
| EU20L EMgr Operating State 0101 map         | 0101 |
| EU20L HP1 Heat Pump Error State 1000 map    | 1000 |
| EU20L HP1 Heat Pump State 1002 map          | 1002 |
| EU20L HP1 Heat Pump Operating State 1003 map | 1003 |
| EU20L Boil1 Operating State 2001 map        | 2001 |
| EU20L Buff1 Operating State 3001 map        | 3001 |
| EU20L HC1 Operating State 5001 map          | 5001 |
| EU20L HC1 Operating Mode 5006 map           | 5006 |

---

## Optional helpers (`helpers.yaml`)

See [`helpers.yaml`](helpers.yaml) — these are separated because they depend on
an external pump model and on custom display preferences.

| Entity ID                                       | Type          | Source             | Notes |
|---|---|---|---|
| `sensor.l_warmepumpe_verdichter_drehzahl`       | sensor        | 1010               | Compressor speed in rpm (assumes 5500 rpm = 100 %) |
| `sensor.l_warmepumpe_heizladepumpe_regelung_hlp`| sensor        | 1006               | IMP NMT MAX II control % (piecewise-linear curve) |
| `binary_sensor.lambda_sammelstoerung`           | binary_sensor | 0000/0100/1001/2000/3000/5000 | Collective fault — on if any error number > 0 |
