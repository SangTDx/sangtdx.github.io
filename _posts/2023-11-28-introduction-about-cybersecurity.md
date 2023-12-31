---
layout: post
title:  "Introduction about Cybersecurity for Autmotive"
summary: "Vehicles are cyber-physical systems and cybersecurity vulnerabilities could impact safety of life."
author: sangtdx
date: '2023-11-28 1:35:23 +0530'
category: ['cybersecurity', 'automotive']
tags: cybersecurity
thumbnail: /assets/img/posts/car-cyber.png
keywords: cybersecurity, automotive, multi categories and tags
usemathjax: false
permalink: /blog/introduction-about-cybersecurity/
---

# Introduction

**Cybersecurity** is any collection of mechanisms and processes to protect a system from malicious attacks. Notice that this concept is different from Functional Safety which  the is a mechanisms to protects the ECU, the system and users systematic failures within the ECU malfunctioning. The majority of mechanism analyzed here are software mechanisms, and those software mechanisms is based on the implementation of **keys of security** based on the secured communication and secured identification of the components related to the secured communication. Cybersecurity also relates to processes that can argue and justify that a system is secured against malicious attacks based on the analysis of the intrinsic nature of the system.

The principal concepts describing Cybersecurity on automotive ECUs are:

* **Authenticity**, the determination than the component sending data is the one expected.
* **Confidentiality** ensures that communication between 2 components cannot be intercepted by a third one.
* **Integrity** ensures that data received by one entity was not modified by another entity during the transmission.
* **Availability** ensures that one entity keeps its stable state during malicious attacks.

The main use case of Cyber security in the Automotive industry are:

* **End 2 End (E2E) secured communication**. The secured communication between ECUs within a vehicle network. This encloses authenticity and verification of messages among components.
* **Vehicle connectivity with outsiders**. Secured Car2X communication for over the air software updates (OTA), and hot-spots for in-car Infotainment.

# Automotive Cybersecurity methodology

Along with the main work product process, any system development that includes Cybersecurity has to comply with the following work products (as the client required) from the beginning of the project:

* **Asset Definition** determines the mechanisms, behaviors, and system attributes that require cybersecurity.
* **Thread and Risk Assessment** is the analysis of different conditions within a system to see how easy it is to receive a malicious attack and how much damage might cause.
* **Security Goals Derivation** is the high-level identification of mechanisms to use to mitigate the analyzed risks from Thread and Risk Assessment.
* **Security Architecture Design and Analysis** is the adaptation of the original architecture to add cybersecurity mechanisms.
* **Security Mechanisms Design and Analysis** is the complete design of cybersecurity mechanisms and how they are adapted for each SWC.
* **Functional Security Testing** are functional tests from the system without defining any dedicated cybersecurity condition.
* **Fuzz Testing** is functional tests that search failures from no defined behavior (sending incorrect data or searching unexpected paths of execution).
* **Penetration Testing** is a black-box test performed by expert third parties with already known mechanisms to hack a system.
* **Security Validation** is a white-box test performed by expert third parties with preconditioned mechanisms to hack a system.

# Software Cryptography Basics

Cryptography is the set of mechanism and algorithm to perform cybersecurity on an SW system. Cryptography is divided into 2 types of cryptography:

* **Symmetric cryptography** uses the same steps to encrypt and decrypt (uses only so called - **public keys**). Decrypt performs the encrypt steps but in reverse order. Symmetrical cryptography can be defined as either block cryptography or data flow cryptography. Since symmetric cryptography relies uniquely on public keys, if one public key is compromised, then all ECU that uses that public key had a security breach. By means of this, the secured public key storage is important and these public keys shall be used uniquely to components that ensure secured key storage, often, these components are managed by the same team (for example, components, that on a specific level, are managed solely by the OEM).
* **Asymmetric cryptography** uses different steps to encrypt and decrypt (uses public key and private key) Asymmetric keys are perfect for environments where you cannot trust fully to the other component to send or receive secure information. For example, when a component, that is solely accessible by the OEM, requires to send or receive secured verification from a component of a Tier1. Then, the asymmetric cryptography enables the OEM to conserve the private key to "mark" other public keys to be distributed to all the Tier-1's components, so these Tier 1 components can be verified by the data produced by the private key. Thereby, the private key is secured on the infrastructure of the OEM by not being shared by any other team, and if one Tier1 compromises its own public key, this will not compromise all the other Tier1 nor OEM infrastructure. Some examples of asymmetric cryptography are integrating factor, discrete logarithmic function, an elliptical curve.

