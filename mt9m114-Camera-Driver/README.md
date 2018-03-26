# mt9m114 Camera Driver

## Settings 
The provided camera driver from Aldebaran/SoftBank Robotics is lacking in auto exposure and image adjustment settings that are available on the camera chip onboard the Nao V4 and V5 robots.

By expanding upon the work done by [NaoDevils](https://github.com/NaoDevils/NaoKernel) to produce an expanded driver, a new camera driver has been written by rUNSWift to take advantage of key settings available on the sensor chip. The expanded settings have focused around the issue of natural and dynamic lighting changes that could occur during an SPL competition match.

**Documentation** 
* [MT9M114 camera product information](https://www.onsemi.com/pub/Collateral/MT9M114-D.PDF)
* [Similar camera full developers guide](http://www.onsemi.com/pub/Collateral/AND9270-D.PDF)

| Feature | ioctl | Range | Notes |
|---------|-------|-------|-------|
| auto exposure | V4L2_CID_EXPOSURE_AUTO | min: 0 max: 3 default: 1 | 0: Manual exposure mode 1: Auto exposure mode 2: Shutter priority mode (AE is disabled) 3: Aperture priority mode (AE is enabled) | Disabling AE will write the used exposure and contrast back, so that they can be read by the corresponding ioctls |
|auto white balance|V4L2_CID_AUTO_WHITE_BALANCE|min: 0 max: 1 default: 1 | 0: disable, 1: enable |Disabling AWB will write the used white balance temperature back, so that it can be read by the corresponding ioctl |
|backlight compensation|V4L2_CID_BACKLIGHT_COMPENSATION|min: 0 max: 4 default: 1|Only changeable while ae is enabled!|
|brightness|V4L2_CID_BRIGHTNESS|min: 0 max: 255 default: 55|Only changeable while ae is enabled!|
|contrast|V4L2_CID_CONTRAST|min: 16 max: 64 default: 32|Gradient of the contrast adjustment curve from 0.5 (16) to 2.0 (64)|
|do an automatic white balance|V4L2_CID_DO_WHITE_BALANCE|min: 0 max: 1 default: 0|Does an automatic white balance. The new white balance can be read from the white balance temperature. This ioctl is a button, thus it will always return 0! **Be aware** that Aldebaran uses this ioctl for setting the white balance temperature.|
|exposure time|V4L2_CID_EXPOSURE|min: 1 max: 1000 default: 1|The exposure can only be changed while AE is disabled! A value on one is 100 microseconds of exposure|
|exposure algorithm|V4L2_CID_EXPOSURE_ALGORITHM|min: 0 max: 3 default: 1|0: Average scene brightness 1: Weighted average scene brightness 2: Evaluated average scene brightness with frontlight detection (exposing for highlights) 3: Evaluated average scene brightness with backlight detection (exposing for lowlights)|
|gain|V4L2_CID_GAIN|min: 0 max: 255 default: 32|Only changeable while AE is disabled! A value of 32 equals to 1.0 gain|
|gamma correction|V4L2_CID_GAMMA|min: 0 max: 1000 default: 220|The gamma correction for the display in units multiplied by 100. A value of 220 equals to 2.2 gamma|
|hue|V4L2_CID_HUE|min: -22 max: 22 default: 0|Hue correction in degrees|
|power line frequency|V4L2_CID_POWER_LINE_FREQUENCY|min: 1 max: 2 default: 2|1: 50Hz, 2: 60Hz The frequency of the local power line so AE can avoid flicker|
|saturation|V4L2_CID_SATURATION|min: 0 max: 255 default: 128|Saturation control for the image. A value of zero results in grayscaled images while values above 128 result in over saturated images|
|sharpness|V4L2_CID_SHARPNESS|min: 0 max: 255 default: 128|Sharpness values are limited from 0 to +7. When setting the value to -7 no sharpness will be applied|
|white balance temperature|V4L2_CID_WHITE_BALANCE_TEMPERATURE|min: 2700 max: 6500 default: 6500|The white balance temperature in kelvin. **Be aware** that Aldebaran uses another ioctl for this feature!|
|vertical flip|V4L2_CID_VFLIP|min: 0 max: 1 default: 0| |	
|horizontal flip|V4L2_CID_HFLIP|min: 0 max: 1 default: 0| |	
|fade to black|V4L2_MT9M114_FADE_TO_BLACK (V4L2_CID_PRIVATE_BASE)|min: 0 max: 1 default: 1|0: disable, 1: enable When enabled the image will fade to black under very low light conditions|
|target average luma|V4L2_MT9M114_AE_TARGET_AVERAGE_LUMA (V4L2_CID_PRIVATE_BASE+1)|min: 0 max: 255 default: 55| The brightness target to be maintained by the auto exposure for bright lighting conditions|
|target average luma dark|V4L2_MT9M114_AE_TARGET_AVERAGE_LUMA_DARK (V4L2_CID_PRIVATE_BASE+2)|min: 0 max: 255 default: 27|The brightness target to be maintained by the auto exposure for dark lighting conditions|
|target analog gain|V4L2_MT9M114_AE_TARGET_GAIN (V4L2_CID_PRIVATE_BASE+3)|min: 0 max: 65535 default: 128| The maximum analog gain before reducing the frame rate|
|min. digital gain|V4L2_MT9M114_AE_MIN_VIRT_DGAIN (V4L2_CID_PRIVATE_BASE+4)|min: 0 max: 65535 default: 32|	The minimum digital gain that the auto exposure is allowed to use. Increasing this value carefully results in shorter exposure times|
|max. digital gain|V4L2_MT9M114_AE_MAX_VIRT_DGAIN (V4L2_CID_PRIVATE_BASE+5)|min: 0 max: 65535 default: 256| The maximum digital gain that the auto exposure is allowed to use|
|min. analog gain|V4L2_MT9M114_AE_MIN_VIRT_AGAIN (V4L2_CID_PRIVATE_BASE+6)|min: 0 max: 65535 default: 32|	The minimum analog gain that the auto exposure is allowed to use. Increasing this value carefully results in shorter exposure times|
|max. analog gain|V4L2_MT9M114_AE_MAX_VIRT_AGAIN (V4L2_CID_PRIVATE_BASE+7)|min: 0 max: 65535 default: 256| The maximum analog gain that the auto exposure is allowed to use|
|weighted brightness|V4L2_MT9M114_AE_WEIGHT_TABLE_0_0 ... V4L2_MT9M114_AE_WEIGHT_TABLE_4_4 (V4L2_CID_PRIVATE_BASE+8...+32)|min: 0 max: 100|Requires V4L2_CID_EXPOSURE_ALGORITHM=1! The weight of every 5x5 area to be used for calculating the average brightness of the current scene.|


Features to be potentially added:
* Variables to set what is considered as bright and dark lighting conditions for "target average luma" and "target average luma dark"
* Gamma correction

## Building the Camera Driver
This guide details how to go about building the mt9m114 camera driver from source to produce a binary .ko file, and then transferring it to the Nao.

[Aldebaran's guide](http://doc.aldebaran.com/2-1/dev/tools/vm-setup.html) on setting up and using the NaoOS VM.

1. Install Virtual Box VM
2. Download the latest NaoOS image for Virtual Box from Aldebaran/SBR and import into Virtual Box
3. Pull a kernel config file from an current Nao using for eg. `scp nao@<robot_name>:/proc/config.gz /your/file/location/`
4. Extract (as `config`) and open the file and check the kernel version listed near the top. Do not edit this file.
5. Download the correct kernel [from Aldebaran](https://github.com/aldebaran/linux-aldebaran/tree/release-2.0/atom) using the branch selector drop down.
6. Add the new driver files to be built into the kernel. For camera the files would go in `drivers/media/video`, overwriting any old files.
7. Add the config file taken from the Nao to the home dir of the kernel, overwriting any old file.
8. Transfer the entire kernel to the VM (while the VM is running). Check Aldebaran's guide above for more help on transfering to the VM.
9. In the NaoOS VM login as `root` Password: `root`
10. Rename the config file to `.config`. Renaming in the VM seems to protect from a corruption issue when you rename it externally.
11. Run `make ARCH=i386` from the kernel home dir.
12. Once completed, pull the binary .ko file from the VM from this location
`/linux-aldebaran-release-2.0-atom/drivers/media/video/mt9m114.ko`
13. Now the binary file can be loaded onto the robot in `lib/modules/2.6.../kernel/drivers/media/video` replacing the old camera driver file.
14. Power restart the robot.

## Thanks
Thanks goes to [Nao Devils](http://naodevils.de/) and [B-Human](https://www.b-human.de/index.html) for their work on the previous iterations of this camera driver that made it possible to expand further.
