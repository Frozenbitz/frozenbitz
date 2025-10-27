---
title: MOTRA - Design and Build customized industrial Testbeds
description: A short introduction to the MOTRA testbed and a collection of links and publication history.
date: 2025-10-17 12:00:00 +0200
categories: [MOTRA]
tags: [motra, docker, opc ua, testbed]     # TAG names should always be lowercase
image:
  # path: /assets/img/motra-maxi.png
  path: /assets/img/motra-title.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAARCAYAAAC8XK78AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAP0SURBVEhLrZXdaxxVGMZ/Z2Z29nuzabOdmGa7NW1ACkINCEoqvciNKPXSDxDb/gfiheiN4I2I3nulIHqhXggikiAIlQrVtlGrbSDFbptktzG2s9mZ3WR35+PM8SJ2m9mNYMHf3TnP8877zJl3ZoRSSrEHSimEECilkFKiaRq7rbqu99dCiF2VD4b4twBnz57lzJkzLCwsIIQgiiISiQSVSoXz588zPT2NZVmkUilOnz49WD5Eq9Uik8lgGEZsX4utdnHs2DEuX75MNptFKYWmaZw4cYLZ2VkAfN/n2rVrLC4uxk5mL954+z2+mf+W1996lwsXF2Na/wS63S6dTgdN00gmk+i6TrvdplAosL29TaFQwPM8wjAklUrRbrcZHR3tX8hxHIrFIkKI2CNxXZf573/knfc/YO7pZzj15DRzc3N9vR+gXq9z/fp1fN9H13U6nQ4zMzMsLS0RBAFRFGFZFpubm1QqFVZXV5FSopQim80SRRG+7zM5Ocnx48f7DcIw5NkXXkMBppni+eee4pWXTg0HGETdvooYfwT0RGzftm3OnTtHFEUYhsHY2BgnT57EaTfZdBscPFAmaSZjNR9/vsCnXywwdXiSN199manKxH1R7UFUu6KUt6WilUtKhf6gPESz1VR3m3eUUkrVNlZVz+/FdBlJ1XTbSkqpIhXFtKEhVPXfCEbKrKzfxR05iqpdgSgctAEgI0ltY41QBmiRzvLyMsXsPu40/sIPvL5PExrFQg5N0xDEX9n7Abwt5IcvEv30CeuNFrVajWr1JuLw46i1XyCSsUKA+R++5svvPqPpbrK8vIzjOFSrVcrjh9hobBDKvYPvph9AdV2UDJAdh3w+z5EjR5iYmMDzPEgXodfaMQbr4NcBSCfT5DJ5ZCgplUqMj49TKpV2hlZKNKGx1enitrf6DQeJDaGyb6EaK7Ssx7Btm3Q6TQkXc6QEudJOc5ECoYNsgVnGaTexGzYGCcIwRNd1Qs1nqnyUbs/H8wOMf76axUIu3n0wAAChj7r9O6o8A3eraJkRyObAnYfcLH/aO5/pCSvbD7HV2WK728ba/xAr6zeZtA5RXV0nkzIJeh3CMGR0/xhmwmRkIMTQEGKYiIOPIlYv7TTPHwB3AeyP8JxLVKtVbty4wZ2GBwiQLXKZHNl0jlv1KmXrEH/cqnPx1yVW1tZxXZdWq4WSIT0/wPODWLvhE9gL6YDzFa56Amc7jVKKpBEwPqYjUlODbro9jws/X8XaV6CYz+z8R8wkmpHAGtsX8/63APfwa3R9k1Aq8qkuJB8edMSwmy6mYSAEdLoeVinenAcOAOCvARGYhweVPbGbLlLKoTu/x4MH+J/5G6IWTrJKHMfLAAAAAElFTkSuQmCC
---

MOTRA is a flexible framework for creating testbeds tailored to the user's needs.

Its main features are its modularity and extensibility through packaging testbed components as containerized applications. Docker is the containerization solution of choice. This repository contains the sources for all available images on Dockerhub. There is a companion repository available which contains ready-to-use testbed setups that uses these images.


Checkout our [examples](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-setups) and [images](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images) over at GitHub for more information.


## The MOTRA core components

Currently there are four main components to our testbed, which can be used to 
create a working testbed with automated data capturing. The different components are 
used to create a simple and recreatbale environment for testing and verification 
of vulnerabilities and pentests. Therefore we offer different tools, images and
instructions for different aspects of the testing process.

