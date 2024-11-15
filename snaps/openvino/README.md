# OpenVINO

There are three methods provided here for building the openvino snap. 

## Snaps for `intel-npu-driver` development validation

The first two were created during development of a `intel-npu-driver` content producer snap:

- **standalone**: The supporting NPU libraries and snap interfaces are built and included directly in the openvino snap
- **consumer-intel-npu-driver**: The supporting NPU libraries and snap interfaces are built in a separate provider snap (`intel-npu-driver`). The openvino snap then accesses these libs over the `content` snap interface, and connects to the `intel-npu` slot provided by `intel-npu-driver` to access the NPU device node on the host

## Snap for `openvino-toolkit-2404` development validation

The third method is much more feature-complete than the first two. It consumes content provided by the `intel-npu-driver` and `openvino-toolkit-2404` content producer snaps, includes apps for validating the OpenVINO Python API using either `ipython` or `jupyter`, and also includes Intel GPU support (in addition to NPU support):

- **consumer-intel-npu-driver-and-openvino-toolkit-2404**
