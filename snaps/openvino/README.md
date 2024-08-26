# Instructions for building and running the snap

## Building the snap

**Important note**: run `snapcraft` in the current directory
in order for the install hook to get integrated with the
snap correctly.

Build the snap:

```
snapcraft
```

```
sudo snap install --dangerous ./openvino_2024.3.0_amd64.snap
```

Note that this triggers an install hook that copies the firmware
binary blobs to /var/snap/openvino/current so that they
are accessible to the kernel driver running on the host.

**Important note**: the size of the snap is several hundred MB due
to a dependency on pytorch.

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

## Running the applications

First connect the snap to the `custom-device` interface, which enables
access to the NPU device node on the host:

```
sudo snap connect openvino:intel-npu-plug openvino:intel-npu-slot
```

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

The example applications below are derived from a Jupyter notebook maintained by Intel:

* [hello-npu-ipynb notebook](https://github.com/openvinotoolkit/openvino_notebooks/tree/latest/notebooks/hello-npu)

There is a known issue in this notebook that causes the model compilation to fail.
See [this issue](https://github.com/openvinotoolkit/openvino_notebooks/issues/2167)
and the suggested workaround until the bug is fixed. The workaround can be deployed
after you have saved the model to OpenVINO IR format, and before you have compiled it
with `compiled_model = core.compile_model(model, device)`. You should re-run the cell
that saves or loads the model after patching the xml file.

Additionally, because the app does not have permissions to access a user's home
directory, a different model path must be used compared to the one used in
the upstream Intel Jupyter notebook. A good choice for the model path is
`/home/ubuntu/snaps/openvino/current` as this is accessible both inside and outside
the snap.

### iPython

Since testing is being performed remotely it is convenient to test many of the
notebook cells from an iPython session rather than a Jupyter Notebook.

```
openvino.ipython3
```

### Benchmark app

```
openvino.benchmark-app
```

This is a C++ program that can be used to benchmark CPU versus NPU performance.

To run on a CPU:

```
openvino.benchmark-app -m $model_path -d CPU -hint latency
```

To run on a CPU:

```
openvino.benchmark-app -m $model_path -d NPU -hint latency
```

Note this app **cannot** be invoked from an iPython session unless you are running
in `devmode`:

```
# This only works if the snap is installed in dev mode!
!/snap/bin/openvino.benchmark-app...
```