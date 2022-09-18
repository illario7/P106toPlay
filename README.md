17 sept 2022:
Unfortunatly HCLONE not supported by Win10 and above, i keep trying to find different function to set PCI bandwidth.
Here is some debug points from DriverEntryHelper() function, according nvdm.cpp file. Driver version 417.35.


nt!PnpCallDriverEntry+0x47 Вызов точки входа DriverEntryHelper драйвера Операц.системой:
Функция точки входа <DriverEntryHelper>
 35_nvlddmkm!nvDumpConfig+0xa86c88 <DriverEntryHelper>
 35_nvlddmkm!nvDumpConfig+0xa8755f 			call    35_nvlddmkm+0x144454
 35 nvlddmkm+0x144db4 					call    qword ptr [nvlddmkm+0x774698]
   	nvLDDMkm: Driver Registry Path = '\REGISTRY\MACHINE\SYSTEM\ControlSet001\Services\nvlddmkm'
 35 nvlddmkm!nvDumpConfig+0xa876aa  call    nvlddmkm!nvDumpConfig+0x22a49c (заполняется) <call cacheBusInfo()>
 	Функция <void cacheBusInfo()>из nvdm.cpp ..здесь заполняются параметры шины.. и
         =FOR1= nvlddmkm!nvDumpConfig+0x22a501   // nwdm.cpp:2274 for (; i < NumObjects; ++i) {
 		nvlddmkm!nvDumpConfig+0x22a549          call    r10   <getPciInterface > -> <nt!KzLowerIrql> (становится в rsp вся инфа), но в память пока не скинута
                          mov     edx,0Eh  -   0Eh это DevicePropertyBusNumber = 0xe из файла <wdm.h>
 			 call    qword ptr [nvlddmkm+0x774608]   nt!IoGetDevicePropert
 				mov     edx,10h  -   10h это DevicePropertyAddress= 0x10 из файла <wdm.h>
 	 			call    qword ptr [nvlddmkm+0x774608]   nt!IoGetDevicePropert
 					В [rsp] содержится информация по текущему адаптеру из списка:
 					mov     edi,dword ptr [rsp+3Ch]        edi = deviceAddress
 					mov     r11d,dword ptr [rsp+34h]       r11 = busNumber
 					shr     edi,10h       			<ULONG deviceNumber = (deviceAddress >> 16) & 0xffff;> 
 					test    r11d,r11d 
          nvlddmkm!nvDumpConfig+0x22a719:cmp     r14d,20h        <nvAssert(ulGpuCount < MAX_DEVICES); MAX_DEVICES=20h>
 	Вся информация входе этой функции сохраняется в  g_GlobalData.pciBusInfo[ulGpuCount].pciCommonConfig это в [r15+0xCCh] = nvlddmkm!nvDumpConfig+0x494a8 
         =FOR1=  nvlddmkm!nvDumpConfig+0x22a795  // nwdm.cpp:2357 конец for (; i < NumObjects; ++i) } 
        nvlddmkm!nvDumpConfig+0x22a7a8:mov     dword ptr [nvlddmkm!nvDumpConfig+0x49570],r14d     <g_GlobalData.ulGpuCountFromBus = ulGpuCount;>
        nvlddmkm!nvDumpConfig+0x22a7af:mov     dword ptr [nvlddmkm!nvDumpConfig+0x4b774],esi <g_GlobalData.ulBrCountFromBus = ulBrCount;>
 
 Заполнение адресов глобальных Колбеков:
 1й адрес:
 <nvdm.cpp:1030-> DriverEntryHelper():g_GlobalData.NvDDICallbacks.DxgkDdiAddDevice  = NvDM_AddDevice;>
 nvlddmkm!nvDumpConfig+0xa8772f lea     rax,[nvlddmkm!nvDumpConfig+0x22706c ] //NvDM_AddDevice

 54адрес:
  <nvdm.cpp:1090-> DriverEntryHelper():g_GlobalData.NvDDICallbacks.DxgkDdiUpdateActiveVidPnPresentPath> Заполнение 54го адреса глобальных Колбеков
  nvlddmkm!nvDumpConfig+0xa87a22  mov     qword ptr [nvlddmkm!nvDumpConfig+0x4bdc8 (fffff807`7f4b9140)],rax

 94й адрес(true):
  <nvdm.cpp:1157-> DriverEntryHelper(): if (g_GlobalData.bWDDMv22EnableSetTimingsDdi) >
  nvlddmkm!nvDumpConfig+0xa87c68  mov     cl,byte ptr [nvlddmkm!nvDumpConfig+0x81602]
  nvlddmkm!nvDumpConfig+0xa87c6e  test    cl,cl <is true?>
  lea     rdx,[nvlddmkm!nvDumpConfig+0x1f0f08 (fffff807`7f65e280)]
  nvlddmkm!nvDumpConfig+0xa87c79   mov     qword ptr [nvlddmkm!nvDumpConfig+0x4c028],rdx //94й адрес

 102й адрес(true): 
  nvlddmkm!nvDumpConfig+0xa87ce2  test    cl,cl
  nvlddmkm!nvDumpConfig+0xa87ce6 lea     rdx,[nvlddmkm+0xd5e9c (fffff807`7ec25e9c)]
  nvlddmkm!nvDumpConfig+0xa87ced mov     qword ptr [nvlddmkm!nvDumpConfig+0x4c050 (fffff807`7f4b93c8)],rdx

 161 адрес(true):
 <nvdm.cpp:1272-> DriverEntryHelper(): if (!(g_GlobalData.bEmulator & EMULATOR_OPTION_NO_DISP)) >

 182 адрес:
 nvlddmkm!nvDumpConfig+0xa88118  mov     qword ptr [rbp+298h],rax

 212 адрес(true):
 nvlddmkm!nvDumpConfig+0xa882c9  mov     qword ptr [rbp+478h],rax

 217 адрес:
 <nvdm.cpp:1381-> DriverEntryHelper(): DriverInitData.DxgkDdiControlInterrupt3   = LDDM_ControlInterrupt3; //217>
 nvlddmkm!nvDumpConfig+0xa88315: mov     qword ptr [rbp+3F8h],rax
 
 <nvdm.cpp:1401-> DriverEntryHelper(): InitializeModsMode(&DriverInitData);>
 nvlddmkm!nvDumpConfig+0xa8831c   call    nvlddmkm!nvDumpConfig+0xa8a82c
 
