Introduction
------------
- What is ScopeSim
- What is the need for ScopeSim
- What niche does it fill? I.e. not ETC, not E2E
- Background of ScopeSim - ELT, SimCADO, HSIM, SimMETIS

Architecture
------------
- 4 main layers     
    - Source
    - FOV
    - Image Plane
    - Detector
- 3 stages
    - UserCommands
    - OpticalTrain
    - Detector
- Effects objects
- Connection to outside
    - Input: YAML files, instrument packages
    - Output: Raw / clean FITS files, 1/2/3D data  

Support Packages
----------------
- Dependencies
    - ScopeSim_templates
    - IRDB
- Periphery
    - AnisoCADO
    - SkyCalc_ipy
    - Pyckles

Use Cases and Examples
----------------------
- MICADO, METIS science team feasibility studies 
- Comparing instrument output within a common framework
- Observation campaigns
    - Support for proposal writing, step up from ETC estimate
    - Finding charts, and "expected outcome" in observation description   
- Pipeline development and testing

Documentation
-------------
- Interface docs - Creating instrument packages

Future Plans
------------
- ELVIS - The ELT Virtual Instrument Simulator
