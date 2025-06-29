# ESP32-C3 CAD Assignment

This assignment introduces students to microcontroller-based PCB design using the ESP32-C3. By completing this assignment, students will gain hands-on experience with schematic creation, footprint assignment, PCB layout, and firmware programming using KiCad and the Arduino IDE.

## Schematic Guide:
First thing is to download KiCad: https://www.kicad.org
Create a new project called `[netid]_esp32c3DevBoard` and open `[netid]_esp32c3DevBoard.sch`.
### Microcontroller Setup
When designing a PCB with a microcontroller unit (MCU), the best place to start is its datasheet. There is crucial information regarding the requirements for a MCU to function defined in this documentation. For this particular assignment, it can be found in the following link.
https://www.espressif.com/sites/default/files/documentation/esp32-c3_datasheet_en.pdf

Hit `a` to open the component add dialog. Search "ESP32-C3" for an ESP32-C3 in an QFN package and click "ok" to place the part.

<figure align="center">
  <img src="Images/schematics/mcu.png" alt="ESP32-C3 Selection" />
  <figcaption>ESP32-C3 Selection</figcaption>
</figure>


While you're here, add the following components
* `Conn_01x04_Pin`
* `Conn_01x02_Pin`
* `R` for resistors
* `C` for capacitors
* `L` for inductors
* `GND` for a general ground
  
Use `w` to start routing a new wire under the mouse, left-click to finish routing, and `Esc` to cancel. For a cleaner schematic, use labels to connect pins to the microcontroller. Use `l` to place labels on pins or wires. Any two points with the same label will behave as if connected by a wire. Use `Q` for pins you will leave unconnected.

Begin connecting components using wires as in the picture below. You can change the values of the passive components by hovering your cursor over and double-clicking or pressing `v`. Complete the schematic for the MCU following what is shown:

<figure align="center">
  <img src="Images/schematics/mcu-schematic.png" alt="Schematic for ESP32-C3" />
</figure>


### USB-C Setup
Add the following components to your schematic to create the USB-C component.

* `USB_C_Receptacle_USB2.0_16P`
* `ESD9B5.0ST5G`
* `R`, 2 resistors, they must have the value of `5.1kOhm` to meet USB-C specifications.
The schematic for the USB-C Setup is below. Please make sure that your labels for `D-` and `D+` pins are exactly `USB_D_N` and `USB_D_P` respectively.
<figure align="center">
  <img src="Images/schematics/usb-schematic.png" alt="Schematic for USB-C" />
</figure>


### Power Setup
Since the ESP32-C3 operates at `3.3V` we will use a voltage regulator to convert `5V` from the USB-C connection to our desired value. Add the following 

* `AMS1117-3.3`
* `C`, 2 capacitors of with values of `22uF`
<figure align="center">
  <img src="Images/schematics/ldo-schematic.png" alt="Schematic for linear dropout regulator" />
</figure>
The convention to follow when placing components in a block is for power/signal to flow from left to right.


### Motor Controller Setup

* `L293`. This is the motor controller that is being used.
* `Conn_01x04_Pin`. These are the output driving signals for a motor
* `R` for a resistor value of `0 Ohms`. When assembling your PCB, this can be left open if your motor is operating at `5V`, or it can be connect to an external power source if your motor requires a different voltage level. Please refer the the datasheet for this component for further details. https://www.ti.com/lit/ds/symlink/l293.pdf
* `Conn_01x02_Pin`. This connector will be an input for a voltage source if the resistor is left open, or a test point if a `0 Ohm` resistor is used.
<figure align="center">
  <img src="Images/schematics/motor-controller.png" alt="L293 Motor Controller Schematic" />
</figure>

### Antenna Setup
The ESP32 offers wireless capability,  which requires the use an antenna that resonates at 2.4GHz. Add the following parts, and create the schematic shown below.

* `Antenna`
* `C` for two `3.2pF` capacitors
* `L` for a `2.2nH` inductor
<figure align="center">
  <img src="Images/schematics/antenna.png" alt="Antenna Schematic" />
