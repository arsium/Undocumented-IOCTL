# Undocumented-IOCTL

Repository dedicated to undocumented IOCTL code from Microsoft drivers and open through a device to communicate with. Extracted with Binary Ninja.
I let you reverse input/output buffer.

## WANARP.sys

### Device

```c
\\Device\\WANARP\\
```

### Dispatch Method (WanDispatch)

```c
1c0016600    uint64_t WanDispatch(int64_t arg1, IRP* arg2)

1c0016620        IRP* Irp = arg2
1c0016623        char* Overlay = arg2->Tail.Overlay.__offset(0x40).q
1c001662a        int64_t rsi
1c001662a        rsi.b = arg1 == data_1c00087d8
1c001662e        arg2->IoStatus.Information = 0
1c0016633        char rax = *Overlay
1c0016637        NTSTATUS rbx_1
1c0016637        
1c0016637        if (rax == 0)
1c0017179            if (g_fPagesLocked == 0)
1c0017182                MmLockPagableDataSection(AddressWithinSection: WanpCreateAdapter)
1c001718e                g_fPagesLocked = 1
1c001718e            
1c0017195            Overlay.b = rsi.b
1c0017198            NTSTATUS rax_4
1c0017198            rax_4, arg2 = WanpStartModule(Overlay.b)
1c001719d            rbx_1 = rax_4
1c001719d            
1c00171a1            if (rax_4 != STATUS_SUCCESS)
1c00171a8                if (rax_4 != STATUS_PENDING)
1c00171a8                    goto label_1c00166cc
1c00171a8                
1c00171b8                void* Object = &data_1c00087e8
1c00171b8                
1c00171bf                if (rsi.b == 0)
1c00171bf                    Object = &data_1c0008908
1c00171bf                
1c00171dd                rbx_1 = KeWaitForSingleObject(Object, WaitReason: Executive, WaitMode: 0, 
1c00171dd                    Alertable: 0, Timeout: nullptr)
1c00171dd            
1c00171e2            char rax_6
1c00171e2            rax_6, arg2 = EnterModuleCode(rsi.b)
1c00171e2            
1c00171e9            if (rax_6 == 0)
1c00171ef                rbx_1 = STATUS_UNSUCCESSFUL
1c00171f4                goto label_1c00166cc
1c00171f4            
1c00166bf        label_1c00166bf:
1c00166bf            ExitModuleCode(rsi.b)
1c00166bf            
1c00166ca            if (rbx_1 != STATUS_PENDING)
1c00166ca                goto label_1c00166cc
1c0016637        else
1c001663f            if (rax != 0xe)
1c0017068                if (rax == 2)
1c001716b                    rbx_1 = STATUS_SUCCESS
1c0017068                else if (rax == 0x12)
1c001707c                    rbx_1 = STATUS_SUCCESS
1c001707e                    Overlay.b = rsi.b
1c0017081                    WanpDriverCleanupNotification(Overlay.b)
1c0017070                else
1c0017072                    rbx_1 = STATUS_INVALID_PARAMETER
1c0017072                
1c0017068                goto label_1c00166cc
1c0017068            
1c0016645            int32_t rbx = *(Overlay + 0x18)
1c0016648            int32_t rbp_1 = *(Overlay + 0x10)
1c001664b            int32_t r14_1 = *(Overlay + 8)
1c001664f            Overlay.b = rsi.b
1c0016652            char rax_1
1c0016652            int64_t r8_1
1c0016652            rax_1, arg2, r8_1 = EnterModuleCode(Overlay.b)
1c0016652            
1c0016659            if (rax_1 != 0)
1c0016666                if (rbx u> 0x90018018)
1c0016704                    if (rbx == 0x90018028)
1c00166ba                        rbx_1 = WanSetInterfaceSubnetInfo(Irp->AssociatedIrp.MasterIrp, 
1c00166ba                            rbp_1, r8_1.d)
1c0016704                    else if (rbx == 0x9001801c)
1c00166ba                        rbx_1 = WanAllocLuidIndex(Irp, rbp_1, r14_1)
1c0017104                    else if (rbx == 0x90018020)
1c00166ba                        rbx_1 = WanFreeLuidIndex(Irp, rbp_1)
1c001710c                    else if (rbx == 0x90018024)
1c00166ba                        rbx_1 = WanRecreateInterface(Irp, rbp_1, r8_1, rsi.b)
1c0017114                    else if (rbx != 0x9001802c)
1c0016717                        rbx_1 = STATUS_INVALID_PARAMETER
1c001711c                    else
1c00166ba                        rbx_1 = WanSetIpAddresses(Irp, rbp_1, r8_1, rsi.b)
1c0016666                else if (rbx == 0x90018018)
1c00166ba                    rbx_1 = WanStartStopQueuing(Irp, rbp_1, r14_1, rsi.b)
1c001666c                else if (rbx == 0x90018000)
1c00166ba                    rbx_1 = WanProcessNotification(Irp, rbp_1, r14_1, rsi.b)
1c0016678                else if (rbx == 0x90018004)
1c00166ba                    rbx_1 = WanAddUserModeInterface(Irp, rbp_1, r14_1, rsi.b)
1c0016684                else if (rbx == 0x90018008)
1c00166ba                    rbx_1 = WanDeleteUserModeInterface(Irp, rbp_1, r8_1, rsi.b)
1c0016690                else if (rbx == 0x9001800c)
1c00166ba                    rbx_1 = WanProcessConnectionFailure(Irp, rbp_1, r8_1, rsi.b)
1c001669c                else if (rbx != 0x90018014)
1c0016717                    rbx_1 = STATUS_INVALID_PARAMETER
1c00166a8                else
1c00166ba                    rbx_1 = WanMapServerAdapter(Irp, rbp_1, r14_1, rsi.b)
1c00166ba                
1c0016666                goto label_1c00166bf
1c0016666            
1c001708c            rbx_1 = STATUS_NO_SUCH_DEVICE
1c00166cc        label_1c00166cc:
1c00166cc            arg2.b = 2
1c00166ce            Irp->IoStatus..Status = rbx_1
1c00166d4            IofCompleteRequest(Irp, PriorityBoost: arg2.b)
1c00166d4        
1c00166fc        return zx.q(rbx_1)

```

### IOCTL

```c
#define IOCTL_WANARP_PROCESS_NOTIFICATION         0x90018000
#define IOCTL_WANARP_ADD_USERMODE_INTERFACE       0x90018004
#define IOCTL_WANARP_DELETE_USERMODE_INTERFACE    0x90018008
#define IOCTL_WANARP_PROCESS_CONNECTION_FAILURE   0x9001800C
#define IOCTL_WANARP_MAP_SERVER_ADAPTER           0x90018014
#define IOCTL_WANARP_START_STOP_QUEUING           0x90018018
#define IOCTL_WANARP_ALLOC_LUID_INDEX             0x9001801C
#define IOCTL_WANARP_FREE_LUID_INDEX              0x90018020
#define IOCTL_WANARP_RECREATE_INTERFACE           0x90018024
#define IOCTL_WANARP_SET_INTERFACE_SUBNET_INFO    0x90018028
#define IOCTL_WANARP_SET_IP_ADDRESSES             0x9001802C

```

