FROM nvidia/cuda:11.8.0-runtime-ubuntu22.04
ENV DEBIAN_FRONTEND noninteractive
ARG CONDA_VER=latest
ARG OS_TYPE=x86_64

# Install required packages
RUN apt-get update && apt-get install -y -qq --no-install-recommends \ 
                                      libglvnd-dev \
                                      libgl1-mesa-dev \
                                      libegl1-mesa-dev \
                                      libgles2-mesa-dev \
                                      libxext6 \
                                      libx11-6 \
                                      freeglut3-dev \
                                      git \
                                      python3-pip \
                                      python3-tk \
                                      curl \
                                      vim \ 
                                      libcgal-dev \
                                      libcgal-demo \
                                      python-is-python3 \
                                      build-essential \
                                      cmake \
                                      libeigen3-dev \
                                      python3-dev \
                                      libglm-dev \
                                      cuda-nvcc-11-8 \
                                      wget \
                                      ninja-build \
                                      && rm -rf /var/lib/apt/lists/*

# Install Git LFS
RUN curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash
RUN apt-get install git-lfs -y

# Install Miniconda
RUN wget https://github.com/conda-forge/miniforge/releases/${CONDA_VER}/download/Miniforge3-Linux-${OS_TYPE}.sh -O ~/miniconda.sh \
    && /bin/bash ~/miniconda.sh -b -p /opt/conda \
    && rm ~/miniconda.sh \
    && ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh \
    && echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc \
    && echo "conda activate base" >> ~/.bashrc

ENV PATH /opt/conda/bin:$PATH   
# Env vars for the nvidia-container-runtime
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES graphics,utility,compute

# Clone the repository
WORKDIR /home
ENV CUDA_HOME=/usr/local/cuda
RUN git clone --recursive https://github.com/Rooholla-KhorramBakht/feature-3dgs.git

# Set CUDA architecture flags
ENV TORCH_CUDA_ARCH_LIST="7.0 7.5 8.0+PTX"
ENV export FORCE_CUDA="1"
ENV CUDA_HOME=/usr/local/cuda

RUN apt-get update && apt-get install -y -qq --no-install-recommends && \
    apt install -y libcusolver-dev-11-8 libcublas-dev-11-8 libcublas-11-8 libcusparse-11-8 libcusparse-dev-11-8
    
# Install the 3dgs-feature environment
RUN conda create --name feature_3dgs python=3.8
# # switch to the habitate environment
SHELL ["conda", "run", "-n", "feature_3dgs", "/bin/bash", "-c"]

RUN pip install torch==2.0.1+cu118 torchvision==0.15.2+cu118 --index-url https://download.pytorch.org/whl/cu118
WORKDIR /home/feature-3dgs


RUN pip install -r requirements.txt
RUN pip install submodules/diff-gaussian-rasterization-feature 
RUN pip install submodules/simple-knn



# Install conda dependencies
RUN conda install -y -c conda-forge \
                        ffmpeg \
                        pillow \
                        typing_extensions \
                        colmap \
    && conda clean -afy

# Install additional Python dependencies
RUN pip install open3d \
                plyfile \
                opencv-python 

# # Install the gaussian splatting monitor
# RUN apt install -y libglew-dev \
#                    libassimp-dev \
#                    libboost-all-dev \
#                    libgtk-3-dev \
#                    libopencv-dev \
#                    libglfw3-dev \
#                    libavdevice-dev \
#                    libavcodec-dev \
#                    libeigen3-dev \
#                    libxxf86vm-dev \
#                    libembree-dev
# WORKDIR /home
# RUN git clone https://github.com/RongLiu-Leo/Gaussian-Splatting-Monitor.git \
#     && cd Gaussian-Splatting-Monitor/SIBR_viewers \
#     && cmake -Bbuild . -DCMAKE_BUILD_TYPE=Release \ 
#     && cmake --build build -j24 --target install


# Install rerun-sdk
RUN conda install -c conda-forge rerun-sdk
RUN echo "export PATH=$PATH:/home/Gaussian-Splatting-Monitor/SIBR_viewers/install/bin" >> ~/.bashrc