</figure>

For the best performance, you will want to implement a  matching networking. The network we are using is often called a `Pi` network. The values for these components may be tuned for optimal performance, but that requires more rigorous testing and experimentation. For now we will use the matching network shown above:

### Bootstrapping Pins and Reset Button
Add the following parts for this portion:
* `SW_DIP_x02` 
* `SW_Push`
* `R` for three pull up resistors with values `10kOhm`. 
* `C` for a `1uF` capacitor
The final schematic should look like this:
<figure align="center">
  <img src="Images/schematics/bootstrap.png" alt="BootStrapping Pins and Reset Schematic" />
</figure>
The switches on the left define the boot modes for the ESP32. A 'bootmode' refers to the various ways a microcontroller can be configured and initialized, often defined by the physical state of hardware pins. Your firmware must match the specific bootmode that a microcontroller is configured to or else it will not work. A common and unfixable mistake is when a board is designed such that its bootmode pins are permanently configured in an invalid way. Switches and buttons to configure your MCU give your design options and flexibility to program, mitigating risk in your PCB designs. Details on the ESP32C3's boot mode can be found on page 29 of its datasheet.

The reset button is setup with a debouncing circuit to filter out parasitic transitions caused by button presses. A deeper explanation of this is provided under `Peripherals` of the following webpage: https://courses.grainger.illinois.edu/ece445/wiki/#/kicad/index.md


### JTAG/UART Setup
For this board, JTAG and UART will be setup for debugging and programming respectively. Add the following components:
* `Conn_01x04_Pin`
* `R` for two resistors with values `10kOhm`. If you intend to use JTAG, do not include these resistors for now. these resistors are optional if you decide to use them as pull up resistors to support `I2C` peripherals. 
The pins connected from the ESP32C3 can be used as `GPIO` pins. For now, the schematic should look like this:
<figure align="center">
  <img src="Images/schematics/jtag-uart.png" alt="JTAG/UART Schematic" />
</figure>

### Crystal Setup
The ESP32 requires a `40MHz` crystal. We will be using the `ECS-400-10-37B2-CKY-TR` crystal. However, this part is not natively found in the KiCAD library. So we will have to import it from online. 

* A schematic symbol and footprint can be found on websites such as `www.snapeda.com` or `app.ultralibrarian.com`. Oftentimes, vendors will include a link to a parts model under `EDA/CAD Models` shown below:
<figure align="center">
  <img src="Images/schematics/cad-model.png" alt="Vendor Linking Models for usage" />
</figure>
You may have to import CAD models for specific component in your teams projects. For this part, the following links will allow you to download a CAD model assuming that they are not deprecated.

* https://www.digikey.com/en/products/detail/ecs-inc/ECS-400-10-37B2-CKY-TR/14548829
* https://www.snapeda.com/parts/ECS-400-10-37B2-CKY-TR/ECS/view-part/?ref=digikey

Make sure to download a `2D Model` for `KiCAD (V6 or later)`.
<figure align="center">
  <img src="Images/schematics/download-model.png" alt="" />
</figure>

Once downloaded, open KiCad and in the top toolbar, click on `Symbol Editor`.
<figure align="center">
  <img src="Images/schematics/toolbar.png" alt="" />
</figure>

From there, search `Device`, left click on it, then go to `File > Import > Symbol`. This should open your file editor, go to the directory where you downloaded the ECAD model and select the file that ends with `.kicad_sym`. This will add the schematic symbol of the crystal to your symbol editor. Make sure to save after this. Your symbol editor should look something like this:
<figure align="center">
  <img src="Images/schematics/symbol-editor.png" alt="" />
</figure>
Now you can finish your schematic adding the following components:

* `ECS-400-10-37B2-CKY-TR`
* `C` for 10pF capacitors
  
Your final crystal schematic should look like this
<figure align="center">
  <img src="Images/schematics/crystal.png" alt="Crystal Schematic" />
</figure>

