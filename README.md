# opencv-realtime-ai-pipeline

Real-time AI pipeline for CCTV-based object detection on Ubuntu 24.04 using OpenCV 4.10 (CUDA + GStreamer), YOLOv8, TensorRT, and PyModbus. Optimized for industrial systems with NVIDIA RTX 4500 and Adam6052 integration.

---

# 🚀 Real-Time AI Video Processing Pipeline on Ubuntu 24.04

This guide documents a **step-by-step setup** for building a real-time CCTV detection system on Ubuntu 24.04.2 LTS using:

- ✅ OpenCV 4.10.0 built from source with CUDA, cuDNN & GStreamer
- ✅ YOLOv8 (Ultralytics) for object detection
- ✅ TensorRT for model acceleration on NVIDIA GPUs
- ✅ PyModbus for industrial communication (e.g., Adam6052)
- ✅ Virtual environment (`opencv_env`) for clean isolation

> ⚙️ **Tested on**: NVIDIA RTX 4500 | CUDA 12.8 | Ubuntu 24.04.2 | Python 3.12.3  
> 📹 Target Application: Real-time AI-based object detection and Modbus I/O integration for industrial automation use cases.

---

## 📦 Components

| Tool         | Purpose                                |
|--------------|----------------------------------------|
| OpenCV       | RTSP stream decoding & computer vision |
| GStreamer    | H.265 RTSP streaming                   |
| YOLOv8       | Real-time object detection             |
| TensorRT     | Model acceleration on NVIDIA GPUs      |
| PyModbus     | Modbus TCP communication               |
| Virtualenv   | Dependency isolation                   |

---

## 🧰 System Requirements

- OS: Ubuntu 24.04.2 LTS
- GPU: NVIDIA RTX 4500
- CUDA: 12.8, cuDNN: 8.9.7
- Python: 3.12.3
- Internet connection

---

## 🛠️ Step-by-Step Installation

### 1. Clean Previous Installations

```bash
rm -rf ~/.virtualenvs/opencv_env
sudo apt remove --purge python3-opencv libopencv*
sudo apt autoremove
````

---

### 2. Set Permissions

```bash
sudo chown -R $USER:$USER ~/opencv ~/opencv_contrib ~/.virtualenvs ~/Documents
sudo chmod -R u+w ~/opencv ~/opencv_contrib ~/.virtualenvs ~/Documents
```

---

### 3. Create Virtual Environment

```bash
sudo apt install python3-virtualenv virtualenvwrapper
```

Then add to `~/.bashrc`:

```bash
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
```

Apply changes:

```bash
source ~/.bashrc
mkvirtualenv --system-site-packages opencv_env
workon opencv_env
```

---

### 4. Install System Dependencies

```bash
sudo apt install build-essential cmake git pkg-config libgtk-3-dev \
libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
libxvidcore-dev libx264-dev libjpeg-dev libpng-dev libtiff-dev \
gfortran openexr libatlas-base-dev python3-dev python3-numpy \
libtbb12 libtbb-dev libopenexr-dev libgstreamer1.0-dev \
libgstreamer-plugins-base1.0-dev x11-apps
```

---

## 🧱 Build OpenCV 4.10.0 with CUDA + GStreamer

### Clone and Checkout Repositories

```bash
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git

cd opencv && git checkout tags/4.10.0 -b v4.10.0
cd ../opencv_contrib && git checkout tags/4.10.0 -b v4.10.0
```

---

### Configure Build with CMake

```bash
cd ~/opencv
mkdir build && cd build

cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D WITH_TBB=ON \
      -D ENABLE_FAST_MATH=1 \
      -D CUDA_FAST_MATH=1 \
      -D WITH_CUBLAS=1 \
      -D WITH_CUDA=ON \
      -D BUILD_opencv_cudacodec=OFF \
      -D WITH_CUDNN=ON \
      -D OPENCV_DNN_CUDA=ON \
      -D CUDA_ARCH_BIN=8.9 \
      -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-12.8 \
      -D WITH_V4L=ON \
      -D WITH_GSTREAMER=ON \
      -D OPENCV_ENABLE_NONFREE=ON \
      -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
      -D OPENCV_PYTHON3_INSTALL_PATH=~/.virtualenvs/opencv_env/lib/python3.12/site-packages \
      -D PYTHON_EXECUTABLE=~/.virtualenvs/opencv_env/bin/python \
      -D BUILD_EXAMPLES=OFF ..
```

---

### Compile and Install

```bash
make -j$(nproc)
sudo make install
sudo ldconfig
```

---

## ⚡ Install TensorRT

```bash
sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/ /"
sudo apt update
sudo apt install tensorrt

workon opencv_env
pip install tensorrt
```

---

## 🐍 Install Python Dependencies

```bash
pip install torch==2.4.1 torchvision==0.19.1 --index-url https://download.pytorch.org/whl/cu124
pip install ultralytics --no-deps
pip install numpy matplotlib pandas pyyaml pillow psutil requests tqdm scipy seaborn
pip install pymodbus
```

---

