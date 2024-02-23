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
