# Build Tutorial

Based on Fagundes, M.A.R., Mendonça-Tinti, I., Iescheck, A.L. et al. An open-source low-cost sensor for
SNR-based GNSS
reflectometry: design and long-term validation towards sea-level altimetry. GPS Solut 25,
73 (2021).
<a href="https://doi.org/10.1007/s10291-021-01087-1"/>

<chapter title="Contributors" id="contributors">
This project was developed by the following contributors:
<list>
    <li><a href="mailto:quirkajay@myvuw.ac.nz">Ajay Quirk</a></li>
    <li><a href="mailto:baxter.b.pollard@gmail.com">Baxter Pollard</a></li>
    <li><a href="mailto:jefferiesc@mtaspiring.school.nz">Craig Jefferies</a></li>
    <li><a href="mailto:delwyn.moller@auckland.ac.nz">Delwyn Moller</a></li>
    <li><a href="mailto:bpollard@restorelab.co.nz">Brian Pollard</a></li>
</list>
</chapter>

<chapter title="Parts" id="parts">
The parts list can <a href="Parts-Lit.md">be found here</a>.
</chapter>

<chapter title="1. Preparing Arduino" id="preparing-arduino" collapsible="true">

This chapter will guide you through the process of setting up the Arduino MKR NB 1500 and
changing the GPS output rate.

<procedure title="Table of Contents" id="section1-toc" collapsible="true">
    <step><a href="#arduino-ide-setup">Arduino IDE Setup</a></step>
    <step><a href="#format-sd-card">Format the SD Card</a></step>
    <step><a href="#get-firmware">Get the Firmware</a></step>
    <step><a href="#change-gps-output-rate">Change GPS Output Rate</a></step>
    <step><a href="#upload-firmware">Uploading the Firmware</a></step>
    <step><a href="#c-definitions">C++ Definitions</a></step>
</procedure>

You will need:
<list>
    <li>Arduino MKR NB 1500</li>
    <li>SD Card</li>
</list>


<chapter title="1.1. Arduino IDE Setup" id="arduino-ide-setup">
Start by downloading the Arduino IDE available <a href="https://www.arduino.cc/en/software">at the arduino website
here</a>.

The first thing to do is install the board group from the Board Manager. To do this click the second icon in the
sidebar and search for "Arduino SAMD". The board group you want to install is called "Arduino SAMD Boards
(32-bits ARM Cortex-M0+)", install the latest version of this.
<img src="arduino-board-install.png" style="block" alt="Screenshot of the Arduino IDE showing the board manager with the Arduno SAMD Board highlighted."/>

You can then install the libraries that we will need for this project. We will need the SD library which handles
communication with the SD Card. To do this click the library icon below the boards manager and search for
SD and install it.
![sd-library-install.png](sd-library-install.png)
</chapter>

<chapter title="1.2. Format the SD Card" id="format-sd-card">
Before you can use the SD card with the Arduino you will need to format it. The link below has instructions on
how to do this for both Windows and macOS. Ensure that the SD card is formatted as FAT32 (just FAT if you
are using macOS) so that the Arduino can read it.
<a href="https://support.garmin.com/en-NZ/?faq=QqSbC0YZTz57kLm7uRQxZ7">Garmin support article on formatting SD cards</a>
</chapter>

<chapter title="1.3. Get the firmware" id="get-firmware">
Now that we have the board group and library installed we can upload the firmware to the Arduino. To do this create a
new sketch and copy the code found in the <code>main.cpp</code> file here into the sketch. You will also need to copy
or download the files <code>dropbox.cpp</code>, <code>dropbox.h</code>, <code>main.h</code> and <code>arduino_secrets.h.example</code> 
(see note below) into their own files as well. You can do this by copying the file in GitHub, then click the three dots
near the top right of the Arduino IDE and select "New File". Name the file the same as the one you copied. Do this for
all three files.

<note>
When copying the <code>arduino_secrets.h.example</code> file remove the <code>.example</code> so you should have a file
called <code>arduino_secrets.h</code>. In this file you will need to enter your SIM providers <tooltip term="APN">APN</tooltip>, 
if you are using Spark you can leave this as <code>m2m</code>.
</note>
</chapter>

