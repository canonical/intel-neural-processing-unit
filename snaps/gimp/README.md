# Instructions for building and running the snap

**Important**: this snap was developed for POC purposes only and is not intended for production use. There are many "hacky" solutions used to get the snap running on the NPU properly, and ultimately we'll need better approaches for the real packaging work. The `snapcraft.yaml` is committed here to preserve the history of the POC.

## Building and installing the snap

Build the snap:

```
snapcraft
```

Before installing the snap, make sure to install the latest `intel-npu-driver` snap from the store:

```
sudo snap install --beta intel-npu-driver
```

Install the snap:

```
sudo snap install --dangerous ./gimp_2.99.19-dev_amd64.snap
```

**Important note**: the size of the snap is multiple GB because for now the models are being built into the snap.

## Running the applications

The `content` interface provides access to the NPU libs.

```
sudo snap connect gimp:npu-libs intel-npu-driver:npu-libs
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

Now you are ready to launch GIMP:

```
gimp
```
