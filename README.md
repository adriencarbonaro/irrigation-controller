# irrigation_controller

## Overview
This controller is used to remotely control a drip irrigation system.  

It controls up to 3 irrigation zones and optionnally a submersible pump

Home Assistant controls the watering sequences
- activation of the irrigation controller, programmable start time of the irrigation sequence 
- behavior of each zone(availaibility and watering duration)  

## Features
### On the irrigation controller: 
- water inlet : one 3/4" connection, on the right side
- water outlets : 1, 2 or 3 3/4" connection,  facing downward
- for each outlet:
    - a manual control button (all 3 valves can be opened at the same time)
    - a LED that lights up when the valve is open

- status LED blinks when WiFi is connected
- controller is AC 230V powered 

### In Home assistant:
A button makes the controller active (watering according to zone paramters) or inactive (no watering)
Start_time : HH/MM [0:00 to 23:59] when the watering sequence shall start
The behavior of each zone is defined in Home Assistant with the following parameters:
- availability: true if zone is used, false if not used
- days of week: frequency of watering _(to be added)_
- duration: duration in mn [1 to 15] of the watering
- a button to manually operate the zone (immediate start on 1st push, immediate stop on 2nd push)  


Sequence definition : at start time, zone 1 is operated for the given duration if activated, then zone 2, then zone 3


## Hardware
### Electronic

![](./Photos/irrigation_controller_sch.jpg)

Schematic & Pcb design using KiCad 9

- Microcrontroller : Seeed Studio XIAO esp32-C3
- 12 solenoid valve control : power MOSFET IRFZ24N (55V 17A)
- Submerged pump 230V : Triac BT136-600

**Final design**
Pcb dimensions 148 x 61 mm, 4 mounting holes (141,4 x 45 mm)
![](./Photos/irrigation_controller.png)

**Various configurations are available**
a) 2 valves + 1 pump  
![](./Photos/irrigation_controller_2valves&pump.jpg)
D5 LED (= TRIAC is active) is optionnal 

b) 3 valves, no pump  
Yet to come

### Hydraulic
- Inlet : 3/4" (20/27) for automatic garden connection
- Output : 3/4"  (20/27) for automatic garden connection
- solenoid valves : 12V DC NC, 0.02 - 0.8 MPa, 1/2" (15/21) solenoid valve, type machine à laver
- 14 mm copper pipes

![](./Photos/Prototype_partie_hydraulique.jpg)

## Software

- esp32 module manages 
    - gpios 
    - a script to drive solenoid valves and the immmerged pump
    - a safety timeout to stop watering in case HA is disconnected durong a watering sequence
- HA manage irrigations sequences and manual control through a card in "Jardin" dashboard

### ESPHome firmware
binary sensor:  
- **GPIO8, GPIO9, GPIO10** : SW3, SW2, SW1 (must be high at boot)  

switch:  
- **GPIO3, GPIO4, GPIO5** : EV1, EV2, EV3  
- **GPIO6** : pump control  
- **GPIO20** : status LED  

button:  
3 virtual buttons to manually control EV1, EV2 and EV3 in HA 

wifi : automatically connects to the 3 possible WiFi routers of the home

script :
- **blink LED**: blinks the status LED according to LED_ON (100 ms) and LED_OFF (5000 ms)  
- **toggle_EV1, toggle_EV2, toggle EV3**: turn on and off the given solenoid valve ; turn on immerged pump when at least one valve is on, and off when all valves are off  
- **valves_security_timeout**: turns off every command after EV_timeout (typically 30mn)  

### Home Assistant configuration

All elements linked to the irrigation controller are found under the label "irrigation"

#### Device
- add "irrigation_ctl1" in appareils & service /ESPHome"

#### Entities
Actionnable entities are 
- commande_EVi: execute script toggle_EVi
- LED: switch_toggle status LED
- pump: switch_toggle pump
- valvei: switch_toggle valve i

#### Helpers
- common to all zones:
        - irrigation_auto: to enable the automatic watering program
        - irrigation_heure_debut: watering start time
- for ach zone (i = 1, 2 or 3)
        - irrigation_active_zonei: to enable watering progm on zone i
        - irrigation_duree_zonei: watering suration of zone i

#### script
- lancer_goutteAgoutte_1: execute "commande_EVi" if "irrigation_active_zonei" is true for "irrigation_duree_zonei"

#### automation
- Démarrage irrigation : at "irrigation_heure_debut", execute script "lancer_goutteAgoutte_1" if "irrigation_auto" set to true

#### dashboard
- yet to come
