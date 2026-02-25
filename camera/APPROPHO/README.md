# Appropho camera driver

## Install
`sudo dpkg -i <debian file>`
- Please restart AIE510 after installing the camera debian file.

## Run GStreamer to display

### preview full-size video
`gst-launch-1.0 v4l2src device="/dev/<cameraID>" ! videoconvert ! xvimagesink`
`gst-launch-1.0 v4l2src device="/dev/<cameraID>" ! autovideoconvert ! xvimagesink`
### preview resized (640x480) video
`gst-launch-1.0 v4l2src device="/dev/<cameraID>" ! nvvidconv ! "video/x-raw(memory:NVMM),width=640,height=480" ! nvvidconv ! xvimagesink sync=false`

## debian file list
### appropho_1.0.0-Appropho-L4T36.4.3-L4T36.4.4_arm64.deb
- Supports L4T 36.4.3 or 36.4.4
- Supports MIO586-A2 camera board

### appropho_1.0.1-Appropho-L4T35.4.1_arm64.deb
- Supports L4T 35.4.1
- Supports MIO586-A2 camera board