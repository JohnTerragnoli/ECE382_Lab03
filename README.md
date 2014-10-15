ECE382_Lab03
============

John Paul Terragnoli
#Lab 3 - SPI - "I/O"
##Mega Prelab
A hard copy of this Mega Prelab is required to be turned in. Answers should not be handwritten. The timing diagram may be NEATLY drawn by hand with the assistance of a straightedge on engineering paper.
Nokia1202 LCD BoosterPack v4-5
Look at the schematic for the Nokia1202 LCD BoosterPack. Complete the following table. The pin number should be the pin number that signal connects to on the MSP 430. You may need to examine page 3 of the MSP430 Data Sheet. The type is from the perspective of the MSP430 and is one of the following: input, output, or power . For example, the RST' line should be listed as having an output type because it is an output from the MSP430 (to an input on the Nokia 1202). For input and output types (do nothing further with power), list their bit settings in the three registers listed (see pages 328 and 329 of the MSP430g2553 User's Guide). 

Thought Process: 
1. For Pin#, page three was consulted.  
a. For GND, there was no direct label.  I then looked at DVCC and DVSS caught my eye since they are usually associated with power.  I look them up, and apparently DVCC and DVSS are usually the high and low input voltages, respectively.  This made since, so I decided to assume that GND was the same as VCSS.  
b. Also, I realized that the chart on page 3 of the MSP430 data sheet corresponds to the LaunchPad headers on the Nokia information sheet.  GND on the Nokia matches up with pin 20 on the data sheet.  
c. In class, Captain Trimble mentioned that SIMO and MOSI  are the same thing.  I could not find MOSI, but I could find SIMO on pin 15.  Therefore, this was the pin that was used.  Also, MOSI is labeled on the Nokia data sheet as pin 6, which matches up with pin 20 on the MSP430 data sheet.  Success.  
d. SCLK and VCC both match up with pins on the Nokia spreadsheet and the MSP430 sheet. 
e. S1-S4: Since Port 1 was an output, I figured it would make sense to make port 2 be an input.  I also figured that the inputs would be clumped together for simplicity.  The Most viable pin numbers were then 9-12, or P2.1-P2.4.  This also makes sense because the numbers 1-4 would correspond to the numbers S1-S4.  Also, S1-S4 is labeled in the left chart on the Nokia datasheet (Is this a valid reasoning process?).  
2. PxDIR
a. Looking at page 329 of the MSP430 Family User Guide, PxDIR designate if the pin is an input or an output.  0 designates and input and 1 designates and output.  The following bits were then assigned in the table corresponding with this rule.  Obviously, power types were not assigned bits.  
3. PxREN
a. From page 329 of the MSP430 Family User Guide, PxREN enables/disables the pull up/pulldown resistors.  In order for data to flow through the outout, the pull up/down resistors must be disabled. To do this, PxREN needs to be a zero.  Therefore, it would make sense to choose 0 for the PxREN for the outputs. 
b.	However the inputs must be pullup resistors, as shown on the Nokia Schematic in the data sheet.  Also, inputs are active low.  Therefore, when the button is not pressed then there is a pull up resistor for each input.  
4. PxOUT
a. All of the outputs will then be outputting data, this is represented by a 1 in the PxOUT column.  The inputs will choose one so that they can be pull up resistors.  




Name | Pin# | Type | PxDIR | PxREN | PxOUT
|--- | --- | --- | --- | --- | ---|
GND	|20|	Power|	-|	-|	-|
RST|	8|	OUTPUT|	1|	0|	Information sent
P1.4|	6|	OUTPUT|	1|	0|	Information sent
MOSI|	15|	OUTPUT|	1|	0|	Information sent
SCLK|	7|	OUTPUT|	1|	0|	Information sent
VCC|	1|	POWER|	-|	-|	-|
S1|	9|	INPUT|	0|	0|	Not used
S2|	10|	INPUT|	0|	0|	Not used
S3|	11|	INPUT|	0|	0|	Not used
S4|	12|	INPUT|	0|	0|	Not used



Configure the MSP430
The following is a portion of the code that initializes the directions of the pins connecting the MSP430 to the Nokia 1202 display. Use the information from the previous question to identify the names of the registers missing in the following code (identified by the letters A - D). Put the register names in the table below.

```
mov.b    #LCD1202_CS_PIN|LCD1202_BACKLIGHT_PIN|LCD1202_SCLK_PIN|LCD1202_MOSI_PIN, & A
mov.b    #LCD1202_CS_PIN|LCD1202_BACKLIGHT_PIN|LCD1202_SCLK_PIN|LCD1202_MOSI_PIN, & B
mov.b    #LCD1202_RESET_PIN, & C
mov.b    #LCD1202_RESET_PIN, & D
```

