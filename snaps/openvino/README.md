# OpenVINO

There are two methods for building the openvino snap:

- **standalone**: The supporting NPU libraries and snap interfaces are built and included directly in the openvino snap
- **consumer**: The supporting NPU libraries and snap interfaces are built in a separate provider snap (`intel-npu-driver`). The openvino snap then accesses these libs over the `content` snap interface, and connects to the `intel-npu` slot provided by `intel-npu-driver` to access the NPU device node on the host
