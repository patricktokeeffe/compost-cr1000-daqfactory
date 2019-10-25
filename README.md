# Compost Soil Sensor Program

Acquire data from soil moisture probes and thermocouples using a
Campbell Scientific [CR1000](https://www.campbellsci.com/cr1000) and send
serial data messages to [DAQFactory](https://www.azeotech.com/).


## Wiring

The CR1000 is powered from a 12V power adapter. Be sure to turn the logger off
before adding or removing sensors. Its program will run automatically whenever
power is applied.

### Thermocouples

Four Type K thermocouples are connected to differential inputs 1-4:

> *Note standard thermocouple wiring convention is **red = negative**.*

| Description                | Color  | CR1000 |
|----------------------------|:------:|:------------:|
| Thermocouple #1 signal (+) | yellow | DF 1 H (SE1) |
| Thermocouple #1 ground (-) | red    | DF 1 L (SE2) |
| Thermocouple #2 signal (+) | yellow | DF 2 H (SE3) |
| Thermocouple #2 ground (-) | red    | DF 2 L (SE4) |
| Thermocouple #3 signal (+) | yellow | DF 3 H (SE5) |
| Thermocouple #3 ground (-) | red    | DF 3 L (SE6) |
| Thermocouple #4 signal (+) | yellow | DF 4 H (SE7) |
| Thermocouple #4 ground (-) | red    | DF 4 L (SE8) |

### Soil moisture/temperature probes

Six soil moisture & temperature probes
([5TM; METER Group](https://www.metergroup.com/environment/articles/meter-legacy-soil-moisture-sensors/))
are connected to the first SDI-12 port using three sets of screw terminals.

The SDI-12 addresses of the sensors are `7`, `8`, `9`, `A`, `B`, `C`. The
address is used in variable names and serial data messages to uniquely identify
the sensor.

> *Note legacy color scheme for 5TM sensors is **white = power**. Connecting
> the red wire to a power source will destroy these sensors.*

| Description | Color  | CR1000 |
|-------------|:------:|:------:|
| Power input | white  | C1     |
| Ground      | *bare* | G      |
| Data output | red    | 12V    |

### DAQFactory Computer

Use a serial cable and usb serial adapter, if needed, to connect the DAQFactory
computer to the `RS-232` port of the CR1000. The serial port configuration is
`115200/8/n/1`.

Each minute, after the data table record is generated, the stored data is then
reported out the RS-232 serial port as a series of comma-delimited strings:

* Each record:
    * is prefixed with either `TC` or `SS`
    * arrives 1-second after the previous record
    * and is delimited with a carriage return and line feed (`\r\n`)
* Line 1 contains thermocouple (TC) data for differential input channels
  1, 2, 3, and 4, respectively.
* Lines 2-7 contain soil sensor (SS) data organized by sensor ID
    * Field 2 contains the soil sensor ID (`7`-`C`)
    * Field 3 contains the temperature (degC)
    * Field 4 contains the volumetric water content (m^3/m^3)
    * Field 5 contains the apparent dielectric permittivity value (dimless)

Example output from CR1000 serial port (non-printing characters escaped for
clarity):
```
TC,22.578640,-76.386620,91.653840,109.250300\r\n
SS,7,13.008330,0.120773,6.768334\r\n
SS,8,11.866670,0.140369,7.661666\r\n
SS,9,12.116670,0.153912,8.299166\r\n
SS,A,21.600000,0.328149,18.648330\r\n
SS,B,21.208330,0.342396,19.758330\r\n
SS,C,22.025000,0.320539,18.078330\r\n
```


## Data Products

The logger queries sensors for new values every 5 seconds and stores averaged
values at the start of each minute. The record is stored to internal memory
and transmitted out the RS-232 serial port as a series of delimited strings.

* Base name: `compost_soil`
* Record interval: 1 minute
* Timestamp anchor: prior interval, e.g. 00:02:00 = avg(00:01:00-00:01:59)
* Internal memory capacity: ~4 weeks

Soil probe **temperature** is obtained from a thermistor in the overmold.
**Volumetric water content** is calculated from the measured **apparent
dielectric permittivity** value using the Topp equation<sup>[[ref](#user-manual)]</sup>,
though users may wish to explore using a different media-specific calibration
curve.


| Field name    | Units   | Description |
|---------------|---------|-------------|
| TC1_tmpr_Avg  | degC    | temperature from DF1 thermocouple |
| TC2_tmpr_Avg  | degC    | temperature from DF2 thermocouple |
| TC3_tmpr_Avg  | degC    | temperature from DF3 thermocouple |
| TC4_tmpr_Avg  | degC    | temperature from DF4 thermocouple |
| SS_7_T_Avg    | degC    | temperature from 5TM probe with ID 7 |
| SS_8_T_Avg    | degC    | temperature from 5TM probe with ID 8 |
| SS_9_T_Avg    | degC    | temperature from 5TM probe with ID 9 |
| SS_A_T_Avg    | degC    | temperature from 5TM probe with ID A |
| SS_B_T_Avg    | degC    | temperature from 5TM probe with ID B |
| SS_C_T_Avg    | degC    | temperature from 5TM probe with ID C |
| SS_7_VWC_Avg  | percent | volumetric water content from 5TM probe with ID 7 |
| SS_8_VWC_Avg  | percent | volumetric water content from 5TM probe with ID 8 |
| SS_9_VWC_Avg  | percent | volumetric water content from 5TM probe with ID 9 |
| SS_A_VWC_Avg  | percent | volumetric water content from 5TM probe with ID A |
| SS_B_VWC_Avg  | percent | volumetric water content from 5TM probe with ID B |
| SS_C_VWC_Avg  | percent | volumetric water content from 5TM probe with ID C |
| SS_7_E_Avg    | dimless | dielectric permittivity from 5TM probe with ID 7 |
| SS_8_E_Avg    | dimless | dielectric permittivity from 5TM probe with ID 8 |
| SS_9_E_Avg    | dimless | dielectric permittivity from 5TM probe with ID 9 |
| SS_A_E_Avg    | dimless | dielectric permittivity from 5TM probe with ID A |
| SS_B_E_Avg    | dimless | dielectric permittivity from 5TM probe with ID B |
| SS_C_E_Avg    | dimless | dielectric permittivity from 5TM probe with ID C |


## Licensing

* Original software contributions are licensed under
  [the MIT License](https://opensource.org/licenses/MIT)
* Screenshots and documentation are licensed under
  [CC-BY-SA-4.0](https://creativecommons.org/licenses/by-sa/4.0/) (Creative
  Commons Attribution-ShareAlike 4.0 International)


## References

* Campbell Scientific. *CR1000 Datalogger Operator's Manual.* Retrieved
  [2019-10-24](http://web.archive.org/web/20191025003939/https://s.campbellsci.com/documents/us/manuals/cr1000.pdf).
  Online: <https://s.campbellsci.com/documents/us/manuals/cr1000.pdf>

* <a href="user-manual" />Decagon Devices. *5TM Water Content and Temperature
  Sensors Operator's Manual.* Version 0.

* O'Keeffe, Patrick. *Vineyard Pilot Study Eddy Flux Tower Program.* Retrieved
  [2019-10-24](https://github.com/patricktokeeffe/2015-vineyard-eddyflux-tower/commit/bbd5e890accc49a4277fa2494571d528c783dc85).
  Online: <https://github.com/patricktokeeffe/2015-vineyard-eddyflux-tower>

