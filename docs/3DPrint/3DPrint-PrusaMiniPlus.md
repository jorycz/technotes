# 3D Print Prusa Mini+

## Fix Z aixs on Prusa Mini+ if selftest fails

* Insert USB Flash
* Enable `logging` to USB Flash
* Run `selftest` of aixs Z
* When finished, disable `logging`
* Unplug USB Flash, connect to computer and read length of axis Z from log

On Display menu, go to `Settings` and then `Press and Hold the control button` to enter `Experimental` menu. Then run `selftest` again.

[Experimental Settings on Prusa website](https://help.prusa3d.com/article/experimental-settings-mk4-s-mk3-9-s-mk3-5-s-xl-mini_264396)