Some other *complementary* cryptography mechanisms are:

* **Hashes** are interface functions that hide the real values of data sets. They can be used to "hide" the real values of the data that can be decrypt or encrypt by the keys to verify its correctness. Hashes are virtually impossible to revert due any small change in its underlying value causes big changes on the hash.
* **Protocols** are complex mechanisms to use different types of cipher with hashes.

There is a principle called *Kerckhoffs Principle* which indicates that the security of a system relies almost entirely on the **secrecy of the key** and not on the secrecy of the algorithm.

A hash function takes one input value with variable length, then the hash maps the output with a predefined length to represent the fingerprint of data. A secure hash function is infeasible to the inverse computation of the output and it is infeasible when two inputs cannot yield to the same mapped output. Hash functions are not defined as cybersecurity mechanisms but are very important as a complement to some real security mechanisms. Usually hash are the transport entity of a private key signature that later is verified against the public key signature that is locally addressed.

## Attack Threads

Threads are defined attack types that can be documented as follows:

* **Spoofing** is the act of disguising a communication by acting as the trusted source, Spoofing is mitigated by protecting secret data by no storing the data in no secured sources, and use appropriate techniques of authentication.
* **Tampering** is the act of deliberately modifying the data by using non-authorized channels. Tampering is mitigated by using appropriate authorization techniques and complement the use of Hashes, MACs, and digital signatures.
* **Repudiation** is the act of a system of not having methods to track and log an entity and its actions leaving room for attacks from this entity. Repudiation is mitigated by using digital signatures, use of timestamps during communication between trusted sources, and audit trails.
* **Information Disclosure** is the failure of a system of not protecting sensitive and confidential information from entities that are not supposed to have access to this kind of information. Information Disclosure is mitigated by authorization requests, encryption of sensitive data, privacy-enhanced protocols, and protection of secret data by not storing this data in unsaved places.
* **Denial of Service** is the act of shutting down the system, network, or making inducible any source to its intended users. The way to mitigate denial of service attack is by ensuring appropriate authorization and authentication, filtering entities, and ensuring the quality of service of the system.

## Symmetric cryptography

Symmetric cryptography uses the same key to encrypt and decrypt. This type of encryption is normally used for data confidentiality, integrity, and authenticity that requires low-profile security mechanism. The distribution of keys shall be careful because anyone with the key can access all security mechanisms. 

The most common symmetric cryptography is the **Advanced Encryption Standard (AES)**, AES is based on block cryptography algorithms and needs the inputs of the key and the fixed data length (128 bits, 192 bits, or 256 bits). AES performs cycles of encryption rounds, these rounds operate combinations of byte substitution, shifting of bytes, or key addition to produce a CipherText.

In general, symmetric cryptography can create **messages of authentication of code (MACs)** during their procedure using the secret key. MAC is a fixed-side digital code that can be only created by the key creator. The MAC is transmitted along with the encrypted data, if the receiver can decipher the MAC, then it is assumed that the encrypted data can be decrypted too. There are two types of MACs, the one defined by hash functions **(HMAC)** or the one defined by block encryption **(CMAC)**.

## Asymmetric cryptography

Asymmetric cryptography uses two keys (one public and another private), These keys have relatively large lengths. The public key can be shared with all the entities that can collaborate with the system; whereas the private key cannot be shared with all the entities of the system and shall be dedicated to a specific entity.

Asymmetric cryptography algorithms are based on one key encrypting the data and the other one decrypts the data. For example, if the public key is used to encrypt data, then the private key is used to decrypt the data.

The most common algorithms of asymmetric cryptography are **RSA**(Rivest, Shamir, and Adleman) and **ECC**(Elliptic Curve Cryptography).

