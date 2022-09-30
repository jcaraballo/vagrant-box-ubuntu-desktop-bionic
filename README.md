# vagrant-box-ubuntu-desktop-bionic
Resources to create a Vagrant Base Box with an Ubuntu Desktop Bionic 18.04 LTS

## Requires

* [Vagrant](https://www.vagrantup.com/downloads.html)
```
wget https://releases.hashicorp.com/vagrant/2.1.2/vagrant_2.1.2_x86_64.deb
sudo dpkg -i vagrant_2.1.2_x86_64.deb
```

* [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads)
```
echo deb http://download.virtualbox.org/virtualbox/debian $( lsb_release -cs ) contrib | sudo tee -a /etc/apt/sources.list
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y virtualbox-5.2 dkms
```

## Build
* _Host_: Download [Ubuntu Desktop 18.04.6 LTS (64 bit)](https://releases.ubuntu.com/18.04.6/ubuntu-18.04.6-desktop-amd64.iso) --see [Ubuntu's releases page](https://releases.ubuntu.com/18.04.6/) for updates to the image.
* _Host_: Create a new Virtual Box VM for the installation
  1. Open VM Virtual Box Manager
  2. Click New
  3. Virtual machine general details:
     1. Name: Ubuntu Desktop 18.04.6 LTS (Bionic Beaver)
     2. Type: Linux
     3. Version: Ubuntu (64-bit)
  4. Memory size: 8192
  5. Hard disk:
     1. Create a virtual hard disk now
     2. VDI
     3. Dinamically allocated
     4. 50 Gb
  6. Open the settings of the VM > Display > Increase video memory to 128 MB
     (for some reason with the default video memory the keyboard doesn't get captured
     by the guest)
  7. Start the new VM: Doble click on the newly created VM with the name given above
  8. Add an optical drive, select the image downloaded from Ubuntu and click start
* _Guest_: Install Ubuntu in a VirtualBox VM, including the guest additions:
  1. Install Ubuntu
  2. Keyboard layout English (UK)/English (UK)  
  3. Minimal installation
  4. Leave _Download updates while installing Ubuntu_ selected
  5. Select _Install third-party software_
  6. Leave selected _Erase disk and install Ubuntu_. Do not select encryption. Select _Use LVM with the new Ubuntu installation_
  7. Time zone London
  8. User _vagrant_ with password _vagrant_. Hostname _vagrant_. No automatic log in.
  9. Once the installation is complete, reboot
  10. Install updates (reject upgrading to a newer Ubuntu version), reboot
  11. Install dependencies to build kernel modules. From a console:
```bash
sudo apt-get install build-essential
```
  12. Install guest additions (used 6.1.38) (Devices > Insert Guest Additions CD image then accept the autorun or run `autorun.sh`), eject the additions media and reboot
  13. Clean up favourites to taste (docked quick launchers)

* _Guest_: Run [prepare-base-box-root.bash](prepare-base-box-root.bash) as root and [prepare-base-box-vagrant.bash](prepare-base-box-vagrant.bash) as the vagrant user
  ```
  wget https://raw.githubusercontent.com/jcaraballo/vagrant-box-ubuntu-desktop-bionic/master/prepare-base-box-root.bash -O - | sudo bash
  wget https://raw.githubusercontent.com/jcaraballo/vagrant-box-ubuntu-desktop-bionic/master/prepare-base-box-vagrant.bash -O - | bash
  ```
  Send the shutdown signal and turn off the VM

* _Host_:
  (Replace `VBOX_PATH` by the path to the Virtual Box VM `.vbox` file, usually inside `~/VirtualBox\ VMs/`)
  ```
  vagrant package --base VBOX_PATH --output package.box
  ```

* _Host_:
  (Add `-f` if you've already added the box to the vagrant list and want to
  replace it)
  ```
  vagrant box add --name ubuntu-desktop-bionic-18.04 package.box 
  ```

* (Optional) If you'd like to upload it to vagrantup
  1. Create a new account if you don't already have one and log in
  2. New Vagrant Box
  3. Add name and description. I use
    1. Name: `jcaraballo` / `ubuntu-desktop-bionic`
    2. Description:
       ```
       Vagrant/VirtualBox Base Box with an Ubuntu Desktop 18.04.6 LTS (Bionic Beaver)
       * Project sources: REPO_LINK
       * For this version: VERSION_LINK
       ```
       (Replace `REPO_LINK` by a link to the repo and `VERSION_LINK` by a permanent
       link to the specific version, e.g. in Github select the commit and click
       on _browes files_)
  4. Version, e.g. 0.0.1
  5. Add a provider
     1. virtualbox
     2. get the checksum e.g.
        ```bash
        sha256sum package.box
        ```
     3. Checksum type SHA256, and paste the value from the result of the command above
     4. Upload to Vagrant Cloud
  6. Choose file: select file `package.box` generated earlier with `vagrant package`
  7. Back to the dashboard, select the created box
  8. On the appropriate version: Release > Release version


## Binary
[jcaraballo/ubuntu-desktop-bionic](https://app.vagrantup.com/jcaraballo/boxes/ubuntu-desktop-bionic)

## Usage example
See [vagrant-ubuntu-18.04-dev](https://github.com/jcaraballo/vagrant-ubuntu-18.04-dev)