### Finalizing Schematics:
Before moving on to the next stage, we will clean up and document our schematic. Since screenshots of your schematics will go into your design document, you should put some effort into keeping them neat and easy to read. Use the graphic line tool to divide up your schematic, it is in the righthand toolbar or you can press `i` to draw graphical lines. Use the text tool to label each of these blocks.
<figure align="center">
  <img src="Images/schematics/right-toolbar.png" alt="Grahical Tools" />
</figure>

<figure align="center">
  <img src="Images/schematics/complete-schematic.png" alt="Complete Schematic" />
</figure>

### Footprint Assignments
After defining the schematics, it's now time to assign physical footprints to each component. A footprint defines the area where a component will be mounted and soldered onto the board, ensuring proper electrical connections and mechanical attachment. It is a layout of pad, outlines, and markings on a PCB that matches the physical dimensions and pin configuration of a specific electronic component. Click on the footprint assigment tool in the the toolbar:

<figure align="center">
  <img src="Images/schematics/footprint-toolbar.png" alt="Footprint Editor Toolbar" />
</figure>

KiCAD will bring up the footprint association window, containing three panes. The left pane contains the categories of footprints, the middle pane contains your parts that need footprints assigned to them, and the right pane contains available footprints based on your filter options.

We'll start with the capacitors. We have 0805 sized capacitors that we will solder by hand, so double-click Capacitor_SMD:C_0805_..._HandSolder. Pay attention to the filter options at the top of the window.

Assignment the following components to the rest of the board:
* `RF_Antenna:Johanson_2450AT43F0100`
* `Button_Switch_THT:SW_DIP_SPSTx02_Slide_9.78x7.26mm_W7.62mm_P2.54mm`
* `Diode_SMD:D_SOD-923` 
* `Connector_USB:USB_C_Receptacle_G-Switch_GT-USB-7010ASV`
* `Connector_PinHeader_2.54mm:PinHeader_1x04_P2.54mm_Vertical`
* `Connector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_Vertical`
* `Inductor_SMD:L_0805_2012Metric`
* `Resistor_SMD:R_0805_2012Metric`
* `Button_Switch_SMD:SW_SPST_B3U-1000P`
* `Package_DFN_QFN:QFN-32-1EP_5x5mm_P0.5mm_EP3.7x3.7mm`
* `Package_TO_SOT_SMD:SOT-223-3_TabPin2`
* `Package_DIP:DIP-16_W7.62mm`
* `Crystal:XTAL_ECS-400-10-37B2-CKY-TR` This is the crystal that you imported from earlier.

The final assignments should look like this:

<figure align="center">
  <img src="Images/schematics/footprint_assignments.png" alt="" />
</figure>

## Layout Guide
Now we will switch to the physical layout of the board. From the schematic view, click on the following icon to switch views:

<figure align="center">
  <img src="Images/schematics/layout-2-schematic.png" alt="Switch to PCB Layout" />
</figure>
Start by clicking the "Update PCB with changes made to schematic" button and place the footprints somewhere in the middle of the page. If you make changes to your schematic later on, you should use this button.

<figure align="center">
  <img src="Images/schematics/pull-updates.png" alt="Pull Updates from PCB" />
</figure>
After initially pulling in footprints from your schematic, it should look something like this:
<figure align="center">
  <img src="Images/layout/update.png" alt="Initial Layout" />
</figure>

The blue lines are unrouted nets and indicatew what component pads are going to be connected together. To start, lets define the physical cutout of the PCB. You can do this by drawing a shape with "Edge.Cuts" selected in the toolbar. 

<figure align="center">
  <img src="Images/layout/edge-cuts.png" alt="Define Edge Cuts" />
</figure>

Drawing a rectangle of size `50mmx75mm`, your layout should look something like this:
<figure align="center">
  <img src="Images/layout/edge-cut-defined.png" alt="" />
</figure>

To make things a bit easier, we will add a ground plane to the front and back side of our board that will automatically connect all GND nets together for us. Any areas of filled in copper like this are known as zones in KiCAD. We can do so by selecting `F.Cu` and `"Draw Filled Zones"` in the toolbar

