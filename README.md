# goodix-ubuntu-fprint
Summary of instructions to enable the Goodix 27c6:55b4 Fingerprint Reader on Ubuntu 22.04.4 LTS

## Laptop Information
```bash
sudo dmidecode | grep -A 9 "System Information"
lsusb | grep "Goodix"
```
* Manufacturer: LENOVO
* Product Name: 82R9
* Version: IdeaPad Flex 5 14ALC7
* Fingerprint Reader: ID 27c6:55b4 Shenzhen Goodix Technology Co.,Ltd. Fingerprint Reader

## OS Information
```bash
lsb_release -a
```
* Distributor ID:	Ubuntu
* Description:	Ubuntu 22.04.4 LTS
* Release:	22.04
* Codename:	jammy

## Procedure
* It is known that the libfprint library does not support various Goodix fingerprint readers
    (see https://fprint.freedesktop.org/supported-devices.html for supported devices)

* The following steps worked for me on my system.

### Flash Firmware of Fingerprint Device
* Follow instructions of README @ https://github.com/goodix-fp-linux-dev/goodix-fp-dump (USE AT OWN RISK)

### Install libfprint library

#### Setup
```bash
# Clone the experimental repository for this device
git clone https://github.com/TheWeirdDev/libfprint/tree/55b4-experimental

# enter repo and setup virtual python environment
cd libfprint
python3 -m venv .venv
source .venv/bin/activate
pip3 install meson ninja gobject python-dbusmock
```

#### Install dependencies
```bash
sudo apt install -y \
    gtk-doc-tools \
    libgirepository1.0-dev \
    libgusb-dev \
    libpam-wrapper \
    libpam0g-dev \
    libpamtest0-dev \
    libpolkit-gobject-1-dev \
    libxml2-utils \
    python3-pip \
    python3-pypamtest \
    libopencv-dev \
    openssl \
    cmake
```

#### Build and Install libfprint
```bash
meson setup build && cd build
meson compile
meson install
```
* If prompt for needing elevated priveleges comes up, press 'y' key to allow.
* For some reason, the build step failed for me due to the missing "doctest" dependency. Follow steps below to fix this before running the above commands again. If there is a way to build without needing doctest, let me know.

### Install doctest
```bash
# Clone into new directory
git clone https://github.com/doctest/doctest
cd doctest
mkdir -pv build && cd build
cmake ../
cmake --build .
cmake --install .
```

### Register Fingerprint
* Follow instructions @ https://www.malibal.com/guides/how-to-enable-fingerprint-login-on-ubuntu/

