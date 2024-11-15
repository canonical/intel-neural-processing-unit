# Snap for Testing the OpenVINO Python API

This is a reference snap designed to validate the `openvino-toolkit-2404` content producer snap. It includes CPU, GPU, and NPU support.

## Installation and setup

Build the snap:

```
snapcraft
```

Install the snap:

```
sudo snap install --dangerous ./openvino-python_2024.4.0_amd64.snap
```

Install content provider snaps to consume from via content interfaces. Since `openvino-toolkit-2404` is not yet in the Snap Store, you first need to build it locally (you can clone [this repo](https://github.com/kenvandine/openvino-toolkit-snap) and run `snapcraft` to build it).

```
sudo snap install --beta intel-npu-driver
sudo snap install --dangerous ~/openvino-toolkit-2404_2024.4.0_amd64.snap
```

Connect interfaces:

```
sudo snap connect openvino-python:intel-npu intel-npu-driver:intel-npu
sudo snap connect openvino-python:npu-libs intel-npu-driver:npu-libs
sudo snap connect openvino-python:openvino-libs openvino-toolkit-2404:openvino-libs
sudo snap connect openvino-python:opengl
sudo snap connect openvino-python:home
sudo snap connect openvino-python:network
sudo snap connect openvino-python:network-bind
sudo snap connect openvino-python:hugepages-control
```

## Running apps

### iPython

```
openvino-python.ipython
```

### Jupyter Lab

```
openvino-python.jupyter lab --notebook-dir=/home/ubuntu/tmp --ip=0.0.0.0 --no-browser --allow-root --port=8888 --ServerApp.token='' --ServerApp.password='' --ServerApp.allow_origin='*' --ServerApp.allow_remote_access=True --ServerApp.authenticate_prometheus=False --ServerApp.base_url='/'
```

Note this assumes you are running remotely, in which case you will need to open a tunnel and bind to port 8888 from your local machine. For example:

```
ssh remote-machine -L 8888:127.0.0.1:8888
```

Then navigate to http://localhost:8888 on a web browser to access Jupyter Lab.