Mystery Label|Register
---|---
A|	P1DIR
B|	P1OUT
C|	P2DIR
D|	P2OUT

Thought Process: 
1. The MOSI and the SCLK pins are both outputs, making it likely that the first two in the first line are also outputs.  There are on the first pin.  Therefore, they should set the direction of the first pin to be an output.  The reset button on the first line is also an output, therefore, it would make sense it would be outputted.  
2. To apply this concept the direction of the signals must be specified, which is done in the P1DIR and P2DIR steps.  The P1OUT and P2OUT steps then open the output to whatever signals would like to pass through.  
3. 
The following initializes the SPI subsystem of the MSP430. For each of the bits listed in the table below, identify how the code-snippet configures that pin and what function is realized by that setting. For example, setting the UCMSB bit of the UCB0CTL0 register forces the SPI subsystem to output the bits starting from the LSB. Also, list the bit position that each occupies in its associated register.

```
    bis.b    #UCCKPH|UCMSB|UCMST|UCSYNC, &UCB0CTL0
    bis.b    #UCSSEL_2, &UCB0CTL1
    bic.b    #UCSWRST, &UCB0CTL1
```

|ID	|Bit|	Function as set in the Code|
|---|---|---|
|UCCKPH	|7 (set to 1)|	Clock phase select: Data is captured on the first UCLK edge and changed on the following edge|
|UCMSB	|5 (set to 1)|	MSB first select. Controls the direction of the receive and transmit shift register.MSB first|
|UCMST|	3 (set to 1)|	Master Mode Select: Master mode chosen. |
|UCSYNCH	|0 (set to 1)|	Synchronous mode enabled; Synchronous mode selected.|
|UCSSEL_2|	6-7 (set to 11)	|Select SMCLK as CLK in master mode|
|UCSWRST|	0 (set to 0)|	Software reset enable|

Disabled. USCI reset released for operation.
Thought Process:   

1. Just looked at the MSP430 Family User Guide on page 445.  Copied down that occurred based on if the specific bits were set or cleared.  


*Communicate to the Nokia1202 display*
The following code communicates one byte to the Nokia 1202 display using its 9-bit protocol. Use this code to draw a timing diagram of the expected behavior of LCD1202_CS_PIN, LCD1202_SCLK_PIN, LCD1202_MOSI_PINs from the begining of this subroutine to the end. Make sure that you clearly show the relationship of the edges in the clk and data waveforms.
```
;-------------------------------------------------------------------------------
;    Name:        writeNokiaByte
;    Inputs:        R12 selects between (1) Data or (0) Command string
;                R13 the data or command byte
;    Outputs:    none
;    Purpose:    Write a command or data byte to the display using 9-bit format
;-------------------------------------------------------------------------------

writeNokiaByte:

    push    R12
    push    R13

    bic.b    #LCD1202_CS_PIN, &P1OUT                ; LCD1202_SELECT
    bic.b    #LCD1202_SCLK_PIN | LCD1202_MOSI_PIN, &P1SEL    ; Enable I/O function by clearing
    bic.b    #LCD1202_SCLK_PIN | LCD1202_MOSI_PIN, &P1SEL2    ; LCD1202_DISABLE_HARDWARE_SPI

    bit.b    #01h, R12
    jeq    cmd

    bis.b    #LCD1202_MOSI_PIN, &P1OUT            ; LCD1202_MOSI_LO
    jmp    clock

cmd:
    bic.b    #LCD1202_MOSI_PIN, &P1OUT            ; LCD1202_MOSI_HIGH

clock:
    bis.b    #LCD1202_SCLK_PIN, &P1OUT            ; LCD1202_CLOCK positive edge
    nop
    bic.b    #LCD1202_SCLK_PIN, &P1OUT            ; negative edge

    bis.b    #LCD1202_SCLK_PIN | LCD1202_MOSI_PIN, &P1SEL    ; LCD1202_ENABLE_HARDWARE_SPI;
    bis.b    #LCD1202_SCLK_PIN | LCD1202_MOSI_PIN, &P1SEL2    ;

    mov.b    R13, UCB0TXBUF

pollSPI:
    bit.b    #UCBUSY, &UCB0STAT
    jz        pollSPI                    ; while (UCB0STAT & UCBUSY);

    bis.b    #LCD1202_CS_PIN, &P1OUT                ; LCD1202_DESELECT

    pop        R13
    pop        R12

    ret
```
Configure the Nokia1202 display
The following code configures the Nokia 1202 display to display pixels. The code consists of two main areas. The first section holds the reset line low and then high for a specific length of time. You will measure the duration of the reset pulse later in the lab. 

