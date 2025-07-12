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

This chapter will guide you through the process of setting up the Arduino MKR NB 1500, creating a Dropbox app, and 
changing the GPS output rate.

<procedure title="Table of Contents" id="section1-toc" collapsible="true">
    <step><a href="#arduino-ide-setup">Arduino IDE Setup</a></step>
    <step><a href="#format-sd-card">Format the SD Card</a></step>
    <step><a href="#get-firmware">Get the Firmware</a></step>
    <step><a href="#dropbox-key">Dropbox Key</a></step>
    <step><a href="#change-gps-output-rate">Change GPS Output Rate</a></step>
    <step><a href="#install-cellular-library">Install Cellular Library</a></step>
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

You can then install the library that we will need for this project. We will need the SD library which handles
communication with the SD Card. To do this click the library icon below the boards manager and search for
SD and install it. You will also need to install the ArduinoJSON library which is used to parse the JSON from
the Dropbox API. To do this search for "ArduinoJSON" and install the latest version.
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

<chapter title="1.4. Dropbox Key" id="dropbox-key">
To get a Dropbox API key you will need a Dropbox account, and to create an app. To create an app go to <a href="https://dropbox.com/developers">
Dropbox Developers</a> and click "Create App" and sign in if you are not already. Select "Scoped access" and then "App Folder",
then give your app a name and click "Create App".

![dropbox_create.png](dropbox_create.png)

Once you have created the app you will be taken to the app settings page, click on "Permissions" in the top bar below the app name.
Add the permissions <code>files.content.write</code> and <code>files.content.read</code> permissions. Click "Submit" to save the changes.

![dropbox_permissions.png](dropbox_permissions.png)

Then go back to the settings page and scroll down to the "OAuth 2" section. Here you will find your app key and secret.
(you will need to click "Show" to see the secret). Copy these into the <code>arduino_secrets.h</code> file in
<code>#define DROPBOX_APP_KEY</code> and <code>#define DROPBOX_APP_SECRET</code> lines. You will also need to generate a 
first time Dropbox access token, using the python script found on the GitHub: <a href="https://github.com/AjayACST/gnss-ir-nz/blob/main/python/dropbox/main.py">
dropbox/main.py</a>. If you have Python installed you can use your terminal to run the script, or you can use our online
compute environment at <a href="https://hub.grss-gnssir.org">hub.grss-gnssir.org</a>. More information on this here.

If you are using your own computer you will need to install the <code>dropbox</code> library using pip inside your terminal:
```bash
pip install dropbox
```
Then run the script with:
```bash
python main.py
```
<note>
Depending on your system you may need to use <code>python3</code> instead of <code>python</code>.
</note>

The script will ask you to paste in your app key and secret, then it will give you a URL to visit in your browser. This
will ask you to log in to your Dropbox account and then give you a code to paste back into the script. Once you have
done this you should see a message saying "Dropbox credentials saved to: ..." and a file called <code>dropbox.txt</code>
will be created in the same directory as the script. You will need to copy this file to the root of your SD card after
formatting it.

<note>If you are using our online compute environment right-click on the file and select "Download" to get it on your device.</note>

![dropbox_credentials.png](dropbox_credentials.png)
</chapter>

<chapter title="1.5. Change GPS Output Rate" id="change-gps-output-rate">
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

<chapter title="1.6. Install Cellular Library" id="install-cellular-library">
You will need to install one more library before uploading the firmware. To do this download this zip file of code: 
<a href="https://github.com/techstudio-design/SimpleNB/archive/refs/heads/master.zip"> GitHub - SimpleNB</a>.
In your Arduino IDE click the "Sketch" menu and then "Include Library" -> "Add .ZIP Library...". Select the zip file you
just downloaded. Once you have the library installed check that the code compiles by clicking the checkmark icon in the
top left. If it compiles without any errors you are ready to upload the code to the Arduino.
</chapter>

<chapter title="1.7. Upload the Firmware" id="upload-firmware">
Now plug the Arduino into your computer and select the board from the board selector beside the upload button. It should
be indicated as "Arduino MKR NB 1500". Then click the upload button (the right arrow icon) to upload the code to the Arduino.
</chapter>

<chapter title="1.8. C++ Definitions" id="c-definitions">
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
    <step><a href="#cell-connection">Cellular Connection</a></step>
    <step><a href="#power-system">Preparing the Power System</a></step>
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
card slot should face away from the Micro USB port on the arduino. If you have your SD card formatted and dropbox
credentials loaded then now is the time to insert the SD card into the slot.

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

<chapter title="2.3. Cellular Connection" id="cell-connection">
    <note>
        Add in info about how to get SIM card.
    </note>
    
To start break the first layer off the SIM card so that you are left with the second-biggest size, the Micro Size.
Then insert the sim card into the SIM slot on the back of the Arduino. The SIM slot is on the back of the board at the
opposite end to the Micro USB port. The sim card should be inserted with the gold contacts facing up.

Then plug the Laird antenna into the U.FL connector on the Arduino. This is the small circle connector on the front of
the board, above the SIM card slot. THis connector can be a bit tricky to plug in, so be careful when plugging it in. It
can also come out easily so we would recommend using a small amount of hot glue or tape to hold it in place once you have
confirmed that everything is working.
    <note>
        Add in photo of the complete board
    </note>
</chapter>


<chapter title="2.4. Preparing the Power System" id="power-system">
    <note>
        Euan to write or give information to write this up.
    </note>
</chapter>
</chapter>

<chapter title="3. Mounting the System" id="mounting-system" collapsible="true">
    <note>
        Need to finalise this section and what we are doing with it
    </note>
</chapter>

<chapter title="4. Testing the System" id="testing-system" collapsible="true">
<chapter title="4.1. Activity LEDs" id="activity-led">
There is a green LED on the Arduino that will flash every 5 seconds to indicate when data is being written to the SD card.
There is also a red LED on the GPS module, this should be constantly on when the GPS module is powered on to indicate power,
it also has a blue LED that will flash when the GPS module has a fix on the satellites. This can take a few minutes, and
may not work if the GPS module is indoors or in a place with poor satellite visibility.

The green LED on the Arduino will also turn solid to indicate when it is uploading a file to Dropbox. This will happen
when the GPS module gets a fix on the satellites and every hour after that.

<note>Add photo highlighting these LEDs</note>
</chapter>

<chapter title="4.2. Serial Monitor" id="serial-monitor">
You can also check that the NMEA sentences look correct and are being received by the Arduino by opening the serial monitor.
To do this follow the steps for plugging the Arduino into the computer from <a href="#upload-firmware"> 1.7. Uploading the Firmware</a>.
Then click the magnifying glass icon in the top right of the Arduino IDE to open the serial monitor, the baud rate should
be set to 9600. You should see the NMEA sentences being printed to the serial monitor every 5 seconds.

If you have the <code>#define DEBUG</code> enabled in the <code>main.cpp</code> file you will also see debug messages
such as the GPS timestamp, file name and data messages from Dropbox.
</chapter>

<chapter title="4.3. Data Files" id="data-files">
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