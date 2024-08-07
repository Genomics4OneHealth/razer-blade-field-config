# Razer Blade Field Configuration
Configuring the Razer Blade 15 laptop for field DNA sequencing and data analysis:
## July 2024 update:
The Ubuntu installation broke, as Ubuntu installations usually do, so I installed Linux Mint this time, available [here](https://www.linuxmint.com/download_all.php). Mint is derived from Ubuntu but it's more stable, so it should work better on the long term.
**Important: as of now ONT hasn't made a MinKNOW version for Ubuntu 24, the latest version. Mint's latest version (22) is based on Ubuntu 24, so MinKNOW is not installable in those versions because of some dumb proprietary ONT packages. So, as of the time of writing, it's necessary to install Mint version 21.3.**
The rest of the installation instructions are the same since Mint uses the same repositories as Ubuntu. 

## Step 1: Replace the original operating system with Ubuntu 22.04LTS
A linux based system is necessary to conduct bioinformatics analysis, and Ubuntu is the only distribution where ONT's MinKNOW is currently supported.
Detailed instructions are available in: [https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview)

Note: At step 4, to boot from the USB drive, pressing F12 will open the boot menu, however, this laptop model boots up very quickly, so you need to start repeatedly pressing the F12 key right after powering on the machine.
If the USB drive is not visible from the boot menu it will be necessary to restart, access the BIOS by pressing F1 right after booting, navigate to the Security tab, down to secure boot, and disabling it.

At step 5 of the installation select the Normal installation option and at step 6 select "Erase disk and install Ubuntu"

Proceed with preferred options until step 12.

## Step 2: CUDA and drivers installation
First open the terminal either by clicking in the icon or pressing ctrl+shift+T. It is necessary to install the aptitude package manager to solve package conflicts. Type:
```bash
sudo apt install aptitude
```
and press enter. You'll be prompted to enter the password you had chosen during the installation.
After aptitude is installed we'll use it to check if all necessary dependencies are already installed, and install them otherwise:
```bash
sudo aptitude install  gcc curl dkms dirmngr ca-certificates build-essential software-properties-common apt-transport-https  
```
Now we need to import the NVIDIA package archive into the system to install the drivers. The first command imports the GPG key and the second imports the actual repository:
```bash
curl -fSsL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/3bf863cc.pub | sudo gpg --dearmor | sudo tee /usr/share/keyrings/nvidia-drivers.gpg > /dev/null 2>&1
```
```bash
echo 'deb [signed-by=/usr/share/keyrings/nvidia-drivers.gpg] https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/ /' | sudo tee /etc/apt/sources.list.d/nvidia-drivers.list
```
Update the package list to include the newly added ones:
```bash
sudo aptitude update
```
To install the drivers (we'll be using the newest version currently):
```bash
sudo aptitude install nvidia-driver-545 cuda-drivers-545 cuda
```
proceed with the installation and pay attention to the output, if aptitude can't install all packages due to version conflicts tell it to try again even if it has to downgrade some packages.

## Step 3: Disable Dynamic Display Switch

After installing the drivers restart the computer, and access the BIOS by quickly pressing F1 right after booting. Navigate to the Chipset tab, down to "GPU MODE", and select "Dedicated GPU only".

This is necessary because MinKNOW won't be able to basecall with Dynamic Display Switch active. This also shortens battery life, so, if the computer needs to be active without charging for a long time and won't be used for basecalling (or for other CUDA based machine learning programs), I recommend leaving DDS active.  

## Step 4: Install MinKNOW

More details for the MinKNOW installation can be found at ONT's website, but you can install it running the following commands line by line:

Install wget, import the security key and ONT's package repository:
```bash
sudo apt update
sudo apt install wget
wget -O- https://cdn.oxfordnanoportal.com/apt/ont-repo.pub | sudo apt-key add -
echo "deb http://cdn.oxfordnanoportal.com/apt focal-stable non-free" | sudo tee /etc/apt/sources.list.d/nanoporetech.sources.list
```
Install MinKNOW:
```bash
sudo apt update
sudo apt install ont-standalone-minknow-gpu-release
```
## Possible problems:

### Not connecting properly to sequencers:
If MinKNOW can't connect to the sequencers it will say there are connection problems or will throw a "Unable to find a hardware check script to start" error. This most likely is a permission problem, and can be checked with:
```bash
sudo systemctl status minknow.service
```
the output should say that libusb "couldn't open USB device" and "requires write access to USB service nodes". If that's the case then the problem can be fixed by editing ```/lib/systemd/system/minknow.service``` and changing the "group" and "user" fields from "minknow" to "root".
Afterwards it's necesary to either reboot or reload the daemon and restart the minknow service:
```bash
sudo systemctl daemon-reload && sudo systemctl restart minknow.service
```

### System not detecting WiFi card
This is a rare problem but, since we must use an older OS version, there is the possibility the the system won't detect the Intel wireless card that ships with the laptop. Usually this can be solved by explicitly installing the HWE drivers:
```bash
sudo aptitude update && sudo aptitude install linux-generic-hwe-22.04
```
