# Razer Blade Field Configuration
Configuring the Razer Blade 15 laptop for field DNA sequencing and data analysis:

## Step 1: Replace the original operating system with Ubuntu 22.04LTS
A linux based system is necessary to conduct bioinformatics analysis, and Ubuntu is the only distribution where ONT's MinKNOW is currently supported.
Detailed instructions are available in: [https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview)
Note: At step 4, to boot from the USB drive, pressing F12 will open the boot menu, however, this laptop model boots up very quickly so you need to start repeatedly pressing the F12 key right after powering on the machine.
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

## Step 4: Install MinKNOW