## TCPIP.sys

### Device

```c
\\Device\\Tcp\\
```

### Dispatch Method (EQoSDispatchIoctl)

```c
1c01425cc    uint64_t EQoSDispatchIoctl(int32_t arg1, int32_t* arg2, int32_t arg3, int32_t* arg4, int32_t arg5, uint64_t* arg6)

1c01425f2        int16_t* rsi = arg2
1c01425fb        int32_t var_98
1c01425fb        NTSTATUS rbx_1
1c01425fb        bool cond:0_1
1c01425fb        
1c01425fb        switch (arg1)
1c01425f5            case 0xc07fc000
1c01428ca                if (arg5 == 0)
1c01428de                    rbx_1 = EQoSProcessPolicyChangeNotification(rsi, arg3)
1c01428ca                else
1c01428cc                    rbx_1 = STATUS_INVALID_PARAMETER
1c0142601            case 0xc07fcfa0
1c01428a2                if (arg3 u< 0x50 || arg5 != 0)
1c01428cc                    rbx_1 = STATUS_INVALID_PARAMETER
1c01428a2                else
1c01428de                    rbx_1 =
1c01428de                        UQoSCreatePolicyInternal(data_1c020ccc0, 0, 1, rsi, arg3, nullptr)
1c014260d            case 0xc07fcfa4
1c014287f                if (arg3 u< 0x50 || arg5 != 0)
1c01428cc                    rbx_1 = STATUS_INVALID_PARAMETER
1c014287f                else
1c01428de                    rbx_1 = UQoSUpdatePolicyInternal(data_1c020ccc0, nullptr, rsi, arg3)
1c0142619            case 0xc07fcfa8
1c01427e7                int32_t var_64_1 = 0
1c01427ea                uint64_t var_80 = 0
1c01427ee                UNICODE_STRING DestinationString_1
1c01427ee                DestinationString_1.__offset(0x4).d = 0
1c01427ee                
1c01427fe                if (arg3 u< 2 || arg5 != 0)
1c01428cc                    rbx_1 = STATUS_INVALID_PARAMETER
1c01427fe                else
1c014280e                    NTSTATUS rax_14 = RtlStringCbLengthW(rsi, zx.q(arg3), &var_80)
1c0142813                    rbx_1 = rax_14
1c0142813                    
1c0142817                    if (rax_14 s>= STATUS_SUCCESS)
1c014281d                        uint64_t rax_15 = var_80
1c014281d                        
1c0142827                        if (rax_15 u>= 0xffff)
1c01428cc                            rbx_1 = STATUS_INVALID_PARAMETER
1c0142827                        else
1c0142830                            int16_t* var_60_1 = rsi
1c0142838                            int16_t SourceString = rax_15.w
1c0142840                            int16_t var_66_1 = rax_15.w
1c0142844                            DestinationString_1.Buffer = rsi
1c0142848                            DestinationString_1.Length = 0
1c014284c                            DestinationString_1.MaximumLength = rax_15.w
1c0142850                            NTSTATUS rax_16 = RtlDowncaseUnicodeString(
1c0142850                                DestinationString: &DestinationString_1, &SourceString, 
1c0142850                                AllocateDestinationString: 0)
1c014285c                            rbx_1 = rax_16
1c014285c                            
1c0142860                            if (rax_16 s>= STATUS_SUCCESS)
1c01428de                                rbx_1 = UQoSDeletePolicyByNameInternal(data_1c020ccc0, 
1c01428de                                    &SourceString)
1c0142625            case 0xc07fcfac
1c0142794                if (arg3 u< 4 || arg5 u< 0x58)
1c01428cc                    rbx_1 = STATUS_INVALID_PARAMETER
1c0142794                else
1c01427a0                    int32_t rdx_5 = *arg2 & 1
1c01427a3                    var_98.q = &arg4[1]
1c01427b1                    *arg4 = arg5 - 8
1c01427b4                    arg4[1] = 0
1c01427ba                    NTSTATUS rax_12 = UQoSEnumerateAllPoliciesInternal(0, rdx_5 * 2, 
1c01427ba                        &arg4[2], arg4, var_98)
1c01427bf                    rbx_1 = rax_12
1c01427bf                    
1c01427c3                    if (rax_12 s>= STATUS_SUCCESS)
1c0142774                        *arg6 = zx.q(*arg4 + 8)
1c01427c3                    else
1c01427c5                        cond:0_1 = rax_12 != STATUS_BUFFER_TOO_SMALL
1c01427ca                    label_1c01427ca:
1c01427ca                        
1c01427ca                        if (not(cond:0_1))
1c01427d4                            rbx_1 = STATUS_BUFFER_OVERFLOW
1c01427d9                            *arg6 = 4
1c0142631            case 0xc07fcfb0
1c0142647                int32_t var_74_1 = 0
1c014264d                uint64_t var_88 = 0
1c0142655                UNICODE_STRING SourceString_1
1c0142655                SourceString_1.Length = 0
1c0142655                SourceString_1.MaximumLength = 0
1c0142655                SourceString_1.Buffer = 0
1c0142659                KLOCK_QUEUE_HANDLE LockHandle
1c0142659                __builtin_memset(dest: &LockHandle, ch: 0, count: 0x18)
1c0142659                
1c014266f                if (arg3 u< 0x18 || arg5 u< 0x58)
1c01428cc                    rbx_1 = STATUS_INVALID_PARAMETER
1c014266f                else
1c0142675                    int32_t r12_1 = *arg2
1c0142687                    NTSTATUS rax_1 =
1c0142687                        RtlStringCbLengthW(&arg2[5], zx.q(arg3 - 0x14), &var_88)
1c014268c                    rbx_1 = rax_1
1c014268c                    
1c0142690                    if (rax_1 s>= STATUS_SUCCESS)
1c0142696                        uint64_t rax_2 = var_88
1c0142696                        
1c01426a0                        if (rax_2 u>= 0xffff)
1c01428cc                            rbx_1 = STATUS_INVALID_PARAMETER
1c01426a0                        else
1c01426a9                            SourceString_1.Buffer = &arg2[5]
1c01426b1                            SourceString_1.Length = rax_2.w
1c01426b9                            SourceString_1.MaximumLength = rax_2.w
1c01426bd                            int16_t* var_70_1 = &arg2[5]
1c01426c1                            int16_t DestinationString = 0
1c01426c5                            int16_t var_76_1 = rax_2.w
1c01426c9                            NTSTATUS rax_3 = RtlDowncaseUnicodeString(&DestinationString, 
1c01426c9                                SourceString: &SourceString_1, 
1c01426c9                                AllocateDestinationString: 0)
1c01426d5                            rbx_1 = rax_3
1c01426d5                            
1c01426d9                            if (rax_3 s>= STATUS_SUCCESS)
1c01426ea                                KeAcquireInStackQueuedSpinLock(
1c01426ea                                    SpinLock: &EQoSPolicyOwnerCacheListLock, &LockHandle)
1c01426ea                                
1c01426fc                                while (data_1c0205608 != 0)
1c01426fc                                    nop
1c01426fc                                
1c0142704                                void** rax_5 = EQoSLookupPolicyOwner(&rsi[2])
1c0142710                                KeReleaseInStackQueuedSpinLock(&LockHandle)
1c0142710                                
1c014271f                                if (rax_5 != 0)
1c014272f                                    var_98.q = arg4
1c0142738                                    *arg4 = arg5 - 8
1c014274f                                    rbx_1 = UQoSQueryPolicyByNameInternal(rax_5, 
1c014274f                                        &DestinationString, (r12_1 & 1) * 2, &arg4[2], 
1c014274f                                        var_98)
1c0142754                                    rax_5[2].d -= 1
1c0142754                                    
1c014275c                                    if (rax_5[2].d == 1)
1c0142761                                        EQoSDeletePolicyOwner(rax_5)
1c0142761                                    
1c0142768                                    if (rbx_1 s< STATUS_SUCCESS)
1c014277c                                        cond:0_1 = rbx_1 != STATUS_BUFFER_TOO_SMALL
1c0142782                                        goto label_1c01427ca
1c0142782                                    
1c0142774                                    *arg6 = zx.q(*arg4 + 8)
1c014271f                                else
1c0142721                                    rbx_1 = STATUS_NOT_FOUND
1c01425fb            default
1c0142639                rbx_1 = STATUS_INVALID_DEVICE_REQUEST
1c0142639        
1c0142902        return zx.q(rbx_1)


```

