# SuperSensor v2.x

**NOTICE**: The Supersensor v2.x is still under development! Parts and configurations
may change until the design is finalized.

The SuperSensor is an all-in-one voice, motion, presence, temperature/humidity/air
quality, and light sensor, built on an ESP32 with ESPHome, and inspired
heavily by the EverythingSmartHome Everything Presence One sensor and the
HomeAssistant "$13 Voice Assistant" project.

Use SuperSensors around your house to provide HomeAssistant Voice Assist
interfaces with wake word detection, as well as other sensor detection options
as you want them.

Assist feedback is provided by a pair of common-cathode RGB LED. No speakers
or annoying TTS feedback here! With an optional 3D Printed case and a clear
diffuser cover, the LEDs can be turned into a sleek light bar on the bottom
of the unit for quick and easy confirmation of voice actions, or just use
it bare if you like the "PCB on a wall" aesthetic.

To Use:

  * Install the ESPHome configuration `supersensor.yaml` to a compatible ESP32 devkit (below).
  * Install the ESP32 and sensors into the custom PCB (if desired).
  * [Optional] 3D Print the custom case.
  * Power up the SuperSensor, connect to the WiFi AP, and connect it to your network.
  * Install the SuperSensor somewhere that makes sense.
  * Add/adopt the SuperSensor to HomeAssistant using the automatic name.
  * Tune the SuperSensor values to your needs.

Note: Once programmed, the output LED will flash continuously until connected
      to HomeAssistant, and a bit longer to establish if the wake word
      functionality is enabled. This is by design, so you know if your sensors
      are connected or not. If you do not want this, comment out the
      `light.turn_on` block starting on line 38 of the ESPHome configuration
      to disable this functionality.

