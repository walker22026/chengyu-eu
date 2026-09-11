---
title: "Reinstalling Ubuntu for a Dedicated LLM Box: NVIDIA Driver, CUDA, and cuDNN"
date: 2024-08-03T00:00:30+00:00
summary: "After one too many driver crashes from installing random open-source software, I wiped the machine and set it up as a single-purpose LLM box."
tags: ["Ubuntu", "NVIDIA", "CUDA"]
categories: ["Blog"]
featureImage: "https://images.chengyu.eu/file/9da8cf29d828ca30be873.jpg"
---

After a lot of tinkering on the previous install, the GPU driver eventually crashed — `nvidia-smi` started erroring out, and Ollama ended up running entirely on the CPU. Reinstalling was the only real option. This time, the machine is dedicated purely to running LLMs — no more installing random open-source software just to try it out, to avoid another crash.

## Install process

### 1. Driver installation

**Check the GPU model and download the matching driver**

```
lspci | grep -i nvidia
```

Find the right driver on NVIDIA's site and download it locally.

![Finding the right driver](https://images.chengyu.eu/file/9da8cf29d828ca30be873.jpg)

**Important: make sure GCC is upgraded to version 12 first.**

1. Add the Ubuntu Toolchain PPA (if not already added):
   ```
   sudo add-apt-repository ppa:ubuntu-toolchain-r/test
   sudo apt update
   ```
2. Install GCC-12:
   ```
   sudo apt install gcc-12 g++-12
   ```
3. Use `update-alternatives` to manage GCC versions:
   ```
   sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-12 120 --slave /usr/bin/g++ g++ /usr/bin/g++-12
   ```
4. Set GCC-12 as the default:
   ```
   sudo update-alternatives --config gcc
   ```
   Pick the matching number at the interactive prompt.
5. Verify:
   ```
   gcc --version
   ```
   This should report GCC 12.

**Install the driver itself**

```
sudo ./NVIDIA-Linux-x86_64-550.107.02.run -no-x-check -no-nouveau-check -no-opengl-files
```

The exact prompts vary by driver version, but the main ones: you can skip 32-bit compatibility libraries, and say yes to automatic configuration.

Once installed, `nvidia-smi` runs and opens normally.

![Driver install prompts](https://images.chengyu.eu/file/00e9cb320e5ee6bf1587f.jpg)

### 2. CUDA installation

CUDA has to be installed for an NVIDIA card to actually show its benefit for LLM workloads. Open:

<https://developer.nvidia.com/cuda-12-4-1-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_local>

Pick the options matching your system, and it'll show you the install commands:

```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.4.1/local_installers/cuda-repo-ubuntu2204-12-4-local_12.4.1-550.54.15-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-12-4-local_12.4.1-550.54.15-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-4
```

Just run these in order.

![CUDA download page](https://images.chengyu.eu/file/5a6e85db44a4e0abd00a6.jpg)

### 3. Update environment variables

```
export PATH=/usr/local/cuda-12.4/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-12.4/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

### 4. Install cuDNN

To get the most out of the NVIDIA hardware, cuDNN is also worth installing — the process is basically the same as CUDA. Open:

<https://developer.nvidia.com/cudnn-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_local>

Pick your system config, and it'll show the install steps:

```
wget https://developer.download.nvidia.com/compute/cudnn/9.2.1/local_installers/cudnn-local-repo-ubuntu2204-9.2.1_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2204-9.2.1_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-9.2.1/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cudnn
```

![cuDNN install steps](https://images.chengyu.eu/file/acd23a5fac3bc71772630.jpg)

Once that's done, you can run a cuDNN sample to verify everything works:

```
cd /usr/src/cudnn_samples_v9/mnistCUDNN
sudo make clean && sudo make
./mnistCUDNN
```

If it compiles and runs, you should see a success message.

![cuDNN sample output](https://images.chengyu.eu/file/771ae025795f074db21f1.jpg)

![Final verification](https://images.chengyu.eu/file/e89aa405e97510e94ec80.jpg)
