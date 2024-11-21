# Quecctel BC95-CNV Message Authentication Bypass

## Overview
- **Description:** Quectel BC95-CNV with firmware version V100R001C00SPC051 allows to bypass message security authentication and inject attack message without the security key via software-defined radio (USRP B210).
- **Affected component:** Quectel BC95-CNV is a high-performance and low-power NB-IoT wireless communication module. According to the 3GPP specification definition, the device must perform message integrity and security protection after security authentication. The BC95-CNV chipset has a vulnerability in the baseband logic implementation, which receives messages with NAS message security header type “Integrity protected and ciphered with new EPS security context"  and the NULL message integrity authentication code (mac=0x0). Attackers use software radio tools (e.g. USRP B210) to bypass message security authentication and arbitrarily manipulate message content.
- **Attack vector:** sec_hdr=3 & mac = 0x00000000 &  Downlink NAS Transport

## Attack Settings
- **Hardware environment:** deployed in the Dell OptiPlex 5000 configured with 32GB RAM and Intel i9-12900 processor running Arch system, as well as connecting a USRP B210.
- **NB-IoT network:** upgraded [srsRAN 4G](https://github.com/srsran/srsRAN_4G) and [Sonica](https://github.com/sonica-nbiot/sonica) open source project to implement the EPC and eNodeB components of the NB-IOT network (As shown in the Figure) and used the USRP B210 as the software radio. The simulated network is configured with the PLMN ID of 99970 and specific frequency .
![](assets/Pasted%20image%2020241121164818.png)
- **NB-IoT device** ( As shown in the Figure): equipped with a programmable SIM card (sysmo ISIM-SJA2) and connected to the simulated network. It is connected to the test device through the Virtual COM port and uses the AT command and diagnostic message monitoring tool (QCOM) to obtain system information about the UE for monitoring and analysis. The firmware version information is shown in Figure.
![](assets/Pasted%20image%2020241121204103.png)
![](assets/Pasted%20image%2020241121204108.png)

## Attack Process
1. In the legal device registration process, the *Downlink NAS Transport* message security header type is “*Integrity protected and ciphered*”. The message is integrity and security protection. In the attack process, the attacker sets the *Downlink NAS Transport* attack message security header type to “*Integrity protected and ciphered with new EPS security context*”, and the message integrity authentication code is 0x00000000 (As shown in Figure).
![](assets/Pasted%20image%2020241121172700.png)
2. Start the EPC and eNodeB components, wait for the NB-IoT device to connect, and inject the Downlink NAS Transport attack message (As shown in Figure), where the security header type of the *Downlink NAS Transport* message is “*Integrity protected and ciphered with new EPS security context*”, and the message integrity authentication code is 0x00000000.
![](assets/Pasted%20image%2020241121212623.png)
3. The BC95-CNV chipset receives the *Downlink NAS Transport* attack message with the security header type “*Integrity protected and ciphered with new EPS security context*” and the message integrity authentication code 0x00000000 (As shown in Figure). This indicates that the device did not verify the message integrity and security protection, and can execute the message manipulation attack.
![](assets/Pasted%20image%2020241121212615.png)
![](assets/Pasted%20image%2020241121204317.png)