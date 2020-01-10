---
path: Blog
date: 2020-01-10T15:11:39.881Z
title: Arduino Info display
description: How to display date time temperature humidity and sound levels in 2x16 lcd
---
# Arduino Info display

Hi all, I know I haven't updated my blog for a long time. Apologies. This is is because of spending time with my family and work. I'll try to post small guides from time to time. This Christmas I wanted to play a little bit with my Arduinos so I thought to connect a 2x16 LCD display into an Arduino Uno and show some data. I used a DS3231 RTC to get date and time, a common DHT11 for temperature and humidity measurements and finally a sound pressure level module from freetronics, which is essentially a microphone with a small amp in order to get some kind of measurement about the noise levels in my environment. Here is a photo of how it looks and the code below if you want to build something similar. For which pins to connect check the code. It is easy to figure out. For the LCD rs = 7, en = 8, d4 = 9, d5 = 10, d6 = 11, d7 = 12. DHT data pin to 2 and SPL data to A0.

![arduino 2x16 lcd ds3231 dht11 spl](https://www.devacron.com/wp-content/uploads/2020/01/arduino_2x16_display-600x400.png)



```
    // include the library code:
    #include <LiquidCrystal.h>
    #include <DS3231.h>
    #include <SimpleDHT.h>
    // Init the DS3231 using the hardware interface
    DS3231  rtc(SDA, SCL);
    // initialize the library by associating any needed LCD interface pin
    // with the arduino pin number it is connected to
    const int rs = 7, en = 8, d4 = 9, d5 = 10, d6 = 11, d7 = 12;
    LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
    int pinDHT11 = 2;
    SimpleDHT11 dht11(pinDHT11);
    const int splSensor = A0;
    void setup() {
      // Setup Serial connection
      Serial.begin(115200);
      // Initialize the rtc object
      rtc.begin();
      // The following lines can be uncommented to set the date and time
      rtc.setDOW(MONDAY);     // Set Day-of-Week to SUNDAY
      rtc.setTime(22, 14, 0);     // Set the time to 12:00:00 (24hr format)
      rtc.setDate(30, 12, 2019);   // Set the date to January 1st, 2014
      // set up the LCD's number of columns and rows:
      lcd.begin(16, 2);
    }
    void loop() {
      lcd.setCursor(0, 0);
      lcd.print(rtc.getDateStr());
      lcd.print(" ");
      lcd.print(rtc.getTimeStr());
      byte temperature = 0;
      byte humidity = 0;
      int err = SimpleDHTErrSuccess;
      if ((err = dht11.read(&temperature, &humidity, NULL)) != SimpleDHTErrSuccess) {
        Serial.print("Read DHT11 failed, err="); Serial.println(err);delay(1000);
        return;
      }
      lcd.setCursor(0, 1);
      lcd.print("t"); lcd.print((int)temperature); lcd.print("C  "); 
      lcd.print("h"); lcd.print((int)humidity); lcd.print("%  ");
      lcd.print("s"); lcd.print(analogRead(splSensor), DEC);
      delay (1000);
    }
```
