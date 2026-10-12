### How it started
A while ago, I came across a break outboard featuring a capacitive scroll wheel, similar to the one from [PCB gadgets](https://pcbgadgets.com/index.php?route=product/product&limit=15&product_id=54).
After a little investigation, I found several different variations of this design. Some had funky, squiggly lines between capacitive pads. This made sense to me, since they could potetially improve finger detection, when the finger rested between two pads. 
Since this idea had stayed with me for quite a while, I spontaneously decided this weekend that I want to try it myself. So I looked for parts lying around in my tinkering box that might be suitable for my quest.
In this treasure trove I found a Seeed Xiao ESP32-S3, some solid-core wire, orange LEDs, resistors, and some copper tape. This was good enough for a start. 

Before I started tinkering, I wanted to understand how capacitive sensing can detect when a sensor is touched. 

In a nutshell, a finger influences the electric field around a sensor. When a finger approaches a sensor, it changes the effective capacitance of the sensor. This change can be measured. Tohe relationship between, voltage, and capacitance is described by

$$I(t) = C \frac{dV(t)}{dt}$$

for a constant capacitance, this can be rearranged to

$$C = \frac{I(t)}{\frac{dV(t)}{dt}}$$

This allows us to derive capacitance from the instantaneous current I(t) divided by the rate of change of voltage dV(t)/dt across the capacitor or sensor.

However, since I am using an ESP32-S3, I can can take advantage of its [integrated capacitive touch hardware](https://docs.espressif.com/projects/esp-idf/en/v5.5/esp32s3/api-reference/peripherals/cap_touch_sens.html?utm_source=chatgpt.com). Pins [D0 to D5 and D8 to D12](https://wiki.seeedstudio.com/xiao_esp32s3_pin_multiplexing/) can be used for touch sensing. 

To understand what the ESP does, the formula above needs to be rearranged again:

$$\frac{dV(t)}{dt} = \frac{I(t)}{C}$$

under the assumtion of constant current I and capacitance C over the measured interval, the voltage change can be expressed as follows:

$$\Delta V = \frac{I}{C} \Delta t$$

Rearranging this for time gives:

$$\Delta t = \frac{C \Delta V}{I}$$

So under the assumtion of a constant currant, I, and a fixed voltage, delta_V, a smaller capacitance charges faster than a larger one.

1. When we connect a piece of copper tape to a touch enabled GPIO pin, such as. D0 on the Xiao ESP32-S3, the electrode forms a electrical capacitive system with its surroundings. 
2. The touch sensing hardare of the ESP repeatedly charges and discharges this capacitor. 
3. When a finger approaches the copper tape, it changes the effective capacitance. This leads to a change in length of the charge and discharge cycle. 
4. The touch hardware measures the time needed for a fixed number of charge-discharge cycles and returns this time as raw data. By monitoring changes in this value, it is possible to detect, when a finger approaches or touches the sonsor.
