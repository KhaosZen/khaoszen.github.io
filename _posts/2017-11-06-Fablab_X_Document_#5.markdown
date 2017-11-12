---
layout: post  
title: FABO X Document 5
date: 2017-11-06 11:11:11.000000000 +08:00  
tags: FABO-X  
---

## CNC Milling
### Exercise: ~~Design~~ and Mill a PCB Board
#### Preparation
+ Milling Maching Type: [Roland monoFab SRM-20](https://www.rolanddg.com/en/products/lineup/monofab-srm-milling-machine)
+ A useful website to convert the png design file to the roland milling file (the output file we need is `.rml` format which is compatible with the roland milling machine...): [Fab Modules](http://fabmodules.org)
	+ Shanghaino circuit scheme:  
		+ Traces:  
		![dsn3](http://oxygvbxux.bkt.clouddn.com/dsn3.png)  
		+ Holes:  
		![dsn1](http://oxygvbxux.bkt.clouddn.com/dsn1.png)  
		+ Outlines:  
		![dsn2](http://oxygvbxux.bkt.clouddn.com/dsn2.png)  
	+ The parameters for the traces I have used ( The parameter Saverio Silli set in the class) :  
	![para](http://oxygvbxux.bkt.clouddn.com/paras.png)
+ A piece of copper PCB board  ![PCB_board](https://images-na.ssl-images-amazon.com/images/I/41FEN0K%2BvuL._SY355_.jpg)

#### Then:	
+ Stick the padding material onto the platform of the machine.
+ Stick the copper PCB board onto the padding material.
+ Turn on the machine and set up the origins of the X/Y and Z.
![setup](http://oxygvbxux.bkt.clouddn.com/setup1.jpeg) 
	+ The diameter of the mill can be changed.
	![mill](http://oxygvbxux.bkt.clouddn.com/mill.jpeg) 
	+ We need 0.4mm diameter mill for the traces and 0.8mm diameter mill for the holes and outlines.
+ Import the `.rml` file and mill  
	<video width="480" align = "center" border ="1" height="320" preload="auto"  controls>
		<source src="http://oxygvbxux.bkt.clouddn.com/IMG_5751.m4v">
	</video>
	![fin1](http://oxygvbxux.bkt.clouddn.com/IMG_5752.jpg)
	![fin2](http://oxygvbxux.bkt.clouddn.com/IMG_5753.jpg)  
	
#### Some troubles
Though the PCB board is finished, there are some troubles exist. We can see there are some thin copper leftover on the final PCB board.  
	![trouble](http://oxygvbxux.bkt.clouddn.com/trouble.jpeg)     
To solve the trouble, the parameters should be changed.  
![solve](http://oxygvbxux.bkt.clouddn.com/paras2.jpeg) 