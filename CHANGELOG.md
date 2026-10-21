
Program : irrigation_controller

# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).  
Types of changes : Added, Changed, Deprecated, Removed, Fixed, Security

----------------------------------------------------------------------------------------------
## [Unreleased]
### To be Added


### To be Changed


### To be Fixed

-----------------------------------------------------------------------------------------------  
# [1.1.1] - 2026-10-21
### Changed
- label update (valves_security_timeout -> valves_safety_timeout)

## [1.1.0] - 2026-08-22
### Added
  -  binary sensors ev1_active and ev2_active exposes actual state of the solenoid valves to HA. This allow HA dashboard to change the aspect of the Zone 1, Zone 2 command buttons (red when open, grey if closed)

## [1.0.0] - 2026-08-14
### Added
  - initial production version