<figure align="center">
  <img src="Images/layout/gnd-planes.png" alt="Define Ground Planes" />
</figure>

Define the front and back ground planes to match the dimensions of your edge cut layer so that your layout looks like this:

<figure align="center">
  <img src="Images/layout/front-ground.png" alt="Front and Back Ground planes define. The back plane is defined, but it is not visible since it is covered by the front ground plane. The back ground plane can be viewed by toggling the visibility of the front ground plane in the toolbar." />
</figure>

Now we can begin sorting the footprints. This is generally where most of the time designing a PCB should be spent. The goal here is to untangle the footprints so that airwires cross as little as possible. This makes routing in the next section simpler and helps to keep traces short. To save some time, below is a suggested layout for your board:
<figure align="center">
  <img src="Images/layout/suggested-position.png" alt="Suggested layout." />
</figure>

As a note, to rotate a footprint to 45 degrees, go to `Settings> PCB Editor > Editing Options` and change the value in the box labeled "Step for rotate commands" to 45.
<figure align="center">
  <img src="Images/layout/rot-45.png" alt="" />
</figure>

When you've placed all your components, make sure the component references are visible and not obscured by other footprints. You will use these references when populating the board.
## Routing:
The success of your routing is dependent on your project. Typically, traces that are high-speed, high-power, or highly parallel are prone to be more sensitive to layout than slower data lines or GPIO. You should identify and prioritze these first. In this particular board, we have the following critical traces

*`USB 2.0` data lines
*`RF 50 Ohm` trace for a `2.4GHz` antenna
* `40MHz` clock signal
* Power Traces

Most of the time, critical connections often adhere to some sort of standard. Otherwise, you can find routing specifications for components in a company's datasheet.  

To define traces, click the dropdown `Track: ... > Edit pre-defined sizes...` and add track widths for `7, 10, 15, 20, and 25 mils`. These larger tracks are used for the power traces.

Some useful shortcuts in KiCAD that you may use for routing are the following:

* `x` - Add tracks
* `v` - Toggle front/back side of board
* `u` - Select all segments of a track
* `Del` - Delete selected track segments
* `b` - redraw zones (after moving footprints or tracks)
* 
#### USB 2.0 Differential Traces
USB 2.0 defines a differential signaling pair, according to standard, the differential trace impedance needs to be `90 Ohm`. You should now define a differential pair with a width of `17mils` and a gap of `8 mils`. The differential impedance is dependent on the geometry of your traces as well as your physical stackup. These dimensions were chosen by using a trace impedance calculator that is available in KiCAD as well as other websites.
<figure align="center">
  <img src="Images/layout/differential-pair.png" alt="" />
</figure>

`6` initiates the process for routing differential pairs. Draw traces labeled `USB_D_P` and `USB_D_N` from the USB C connector to the ESP32.

Since USB is a differential signal, the physical length of each line should be equal. Given your routing, there may be some physical differences in length. To fix this, skew can be intentionally introduced in your routing. To do so, click on `Tools > Tune Skew of Differential Pair`. Your cursor hovering over the differential pair should indicate how much length mismatch there is:
<figure align="center">
  <img src="Images/layout/current-skew.png" alt="" />
</figure>

By clicking on one of the traces and bringing your cursor into empty space on the PCB, KiCAD will introduce a skew to make sure that the lengths of each trace are the same.

<figure align="center">
  <img src="Images/layout/tune-skew.png" alt="" />
</figure>

#### RF Trace
Select a width of `20mils` and add a track from pin 1 of the microcontroller through its matching network and to the antenna as shown below:

<figure align="center">
  <img src="Images/layout/rf-trace.png" alt="" />
</figure>

### Routing on the Backside
When routing a PCB, you will will need to route traces on the backside of the PCB since you will have traces on the same side that need to cross. To do so, you need two vias, which are electrically connected holes in the PCB. First route a trace on the front copper side, place a via, route a trace on the backside, and place a via again to enter the front side again. The example is shown below: 

