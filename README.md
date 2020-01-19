# iLife_Robotic_Vaccum_v3s_Reverse_Engineer
Project to reverse engineer the iLife v3s robotic vaccum. Trying to rewrite the firmware and use as a robotic platform and add a robotic arm.

In my apartment complex, someone through away this robotic vaccum. It looks like solid base to use as a robotics platform. I have identified all the components and their headers.
![Current Image of system Components](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/system.jpg)

 I definently want to utilize the motor drivers and IR sensors. This will save a lot of work testing new components and the wiring harnesses are already built. That being said I need to get into the STM32f030 and rewrite the firmware. Below is a picture of the PCB, and I believe I have found a UART port I may be able to utilize.
 
 ![Front of v3s PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/frontofIlifev3s_pcb.jpg)
 ![Back of PCB](https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/https://github.com/kbickham/iLife_Robotic_Vaccum_v3s_Reverse_Engineer/blob/master/backofIlifePCB.jpg)
 
 I've built a control box and small 1DoF ultasonic detection system I wish to place on the end of a stepper driven 2DoF arm. I would like to use the original PCB because I don't really want to spend the time reintegrating these sensors and myster stepper driver boards.
 
 
