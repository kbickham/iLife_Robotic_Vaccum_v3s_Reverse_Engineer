# iLife Robotic Vaccum v3s Reverse Engineering

Project to reverse engineer the iLife v3s robotic vaccum. Trying to rewrite the firmware and use as a robotic platform and add a robotic arm.

In my apartment complex, someone through away this robotic vaccum. It looks like solid base to use as a robotics platform. I have identified all the components and their headers. I will continually add to this readme, as I work throguh the problems associated with reverse engineering this board. 


===============================================================================================================================
1/19/20 - I'm about done with this ultasonic sensor system. It is operational, the only hiccups I'm having are with refresh rates to the display ( which isn't really relevant to the final implementation I plan on using here). I've identified the chip and found what I believe to be a debug/uart port on the PCB labeled J11. I'm going to go through the datasheet and learn a little bit before I start messing with it, as all of my experience with ARM processors (in a non .."end user capacity") have been in a linux environement on the raspberry pi. I have more experience with 8-bit AVR processors (328p,ATMEGA1280,ATMEGA2560) and Espressif processors (ESP8266 and ESP32), as those chips are easier to translate from dev board into my own hardware with a soldering iron nad components. 


![Current Image of system Components](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/system.jpg)

 I definently want to utilize the motor drivers and IR sensors. This will save a lot of work testing new components and the wiring harnesses are already built. That being said I need to get into the STM32f030 and rewrite the firmware. Below is a picture of the PCB, and I believe I have found a UART port I may be able to utilize.
 
 ![Front of v3s PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/frontofIlifev3s_pcb.jpg)
 
 And on the back I have soldered some jumper wires so I can easily connect/disconnect to this header.
 
 ![Back of PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/backofIlifePCB.jpg)
 
 I've built a control box and small 1DoF ultasonic detection system I wish to place on the end of a stepper driven 2DoF arm. I would like to use the original PCB because I don't really want to spend the time reintegrating these sensors and motors with mystery encoder boards.
 
 ![STM32 PINOUT](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/processor.PNG)
 Looking through the datasheet we find the pinout, and voltage/current characteristics for this processor:
 
 Examining the chip we can look up this information in the datasheet for this STM32F030 R8T6
 
 R - 64 pins
 
 8 - 64 kbyte of flash
 
 T - LQFP package
 
 6 -  (-40,85) degree C operating range
 
 
 and we must make sure to stay within the characterized ranges for voltage 
 ![Voltage characteristics from datasheet](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/ratingsstm32.PNG)
 
 ... and current, during testing and operation. If one does not adhere, it is most likely you will experience magic smoke. If this happens, you are observing the soul of your chip ascending into silicon heaven.
  ![Current characteristics from datasheet](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/current%20ratings.PNG)
  
 
     
Looking ing on the board, it's mostly populated by passive components, I can see an LM224 and LM258.....which are quad/dual op amp ICs. That's not very useful for our purposes. Each of the main wheel motors have a small pcb with what appears to be small encoder pcb. We don't really need to mess with any of this right now, but it's good to know what all the components on your board are doing.
 
I'm not totally alone. I have found a repository by Elias Kotlyar who has a good breakdown of a very similar model...and with what appears to be the exact pcb from my V3s. 

Check out his repo here:
https://github.com/EliasKotlyar/IlifeX5Disassembly

He has created a good labeled image of the pcb. I've indicated on the front of the pcb the 4 pins I have soldered wires to. This is our initial point of attack.
https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/PCB-LABELED2.jpg

You know what... I'm going to stop right and just email the manufacturer. The company is based out of ShenZen, and the culture there is a little more open to the notion of sharing. I'm going to ask for schematics to the main PCB as there are a lot of vias aroudn the JP11 header and it's hard to visually trace them. I'll still use a multimeter to test continuity to these mcu pins, but it really couldn't hurt to ask.


 
 
  
 =================================================================================================================================
 
 
 
 
 
 
 