For more details, please [see my first blog post on the SuperSensor project](https://www.boniface.me/the-supersensor/)
and [my update post on version 2.0](https://www.boniface.me/the-supersensor-2.0).

**NOTE: For those with v1.x hardware, see [the repository for that code instead](https://github.com/joshuaboniface/supersensor).**

## Major Changes from 1.x

1. Replaced the Bosch BME680 with the Sensirion SHT45 and Sensirion SGP41.

   The BME680 proved to be woefully unreliable in my testing. Temperature was fairly accurate (internal heating and offset notwithstanding),
   but humidity was wildly off of what other thermometers/hydrometers would report. In addition, the AQ functionality of the sensor was a
   source of much frustration and I was never able to get it to work reliably, either with the official BSEC library or with my own attempts
   at self-configuration.

   Thus, this sensor has been replaced with two Sensirion sensors which in my experience so far have been much more reliable and consistent.
   There is a slight cost increase due to these sensors, but not signfigant enough to outweigh the benefit of reliable monitoring they confer.

2. Replaced the SR602 PIR sensor with the AM312 PIR sensor.

   The SR602 was, in my experience, prone to constant false misfirings even in completely empty rooms. In addition its orientation requirements
   are awkward (pins on the left or right side). While it's possible I just had a bad batch, this soured me significantly to these sensors,
   especially after reading many other similar reports around the internet.

   Thus, this sensor has been replaced with the more reliable AM312. While the form factor fo the AM312 leaves a bit to be desired (sticking
   up by about 1cm more from the board), in the end I found this to be more of a positive than a negative for resposiveness and apperance.

3. Completely redesigned the custom PCB around the above sensor changes, which is now more compact in a 50x55mm almost-square configuration.

4. Significantly cleaned up the ESPHome configuration, to support the above sensors and remove a lot of cruft that was caused by the BME680.

## Parts List

| Qty   | Component          | Cost (2025/05 CAD, ex. shipping) | Links |
|-------|--------------------|----------------------------------|-------|
| 1     | GY-SGP41           | $11.08                           | [AliExpress](https://www.aliexpress.com/item/1005006746827606.html)  |
| 1     | GY-SHT45           | $5.67                            | [AliExpress](https://www.aliexpress.com/item/1005008175340220.html)* |
| 1     | SR602              | $0.81                            | [AliExpress](https://www.aliexpress.com/item/1005001572550300.html)  |
| 1     | TSL2591            | $4.59                            | [AliExpress](https://www.aliexpress.com/item/1005008619462097.html)  |
| 1     | HL-LD2510C         | $4.79                            | [AliExpress](https://www.aliexpress.com/item/1005006000579211.html)* |
| 1     | INMP441            | $2.93                            | [AliExpress](https://www.aliexpress.com/item/1005002902615623.html)  |
| 1     | ESP32 HW-395       | $6.67                            | [AliExpress](https://www.aliexpress.com/item/1005006019875837.html)* |
| 2     | RBG LED            | $0.09 ($9.12/100)                | [Amazon](https://www.amazon.ca/dp/B09Y8M2PKS) |
| 1     | 470Ω resistor      | $0.08 ($7.99/100)                | [Amazon](https://www.amazon.ca/dp/B08MKQX2XT) |
| 1     | Female pin header† | $1.59 ($15.99/10)                | [Amazon](https://www.amazon.ca/dp/B08CMNRXJ1) |
| 1     | Custom PCB (JLC)   | $0.69 ($6.89/10)                 | [GitHub](https://github.com/joshuaboniface/supersensor) |
| 1     | 3D Printed case    | $?.??‡                           | [GitHub](https://github.com/joshuaboniface/supersensor) |
| **TOTAL** |                    | **$38.99**                           |       |

`*` Ensure you select the correct device on the page as it shows multiple options.

`†` This is optional and only required if you don't want to directly solder the ESP32 to the board, but I recommend it.

`‡` Providing a price is impossible due to the wide range of possible fillament types and brands, but should be negligible.

## Configurable Options

There are several UI-configurable options with the SuperSensor to help you
get the most out of the sensor for your particular use-case.

**Note:** Configuration of the LD2410C is excluded here, as it is extensively
configurable. See [the documentation](https://esphome.io/components/sensor/ld2410.html) for more details on its options.

### Enable Voice Support (switch)

If enabled (the default), the SuperSensor's voice functionality including
wake word will be started. Disabling this defeats most of the point of the
SuperSensor, but can be done if desired, for instance if you have multiple
SuperSensors in a single room and only want one to respond to voice commands.

### Enable Presence LED (switch)

If enabled (the default), when overall presence is detected, the LEDs will
glow "white" at 15% power to signal presence.

### Temperature Offset (selector, -30 to +10 @ 0.1, -5 default)

Allows calibration of the SHT45 temperature sensor with an offset from -30 to +10
degrees C. Useful if the sensor is misreporting actual ambient tempreatures. Due
to internal heating of the SHT45 by the ESP32, this defaults to -5; further
calibration may be needed for your sensors and environment based on an external
reference.

### Humidity Offset (selector, -20 to +20 @ 0.1)

Allows calibration of the SHT45 humidity sensor with an offset from -10 to +10
percent relative humidity. Useful if the sensor is misreporting actual humidity
based on an external reference.

### PIR Hold Time (selector, 0 to +60 @ 5, 0 default)

The SuperSensor uses an AM312 PIR sensor, which has a stock hold time of ~2.5
seconds. This setting allows increasing that value, with retrigger support, to
up to 60 seconds, allowing the PIR detection to report for longer. 0 represents
"as long as the AM312 fires".

### Light Threshold Control (selector, 0 to +200 @ 5, 30 default)

The SuperSensor features a "light presence" binary sensor based on the light
level reported by the TSL2591 sensor. This control defines the minimum lux
value from the sensor to be considered "presence". For instance, if you have
a room that is usually dark at 0-5 lux, but illuminated to 100 lux when a
(non-automated) light switch is turned on, you could set a threshold here
of say 30 lux: then, while the light is on, "light presence" is detected,
and when the light is off, "light presence" is cleared. Light presence can
be used standalone or as part of the integrated occupancy sensor (below).

### Integrated Occupancy Sensor (Selector)

The SuperSensor features a fully integrated "occupancy" sensor, which can be
configured to provide exactly the sort of occupancy detection you may want
for your room.

There are 7 options (plus "None"/disabled), with both "detect" and "clear"
handled separately:

#### PIR + Radar + Light

Occupancy is detected when all 3 sensors report detected, and occupancy is
cleared when any of the sensors report cleared.

For detect, this provides the most "safety" against misfires, but requires
a normally-dark room with a non-automated light source and clear PIR
detection positioning.

For clear, this option is probably not very useful as it is likely to clear
quite frequently from the PIR, but is provided for completeness.

#### PIR + Radar

Occupancy is detected when both sensors report detected, and occupancy is
cleared when either of the sensors report cleared.

For detect, this provides good "safety" against PIR misfires without
needing a normally-dark room, though detection may be slightly delayed
from either sensor.

For clear, this option is probably not very useful as it is likely to clear
quite frequently from the PIR, but is provided for completeness.

#### PIR + Light

Occupancy is detected when both sensors report detected, and occupancy is
cleared when either of the sensors report cleared.

For detect, this provides some "safety" against PIR misfires, but requires
a normally-dark room with a non-automated light source and clear PIR
detection positioning.

For clear, this option is probably not very useful as it is likely to clear
quite frequently from the PIR, but is provided for completeness.

#### Radar + Light

Occupancy is detected when both sensors report detected, and occupancy is
cleared when either of the sensors report cleared.

For detect, this allows for radar detection while suppressing occupancy
without light, for instance in a hallway where one might not want a late
night bathroom visit to turn on the lights, or something to that effect.

For clear, this option can provide a useful option to clear presence
quickly if the lights go out, while still providing Radar presence.

#### PIR Only

Occupancy is based entirely on the PIR sensor for both detect and clear.

Prone to misfires, but otherwise a good option for quick detection and
clearance in a primarily-moving zone (e.g. hallway).

#### Radar Only

Occupancy is based entirely on the Radar sensor for both detect and clear.

Useful for an area with no consistent motion or light level.

#### Light Only

Occupancy is based entirely on the Light sensor for both detect and clear.

Useful for full dependence on an external light source.

#### None

Disable the functionality in either direction.

For detect, no occupancy will ever fire.

For clear, no states will clear occupancy; with any detect option, this
means that occupancy will be detected only once and never clear, which
is likely not useful.

## AQ Details

The SuperSensor 2.0 features an SGP41 air quality sensor by Sensirion. This is a powerful AQ
sensor which powers several commercial devices including the AirGradient One, which gave
us a lot of our configuration via their sharing of algorithms.

The sensor provides two base readings: a VOC Index, and a NOx Index. These values are both
floating references centered at 100 (VOC) and 1 (NOx), where that value represents "normal"
air over the previous 24 hours. These sensors are very useful for any sort of quick-change
automations, e.g. turn on a fan if levels spike due to cooking.

In addition, we leverage AirGradient's published forumulas to convert the VOC index into
actual VOC quantities, in both µg/m³ and ppb. While this may drift due to the sensor's regular
internal recalibration, I feel that following what AirGradient does is sufficient enough
for any real-world home usage. Further, we use a very rough conversion of the aforementioned
VOC quantity into an eCO2 reading, using Isobutylene as a reference gas. These sensors are
more useful for display purposes, to show the current levels in a room in a dashboard or
other such place, for human consumption. Note that no such conversions are done for NOx as
there are no (that I can find) published empirical calculations for this conversion, unlike
for VOCs via AirGradient.

Note however that like all MOx sensors, the SGP41 does not differentiate gasses, and as
such cannot tell the difference between normal, everyday natural VOCs like those in
breath or from e.g. ripening fruit, and dangerous VOCs from e.g. construction materials.
These should be used only as a general indication of air quality over short periods, rather
than an absolute reference over long periods (much to my own frustration but inevitable
begruding acceptance).
