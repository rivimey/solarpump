Solar powered Water pump controller
===================================

## Introduction

The project has been to put together a water pump powered by a solar panel, with a battery
for reserve power. The need was to pump water from a stream beside allotment garden up to
the allotment for watering plants. The whole unit needs to be completely portable because
the area was was prone to theft.

## Initial version

An initial version was created using off the shelf components, and it worked to a degree.
The solar panel linked to the battery using a cheap solar controller, PWM based, and the
rest of the circuit was wire and switches.

Parts:

 - VerRich Diaphragm pump, 12V Water Pumps 70W Self Priming Pump 6 L/Min

 - Photonic Universe 10A Solar Charge Controller for up to 160W panel.

 - BETOP 60W foldable solar panel kit, 18V.

 - 12V LED Panel Digital Voltmeter Color LED Digital Display
 
 - 12V 20A illuminated rocker switches.

 - 3-pin XLR plugs and sockets for the Solar input


This was constructed with the battery, controller and other wiring inside a wooden box
bought from Amazon, and with the pump screwed to the top (this helps ensure that any
drips of water and electricity don't interact!)

## Second design

A second version was designed with the intention of supporting MPPT conversion and also
to provide additional information about the current flows. This was because placement of
the panel in the prevailing sunlight was easier when you have feedback on what is going
on.

However, this second design was never built -- I was never sure enough it would work to
make it worth getting a PCB built. I had done a lot of reading around the topic of MPPT
converters and was starting to get some idea of the issues and constraints, and could
see that what I had would probably have issues.

I did get as far as a KiCad PCB design, though:

![The abandoned Solarpump v2](/3dpics/solarpump%201.png)

## Second build

A new version was built... mostly using the components of the first version in a larger
box (a two bottle "wine presentation" box with a nice looking, and water repellant, red
varnish). This allowed a much better arrangement of the wiring, and made room for a 12V
Car Accessory connector, which was supposed to be helpful for a camping trip.

For this version, a 3D CAD model was built in Blender 3D to verify the dimensions and
fit.

![Solar pump in its new box](/3dpics/Render_025123.png)


## Third design

An Intervention came along, in the way of the "DIY 1KW MPPT Solar Charge Converter"
build, published on Instructables. This finally showed a path -- to be fair, not
so very different from my earlier attempt but far better explained and, critically,
tested! I resolved to rebuild my earlier design around this base.

The Instructables build is https://www.instructables.com/DIY-1kW-MPPT-Solar-Charge-Controller/
and there are links to the firmware and design files in that article.

A design goal for this version was to increase the flow rate somewhat, as although the
existing pump worked, it was rather slow. I eventually settled on the Seaflo 41 Series
DC Water System Pump 12V 12.5 LPM, which is up to about 2.5x higher flow rate.

Another design goal is to support measurement of water flow rate, so the unit can
keep track of total water pumped and water pumped per session. While not strictly
necessary this was something I felt was important.

Compared to that version, I decided to:

  - use the Solderparty RP2040 Stamp, partly because I had it and like it;

  - use an alternate half-bridge driver, because the 2104/5 is out of stock at present
    and the new one integrates a diode;

  - use an alternate current sensor (ACS758), because the article was quite critical of
    the one suggested (ACS712). I can't tell from the datasheet if it's an improvement,
    though :-(

  - use more current sensors, to avoid needing to estimate current output from current
    input, partly to monitor more things directly;

  - use 3 separate SM power supply devices for local power, rather than the arrangement
    in the original, which looked clunky;

  - use a different, and 8 channel, ADC to measure the extra circuits, and also enabling
    fast 16-bit sampling and faster MCU comms via SPI. It's slightly more expensive but
    not a lot, and overall better;

The big change I added was one I alluded to in an earlier mail: I essentially have 2
separate buck converters now, rather than just one, because I wanted to:

  - support MPPT input (eg. 18V);

  - support a controllable battery voltage for charging/float purposes (eg. 15V);

  - support a controllable output voltage (sep from battery) to e.g. control DC pump
    speed (eg. 9V to 12.5V).

So I've essentially got:  MPPT => [Battery] => Simple Buck => Output.

This circuit then has two, switchable, outputs, rather than one, which I hope I've
designed right this time! The purpose is to support a fixed link to the pump, plus an
'Aux' circuit for e.g. a car cig socket, for use when e.g. camping. My intention is that
the two outputs are not used at the same time, but the circuit allows that option. I
decided that USB power delivery, while nice, would be via a commercial car adapter,
not built in.

I have derated the components slightly compared to the original, supporting only up
to 48V input and up to about current output 18A rather than the 80V and 30A original,
as this is meant to be a portable unit and not destined for huge batteries or panel
arrays. On the battery front I'm intending to change from a 12V lead acid battery to
a 4S3P Lithium one based on twelve 18650 batteries + controller for a 14V output. It
should help reduce the weight (the new pump, now 3 gal/min rather than 1gal/min of
earlier, is heavier!).

The second smaller board is for a 'front panel', which I envisage with a large
"emergency stop" pushbutton, a digital rotary encoder, and a Pimoroni "Pico Display
Hat", which is a small LCD on a 40pin R-Pi Pico footprint with 4 small pushbuttons and
an RGB led. Most of that is interfaced via an MCP 16-pin IO adapter on the I2C bus,
except for the SPI bus for the display itself.

Not shown in the circuit because it's off-board, the main on/off switch will be an
SPST switch between the battery and the PCB.

Some pictures of the build so far (from KiCad):

![PCB for Solarpump v3](/3dpics/solarpumpv3_5r.png)

![PCB for Solarpump v3](/3dpics/solarpumpv3_8r.png)