### IOCTL

```c
// QoS Policy Management IOCTLs
#define IOCTL_EQOS_POLICY_CHANGE_NOTIFICATION    0xC07FC000
#define IOCTL_EQOS_CREATE_POLICY                 0xC07FCFA0
#define IOCTL_EQOS_UPDATE_POLICY                 0xC07FCFA4
#define IOCTL_EQOS_DELETE_POLICY_BY_NAME         0xC07FCFA8
#define IOCTL_EQOS_ENUMERATE_ALL_POLICIES        0xC07FCFAC
#define IOCTL_EQOS_QUERY_POLICY_BY_NAME          0xC07FCFB0
```

### Dispatch Method (IPSecDispatchDevCtl)

```c
1c0234900    uint64_t IPSecDispatchDevCtl(int64_t arg1, IRP* arg2)

1c023490f        int32_t arg_18 = 0
1c023491a        int32_t rax_1
1c023491a        
1c023491a        if (IPSecCheckDriverUnloadingAndIncrementOperationCount() == 0)
1c0234933            void* Overlay = arg2->Tail.Overlay.__offset(0x40).q
1c023493a            arg2->IoStatus..Status = 0
1c023493d            arg2->IoStatus.Information = 0
1c0234941            int32_t* MasterIrp = arg2->AssociatedIrp.MasterIrp
1c0234945            int32_t r8_1 = *(Overlay + 0x18)
1c0234949            uint64_t rcx = zx.q(*(Overlay + 0x10))
1c023494c            int32_t r10_1 = *(Overlay + 8)
1c023494c            
1c0234958            if (r8_1 u> 0x128008)
1c0234a02                if (r8_1 == 0x12800c)
1c0234a4f                    rax_1 = IPSecExpireInboundDeleteOutboundSaHndlr(rcx.d, MasterIrp)
1c0234a02                else if (r8_1 == 0x128010)
1c0234a48                    rax_1 = IPSecResponderCreateSPIHndlr(rcx.d, r10_1, MasterIrp, 
1c0234a48                        MasterIrp, &arg_18)
1c0234a08                else if (r8_1 == 0x128018)
1c0234a2e                    rax_1 = IPSecUpdateSaInfoHndlr(rcx.d, MasterIrp)
1c0234a0e                else if (r8_1 == 0x12801c)
1c0234a27                    rax_1 = IPSecNotifyStatusHndlr(rcx.d, MasterIrp)
1c0234a14                else if (r8_1 != 0x128028)
1c0234929                    rax_1 =
1c0234929                        WfpReportAppErrorAsNtStatus(rcx, "IPSecDispatchDevCtl", 0xc0000001)
1c0234a1a                else
1c0234a20                    rax_1 = IPsecSetS2STunnelInterfaceHndlr(rcx, MasterIrp)
1c0234958            else if (r8_1 == 0x128008)
1c02349f4                rax_1 = IPSecDeleteInboundOutboundSaPairHndlr(rcx.d, MasterIrp)
1c023495e            else if (r8_1 == 0x124014)
1c02349ed                rax_1 = IPSecQueryGlobalStatisticsHndlr(r10_1, MasterIrp, &arg_18)
1c023496b            else if (r8_1 == 0x124020)
1c02349de                rax_1 = IPsecQueryOffloadDone(r10_1, MasterIrp, &arg_18)
1c0234971            else if (r8_1 == 0x124024)
1c02349cf                rax_1 = IPSecQuerySaPropertiesHndlr(rcx.d, r10_1, MasterIrp, MasterIrp, 
1c02349cf                    &arg_18)
1c0234977            else if (r8_1 == 0x12402c)
1c02349b2                rax_1 = IPsecGetS2STunnelInterfaceHndlr(rcx, MasterIrp, r10_1, MasterIrp, 
1c02349b2                    &arg_18)
1c023497d            else if (r8_1 == 0x128000)
1c0234998                rax_1 = IPSecAddOutboundSaHndlr(rcx.d, MasterIrp)
1c0234986            else if (r8_1 != 0x128004)
1c0234929                rax_1 = WfpReportAppErrorAsNtStatus(rcx, "IPSecDispatchDevCtl", 0xc0000001)
1c023498c            else
1c023498e                rax_1 = IPSecUpdateLarvalInboundSaHndlr(rcx.d, MasterIrp)
1c023491a        else
1c0234929            rax_1 = WfpReportAppErrorAsNtStatus(0, "IPSecDispatchDevCtl", 0xc0000001)
1c0234929        
1c0234a57        int32_t rax_2 = WfpErrorToNtStatus(rax_1)
1c0234a57        
1c0234a63        if (rax_2 != 0x103)
1c0234a6b            arg2->IoStatus.Information = zx.q(arg_18)
1c0234a72            arg2->IoStatus..Status = rax_2
1c0234a75            IofCompleteRequest(Irp: arg2, PriorityBoost: 0)
1c0234a75        
1c0234a81        IPSecDecrementActiveOperationCount()
1c0234a92        return zx.q(rax_2)


```

### IOCTL

