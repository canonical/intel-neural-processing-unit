# Instructions for building and running the snap

## Building the snap

**Important note**: run `snapcraft` in the current directory
in order for the install hook to get integrated with the
snap correctly.

Build the snap:

```
snapcraft
```

Install the snap (use `--devmode` if testing without NPU snap interface):

```
sudo snap install --dangerous ./vpu-umd-test_1.5.1_amd64.snap
```

Note that this triggers an install hook that copies the firmware
binary blobs to /var/snap/vpu-umd-test/current so that they
are accessible to the kernel driver running on the host.

## Loading new VPU firmware

Now connect the snap to the `kernel-firmware-control` snap interface,
which allows the snap to access a file in /sys for customizing the
kernel's firmware search path:

```
sudo snap connect vpu-umd-test:kernel-firmware-control
```

Now run the following to customize the search path:

```
sudo vpu-umd-test.set-firmware-path 
```

The expected output is something like the following:

```
Firmware search path is set to: /var/snap/vpu-umd-test/x1
```

Now reload the `intel_vpu` kernel module to load the updated firmware:

```
sudo rmmod intel_vpu 
sudo modprobe intel_vpu
```

Verify it's running and loaded correctly with:

```
lsmod | grep intel_vpu
sudo dmesg | grep intel_vpu
```

TODO: verify firmware is the expected version.

## Running the vpu-umd-test application

TODO: connect snap to custom NPU snap interface

If you have not done so already, ensure the following
are performed in oder to set up non-root access to the
NPU device:

```
sudo usermod -a -G render $USER # log out and log back in
```

If this is your first run, or if you re-loaded the `intel_vpu` driver,
then you'll also need to perform the following:

```
sudo chown root:render /dev/accel/accel0
sudo chmod g+rw /dev/accel/accel0
```

Create input for tests:

```
mkdir -p models/add_abc
curl -o models/add_abc/add_abc.xml https://raw.githubusercontent.com/openvinotoolkit/openvino/master/src/core/tests/models/ir/add_abc.xml
touch models/add_abc/add_abc.bin
curl -o basic.yaml https://raw.githubusercontent.com/intel/linux-npu-driver/v1.5.1/validation/umd-test/configs/basic.yaml
```

Finally run the application:

```
vpu-umd-test --config=basic.yaml
```
