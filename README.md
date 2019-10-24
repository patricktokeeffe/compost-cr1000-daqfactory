# Compost WXT520 Logger Program

Acquire data from soil moisture probes and thermocouples using a
Campbell Scientific [CR1000](https://www.campbellsci.com/cr1000) and send
serial data messages to [DAQFactory](https://www.azeotech.com/).


## Wiring

The CR1000 is powered from a 12V power adapter. Be sure to turn the logger off
before adding or removing sensors.

### Thermocouples

Up to (4) Type K thermocouples are connected to differential inputs 1-4:

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

Up to (7) soil moisture & temperature probes
([5TM; Decagon Devices](https://www.metergroup.com/environment/articles/meter-legacy-soil-moisture-sensors/))
are connected to an SDI-12 port:

> The SDI-12 addresses of the sensors must be assigned from `6` to `C`. The
> address is used in variable names and serial data messages to uniquely
> identify the sensor.

| Description | Color  | CR1000 |
|-------------|:------:|:------------:|
| Power input | white  | C1 (Com1 Tx) |
| Ground      | *bare* | G |
| Data output | red    | 12V |




