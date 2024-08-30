# NPU-enabled snaps

This directory contains reference snaps for building applications
that can leverage the Intel NPU. Building the snaps is resource intensive,
so it is recommended to do so on a machine with a lot of cores and at least
32 GB of RAM (16 GB will fail without a patch for the NPU UMD build configuration).

Note that these snaps are currently meant to be a reference for how to build
and integrate the NPU software stack from a snap. Long term we may distribute
the NPU software stack as a snap extension or through the content interface
(or some other method).
