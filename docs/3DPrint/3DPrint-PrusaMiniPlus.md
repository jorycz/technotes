# 3D Print Prusa Mini+

## Fix Z aixs on Prusa Mini+ if selftest fails

* Insert USB Flash
* Enable `logging` to USB Flash
* Run `selftest` of aixs Z
* When finished, disable `logging`
* Unplug USB Flash, connect to computer and read length of axis Z from log

On Display menu, go to `Settings` and then `Press and Hold the control button` to enter `Experimental` menu. Then run `selftest` again.

[Experimental Settings on Prusa website](https://help.prusa3d.com/article/experimental-settings-mk4-s-mk3-9-s-mk3-5-s-xl-mini_264396)

### For my Mini+ I need to adjust setup in the `Experimental` menu

* Z-axis length: 190
* Extruder steps: 415

## Integrace Camera to Prusa Connect

I bought ESP32-CAM module with MB board so I get PSRAM and also connector which is used for programming (microUSB in this case). USB-C version is also available but lack PSRAM from what I found on the internet. I flashed [this jerzeek's firmware](https://jerzeek.github.io/PrintCam-Firmware/) and set up following settings:

* Frame rate: 100 ms (10.0 f/s)
* Startup delay: 15
* Board: AI THINKER
* Frame size: SXGA (1280x1024)
* JPG quality: 14
* Enable PSRAM if available: Checked
* Buffers: 2
* Brightness: 1
* Contrast: 1    
* Saturation: 0
* Refresh frequency: 20 s

To setup Refresh frequency to 20 second just change values in http form before submit. In Safari you can do that in JavaScript Console using this code:

    Array.from(document.getElementsByTagName("option"))
    .forEach(o => {
        if (o.value === "10" || o.textContent.trim() === "10 s") {
        o.value = "20";
        o.textContent = "20 s";
        }
    });

To add Camera to Prusa Connect you need **fingerprint** and camera **token**. Fingerprint is your password to PrusaLink and token is generated when you add new camera in Prusa Connect.

For Camera case & mount I remixed model where I added vent holes and larger hole to support of wide lenses. You can download it [on printables](https://www.printables.com/model/1399104-esp32-cam-case-with-mount-for-prusa-minimini)

To enable / disable LED on ESP32 use

    curl -u admin:<YOUR ESP32 PASSWORD> http://<YOUR ESP32 IP>/flash?v=200

* `v=0` - Disable LED
* `v=1` - Enable LED (intensity could be 1 - 250)
