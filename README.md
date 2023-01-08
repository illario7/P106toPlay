Hello here some results of my work with driver 417.35, you can examine it:

addresses352.txt - Addresses of most common functions. 

result39.txt - Results of my work since summer 2022.

Регистры.txt - most common registers wich setting bus speed ans wide.

security.vsd - function call diagram.

I have tried all possible ways to deal with gen1 x4, but so far nothing has worked, the conclusions are as follows:

1. Changing the video bios does not affect the bus in any way, everything is sewn into the pci config of the chip, actually in the magic bytes pciconfig +80h = 40 10, it reads like this: 1040, that is, 1 generation, 04 -number of lines. 

2. This value of 1040 is set even before Windows is loaded (before the driver), apparently when initializing the card in the bios, since when viewing the results of the first call to the getbusdata() function from acpi initialization, this value of 1040 is already set in pci config, which cannot be corrected in the debugger memory viewer, so at the time of the change someone, apparently the card, returns 1040 to the place. 
I checked these numbers by inserting the card into the x2 slot on the miniITX miniITX asus n3050i, and after the bios it turned out 1020, which indicates that the bios of the computer sets this value to the card based on its capabilities, and it prints it into its pci config. 

3.The value of 1040 cannot be changed in the driver's ChangeBusSpeed() function, since it is written in the driver itself in the comments that it is only possible to degrade the number of lines, and not increase, which I successfully tested on gtx1050, the function can dynamically decrease lines, but cannot dynamically increase their number. For example, for gtx 1050, this value is equal to 1100: the first digit 1 is always present before the driver is loaded, since the initialization of the hardware level always takes place at generation 1, and only then is raised by the driver in the function
ChangeBusSpeed() when the load increases. 

4. I am inclined to believe that the lines on the p106-90 are disconnected somehow logically inside the chip (in some registers), since I have rung all 16 dif lines. pairs of combs on the map and everywhere there was the same resistance.

Is there any information about how the order of the allocation of lines in the bios to the pci-e device (the order of the communication dialogue) occurs?
