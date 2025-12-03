---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---


I'm Peter, a PhD researcher in machine learning and embedded systems security at
OTH Regensburg. My work focuses on developing host-based intrusion detection 
systems using anomaly detection techniques tailored for networked industrial 
devices. 


## Team MOTRA

This is the team currently developing the MOTRA testbed for our work on IDS and automated pentesting systems. MOTRA is a hybrid testbed system developed at the OTH Regensburg in the heart of Bavaria. Given the [Industrial Control Systems Testbed Survey](https://spritz.math.unipd.it/projects/ics_survey/) interactive map, we are currently one of two systems located in Germany focusing on OT security. Our current research is focusing on generating data for different types of intrusion detection systems and how to create automated agentic pentests for OT systems. A more in-depth overview of our testbed setup and the different modules can be found here. On the subject of active network security, we attended [ScapyCon 2025](https://frozenbitz.github.io/frozenbitz/posts/SCAPYCON/) for our presentation on how to build and design a system for hands-on vulnerability testing using MOTRA and SCAPY. We showcased our work and a custom port for SCAPY built to emulate the base services powering OPC UA.

![Peter Dev](/assets/img/help.png){: width="100"  .w-60 .left} Peter (☕) is currently working on the main components for our testbed to generate HIDS data-sets, port OPC UA devices into preconfigured container images (reference images) and preparing the attack framework for MOTRA-Playbook. Current research focuses on the host side of industrial devices to integrate anomaly detection for security purposes. The main goal is to generate hardware traces to detect malicous interactions originating from a remotely conneted device or from local interactions within the filesystem. During my time at the OTH i've supported and worked on different funded research projects: [ES<sup>3</sup>M](https://www.enargus.de/pub/bscw.cgi/?op=enargus.eps2&q=es3m&v=10&s=2&id=952222) (Grant: 0350042A), [KRITIS<sup>3</sup>M](https://www.enargus.de/pub/bscw.cgi/?op=enargus.eps2&q=03EI6089A&v=10&s=2&id=14608808) (Grant: 03EI6089A) and [I4.0 ACE](https://www.las3.de/projects/kritis3m-2-2/) (Grant: KK5235103FG3).

__Expertise__: C/C++ and Python development; lots of Docker; Backelor Thesis on custom CNC drivers based off Trinamic TMC4361/TMC262 with some documentation over at [Hackaday.io](https://hackaday.io/project/162707-another-home-shop-cnc-mill); Masters Thesis on a custom embedded ethernet stack and an integrated firewall solution on the STM32H7 plattform. Two successfull grant applications at OTH Regensburg on security focused projects. 

[![ORCID](https://img.shields.io/badge/ORCID-A6CE39?style=for-the-badge&logo=ORCID&logoColor=white)](https://orcid.org/0009-0005-4762-6056)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-%230077B5.svg?logo=linkedin&logoColor=white&style=for-the-badge)](https://www.linkedin.com/in/peter-heller-55a996399/)
[![ResearchGate](https://img.shields.io/badge/ResearchGate-%2300CCBB.svg?logo=ResearchGate&logoColor=white&style=for-the-badge)](https://www.researchgate.net/profile/Peter-Heller-7)


![Sebastian Dev](/assets/img/Kraust.png){: width="100"  .w-60 .left} Sebastian is a PhD student specializing in reinforcement learning-based penetration testing for operational technology (OT) systems. With five years of experience in developing automotive cybersecurity solutions, he has contributed to three major research projects as a developer and project lead: [DECADE](https://www.net.in.tum.de/sites/decade/) (Grant: 16KIS0542), [VITAF](https://vitaf-projekt.de/) (Grant: KIS6AUT014), and [SKINET](https://skinet-projekt.de/) (Grant: KIS6KIITS33). Currently, he is working on enhancing the security of critical infrastructure systems as part of the [KRITIS<sup>3</sup>M](https://rcai.de/2024/02/14/kritis3m/) project.


**Expertise:** C and Python development; Docker; CI/CD; AWS; Advanced Linux system customization

[![ORCID](https://img.shields.io/badge/ORCID-A6CE39?style=for-the-badge&logo=ORCID&logoColor=white)](https://orcid.org/0009-0008-8108-2138)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-%230077B5.svg?logo=linkedin&logoColor=white&style=for-the-badge)](https://www.linkedin.com/in/sebastian-kraust-193534174/)
[![ResearchGate](https://img.shields.io/badge/ResearchGate-%2300CCBB.svg?logo=ResearchGate&logoColor=white&style=for-the-badge)](https://www.researchgate.net/profile/Sebastian-Kraust)



## Current Work
![MOTRA Mockup](/assets/img/motra-sideview.png){: width="200"  .w-60 .right} 
The core challenges and solutions of our research are part of the KRITIS<sup>3</sup>M funding project and generally address security for networked industrial devices. Operators have become increasingly concerned with the security of industrial networks, as the detection and monitoring of attacks has become more critical. As part of our efforts to develop specialized attack detection for OT-specific systems, we created a hybrid testbed that emphasizes ease of use and reproducibility for cybersecurity and pentesting. This testbed is intended to simulate dedicated attacks and interactions and provide the research community with data necessary for advances in intrusion and anomaly detection.

During our research, we noticed that most scientific publications often only release finished (specialized) datasets and that testbeds are usually optimized for very domain-specific problems. In both cases, reproducibility for a generic use case is often difficult to achieve because important details or tools are omitted. We decided to document and publish our development process with the most important design and implementation decisions as transparently as possible to ensure further development and reusability for other projects.

Our current PoC is designed to address four key areas that we have identified in the available literature: simulation, vulnerability analysis, pentesting, and data collection. As part of this development process, we have (currently) identified four modules that are relevant to these areas. For the simulation of industrial systems, we rely on various open source tools that we use in containers. For this purpose, we have developed our two components MOTRA Setups and MOTRA Images. MOTRA Images is a repository of ready-made building blocks for testing various open source stacks. Our building blocks allow industrial applications to be recreated in a highly abstracted form in order to simulate simple networked systems. MOTRA Setups provides the blueprints for applications and networks to create a working system from a configuration file format. Containers allow us to simulate our components locally, but can also be operated on modern embedded hardware. 

Our tools for attacking different systems, monitoring/capturing data, and modeling different types of interactions in our testbed are MOTRA Playbook and MOTRA Capture. With our open-source simulation modules, these tools are designed to enable non-invasive measurements and produce repeatable interactions. In addition to a testbed environment, we created and released our framework to offer security research specific attack and measurement setups. Our containers, network setups, and interactions for data acquisition can be shared through custom configuration files, enabling reproducible results that can also be used in other domains.

---

Our latest work on the testbed publication has been honored with a paper award at IARIA SECURWARE conference this year. 

![IARIA Award](/assets/img/paper_award.png){: width="972" height="589" }
_SECURWARE 2025: A Modular and Flexible OPC UA Testbed Prototype for Cybersecurity Research by Sebastian Kraust, Peter Heller and Jürgen Mottok_


### Build Stats of the Current Testbed Version

__OPC Servers__ \
[![pkg Release UA Standard dotnet to GHCR](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-dotnet-UAStandard-to-GHCR.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-dotnet-UAStandard-to-GHCR.yaml) \
[![pkg Release NodeJS node-opcua to GHCR](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-nodejs-node-opcua-to-GHCR.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-nodejs-node-opcua-to-GHCR.yaml) \
[![pkg Release Python asyncua to GHCR](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-python-asyncua-to-GHCR.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-python-asyncua-to-GHCR.yaml) \
[![pkg Release open62541 to GHCR](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-open62541-to-GHCR.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-open62541-to-GHCR.yaml)

__LDS Servers__ \
[![pkg Release UA Standard LDS to GHCR](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-lds-UA-standard-to-GHCR.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-lds-UA-standard-to-GHCR.yaml) \
[![pkg Release open62541 LDS to GHCR](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-lds-open62541-to-GHCR.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-lds-open62541-to-GHCR.yaml)

__Simulation and Demo Models__ \
[![OPC UA Model Generation and Verification](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/full-opc-server-model-gen-checks.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/full-opc-server-model-gen-checks.yaml)

__Model Compiler, Source Generator__ \
[![model-compiler Build and pkg Release](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-test-UA-model-compiler.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-test-UA-model-compiler.yaml)

__Custom Components__ \
[![pkg Release openssh to GHCR](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-openssh-to-GHCR.yaml/badge.svg)](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images/actions/workflows/publish-openssh-to-GHCR.yaml)


## Conferences and (soon) Journal References

General system description and usage of our current testbed
> S. Kraust, P. Heller and J. Mottok, "A Modular and Flexible OPC UA Testbed Prototype for Cybersecurity Research", in proceedings of the Nineteenth International Conference on Emerging Security Information, Systems and Technologies, Barcelona, Spain
ISBN: 978-1-68558-306-4, October 26, 2025 to October 30, 2025.

Design and concept for the testbed components for OPC UA and our custom components 
> P. Heller, S. Kraust, J. Mottok, "Building Modular OPC UA Testbed Components for Industrial Security Pentesting", in proceedings of 30th International Conference on Applied Electronics 2025, Pilsen, Czech Republic.

The general testbed design and research concepts
> S. Kraust, P. Heller and J. Mottok, "Concept for Designing an ICS Testbed from a Penetration Testing Perspective," 2025 IEEE European Symposium on Security and Privacy Workshops (EuroS&PW), Venice, Italy, 2025, pp. 561-568, doi: 10.1109/EuroSPW67616.2025.00071.

Initial work on timing related channels for anomaly detection
> Peter Heller, Jürgen Mottok. Timing Architecture Model for Embedded Systems Anomaly Detection. 12th European Congress Embedded Real Time Systems - ERTS 2024, Jun 2024, Toulouse, France. ⟨hal-04646322⟩

A very interesting talk at this years SCAPYCON presenting our work on OPC UA and how to integrate testing into our toolbox
> __MOTRA Testbed for OT Applications__
> The increasing interconnection between IT and OT is leading to growing cyber-threats for critical industrial systems. As more and more cross-system protocols such as OPC UA and numerous services are being integrated into modern embedded devices, new security-related challenges are emerging. This presentation discusses the problems of complex OT architectures using the example of OPC UA and then presents a container-based testbed for reproducible attacks. Participants will gain insights into industrial applications, the design of vulnerability-specific tests, and the handling of virtual systems in the testbed.
> [ScapyCon 2025](https://scapycon.com/scapycon-2025/)

## Recent Funding Projects

Current project - KRITIS<sup>3</sup>M
> Joint project: KRITIS<sup>3</sup>M – KRITIS Scalable Safe and Secure Modules; Subproject: Innovative security solutions for the long-term protection of communication systems in critical infrastructures. 
> [Funding Details in german](https://www.enargus.de/pub/bscw.cgi/?op=enargus.eps2&q=03EI6089A&v=10&s=2&id=14608808)

Successfull grant for the ZIM program: I4.0 ACE
> Software Security solutions for a scalable I4.0 Edge plattform developed with OTH Amberg Weiden, OTH Regensburg and industrial partners. 
> [General Information](https://www.las3.de/projects/kritis3m-2-2/)

The previous ES<sup>3</sup>M funding project on embedded security
> Joint project: ES<sup>3</sup>M - Energy Safe&Secure System Module; Subproject: Secure Software and System Engineering
> [Funding Details](https://www.enargus.de/pub/bscw.cgi/?op=enargus.eps2&q=es3m&v=10&s=2&id=952222)
> 
> ES<sup>3</sup>M - Energy Safe and Secure System Module - Project Report, Frauenschläger, Tobias / Heller, Peter / Mottok, Jürgen / Jost, Joachim / Böhm, Thilo / Krause, Sebastian / Weidenhammer, Detlef / Ostbayerische Technische Hochschule Regensburg, Laboratory for Safe and Secure Systems, 2022; [Final Report from TIBKAT](https://www.tib.eu/de/suchen/id/TIBKAT:1870325583)
