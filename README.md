

15.10.2022-> работа с nvapiEscape.cpp: попытка вызова команды NV2080_CTRL_BUS_SET_PCIE_SPEED_8000MBPS  
nvapiEscape.cpp:30732     EXTERN_C BOOL nvapi_Escape(DRIVER_STATE_TYPE *pDrvState,ULONG iEsc, ULONG cjInOut, VOID *pvInOut)
nvapiEscape.cpp:31584     rmParams.busSpeed = NV2080_CTRL_BUS_SET_PCIE_SPEED_8000MBPS;
nvapiEscape.cpp:31593     rmStatus = NvControl(DRIVER_HANDLE, g_nvapi.hRmClient, pPhysicalGpu->rmSubDeviceObjectID,NV2080_CTRL_CMD_BUS_SET_PCIE_SPEED,&(rmParams), sizeof(rmParams));

#define NV2080_CTRL_CMD_BUS_SET_PCIE_SPEED       NV2080_CTRL_CMD(BUS, 0x05)
#define NV2080_CTRL_BUS_SET_PCIE_SPEED_8000MBPS                    (0x00000003)

<-15.10.2022 
===========================================================
14.10.2022->>путь до установки PCI_EXPRESS_GEN2
g_GlobalData.NvDDICallbacks.DxgkDdiCreateContext                   = NvLDDM_CreateContext;//31  
nvlddm.cpp:1239   NvLDDM_CreateContext(HANDLE hDevic..){ 					nvlddmkm!nvDumpConfig+0x1ea558
nvlddm.cpp:1266    Status = pDevice->createContext(pCreateContext);				nvlddmkm!nvDumpConfig+0x1ea658: call nvlddmkm!nvDumpConfig+0x2073e0
nvlContext.cpp:1343 CNvLDevice::createContext(DXGKARG_CREATECONTEXT* pCreateContext){		nvlddmkm!nvDumpConfig+0x2073e0
nvlContext.cpp:1471  pContext = createContextInstance(pCreateContext, &CreateContextData);	nvlddmkm!nvDumpConfig+0x2074ba:  call    nvlddmkm!nvDumpConfig + 0x207608
nvlContext.cpp:793    static CNvLContext* createContextInstance(){				  nvlddmkm!nvDumpConfig + 0x207608
nvlContext.cpp:803     CNvLContext *pContext = CNvLContext::createInstance(pCreateContext, pCreateContextData); nvlddmkm!nvDumpConfig+0x207646: call    nvlddmkm!nvDumpConfig + 0x207df0
nvlContext.cpp:97       CNvLContext::createInstance(DXGKARG_CREATECONTEXT* pCreateContext, CREATE_CONTEXT_DATA* pCreateContextData){nvlddmkm!nvDumpConfig + 0x207df0

nvlContext.cpp:116  CNvLContextKepler()     nvlddmkm!nvDumpConfig+0x207eab:    call    nvlddmkm!nvDumpConfig+0x22d2b0  
nvlContext.cpp:128 if (pContext != NULL)    nvlddmkm!nvDumpConfig+0x207f03: call    nvlddmkm!nvDumpConfig+0x23af94  {xor eax,eax} eax=94f92660	 
nvlContext.cpp:131    if (NT_SUCCESS(pContext->initializeProfileAndDebug())) ???
nvlContent.cpp:134     if (pCreateContext->Flags.SystemContext)       nvlddmkm!nvDumpConfig+0x207f14: mov     eax, dword ptr[r13 + 10h]  {eax=5}
		       иначе  переход на else см.  nvlContext.cpp:159 nvlddmkm!nvDumpConfig+0x207f1b: je      nvlddmkm!nvDumpConfig+0x207fbb	  
nvlContent.cpp:137	  pContext->setContextType(ContextSystem);    nvlddmkm!nvDumpConfig+0x207f26: call    nvlddmkm!nvDumpConfig+0x20b148 {ContextSystem=cType=edx=0;m_contextType=ptr[rcx+84h]=0xAh; => m_contextType = cType=0; return true=bl=1}
nvlContext.cpp:147	  if (pAdapter->useWDDM20()&& pAdapter->getPager()->getTilePoolComptagMgr() != NULL) nvlddmkm!nvDumpConfig+0x207f5c: cmp dword ptr[rsi + 0C630h], 2000h
nvlContext.cpp:149	    allocCbcCleanInvalidateWarResources(pDevice);                                    nvlddmkm!nvDumpConfig+0x207f7b: call    nvlddmkm+0x16e890  (дошел до точки!))))
nvlContext.cpp:153	  if (pAdapter->useWDDM21()&& vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv )          nvlddmkm!nvDumpConfig+0x207f8d: cmp dword ptr[rsi + 0C630h], 2100h
   выход  из if (NT_SUCCE.. по второй проверке        pCreateContext->Flags.SystemProtectedContext	     mov  eax,dword ptr [r13+10h] ; test    al,8 {al=5} условие не срабатывает! и переход на конец:  nvlddmkm!nvDumpConfig+0x207fa3:  je  nvlddmkm!nvDumpConfig+0x209311
nvlContext.cpp:159   Обработка else от {if (pCreateContext->Flags.SystemContext)} //Not the system context// nvlddmkm!nvDumpConfig+0x207fbb
 	Адреса переменных:
	pCreateContext->pPrivateDriverData 		ptr [r13+18h] 
        pCreateContext->PrivateDriverDataSize		ptr [r13+20h] 

1вызов nvlContext.cpp:259 VALIDATION_LOG_DPF(..			 nvlddmkm!nvDumpConfig+0x2080d6:  call    nvlddmkm+0x1445f0
2вызов nvlContext.cpp:291 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x20846e:  call    nvlddmkm+0x1445f0
3вызов nvlContext.cpp:311 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x2084c3:  call    nvlddmkm+0x1445f0
4вызов nvlContext.cpp:326 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x208714:  call    nvlddmkm+0x1445f0
5вызов nvlContext.cpp:340 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x208800:  call    nvlddmkm+0x1445f0   
6вызов  nvlContext.cpp:371 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x208935:  call    nvlddmkm+0x1445f0   
7вызов  nvlContext.cpp:399 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x208b34:  call    nvlddmkm+0x1445f0   
8вызов  nvlContext.cpp:416 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x208c58:  call    nvlddmkm+0x1445f0   
//искомый код между 8м и 9м вызовами  VALIDATION_LOG_DPF:
nvlContext.cpp:448       pContext->setLockPexGen2WAR(true);
nvlContext.cpp:449       pAdapter->lockPexGen2WAR(true);	nvlddmkm!nvDumpConfig+0x208cff:   call    nvlddmkm+0xeb964
	#define NV2080_CTRL_CMD_PERF_LOCK_PEX_GEN2         NV2080_CTRL_CMD(PERF, 0x8B)
									      nvlddmkm+0xeb9e7:   mov     r8d,2080208Bh		// NV2080_CTRL_CMD_PERF_LOCK_PEX_GEN2=2080 020 8B
	nvlAdapter.cpp:15602        if (!pChannel->rmControl(pChannel->hSubDevice(ulEngine), NV2080_CTRL_CMD_PERF_LOCK_PEX_GEN2,...   nvlddmkm+0xeb9f8:   call    nvlddmkm+0x150350	
если не получится надо искать вызов функции NV2080_CTRL_BUS_SET_PCIE_SPEED_8000MBPS в области nvapiEscape, это вызывается из Система->NvMgmtDispatchDeviceControl()-> nvapi_Escape(...)[nvlFilter.cpp:2758] 
и далее идем в реализацию nvapiEscape.cpp:30732

9вызов  nvlContext.cpp:466 VALIDATION_LOG_DPF(..			 nvlddmkm!nvDumpConfig+0x208dee:  call    nvlddmkm+0x1445f0  
10вызов  nvlContext.cpp:481 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x208e01:  call    nvlddmkm+0x1445f0  
11вызов  nvlContext.cpp:491 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x208f00:  call    nvlddmkm+0x1445f0 
12вызов  nvlContext.cpp:525 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x209003:  call    nvlddmkm+0x1445f0 
13вызов  nvlContext.cpp:542 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x2091ca:  call    nvlddmkm+0x1445f0 
14вызов  nvlContext.cpp:556 VALIDATION_LOG_DPF(..   			 nvlddmkm!nvDumpConfig+0x2092b4:  call    nvlddmkm+0x1445f0
nvlContext.cpp:564 pContext->setContextType(ContextKernel); 	 nvlddmkm!nvDumpConfig+0x2092d8:  call    nvlddmkm!nvDumpConfig+0x20b148
nvlContext.cpp:584 if (pAdapter->useWDDM20()... 		 nvlddmkm!nvDumpConfig+0x209311:  cmp     dword ptr [rsi+0C630h],edi
<-14.10.2022
======================================================== 
28.09.2022->> ищем адрес rmControl через вызов nvlddm.cpp:2853 NTSTATUS NvDM_StartDevice   ( nvlddmkm!nvDumpConfig+0x2298b0 )     
	[nvRegistryReadFTS(...)= nvlddmkm+0x17363c]
	[NV_ETW_INFO(...)=  nvlddmkm+0x148990
nvlddm.cpp:2892 Status = pAdapter->startDevice() 	nvlddmkm!nvDumpConfig+0x229a09: call nvlddmkm+0xf0894
nvlAdapter.cpp:2841   NTSTATUS  CNvLAdapter::startDevice(PDXG...){ 
	[createProcessNotify()=nvlddmkm+0x135630]
 nvlAdapter.cpp:2876 if (getInterfaceVersion() >= DXGKDDI_INTERFACE_VERSION_WIN8) //nvlddmkm + 0xf0961: cmp     dword ptr [rax+4],300Eh ds:002b
 nvlAdapter.cpp:2889 nvAssert(m_pMCMgr==NULL); // nvlddmkm+0xf0994:
 nvlAdapter.cpp:2919  if (.... = startEventThread()...)				  // nvlddmkm+0xf0a2c: call  nvlddmkm+0x125f34    { nvlddmkm+0x124b38 =EventRoutine() nvlEvent.cpp:906  }
 nvlAdapter.cpp:2926  Status = setupAdapter(bUseOsPostDeviceAsPrimary, 		  // nvlddmkm+0xf0a48: call [rax+50]	 = nvlddmkm+0x129c24
nvlInit.cpp:855  NTSTATUS CNvLAdapter::setupAdapter(    //nvlddmkm+0x129c24
 nvlInit.cpp:972 ...Status = buildDeviceInfo()... 				//nvlddmkm+0x129e0b: call    nvlddmkm+0x12756c
	buildDeviceInfo(){ //nvlddmkm+0x12756c
		CNvLBaseAdapter::enableDevice(void) {  //nvlddmkm+0x128064
			m_PciInterface->SetBusData  //nvlddmkm+0x1280b0: call [rax+30]? вычислить!
 nvlInit.cpp:1004   if (!rmAlloc(NV01_NUcall        //nvlddmkm+0x14ca40
 nvlInit.cpp:1019   if (!rmControl(hClient(), NV0000_CTRL_CMD_OS_SET_WRITE_COMBINE_POLICY, &Policy, sizeof(Policy)))  //nvlddmkm+0x129f1e     call    nvlddmkm+0x15026c
  	nvlRm.cpp:1172  ::rmControl(&params, kernelMode);	// nvlddmkm+0x150309:    call    nvlddmkm+0x15006c
		nvlRm.cpp:5219     Nv04ControlKernel(pParams); //nvlddmkm+0x150104: call nvlddmkm+0x1dbb30    
<--28.09.2022
=====================================================

17 sept 2022:
Unfortunatly HCLONE not supported by Win10 and above, i keep trying to find different function to set PCI bandwidth.
Here is some debug points from DriverEntryHelper() function, according nvdm.cpp file. Driver version 417.35.


nt!PnpCallDriverEntry+0x47 Вызов точки входа DriverEntryHelper драйвера Операц.системой:

Функция точки входа <DriverEntryHelper>
	
 nvlddmkm!nvDumpConfig+0xa86c88 <DriverEntryHelper>
	
  nvlddmkm!nvDumpConfig+0xa8755f 			call    nvlddmkm+0x144454
	
  nvlddmkm+0x144db4 					call    qword ptr [nvlddmkm+0x774698]
		
  Пишется на экран строка:
	nvLDDMkm: Driver Registry Path = '\REGISTRY\MACHINE\SYSTEM\ControlSet001\Services\nvlddmkm'
	
  nvlddmkm!nvDumpConfig+0xa876aa  call    nvlddmkm!nvDumpConfig+0x22a49c (заполняется) call cacheBusInfo()
	
 	Функция void cacheBusInfo()из nvdm.cpp ..здесь заполняются параметры шины..
	
         =FOR1= nvlddmkm!nvDumpConfig+0x22a501   // nwdm.cpp:2274 for (; i < NumObjects; ++i) {
											
 		nvlddmkm!nvDumpConfig+0x22a549          call    r10   <getPciInterface > -> <nt!KzLowerIrql> (становится в rsp вся инфа), 
	но в память пока не скинута
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