The second section sends a sequence of commands to the Nokia 1202 display. Your task is to use the information on page 42 (and beyond) of the STE2007 technical document to decode the symbolic constants moved into register R13 on the lines marked with "DECODE HERE".
```
;-------------------------------------------------------------------------------
;    Name:        initNokia        68(rows)x92(columns)
;    Inputs:        none
;    Outputs:    none
;    Purpose:    Reset and initialize the Nokia Display
;-------------------------------------------------------------------------------
initNokia:

    push    R12
    push    R13

    bis.b    #LCD1202_CS_PIN, &P1OUT

    ;-------------------------------------------------------------------------------
    ; Measure the time that the RESET_PIN is held low by the delayNokiaResetLow loop
    bic.b    #LCD1202_RESET_PIN, &P2OUT
    mov    #0FFFFh, R12
delayNokiaResetLow:
    dec    R12
    jne    delayNokiaResetLow
    bis.b    #LCD1202_RESET_PIN, &P2OUT
    ;-------------------------------------------------------------------------------

    mov    #0FFFFh, R12
delayNokiaResetHigh:
    dec    R12
    jne    delayNokiaResetHigh
    bic.b    #LCD1202_CS_PIN, &P1OUT

    ; First write seems to come out a bit garbled - not sure cause
    ; but it can't hurt to write a reset command twice
    mov    #NOKIA_CMD, R12
    mov    #STE2007_RESET, R13                    ; DECODE HERE
;this is basically loading the command into R12 so that it can be used for later operations.  ;this is like the fetch step! (sort of)
;this is just a fancy way of clearing R13 so that it can be used to store results from 
;operations without any bias from previous calculations.  
    call    #writeNokiaByte


    mov    #NOKIA_CMD, R12
    mov    #STE2007_RESET, R13
    call    #writeNokiaByte

    mov    #NOKIA_CMD, R12
    mov    #STE2007_DISPLAYALLPOINTSOFF, R13            ; DECODE HERE
;turns the display off while the next command is obtained.    
    call    #writeNokiaByte

    mov    #NOKIA_CMD, R12
    mov    #STE2007_POWERCONTROL | STE2007_POWERCTRL_ALL_ON, R13    ; DECODE HERE
;fetches the new command; turns the power on again.  This includes the booster, voltage 
;regulator, and voltage follower.  
    call    #writeNokiaByte

    mov    #NOKIA_CMD, R12
;fetch’s the third command
    mov    #STE2007_DISPLAYNORMAL, R13                ; DECODE HERE
;ensures that the LCD is configured in a normal fashion.      
    call    #writeNokiaByte

    mov    #NOKIA_CMD, R12
;fetchs the fourth command.  
    mov    #STE2007_DISPLAYON, R13                    ; DECODE HERE
;allows the data to be written onto the screen.    

    call    #writeNokiaByte

    pop    R13
    pop    R12

    ret
```
Complete the table below. To answer this question you will have to use some common sense in decoding the meaning of the symbolic constants.
(looked up these values on page 41 of STE2007)

|Symbolic Constant|	Hex	|Function|
|---|---|---|
|#STE2007_RESET|	E2	|Internal Reset|
|#STE2007_DISPLAYALLPOINTSOFF|	A4|	LCDdisplay, normal display|
|#STE2007_POWERCONTROL|	Blank|	Sets the on–chip power supply circuit operating mode|
|#STE2007_POWERCTRL_ALL_ON|	2F	|Booster, voltage regulator, voltage follower all on|
|#STE2007_DISPLAYNORMAL|	A6|	LCD Display|
|#STE2007_DISPLAYON	|AF	|Display on|

(This marks the end of the Mega Prelab.)


#Logic Analyzer
##Physical Communication

Complete the following table by finding the 4 calls to writeNokiaByte that generate the data sent to the LCD display to draw the veritcal bar.  

###Four Calls to writeNokiaByte:

