## 12th October 2026
Currently I am trying to figure out how my board will interface with a USB to UART interface.

I've done a bit of research which I managed to dumb it down to using either:
- CH340
- FTDI FT230XS

However, I am trying to select something that I can procure from Mouser, so CH340 is not in my list. JCLPCB does have that option, but I want to be able to buy all of the parts myself. So, in this case, I am going for the FTDI FT230XS.

I managed to find 
![[Pasted image 20261012213644.png]]
Source: https://ftdichip.com/wp-content/uploads/2025/06/DS_FT230X.pdf

![[Pasted image 20261012213724.png]]Source: https://www.mouser.com/datasheet/2/163/AN_216_Using_the_FT230X_to_design_a_GPS_Dongle-267962.pdf

My implementation of the USB-UART conversion is as below:![[Pasted image 20261012213755.png]]
I then managed to find a reference online by from this guy: https://www.limpkin.fr/index.php?post/2014/11/27/A-Development-Board-for-the-ESP8266-03 which he showed his schematics on how he had implemented his USB->UART conversion.
![[Pasted image 20261013105958.png]]
Interestingly enough, as per the datasheet, he is routing his 3V3OUT to the CBUSx input to drive the LED. That's quite an interesting application, since I do want to be able to view if the USB chip is working or not! Another interesting thing to note is that he does not put any 10k ohm resistor to the USB Tx and Rx. The application note seems to be using that a lot and I am unsure how that works. One thing that should also not be forgotten is to invert Rx and Tx when it goes into the STM32 chip!

## Accelerometer
### LSM6DSM
![[Pasted image 20261014154300.png]]![[Pasted image 20261014154228.png]]
For the CS, what does it mean by 0,1? Is that basically saying that I need to have a pull up resistor on the CS pin to ensure that it is always logic high for the chip to be selected?

Actually, I understood ir wrongly, here is what I understand:
- 0: SPI mode selected, I2C disabled
- 1: SPI in idle, I2C enabled

I found a schematic from some research paper of the following schematic:
![[Pasted image 20261014155003.png|820]]
Source: https://aaltodoc.aalto.fi/server/api/core/bitstreams/0bbceb2b-3e8b-497c-96ac-d3c5c5635ea1/content

## Micro SD Card

I couldnt find any reference schematic on how to make your own micro sd card connection. However, I had managed to find the following blogpost and he also mentioned a mistake that he did which is very nice of him.

Quote from him:
> my next favourite mistake was the complete misunderstanding of the uSD populated switch. it really is just a switch with 2 pins. for some time saving reason I thought surely the switch pin just goes high when you insert a card. NO! the mechanical switch just bridges the 2 pins so you need to pull the line high with a resistor when connected or something similar. WRONG!!! this just does nothing :)

![[Pasted image 20261014181135.png]]
- Example source: [Hackaday post](https://hackaday.io/project/7041-multichannel-audio-dsp-field-mixer-recorder/log/158641-new-board-v12-with-tons-of-fixes-from-pcbway)

I am unsure why it is so difficult to find the mappings of SPI to the convention used for the sd card. I finally found a reference online here:
![[Pasted image 20261014201545.png]]
- [Life saver](https://www.google.com/url?sa=t&source=web&rct=j&url=https%3A%2F%2Fforum.armbian.com%2Ftopic%2F12736-orangepi-zero-external-sd-cad%2F&ved=0CBoQjhxqGAoTCNj-i8KaupcDFQAAAAAdAAAAABDcAg&opi=89978449)
My google search was: `micro sd card pinout spi -ai` and I basically went through google image to find the exact reference. Maybe I am just terrible at finding this information online.

### 17th October
I am now struggling for handling the placement of my sd card on the pcb editor. I want to make the board as clean as possible and right now here is what I have.
![[Pasted image 20261017075220.png]]It is quite compact but I have not finished my routing yet. It feels quite shifted to one side due to the micro sd card. I am using this as a sort of first pass reference on how other people have done it.
![[Pasted image 20261017075326.png|657]]![[Pasted image 20261017075340.png]]
Source: https://risaksson.com/Designing-and-building-an-embedded-Linux-computer

His board looks clean with not empty spots! To be honest, my current limitation is the fact that I am planning on doing a 2 layer PCB which means that I would only have the GND plane as the 2nd layer, and for the 3.3V layer, I am simply going to find a way to route it on the board itself. Since this is a small board, I think it is generally doable and I believe this is also going to teach me some good practice on how to route my wires on the PCB.
## Power electronics
![[Pasted image 20261015111348.png]]
Right now, what I am doing is quite simple. I take the VBUS output from the USB device, which provides 5V then fed it into the ferrite bead to filter high frequency noise on the signal and then fed it into the the linear voltage dropout (LDO) regulator stepping the 5V down to 3.3V (actually in the schematic, I have chosen the wrong part it should be ..P332 (3.3V) instead of ..P302 (3.0V)).

That aside, my new goal is to be able to connect an external power supply or connect a 3.7V lipo battery which I am thinking of this one,
![[Pasted image 20261015111604.png]]
to power my circuit. The objective is simple:
- When USB is connected with no external power input, then USB is responsible for providing the power for the circuit
- When the external power input is plugged in (power supply or battery), the board then seamlessly swaps to the other power source. In this case, we are no longer limited to the current limitation of the USB power supply!

My idea in my head is to basically have a transistor that handles the switching. Upon receiving power from the power supply, this then drives the gate, and then switched it over to the battery/power supply. This is of course, easier said than done, cause I have jack shit knowledge of power electronics. I graduated from youtube academy. So this is gonna be fun figuring it out how it works by finding online reference schematic on how people have approached it!

![[Pasted image 20261015134140.png]]

Adafruit used an interesting method.

It has a schottky diode for the VBAT which I believe is the external power input, and the VBUS is from the USB. See the JSTPH pin there, which I believe where it provides the power from the battery.

I am not exactly sure how this works because I am assuming that here if it is using a JSTPH and taking a 2 pin battery, this would simply be a single cell lipo battery which means that the power being provided would be 4.2V or maybe 3.7V the same as what I am planning on considering to use. In this case, the VBUS is providing 5V would be higher than the VBAT, hence why it requires the schottky diode. It basically prevents the current from flowing in the opposite direction due to the higher potential difference from the VBUS.

This setup is not really one that I am looking for is that if an external battery is connected, the battery will be the main power supply.



