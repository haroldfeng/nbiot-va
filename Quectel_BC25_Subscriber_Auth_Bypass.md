
# Quectel BC25 Subscriber Authentication Bypass

## Overview
- **Description:** Quectel BC25 with firmware version BC25PAR01A06 allows to bypass subscriber security authentication and legally register the device to rouge NB-IoT network without the security key via software-defined radio (USRP B210).
- **Affected component:** Quectel BC25 is a high-performance, low-power NB-IoT wireless communication module. As defined by the 3GPP specifications, mobile devices must support mandatory integrity protection, as well as non-mandatory cryptographic protection. The Quectel B25 chipset has a vulnerability in the baseband logic implementation and receives NAS messages with null integrity (EIA0) and null encryption protection (EEA0), allowing attackers to use software radio tools (e.g. USRP B210) to bypass subscriber security authentication, legally register to rouge NB-IoT network without security key, and arbitrarily manipulate with the message.
- **Attack vector:** encryption_algo = EEA0 & integrity_algo = EIA0 & security_mode_command

## Attack Settings
- **Hardware environment:** deployed in the Dell OptiPlex 5000 configured with 32GB RAM and Intel i9-12900 processor running Arch system, as well as connecting a USRP B210.
- **NB-IoT network:** upgraded [srsRAN 4G](https://github.com/srsran/srsRAN_4G) and [Sonica](https://github.com/sonica-nbiot/sonica) open source project to implement the EPC and eNodeB components of the NB-IOT network (As shown in the Figure) and used the USRP B210 as the software radio. The simulated network is configured with the PLMN ID of 99970 and specific frequency .
![](assets/Pasted%20image%2020241121164818.png)
- **NB-IoT device** ( As shown in the Figure): equipped with a programmable SIM card (sysmo ISIM-SJA2) and connected to the simulated network. It is connected to the test device through the Virtual COM port and uses the AT command and diagnostic message monitoring tool (QCOM) to obtain system information about the UE for monitoring and analysis. The firmware version information is shown in Figure.
	![](assets/Pasted%20image%2020241121165017.png)

![](assets/Pasted%20image%2020241121165116.png)

## Attack Process
1. Set encryption_algo = EEA0 and integrity_algo = EIA0 in the epc.conf configuration file, as shown in Figure.
![](assets/Pasted%20image%2020241121165216.png)
2. Start the EPC and eNodeB components, wait for the BC25 to connect, and inject *Security mode command* attack message (As shown in Figure). The security algorithms are EEA0 and EIA0.
![](assets/Pasted%20image%2020241121165256.png)
3. The Quectel B25 chipset receives the *Seurity mode command* attack message and accepts the EEA0 algorithm and EIA0 algorithm (As shown in Figure). The device normally responses *Seurity mode complete* message with message authentication code 0x00000000, indicating that the device uses the EEA0 and EIA0 algorithm and the message is not encrypted and integrity protected. As a result, all subsequent messages are not encrypted or integrity protected. Attacker can manipulate with the message content at will.
![](assets/Pasted%20image%2020241121165513.png)
![](assets/Pasted%20image%2020241121165522.png)