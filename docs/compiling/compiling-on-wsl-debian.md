# OTClient Redemption

## Supported OS

- Windows 11 + WSL2 with Debian

## 1. Install the required software

The following command will install Git, CMake, a compiler and the libraries used by OTClient Redemption.

Git will be used to download the source code, and CMake will be used to generate the build files.

```bash
sudo apt update
sudo apt install git cmake build-essential autoconf libtool ca-certificates curl zip unzip tar pkg-config ninja-build libglew-dev libx11-dev ccache linux-headers-amd64 xorg-dev -y
```

- Update gcc

- Add isolated testing repository

```bash
sudo nano /etc/apt/sources.list.d/testing.list
deb http://deb.debian.org/debian testing main contrib non-free
```

- Pinning (prevents system upgrade)

```bash
sudo nano /etc/apt/preferences.d/testing
```

```bash
Package: *
Pin: release a=testing
Pin-Priority: 100
```

- Update package index

```bash
sudo apt update
```

- Install GCC 14

```bash
sudo apt install -t testing gcc-14 g++-14
```

- Update Alternatives

```bash
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-14 14
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-14 14
```

- Verify installation

```bash
gcc-14 --version
g++-14 --version
```

## 2. Set up vcpkg

```bash
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
cd ..
```

### Configure environment for vcpkg (important)

To ensure **CMake presets and CLion detect vcpkg automatically without modifying the preset**, export `VCPKG_ROOT` globally for the user:

```bash
mkdir -p ~/.config/environment.d
echo 'VCPKG_ROOT=$HOME/vcpkg' > ~/.config/environment.d/10-vcpkg.conf
```

Note: This approach requires systemd. If `systemctl status` shows "unit not found", enable systemd by editing `/etc/wsl.conf` (e.g., `sudo nano /etc/wsl.conf`) and adding:

```ini
[boot]
systemd=true
```

Then run `wsl --shutdown` and restart WSL.

Fallback (for non-systemd setups):
```bash
echo 'export VCPKG_ROOT=$HOME/vcpkg' >> ~/.bashrc
source ~/.bashrc
```

Now **log out and log in again** (or reboot), then verify:

```bash
echo $VCPKG_ROOT
# Expected output:
# /home/<user>/vcpkg
```

Also validate the toolchain file exists:

```bash
test -f "$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake" && echo "vcpkg OK" || echo "vcpkg not found"
```

## 3. Download the source code

```bash
git clone --depth 1 https://github.com/opentibiabr/otclient.git
cd otclient
```

## 4. Folder structure

```bash
.
├── otclient
└── vcpkg
```

## 5. Configure and build

```bash
cmake --preset linux-release -DTOGGLE_BIN_FOLDER=ON
cmake --build --preset linux-release -j4
```

> -- Running vcpkg install

**This step will take a long time on the first run, as it needs to download and install all the dependencies, so be patient!**

---
