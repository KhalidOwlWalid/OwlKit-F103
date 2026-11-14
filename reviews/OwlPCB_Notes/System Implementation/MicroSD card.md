I couldnt find any reference schematic on how to make your own micro sd card connection. However, I had managed to find the following blogpost and he also mentioned a mistake that he did which is very nice of him.

Quote from him:
> my next favourite mistake was the complete misunderstanding of the uSD populated switch. it really is just a switch with 2 pins. for some time saving reason I thought surely the switch pin just goes high when you insert a card. NO! the mechanical switch just bridges the 2 pins so you need to pull the line high with a resistor when connected or something similar. WRONG!!! this just does nothing :)

![[Pasted image 20261014181135.png]]
- Example source: [Hackaday post](https://hackaday.io/project/7041-multichannel-audio-dsp-field-mixer-recorder/log/158641-new-board-v12-with-tons-of-fixes-from-pcbway)

I am unsure why it is so difficult to find the mappings of SPI to the convention used for the sd card. I finally found a reference online here:
![[Pasted image 20261014201545.png]]
- [Life saver](https://www.google.com/url?sa=t&source=web&rct=j&url=https%3A%2F%2Fforum.armbian.com%2Ftopic%2F12736-orangepi-zero-external-sd-cad%2F&ved=0CBoQjhxqGAoTCNj-i8KaupcDFQAAAAAdAAAAABDcAg&opi=89978449)
My google search was: `micro sd card pinout spi -ai` and I basically went through google image to find the exact reference. Maybe I am just terrible at finding this information online.

I am now struggling for handling the placement of my sd card on the pcb editor. I want to make the board as clean as possible and right now here is what I have.
![[Pasted image 20261017075220.png]]

It is quite compact but I have not finished my routing yet. It feels quite shifted to one side due to the micro sd card. I am using this as a sort of first pass reference on how other people have done it.
![[Pasted image 20261017075326.png|657]]![[Pasted image 20261017075340.png]]
Source: https://risaksson.com/Designing-and-building-an-embedded-Linux-computer

His board looks clean with not empty spots! To be honest, my current limitation is the fact that I am planning on doing a 2 layer PCB which means that I would only have the GND plane as the 2nd layer, and for the 3.3V layer, I am simply going to find a way to route it on the board itself. Since this is a small board, I think it is generally doable and I believe this is also going to teach me some good practice on how to route my wires on the PCB.