One major goal of our testbed design is the creation of datasets for our current 
work on intrusion detection. This requires us to build a working testbed prototype 
to mimic other devices (in our case OPC UA network) and to create a virtual adversary
to use for testing. Finally we need to have one entity inside the network to 
capture data and monitor/instruct the data generation process.

### MOTRA-Images - The Base Layer

The [image repository](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-images) is the main starting point to build a working setup for testing.

![Motra Images](/assets/img/motra-images.png){: width="972" height="589" .w-50 .left}

This part of the tooling covers our so claled blueprints. Images that contain
reference implementations that can be used to create vulnerable applications or 
implementations that can be used in combination with other services to mimic an
industrial appliation. More complex devices can be created by mixing and connecting
resources at the container level to allow for interaction between image instances.


When using the images, our initial goal was to build a setup allowing us to switch
different implementation types of servers when working with OPC UA stacks. This 
allowed us to create servers that were near identical in behaviour, but allowed 
us to test different vulnerabilites by switching server instances. From 
there we went on with adding other container implementations and started to build
a setup that could be used to create more complex applications.


### MOTRA-Setups - The Configuration Layer

The [setup repository](https://github.com/Laboratory-for-Safe-and-Secure-Systems/motra-setups) shows how the current image layers can be used for testing 
and setting up different devices. 

![Motra Setups](/assets/img/motra-setups.png){: width="972" height="589" .w-50 .left}

The current implementation contains a simple setup for mimicing a water treatment 
process that serves as a introduction to the framework. This process uses a simple
simulation and a fixed number of devices to run a virtualized setup using three 
OPC UA servers, two clients and additional logic for the simulation. The current
examples for the setup instructions include a configuration that allows running 
a number of virtual devices on custom hardware like a raspberry pi. 

A nice feature of using docker compose as a testbed orchestration service is the 
ability to parse files from remote repositories. By default all of our resources
for building a testbed would need to be installed locally and had to be updated 
manually every time we need to change parts of the core implementation or the 
simulation logic. This way we only need to pass compose a valid repository and 
the builder can pull data live from the repository, when starting our services.
If we have a working git server inside the testbed, we can use a single configuration
file to create our devices and handle different configurations for the networked
nodes inside the testbed we are configuring.

A very simple example: 

```yaml
services:
  plc-server:
    container_name: "plc-server"
    build:
      context: ${IMAGE_REPO_URL}#main:opcua/server/nodejs-node-opcua/latest
      args:
        NODESET_MODEL: "PLC.NodeSet2.xml"
      additional_contexts:
        container_context: ${IMAGE_REPO_URL}#main:opcua/server/nodejs-node-opcua/latest
        nodeset_context: ../meta/demo-nodeset2
        companion_context: [...]
  plc-historian:
    container_name: "plc-historian"
      - [...]
  plc-logic:
    container_name: "plc-logic"
      - [...]
    build:
      context: ${IMAGE_REPO_URL}#main:opcua/plc-logic/python-opcua-asyncio/latest
    networks:
      - [...]
```
{: file='plc-logic.compose.yaml'}

Here we can see three services: plc-server, plc-historian and plc-logic. These 
are built and configured using the docker build daemon using our current GitHub 
sources. Additionally we can pass in any custom configuration at this point as 
well as different network configurations for testing. This way we can build 
different device configurations and mimic multiple services using a single 
configuration file and access to a local or fully remote git/svn service.

Once everything is built and setup, we can remove access to the remote resource 
from the testbed or we can filter any unwanted traffic to the remote service. 


### MOTRA-Playbook - Collection of Attacks and Frameworks

MOTRA Playbook is currently on private visibility.

MOTRA Playbook is our collection of tools, packages and vulnerabilities we found
online during our research. We packaged all the tools into a custom container 
so we can run different payloads from different devices in a similar manner. 

Playbook can be built from the command line using docker build or a compose file. 
Usage is similar to docker one shot containers. For the data capturing process, 
the current version of Playbook can also be used as a payload for MOTRA Capture to 
run preconfigured attacks on different targets inside the testbed. 


### MOTRA-Capture - Data Collection and Automation Tools

MOTRA Capture is currently on private visibility.

MOTRA Capture is an automation framework that allows to create measurement 
configurations based on a client server architecture, that can be run inside the 
testbed. The goal is to automate the measurement process together with sample 
attacks to create clean and repetetive datasets for research purposes.


## Current Publications

tbd... 
