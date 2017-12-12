---
layout: post  
title: FABO X Final Project
date: 2017-12-3 11:11:11.000000000 +08:00  
tags: FABO-X  
---

## Final Project: ~~Password~~ Instrumental Alarm
### Description  
This final project will combine as much knowledges I have learnt as possible to show the completeness of the learning of this course.  

Firstly I will implement an alarm clock which can play music notes.

Secondly I will implement the Setup Mode for this alarm that I can setup the Alarm without uploading the code.

Finally I will put the password function into this alarm. When the alarm clock rings, the user should input the musical password they have set to cancel the alarm.

The Password Alarm Clock is an interesting alarm clock which need to input the correct password to cancel the alarm clock.

### Electronic Part Lists
- Shanghaino Board (Copy of Arduino Pro) * 1
- 1kOhm Resistor * 6
- 16 * 2 LCD Display * 1
- SN74HC595 Logic Shifter * 1
- DS3231 Real Time Clock Module * 1
- Buttons * 6
- Potentiometer * 1
- Speaker * 1
- Wires

### Circuit Scheme
![circuit](http://oxygvbxux.bkt.clouddn.com/circuit.png)

### Control Scheme
![control scheme](http://oxygvbxux.bkt.clouddn.com/control%20scheme.001.jpeg)

### Outlook Design
#### A general look of the box design
![box design](http://oxygvbxux.bkt.clouddn.com/FINAL.jpg)
#### Front Board  
- 1 big hole for the potential meter to adjust the brightness of the LCD display.  
- 6 smaller holes for the buttons.
- 1 large window for the LCD display.

#### Upper Board
- 1 large hole for the speaker.

#### Rear Board
- 1 hole for the power cable.

### Arduino Code
#### The code on development:
``` c
#include <SPI.h>
#include <DS3231.h>
#include <Wire.h>
#include <LiquidCrystal.h>
#include "pitches.h"

LiquidCrystal lcd(9);
DS3231  rtc(SDA, SCL);
Time  t;

int Alarm[] = {0,1,2,3}; // A default alarm order
int pwRecord[] = {-1,-1,-1,-1};

int Hor;
int Min;
int Sec;

int modeFlag;
int pwFlag;

int spkr = 10;
int Al = 1; //A state variable to describe which the alarm is on or off
int AlHor = 22; // The Hour time of the alarm
int AlMin = 4; // The Minute time of the alarm

int button_0 = 2;
int button_1 = 3;
int button_2 = 4;
int button_3 = 5;
int button_Mode = 6;
int button_Next = 7;

int tones[] = { NOTE_C5, NOTE_D5, NOTE_E5, NOTE_G5}; //freq
int Mode = 0;
int Cur_tone = 0;

void setup() {
  Wire.begin();
  rtc.begin();
  Serial.begin(9600);

  pinMode(button_0, INPUT);
  pinMode(button_1, INPUT);
  pinMode(button_2, INPUT);
  pinMode(button_3, INPUT);
  pinMode(button_Mode, INPUT);
  pinMode(button_Next, INPUT);
  pinMode(spkr, OUTPUT);

  Mode = 0;
  
  // set up the LCD's number of columns and rows: 
  lcd.begin(16, 2);
  lcd.setCursor(0,0);
  lcd.print("KhaosZen's");
  lcd.setCursor(0,1);
  lcd.print("Password Alarm");
  
  // The following lines can be uncommented to set the date and time
  //rtc.setDOW(TUESDAY);     // Set Day-of-Week to SUNDAY
  //rtc.setTime(15, 8, 0);     // Set the time to 12:00:00 (24hr format)
  //rtc.setDate(5, 12, 2017);   // Set the date to January 1st, 2014

  delay(2000);
}

void loop() { 
   if(Mode == 0){
    Cur_tone = 0;
    t = rtc.getTime();
    Hor = t.hour;
    Min = t.min;
    Sec = t.sec;
    lcd.setCursor(0,0);
    lcd.print("Time: ");
    lcd.print(rtc.getTimeStr());
    lcd.setCursor(0,1);
    lcd.print("Date: ");
    lcd.print(rtc.getDateStr());
    ClickButton(0,0);
   }

   if(digitalRead(button_Mode)==HIGH){
     if(Mode == 1){
       Mode = ModeChange(Mode);
     }
     else if(Mode == 0){
       Mode = ModeChange(Mode);
     }
   }

  //Comparing the current time with the Alarm time and if it is the time to alarm, ring the alarm for 2 minutes
   if(Al == 1 && Hor == AlHor &&  (Min == AlMin || Min == AlMin + 1)){
     Play();  
     Play();
     lcd.clear();
     lcd.print("Alarm ON");
     lcd.setCursor(0,1);
     lcd.print("Alarming");
     Play();
     Play();
     pwRead();
     if(Alarm == pwRecord){
      Al = 0;
      for(int i = 0; i <= 3;i++){
        pwRecord[i] = -1;
        }
     }
   }
}


int ModeChange(int mode){
  if(mode == 0){
    SetAlarm1();
    if(Al == 1){
      SetAlarm2();
      SetAlarm3();
    }
    mode = 0;
  }
  else if(mode == 0){
    Serial.print("mode2");
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Clock Mode");  
    mode = 1;
  }
  return mode;
}

void SetAlarm1(){
  lcd.clear();
  while(digitalRead(button_Next) == LOW){
    if(digitalRead(button_Mode)==HIGH){
     if(Mode == 1){
       Mode = ModeChange(Mode);
       return;
     }
     else if(Mode == 0){
       Mode = ModeChange(Mode);
       return;
     }
    }
    lcd.setCursor(0,0);
    lcd.print("The alarm is:");
    lcd.setCursor(0,1);
    if(Al == 1) lcd.print("O N");
    else lcd.print("OFF");
    if(digitalRead(button_0) == HIGH
    || digitalRead(button_1) == HIGH||
    digitalRead(button_2) == HIGH
    || digitalRead(button_3) == HIGH){
      if(Al == 1) Al = 0;
      else Al = 1;
    }
  }
}
void SetAlarm2(){
  while(digitalRead(button_Next) == LOW){
    if(digitalRead(button_Mode)==HIGH){
     if(Mode == 1){
       Mode = ModeChange(Mode);
       return;
     }
     else if(Mode == 0){
       Mode = ModeChange(Mode);
       return;
     }
    }
    lcd.setCursor(0,0);
    lcd.print("Set up the alarm");
    lcd.setCursor(0,1);
    lcd.print("Alarm:");
    lcd.print(AlHor);
    lcd.print(":");
    lcd.print(AlMin);
    if(digitalRead(button_0) == HIGH){
      if(AlHor == 0)AlHor = 23;
      else AlHor --;
      delay(1000);
    }
    if(digitalRead(button_1) == HIGH){
      if(AlHor == 23)AlHor = 0;
      else AlHor ++;
      delay(1000);
    }
    if(digitalRead(button_2) == HIGH){
      if(AlMin == 0)AlMin = 59;
      else AlMin --;
      delay(1000);
    }
    if(digitalRead(button_3) == HIGH){
      if(AlMin == 59)AlMin = 0;
      else AlMin ++;
      delay(1000);
    }
    
  }
}
void SetAlarm3(){
  while(digitalRead(button_Next) == LOW){
    if(digitalRead(button_Mode)==HIGH){
     if(Mode == 1){
       Mode = ModeChange(Mode);
       return;
     }
     else if(Mode == 0){
       Mode = ModeChange(Mode);
       return;
     }
    }
    lcd.setCursor(0,0);
    lcd.print("Input the pw");
    pwFlag = 0;
    pwRead();
    for (int i = 0; i <= 3; i++){
      tone(spkr,tones[Alarm[i]]);
      delay(120);
    }
    lcd.setCursor(0,1);
    lcd.print("Setup complete!");  
  }
  return;
}


void Play(){
  for (int i = 0; i <= 3; i++){
    tone(spkr,tones[Alarm[i]]);
    delay(900);
  }
}

void pwRead(){
  int i = 0;
  while(pwFlag < 4){
    ClickButton(2,i);
  }
}

void ClickButton(int PW,int i){
  if(digitalRead(button_0) == 1){
    Serial.println(0);
    tone(spkr, tones[0]);
    delay(120);
    if(PW == 1){
      Alarm[i] == 0;
    }
    if(PW == 2){
      pwRecord[i] = 0;
    }
  }else if(digitalRead(button_1) == 1){
    Serial.println(1);
    tone(spkr, tones[1]);
    delay(120);
    if(PW == 1){
      Alarm[i] == 1;
    }
    if(PW == 2){
      pwRecord[i] = 1;
    }
  }else if(digitalRead(button_2) == 1){
    Serial.println(2);
    tone(spkr, tones[2]);
    delay(120);
    if(PW == 1){
      Alarm[i] == 2;
    }
    if(PW == 2){
      pwRecord[i] = 2;
    }
  }else if(digitalRead(button_3) == 1){
    Serial.println(3);
    tone(spkr, tones[3]);
    delay(120);
    if(PW == 1){
      Alarm[i] == 3;
    }
    if(PW == 2){
      pwRecord[i] = 3;
    }
  }else{
    Serial.println("no");
    noTone(spkr);
  }
}
``` 

  
#### The code used to demo:

```c
#include <SPI.h>
#include <DS3231.h>
#include <Wire.h>
#include <LiquidCrystal.h>
#include "pitches.h"

LiquidCrystal lcd(9);
DS3231  rtc(SDA, SCL);
Time  t;

int Alarm[] = {0,1,2,3}; // A default alarm order
int pwRecord[] = {-1,-1,-1,-1};

int Hor;
int Min;
int Sec;

int modeFlag;
int pwFlag;

int spkr = 10;
int Al; //A state variable to describe which the alarm is on or off
int AlHor = 7; // The Hour time of the alarm
int AlMin = 0; // The Minute time of the alarm

int button_0 = 2;
int button_1 = 3;
int button_2 = 4;
int button_3 = 5;
int button_Mode = 6;
int button_Next = 7;

String weekdays[] = {"","MON","TUE","WED","THU","FRI","SAT","SUN"};
int tones[] = { NOTE_C5, NOTE_D5, NOTE_E5, NOTE_G5}; //freq
int Mode = 0;
int Cur_tone = 0;

void setup() {
  Wire.begin();
  rtc.begin();
  Serial.begin(9600);

  pinMode(button_0, INPUT);
  pinMode(button_1, INPUT);
  pinMode(button_2, INPUT);
  pinMode(button_3, INPUT);
  pinMode(button_Mode, INPUT);
  pinMode(button_Next, INPUT);
  pinMode(spkr, OUTPUT);
  Al = 1;
  Mode = 0;
  
  // set up the LCD's number of columns and rows: 
  lcd.begin(16, 2);
  lcd.setCursor(0,0);
  lcd.print("KhaosZen's");
  lcd.setCursor(0,1);
  lcd.print("Instru Alarm");
  
  // The following lines can be uncommented to set the date and time
  //rtc.setDOW(TUESDAY);     // Set Day-of-Week to SUNDAY
  //rtc.setTime(15, 8, 0);     // Set the time to 12:00:00 (24hr format)
  //rtc.setDate(5, 12, 2017);   // Set the date to January 1st, 2014
  delay(2000);
  lcd.clear();
}

void loop() { 
   if(Mode == 0){
    Cur_tone = 0;
    t = rtc.getTime();
    Hor = t.hour;
    Min = t.min;
    Sec = t.sec;
    
    lcd.setCursor(0,0);
    lcd.print(rtc.getTimeStr());
    lcd.print(" ");
    lcd.print(rtc.getTemp());
    lcd.print(" C");
    lcd.setCursor(0,1);
    lcd.print(rtc.getDateStr());
    lcd.print("   ");
    lcd.print(weekdays[t.dow]);
    ClickButton(0,0);
   }

  //Comparing the current time with the Alarm time and if it is the time to alarm, ring the alarm for 2 minutes
   if(Al == 1 && Hor == AlHor &&  Min == AlMin && Sec <= 30){
     Play();  
     Play();
     lcd.clear();
     lcd.print("Alarm ON");
     lcd.setCursor(0,1);
     lcd.print("Alarming");
     Play();
     Play();
     if(digitalRead(button_Next)==HIGH){
      Serial.println("next");
      Al = 0;
     }
   }
}

void Play(){
  for (int i = 0; i <= 3; i++){
    tone(spkr,tones[Alarm[i]]);
    delay(900);
  }
}

void ClickButton(int PW,int i){
  if(digitalRead(button_0) == 1){
    Serial.println(0);
    tone(spkr, tones[0]);
    delay(120);
  }else if(digitalRead(button_1) == 1){
    Serial.println(1);
    tone(spkr, tones[1]);
    delay(120);
  }else if(digitalRead(button_2) == 1){
    Serial.println(2);
    tone(spkr, tones[2]);
    delay(120);
  }else if(digitalRead(button_3) == 1){
    Serial.println(3);
    tone(spkr, tones[3]);
    delay(120);
  }else{
    Serial.println("no");
    noTone(spkr);
  }
}
```

### Development log
#### Functions Realization
+ ~~Time display~~
+ ~~Date display~~
+ ~~Weekday display~~
+ ~~LCD brightness adjustment~~
+ ~~Music play with buttons~~
+ ~~Alarm play~~
+ ~~Temperature display~~
+ ~~Alarm turn off by one button~~
+ Alarm turn off by the password
+ Alarm setup by buttons
+ Alarm password setup

#### Prototype Test
I tested the prototype and find something wrong with the code. It can play 4 notes very well but the change of the modes and the setting of the alarm doesn't work pretty well.
![Prototype](http://oxygvbxux.bkt.clouddn.com/IMG_6024.jpg)

#### Final Model


### Conclusion
It is truely a complex single-person project for a nearly 3-week time. This final project can not set up the time of the time mannually due to the limit of the time. Howerver, it combined 2D-Design, 3D-Design, electric circuits design, also a bunch of coding. It's pretty fun to play with the buttons and it is interesting when I combine all of the knowledges I have learnt from this course into this tiny-look but hard-working project. 