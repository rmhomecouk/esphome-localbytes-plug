# About

This is an ESPHome firmware for the Localbytes smart plug, which is sold pre-loaded with tasmota. This firmware supports the original 10A plug and the upgraded 13A plug. <a href="https://www.mylocalbytes.com/products/smart-plug-pm?variant=41600621510847">You can buy these smart plugs here</a>.

## Manual Addition

You can use the following YAML to add the device to your ESPHome setup.  
Please note this does not enable [API Encryption](https://esphome.io/components/api#configuration-variables) or [OTA Password](https://esphome.io/components/ota.html#configuration-variables)

```yaml
substitutions:
  name: power-location-item
  friendly_name: Location Item Power
  area: Garage

  calibrated_c: "0.850"
  calibrated_p: "0.179"
  calibrated_v: "0.396"

  comment: "Calibrated: 2025-02-16 - c:${calibrated_c} p:${calibrated_p} v:${calibrated_v}"

  update_interval: 10s
  default_state: "ALWAYS_ON"

packages:
  localbytes.plug-pm: 
    url: https://github.com/rmhomecouk/esphome-localbytes-plug
    files: [localbytes-plug-pm.yaml]
    ref: main
    refresh: 1min

esphome:
  name: ${name}
  name_add_mac_suffix: false
  friendly_name: ${friendly_name}
  area: ${area}
  comment: ${comment}
  on_boot: 
    then:
      - lambda: |
          id(voltage_multiply) = ${calibrated_v};
          id(power_multiply) = ${calibrated_p};
          id(current_multiply) = ${calibrated_c};

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

# Flash manually first, uncomment web_server and flash wirelessly
#web_server:
 
 
```

## Original Credit

Massive thanks to @JamesSwift who made the original here - JamesSwift/localbytes-plug-pm  
Without whom, esphome would still be an unsupported platform

# Features

- Turn the switch on and off via home assistant or web interface
- Disabled the LED by pressing and holding the button for 3 seconds (or toggle the option in home assistant/web interface)
- Disable the physical button (i.e. child lock) by pressing and holding the button for 10 seconds (or toggle the option in home assistant/web interface)
- Power monitoring via home assistant or web interface
- Calibrate the power monitoring using a GUI via home assistant
- See and alter the calibration data via home assistant
- ESPHome dashboard import

<img src="https://user-images.githubusercontent.com/2080205/169600703-0ddfab3f-5309-4dd7-bd22-87a6d4d0ecb1.png" width="45%" /> 
<img src="https://user-images.githubusercontent.com/2080205/168430744-598f9d21-c1ce-4fce-9076-8fca26c62be8.png" width="45%" />
<img src="https://user-images.githubusercontent.com/2080205/168430637-ae9f14c6-57a8-4f3f-8a85-1f35966b43bd.png" width="45%" />




# Installation

To flash the ESPHome firmware over tasmota, first flash the <a href="https://github.com/LocalBytes/esphome-localbytes-plug/releases/latest/download/minimal.bin">ESPHome minimal</a> firmware using the tasmota web interface (as the full firmware is too big to fit in the free space left by tasmota). Then connect to the wifi hotspot that is created and enter your network's wifi details. 

At this point you can use the "dashboard import" feature of esphome to take ownership of the device. The next time you hit install/update via the dashboard, the full firmware will be uploaded to the plug. 

Alternatively, if you don't want to import the plug to your ESPHome dashboard, connect to the hotpsot the device creates and use the web UI to flash the <a href="https://github.com/LocalBytes/esphome-localbytes-plug/releases/latest/download/localbytes-plug-pm.bin">full firmware</a> from the latest release.

# Firmware File Too Big

[A minimal firmware](https://github.com/LocalBytes/esphome-localbytes-plug/releases) is provided as an intermiediary step, as there isn't always enough space on the factory smart plugs to store the new full firmware while it is being flashed.

If you're plug is currently running Tasmota, you can try flashing the <a href="http://ota.tasmota.com/tasmota/release/tasmota-minimal.bin.gz">Tasmota minimal</a> firmware instead. After which, you can flash the <a href="https://github.com/LocalBytes/esphome-localbytes-plug/releases/latest/download/localbytes-plug-pm.bin">full firmware</a>. **Do not try flashing Tasmota Minimal unless you already have Tasmota on the device.**

# Calibration

Once you have flashed the new firmware onto your smart plug and connected it to home assistant, you may wish to calibrate your plug to improve it's accuracy. To calibrate your plug, you need another "known-good" smart plug or a calibration device.

Plug your new smart plug into the known-good smart plug, then plug a kettle, toaster, or other high-power appliance into it. From home assistant, go to `Developer Tools` > `Services`. Use the services `calibrate_current`, `calibrate_power`, and `calibrate_voltage` to report the real readings as given from the "known-good" device. (Don't forget to turn on the kettle/toaster and leave it to stabilize it's power usage for a moment before starting to copy the readings).