<figure align="center">
  <img src="Images/layout/backside-routing.png" alt="" />
</figure>


Continue routing the rest of the tracks. Use 20 mil tracks when connecting power traces to the regulator, and the regulator to the microcontroller. Otherwise, a `10mil` trace width for GPIO and other trace will be okay.

Before moving on, make sure your trace sizes match the following:
* USB 2.0: `7 mils`
* Power Traces (VDD33, 5V): `25 mils`
* RF Traces: `20mils`

You should also have some spacing between your data traces to mitigate cross talk. Generally, a separation of `2x` the trace width is good rule of thumb to follow.

### No Fill Zone
The antenna requires a no fill zone, where there will be no copper pour when the PCB is manufactured. To define a zone, click on the following in the toolbar.

<figure align="center">
  <img src="Images/layout/no-fill.png" alt="No Fill Zone Toolbar selection" />
</figure>

Use the following settings for the zone:
<figure align="center">
  <img src="Images/layout/antenna-no-fill.png" alt="No Fill Zone Settings" />
</figure>

Define a no fill zone rectangle with dimensions of `10mmx75mm`. It should look like something below:

<figure align="center">
  <img src="Images/layout/antenna-zone.png" alt="Antenna No Fill Zone" />
</figure>

### Grounding
Press `b` to define a copper pour. This will fill the empty space with copper that acts as a GND signal plane for your circuit. Notice that in certain areas of the board there are discontinuities in the copper fill, resulting in `"copper islands"`. These can be problematic, however, they are easily solved by utilized a stitching via.

<figure align="center">
  <img src="Images/layout/copper-island.png" alt="" />
</figure>

<figure align="center">
  <img src="Images/layout/stitching.png" alt="Stiched Via" />
</figure>

Place other stitching GNDs vias through your board to ensure a return path for your components throughout the board. This is a simple and important step! The final board should look something like this:

<figure align="center">
  <img src="Images/layout/final.png" alt="" />
</figure>


### Fabrication Outputs
Click `File > Fabrication Outputs > Gerbers .gbr`. Select an output directory, and click `Generate Drill Files` and then `Plot`. This will populate your selected directory with the necessary files that a manufacturer will need to create your board. Zip this folder and submit to your respective manufacturer.


#### Generating Custom Footprints and Symbols. 
Please refer to https://courses.grainger.illinois.edu/ece445/wiki/#/kicad/index?id=appendix if you would like to create custom footprints or symbols.

## Assembly Guide:

## Firmware Guide:
Download the Arduino IDE:
In settings, copy paste this link into the section labeled `Additional Board Managers URLs` and click `Ok`:
```
https://espressif.github.io/arduino-esp32/package_esp32_index.json
```

<figure align="center">
  <img src="Images/firmware/BoardManager.png" alt="Arduino IDE Package" />
</figure>

In boards manager, install the following package labeld "esp32" by Espressif Systems. At the time of this tutorial, version 3.2.0 was used.
<figure align="center">
  <img src="Images/firmware/arduino-package.png" alt="Arduino IDE Package" />
</figure>

Before uploading a program, make sure your settings match those shown below:
<figure align="center">
  <img src="Images/firmware/firmware-settings.png" alt="Firmware Settings" />
</figure>

You can change your board by selecting "Board:" > "ESP32 Arduino (In sketchbook)" and selecting "ESP32C3 Dev Module"
The description under "Port: " May vary based on your computer. When plugging in your board into your computer, a new port should pop up, select that board for this program.
"USB CDC On Boot" should be enabled. This allows for serial connection over USB with the ESP32.

### Hello World Program
```
void setup() {
  Serial.begin(115200);
  pinMode(LED_BUILTIN, OUTPUT);
  delay(1000);
  Serial.println("ESP32-C3 is alive!");
}

void loop() {
  Serial.println("Hello Wolrd!");
  delay(1000);
}
```
### Remainder of this Assignment:

