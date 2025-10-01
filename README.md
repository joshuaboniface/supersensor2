# SuperSensor v2.x

The SuperSensor is an all-in-one voice, motion, presence, temperature/humidity/air
quality, and light sensor, built on an ESP32 with ESPHome, and inspired
heavily by the EverythingSmartHome Everything Presence One sensor and the
HomeAssistant "$13 Voice Assistant" project.

Use SuperSensors around your house to provide HomeAssistant Voice Assist
interfaces with wake word detection, as well as other sensor detection options
as you want them.

Assist feedback is provided by a pair of common-cathode RGB LED. No speakers
or annoying TTS feedback here! With [an optional 3D Printed case and a clear
diffuser cover](/case), the LEDs can be turned into a sleek light bar on the bottom
of the unit for quick and easy confirmation of voice actions, or just use
it bare if you like the "PCB on a wall" aesthetic.

To Use:

  * Install the ESPHome configuration `supersensor.yaml` to a compatible ESP32 devkit (below).
  * Install the ESP32 and sensors into the custom PCB.
  * Power up the SuperSensor, connect to the WiFi AP, and connect it to your network.
  * Install the SuperSensor somewhere that makes sense.
  * Add/adopt the SuperSensor to HomeAssistant using the automatic name.
  * Tune the SuperSensor values to your needs.

