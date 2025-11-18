# Timer-Controlled-Switch
College Project 
# Time Controlled Switch for Electronic Devices

## Atharva Madhavapeddi (23ECB0F26)

## Bibek Chaudhary (23ECB0F33)

## Nirdesh Bhandari (23ECB0F36)

## Department of Electronics and Communication Engineering

## National Institute of Technology, Warangal

## November, 2025

## Abstract

## 1.15 The Time-Controlled Switch for Electronic Devices is an automated system designed

## to control the switching (ON/OFF) of electrical loads based on user-defined time intervals.

## The project uses an Arduino Uno as the main controller, a 16×2 LCD for real-time display,

## push-button switches for user inputs, a relay module for controlling AC loads, and discrete

## resistors for signal conditioning. A 5V DC adapter is used to power the control circuitry,

## and a light bulb is used as the demonstration load. The system allows the user to

## set ON and OFF durations with precision, providing an efficient and safe method of

## automating appliances. This project is useful in energy conservation, automation, and

## timed operation of household and industrial devices.

## 1 Introduction

## In modern automation systems, time-controlled switches are essential for automating

## electrical devices based on predefined schedules. Such systems can reduce manual effort,

## enhance energy efficiency, and improve safety. Traditional mechanical timers are limited

## in precision and functionality, whereas microcontroller-based digital timers offer flexible

## programming and real-time tracking.

## This project demonstrates the design and implementation of a Time-Controlled Switch

## using the Arduino Uno. By interfacing an LCD display, relay module, and push buttons,

## the system allows the user to easily configure and monitor the timing functions. The

## project highlights the application of embedded systems for smart automation at low cost

## and with high reliability.

## 2 Theory and Working Principle

## The user sets the ON time or OFF time using push buttons. The timer counts down

## and, upon reaching zero, toggles the relay state. A buzzer alerts the user when switching

## occurs. EEPROM memory stores the last used timer values.


### 2.1 Arduino Uno

### Arduino Uno, based on the ATmega328P microcontroller, is responsible for reading in-

### puts, processing timer logic, and controlling the output relay.

### 2.2 Relay Module

### A relay acts as an electrically operated switch that allows the Arduino (5V DC logic)

### to safely control a high-voltage AC circuit. The onboard optocoupler provides isolation

### between low-voltage and high-voltage domains.

### 2.3 LCD Display

### The 16×2 LCD module displays timer information, operating mode, and status messages.

### It is interfaced in 4-bit mode to reduce the number of required Arduino pins.

### 2.4 Push Buttons

### Four buttons (Mode, Up, Down, Set/Start) are used to:

- Set ON/OFF modes
- Adjust time (HH:MM:SS)
- Start, stop, or pause timer

## 3 Circuit Design and Implementation

### The project integrates the following components:

### 3.1 Power Supply

### A 5V 1A adapter powers the Arduino, LCD, and relay module. The relay isolates the

### low-voltage and high-voltage circuits to ensure safety.

### 3.2 Input Section

### Push buttons are connected to Arduino analog pins (A0–A3). Each button uses resistors

### to prevent floating inputs and ensure stable logic levels.

### 3.3 Display Section

### The 16×2 LCD is interfaced using:

- RS - Arduino D
- EN - Arduino D
- D4–D7 - Arduino D5–D
- Contrast control via 10k potentiometer


### 3.4 Output Section

### The relay module connects to Arduino digital pin D12 (or as per your code). The AC

### bulb connects to the relay’s NO or NC terminal based on required switching mode.

### The working circuit diagrams and Proteus simulation validate wiring and functionality

### before hardware implementation.

### Figure 1: Circuit Diagram of Time Controlled Switch

### Figure 2: Simulation Diagram of Time Controlled Switch

## 4 Arduino Code

#### 1 #include <LiquidCrystal.h>

#### 2 #include "Countimer.h"

#### 3 Countimer timer;

#### 4 LiquidCrystal lcd(2, 3, 4, 5, 6, 7);

#### 5 #include <EEPROM.h>

##### 6

#### 7 #define bt_set A

#### 8 #define bt_up A


#### 9 #define bt_down A

#### 10 #define bt_start A

##### 11

#### 12 int time_s = 0;

#### 13 int time_m = 0;

#### 14 int time_h = 0;

##### 15

#### 16 int set=0, mode =0;