<chapter title="1.4. Change GPS Output Rate" id="change-gps-output-rate">
The GPS module will output data at a rate of 1Hz by default. This will create a massive amount of data that is not 
needed for this project. Therefore, we will need to change this output rate to 0.2Hz (5 seconds). To do this you will need
a Windows computer with the <a href="https://www.u-blox.com/en/product/u-center">u-center 2.0</a> software installed.

Open u-Center connect the GPS module, if you are using the Sparkfun NEO-M9N from the parts list you can do this by
plugging a USB-C cable into the GPS module and then into your computer. Then click the "devices" icon and then the plus
button.

![u-center-add.png](u-center-add.png)

Then select the COM port that the GPS module is connected to and click "Connect". If everything is working correctly
and you GPS module has a FIX on some satellites you should see a screen like this:

![u-center-main.png](u-center-main.png)

This screen shows all the parsed raw data that is collected from the GPS module. Now click the settings button beside the
plus icon you used to add the module. In the window that opens click the button indicated below to load in the current module configuration,
then search for "CFG-RATE" in the search bar.

![u-center-search.png](u-center-search.png)

Now click "CFG-RATE" in the list, this will bring up the settings panel on the side, under "Value (scaled) s" change the
value to 5 then tick the boxes for "RAM", "BBR" and "Flash" to save the settings to the module's RAM, BBR and Flash memory.
Then click the "Send" button to send the new settings to the module. If everything has updated you should see 3 green
ticks beside the 3 rows in the table.

![u-center-config-set.png](u-center-config-set.png)

You can now close out of u-Center and unplug the GPS module.
</chapter>

<chapter title="1.5. Upload the Firmware" id="upload-firmware">
Now plug the Arduino into your computer and select the board from the board selector beside the upload button. It should
be indicated as "Arduino MKR NB 1500". Then click the upload button (the right arrow icon) to upload the code to the Arduino.
</chapter>

<chapter title="1.6. C++ Definitions" id="c-definitions">
At the top of the file below the #include directives you will see some definitions that you can change if you have 
different configs
<list>
<li><code>#define GPS_BAUD 38400</code> 
- default baud rate for the u-Blox NEO-M9N GPS module, only change if using a different module</li>
<li><code>#define SERIAL_BAUD 9600</code> - 
baud rate for the serial monitor, only change if you want to change the speed of the serial monitor.</li>
<li><code>#define IDLE_THRESHOLD 10</code> - 
threshold for no output from the GPS module to be considered "idle". In milliseconds.</li>
<li><code>#define SDCard 4</code> - 
SD card chip select PIN, only change if not using the MKR SD Shield.</li>
<li><code>#define WAIT_FOR_VALID_GPS</code> - 
comment out if you want to start recording data immediately, even if the GPS module is not ready.</li>
<li><code>#define MAX_BASENAME_LEN (8+1)</code> - 
maximum length of the basename of the log files.</li>
<li><code>#define MAX_FILENAME_LEN (MAX_BASENAME_LEN + 1 +3)</code> - 
maximum length of the log file name including extension</li>
<li><code>#define DEBUG</code> - 
comment out if you want to disable debug messages, recommend to comment out when confirmed working.</li>
<li><code>#define GPS_BUFFER_SIZE_TYPICAL 512</code> - 
typical size of the GPS <tooltip term="NMEA">NMEA</tooltip>, used for buffer pre-allocation</li>
</list>
</chapter>
</chapter>

