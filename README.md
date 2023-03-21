# What's Ploopy Nano?

Ploopy nano is a compact trackball mouse whose hardware project (electronics & mechanics) is completely open source. The original github project can be found here:

<https://github.com/ploopyco/nano-trackball>

# Why a clone?

There are a couple of reasons for this:

1. Albeit the original project is open, it's designed for Altium software. The goal of this project is to clone that design as much as possible, but in KiCAD

1. Since I would like to test the BTU mod and I had that printed from an external online service, it didn't make much sense in my opinion to buy the whole kit just for the PCB. That's why I prefered to do it on my own

# What are the differences with the original design?

For technical and components' availability reasons, this design is slightly different from the original version. Here's the main points:

* USB typeC connector instead of microUSB
    * that's my preference
    * of course the USB speed is not changed, it's just a connector replacement
* debug UART pinheader
    * it's always useful to have it in case some debug is required
* replaced the 3x2 programmer's pinheader with TPs
    * this is mostly for layout reasons: on one hand the original connector required through holes, whereas TPs are just on one side; on the other hand it's also more flexible to place those 6 TPs
* smaller crystal (in size)
    * I could not find the original crystal available on JLCPCB, so I switched to an equivalent one which unfortunately has smaller footprint

# Board's design

The placement of componenets on the board was obtained using FreeCAD:

* I loaded PCB's step file into FreeCAD
* Generated a 2D top view of the PCB
* Exported into DXF
* Impoted the DXF into KiCAD

Similarly I did also for the layout of the PCB itself. In this case:

* I imported the layout of the board in FreeCAD using KiCadStepUp plugin
* Ridesigned the edges of the board
* Exported again into the *kicad_pcb* file
* Reopened KiCAD with new shape

# Extra

The **jlcpcb** folder contain gerber, BOM and placement files I used to order from JLCPCB site. They have been automatically generated from the specific KiCAD's extension.

Some components, likes the ATmega32U4 or the LED, are not present in the BOM because they're out of stock on JLCPCB, so I'm planning to buy them separately and solder manually.

# Results

Since the description is quite long I decided to create a specific [readme file](result/README.md) for this in order to explain also the step I did for the bringup.