#### 17 int flag1=0, flag2=0, flag3 =0;

##### 18

#### 19 int relay = 8;

#### 20 int buzzer = A5;

##### 21

#### 22 void setup() {

#### 23 Serial.begin (9600);

##### 24

#### 25 pinMode(bt_set , INPUT_PULLUP);

#### 26 pinMode(bt_up , INPUT_PULLUP);

#### 27 pinMode(bt_down , INPUT_PULLUP);

#### 28 pinMode(bt_start , INPUT_PULLUP);

##### 29

#### 30 pinMode(relay , OUTPUT); digitalWrite(relay , HIGH);

#### 31 pinMode(buzzer , OUTPUT);

##### 32

#### 33 lcd.begin(16, 2);

#### 34 lcd.clear();

#### 35 lcd.setCursor (0,0);

#### 36 lcd.print(" Welcome To ");

#### 37 lcd.setCursor (0,1);

#### 38 lcd.print(" On/Off Timer ");

#### 39 timer.setInterval(print_time , 999);

#### 40 delay (1000);

#### 41 lcd.clear();

#### 42 read_on_time ();

#### 43 }

##### 44

#### 45 void print_time (){

#### 46 time_s = time_s -1;

#### 47 if(time_s <0){time_s =59; time_m = time_m -1;}

#### 48 if(time_m <0){time_m =59; time_h = time_h -1;}

#### 49 }

##### 50

#### 51 void loop(){

#### 52 timer.run();

##### 53

#### 54 if(digitalRead (bt_set) == 0){

#### 55 if(flag1 ==0 && flag2 ==0){flag1 =1;

#### 56 mode = mode +1;

#### 57 if(mode >2){mode =0; set =0;}

#### 58 if(mode ==1){read_on_time (); flag3 =0;}

#### 59 if(mode ==2){read_of_time (); flag3 =1;}

#### 60 delay (100);

#### 61 }

#### 62 }

##### 63

#### 64 if(digitalRead (bt_up) == 0){

#### 65 if(set ==0 && mode ==0){timer.start(); flag2 =1;}

#### 66 if(set ==1){time_s ++;}


#### 67 if(set ==2){time_m ++;}

#### 68 if(set ==3){time_h ++;}

#### 69 if(time_s >59){time_s =0;}

#### 70 if(time_m >59){time_m =0;}

#### 71 if(time_h >99){time_h =0;}

#### 72 if(set >0 && mode ==1){write_on_time ();}

#### 73 if(set >0 && mode ==2){write_of_time ();}

#### 74 delay (200);

#### 75 }

##### 76

#### 77 if(digitalRead (bt_down) == 0){

#### 78 if(set ==0 && mode ==0){timer.stop(); flag2 =0;}

#### 79 if(set ==1){time_s --;}

#### 80 if(set ==2){time_m --;}

#### 81 if(set ==3){time_h --;}

#### 82 if(time_s <0){time_s =59;}

#### 83 if(time_m <0){time_m =59;}

#### 84 if(time_h <0){time_h =99;}

#### 85 if(set >0 && mode ==1){write_on_time ();}

#### 86 if(set >0 && mode ==2){write_of_time ();}

#### 87 delay (200);

#### 88 }

##### 89

#### 90 if(digitalRead (bt_start) == 0){

#### 91 if(mode ==0){ flag2 =1; flag3 =0;

#### 92 read_on_time ();

#### 93 timer.restart ();

#### 94 timer.start();

#### 95 }

#### 96 if(mode >0 && flag1 ==0){ flag1 =1;

#### 97 set = set +1;

#### 98 if(set >3){set =0;}

#### 99 delay (100);

#### 100 }

#### 101 }

##### 102

#### 103 if(digitalRead (bt_set) == 1 && digitalRead (bt_start) == 1){flag1 =0;}

##### 104

#### 105 lcd.setCursor (0,0);

#### 106 if(flag3 ==0){lcd.print(" On ");}

#### 107 if(flag3 ==1){lcd.print("Off ");}

#### 108 lcd.print(" Timer");

#### 109 if(set ==0 && mode >0){lcd.print(" Mode S ");}

#### 110 if(set ==0 && mode ==0 && flag2 ==0){lcd.print(" Stop ");}

#### 111 if(set ==0 && mode ==0 && flag2 ==1){lcd.print(" Start ");}

