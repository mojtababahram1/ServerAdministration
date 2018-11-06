# Install ubuntu server
Download ubuntu server 16.04 and create a bootable usb stick with it using [startup disk creator](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-ubuntu#0).

# One static IP address for multiple ssh server?
The solution is using **NAT** port forwarding ([see here](http://cookbook.fortinet.com/one-ip-address-serving-multiple-servers/)). We tested this solution and it worked fine. The other suggestion was using *vpn* which is not appropriate because we don't want to transfer all the traffic through the servers.

# Configure proxy for apt
Some universities have their own proxy server for apt, which first sends request to local cache server and if failed, uses the main server. From [here](https://askubuntu.com/questions/257290/configure-proxy-for-apt):  
```shell
sudo nano /etc/apt/apt.conf
[add] Acquire::http::Proxy "http://yourproxyaddress:proxyport";
```
# Configure pptp VPN client
Used the answer from [here](https://askubuntu.com/questions/891393/vpn-pptp-in-ubuntu-16-04-not-working). For my vpn I should comment *require-mppe* and *refuse-mschap*.

# Setup ssh-server
I followed this [link](https://help.ubuntu.com/community/SSH/OpenSSH/Configuring). As suggested, I disabled password authentication but allow tcp and x11 port forwarding. I also applied the rate limit. Then I tried to do ssh from a remote computer but I couldn't because I do not set public and private key. First I should create public and private key for myself. I found this [link](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) and used the following two commands:
```shell
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
ssh-add ~/.ssh/id_rsa
```
Then I should add my public key to the server. [ssh-copy-id](https://www.ssh.com/ssh/copy-id) can do this:
```shell
ssh-copy-id -i ~/.ssh/id_rsa user@host
```
But it needs password authentication, so just for once I make it enabled and did this and again make it disabled. Now I can ssh to the server with admin user. Other users can send me their public ID and then I can add them to their authorized keys. How? see [this](https://www.digitalocean.com/community/questions/ubuntu-16-04-creating-new-user-and-adding-ssh-keys).  
We decided to permit password authentication.

# Install Python
```shell
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt install python2.7
sudo apt install python3.6
sudo apt install python3.5
sudo apt install python-pip
sudo apt install python3-pip
sudo pip install --upgrade pip
sudo pip3 install --upgrade pip
```
To check pip version: ```pip --version or pip3 --version```.  
To check source of symbolic links:```ls -l `which python` or ls -l /usr/bin/python```.  
I tried ```pip install numpy``` without sudo and numpy installed locally in ```~/.local``` (found out by printing ```numpy.__file__``` in python) so I removed it with ```pip uninstall numpy```.  
install **pandas**, **scipy**, **scikit-learn** and **jupyter**.

# Installing nvidia driver and cuda
using this [link](https://towardsdatascience.com/build-and-setup-your-own-deep-learning-server-from-scratch-e771dacaa252) for installing driver and this [link](https://blog.kovalevskyi.com/multiple-version-of-cuda-libraries-on-the-same-machine-b9502d50ae77) for installing cuda binaries in order to be able to install different versions of cuda.

Install cudnn : [cudnn_installation_link](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/)

# Install tensorflow
```shell
sudo pip install tensorflow-gpu
sudo pip install tensorboard
```
keras is installed inside tensorflow??

# Install rstudio
using this [link](https://medium.com/@GalarnykMichael/install-r-and-rstudio-on-ubuntu-12-04-14-04-16-04-b6b3107f7779):
```shell
sudo apt-get install r-base
sudo apt-get install r-base-dev
# Download and Install RStudio
sudo apt-get install gdebi-core
wget https://download1.rstudio.org/rstudio-1.0.44-amd64.deb
sudo gdebi rstudio-1.0.44-amd64.deb
```



# Copy from external usb to server
```shell
sudo fdisk -l -> find the name of hdd (sda1)
sudo mount -t ntfs /dev/sda1 /media/myhdd
do whatever you want
sudo umount /dev/sda1
```

# Add users
Using this [link](https://www.cyberciti.biz/tips/linux-how-to-create-multiple-users-accounts-in-batch.html). Don't use this link again.


# Add to system path
tensorflow could not find cuda 9.0 for non root users because cuda was not added to default path for other users:
```shell
	add cuda path to /etc/environment
	add cuda/lib64 path to /etc/ld.so.conf directory
```

# Limiting users' memory with cgroup
installing and defining memory cgroup based on this [link](https://github.com/jooyong-park/workspace/wiki/cgroups-on-debian-ubuntu). Instead of limiting the memory of applications, limit the memory of a user group (named dmlgroup (assigning different users to this group by using the file scripts/add-users-to-groups.sh))  (now the memory limit is set to 64GB)

# Reset swap space
here is the link for increasing swap: [link](https://www.digitalocean.com/community/questions/how-to-change-swap-size-on-ubuntu-14-04)

# Install Matlab
follow these two links:

[matlab installation files and installation tips](https://downloadly.ir/software/engineering-specialized/mathworks-matlab-5/)

[install matlab in silent mode in linux](http://installfights.blogspot.com/2016/11/how-to-install-matlab-without-gui.html)

Install matlab kernel for jupyter:
1. [Install matlab_kernel](https://github.com/Calysto/matlab_kernel)
2. [Install MATLAB Engine API for Python](https://www.mathworks.com/help/matlab/matlab_external/install-the-matlab-engine-for-python.html)
# TO DO:
1. install pip for python3.6
4. install network-monitoring tool

# QUESTIONS:
1. ban downloading too much on the server
2. what if a user wants to ssh from another computer. then he should knows his private key in the other computer yes? isn't it hard? permit password authentication.
