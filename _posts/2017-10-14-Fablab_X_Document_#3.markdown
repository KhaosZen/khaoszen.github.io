---
layout: post  
title: FABO X Document 3
date: 2017-10-14 11:11:11.000000000 +08:00  
tags: FABO-X  
---

## Introduction to 3D Printing  
### 3D Printer Technologies
+ SLA: Stereolithography.
	+ Liquid resin + Laser beam

+ DLP: Digital Light Processing.
	+ liquid resin + light

+ SLS: Selective Laser Sintering.
	+ Nylon powder + Laser beam

+ FDM: Fuse Deposition Modeling
	+ plastic filament + Heated nozzle

### FDM Printing Material
+ ABS(Actylonitrile butadiene styrene)
+ PLA (Polylactic Acid)
+ Nylon (PA, Polyamides)
+ Rubber(TPE, Thermoplastic Elastomers)

### FDM Printing Comes Down to a Combination of:
+ Nozzle (Hot end) temperature
+ Print Speed(XYZ Motors)
+ Flow Speed(Extrusion speed of plastic filament)
+ Heated Temperature

### Design Rules For Your Printer
+ Wall Thickness/Shell Thickness(nozzle size)
	+ The wall thickness must be a multiple of the size of your nozzle. If your nozzle is 0.4mm your wall must be 0.4 or 0.8 or 1.2 and so on.

+ Minimal Gap of separation between moving parts

+ Overhang(45 degree rule)
	+ With FDM printing you cannot "print in the air", you always need material fro the previous layer to support.
	+ Overhang is OK until 45(degree). After this limit, you need to add support material, that will be removed later.

+ Layer Height
	+ The height of each layer affects the resolution, the detail and the smoothness of your print. Differences in layer height cane especially seen in horizontal curved shapes. Bigger layer height can be used to quickly print test models. 

+ Bridging
	+ In one case you can connect two parts of the model by "printing in the air" by creating a bridge with the filament. It is important to set the right speed, flow and temperature to get a good result.

+ Useful Websites
	+ [reprap.org](riprap.org/wiki/Glossary)
	+ [Thingiverse](thingiverse.com)
	 
	 
### Exercise:DESIGN AND PRINT SOMETHING THAT CANNOT BE LASER CUTTED.
#### Step 1 3D Design
Use the Rhinocero tool kit and design the 3D model.  
![model](http://oxygvbxux.bkt.clouddn.com/model.png)

#### Step 2 Mesh
In Rhinocero, turn the 3D model to the mesh model.  
![mesh1](http://oxygvbxux.bkt.clouddn.com/mesh1.png)  
![mesh2](http://oxygvbxux.bkt.clouddn.com/mesh2.png)

#### Step 3 Driver Installation
Go to the XYZ Printing [offical website](http://xyzprinting.com) and install the printer driver.

#### Step 4 Printer Configuration and Printing
+ Set up the model and decide which precision you want to print.
![setup1](http://oxygvbxux.bkt.clouddn.com/setup1.png)  
![setup2](http://oxygvbxux.bkt.clouddn.com/setup2.jpg)  
![heating](http://oxygvbxux.bkt.clouddn.com/heating.jpg)  
+ Load the ABS filament.(On the Printer's Utility)
+ Print and wait!  
	<video width="480" align = "center" border ="1" height="320"  autoplay="autoplay"  preload="auto" loop="loop"   controls>
		<source src="http://oxygvbxux.bkt.clouddn.com/IMG_5550.m4v">
	</video>
		

#### A Glance of My "masterpiece"
![heating](http://oxygvbxux.bkt.clouddn.com/stand1.jpg) 
![heating](http://oxygvbxux.bkt.clouddn.com/stand2.jpg)   
   