```c
// IPSec Security Association (SA) Management - Write Operations (0x128xxx)
#define IOCTL_IPSEC_ADD_OUTBOUND_SA                    0x128000
#define IOCTL_IPSEC_UPDATE_LARVAL_INBOUND_SA           0x128004
#define IOCTL_IPSEC_DELETE_INBOUND_OUTBOUND_SA_PAIR    0x128008
#define IOCTL_IPSEC_EXPIRE_INBOUND_DELETE_OUTBOUND_SA  0x12800C
#define IOCTL_IPSEC_RESPONDER_CREATE_SPI               0x128010
#define IOCTL_IPSEC_UPDATE_SA_INFO                     0x128018
#define IOCTL_IPSEC_NOTIFY_STATUS                      0x12801C
#define IOCTL_IPSEC_SET_S2S_TUNNEL_INTERFACE           0x128028

// IPSec Query Operations (0x124xxx)
#define IOCTL_IPSEC_QUERY_GLOBAL_STATISTICS            0x124014
#define IOCTL_IPSEC_QUERY_OFFLOAD_DONE                 0x124020
#define IOCTL_IPSEC_QUERY_SA_PROPERTIES                0x124024
#define IOCTL_IPSEC_GET_S2S_TUNNEL_INTERFACE           0x12402C

```

### Dispatch Method (KfdDispatchDevCtl)

```c
1c008a8d0    uint64_t KfdDispatchDevCtl(int64_t arg1, IRP* arg2)

1c008a8e7        void* Overlay = arg2->Tail.Overlay.__offset(0x40).q
1c008a8f2        void* MasterIrp = arg2->AssociatedIrp.MasterIrp
1c008a8f9        int32_t rbx = 0
1c008a8fb        uint64_t r12 = zx.q(*(Overlay + 8))
1c008a8ff        int32_t r15 = *(Overlay + 0x10)
1c008a903        arg2->IoStatus.Information = r12
1c008a90f        int32_t i = *(Overlay + 0x18)
1c008a928        void* gsbase
1c008a928        
1c008a928        if (NdisGetThreadObjectCompartmentId(*(gsbase + 0x188)) != 1)
1c00e11dc            int32_t rcx_4 = 0
1c00e11df            int32_t* rax_15 = &gFwppContainerAllowedIoctls
1c00e11df            
1c00e11e9            while (*rax_15 != i)
1c00e11ef                rcx_4 += 1
1c00e11f1                rax_15 = &rax_15[1]
1c00e11f1                
1c00e11f7                if (rcx_4 u>= 0xc)
1c00e11f7                    goto label_1c008a9b2
1c00e11f7        
1c008a936        if (i u> 0x12803c)
1c00e1326            if (i u> 0x12806c)
1c00e1494                switch (i)
1c00e148f                    case 0x128070
1c008a9b0                        rbx = WfpVpnUninitNrptTriggers(&arg2->IoStatus.Information, 0)
1c00e149a                    case 0x128074
1c008a9b0                        rbx = WfpVpnResetNrptTrigger(&arg2->IoStatus.Information)
1c00e14a2                    case 0x128078
1c00e1535                        int64_t* var_48 = nullptr
1c00e1543                        int32_t rax_41 = WfpSetConfigureParametersDecodeHelper(MasterIrp, 
1c00e1543                            zx.q(r15), &var_48)
1c00e154f                        rbx = rax_41
1c00e154f                        
1c00e1553                        if (rax_41 s>= 0)
1c00e1442                            rbx = WfpVpnConfigureParameters(&arg2->IoStatus.Information, 
1c00e1442                                *var_48)
1c00e1444                            WfpDecodedBufferFreeHelper(var_48)
1c00e14aa                    case 0x12807c
1c00e14f1                        int64_t* var_50 = nullptr
1c00e14ff                        int32_t rax_40 = WfpSetVpnTriggerSecurityDescriptorDecodeHelper(
1c00e14ff                            MasterIrp, zx.q(r15), &var_50)
1c00e150b                        rbx = rax_40
1c00e150b                        
1c00e150f                        if (rax_40 s>= 0)
1c00e1515                            int64_t* rdx_21 = var_50
1c00e1442                            rbx = WfpSetVpnTriggerSecurityDescriptor(*(*rdx_21 + 8), 
1c00e1442                                rdx_21[1], &arg2->IoStatus.Information)
1c00e1444                            WfpDecodedBufferFreeHelper(var_50)
1c00e14ae                    case 0x128080
1c008a9b0                        rbx = WfpRemoveVpnSecurityDescriptorTrigger(
1c008a9b0                            &arg2->IoStatus.Information)
1c00e14b2                    case 0x128084
1c008a9b0                        rbx = FwppDispatchDevCtl0(zx.q(i), MasterIrp, zx.q(r15), 
1c008a9b0                            &arg2->IoStatus.Information, arg2)
1c00e1494                    default
1c00e14b6                        rbx = -0x3ffffff3
1c00e1326            else if (i == 0x12806c)
1c00e1456                int64_t* var_58 = nullptr
1c00e1464                int32_t rax_33
1c00e1464                int64_t r8_17
1c00e1464                rax_33, r8_17 = WfpNrptTriggerDecodeHelper(MasterIrp, zx.q(r15), &var_58)
1c00e1470                rbx = rax_33
1c00e1470                
1c00e1474                if (rax_33 s>= 0)
1c00e1442                    rbx =
1c00e1442                        WfpVpnInitNrptTriggers(var_58, &arg2->IoStatus.Information, r8_17)
1c00e1444                    WfpDecodedBufferFreeHelper(var_58)
1c00e132c            else if (i == 0x128040)
1c008a9b0                rbx = FwppDispatchDevCtl0(zx.q(i), MasterIrp, zx.q(r15), 
1c008a9b0                    &arg2->IoStatus.Information, arg2)
1c00e133a            else if (i == 0x128058)
1c00e13f4                int64_t* arg_20 = nullptr
1c00e1402                int32_t rax_30
1c00e1402                int64_t r9_11
1c00e1402                rax_30, r9_11 =
1c00e1402                    WfpSetVpnTriggerSidsDecodeHelper(MasterIrp, zx.q(r15), &arg_20)
1c00e140e                rbx = rax_30
1c00e140e                
1c00e1412                if (rax_30 s>= 0)
1c00e1418                    int64_t* rax_31 = arg_20
1c00e1442                    rbx = WfpSetVpnTriggerSids(zx.q(*rax_31), rax_31[1], 
1c00e1442                        &arg2->IoStatus.Information, r9_11)
1c00e1444                    WfpDecodedBufferFreeHelper(arg_20)
1c00e1343            else if (i == 0x12805c)
1c008a9b0                rbx = WfpRemoveVpnAppSidTriggers(&arg2->IoStatus.Information)
1c00e134b            else if (i == 0x128060)
1c00e13aa                int64_t* arg_18 = nullptr
1c00e13b8                int32_t rax_28
1c00e13b8                int64_t r9_10
1c00e13b8                rax_28, r9_10 =
1c00e13b8                    WfpSetVpnTriggerFilePathsDecodeHelper(MasterIrp, zx.q(r15), &arg_18)
1c00e13c4                rbx = rax_28
1c00e13c4                
1c00e13c8                if (rax_28 s>= 0)
1c00e13ce                    int64_t* rax_29 = arg_18
1c00e1442                    rbx = WfpSetVpnTriggerFilePaths(zx.q(*rax_29), rax_29[1], 
1c00e1442                        &arg2->IoStatus.Information, r9_10)
1c00e1444                    WfpDecodedBufferFreeHelper(arg_18)
1c00e1353            else if (i == 0x128064)
1c008a9b0                rbx = WfpRemoveVpnFilePathTriggers(&arg2->IoStatus.Information)
1c00e1357            else if (i != 0x128068)
1c00e14b6                rbx = -0x3ffffff3
1c00e135b            else
1c00e1361                int64_t* arg_10 = nullptr
1c00e136f                int32_t rax_25 =
1c00e136f                    WfpSetDisconnectDecodeHelper(MasterIrp, zx.q(r15), &arg_10)
1c00e137b                rbx = rax_25
1c00e137b                
1c00e137f                if (rax_25 s>= 0)
1c00e1442                    rbx = WfpVpnSetStateDisconnected(&arg2->IoStatus.Information, *arg_10)
1c00e1444                    WfpDecodedBufferFreeHelper(arg_10)
1c008a936        else if (i == 0x12803c)
1c00e12fa            if (FwppBfeStateGetResetCount0() != 1)
1c008a9b0                rbx = IoctlKfdResetState(MasterIrp, zx.q(r12.d), 
1c008a9b0                    &arg2->IoStatus.Information, arg2)
1c008a93c        else if (i u> 0x12801c)
1c008a9ee            if (i == 0x128020)
1c008a9b0                rbx = FwppDispatchDevCtl0(zx.q(i), MasterIrp, zx.q(r15), 
1c008a9b0                    &arg2->IoStatus.Information, arg2)
1c008a9ee            else if (i == 0x128028)
1c008a9b0                rbx = IoctlKfdAddCache(MasterIrp, zx.q(r15), &arg2->IoStatus.Information, 
1c008a9b0                    arg2)
1c008a9f7            else if (i == 0x12802c)
1c008a9b0                rbx = IoctlKfdDeleteCache(MasterIrp, zx.q(r15), 
1c008a9b0                    &arg2->IoStatus.Information, arg2)
1c008a9ff            else if (i == 0x128030)
1c008a9b0                rbx = IoctlKfdCommitTransaction(MasterIrp, zx.q(r12.d), 
1c008a9b0                    &arg2->IoStatus.Information, arg2)
1c008aa07            else if (i == 0x128034)
1c008a9b0                rbx = IoctlKfdAbortTransaction(MasterIrp, zx.q(r15), 
1c008a9b0                    &arg2->IoStatus.Information, arg2)
1c00e1257            else if (i != 0x128038)
1c00e14b6                rbx = -0x3ffffff3
1c00e125b            else
1c00e126d                int32_t rax_17 = IoctlKfdQueryLayerStatistics(MasterIrp, zx.q(r12.d), 
1c00e126d                    &arg2->IoStatus.Information, arg2)
1c00e1279                rbx = rax_17
1c00e1279                
1c00e127d                if (rax_17 s>= 0)
1c008a9b0                    rbx = WfpAleQueryStatistics(MasterIrp)
1c008a94a        else if (i == 0x12801c || i == 0x124044)
1c008a9b0            rbx = FwppDispatchDevCtl0(zx.q(i), MasterIrp, zx.q(r15), 
1c008a9b0                &arg2->IoStatus.Information, arg2)
1c008a95e        else if (i == 0x124048 || i == 0x12404c || i == 0x124050)
1c008a9b0            rbx = IoctlKfdSetBfeEngineSd(zx.q(i), MasterIrp, zx.q(r15))
1c008a976        else if (i == 0x128004)
1c008a9b0            rbx = IoctlKfdAddIndex(MasterIrp, zx.q(r15), &arg2->IoStatus.Information, arg2)
1c008a981        else if (i == 0x128008)
1c008a9b0            rbx = IoctlKfdDeleteIndex(MasterIrp, zx.q(r15), &arg2->IoStatus.Information, 
1c008a9b0                arg2)
1c008a989        else if (i != 0x128018)
1c00e14b6            rbx = -0x3ffffff3
1c008a992        else
1c008a9b0            rbx = IoctlKfdBatchUpdate(MasterIrp, zx.q(r15), &arg2->IoStatus.Information, 
1c008a9b0                arg2)
1c008a9b0        
1c008a9b2    label_1c008a9b2:
1c008a9b2        arg2->IoStatus..Status = rbx
1c008a9b2        
1c008a9b8        if (rbx s< 0)
1c008aa27            arg2->IoStatus.Information = 0
1c008aa27        
1c008a9bf        IofCompleteRequest(Irp: arg2, PriorityBoost: 0)
1c008a9e4        return zx.q(rbx)



```