For more details, please [see my first blog post on the original SuperSensor project](https://www.boniface.me/posts/the-supersensor/)
and [my update post on version 2.0](https://www.boniface.me/posts/the-supersensor-2.0).

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

   The SR602 was, in my experience, prone to constant false misfirings and hard to enclose due to its shape. In addition its orientation is
   awkward (pins on the left or right side) which made building around it difficult. Thus, this sensor has been replaced with the nicer,
   more straight AM312 PIR design. This does add vertical height to the sensor but I consider this a good tradeoff, and think it looks neat.

   Note that like all cheap PIRs, the AM312 is prone to misfiring if exposed. For this reason, [the case](/case) is recommended for any serious
   PIR use-cases.

3. Completely redesigned the custom PCB around the above sensor changes, which is now more compact in a 50x55mm almost-square configuration.

4. Significantly cleaned up the ESPHome configuration, to support the above sensors and remove a lot of cruft that was caused by the BME680.

## Parts List

| Qty   | Component          | Cost (2025/05 CAD, ex. shipping) | Links |
|-------|--------------------|----------------------------------|-------|
| 1     | GY-SGP41           | $11.08                           | [AliExpress](https://www.aliexpress.com/item/1005006746827606.html)  |
| 1     | GY-SHT45           | $5.67                            | [AliExpress](https://www.aliexpress.com/item/1005008175340220.html)* |
| 1     | AM312              | $1.97                            | [AliExpress](https://www.aliexpress.com/item/1005008067324301.html)  |
| 1     | TSL2591            | $4.59                            | [AliExpress](https://www.aliexpress.com/item/1005008619462097.html)  |
| 1     | HL-LD2510C         | $4.79                            | [AliExpress](https://www.aliexpress.com/item/1005006000579211.html)* |
| 1     | INMP441            | $2.93                            | [AliExpress](https://www.aliexpress.com/item/1005002902615623.html)  |
| 1     | ESP32 HW-395       | $6.67                            | [AliExpress](https://www.aliexpress.com/item/1005006019875837.html)* |
| 2     | RBG LED            | $0.09 ($9.12/100)                | [Amazon](https://www.amazon.ca/dp/B09Y8M2PKS) |
| 1     | 470Ω resistor      | $0.08 ($7.99/100)                | [Amazon](https://www.amazon.ca/dp/B08MKQX2XT) |
| 2     | Female pin header† | $1.59 ($15.99/10)                | [Amazon](https://www.amazon.ca/dp/B08CMNRXJ1) |
| 1     | Custom PCB (JLC)   | $0.69 ($6.89/10)                 | [GitHub](https://github.com/joshuaboniface/supersensor) |
| **TOTAL** |                    | **$41.74**                           |       |

`*` Ensure you select the correct device on the page as it shows multiple options.

`†` One of these sets is optional, and is useful if you do not want to solder the individual sensors directly to the board (see below).

### To Solder or Not To Solder

I strongly encourage anyone building one of these units to leverage sockets for all components. First,
this provides good spacing between components which can help with general better performance. It can
also allow for quick swapping if any turn out to be defective or if future changes are warranted.

**If you use the [case](/case), it is sized assuming socketed components!** So for case users you
must socket all components.

Note that due to the PCB design, you *must socket at least one* set of components - either the ESP32
or the sensors on the front. Due to the positioning and overlap, it would be impossible to solder
everything directly to the board, as the ESP covers several of the solder points of the front
sensors and vice versa.

You can use the provided 40-pin female headers exclusively if you wish, and cut them to length for
the individual sensors as needed, or you can use individually-sized female headers in the following
quantities should you wish for a slightly neater finish:

* 3x 3-pin (AM302, INMP441 x2)
* 2x 4-pin (SGP41, SHT45)
* 1x 5-pin (LD2410C)
* 1x 6-pin (TSL2591)

I will leave it up to the reader to source these specific sizes if they desire (I found all except
a 5-pin on Amazon, and just used a 6-pin with one pin removed).

I still directly solder the RGB LEDs and resistor to the board for simplicity as these very small
leads are not easily socketed, and these components are so inexpensive as to be effectively
disposable along with the PCB should that be required.

### Part Swaps

To save a little money, it is possible to swap out the two Sensirion sensors for their less-feature-
rich peers, with no code changes:

* SGP41 -> SGP40 - removes the NOx functionality
* SHT45 -> SHT40/41/43 - less accuracy

Personally, I do not find the minimal cost savings to be worth sacrificing the extra potential
functionality, so I recommend using the provided models, but this is up to the builder to decide.

No other parts can be easily swapped without code or PCB design changes.

## Air Quality Handling

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
It also reacts strongly to heavy humidity, resulting in higher values in such environments.
These should be used only as a general indication of air quality over short periods, rather
than an absolute reference over long periods (much to my own frustration but inevitable
begruding acceptance).

## Room Health

The SuperSensor 2.0 leverages the outputs of the SHT45 and SGP41 sensors to calculate a
"Room Health", expressed as a percentage, which represents how "healthy" i.e. comfortable
a room is for a person to be in.

The room health is calculated based on the VOC level, temperature, and relative humidity.
First, the raw value is converted to a per-sensor 100-0 scale as follows:

  * For VOC levels, there is a set of linear scales based on common VOC level
    mappings, such that less than 200 ppb is 100, 200-400 maps to 100-90,
    400-600 maps to 90-70, 600-1500 maps to 70-40, 1500-3000 maps to 40-0, and
    greater than 3000 is 0.
  * For temperature and humidity, there is a single linear scale based on a
    configurable penalty value, such that a value between the configurable
    minimum and maximum is 100, and each degree C or %RH outside of that range
    decreases the value by the penalty value.

Next, each indivdual per-sensor value is applied to the total 100-0 value by a configurable
weight, with the defaults being 40% to VOC level, 30% to temperature, and 30% to humidity. The
values can never total more than 100% or total to 0% but are otherwise normalized (i.e. decrease
others before increasing one, or the values will not be accepted; and at least one weight
must be >0).

The final result is thus a 100-0% range that, in broad strokes, describes the overall
health of the room. For some examples, assuming all of the default values below:

   * Perfect: Temp 23C, humidity 50%RH, and VOC level 150ppb = 100% health
   * A little warm: Temp 25C (+1), humidity 50%RH, and VOC level 250ppb = 97% health
   * Dry: Temp 22C, humidity 30%RH (-10), VOC level 150ppb = 91% health
   * Dirty air: Temp 23C, humidity 50%RH, VOC level 800ppb = 85% health
   * Hot & humid: Temp 28C, humidity 70%RH, VOC level 250ppb = 84% health
   * All-around bad: Temp 30C, humidity 30%RH, VOC level 2000ppb = 52% health

These are then mapped to textual values as well with the following bands:

   * 100%-95%: Great
   * 95%-90%: Good
   * 90%-80%: Fair
   * 80%-60%: Poor
   * 60%-0%: Bad

As mentioned above, most portions of this are configurable; see the section below for
specific details of each configuration value.

## Configurable Options

There are several UI-configurable options with the SuperSensor to help you
get the most out of the sensor for your particular use-case.

**Note:** Configuration of the LD2410C is excluded here, as it is extensively
configurable. See [the documentation](https://esphome.io/components/sensor/ld2410.html) for more details on its options.

### Enable Voice Support (switch)

If enabled (the default), the SuperSensor's voice functionality including
wake word will be started, or it can be disabled to use the SuperSensor
purely as a presence/environmental sensor.

### Enable Presence LED (switch)

If enabled (the default), when overall presence is detected, the LEDs will
glow "white" at 15% power to signal presence.

### Temperature Offset (number, -30 to +10 @ 0.1, -5 default)

Allows calibration of the SHT45 temperature sensor with an offset from -30 to +10
degrees C. Useful if the sensor is misreporting actual ambient tempreatures. Due
to internal heating of the SHT45 by the ESP32, this defaults to -5; further
calibration may be needed for your sensors and environment based on an external
reference.

### Humidity Offset (number, -20 to +20 @ 0.1)

Allows calibration of the SHT45 humidity sensor with an offset from -10 to +10
percent relative humidity. Useful if the sensor is misreporting actual humidity
based on an external reference.

### PIR Hold Time (number, 0 to +60 @ 5, 0 default)

The SuperSensor uses an AM312 PIR sensor, which has a stock hold time of ~2.5
seconds. This setting allows increasing that value, with retrigger support, to
up to 60 seconds, allowing the PIR detection to report for longer. 0 represents
"as long as the AM312 fires".

### Light Threshold Control (number, 0 to +200 @ 5, 30 default)

The SuperSensor features a "light presence" binary sensor based on the light
level reported by the TSL2591 sensor. This control defines the minimum lux
value from the sensor to be considered "presence". For instance, if you have
a room that is usually dark at 0-5 lux, but illuminated to 100 lux when a
(non-automated) light switch is turned on, you could set a threshold here
of say 30 lux: then, while the light is on, "light presence" is detected,
and when the light is off, "light presence" is cleared. Light presence can
be used standalone or as part of the integrated occupancy sensor (below).

### Integrated Occupancy Sensor (selector)

The SuperSensor features a fully integrated "occupancy" sensor, which can be
configured to provide exactly the sort of occupancy detection you may want
for your room.

There are 7 options (plus "None"/disabled), with both "detect" and "clear"
handled separately. Occupancy is always detected when ALL of the selected
sensors report detection, and occupancy is always cleared when ANY of the
selected sensors stop reporting detection (logical AND in, logical OR out).

   * PIR + Radar + Light
   * PIR + Radar
   * PIR + Light
   * Radar + Light
   * PIR Only
   * Radar Only
   * Light Only
   * None

### Room Health Sensor

#### Minimum Temperature (number, 15 to 30 @ 0.5, 21 default)

The lower bounds of a fully comfortable temperature; temperature values below
this value will begin decreasing the room health score.

#### Maximum Temperature (number, 15 to 30 @ 0.5, 24 default)

The upper bounds of a fully comfortable temperature; temperature values above
this value will begin decreasing the room health score.

#### Temperature Penalty (number, 1 to 20 @ 1, 10 default)

The penalty value per degree of temperature deviation from ideal levels, applied
to the pre-weighting value for temperature.

#### Minimum Humidity (number, 20 to 80 @ 1, 40 default)

The lower bounds of a fully comfortable relative humidity level; relative
humidity values below this value will begin decreasing the room health score.

#### Maximum Humidity (number, 20 to 80 @ 1, 60 default)

The upper bounds of a fully comfortable relative humidity level; relative
humidity values above this value will begin decreasing the room health score.

#### Humidity Penalty (number, 1 to 10 @ 1, 5 default)

The penalty value per % of relative humidity deviation from ideal levels, applied
to the pre-weighting value for humidity.

#### VOC Weight (number, 0.0 to 1.0, 0.4 default)

The weighting value of the VOC score relative to the other two for calculating
the total room health.

Note: Cannot exceed 0.4 without first decreasing one of the other weights (total max of 1.0).

#### Temperature Weight (number, 0.0 to 1.0, 0.3 default)

The weighting value of the Temperature score relative to the other two for
calculating the total room health.

Note: Cannot exceed 0.3 without first decreasing one of the other weights (total max of 1.0).

#### Humidity Weight (number, 0.0 to 1.0, 0.3 default)

The weighting value of the Humidity score relative to the other two for calculating
the total room health.

Note: Cannot exceed 0.3 without first decreasing one of the other weights (total max of 1.0).
