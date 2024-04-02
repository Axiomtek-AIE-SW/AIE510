# oToCAM camera driver

## Install
`sudo dpkg -i <debian file>`
- Please restart AIE510 after installing the camera debian file.

## Run GStreamer to display

### preview full-size video
`gst-launch-1.0 v4l2src device="/dev/<cameraID>" ! videoconvert ! xvimagesink`
`gst-launch-1.0 v4l2src device="/dev/<cameraID>" ! autovideoconvert ! xvimagesink`
### preview resized (640x480) video
`gst-launch-1.0 v4l2src device="/dev/<cameraID>" ! nvvidconv ! "video/x-raw(memory:NVMM),width=640,height=480" ! nvvidconv ! xvimagesink sync=false`
