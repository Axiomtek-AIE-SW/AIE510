# Introduction

This is sample code for Axiomtek AIE510

# Image Flash

### **Before flashing image, you MUST prepare a Linux host system running x86_64 Ubuntu 18.04 or later.**
- **Step 1. Set up the AIE510 connection as follows:**
    * Take off the Maintenance cover.
    * Connect a USB cable from the Linux host system to the USB Type A port on AIE510, and switch the recovery switch to ON.
    * onnect an HDMI monitor to AIE510.

- **Step 2. Open the terminal at host system, and change the path to the image file directory for example  "~/Downloads" with following commands to check image tarball data integrity first.**

    > ```
    > $ cd ~/Downloads
    > $ md5sum -c <image_tarball_file_name>.tbz2.md5sum
    > ```

    #### \[Command example\]:
    > ```
    > $ md5sum -c mfi_jetson-orin-nx-16GB-JP5.1.2-AIE510-STD-V1.0.0.tbz2.md5sum
    > ```

- **Step 3. If above command returns OK, Untar the image file with the command below:**

    > ```
    > $ sudo tar jxvf <image_tarball_file_name>.tbz2
    > ```

    #### \[Command example\]:
    > ```
    > $ tar jxvf mfi_jetson-orin-nx-16GB-JP5.1.2-AIE510-STD-V1.0.0.tbz2
    > ```

- **Step 4. Change the directory to the image package folder with the command below:**

    > ```
    > $ cd <image_file_name>
    > ```

    #### \[Command example\]:
    > ```
    > $ cd mfi_jetson-orin-nx-16GB-JP5.1.2-AIE510-STD-V1.0.0
    > ```

- **Step 5. Make sure the recovery switch(SW5) has been switched to ON, and run the command lsusb, then the command line "0955:7323 NVidia Corp." should be listed.**

    > ```
    > $ lsusb
    > <Note>:
    >   - "0955:7323 NVidia Corp." for Orin NX 16GB
    >   - "0955:7523 NVidia Corp." for Orin NANO 8GB
    > ```

- **Step 6. Running the following command to flash the image.**

    > ```
    > $ sudo ./tools/kernel_flash/l4t_initrd_flash.sh --flash-only --massflash 5 --showlogs
    > ```

- **Step 7. The flashing procedure takes approximately 8 minutes or more. Once finished, and AIE510-ONX will automatically reboot, and please switch the recovery switch(SW5) to OFF to return to standard mode..**

    > ```
    > <Note>:
    >   THE DEFAULT LOGIN CREDENTIALS:
    >     - Username: nvidia
    >     - Password: nvidia
    > ```

