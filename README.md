arduino-cloud-evohome-nodered
=============================

# Introduction
We're surrounded by systems in our homes that help us control and manage our environment. From controlling the heating system and monitoring the temperature to managing the lights and more, these systems make our lives easier. 

While we can control most of these systems through PC or mobile apps, they are usually locked-in and it can get sometimes not practical to have multiple applications. That's where an integrated platform comes in, offering a centralized hub for all our customized dashboards.

But additionally, sometimes, we want to combine data from different applications to trigger actions on other systems. And if you're an Arduino enthusiast like me, you'd want to integrate all of it with your Arduino projects. 

Arduino Cloud is an excellent solution for that, but integrating external platforms can be a bit tricky, especially with many commercial products lacking direct integration. That's where Node-RED comes to the rescue! It helps us redirect and manage the traffic between these commercial products and Arduino Cloud. 

This project is part of a series that I've created to showcase how I manage my home systems by integrating them with Arduino Cloud. In addition to this project, I've also worked on integrating TP Link switches and Tuya Energy meter, so you can check those out too! 

## What is Arduino Cloud?
Arduino Cloud is a platform that simplifies the process of developing, deploying, and managing IoT devices. It supports various hardware, including Arduino boards, ESP32, and ESP8266 based boards, and makes it easy for makers, IoT enthusiasts, and professionals to build connected projects without coding expertise. 

The platform's IoT Cloud tool allows for easy management and monitoring of connected devices through customizable dashboards, which provide real-time visualisations of the device's data. The dashboards can be accessed remotely through the mobile app Arduino IoT Cloud Remote, which is available for both Android and iOS devices, allowing users to manage their devices from anywhere.

## What is Node-RED?
Node-RED is an open-source visual programming tool designed for connecting and automating devices, services, and APIs. It provides a browser-based flow editor that allows users to create flows by dragging and dropping nodes onto a canvas and connecting them together. Each node represents a specific functionality or service, such as data input/output, data transformation, or communication with external systems. With its intuitive interface and extensive library of pre-built nodes, Node-RED enables users to quickly and easily create complex workflows without the need for traditional coding. It is particularly popular in the Internet of Things (IoT) domain, where it simplifies the integration of various sensors, devices, and cloud services, allowing users to build powerful IoT applications and automation systems.

## What is Honeywell Evohome?
Honeywell Evohome is a smart home heating system that offers individual room control for optimal comfort and energy efficiency. It consists of a central control unit, wireless radiator controllers, and a mobile app for remote control. Evohome allows users to create customized heating schedules for each room or zone in their home, enabling precise temperature control and eliminating energy waste. The system utilizes wireless communication to communicate with the radiator controllers, providing flexibility in installation and control. With features like geofencing and adaptive learning, Evohome intelligently adjusts heating settings based on occupancy and user preferences, ultimately providing a convenient and cost-effective heating solution for homeowners.