### IOCTL

```c
// KFD Index Management
#define IOCTL_KFD_ADD_INDEX                        0x128004
#define IOCTL_KFD_DELETE_INDEX                     0x128008
#define IOCTL_KFD_BATCH_UPDATE                     0x128018
#define IOCTL_KFD_UNKNOWN_1C                       0x12801C  // Handled by FwppDispatchDevCtl0

// KFD Transaction and State Management
#define IOCTL_KFD_UNKNOWN_20                       0x128020  // Handled by FwppDispatchDevCtl0
#define IOCTL_KFD_ADD_CACHE                        0x128028
#define IOCTL_KFD_DELETE_CACHE                     0x12802C
#define IOCTL_KFD_COMMIT_TRANSACTION               0x128030
#define IOCTL_KFD_ABORT_TRANSACTION                0x128034
#define IOCTL_KFD_QUERY_LAYER_STATISTICS           0x128038
#define IOCTL_KFD_RESET_STATE                      0x12803C
#define IOCTL_KFD_UNKNOWN_40                       0x128040  // Handled by FwppDispatchDevCtl0

// VPN Trigger Management - Application SIDs
#define IOCTL_WFP_SET_VPN_TRIGGER_SIDS             0x128058
#define IOCTL_WFP_REMOVE_VPN_APP_SID_TRIGGERS      0x12805C

// VPN Trigger Management - File Paths
#define IOCTL_WFP_SET_VPN_TRIGGER_FILE_PATHS       0x128060
#define IOCTL_WFP_REMOVE_VPN_FILE_PATH_TRIGGERS    0x128064

// VPN Trigger Management - State Control
#define IOCTL_WFP_VPN_SET_STATE_DISCONNECTED       0x128068
#define IOCTL_WFP_VPN_INIT_NRPT_TRIGGERS           0x12806C
#define IOCTL_WFP_VPN_UNINIT_NRPT_TRIGGERS         0x128070
#define IOCTL_WFP_VPN_RESET_NRPT_TRIGGER           0x128074

// VPN Configuration and Security
#define IOCTL_WFP_VPN_CONFIGURE_PARAMETERS         0x128078
#define IOCTL_WFP_SET_VPN_TRIGGER_SECURITY_DESC    0x12807C
#define IOCTL_WFP_REMOVE_VPN_SECURITY_DESC_TRIGGER 0x128080
#define IOCTL_WFP_UNKNOWN_84                       0x128084  // Handled by FwppDispatchDevCtl0

// Query Operations
#define IOCTL_KFD_UNKNOWN_QUERY_44                 0x124044  // Handled by FwppDispatchDevCtl0
#define IOCTL_KFD_SET_BFE_ENGINE_SD_48             0x124048  // BFE = Base Filtering Engine
#define IOCTL_KFD_SET_BFE_ENGINE_SD_4C             0x12404C
#define IOCTL_KFD_SET_BFE_ENGINE_SD_50             0x124050

```

