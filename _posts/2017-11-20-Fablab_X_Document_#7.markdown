---
layout: post  
title: FABO X Document 7
date: 2017-11-20 11:11:11.000000000 +08:00  
tags: FABO-X  
---

## Programming
### Exercise:Use the Arduino IDE to write or modify a program with I/O devices. Assemble your circuit on a breadboard and test it.
#### A Ultrasonic-sensor-based gradully changed RGB LED
The LED would change its own color gradully with the distance of the object related to the ultra-sonic sensor.  

A list of the items here:    

+ Wires  
+ Shanghaino Board  
+ RGB LED   
+ HC-SR04 Ultrasonic Sensor

Here is the code:
  
``` c
#include <Ultrasonic.h>

Ultrasonic ultrasonic(12,13);
int LED_R = 11;
int LED_G = 10;
int LED_B =  9;
int dis_max = 45;
int t = dis_max/3;
int dis;

void setup() {
  Serial.begin(9600);
  pinMode(LED_R, OUTPUT);
  pinMode(LED_G, OUTPUT);
  pinMode(LED_B, OUTPUT);
}

void loop() {
  // main code here, to run repeatedly:
  Serial.print(ultrasonic.Ranging(CM)); // CM or INC
  Serial.println(" cm" );
  dis = ultrasonic.Ranging(CM);
  // red to orange to yellow to green
  if(dis >= dis_max*2/3 && dis < 45){
     analogWrite(LED_B, 255*(1-(dis-2*t)/t));
     analogWrite(LED_R, 255*(dis-2*t)/t);
     analogWrite(LED_G, 0);
  }
  // green to indigo to blue
  else if(dis >= dis_max*1/3 && dis < dis_max*2/3){
     analogWrite(LED_G, 255*(1-(dis-t)/t));
     analogWrite(LED_B, 255*(dis-t)/t);
     analogWrite(LED_R, 0);
  }
  // blue to purple to red
  else if(dis >= 0 && dis < dis_max*1/3){
     analogWrite(LED_R, 255*(1-dis/t));
     analogWrite(LED_G, 255*dis/t);
     analogWrite(LED_B, 0);
  }
  else{
     analogWrite(LED_R, 0);
     analogWrite(LED_G, 0);
     analogWrite(LED_B, 0);
    }
}
```
   
   A demo video here:  
   <video width="480" align = "center" border ="1" height="320" preload="auto"  controls>
		<source src="http://oxygvbxux.bkt.clouddn.com/USRGB.m4v">
	</video>