<nvdm.cpp:1407-> DriverEntryHelper():  memcpy_s(&g_GlobalData.pDxgkrnlDispatch[0],sizeof(g_GlobalData.pDxgkrnlDispatch),pDriverObject->MajorFunction[0]) // pDriverObject->MajorFunction[ulFunction]  = NvDispatch;>
 nvlddmkm!nvDumpConfig+0xa8832b  lea     r15,[nvlddmkm+0x197a1c ] //установка указателя на функцию DRIVER_DISPATCH NvDispatch();

 <nvdm.cpp:1412-> DriverEntryHelper(): g_GlobalData.pDxgkrnlAddDevice  = pDriverObject->DriverExtension->AddDevice;>
 nvlddmkm!nvDumpConfig+0xa88337  lea     rdx,[nvlddmkm+0x197644]  //AddDevice = NvAddDevice

 <nvdm.cpp:1413> DriverEntryHelper(): g_GlobalData.pDxgkrnlUnload  = pDriverObject->DriverUnload; //
 nvlddmkm!nvDumpConfig+0xa8833e lea     r8,[nvlddmkm+0x199dcc ] //DriverUnload = NvUnload;

 -- <nvdm.cpp:1416> Начало FOR Setup default dispatch handlers
 -- for (int ulFunction = 0; ulFunction <= IRP_MJ_MAXIMUM_FUNCTION; ulFunction++)
 -- в этом цикле заполяется 28 указателей   g_GlobalData.pNvDispatch[0..27]  и g_GlobalData.pNvDispatch[0..27] одними и теми же функциями

  <nvdm.cpp:1418> DriverEntryHelper(): g_GlobalData.pNvDispatch[ulFunction]= NvDispatchPassthrough;
  nvlddmkm!nvDumpConfig+0xa8843a lea     rcx,[nvlddmkm+0x197b38] // NTSTATUS NvDispatchPassthrough
        nvlddmkm+0x1208f0 nvAssert()
        nvlddmkm+0x199f50 getAdapterAndAcquireRemoveLock()
  	
  <nvdm.cpp:1419> DriverEntryHelper(): g_GlobalData.pNvMgmtDispatch[ulFunction] = NvMgmtDispatchInvalidDeviceRequest;
  nvlddmkm!nvDumpConfig+0xa88448 lea     rcx,[nvlddmkm+0x199d28] // NTSTATUS NvMgmtDispatchInvalidDeviceRequest;
	nvlddmkm+0x774380  nt!IofCompleteRequest()
 
 --<nvdm.cpp:1416> Конец FOR Setup default dispatch handlers
 --nvlddmkm!nvDumpConfig+0xa8845d           cmp     rax,rcx  //Если указатель на первую фуннкцию Не РАВЕН указателю на последнюю функцию (счетчик IRP_MJ_MAXIMUM_FUNCTION=1b)
 --                                         jle     nvlddmkm!nvDumpConfig+0xa8843a //переход вверх по коду к заполнению нового указателя
	
 <nvdm.cpp:1422> DriverEntryHelper(): g_GlobalData.pNvDispatch[IRP_MJ_PNP] = NvDispatchPnp (  nvlddmkm+0x197c28)
 nvlddmkm+0x197c28 = NvDispatchPnp()
 
 <nvdm.cpp:1423> DriverEntryHelper(): g_GlobalData.pNvDispatch[IRP_MJ_POWER= NvDispatchPower      (nvlddmkm+0x198194 );
 <nvdm.cpp:1426> DriverEntryHelper(): g_GlobalData.pNvMgmtDispatch[IRP_MJ_CREATE]         = NvMgmtDispatchCreateClose;  (nvlddmkm+0x198b78)
 <nvdm.cpp:1427> DriverEntryHelper(): g_GlobalData.pNvMgmtDispatch[IRP_MJ_CLOSE]          = NvMgmtDispatchCreateClose;  (nvlddmkm+0x198b78)
 <nvdm.cpp:1428> DriverEntryHelper(): g_GlobalData.pNvMgmtDispatch[IRP_MJ_DEVICE_CONTROL] = NvMgmtDispatchDeviceControl;(nvlddmkm+0x198c08)


 <nvdm.cpp:1439> DriverEntryHelper():  if (canCreateHCloneLayer()) (nvlddmkm+0x17ee44)
 nvlddmkm!nvDumpConfig+0xa884bd  call    nvlddmkm+0x17ee44  //(canCreateHCloneLayer
	nvlddmkm+0x11ff2 NvIsWin10OrLater()  // Disable the HCLONE feature from Win10 and above.
	
Конец функции точки входа <DriverEntryHelper>

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

