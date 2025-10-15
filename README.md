# Automated Tower Clock Winder Control Logic
### Copyright 2019 - P. Stephen Vail of North Parish of North Andover, MA.

Ladder Logic for North Parish's Paul Revere Bell tower clock auto-winder mechanism.<br>

In awe-inspired memory of **Dana Fischer**; original architect and installer of the discrete analog winder controller in 1986.

Physically, there are two winding drums, each of which has a weight attached via steel cable.  One is for the bell striking mechanism, and the other for the clock itself.  Both drums are connected via chain drive to a common worm-gear drive motor through independent PWM controlled electric clutches. Photo-optic sensors track drum rotations and minute-hand position of the clock.  There is a limit switch at the top of each weight's travel range to disengage its clutch in case of confusion or a runaway condition. 

This project was designed for a [Do-More BRX-DM1E-18ED23](<https://www.automationdirect.com/adc/shopping/catalog/programmable_controllers/brx_plcs_(stackable_micro_brick)/plcs_-a-_cpus/bx-dm1e-18ed23>) Programmable Logic Controller, after Dana's original controller components were disabled by lightning in 2017. 
This repository initially contains version 55, created and maintained in Automation Direct's [Do-More Designer IDE](<https://www.automationdirect.com/support/software-downloads?itemcode=Do-more%20Designer>) and is the initial upload for posterity in October of 2025. This project continues to be a work-in-progress.<br>

Gory details of operation of the BRX and training videos can be found [HERE](https://accautomation.ca/brx-plc-analog-io-system-configuration/)


## Modes Of Operation

  In ALL Modes, the Red Stop switch will shut off the clutches and motor.  
     The Green Start button must be pressed to continue.

  The PWM clutches intentionally ramp up and ramp down in voltage to allow for a little
     slippage, thereby reducing the 'jolt' of the sudden release of the tension on the 
     drums and weights.

  Even when the Stop button is pressed, things will run around 5 seconds longer 
     to account for this slow stop.  
    
  The round EMO button will physically shut off power to EVERYTHING, BRX (PLC) INCLUDED!!!


## Auto/Photo Mode

  When the system is turned on, it has no idea how full the drums/cables are.
      To calibrate it, the CALIBRATE button should be pressed causing both drums 
      to be wound up to the limit switches. This will set the photo counters to 8, 
      which is two more than the old system.  This will allow the drums to unwind to a 
      safe point, allowing the 'window' on the cable to be in an appropriate range.

  Calibration also occurs AUTOMATICALLY after a long power failure, when in 
      Auto/Photo Mode, or if the system is switched from Timer Mode to Photo Mode.

  When the drum(s) unwind, the count is decremented every time the reflector 
      passes the photo detector.  Once the count goes to zero, the relevant clutch is 
      turned on, and the Drive Motor sees the demand and turns on as well. 
      Winding commences, counting UP every time the reflector passes the detector.
      When the counter reaches the setpoint, the clutch slowly disengages.
      Once the motor sees no more demand (ie both clutches off),  the motor shuts off.
      For efficiency, if one clutch starts the wind the other will start if near the bottom.


## Auto/Timer Mode

  When in Timer Mode, the system will run both drums for a pre-determined amount 
      of time (initially 30 minutes), every 24 hours (initially at 13:05 to avoid the lockout).  
      These times and durations are arbitrary and can be changed.

  In timer Mode, NO provision is made to count pulses, so the system relies 
      on the limit switches.
     
  Once the system is taken out of Timer Mode (back to Photo), it is recommended
      that the Pulse CALIBRATE sequence is performed.


## Manual Mode

The Pulse/Timer switch is ignored in Manual Mode.  
  The Green Start button and the Red Stop button control both clutches and the 
      motor.
  As always, the limit switches will shut off their respective clutch when tripped.


## Calibration Mode

  When CALIBRATE momentary button is pressed, run both drums to limit switches.
    This will set counts to 8, causing the drums to count down two rotations past the 
    current position and normal range to keep the 1-6 pulses within a safe range of cable

  The intent here is that once the momentary CALIBRATE SW is pressed, we would
    enter a calibration mode which would wind up both drums until their  limits, set the
    counters to 8 or some number outside of the normal range so that they count down
    to a comfortable range of the cables, then turn CALIBRATION mode off

  Calibration also occurs automatically after a power failure, when in Auto/Photo  
    Mode, or if the system is switched from Timer Mode to Photo Mode.

  Also, set the period time values and the TIMER_ON values


 ## A little bit about startup --
 Startup occurs in three phases, or Stages:
 
  **Stage 0** <br>Wait in a hard loop for the OS to mount the Flash Memory (SD) card for logging, and give the OS a chance to get going.<br> Also get the Time server from DNS, and set system time.
  
  **Stage 1**  <br>Determine how long we were without power, and do appropriate initialization. Wait 5 seconds for the system to settle down before entering the Main Loop
    
  **Stage 2** <br>The Main Loop.  Set up contact debouncers, and start processing events!
  