### Digital Signature

The **digital signature** is based on asymmetric cryptography and offers data authenticity and data integrity. The digital signature creator generates the signature by the hash generation of complex data, to later encrypt this data, using the key, before transmitting it to the user. The user decrypts the received data to  compare the hash data with the decrypted hash data, that has in its own, and if they are the same, then the data integrity and data authenticity are guaranteed. Notice that the encryption can be done by the private key or public key, or otherwise, depending of the mechanism architecture (decryption is going always to be the opposite key than the encryption key).

Digital signatures are mainly used by **certifications**. Certifications are mechanisms to ensure that one data-sending component is allowed to send those data. Certifications might contain personal data from the sender (for example IDs, sensitive information) to be identified, this personal data can have expiration time.

The automotive industry permits secure interaction of the ECU with diagnostic tools or external *back-ends* using certifications. This ECU access is supported by different certifications for each access level (Root, Tester, Checker, …).

# Cybersecurity use cases on the Automotive industry
## Flash Programming

The ECU Flash programming encryption can be performed using two methods:

* **HMACs use**. The manufacturer creates an HMAC by a secret key and a hash. Then the manufacturer transfers the file to reflash along with the HMAC to the ECU. The ECU takes the HMAC and compares it to the decrypted HMAC by the secret key. If the HMAC verification is correct, then the ECU reflash is permitted.
* **Digital Signature**. Same process than the HMAC but using public keys and private keys. The public key is generated by the manufacturer delivering a digital signature, then the ECU takes this signature and processes it with the private key. If the signature verification is correct, then the ECU reflash is permitted.

<figure style="max-width: 100%;">
  <img src="/assets/img/blogs/automotive/cybersecurity/flash-programming.png" alt="use cases on the Automotive industry Flash Programming" style="width: 100%;">
  <figcaption>use cases on the Automotive industry
Flash Programming</figcaption>
</figure>

If the over the air feature is available to the ECU, then it is possible to update the SW application binaries using the same methods as above. OTA might require an additional pair of keys to verify the commands to perform SW update. By this, preventing malicious commands to trigger the SW update to interfere with the processing of real SW updates and therefore avoiding denial of SW update service.

## Vehicle-Tester communication

Vehicle Tester communication is the secured way to assign privileges to testers based on the OEM server whitelist back-bone. This is based on platform certifications and root certifications with their respective keys. Here, the tester asks for permission to perform tasks on an ECU by communicating with the OEM server, the OEM server based on the certification request, identifies the tester, and returns with permission to perform the requested tasks.

## OEM Backend communication with the vehicle

The vehicle can have communication with  OEM servers by root certifications and platform certifications with their respective keys. This use case is based on TLS and the revocation or change of certification if the ECU have a compromised key.

OEM backend communication is also important in the case of over the air, since the secured and verified OEM infrastructure is required to send the OTA commands to manage the over the air in specific ECU. Specific "milestones" commands can be required to be verified by special OTA pair keys to avoid the process of unauthorized OTA request that can denial services or complete unauthorized process. 

# Autosar Crypto Modules

The Autosar cryptography cluster is composed by the following modules:

* **Crypto Service Manager** (CSM): This module communicates with the application, SWCs or BswM to offer services in demand and handles the cryptography operations. The availability of these services depends totally of the Crypto drivers that are available in the ECU, but these are the principal CSM primitive types that CSM supports:
    * Random number and hashing generation.
    * Data encryption/decrypt.
    * MACs
    * Digital Signature
    * Models of operations based on “jobs”
    * Asynchronous/Synchronous process of jobs.
    * Generation, derivation, and exchange of keys.
    * Freshness counting.
* **Crypto Interface** (CRYIF): This module enables CSM to access Crypto Driver features based on standardized interfaces.
* **Crypto Driver** (CryptoSW and CryptoHw): These modules are the ones performing the cryptographic functions. CryptoSW uses SW libraries whereas CryptoHw uses HW capabilities (SHE, HSM, TPM, …).

