# USB-C and li-ion power supply

I designed this little power supply board for my ongoing polyphonic synth project. Like most analog synths it needs a +/-12V supply, as well as a 3.3V supply to run a microcontroller and some other digital bits and pieces. It doesn't consume a crazy amount of power, so I thought a built-in battery and the ability to power the synth over USB-C would be nice features. As power supplies go it's a bit complicated, but the circuit is made up of six basic blocks which are each simple enough on their own so I'll go through each one below.

The KiCad files are for both the schematic and the PCB are in this repo along with a PDF of the schematic. There's an interactive BOM file inside the "bom" directory as well.

## Block 1: USB-C connector and PD negotiation

Throughout the revisions of this board I learned the hard way that power over USB-C isn't as simple as routing the VBUS pin to a power input. Devices that recieve power via USB-C PD (power delivery) must "negotiate" with the power source via the connector's two CC pins to request the needed voltage and current. A simple solution here is to connect CC1 and CC2 to ground each through their own 5.1k resistor. This, however limits the device to 500mA at 5V. In order to negotiate our target max power of 3A at 5 volts, we can use a dedicated IC like the [CYPD3177](https://www.infineon.com/dgdl/Infineon-EZ-PD_BCR_Datasheet_USB_Type-C_Port_Controller_for_Power_Sinks-DataSheet-v03_00-EN.pdf?fileId=8ac78c8c7d0d8da4017d0ee7ce9d70ad), which connects to our CC pins and negotiates for voltage and current values which are set by some resistor dividers. [This video](https://www.youtube.com/watch?v=W13HNsoHj7A&t=1458s) by Phil's Lab on YouTube is a great explanation of this IC and how to work with USB-C in general. The IC operates a MOSFET load switch (Q102 on the schematic) to connect the input 5V line to our VDD net when negotiation is successful.

## Block 2: Battery charger