[//]: # (Chapter 2 connecting electronics)

<chapter title="2. Connecting the Electronics" id="connecting-electronics" collapsible="true">

In this chapter we will guide you through the process of connecting the Arduino, SD Shield and GPS Module together. As
well as how to set up the cellular connection and the power system.

<procedure title="Table of Contents" id="section2-toc" collapsible="true">
    <step><a href="#arduino-sd-shield">Connecting the Arduino and SD Shield</a></step>
    <step><a href="#arduino-gps-module">Connecting the Arduino and GPS Module</a></step>
</procedure>


<chapter title="2.1. Connecting the Arduino and SD Shield" id="arduino-sd-shield">
You will need
<list type="decimal">
<li>Arduino MKR NB 1500</li>
<li>Arduino MKR SD Shield</li>
<li>Micro SD Card</li>
</list>

Start by plugging the SD Shield into the Arduino. To do this simply line the pins up on the SD shield to the pins on the
NB 1500 and push them together. The labels should line up between the two boards, e.g. 5V to 5V. As a general guide the SD
card slot should face away from the Micro USB port on the arduino. If you have your SD card formatted 
then now is the time to insert the SD card into the slot.

![arduino-stack.jpeg](arduino-stack.jpeg)
</chapter>

<chapter title="2.2. Connecting the Arduino and GPS Module" id="arduino-gps-module">
You will need
<list type="decimal">
<li>Sparkfun NEO-M9N</li>
<li>Header Pins</li>
<li>Breadboard</li>
<li>Arduino MKR NB 1500</li>
<li>Jumper Wires</li>
<li>GNSS Antenna</li>
</list>

To connect the GPS module to the Arduino you will first need to solder the header pins to the GPS module. To do this cut
the header pins to the correct length and solder them to the GPS module. The pins should be soldered to the empty row that
is on the USB port side of the PCB. Then put the header pins into the breadboard so that the longer side of the pings goes
into the breadboard, then place the GPS module on top of the header pins and solder to the board.

![solder-neo-m9n.jpeg](solder-neo-m9n.jpeg)

Once it has been soldered you can plug the GPS module and Arduino into the breadboard. Place the Arduino at one end of
the breadboard and the GPS module at the other end.

| ![gps-placement-bad.jpeg](gps-placement-bad.jpeg)     | ![gps-placement-good.jpeg](gps-placement-good.jpeg)    |
|-------------------------------------------------------|--------------------------------------------------------|
| Example of bad placement of the board and GPS Module. | Example of good placement of the board and GPS module. |

<warning>
    Placing the Arduino and GPS module so that some of the pins are on the same track can fry your board or pins.
</warning>

You can now connect the two using jumper wires and the following connection list
<list>
    <li>GPS Module VCC -> Arduino 3.3V</li>
    <li>GPS Module GND -> Arduino GND</li>
    <li>GPS Module RX  -> Arduino TX</li>
    <li>GPS Module TX  -> Arduino RX</li>
</list>
</chapter>
</chapter>

<chapter title="3. Power System" id="power-system" collapsible="true">
In this section we will guide you through the process of preparing the solar panel and battery system to power the Arduino.

<procedure title="Table of Contents" id="section3-toc" collapsible="true">
    <step><a href="#preparing-solar-panels">Preparing the Solar Panels</a></step>
    <step><a href="#preparing-battery-system">Preparing the Battery System</a></step>
    <step><a href="#connect-arduino-power">Connect the Arduino Power</a></step>
</procedure>
<chapter title="3.1. Preparing the Solar Panels" id="preparing-solar-panels">
You will need
<list>
    <li>Solar Panels</li>
    <li>2x Screw Terminal Blocks</li>
    <li>Wire Stripper</li>
    <li>Screwdriver</li>
</list>

Start by connecting the two solar panels together. To do this simply connect one of the wires from one panel to the other.
They are keyed so that you can only connect positive to negative.
<img src="solar-panels-daisy-chain.jpeg" alt="Photo showing the two solar panels connected together."/>

Once they are connected you can now connect the screw terminal blocks to the wires. To do this strip about 10mm of insulation
off the end of each wire using the wire stripper. Then loosen the screws on the terminal block and insert the wires into the
holes. Finally tighten the screws to secure the wires in place.
<img src="solar-panel-terminal-block.jpeg" alt="Photo showing the solar panel wires connected to the screw terminal block."/>
</chapter>

<chapter title="3.2. Preparing the Battery System" id="preparing-battery-system">
You will need
<list>
    <li>Prepared Solar Panels</li>
    <li>12V/24V solar panel charge controller</li>
    <li>2x 12V batteries</li>
    <li>Multimeter</li>
    <li>12AWG wire</li>
</list>
We need to connect the batteries in parallel to increase the capacity of the battery system. To do this 
<warning>
Pls finish this when screwdriver gets here
</warning>
<warning>
Bit about fuse goes here
</warning>

Next connect the battery's to the charge controller. In the case of the charge controller in the parts list you just need
to connect the positive terminal of the battery system to the positive battery terminal on the charge controller, and the negative
terminal of the battery system to the negative battery terminal on the charge controller. Use the terminal screw blocks
to connect them.

<img src="battery-connection-solar.png" alt="Photo showing the connection between the battery and solar controller."/>

Now we can connect the solar panels to the charge controller. First use a multimeter to find out which wire is positive
and negative. You will need to set the multimeter to read DC voltage, then place the red probe on one wire and the black probe on the other.
If they are around the correct way you will see a positive voltage readout.

Once you have identified the positive and negative wires you can connect them to the charge controller the same way as the battery.

On the recommended solar controller, the solar panel terminals are to the left of the battery terminals. 

<img src="solar-connection.png" alt="Photo showing the connection between the solar panels and solar controller."/>

</chapter>
<chapter title="3.3. Connect the Arduino Power" id="connect-arduino-power">
You will need
<list>
    <li>Micro USB Cable</li>
    <li>Wire Strippers</li>
    <li>Screw Driver</li>
    <li>Prepared solar/battery system</li>
    <li>Screw Terminal Blocks</li>
    <li>Multi-voltage Adaptor</li>
</list>

We will start by wiring the USB cable up to the multi-voltage adaptor. To do this cut the USB cable near the USB-A end
Then strip about 10mm of insulation off the outer sheath of the cable to expose the inner wires. You should see four wires inside,
red, black, white and green. Strip about 5mm of insulation off the red and black wires only, the white and green wires are not needed.
Connect this to a screw terminal, on the other side of the screw terminal, connect to the output side of the multi-voltage
adaptor.

On the multi-voltage adaptor make sure that the plastic jumper has 5V selected, as shown below in the photo.

Now connect the input side of the multi-voltage adaptor to the battery system. On the recommended solar controller
the load terminals are to the right of the battery terminals.

<img src="arduino-power-solar.png" alt="Photo showing the multi-voltage adaptor connected to the solar controller."/>

</chapter>
</chapter>

<chapter title="4. Mounting the System" id="mounting-system" collapsible="true">
<chapter title="4.1. Prepare the Enclosure" id="prepare-enclosure">
    You will need
    <list>
        <li>Prepared Electronics on Breadboard</li>
        <li>Waterproof Enclosure</li>
        <li>Drill</li>
        <li>Drill Bits</li>
        <li>Screwdriver</li>
        <li>Grommets</li>
    </list>
First of all drill two holes through the side of the box for the power cable and the GNSS antenna cable. If using the
grommets from the parts list you will need a 16mm drill bit. Once the holes are drilled insert the grommets into the holes,
and pass the cables through the grommets. Then mount the breadboard inside the enclosure using double-sided tape or
Velcro strips. Connect the power cable to the arduino board and the GNSS antenna to the GPS module. Finally close by screwing 
the lid onto the enclosure. Ensure when before you close the lid that the rubber seal on the clear lid is seated properly
so that the enclosure is waterproof.

The best place for the electronics box to be stored is alongside the power system enclosure so that the cables can reach 
and reduce the amount of points that water can enter.
</chapter>
<chapter title="4.2. Solar Panel Mounting" id="solar-panel-mounting">
    You will need
    <list>
        <li>Solar Panel</li>
        <li>Screws</li>
        <li>Drill</li>
        <li>6x pieces of wood</li>
    </list>
To mount the solar panel you will need to create a stand for them to go on. For our Deployment we created an A-frame style
stand using 6 pieces of 2x4 timber and one 4x4. We used the 4x4 as the connector between the two legs at the top,
where we screwed in hinges to allow the stand to fold flat for transport. We then used zip ties to secure the solar panels to the
stand. We also made the front legs of the stand shorter than the back legs to give the solar panels tilt upwards.

<img src="solar-panel-design-front.png" alt="Design of the solar panel stand from a front side on view."/>
<img src="solar-panel-drawing-top.png" alt="Design of the solar panel stand from top-down view, showing where hinges go."/>
<note>
The fusion 360 file for this can be found here: <a href="https://a360.co/4hYwEia"/>
</note>
</chapter>
<chapter title="4.2. Mount the GNSS Antenna" id="mount-gnss-antenna">
    You will need
    <list>
        <li>GNSS Antenna with Cable</li>
        <li>Antenna Mast</li>
        <li>Antenna Mast Mount (Optional)</li>
    </list>
Once again this is upto your deployment for the best way of doing this. For our deployment we had some students build a 
custom mast stand out of metal. However, you can also buy one online. We have one linked in our <a href="Parts-Lit.md">parts list</a>.

Once you have the mast you will need to mount the antenna to the top of the mast. We did this using zip ties and duct tape.
If your mast has guide wires make sure to install these before mounting the antenna. Ensure they are tight and secure so
that the mast does not sway in the wind. The easiest way to do this is use some heavy-duty stakes.
</chapter>
</chapter>

<chapter title="5. Testing the System" id="testing-system" collapsible="true">
<chapter title="5.1. Activity LEDs" id="activity-led">
There is a green LED on the Arduino that will flash every 5 seconds to indicate when data is being written to the SD card.
There is also a red LED on the GPS module, this should be constantly on when the GPS module is powered on to indicate power,
it also has a blue LED that will flash when the GPS module has a fix on the satellites. This can take a few minutes, and
may not work if the GPS module is indoors or in a place with poor satellite visibility.


<note>Add photo highlighting these LEDs</note>
</chapter>

<chapter title="5.2. Serial Monitor" id="serial-monitor">
You can also check that the NMEA sentences look correct and are being received by the Arduino by opening the serial monitor.
To do this follow the steps for plugging the Arduino into the computer from <a href="#upload-firmware"> 1.7. Uploading the Firmware</a>.
Then click the magnifying glass icon in the top right of the Arduino IDE to open the serial monitor, the baud rate should
be set to 9600. You should see the NMEA sentences being printed to the serial monitor every 5 seconds.

If you have the <code>#define DEBUG</code> enabled in the <code>main.cpp</code> file you will also see debug messages
such as the GPS timestamp and file name.
</chapter>

<chapter title="5.3. Data Files" id="data-files">
The files that are saved to the SD card will have an .LOG extension. The file names will look like the files below

![log-file-example.png](log-file-example.png)

Where the first two digits are the year, the second two digits are the month, and the last two digits are the day.
This date is in UTC.

The data in the file should look like the below example.

<code-block lang="plain text">
$GNGGA,000058.000,4443.0116,S,16910.7098,E,2,16,0.66,345.6,M,6.1,M,,*5D
$GPGSV,3,1,12,03,62,297,47,26,59,075,49,31,58,126,48,04,52,236,46*77
$GPGSV,3,2,12,16,40,018,44,28,25,119,43,50,23,307,39,09,14,243,40*75
$GPGSV,3,3,12,06,09,222,45,29,06,131,39,195,06,330,32,194,,,25*43
$GLGSV,2,1,08,80,80,003,42,70,71,203,30,79,39,142,44,69,37,054,44*61
$GLGSV,2,2,08,73,24,330,37,71,20,221,35,87,08,198,24,86,04,244,28*6B
$GNRMC,000058.000,A,4443.0116,S,16910.7098,E,0.01,0.00,310524,,,D*6D

$GNGGA,000108.000,4443.0116,S,16910.7098,E,2,16,0.66,345.6,M,6.1,M,,*59
$GPGSV,3,1,12,03,61,298,46,26,59,075,49,31,58,126,48,04,52,236,47*7B
$GPGSV,3,2,12,16,40,018,44,28,25,119,44,50,23,307,40,09,14,243,39*72
$GPGSV,3,3,12,06,09,222,47,29,06,131,39,195,06,330,38,194,,,*4C
$GLGSV,2,1,08,80,80,003,43,70,71,203,30,79,39,142,45,69,37,054,44*61
$GLGSV,2,2,08,73,24,330,37,71,20,221,36,87,08,198,24,86,04,244,26*66
$GNRMC,000108.000,A,4443.0116,S,16910.7098,E,0.02,0.00,310524,,,D*6A
</code-block>
</chapter>
</chapter>