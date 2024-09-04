# Instructions for building and running the snap

## Building and installing the snap

Build the snap:

```
snapcraft
```

Install the snap:

```
sudo snap install --dangerous ./openvino_2024.3.0_amd64.snap
```

**Important note**: the size of the snap is several hundred MB due
to a dependency on pytorch.

## Running the applications

First connect the snap to the slots provided by the `intel-npu-driver` snap.
The `custom-device` interface provides access to the NPU device node(s):

```
sudo snap connect openvino:intel-npu intel-npu-driver:intel-npu
```

The `content` interface provides access to the NPU libs.

```
sudo snap connect openvino:npu-libs intel-npu-driver:npu-libs
```

If you have not done so already, ensure the following
are performed in order to set up non-root access to the
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

* [hello-npu-ipynb notebook](https://github.com/openvinotoolkit/openvino_notebooks/tree/ac3b192073ac553e16d1f7e4f3df46e9081653dd/notebooks/hello-npu)

There is a known issue in this notebook that causes the model compilation to fail.
See [this issue](https://github.com/openvinotoolkit/openvino_notebooks/issues/2167)
and the suggested workaround until the bug is fixed. The workaround can be deployed
after you have saved the model to OpenVINO IR format, and before you have compiled it
with `compiled_model = core.compile_model(model, device)`. You should re-run the cell
that saves or loads the model after patching the xml file.

### iPython

Since testing is being performed remotely it is convenient to test many of the
notebook cells from an iPython session rather than a Jupyter Notebook. To interact
with files in your working directory, first `cd` to a directory that is accessible
both inside and outside the snap.

```
cd /home/ubuntu/snap/openvino/current
openvino.ipython3
```

### Benchmark app

Again, start by `cd`'ing to a snap-accessible location.

```
cd /home/ubuntu/snap/openvino/current
openvino.benchmark-app
```

This is a C++ program that can be used to benchmark CPU versus NPU performance.

To run on a CPU:

```
openvino.benchmark-app -m model/ir_model/resnet50_fp16.xml -d CPU -hint latency
```

To run on a NPU:

```
openvino.benchmark-app -m model/ir_model/resnet50_fp16.xml -d NPU -hint latency
```

Note this app **cannot** be invoked from an iPython session unless you are running
in `devmode`:

```
# This only works if the snap is installed in dev mode!
!/snap/bin/openvino.benchmark-app...
```
