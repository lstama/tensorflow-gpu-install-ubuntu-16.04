# Tensorflow GPU install on ubuntu 16.04    

These instructions are intended to set up a deep learning environment for GPU-powered tensorflow.      
[See here for pytorch GPU install instructions](https://github.com/williamFalcon/pytorch-gpu-install)

After following these instructions you'll have:

1. Ubuntu 16.04. 
2. Cuda 9.0 drivers installed.
3. Cudnn 7.3.1
4. A conda environment with python 3.6.    
5. Tensorflow 1.11.0.
6. Keras

---   
### Step 0: Noveau drivers     
Before you begin, you may need to disable the opensource ubuntu NVIDIA driver called [nouveau](https://nouveau.freedesktop.org/wiki/).

**Option 1: Modify modprobe file**
1. After you boot the linux system and are sitting at a login prompt, press ctrl+alt+F1 to get to a terminal screen.  Login via this terminal screen.

2. Create a file: /etc/modprobe.d/nouveau-blacklist.conf e.g. by 
```
sudo touch /etc/modprobe.d/nouveau-blacklist.conf
```
3.  Put the following in the above file...
```
blacklist nouveau
options nouveau modeset=0
```

4. Regenerate the kernel initramfs

```
sudo update-initramfs -u
```
5. reboot system   
```bash
reboot
```   
    
6. On reboot, verify that noveau drivers are not loaded   
```
lsmod | grep nouveau
```

If `nouveau` driver(s) are still loaded do not proceed with the installation guide and troubleshoot why it's still loaded.    

**Option 2: Modify Grub load command**    
From [this stackoverflow solution](https://askubuntu.com/questions/697389/blank-screen-ubuntu-15-04-update-with-nvidia-driver-nomodeset-does-not-work)    

1. When the GRUB boot menu appears : Highlight the Ubuntu menu entry and press the E key.
Add the nouveau.modeset=0 parameter to the end of the linux line ... Then press F10 to boot.   
2. When login page appears press [ctrl + ALt + F1]    
3. Enter username + password   
4. Uninstall every NVIDIA related software:   
```bash    
sudo apt-get purge nvidia*  
sudo reboot   
```   

---   
## Installation steps     


0. update apt-get   
``` bash 
sudo apt-get update
```
   
1. Install apt-get deps  
``` bash
sudo apt-get install openjdk-8-jdk git python-dev python3-dev python-numpy python3-numpy build-essential python-pip python3-pip python-virtualenv swig python-wheel libcurl3-dev curl   
```

2. install nvidia drivers 
``` bash
# The 16.04 installer works with 16.10.
# download drivers
curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.0.176-1_amd64.deb

# download key to allow installation
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

# install actual package
sudo dpkg -i ./cuda-repo-ubuntu1604_9.0.176-1_amd64.deb

#  install cuda (but it'll prompt to install other deps, so we try to install twice with a dep update in between
sudo apt-get update
sudo apt-get install cuda-9-0   
```    

2a. reboot Ubuntu
```bash
sudo reboot
```    

2b. check nvidia driver install 
``` bash
nvidia-smi   

# you should see a list of gpus printed    
# if not, the previous steps failed.   
``` 

3. Install cudnn 

``` bash
wget https://s3.amazonaws.com/open-source-william-falcon/cudnn-9.0-linux-x64-v7.3.1.20.tgz
sudo tar -xzvf cudnn-9.0-linux-x64-v7.3.1.20.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```    

4. Add these lines to end of ~/.bashrc:   
``` bash
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
export PATH="$PATH:/usr/local/cuda/bin"
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```   

4a. Reload bashrc     
``` bash 
source ~/.bashrc
```   

5. update pip (might already be up to date, but just in case...)
```
pip3 install --upgrade pip
```

6. Install stable tensorflow with GPU support for python 3.6    
``` bash
pip3 install tensorflow-gpu==1.11.0
```

7. Install stable tensorflow with GPU support for python 3.6    
``` bash
pip3 install keras
```

8. Test tf and keras install with [keras mnist script](https://github.com/keras-team/keras/blob/master/examples/mnist_cnn.py)  
``` bash
# start python shell   
python3 mnist_cnn.py
```