## SRV2.sys

### Device

```c
\\Device\\Srv2\\
```

### Dispatch Method (Smb2ProviderDispatchFsctl)

```c
1c0059624    NTSTATUS Smb2ProviderDispatchFsctl(void* arg1, int64_t arg2, int64_t* arg3, int32_t arg4, int64_t* arg5, 
1c0059624      int32_t arg6, int32_t arg7, void* arg8, IRP* arg9)

1c0059647        int64_t* r14 = arg5
1c005964b        int64_t* rdi = arg3
1c005964e        uint64_t rsi = zx.q(arg4)
1c005964e        
1c0059653        if (arg7 u> 0x144190)
1c00596f7            NTSTATUS rax_5
1c00596f7            
1c00596f7            switch (arg7)
1c00596f2                case 0x148074
1c006eeaf                    if (rsi.d u< 2)
1c0059782                        return STATUS_INVALID_PARAMETER
1c0059782                    
1c006eeb9                    int16_t* rax_34
1c006eeb9                    int64_t rdx_13
1c006eeb9                    rax_34, rdx_13 = Smb2FindDialectRecord(*arg3)
1c006eeb9                    
1c006eec1                    if (rax_34 == 0)
1c0059782                        return STATUS_INVALID_PARAMETER
1c0059782                    
1c006eec7                    uint64_t Smb2MaxClusterDialect_1 = zx.q(Smb2MaxClusterDialect)
1c006eece                    int16_t r9_3 = *rax_34
1c006eece                    
1c006eed6                    if (r9_3 != Smb2MaxClusterDialect_1.w)
1c006eedc                        bool cond:2_1 = (data_1c003caa1 & 4) == 0
1c006eee3                        Smb2MaxClusterDialect = r9_3
1c006eee3                        
1c006eeeb                        if (not(cond:2_1))
1c006eef1                            int32_t var_68
1c006eef1                            var_68.w = Smb2MaxClusterDialect_1.w
1c006eef6                            McTemplateK0hh_EtwWriteTransfer(Smb2MaxClusterDialect_1, 
1c006eef6                                rdx_13, &Smb2Dialects, r9_3)
1c006eef6                    
1c0059684                    return STATUS_SUCCESS
1c0059703                case 0x14807c
1c0059717                    int128_t var_30_1 = zx.o(0)
1c0059717                    
1c005971c                    if (Srv2IsRunning == 0)
1c006ee21                        return 0xc0980005
1c006ee21                    
1c0059722                    var_30_1.q = 0
1c0059722                    
1c005972f                    if (rsi.d u< 0x20 || arg6 u< 0x10)
1c0059782                        return STATUS_INVALID_PARAMETER
1c0059782                    
1c0059734                    uint128_t var_48
1c0059734                    uint128_t zmm1
1c0059734                    
1c0059734                    if (rsi.d == 0x28)
1c006ee8f                        zmm1 = *(arg3 + 0x10)
1c006ee94                        var_48 = *arg3
1c006ee9e                        var_30_1:8.q = arg3[4]
1c006eea3                        uint128_t var_38_1 = zmm1
1c0059734                    else
1c005973d                        if (rsi.d != 0x20)
1c0059782                            return STATUS_INVALID_PARAMETER
1c0059782                        
1c0059743                        int32_t rax_11 = arg3[3].d
1c0059747                        zmm1 = zx.o(arg3[2])
1c005974d                        var_48 = *arg3
1c0059751                        var_30_1.d = rax_11
1c0059754                        int128_t var_38
1c0059754                        var_38.q = zmm1.q
1c0059759                        var_30_1:4.q = 0
1c0059764                    rax_5 = Smb2ProcessHandleDuplicateRequest(&var_48, rdi)
1c0059764                    
1c005976b                    if (rax_5 s>= STATUS_SUCCESS)
1c0059775                        *(arg8 + 8) = 0x10
1c0059775                    
1c005969f                    return rax_5
1c006ed31                case 0x148194
1c006ee80                    return Smb2RefreshInterfaceProperty(rdi, rsi.d)
1c006ed3c                case 0x148198
1c006ee61                    rax_5 = Smb2SetSharesForSlowIO(arg1, rdi, rsi.d, r14, arg6)
1c006ee6a                    int64_t rbx_2 = 0
1c006ee6a                    
1c006ee6e                    if (rax_5 s>= STATUS_SUCCESS)
1c006ee6e                        rbx_2 = 8
1c006ee6e                    
1c006ee72                    *(arg8 + 8) = rbx_2
1c005969f                    return rax_5
1c006ed45                case 0x14819c
1c006ee46                    Smb2ClearSharesForSlowIO(arg1, rdi, rsi.d)
1c0059684                    return STATUS_SUCCESS
1c006ed4e                case 0x1481a4
1c006ee33                    if (Srv2IsRunning != 0)
1c006ee35                        return Smb2RefreshSrvCredHandle()
1c006ee35                    
1c006ee21                    return 0xc0980005
1c006ee21            
1c006ed5a            NTSTATUS rax_24
1c006ed5a            void* rsi_2
1c006ed5a            int32_t r10
1c006ed5a            
1c006ed5a            if (arg7 == 0x1481c4)
1c006edf6                wil_details_FeatureReporting_ReportUsageToService(
1c006edf6                    &Feature_SMBSynchronousRedirection__private_reporting, 0xa97433, 0, 0, 
1c006edf6                    r10, &Feature_SMBSynchronousRedirection_logged_traits, 1)
1c006edf6                
1c006ee03                if (Srv2IsRunning == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c006ee08                if (rsi.d u< 0x10)
1c0059782                    return STATUS_INVALID_PARAMETER
1c0059782                
1c006ee10                void* rax_32 = Srv2GetInstance(0)
1c006ee15                rsi_2 = rax_32
1c006ee15                
1c006ee1b                if (rax_32 == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c006ecfb                rax_24 = Smb2StartCsvVolumeDrain(*(rax_32 + 0xa0), rdi)
1c006ed5a            else
1c006ed5f                if (arg7 != 0x1481c8)
1c006edc6                    return STATUS_NOT_SUPPORTED
1c006edc6                
1c006ed87                wil_details_FeatureReporting_ReportUsageToService(
1c006ed87                    &Feature_SMBSynchronousRedirection__private_reporting, 0xa97433, 0, 0, 
1c006ed87                    r10, &Feature_SMBSynchronousRedirection_logged_traits, 1)
1c006ed87                
1c006ed94                if (Srv2IsRunning == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c006ed9d                if (rsi.d u< 0x10)
1c0059782                    return STATUS_INVALID_PARAMETER
1c0059782                
1c006eda5                void* rax_31 = Srv2GetInstance(0)
1c006edaa                rsi_2 = rax_31
1c006edaa                
1c006edb0                if (rax_31 == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c006edbc                rax_24 = Smb2CompleteCsvVolumeDrain(*(rax_31 + 0xa0), rdi)
1c006ed05            Srv2DereferenceInstance(rsi_2)
1c006ed0a            return rax_24
1c006ed0a        
1c0059659        switch (arg7)
1c005965f            case 0x1401bc
1c00596a9                if (Srv2IsRunning == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c00596b2                if (rsi.d u< 4)
1c0059782                    return STATUS_INVALID_PARAMETER
1c0059782                
1c00596bb                Smb2ServerStage = *arg3
1c0059684                return STATUS_SUCCESS
1c0059666            case 0x1401c0
1c00596cb                if (Srv2IsRunning == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c00596d5                if (arg6 != 4)
1c0059782                    return STATUS_INVALID_PARAMETER
1c0059782                
1c00596e1                *r14 = Smb2ServerStage
1c00596e8                *(arg8 + 8) = 4
1c0059684                return STATUS_SUCCESS
1c005966b            case 0x144038
1c006ec6a                NTSTATUS rbx_1 = STATUS_SUCCESS
1c006ec6c                bool cond:3_1 = Srv2IsRunning == 0
1c006ec72                int64_t arg_48 = 0
1c006ec72                
1c006ec76                if (cond:3_1)
1c006ee21                    return 0xc0980005
1c006ee21                
1c006ec7e                void* rax_22
1c006ec7e                int64_t r8_2
1c006ec7e                rax_22, r8_2 = Srv2GetInstance(0)
1c006ec7e                
1c006ec89                if (rax_22 == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c006ec9c                if (rsi.d u< 8 || arg6 u< 0x10)
1c006ece7                    rbx_1 = STATUS_BUFFER_TOO_SMALL
1c006ec9c                else
1c006ecb4                    void* rax_23 = RfsTable64LookupAndEnumerate(**(rax_22 + 0xa0), 
1c006ecb4                        &arg_48, r8_2, arg9->AssociatedIrp.MasterIrp)
1c006ecb4                    
1c006ecbc                    if (rax_23 == 0)
1c006ece0                        rbx_1 = STATUS_NO_TOKEN
1c006ecbc                    else
1c006ecc9                        *r14 = *(rax_23 + 0xa8)
1c006ecce                        *(arg8 + 8) = 0x10
1c006ecd9                        Smb2DereferenceSession(rax_23)
1c006ecd9                
1c006ed05                Srv2DereferenceInstance(rax_22)
1c006ed0a                return rbx_1
1c0059676            case 0x144047
1c005967f                Smb2RefreshRegistryValue()
1c0059684                return STATUS_SUCCESS
1c006eab2            case 0x14405c
1c006ec40                if (rsi.d u< 4)
1c0059782                    return STATUS_INVALID_PARAMETER
1c0059782                
1c006ec4c                Smb2ReauthPolicy = *arg3
1c006ec58                data_1c00470be = *(arg3 + 2)
1c006ec5f                Smb2ReauthModulo = 0
1c0059684                return STATUS_SUCCESS
1c006eabb            case 0x14406c
1c006ec32                return Smb2RefreshNetnameTable(rdi, rsi.d, arg9)
1c006eac4            case 0x1440a0
1c006ebdf                if (Srv2IsRunning == 0)
1c006ee21                    return 0xc0980005
1c006ee21                
1c006ebf8                if (rsi.d != 0xb18 || arg6 != 0xb18)
1c0059782                    return STATUS_INVALID_PARAMETER
1c0059782                
1c006ec01                NTSTATUS rax_19 = Smb2InvalidateSessions(rdi)
1c006ec01                
1c006ec0a                if (rax_19 s>= STATUS_SUCCESS)
1c006ec15                    memcpy(r14, rdi, 0xb18)
1c006ec1e                    *(arg8 + 8) = 0xb18
1c006ec1e                
1c006ec22                return rax_19
1c0059651            case 0x144190
1c006ed26                return Smb2EnumerateServerInterfaces(rdi, rsi.d, r14, arg6, arg8)
1c006ed26        
1c006ead0        if (arg7 != 0x1440a4)
1c006ead5            if (arg7 != 0x1440a8)
1c006edc6                return STATUS_NOT_SUPPORTED
1c006edc6            
1c006eae3            if (Srv2IsRunning == 0)
1c006ee21                return 0xc0980005
1c006ee21            
1c006eaf5            if (rsi.d u>= 0x38 && arg6 == rsi.d)
1c006eb00                NTSTATUS rax_16 = Smb2GetAppInstances(rdi, rsi.d)
1c006eb00                
1c006eb0a                if (rax_16 s>= STATUS_SUCCESS)
1c006eb15                    memcpy(r14, rdi, rsi)
1c006eb21                    *(arg8 + 8) = zx.q(arg6)
1c006eb21                
1c006eb25                return rax_16
1c006eb25            
1c0059782            return STATUS_INVALID_PARAMETER
1c0059782        
1c006eb35        if (Srv2IsRunning == 0)
1c006ee21            return 0xc0980005
1c006ee21        
1c006eb4e        if (rsi.d != 0x294 || arg6 != 0x294)
1c0059782            return STATUS_INVALID_PARAMETER
1c0059782        
1c006eb57        Smb2InvalidateFiles(rdi)
1c006eb62        int64_t i_1 = 5
1c006ebb8        int64_t i
1c006ebb8        
1c006ebb8        do
1c006eb6b            *r14 = *rdi
1c006eb73            *(r14 + 0x10) = *(rdi + 0x10)
1c006eb7c            *(r14 + 0x20) = *(rdi + 0x20)
1c006eb85            *(r14 + 0x30) = *(rdi + 0x30)
1c006eb8e            *(r14 + 0x40) = *(rdi + 0x40)
1c006eb97            *(r14 + 0x50) = *(rdi + 0x50)
1c006eba0            *(r14 + 0x60) = *(rdi + 0x60)
1c006eba5            r14 = &r14[0x10]
1c006eba8            int128_t zmm1_1 = *(rdi + 0x70)
1c006ebac            rdi = &rdi[0x10]
1c006ebaf            *(r14 - 0x10) = zmm1_1
1c006ebb4            i = i_1
1c006ebb4            i_1 -= 1
1c006ebb8        while (i != 1)
1c006ebbd        *r14 = *rdi
1c006ebc4        r14[2].d = rdi[2].d
1c006ebcc        *(arg8 + 8) = 0x294
1c006ebd0        return STATUS_SUCCESS

```

