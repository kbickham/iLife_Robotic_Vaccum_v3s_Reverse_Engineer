# iLife Robotic Vaccum v3s Reverse Engineering

Project to reverse engineer the iLife v3s robotic vaccum. Trying to rewrite the firmware and use as a robotic platform and add a robotic arm.

In my apartment complex, someone through away this robotic vaccum. It looks like solid base to use as a robotics platform. I have identified all the components and their headers. I will continually add to this readme, as I work throguh the problems associated with reverse engineering this board. 


# 1/19/20
I'm about done with this ultasonic sensor system. It is operational, the only hiccups I'm having are with refresh rates to the display ( which isn't really relevant to the final implementation I plan on using here). I've identified the chip and found what I believe to be a debug/uart port on the PCB labeled J11. I'm going to go through the datasheet and learn a little bit before I start messing with it, as all of my experience with ARM processors (in a non .."end user capacity") have been in a linux environement on the raspberry pi. I have more experience with 8-bit AVR processors (328p,ATMEGA1280,ATMEGA2560) and Espressif processors (ESP8266 and ESP32), as those chips are easier to translate from dev board into my own hardware with a soldering iron nad components. 


![Current Image of system Components](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/system.jpg)

 I definently want to utilize the motor drivers and IR sensors. This will save a lot of work testing new components and the wiring harnesses are already built. That being said I need to get into the STM32f030 and rewrite the firmware. Below is a picture of the PCB, and I believe I have found a UART port I may be able to utilize.
 
 ![Front of v3s PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/frontofIlifev3s_pcb.jpg)
 
 And on the back I have soldered some jumper wires so I can easily connect/disconnect to this header.
 
 ![Back of PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/backofIlifePCB.jpg)
 
 I've built a control box and small 1DoF ultrasonic detection system I wish to place on the end of a stepper driven 2DoF arm. I would like to use the original PCB because I don't really want to spend the time reintegrating these sensors and motors with mystery encoder boards.
 
 ![STM32 PINOUT](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/processor.PNG)
 Looking through the datasheet we find the pinout, and voltage/current characteristics for this processor:
 
 Examining the chip markings we find: STM32F030 R8T6
 
 R - 64 pins
 
 8 - 64 kbyte of flash
 
 T - LQFP package
 
 6 -  (-40,85) degree C operating range
 
 
 and we must make sure to stay within the characterized ranges for voltage 
 ![Voltage characteristics from datasheet](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/ratingsstm32.PNG)
 
 ... and current, during testing and operation. If one does not adhere, most likely you will experience the fabled magic smoke. If this happens, you are observing the soul of your chip ascending into silicon heaven.
  ![Current characteristics from datasheet](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/current%20ratings.PNG)
  
 
     
Looking on the board, it's mostly populated by passive components, I can see an LM224 and LM258.....which are quad/dual op amp ICs. That's not very useful for our purposes. Each of the main wheel motors have a small pcb with what appears to be small encoder pcb. We don't really need to mess with any of this right now, but it's good to know what all the components on your board are doing.
 
I'm not totally alone. I have found a repository by Elias Kotlyar who has a good breakdown of a very similar model...and with what appears to be the exact pcb from my V3s. 

Check out his repo here:
https://github.com/EliasKotlyar/IlifeX5Disassembly

He has created a good labeled image of the pcb. I've indicated on the front of the pcb the 4 pins I have soldered wires to. This is our initial point of attack.

The below picture was made by Kotlyar , not myself
![Kotlyar's description](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/PCB-LABELED2.jpg)

You know what... I'm going to stop right and just email the manufacturer. The company is based out of ShenZen, and the culture there is a little more open to the notion of sharing. I'm going to ask for schematics to the main PCB as there are a lot of vias aroudn the JP11 header and it's hard to visually trace them. I'll still use a multimeter to test continuity to these mcu pins, but it really couldn't hurt to ask.


# 2/1/2020:

7am:

 I've found some good references over the past few weeks, as I've been pretty busy. I'm reading all I can / doing some coding problems until Jiu Jitsu at noon,
Then I'm gonna take a crack at this thing.

-YouTube video from a European seminar on reverse engineering an SDR ( will post link later ) I
https://youtu.be/GX8-K4TssjY

-Guide on reverse engineering firmware from stm32f1xx ( this board is f3xx though)
https://medium.com/techmaker/reverse-engineering-stm32-firmware-578d53e79b3


From what I understand, I need to do a null pointer dereference/make a read 
From 0x00 in attempt to dump the first firmware from memory.
After I get this, I need to search for string literals which may be used as a 
Key if it is encrypted. I must however first find ports to connect JTAG or UART...  I have an STLINK v2 , and this is the only stm32 I have acess to currently...after an interview for a job I have gathered that I must in fact gain experience working with this processor type. 

5:15pm

The first thing I need to do, is to find what pins connect to this J11 port and hopefully... it's uart.
With a multimeter I checked, rechecked, and triple checked the pins and maked the pins from which continuity existed between the port J11 pins and the processor's pins. Initially, looking at the back of the PCB traces... I guessed the top first hole on the port was power and that the second was ground...as the second was not isolated from the ground plane. I have just completed this continuity test, and I believe that assumption may be correct.
![notes from continuity test](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/pinout.jpg)

I still have to ascertain which is pin#1 as there are circles in two corners of the processor on this board. I have drawn color coded lines corresponding to the color of wires I have soldered to the j11 port (as seen above). NOTE that that board is necessarily oriented in the same position as in above pictures. This is in attempt to show the chip markings.
![Color coded pin connections](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/processorcolorcoded.jpg)


8:15pm After careful testing and combing through the data sheet, I have ascertained that port J11 is not a uart port...it's a a single wire (SWDIO/SWCLK) interface. 
This is an STMF030 R8T6 and I believe pin 49  (my labeled D) may have TX function on usart2.


A: Pins 1,13,32,64 - VDD - power (max 4v, 13 is analog)

B: Pins 12,31,63 - VSS - ground (12 is analog)

C: Pin 46 - PA13 - SWDIO

D: Pin 49 - PA14 - SWCLK  (/USART2_TX - untested)

![Map of pin functions for header J11](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/pin%20functions.jpg)


I have an ST-Link V2, I'm going to see what I can do. I predict the manufacturer has the firmware protected and I won't be able to read... but we'll see.

# 2/6/2020
8:30pm:

Over the last few days, I've been looking through data sheet and mapping out the other pins...as I was hoping for a UART interface. This is the method utilized in hacking the Roomba... I found a link to a pdf (over 400 pages) to this if you're interested:
https://www.robotiklubi.ee/_media/kursused/roomba_sumo/failid/hacking_roomba.pdf

If anyone is interested (these are not yet tested) however I pulled these pin numbers and double checked, from the data sheet for the chip. I have several interface pins labeled:
![i2c and Uart pins](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/iLife_interface_pins_untested.jpg)

I decided to take off the wires I had soldered on the board and add a male pins. The spacing on j11 is smaller than normal breadboard headers, so I had to break them off individually and cut down the plastic spacers/turn them sideways to fit. below is an image of this process, it's pretty self explanatory. Don't solder wires onto the port, it's a pain to get the extra solder out of the holes ... I wish I had added these pins initially.
![Soldering male pins on j11](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/iLife_header_solder.jpg)

I've hooked it up to the st-link and the board makes some noise, it has a little buzzer on it. This leads me to believe the chip is good and memory still contains the firmware in working form. After it boots up, the st-link has an error and disconnects. This board has several voltage regulators and a couple motor drivers. I have all the harnesses disconnected from the board, so my instinct tells me that they shouldn't be drawing power...but I'm wondering if those are drawing current and I may need to power the board from an external power supply to keep the main chip operating stable? 

 I'm going to try to utilize the single wire debug port as is with an st-link. This is my first delve into the stm32, but since I need to learn it anyways ...I bought 2 stm32 dev boards (blue pill) and am learning to work with the Arm Embedded Toolchain in ubuntu with them. I want some practice before I take a crack at this board, because I don't want to brick it/loose my chance at pulling off the firmware. I believe it will take a lot more time if I have to test each of these sensors on the front harness and write new code to utilize them (bump sensors, a pleatheora of ir sensors, and  3 pairs of emitter/detector pairs). 
 

# 2/7/2020
7:30am
I had opened some links last night to read in the morning... and I happened upon two other people who are working on different iLife robotic vacuum models. I'll be honest, it's encouraging to see other people working at these things. I gotta give credit:

https://ci8.it/haier-t32x.html
He has seemed to find some success via UART.

and

http://roboforum.ru/forum90/topic16676.html / https://github.com/linvinus/open_ilife
The project page is in Russian but can be translated in google. They have some good details here as well. 


# 2/8/2020
I've got the st-link to connect to the board but I beleive there is read out protection in place.
![memory map](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/memory%20map.jpg)
Above is a map of the memory on the controller. I made several attempts to read from different starting and ending addresses...and I'm getting nothing but null... with some bits early on. 
The files are in my "memoryreadattempts" folder. I will follow this name convention followed my attempt # once I make more progress. I've seen a few exploits on youtube. One steps through the processor cycles one step at a time and re-writes the register where the readout protection is on and nother exploits a race condition to read out memory one word at a time. I'm going to call it a night, but I'm getting better at using these tools. I will find success.

