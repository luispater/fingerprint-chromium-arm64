# fingerprint-chromium-arm64

* This project is based on [fingerprint-chromium](https://github.com/adryfish/fingerprint-chromium) to fix arm64 Linux compilation issues.
* These are the modifications for [ungoogled-chromium-debian](https://github.com/ungoogled-software/ungoogled-chromium-debian) on Ubuntu 24.04.2 LTS aarch64, using [fingerprint-chromium](https://github.com/adryfish/fingerprint-chromium).

## Requirements
* Ubuntu 24.04.2 LTS aarch64
* rustc 1.78.0-nightly

## Building a binary package

```sh
# Install initial packages
sudo apt install -y devscripts equivs quilt rsync

# Clone ungoogled-chromium-debian repository
git clone https://github.com/ungoogled-software/ungoogled-chromium-debian.git
cd ungoogled-chromium-debian/convert

# Clone fingerprint-chromium-arm64 repository
git clone https://github.com/luispater/fingerprint-chromium-arm64 ungoogled-chromium
cd ungoogled-chromium
git checkout 135.0.7049.95

cd ..

wget "https://ftp.debian.org/debian/pool/main/c/chromium/chromium_135.0.7049.95.orig.tar.xz"
wget "https://ftp.debian.org/debian/pool/main/c/chromium/chromium_135.0.7049.95-1~deb12u1.debian.tar.xz"
wget "https://ftp.debian.org/debian/pool/main/c/chromium/chromium_135.0.7049.95-1~deb12u1.dsc" 

dpkg-source -x chromium_135.0.7049.95-1~deb12u1.dsc

# change the VERSION in Makefile line 7 to 135.0.7049.95
sed -i '7s/.*/VERSION      = 135.0.7049.95/' Makefile 

# run the Makefile
make

# Unpack the fingerprint-chromium source
dpkg-source -x ungoogled-chromium_135.0.7049.95-1~deb12u1.dsc

cd ungoogled-chromium-135.0.7049.95

# Install missing packages
sudo mk-build-deps -i debian/control
rm -f ungoogled-chromium-build-deps_*

# Install missing packages
sudo apt update
sudo apt install -y lld-19 clang-format-19 rustup bindgen pkgconf ninja-build elfutils flex yasm xvfb gperf bison nodejs node-rollup-plugin-terser rollup valgrind x11-apps xcb-proto xfonts-base libdav1d-dev libx11-xcb-dev libxshmfence-dev libgl-dev libglu1-mesa-dev libegl1-mesa-dev libgles2-mesa-dev libopenh264-dev generate-ninja mesa-common-dev rapidjson-dev libva-dev libxt-dev libgbm-dev libpng-dev libxss-dev libelf-dev libpci-dev libcap-dev libffi-dev libkrb5-dev libexif-dev libflac-dev libudev-dev libpipewire-0.3-dev libopus-dev libxtst-dev libjpeg-dev libxml2-dev libgtk-3-dev libxslt1-dev liblcms2-dev libpulse-dev libpam0g-dev libdouble-conversion-dev libxnvctrl-dev libglib2.0-dev libasound2-dev libsecret-1-dev libspeechd-dev libminizip-dev libhunspell-dev libharfbuzz-dev libxcb-dri3-dev libusb-1.0-0-dev libopenjp2-7-dev libmodpbase64-dev libnss3-dev libnspr4-dev libcups2-dev libevent-dev libevdev-dev libgcrypt20-dev libcurl4-openssl-dev libzstd-dev fonts-ipafont-gothic fonts-ipafont-mincho

# Install rust
rustup toolchain install nightly-2024-02-08
rustup default nightly-2024-02-08

# Build the package
dpkg-buildpackage -b -uc -d
```

## Attention
Please **DO NOT** attempt to compile on arm64 hardware such as a Raspberry Pi. With less than 16GB of RAM, the compile process may be terminated due to insufficient memory.

For reference, I used a virtual machine on my Mac mini with an M4 Pro chip, configured with 8 cores, 16GB of RAM, and a 100GB hard drive. Even with this setup, the compilation took approximately 6 hours. 

Therefore, **PLEASE BE PATIENT**.