### IOCTL

```c
// SMB2 Server Configuration
#define FSCTL_SMB2_SET_SERVER_STAGE                0x1401BC
#define FSCTL_SMB2_GET_SERVER_STAGE                0x1401C0

// SMB2 Session and Connection Management
#define FSCTL_SMB2_ENUMERATE_SESSIONS              0x144038
#define FSCTL_SMB2_REFRESH_REGISTRY_VALUE          0x144047
#define FSCTL_SMB2_SET_REAUTH_POLICY               0x14405C
#define FSCTL_SMB2_REFRESH_NETNAME_TABLE           0x14406C
#define FSCTL_SMB2_INVALIDATE_SESSIONS             0x1440A0
#define FSCTL_SMB2_INVALIDATE_FILES                0x1440A4
#define FSCTL_SMB2_GET_APP_INSTANCES               0x1440A8
#define FSCTL_SMB2_ENUMERATE_SERVER_INTERFACES     0x144190

// SMB2 Dialect and Protocol Control
#define FSCTL_SMB2_SET_MAX_CLUSTER_DIALECT         0x148074
#define FSCTL_SMB2_PROCESS_HANDLE_DUPLICATE        0x14807C

// SMB2 Network Interface Management
#define FSCTL_SMB2_REFRESH_INTERFACE_PROPERTY      0x148194
#define FSCTL_SMB2_SET_SHARES_FOR_SLOW_IO          0x148198
#define FSCTL_SMB2_CLEAR_SHARES_FOR_SLOW_IO        0x14819C
#define FSCTL_SMB2_REFRESH_SRV_CRED_HANDLE         0x1481A4

// CSV (Cluster Shared Volumes) Management
#define FSCTL_SMB2_START_CSV_VOLUME_DRAIN          0x1481C4
#define FSCTL_SMB2_COMPLETE_CSV_VOLUME_DRAIN       0x1481C8

```

