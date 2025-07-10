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


</chapter>