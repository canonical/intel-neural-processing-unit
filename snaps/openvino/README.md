# OpenVINO

There are two methods for building the openvino snap:

- **standalone**: The supporting NPU libraries are built and included directly in the openvino snap
- **consumer**: The supporting NPU libraries are built in a separate provider snap (`intel-npu-driver`). The openvino snap then accesses these libs over the `content` snap interface
