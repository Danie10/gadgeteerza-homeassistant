# GadgeteerZA Home Assistant
A repository of the YAML files used to configure my Home Assistant instance. My instance is hosted in a Docker container on an Intel NUC inside the home.

## Screnshots
<p align="center">
<img src="images/dashboard-screenshot.jpg" style="width: 500px; max-width: 100%; height: auto" title="Solar dashboard tab">

<img src="images/dashboard-screenshot2.jpg" style="width: 500px; max-width: 100%; height: auto" title="Front dashboard tab">

<img src="images/HA-Solar-Status-2022-09-18.gif" style="width: 500px; max-width: 100%; height: auto" title="Video showing grid lost and battery time left">
</p>

## Description
These are the YAML config files from my own installation of Home Assistant. Every installation is unique, and built up from others' ideas and code, and mine will likely be expanding and updating over time. So I will share what I've been learning on my journey, in the hope it helps other new users. I've battled up to three or four hours sometimes to just get one card working properly, and it often comes down to syntax or formatting issues.

A key focus is on the Modbus TCP interface with a Victron solar system, and a Balancell battery. This instance of HA is running in a Docker container (docker-compose.yaml also included).

This is not a 'how-to' guide, but intended more as a reference for others to learn from the syntax and formatting of the code, or for accessing Victron or Balancell battery Modbus address registers.

You can watch a video at https://www.youtube.com/watch?v=dlvlhou70VA about my initial setup of the dashboards.

## Key features and Integrations
The following are some notable features that this particular installation uses:
* Modbus TCP interface to a Victron CCGX with a Multiplus II inverter/charger, and Victron SmartSolar charger
* Balancell battery stats read - https://balancell.com/products/balancell-p26-solar/ (not all are readable)
* Time left on battery to 0 Ah as well as to current Minimum SoC (based on current battery load in Amps). For zero current or charging, it now shows a default time to go based on a very light 8 Amps load and the current SOC (so will show increase as charge progresses)
* Gauges tuned for severity colours
* Ellies Efergy Engage hub energy monitoring of grid power used at DB - https://engage.efergy.com/
* Tasmota firmware on Sonoff WiFi switch via MQTT showing power usage stats, and switch control
* Glances performance stats from one loacl server, and a remote VPS server
* Reolink WiFi IP cameras
* Apple iPhone and iPad status, location, etc
* Ambient weather station
* Weather Underground Personal Weather Station stats
* City of Cape Town load shedding
* Ring doorbell
* Google Cast for voice output alert notifications
* Asus router for alerting VoIP phone left network (battery is flat)
* UptimeRobot to monitor key websites
* AdGuard Home for performance of DNS
* Domain name certificate expiries
* OurGroceries shopping application
* Google Calendar showing next few days' schedule
* RSS feed showing Netflix titles leaving soon
* Mini graph showing actual solar charger yield comparted to weather station's actual measured radiation in W/m2, and adjusted for panel area and wattage. Also includes forecasted solar for the day, and the next day.
* Restrictions (locks) set with warnings before some switches are toggled on the dashboard
* Home Assistant Community Store (HACS) integrations and frontend UI
* HA running in a Docker container

## Automations
* Voice alerting to start of rain (to take washing off the line) - unfortunately weather station takes a few minutues to report
* Voice notification to home speaker for when I leave the shopping mall
* Slider to set minimum state of charge back to Victron CCGX ESS ie. adjust Victron system
* Ajust minimum SoC slider to match any changes made on Victron CCGX side ie. respond to changes made on Victron system remotely
* Alerts when hot water cylinder reached temperature and is ready, based on timers looking at grid power usage dropping after 15 minutes of high usage
* VoIP phone battery flat by checking when it's 'last_activity' is longer than 10 minutes ago
* Ham radio APRS beacon going offline (can't use left Zone as coordinates never change to elsewhere) so looks at a status change to 'Away' for longer than 31 minutes (still weaking this)
* Some audio message alerts for Victron system alarms
* Audible warning when AC load on the inverter exceeds 4,7 kWh
* Voice alerts for when grid power has been lost, as well as when it is back on (it's a must is South Africa, and with a solar system you don't always know if the grid is back on, or when it has gone off)

## Files
* File in docker sub-folder is the docker-compose file I used to create the Home Assistant container
* Other config files are inside the ha-configs subfolder for HA. All are as named. The lovelace file is a copy of the Lovelace dashboard UI config to see how the UI cards are configured, especially those with text replacement for numerical value data.

## ToDo Wishlist
* Tweak load shedding card to show attributes and better status
* Try link for click on Glances card to open full Glances web page
* Automations for motion detected on cameras
* Automation to adjust battery minimum SoC based on next day's weather forecast
* Get Ambient weather card to show rain in mm instead of inches
* Integration with Geyserwise hot water cylinder heating - no comms so may need a Geyserwise Max IoT device instead
* Integration with Texecom burglar alarm system if possible
* Our Groceries shopping lists are showing and working now, but filtering on specific lists seems broken upstream
* Tweak time left on batteries to rather use current AC inverter load, than the small default 8 Amps I had set it to now. This will be more realistic, based on the current loads on the inverter (not battery current)
* Possible time left to full charge gauge

## Time Left on Battery Calculations
Just for interest this is how I've done the basic calculation, based on the Balancell battery pack having a 206 Ah capacity at full ie. 100% SOC:
* Hours left = SOC% multiplied by 206 Ah and then divided by Battery Current in Amps (yes battery current varies but it recalculates on current usage)
  * (74% * 206 Ah) / 13 Amps = 11.72 hours
* Time left to Minimum SoC basically is the same except it subtracts the Min SOC from Current SOC to arrive at the difference left to reach Min SOC, so if Minimum SOC is say 40% then:
  * ((75%-40%) * 206 Ah) / 13 Amps = 5.38 hours
  * Added conditions to allow for when charging by showing an estimated time left with current SOC and a light discharge
  * Added condition for time to Min SOC to keep showing 0 hours if the SOC falls below Min SOC
Detail is in the sensors.yaml file with comments.

## Credits
Some resources I learnt a lot from include:
* Video using Modbus-TCP at https://youtu.be/giosYremoss
* Modbus guide on Victron site at https://community.victronenergy.com/questions/78971/home-assistant-modbus-integration-tutorial.html and mentions automating SOC according to weather
* Another Modbus guide inc Unit ID's = https://www.reddit.com/r/homeassistant/comments/nzimbj/tutorial_how_to_integrate_a_victron_inverter_into/
* Modbus code with Victron Github code at https://github.com/lucode/home-assistant
* MQTT guide at https://www.imval.tech/index.php/blog/victron-mqtt-server-bridging
* Victron CCGX Modbus TCP FAQ at https://www.victronenergy.com/live/ccgx:modbustcp_faq
* How to set up Automations - https://youtu.be/2tRZ_WA8Xyc
* Instructions for container use of HAC - https://hacs.xyz/docs/setup/download
* HAC Custom button possibility at https://github.com/custom-cards/button-card
* RGB colour chart - https://www.rapidtables.com/web/color/RGB_Color.html
* Restriction card confirmation at https://smarthomepursuits.com/how-to-create-a-lovelace-restriction-card-in-home-assistant/
* Home Assistant docker image at https://www.home-assistant.io/installation/linux#docker-compose
