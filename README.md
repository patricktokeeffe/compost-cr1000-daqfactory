# Compost Soil Sensor Program

Acquire data from temperature/RH probe and thermocouples using a
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

### Temperature/RH probe

The temperature/relative humidity probe ([CS215; Campbell Scientific](https://www.campbellsci.com/cs215-l))
is connected to port C1 and data is 

| Description  | Color  | CR1000 |
|--------------|:------:|:------:|
| Power input  | red    | 12V    |
| Power ground | black  | G      |
| Power ground | white  | G      |
| SDI-12 data  | green  | C1     |
| shield       | clear  | G      |

### ~~Soil moisture/temperature probes~~

> *These sensors were removed*

~~Six soil moisture & temperature probes
([5TM; Decagon Devices](https://web.archive.org/web/20150512214403/http://www.decagon.com/products/soils/volumetric-water-content-sensors/5tm-vwc-temp))
are connected to the first SDI-12 port using three sets of screw terminals.~~

~~The SDI-12 addresses of the sensors are `7`, `8`, `9`, `A`, `B`, `C`. The
address is used in variable names and serial data messages to uniquely identify
the sensor.~~

> ~~*Note legacy color scheme for 5TM sensors is **white = power**. Connecting
> the red wire to a power source will destroy these sensors.*~~

### DAQFactory Computer

Use a serial cable and usb serial adapter, if needed, to connect the DAQFactory
computer to the `RS-232` port of the CR1000. The serial port configuration is:
* Baud rate: `115200`
* Data bits: `8`
* Parity: `none`
* Stop bits: `1`

A new record is generated after each scan and reported out the RS-232 serial port.

* Each record:
    * is prefixed with `TC`
    * and is delimited with a carriage return and line feed (`\r\n`)
	* contains thermocouple (TC) data for differential input channels
  1, 2, 3, and 4, respectively.
* Note: only thermocouple data is included in the serial output record. Data
  from the ambient temperature/RH probe is only stored locally on the logger.

Example CR1000 serial port output (non-printing characters shown for clarity)
is shown below.
*(Only thermocouple DF1 is attached in this example data stream... disregard
the strange temperature values.)*

```
TC,22.578640,-76.386620,91.653840,109.250300\r\n
```


## Data Products

The logger queries sensors for new values and records data every 5 seconds.
The record is stored to internal memory
in a ring-fill mode (e.g. newest overwrites oldest when full).

* Base name: `compost_soil`
* Record interval: 5 seconds
* Timestamp anchor: prior interval, e.g. 00:02:00 = avg(00:01:00-00:01:59)

~~Soil probe **temperature** is obtained from a thermistor in the overmold.
**Volumetric water content** is calculated from the measured **apparent
dielectric permittivity** value using the Topp equation<sup>[[ref](#user-manual)]</sup>,
though users may wish to explore using a different media-specific calibration
curve.~~


| Field name    | Units   | Description |
|---------------|---------|-------------|
| TC1_tmpr_Avg  | degC    | temperature from DF1 thermocouple |
| TC2_tmpr_Avg  | degC    | temperature from DF2 thermocouple |
| TC3_tmpr_Avg  | degC    | temperature from DF3 thermocouple |
| TC4_tmpr_Avg  | degC    | temperature from DF4 thermocouple |
| CS215_T_Avg   | degC    | temperature from CS215 probe      |
| CS215_RH_Avg  | percent | relative humidity from CS215 probe |


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

* Campbell Scientific. *CS215 Temperature and Relative Humidity Probe Instruction
  Manual.* Online: <https://s.campbellsci.com/documents/us/manuals/cs215.pdf>

* <a name="user-manual" />Decagon Devices. *5TM Water Content and Temperature
  Sensors Operator's Manual.* Version [July 10, 2017](https://web.archive.org/web/20171006170216/http://manuals.decagon.com/Manuals/13441_5TM_Web.pdf).
  Online: <http://library.metergroup.com/Retired%20and%20Discontinued/Manuals/13441_5TM_Web.pdf>


