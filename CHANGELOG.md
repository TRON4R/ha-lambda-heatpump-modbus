# Changelog

## v1.0.0 (2026-04-10)

Initial release.

- Full Modbus TCP integration for the Lambda EU-L series heat pumps (EU08L, EU13L, EU15L, EU20L)
- 5 Ambient sensors (register block 0000)
- 5 E-Manager sensors (register block 0100)
- 17 Heat Pump 1 sensors (documented register block 1000, including heating capacity, compressor rating, COP and accumulated energy counters)
- 8 undocumented Heat Pump 1 sensors (registers 1025-1032, detected by Modbus scan — includes hot gas, subcooling, suction gas, condensation, evaporation, expansion valve opening angle)
- 5 Boiler 1 sensors (register block 2000)
- 11 Buffer 1 sensors (register block 3000)
- 10 Heating Circuit 1 sensors (register block 5000)
- 3 climate entities: heating curve offset, domestic hot water thermostat, room thermostat
- 4 calculated template sensors: SCOP, flow/return delta (dT), calculated heating power, heating power difference
- 9 state-mapping template sensors with German panel terminology and MDI icons
- Separate `helpers.yaml` with 3 template helpers: compressor speed (rpm), IMP NMT MAX II control percentage (HLP), collective fault binary sensor (`Lambda Sammelstörung`)
- Ready-to-use Lovelace dashboard card (`dashboard_card.yaml`) with 6 gauges (heating power, electrical power, COP, compressor speed, HLP control, HLP flow), heat pump state badge and an SVG buffer-tank visualization
- Entity quality: correct `device_class`, `state_class`, `unit_of_measurement`, `scale`, `precision` and `unique_id` for every sensor — long-term statistics and Energy Dashboard work out of the box
- Entity reference table (`entities.md`)