#### 112 if(set ==1){lcd.print(" Set SS ");}

#### 113 if(set ==2){lcd.print(" Set MM ");}

#### 114 if(set ==3){lcd.print(" Set HH ");}

##### 115

#### 116 lcd.setCursor (4,1);

#### 117 if(time_h <=9){lcd.print ("0");}

#### 118 lcd.print(time_h);

#### 119 lcd.print (":");

#### 120 if(time_m <=9){lcd.print ("0");}

#### 121 lcd.print(time_m);

#### 122 lcd.print (":");

#### 123 if(time_s <=9){lcd.print ("0");}

#### 124 lcd.print(time_s);


#### 125 lcd.print(" ");

##### 126

#### 127 if(time_s ==0 && time_m ==0 && time_h ==0 && flag2 ==1){

#### 128 flag3 = flag3 +1;

#### 129 if(flag3 >1){flag3 =0;}

##### 130

#### 131 if(flag3 ==0){read_on_time ();}

#### 132 if(flag3 ==1){read_of_time ();}

##### 133

#### 134 timer.restart ();

#### 135 timer.start();

##### 136

#### 137 digitalWrite(relay , flag3);

#### 138 digitalWrite(buzzer , HIGH);

#### 139 delay (500);

#### 140 digitalWrite(buzzer , LOW);

#### 141 }

##### 142

#### 143 if(flag2 ==1){digitalWrite(relay , flag3);}

#### 144 else{digitalWrite(relay , HIGH);}

##### 145

#### 146 delay (1);

#### 147 }

##### 148

#### 149 void write_on_time (){

#### 150 EEPROM.write(1, time_s);

#### 151 EEPROM.write(2, time_m);

#### 152 EEPROM.write(3, time_h);

#### 153 }

##### 154

#### 155 void write_of_time (){

#### 156 EEPROM.write(11, time_s);

#### 157 EEPROM.write(12, time_m);

#### 158 EEPROM.write(13, time_h);

#### 159 }

##### 160

#### 161 void read_on_time (){

#### 162 time_s = EEPROM.read (1);

#### 163 time_m = EEPROM.read (2);

#### 164 time_h = EEPROM.read (3);

#### 165 }

##### 166

#### 167 void read_of_time (){

#### 168 time_s = EEPROM.read (11);

#### 169 time_m = EEPROM.read (12);

#### 170 time_h = EEPROM.read (13);

#### 171 }

### Listing 1: Arduino Source Code

## 5 Operation

### 1. Power the Arduino with 5V supply.

### 2. LCD displays the main menu.

### 3. Use buttons to select ON Timer or OFF Timer.


### 4. Adjust hours, minutes, and seconds.

### 5. Press Start to begin countdown.

### 6. Relay toggles when timer expires.

### 7. Buzzer gives an alert on switching.

### Figure 3: PCB Implementation of the Timer System

## 6 Results and Observations

### 6.1 Results

- The timer accurately controlled the AC bulb based on set duration.
- LCD displayed real-time countdown.
- Relay switching was smooth and electrically isolated.
- EEPROM successfully retained last used timer values.

### 6.2 Observations

- Stable 5V power improves relay operation.
- Button debouncing may be required depending on hardware.
- Relay and load side remain electrically isolated for safety.


### 6.3 Real-Life Applications

- Automatic switching of household lights and fans.
- Water pump or motor automation.
- Egg incubator heating cycle control.
- Agricultural irrigation scheduling.
- Industrial machine cycle timing.
- Automatic battery charging cut-off.
- Aquarium light and aerator timers.

## 7 Conclusion

### The digital ON/OFF timer relay system using Arduino successfully automates the con-

### trol of electrical loads based on user-defined timing settings. By integrating an LCD

### display, push-button interface, and relay module, the system offers a reliable and efficient

### solution for applications where precise timing and automation are required. The project

### demonstrates how microcontrollers can simplify routine tasks, improve safety, and re-

### duce manual intervention. Overall, the design is cost-effective, easy to implement, and

### can be upgraded further with additional features such as real-time clocks, advanced user

### interfaces, and remote monitoring.

## 8 References

- Arduino Official Documentation
- Proteus Design Suite Documentation
- ElectronicsHub and CircuitDigest Tutorials
- Relay Module Datasheet (SRD-05VDC-SL-C)