![CSM Stack](https://raw.githubusercontent.com/CharlieGearsTech/CharlieGearsTech.github.io/main/images/sc2.png)

## Crypto Service Manager

**Crypto Service Manager** (CSM) is the service module from BSW that manages Crypto services (such as description of keys, generation of random number, generation of hash, and digital signature verification). These crypto services can be asynchronous or synchronous, so CSM might support cycles of services to process or have in demand request. CSM is above the Crypto interface and Crypto drivers and it is the unique module that can request Crypto services directly. CSM uses CSM queues to select the priority of or be able to cancel asynchronous CSM jobs. Usually, the implementations of CSM contain interfaces to enable the user-defined call in the SWCs, application or callbacks of other modules.

A **CSM Job** is the abstraction of CSM keys, CSM queues, and CSM primitive types. CSM has the properties to be able to dispatch jobs based on priorities. The jobs can be synchronous or asynchronous, Sync jobs wait for the returning value of a finished call and do not use callbacks; whereas async jobs are inserted into the queue based on their priority when the respective callback is finished.

Based on the exchange of information on the security stack, each CSM key is mapped to a CRYIF key and a Crypto Driver key. So a CSM job shall be consistent with its CryIf keyy and CryptoDriver key. 

The CSM Queues are mapped based on the Crypto Driver, CRYIF uses one channel for each CSM Queue to a Crypto Driver in order to process one job at a time.

One possible crypto stack flow is:

* One SWC asks for a CSM service using the *API Csm_\<PrimitiveType>()*. This API use Job IDs to identify every CSM primitive process.
* CSM adds the required job to one of the available CSM queues, the scheduler processes this job based on its priority.
* If the CSM job is synchronous, CSM executes *Csm_MainFunction()* to transmit the selected job to lower modules.
    * if the CSM job is asynchronous, then CSM executes the respective callback to transmit the selected job to lower modules.
* The job is transmitted to the Crypto Driver, which processes it, and returns a specific value to let CSM know the result.
* CSM returns the result to the SWC while removing the processed job from the queue.

CSM primitives define each job type based on:

* Crypto predefined generic algorithms.
    * MAC generation or verification.
    * A digital signature generation or verification.
    * Encryption/Decryption of symmetric keys.
    * Encryption/Decryption of asymmetric keys.
    * Hashing, random number generation, etc.
* Specific algorithm configuration
    * Algorithm family (AES, RDS, …)
    * Algorithm mode.
    * Job processing mode.
    * Length of input/output data.

CSM can enable the user to define function as **preprocessing** and **postprocessing** to a CSM job callout. Preprocessing of a CSM job callout can disable the CSM job process if there is no correct condition to execute the CSM primitive request, and the prostprocessing CSM job callout is able to provide to higher layer function additional data such as the CSM job return.
    
CryIf is the implementation of cryptography abstraction made by the crypto drivers and coordinated by CSM access. CryIf offers independent interfaces from HW/SW crypto drivers to superior channels to process the crypto operations.

Crypto Drivers are the principal responsibilities of the crypto operations. They specify the crypto capabilities and the composition of the key, as well as, the keys access properties.

The key component is the classification of key types made by the Crypto Drivers. Examples of these key types are Private key, Public key, hash values, random number certification, etc. Autosar defines these key types to perform crypto operations. Each composition key type has one ID, Autosar defines a key type ID below 1000, when two different key types have the same ID, then these key types can be used such the same type (Length, operations, access, etc). Keys ID that is above 1000 is free-definition to e used by the vendor.

## Security on On-board Communication

Security on On-board Communication (**SecOC**) is an additional module at the  Autosar communication stack related to PDU transmission authentication and integrity. SecOC retrieves PDUs from PDUR and adds security information to encrypt based on the PDU payload and the SOME/IP transformer use. SecOC supports various communication buses such as CAN, FlexRay, and Ethernet but are not supporting LIN yet. 

![SecOC_Pdu_Flow](https://raw.githubusercontent.com/CharlieGearsTech/CharlieGearsTech.github.io/main/images/sc4.png)

One use case of SecOC is:

* PDUR receives one PDU from any communication module (CanTp, FlexRay, Ethernet).
* PDUR transmits the PDU to SecOC, then SecOC converts this PDU to a “secure” PDU.
* SecOC along with CSM verifies the PDU authenticity and integrity.
* SecOC returns the PDU without security wrappers to PDUR in order to finish the PDU processing. 
    * When SecOC determines that one PDU is insecure, SecOC discards the PDU, and PDUR receives nothing from SecOC.
    * If SecOC determines that one PDU is secured, SecOC forwards the PDU to PDUR to be routed.
* SecOC communicates the PDU verification results to ASW.
* Additionally, SecOC can support PDU data freshness. This is a configurable option if the ECU requires the freshness of data. This freshness of data can be integrated on the secured PDU and sometimes even be splitted to a pair of associated PDUs.

![SecOC_Stack](https://raw.githubusercontent.com/CharlieGearsTech/CharlieGearsTech.github.io/main/images/sc3.png)

The transmission of secured data among ECUs is based on the authenticated message that is composed of data + freshness + MAC value. The MAC part of the authenticated message is generated internally by the sender and it is based on the private key shared among ECUs.

![ECU_Secure_Message](https://raw.githubusercontent.com/CharlieGearsTech/CharlieGearsTech.github.io/main/images/sc5.png)

Autosar does not specify a freshness definitive calculation. Autosar SecOC only support Freshness-
value callout from a module called Freshness Value Manager (FVM). FNM is the module that support mechanism to avoid malicious replication of messages that are exchanged between ECUs. These malicious replications are progressive changes without detection. To avoid these replications, these Freshness mechanisms are defined:

* **Message Counter Based Freshness (MCBF)**. Each time a message is transmitted, one internal counter which is independent of the ECU will be incremented. The disadvantage of MCBF is that counters shall be persistent and will add CPU load due to NVM processing. Another disadvantage of MCBF is its relative weakness from out of sync counter conditions. For example, if one component does not change its counter and it returns to normal enought time to wrap up again within the threshold of the last recorded value, the Message Counter Based Freshness would not be able to detect any malfunction conditions that causes the component to be on halt.
* **Trip Counter Based Freshness (TCBF)**. TCBF is incremented when a trip is completed (ECU message process). FVM increments the TCBF along with a synch message (TripResetSynchMessage) to both, the sender and receiver. TCBF is based on the hybrid sending of freshness based on 3 counters to support automatic re-sync:
    * Trip Counter. ECU incremental persistent 4 bytes for each ECU process.
    * Reset Counter. PDU incremental no-persistent value, this counter increases itself when a message counter has suffered an overflow.
    * Message Counter. PDU incremental no- persistent value, this counter increases itself every time a message is sent.
* **Time Stamps.** Verification for each ECU with a real-time value, time-stamps shall be sent by “secure” messages.
* **Hybrid mechanisms**. Combination of Timestamps and Freshness counters.

Conditions of Out of sync counter happens when 2 counter mismatch during E2E communication. The following mechanisms prevent the out of sync counter mismatching:

* **Truncated freshness**. This approach recognizes small differences in the freshness values from two ECUs. For example, sending values with a resolution of 10 seconds and starting on 11 seconds, then the receptor verify the valid freshness before 19 seconds.
* **Challenge-response protocol**. This approach is able to detect large differences in the freshness values from two ECUs. Challenge-response protocol is a “challenge” special message from one ECU to another to perform a sync execution.
* **Periodic counter-message**. This approach injects periodic messages to the schedule to verify the counter from both ECUs and thereby avoiding out of sync conditions.

## Special case Transport Layer Security

Transport Layer Security (**TLS**) ensures the data exchange privacy and integrity on the TCP protocol, which alone cannot ensure these features. TLS is the abstraction of the Session layer at the Ethernet communication stack. This layer is just above the transport layer (TCP in this case).

TLS encapsulates a security mechanism collection:

1. For privacy, symmetric cryptography is used.
2. For data integrity, HMACs are used. HMACs calculations create secret and temporal keys that are linked to the specific session of TLS.
3. The server authenticity is always ensured by Digital Certifications.
4. Optimally, the client can be authenticated if necessary.

Levels of TCP/IP

* Application Layer -> COM/SomeIP
* Session Layer -> TLS
* Transport Layer -> TCP
* Network Layer -> IPv4/IPv6
* Link Layer -> Ethernet

A TLS cryptography session specifies the cryptography mechanisms that one group of clients supports. The server always chooses to communicate with the client with the best robust TLS cryptography session.

# Crypto Instances of use
## Secure Bootloader

Secure Bootloader prevents the execution of altered SW during the trusted execution chain of the ECU (programmingSession). The secure bootloader shall verify the SW integrity for each ECU startup by using checksums, signature validation, and MAC/Keys containment in a secured area. This integrity validation prolongs the startup time, so HW specific solutions can be considered to mitigate high Startup times.

The Secure Bootloader requires truthful SW and HW entities to save the Boot MAC/secret key inside them. For example, HSMs or eShe compare their Boot MAC against a computation from the bootloader host; if both are equal, then the bootloader is considered to be secure. Thereby, the bootloader performs calculations to determine the Application level MAC and ask HSM to compare with its stored Application-level MAC. If both coincide, then the application is started. If any of these comparisons do not match, the HSM is blocked and triggers an ECU reset.

## Secure Diagnostic

The secure diagnostic is based on whitelist to trusted authorities by certifications. Each role has process permissions or diagnostic data access. The OEM contains back-end devices to establish certifications and roles, while the vehicle verifies these certifications by authentication demands. The OEM certification can have an expiration time or the diagnostic tool can have restricted uses cases based solely on the OEM back-end infrastructure.

## Secure Software download

It is possible to secure the ECU against malicious SW binaries download. Each SW binary can contain a verification structure that contains the signature of a private key, then this signature is verified against the public key during the download process. If the SW is not valid, then the verification does not permit the binary to be attached to the ECU, if the SW is valid, then the verification permits the binary to be inserted on the ECU to be used. The secure software download can be used along with other complement secure methods such as session key on UDS service 27h or Random Number comparison, or Part Number identification.

The verification structure contains the signature of the private key, but can also contain CRC and other verifications in order to detect malicious changes on the data of the binary. Since these additional verifications shall be secured as well, they are executed after the verification of the signature.

Software binaries can have different roles in the OEM use cases. For example, the ECU can integrate several types of public keys to be able to change between use cases of software binaries. Usually, there can be a development mode and a production mode on the ECU, the development mode uses development pair keys and enables the ECU to run development binaries before and after production, while the production mode uses production pair keys to have official software in the ECU that are identified to be stable enough to run on production vehicles.

## Secure over the air commands

When an ECU support over the air commands, some OTA commands might request verification of signature. This verification of signature is specific for OTA commands and for special OTA milestone commands that might authorize routines such as resume pause of binaries download, verification of compatibility of the binaries, and preparation of the software swap. If the milestone OTA command is not verified correctly, then the dedicated routines are going to not be executed and the ECU shall respond with a negative response.

OTA command verification is independent verification and can be required in the ECU with the software download verification, the OTA verification signature permits the software download but is requires the software download signature to detect malicious binaries, and other special verifications.

## Production and development SW verification

The ECU during its processes that require verification of signature might act different based on the production phase, so during development processes, the key shall be development, whereas any production phase relies solely in the stable production keys. In some cases, it is required to change among keys, so the ECU shall support different key slots and logic to select the correct key based on the mode inputs. So one use case is that after production release, vehicles can detect a bug, so when returning to the "garage", then a special development software can be verified and integrated on the ECU to debug the issue.

### Reference Link

[https://www2.cs.arizona.edu/~mccann/styletemplatesCP.html](https://www2.cs.arizona.edu/~mccann/styletemplatesCP.html)

[https://github.com/MaJerle/c-code-style](https://github.com/MaJerle/c-code-style)

[https://users.ece.cmu.edu/~eno/coding/CCodingStandard.html](https://users.ece.cmu.edu/~eno/coding/CCodingStandard.html)