|Number|Line|R12|R13|Purpose|
|---|---|---|---|---|
|1|66|NOKIA_DATA (0x0001)|0xE7|makes the dotted line for the LCD|
|2|276|NOKIA_CMD (0x0000)|0xB0|setting row (least significant nibble|
|3|288|NOKIA_CMD (0x0000)|0x10|setting column, least significant nibble is second part of address|
|4|294|NOKIA_CMD (0x0000)|0x0F|setting column, most significant nibble is first part of address|

For this part, it seemed that the most important information to be sent was if the pixles were lit or not, and where they were on the screen.  Additionally, the data that stores the current column must be longer than what keeps track of the rows because the screen is longer than it is tall.  

Also, it was know that the least/most significant nibbles had to be used for these addresses because the drawing clearly started at 0,0 when the code was implemented.  


##SW3 Logic Waveforms
|Line|Command/Data|8-bit Packet|
|---|---|---|
|66|DATA|0xE7|
|276|COMMAND|0xB0|
|288|COMMAND|0x10|
|294|COMMAND|0x0F|

The waveform from the logic analyzer can be seen below.  This was attained from running Dr. Coulston's code and clicking "single run" while the SW3 button was hit and released.  This was done for a rising edge on the clock. The total four packets of information can be seen in the picture below:  
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Overall%204%20packets.JPG "SW3 Pressed")
Notice: the CS signal was not show as it is intuitive.  Information may only be sent to the Nokia while the signal is low.  Therefore, the CS signal was low when the information was being sent in the packets.  


The four packets are then zoomed in on below: 

###Packet 1
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Packet%201.JPG "Packet 1")
The analyzation of the data must be focused on the packet.  The MOSI signal is responsive to rising edges.  This means that the MOSI signal will only send information on the rising edge of a clock.  During the packet of information, the bits that are send through the MOSI were 1110 0111.  This is equivalent to 0xE7, which is the value predicted in the table above!!!

###Packet 2
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Packet%202.JPG "Packet 2")
Information sent is 1011 0001.  This translates to 0xB1.  Oddly enough, this is not the value that was predicted, it was one off.  It is very close however, and could be because of sign conventions or something like that.  


###Packet 3
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Packet%203.JPG "Packet 3")
The information sent in the packet is 0001 0000, which translates to 0x10, the exact value predicted in the table above.  


###Packet 4
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Packet%204.JPG "Packet 4")
The information sent in the pack is 0000 0001. This is not 0x0F.  However, since the most significant nibble is used to keep track of the columns, this value does not play much significance.  



After the SW3 button was analyzed, the RESET signal was.  To do this the trigger for RESET was changed to falling edge and the trigger for the clock was changed to "don't care".  This is because the reset button is an active low.  A close up of the screenshot is shown below: 
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Reset%20Close%20Up.JPG "Reset up Close")

A screenshot was then taken of from farther out so that the time the reset was actively low could be measured.  
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Reset%20Measure.JPG "Reset up Close")

As evident from the screenshot above, the reset signal was low for about 18.64us.  It is hard to tell but at the M2 marker is when the reset signal changes.  It happens so quickly, compared to the length of time recorded, that its change can hardly be seen without zooming in.  That is the reason for the first picture.  

##Writing Modes
The operations AND, OR, and XOR were done on the images below.  A black square is a 1 and a blank square is a zero. When an image is compared with another image using one of the three operations just mentioned, each corresponding bit undergoes the same operation.  The result is then stored in a corresponding chart on a third grid.  
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/bitblock.bmp  "Writing Mode Answers")



#Functionality
##Required Functionality
This creates a block on the LCD that is 8x8 pixels.  The location of the block is passed into the subroutine via r12 and r13.

The code for this functionality can be seen [here](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/Creates%20Block.asm)


A screenshot of the code in action is shown below: 
![alt tag](https://raw.githubusercontent.com/JohnTerragnoli/ECE382_Lab03/master/One%20Brick.JPG "Required Functionality")

##A Functionality
This moves the 8-pixel block one block in the direction of the pressed button (up, down, left, right).


#Documentation: 
###Prelab: 
We all helped each other understand the concepts in the homework Sunday night.  
C2C Nathan Ruprecht, C2C Erik Thompson, C2C Austin Bolinger, C2C Sabin Park, C2C Kyle Jonas, C2C Jeremy Gruszka, C2C Kevin Cabusora, C2C Taylor Bodin, C2C Jarrod Wooden, C2C Sean Bapty, C2C Erica Lewandowski, and C2C Chris Kiernan.   
C2C Sabin Park and I were talking and he said he had EI with Dr. Coulston.  He said that the inputs are active low and that therefore to give them a zero is thus enabling them to set their pull up/down resistors.  Park and I also discussed how the waveform was going to constantly decrement and where the waveforms should change in relation to the clock.  We came to the conclusion that it would occur on the falling edge.  

###Rest of Lab: 
