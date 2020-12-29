# Fischertechnik Training Factory Industry 4.0 - Modified Implementation

<p align="center">
  <a href="https://www.fischertechnik.de/en/products/teaching/training-models/536629-edu-factory-simulation-9v-education">
    <img src="https://content.ugfischer.com/cbfiles/fischer/produktbilder/ft/FT_Fabrik_Simulation_9V.jpg" alt="Logo">
  </a>
</p>

This project contains a modified implementation for the fischertechnik [**Training Factory Industry 4.0**](https://www.fischertechnik.de/en/service/elearning/teaching/lernfabrik-4). The fork is based on the txt_training_factory version v0.8.0 ([Release Notes](https://github.com/fischertechnik/txt_training_factory/releases)).

<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#overview">Overview</a>
      <ul>
        <li><a href="#vgr-tasks">VGR Tasks</a></li>
        <li><a href="#hbw-tasks">HBW Tasks</a></li>
        <li><a href="#mpo-tasks">MPO Tasks</a></li>
        <li><a href="#sld-tasks">SLD Tasks</a></li>
      </ul>
    </li>
    <li><a href="#installation">Installation</a></li>
    <li><a href="#build">Build</a></li>
    <li><a href="#network">Network</a></li>
    <li><a href="#mqtt-interface">MQTT Interface</a></li>
    <li><a href="#network">Network</a></li>
    <li><a href="#api-reference-cc-library">API Reference C/C++ Library</a></li>
  </ol>
</details>

## Overview

The factory consists of the following stations or machines. Each one of them run a TXT controller:

- **Vacuum Gripper Robot (VGR):** A three-axis robot with a vacuum suction gripper that can position products quickly and precisely in three-dimensional space. The vacuum gripper picks up products and moves them within the working space.
- **High-Bay Warehouse (HBW):** It is a storage area that saves space and allows computer-aided storage and retrieval of products.
- **Multi-Processing Station with Oven (MPO):** It is a processing station that simulates different processes like melting and milling.
- **Sorting Line with Color Detection (SLD):** A sorting path with color recognition automatically separates differently colored products.
- **Sensor Station with Camera (SSC):** A environmental station with a surveillance camera is used to record measured values within the factory. It has an environmental sensor and a photoresistor that allows the air temperature, humidity, air pressure, air quality, and brightness to be measured.
- **Delivery and Pickup Station (DPS):** It is an input and output station with a color detection and NFC reader.

The controllers exchange and communicate messages to coordinate the execution of the program. All the communication is done via MQTT (Message Queuing Telemetry Transport) protocol. The program implements the following two manufacturing processes:

- The supply process aims to supply the unprocessed products into the factory and store them in the warehouse. The process starts when the robot arm grabs the product from the input area and moves it to the identification station. Once the product information is available, the warehouse fetches an empty container. The robot arm moves the product from the identification station and places it into the empty container. Another small device within the warehouse moves the container and store in the warehouse's right place.
- The production process's goal is to perform the main processing on the unprocessed products and deliver them through the delivery station. It starts by moving one unprocessed product from the storage place into the pickup area of the warehouse. Then the robot arm picks the product and moves it into the input area of the main processing. Afterward, the product will go through a series of processing operations like milling and melting. Once the main processing unit finishes processing the product, a small robot arm within it will put the product on the sorting line's belt. A sensor detects the product color, and according to the color of the product, it will be placed in the corresponding collection area. Lastly, the main robot arm picks the product from the collection area and moves it into the delivery station.

These two processes are divided into tasks performed by different machines. To start executing a specific task the corresponding MQTT should be sent. After finishing the task, the machine sends an acknowledgment to indicate completing the task. **Therefore the processes can not run independently and need an external program to coordinate the execution and call the next step.**

### VGR Tasks

- **Get info:** This task is responsible for picking up the product from the input station and detect the color and the NFC ID of the product.
- **Drop to the container:** This task place the product into the empty container.
- **Move from HBW to MPO:** This task moves the product from the HBW machine to the input station of the MPO machine.
- **Pick sorted:** This task picks the sorted product from the SLD machine.

### HBW Tasks

- **Fetch container:** This task fetches an empty container if exists.
- **Store product:** This task store a product in the warehouse if there is an empty place.
- **Fetch product:** This task fetches a product from the warehouse if exists.
- **Store container:** This task stores the empty container.

### MPO Tasks

- **Processing:** This task apply operations like melting and milling on the product.

### SLD Tasks

- **Sorting:** This task sort the product and place in storage location according to the color of the product.

## Installation

The controllers programs for the machines can be found in the [bin](https://github.com/ghanem-mhd/txt_training_factory/tree/master/bin) folder. To install these programs follow the instructions using the [WEB server](doc/WEBServer.md). To install the original program provided by fischertechnik install the programs provided in the original repository [bin](https://github.com/ghanem-mhd/fischertechnik/tree/master/bin) folder.

## Build

You can import the git repository as a workspace in [eclipse CDT](https://www.eclipse.org/cdt/downloads.php) or build the programs with _make_. Please read the [building instructions](doc/IDE_Setup.md).

## Network

The next picture shows the [network](doc/Network_Config.md) overview with the TXT controllers.
![Overview Network](doc/Overview_Network.PNG "Overview Network")

## MQTT Interface

The [MQTT Interface](TxtSmartFactoryLib/doc/MqttInterface.md) describes the topics and the payload of the MQTT clients and the configuration of the mosquitto MQTT bridge.

## API Reference C/C++ Library

The Doxygen documentation of the C/C ++ library classes can be found in the [API Reference](https://fischertechnik.github.io/txt_training_factory_doc/html/index.html).
