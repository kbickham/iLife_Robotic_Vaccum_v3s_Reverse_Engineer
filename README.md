# iLife_Robotic_Vaccum_v3s_Reverse_Engineer

Project to reverse engineer the iLife v3s robotic vaccum. Trying to rewrite the firmware and use as a robotic platform and add a robotic arm.

In my apartment complex, someone through away this robotic vaccum. It looks like solid base to use as a robotics platform. I have identified all the components and their headers. I will continually add to this readme, as I work throguh the problems associated with reverse engineering this board. 

![Current Image of system Components](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/system.jpg)

 I definently want to utilize the motor drivers and IR sensors. This will save a lot of work testing new components and the wiring harnesses are already built. That being said I need to get into the STM32f030 and rewrite the firmware. Below is a picture of the PCB, and I believe I have found a UART port I may be able to utilize.
 
 ![Front of v3s PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/frontofIlifev3s_pcb.jpg)
 ![Back of PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/backofIlifePCB.jpg)
 
 I've built a control box and small 1DoF ultasonic detection system I wish to place on the end of a stepper driven 2DoF arm. I would like to use the original PCB because I don't really want to spend the time reintegrating these sensors and myster stepper driver boards.
 
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
  
 =================================================================================================================================
 
 
 
 
 
 
 
