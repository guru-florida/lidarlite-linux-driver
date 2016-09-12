Linux IIO Driver for LidarLite by PulsedLight3D Inc.

See also https://github.com/PulsedLight3D/LIDARLite_Basics/blob/master/README.md

This driver is based on the IIO driver made by PulsedLight3D. Their driver was a set of patch files to Linux source so 
I simply extracted it and added Makefile and DeviceTree files so it can be built out of the Kernel source tree. On the
BeagleBoard this means a simple checkout and build step as opposed to downloading, building and installing a whole
kernel.

I've since learned the driver is already integrated into the BeagleBoard image. However, It doesnt auto detect the sensor
so I am not sure how to activate the built-in module. From the original driver code it seems to be missing some DeviceTree
configuration.

To compile you need to have at least the kernel headers pkg installed. You could also download the Kernel source into
the directory ~/src/linux and the Makefile will find it there. You will also need the dtc compiler:

	sudo apt-get install device-tree-compiler

To compile the driver and DeviceTree blob:

	make

To install the driver:

	sudo make install

To configure the driver to load at boot follow the instructions printed out in the 'make install' output. You can also
load the driver without a reboot by executing:

	sudo sh -c "echo lidarlite-bbb > /sys/devices/platform/bone_capemgr/slots"


After the driver is loaded, you should have an IIO node that you can read from in /sys/bus/iio/devices/. Use the following 
to find the device path: (There will probably only be one device)

	find -L /sys/bus/iio/devices/* -maxdepth 1 -name name -type f -exec grep -q lidar {} \; -print | xargs dirname

To enable the LidarLite sensor change directory to the /sys/bus/devices/iio:deviceN device path you found above, for example:

	cd /sys/bus/iio/devices/iio:device0
	echo 1 > scan_elements/in_distance_en 
	echo 1 > scan_elements/in_timestamp_en 		# optional

To read a value from the sensor:

	cat in_distance_raw 