## http.sys

### Devices

```c
\Device\Http\Server       - Server-side operations (IIS, HTTP listeners)
\Device\Http\ReqQueue     - Request queue handles
\Device\Http\Control      - Control/configuration operations

```

### Dispatch Method (?)

```c
//Unknown

```

### IOCTL (extracted from httpapi.dll)

```c
#define IOCTL_HTTP_DELETE_SERVICE_CONFIGURATION      0x128085
#define IOCTL_HTTP_QUERY_SERVICE_CONFIGURATION       0x12407A
#define IOCTL_HTTP_SET_SERVICE_CONFIGURATION         0x12887D
#define IOCTL_HTTP_DELETE_SERVICE_CONFIGURATION_2    0x128891
#define IOCTL_HTTP_CONTROL_SERVICE                   0x12408A

// URL Group Management
#define IOCTL_HTTP_REMOVE_URL_FROM_URL_GROUP         0x128024
#define IOCTL_HTTP_ADD_URL_TO_URL_GROUP              0x128020
#define IOCTL_HTTP_GET_URL_GROUP_FOR_REQUEST_QUEUE   0x124023  
#define IOCTL_HTTP_SET_URL_GROUP_PROPERTY            0x12801D
#define IOCTL_HTTP_QUERY_URL_GROUP_PROPERTY          0x12402A
#define IOCTL_HTTP_CREATE_URL_GROUP                  0x128010
#define IOCTL_HTTP_CLOSE_URL_GROUP                   0x128014

// Request Queue
#define IOCTL_HTTP_FLUSH_RESPONSE_CACHE              0x124004
#define IOCTL_HTTP_SET_REQUEST_QUEUE_PROPERTY        0x128001 

// HTTP Counters
#define IOCTL_HTTP_GET_COUNTERS                      0x124056

// Request Operations
#define IOCTL_HTTP_RECEIVE_HTTP_REQUEST              0x124036
#define IOCTL_HTTP_SEND_HTTP_RESPONSE                0x12403F
#define IOCTL_HTTP_WAIT_FOR_DISCONNECT               0x124048
#define IOCTL_HTTP_RECEIVE_REQUEST_ENTITY_BODY       0x12403A
#define IOCTL_HTTP_CLOSE_SERVER_SESSION              0x128004
#define IOCTL_HTTP_CREATE_SERVER_SESSION             0x128000
#define IOCTL_HTTP_QUERY_SERVER_SESSION_PROPERTY     0x12400A
#define IOCTL_HTTP_SET_SERVER_SESSION_PROPERTY       0x12800D

// Client Operations
#define IOCTL_HTTP_CANCEL_CLIENT_REQUEST             0x124098
#define IOCTL_HTTP_CLOSE_CLIENT_REQUEST              0x12409C
#define IOCTL_HTTP_CREATE_CLIENT_REQUEST             0x12408C
#define IOCTL_HTTP_DECLARE_PUSH                      0x124070
#define IOCTL_HTTP_DELEGATE_REQUEST                  0x124074
#define IOCTL_HTTP_DELEGATE_REQUEST_EX               0x1240AC
#define IOCTL_HTTP_GET_COMPLETE_REQUEST              0x124068
#define IOCTL_HTTP_NOTIFY_CLIENT_RESPONSE_HEADERS    0x124096
#define IOCTL_HTTP_QUERY_CLIENT_RESPONSE_HEADERS     0x1240A2
#define IOCTL_HTTP_QUERY_REQUEST_PROPERTY            0x12406C  
#define IOCTL_HTTP_RECEIVE_CLIENT_CERTIFICATE        0x12404A  
#define IOCTL_HTTP_RECEIVE_CLIENT_REQUEST_ENTITY_BODY 0x1240E6 
#define IOCTL_HTTP_SEND_CLIENT_REQUEST               0x124093
#define IOCTL_HTTP_SEND_RESPONSE_ENTITY_BODY         0x124043
#define IOCTL_HTTP_WAIT_FOR_DISCONNECT_EX            0x12484C  
#define IOCTL_HTTP_ADD_FRAGMENT_TO_CACHE             0x12404E 
#define IOCTL_HTTP_READ_FRAGMENT_FROM_CACHE          0x12405F
#define IOCTL_HTTP_QUERY_REQUEST_QUEUE_PROPERTY      0x124002
#define IOCTL_HTTP_SHUTDOWN_REQUEST_QUEUE            0x124030
#define IOCTL_HTTP_WAIT_FOR_DEMAND_START             0x128048

```

## 

