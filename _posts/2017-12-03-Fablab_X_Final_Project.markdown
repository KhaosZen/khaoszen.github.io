---
layout: post  
title: FABO X Final Project
date: 2017-12-3 11:11:11.000000000 +08:00  
tags: FABO-X  
---

## Final Project: Password Alarm Clock. 
### Description  
This final project will combine as much knowledges I have learnt as possible to show the completeness of the learning of this course.  
The Password Alarm Clock is a interesting alarm clock which need to input the correct password to cancel the alarm clock.

### Arduino Code
``` c
#include <SPI.h>
#include <DS3231.h>
#include <Wire.h>
#include "LiquidCrystal.h"
#include "pitches.h"
LiquidCrystal lcd(9);
DS3231  rtc(SDA, SCL);
Time  t;
#define spkr 10

int Alarm[] = {0,1,2,3}; // A default alarm order
int pwRecord[] = {-1,-1,-1,-1};

int Hor;
int Min;
int Sec;

int Al = 0; //A state variable to describe which the alarm is on or off
int AlHor = 7; // The Hour time of the alarm
int AlMin = 0; // The Minute time of the alarm

int button_0 = 2;
int button_1 = 3;
int button_2 = 4;
int button_3 = 5;
int button_Mode = 6;
int button_Next = 7;

int tones[] = { NOTE_C4, NOTE_D4, NOTE_E4, NOTE_G4}; //freq
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
  pinMode(spkr, OUTPUT);

  // set up the LCD's number of columns and rows: 
  lcd.begin(16, 2);
  lcd.setCursor(0,0);
  lcd.print("KhaosZen's");
  lcd.setCursor(0,1);
  lcd.print("Password Alarm");
  
  // The following lines can be uncommented to set the date and time
  //rtc.setDOW(MONDAY);     // Set Day-of-Week to SUNDAY
  //rtc.setTime(14, 25, 0);     // Set the time to 12:00:00 (24hr format)
  //rtc.setDate(4, 12, 2017);   // Set the date to January 1st, 2014

  delay(2000);
}

void loop() {
  
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

   if(digitalRead(button_Mode)==HIGH){
     if(Mode == 1){
       Mode = 0;
       ModeChange(Mode);
     }
     else if(Mode == 0){
       Mode = 1;
       ModeChange(Mode);
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
     if(Alarm == pwRecord){
      Al = 0;
      for(int i = 0; i <= 3;i++){
        pwRecord[i] = -1;
        }
     }
   }
   if(Mode == 0) ClickButton(0,0);
}


void ModeChange(int mode){
  if(mode == 1 && Hor != AlHor &&  (Min != AlMin || Min != AlMin + 1) ){
    lcd.setCursor(0,1);
    lcd.print("Setup Mode");
    SetAlarm();
  }
  else if((mode == 0 && Hor != AlHor &&  (Min != AlMin || Min != AlMin + 1) )){
    lcd.setCursor(0,1);
    lcd.print("Clock Mode");  
  }
}

void SetAlarm(){
  lcd.clear();
  while(digitalRead(button_Next) == LOW){
    lcd.setCursor(0,0);
    lcd.print("The alarm is:");
    lcd.setCursor(0,1);
    if(Al == 1) lcd.print("ON");
    else lcd.print("OFF");
    if(digitalRead(button_0) == HIGH
    || digitalRead(button_1) == HIGH||
    digitalRead(button_2) == HIGH
    || digitalRead(button_3) == HIGH){
      if(Al == 1) Al = 0;
      else Al = 1;
    }
  }
  if(Al == 0) return;
  while(digitalRead(button_Next) == LOW){
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
  while(digitalRead(button_Next) == LOW){
    lcd.setCursor(0,0);
    lcd.print("Setup the pw");
    for(int i = 0;i <= 3;i++){
      ClickButton(1,i);
    }
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
    delay(120);
  }
}

void pwRead(){
  while(Al == 1 && Hor == AlHor &&  (Min == AlMin || Min == AlMin + 1))
    for (int i = 0; i <= 3; i++){
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
    noTone(spkr);
  }
}
```