# Pre-requisites
You need:
* a Honeywell Evohome system
* an Arduino Cloud account (https://cloud.arduino.cc)
* an instance of Node-RED (running on a local or Cloud machine). You can find more information about how to install Node-RED [here](https://nodered.org/docs/getting-started/local).

You don't need:
* any Arduino, ESP32 or ESP8266 devices

# The process 
1. Get your Honeywell getconnected credentials 
2. Create the Device in the Arduino Cloud and program it
3. Create the Node RED flow 
4. Create the Arduino Cloud Dashboard 
5. Test everything 

## Get your Honeywell getconnected credentials
The process is very straightforward. You just have to create a Honeywellhome developer account and create an application.
1. https://developer.honeywellhome.com
2. Sign up (if you don't have an account yet)
3. Log in and go to the tab "MY APPS"
4. Click on "CREATE NEW APP"
5. Introduce a name ("App Name")
6. Introduce a callback URL
Note: You can use `http://localhost` if you don't have any
7. Get your key and secret. We will use it later on.

Full Honeywell documentation [here](https://developer.honeywellhome.com/api-methods)

## Create the Device in the Arduino Cloud
### Create the Device 
Go to the [Devices](https://create.arduino.cc/iot/devices) section of the Arduino IoT Cloud and click on ADD. 
Select "Any Device" and follow the instructions on the wizard.
Note: Save your `Device ID` and `Secret Key`. We are not going to use them, but it can be helpful for other use cases.

### Create the Thing 
In the Devices list, find the device you just created and click on "CREATE THING". Give it a name and. 

### Create the Variables 
**Important Note**: Every EvoHome setup is different. You may have several temperature sensors, set points, actions, etc. So, you will have to adjust the following instructions to your own setup.
In my case, I have two zones, each with an electrovalve controlled by a thermostat. The zones have the names: 'salon' and 'arriba'.

Each zone has:
* The current temperature
* A set point temperature

For the set point, we will need two variables. The first one will hold the information read from the platform (\<zone\>_target). The second one is the set point that we can define (\<zone\>_set_point).

Add the variables clicking on the ADD button. At the end of the process, your list of variables should look like this 

|Name   |Type   |Description   |
|---|---|---|
|arriba_set_point|CloudTemperature|The set point that you can set for zone 'arriba'| 
|arriba_target|CloudTemperature|The current set point read from EvoHome for zone 'arriba'|
|arriba_temp|CloudTemperature|The current temperature read from EvoHome for zone 'arriba'|
|salon_set_point|CloudTemperature| The set point that you can set for zone 'salon'|
|salon_target|CloudTemperature| The current set point read from EvoHome for zone 'salon'|
|salon_temp|CloudTemperature| The current temperature read from EvoHome for zone 'salon'|
|mode|String|Mode read from EvoHome|
|on_off|CloudSwitch|The action we want to do on the system|

### Create an Arduino Cloud API key
Go to https://cloud.arduino.cc/home/api-keys.
Click on "CREATE API KEY", enter a name.
Note down the Client ID and Client Secret or download the PDF. We will use these credentials in the Node-RED node.

## Create the Node-RED flow
Access your Node-RED instance (typically `http://<YOUR_IP>:1880`) and import the code:
1. Select `Import` in the menu
2. Paste the code that you can find in [flows.json](https://github.com/dbduino-prjs/arduino-cloud-evohome-nodered/blob/develop/flows.json) in the github project
3. Click on `Import`
4. Configure EvoHome node using the Honeywell API key that you created earlier
5. Configure Arduino Cloud node using the Arduino Cloud API Key that you created in the previous section  
6. Click on Deploy

## Create the Arduino Cloud dashboard
Go to the Dashboards section and click on CREATE. Assign a name and start adding the widgets according to the following table: 
|Widget type   |Widget name   |Variable name   | 
|---|---|---| 
|Messenger|Chat window|message| 
|Value|Question|text| 
|Value|Response|response| 
|Push Button|Send Question|send_message| 
The final result can be something like this: 

## Let's test the system


## Final considerations
Variable tweaking 
In the sketch, the following variables can be tweaked: 
maxTokens : Define the maximum number of tokens (characters) that ChatGPT will use in its response apiEndpoint : OpenAI has many different endpoints. You can investigate and play with them OpenAImodel : You can use a different language model. Check the full list [here]( $ https://platform.openai.com/account/rate-limits $ ). 
Remember that OpenAI tokens are not free. So remember to limit the maximum number of tokens with the maxTokens variable so that your billing doesn't grow without control. Bear that also in mind when you ask your questions. 
LED Blink 
The sketch makes the builtin LED blink every 2 seconds. This way, we know that the board is alive. It uses the ArduinoThread library that you can find[here](https://github.com/ivanseidel/ArduinoThread). If you want to disable the blink, you just have to comment out the line `#define USE_THREAD 1` 
Import the Thing and Dashboard with the Arduino Cloud CLI 
In the github project, you can find the YAML files describing the Thing and Dashboard: $ https://github.com/d-beamon/chatgpt-arduino-cloud/tree/master/IoT-Cloud $ 
You can use the following tutorial to create your Thing and Dashboard using the Arduino Cloud CLI easily and those templates. 
[Create a Thing with Cloud CLI]( $ https://docs.arduino.cc/arduino-cloud/getting-started/arduino-cloud-cli#create-things $ ) [Create a Dashboard with Cloud CLI]( $ https://docs.arduino.cc/arduino-cloud/getting-started/arduino-cloud-cli#create-dashboard $ ) 
Future use cases and expansions
There are many follow-up projects to this one. These are the ones that I have in mind: 
Chat with ChatGPT with your voice using a NanoRP2040 Connect. The idea is to use the mic of the board, process the speech, send it to OpenAI, wait for the reply and show the answer. We can use a display, a widget on the dashboard or use a TTS synth with a speaker. Use natural language to perform daily actions using a NanoRP2040 Connect board. Use ChatGPT as a parser of natural language receiving the list of actions in an actionable way 
