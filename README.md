15 sept 2022:
For now i still debugging driver to compare asm functions and calls with a C++ code from nwdm. cpp, hFilter. cpp, HClone. cpp and others.
I still keep trying to find HClone section to change some values. 
Already done DriverEntryHelper function . I had defined addresses of 
shifts of most common used functions (for example...nvlddmkm+0x199f50 = NvDispatch() )! 
Work in progress. Don't worry, be happy! 

12 sept 2022:
During debugging I have found the code data section that returns data from PCIe interfere of graphic card. It writes to RSP register next sequence:
01 00 00 00 02 00 00 00 07 1C DE 40 ..
that means pcie v. 1 line width x2 and card id 1C07...

04 sept 2022:
I have changed the nvidia kernel driver 417.35,466.77,512.15
to make support all DirectX features,  such RayTracing by 
mining cards such as p106-090 and p106-100. 
My next target is to make these card work with pci-e version 3.0.
I have understanding where to find variables such pcilinkSpeed=1000
by disassembling code in Windows kernel debug mode. 

