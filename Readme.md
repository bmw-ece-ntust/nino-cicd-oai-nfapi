# Integration: OAI nFAPI

> 
:::warning
> If you didn't see any images on this page, open the following url on a new tab and proceed with the certificate <https://minio.bmw.lab/minio/health/live>
>
> :::

## Progress Tracker

| Phase | Log | Remarks |
|----|----|----|
| Build Cycle | - [x] Source Code 
- [x] Build Pipeline
  * OAI `master`
  * OAI `nFAPI`
- [x] Store generated images on registry
  * OAI `master`
  * OAI `nFAPI` | *  |
| Testing Phase | - [x] Build test
- [ ] Compiled binary testing | * Determine weather the NIC  |
| Integration Phase | - [x] Integrate OAI `nFAPI` VNF with OAI `nFAPI` PNF on k8s.
- [ ] \[ ! \] |    |

## Introduction


 ![Richard's & Ming Integration Goal](attachments/0838f8e5-9b02-4e7f-ad1f-b39c570249af.png " =720x304")

* OSC's phy_stub is bad, need to find a way to utilize OAI's L1, rfSIM, and UE Sim.
* *Integrate OSC-L2 High with OAI-L1 Low* 


\
 ![Functional Mapping](attachments/b9c7de3d-6c64-4e8d-9375-801b88409a88.png " =851x1158")

 ![UE Call Flow](attachments/72556dbc-4c1c-4a38-8745-1d2413496298.png " =582x590") ![](attachments/53b69e45-4a05-40bc-98d8-692b23c10e78.png " =627x502")


## Integration Process

### Integration Design

| Node | Interface | IP | CNI Driver |
|----|----|----|----|
| AMF | `N2` | 172.2.2.254 | `Whereabouts` |
| UPF | `N3` | 172.2.2.253 | `Multus` |
| OAI-CU | `N2` | - | `Multus` |
| OAI-VNF | `N2` | 172.2.2.10 | `Whereabouts` |
| OAI-VNF | `N3` | 172.2.2.20 | `Whereabouts` |
| OAI-PNF | `F1` | - |    |


**UE Data**

> Make sure the UE is registered on the UDR



| Param | Value |
|----|----|
| IMSI | `001010000000100` |
| KI | `fec86ba6eb707ed08905757b1bb44b8f` |
| OPC | `C42449363BBAD02B66D16BC975D77CC1` |
| DNN | `oai` |
| SST | `1` |
| SD | `0xFFFFFF ` |


### Integration Tracker

| CU | DU-L2 | DU-L1 | rfSim | UE | Remarks |
|----|----|----|----|----|----|
| OAI-CU | OAI-DU-L2 | OAI-DU-L1 | rfSim | OAI UE | *  **==OnGoing ==**
* OAI Master branch support for nFAPI is not complete
* Need to use Richard & Ming's branch
  * Build automation process success |
|    |    |    |    |    |    |


### Deployment Config


**VNF**

 ![](attachments/d017748b-4a5a-4f7f-a1a8-be2c30b889f2.png)

 ![](attachments/b97325db-9352-4554-82a2-f5d169420ea9.png)

**PNF**

 ![](attachments/d8fbc3b8-66f5-47dd-b998-3cf1acf1b2fe.png)

**OAI L1 Command Override** 


```javascript
cd ran_build/build
sudo ./nr-softmodem -O ../../../../OSC_L2/mwnl/oai_pnf_conf/oaiL1.nfapi.usrpb210.conf --sa -E --gNBs.[0].min_rxtxtime 6 --continuous-tx --nfapi 1
```


**OAI CU Command Override**

```javascript
/opt/oai-gnb/bin/nr-softmodem -O /tmp/cu.conf --sa --log_config.global_log_options level nocolor time
```


**OAI-CU (2023.w43) - OSC-DU (master)**


**Failed to decode PDU**

 ![Two Main Error](attachments/1289ccd1-1215-40aa-a780-a025f639db38.png " =762x96")

**OAI-CU (Tony) - OSC-DU  (master)**

 ![OSC-DU - OAI-CU Tony](attachments/aca3e0ba-aeda-4690-b6a2-55ca580aec09.png " =762x122")

### OAI E2E nFAPI


> `OAI-CU` → `OAI-DU-L2` → `OAI-DU-L1` → `OAI-rfSIM`→ `OAI-nrUE`


 ![](attachments/61431837-7925-4c7b-bbba-99ba69f06fbc.png " =762x355")


1. Gather the necessary conf files for nFAPI Mode
   - [x] VNF
   - [x] PNF
2. Crete Helm chart 
   - [x] VNF
   - [x] PNF


 ![](attachments/c3b58190-46fa-4f4d-bde8-7b3aa7bb1156.png " =842x157")

 ![](attachments/d8209e0b-94c3-4ae0-895f-558f9fa2c6c1.png)

 ![](attachments/bf66aae9-0d38-4430-91bf-827727b26dfa.png)

 ![gNB IP Address?](attachments/10e02f08-1944-4575-aa86-24645558fa5c.png)

| Param | Default | Updated |
|----|----|----|
| absoluteFrequencySSB | 641032 | *641280* |
| dl_absoluteFrequencyPointA | 640000 | *640008* |


### **ToDo** 


### Tony's version of OAI-CU & OSC-DU-L2

Tony's approach is to change OAI-CU's F1AP codec format according to OSC-CU. Need to reconsider this approach in the future as this approach will need always need a custom OAI-CU patched version to integrate with OSC-DU-L2



1. Git pull ***latest*** OAI-CU
2. Apply tony's patch on ***latest*** OAI-CU

   
   1. Find a way to compare tony's change on OAI-CU with the upstream code.
   2. `Tested` : Incompatible, many missing files and struct. 

   \

### Automate Testing Cycle

- [x] Gather ready to use codes from scattered repos
- [x] Automate build process relevant code base using docker
- [x] Host builds with different tags

  \
  ![](attachments/1bb5b73a-108d-4d9e-a5ad-c4cf9ba8be54.png " =582x382")
- [ ] Deploy builds on kubernetes 


## Create Build Pipeline nFAPI OAI 


 ![](attachments/ebc356b3-492d-49d4-860d-8a8243c971d1.png)

- [x] Create pipeline for build process

  
  1. <https://github.com/motangpuar/bmw-jenkins-build-patches>
  2. <https://jenkins.bmw.lab/view/nFAPI/job/OAI-nfapi-fixes-rebased-demo-pipeline/>
- [x] Automate build

  
  1. Build pipeline success

     ![Automated build pipeline for OAI nFAPI on Kubernetes](attachments/66153477-50c7-4ca3-b799-9f07e4c52e38.png)
  2. Ready to use custom OAI images

     ![](attachments/3d37678f-2763-48cd-a046-a525afb66d87.png)

     \
- [x] patch build issues

  
  1. Failed build process without correct flags
  2. Wrong dependencies on build docker container


- [x] Missing libraries \[ **SOLVED \]**

  ![](attachments/695ce3d6-213f-4248-985a-4a565636ca2e.png)


- [x] Integration OAI-VNF & OAI-PNF

  
  1. SCTP connection issue \[ **August 5, 2024** \] 

     ![](attachments/95824d8a-0199-4d38-85db-ad274c741702.png)


\
## Automatic Integration & Deployment ( Post Development)


### CI: OAI-nFAPI



:::info
CI : <https://jenkins.bmw.lab/view/nFAPI/>

:::


 ![Build pipeline for OAI-nFAPI](attachments/db19b187-48b2-4b68-93eb-a0d677d09815.png)

### ArgoCD : VNF, PNF+rfSIM & nr-UE



:::info
CD Repo: <https://github.com/motangpuar/bmw-cicd-manifests>

:::


 ![CD Instances of OAI-nFAPI VNF, PNF+rfSIM and UE-sim](attachments/e1ee9569-ac46-41fa-b8d8-eebb543572e9.png)


1. AMF

   ![](attachments/9b8e5a2c-2fa2-48dd-96e5-3468f6246053.png)
2. VNF

   ![](attachments/12cfd5c3-2561-410d-99f6-97d7df8aad75.png)
3. PNF+rfSIM

   ![Socket connection](attachments/9658f7f8-c39e-4690-a99b-bc23723b8631.png)![Timeframe out of order](attachments/d4fea532-5221-4ec4-aa31-ccb88345a97d.png)![Error condition triggered because last Timestamp is bigger than timestamp+number of samples](attachments/d2215113-cdcf-4315-9c7c-b4912087fc59.png)
4. **nr-UE** ( Failed to sync timeframe and decode Mib from rfSIM )

   \
   ![Issue](attachments/ee089c94-16bb-4d0c-908b-712f16de5d5b.png)

   >  **==FIXED== :** Wrong config file and execution mode
   > * *PNF Exec Command*
   >
   >   ![](attachments/13a71d73-0d47-4faa-b34f-3a4a9374955c.png)
   > * *VNF Config*
   >
   >   ![](attachments/45c3c9c8-23bc-444a-a723-b4978bd07433.png)
   > * UE 
   >
   >   ![](attachments/79bdf32d-0d6b-40bf-9881-815c2b5e1562.png)

   \
   \
5. SMF : PDU Request detected

   \
   ![SMF ](attachments/19775248-55d2-4310-b783-1d77924bac01.png)

   > *Make sure the VNF n3 interface address are on the same subnet with the UPF n3* 

   \
   ![UE Attach Request](attachments/80421217-2866-4d32-bf9b-db8efaed70f7.png)

 ![GTP traffic UE-UPF](attachments/ae02742a-f494-4479-ab09-d7c8bd4f1888.png)

 ![](attachments/25391bf0-0b97-4b05-81a8-a7ce00382164.png)

> **Attach PCAP**
>
> [nFAPI-K8S.pcapng 3601928](attachments/f989bf0d-e43f-418f-91f8-fa9e439c231e.false)

> \


\
## References

* [Integration Manual](https://hackmd.io/kJDv7rgISu-VrbkH-ShXTw)


* Functional Split

  [HUBER-SUHNER-Cube-Optics-Functional-Split-Rev03.pdf 204406](attachments/ccc67837-6080-43df-852f-b0dd82076036.pdf)

* *==Richard & Ming's Work==*

  [https://hackmdming.io/@Mi50010ngHung/StudyBook/%2F%40MingHung%2FOAI_DU](https://hackmd.io/@MingHung/StudyBook/%2F%40MingHung%2FOAI_DU)
* …


\