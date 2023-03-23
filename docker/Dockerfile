# https://gitlab.com/nvidia/container-images/cuda/blob/master/dist/11.3.1/ubuntu16.04-x86_64/devel/cudnn8/Dockerfile

# Pull l4t-pytorch:r35.2.1-pth2.0-py3 imagine and include CUDA cuDNN nvcc
FROM nvcr.io/nvidia/l4t-pytorch:r35.2.1-pth2.0-py3
LABEL maintainer avanetten

ENV CUDNN_VERSION 8.6.0.166
LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    libcudnn8=$CUDNN_VERSION-1+cuda11.4 \
    libcudnn8-dev=$CUDNN_VERSION-1+cuda11.4 \
    && apt-mark hold libcudnn8 && \
    rm -rf /var/lib/apt/lists/*

# FROM nvidia/cuda:9.2-devel-ubuntu16.04
# MAINTAINER avanetten
#
# ENV CUDNN_VERSION 7.3.0.29
# LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"
#
# # prep apt-get and cudnn
# RUN apt-get update && apt-get install -y --no-install-recommends \
#         apt-utils \
#             libcudnn7=$CUDNN_VERSION-1+cuda9.0 \
#             libcudnn7-dev=$CUDNN_VERSION-1+cuda9.0 && \
#     apt-mark hold libcudnn7 && \
#     rm -rf /var/lib/apt/lists/*
	
# install requirements
# RUN apt-get update \
#   	&& libopencv-dev \
# 	python3-opencv \
# 	&& apt-get clean \
# 	&& rm -rf /var/lib/apt/lists/*

RUN apt-get update \ 
	&& apt-get install -y --no-install-recommends \
	    bc \
	    bzip2 \
        apt-utils \
	    ca-certificates \
	    curl \
	    git \
	    libgdal-dev \
	    libssl-dev \
	    libffi-dev \
	    libncurses-dev \
	    libgl1 \
	    jq \
	    nfs-common \
	    parallel \
	    python3-dev \
	    python3-pip \
	    python3-wheel \
	    python3-setuptools \
	    unzip \
	    vim \
		tmux \
	    wget \
	    build-essential \
		python3-opencv \
        eog \
        cmake \
	  && apt-get clean \
	  && rm -rf /var/lib/apt/lists/*


SHELL ["/bin/bash", "-c"]
ENV PATH /opt/conda/bin:$PATH

# install anaconda
RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-py38_23.1.0-1-Linux-aarch64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda clean -t -i -p -y && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

# prepend pytorch and conda-forge before default channel
RUN conda update conda && \
    conda config --prepend channels conda-forge

# set up conda environment and add to $PATH
RUN conda create -n yoltv4 python=3.8.10 \
                    && echo "source activate yoltv4" > ~/.bashrc
ENV PATH /opt/conda/envs/yoltv4/bin:$PATH

RUN conda update --all --yes \
    && source activate yoltv4 \
	&& conda install -n yoltv4 \
                  geopandas \
                  gdal \
				  fiona \
				  rasterio \
	     	      awscli \
	              affine \
	              pyproj \
	              pyhamcrest \
	              cython \
	              h5py \
	              jupyter \
	              jupyterlab \
	              ipykernel \
	              libgdal \
	              matplotlib \
		          ncurses \
	              numpy \
				  statsmodels \
	              pandas \
	              pillow \
	              pip \
	              scipy \
	              scikit-image \
	              scikit-learn \
	              shapely \
	              rtree \
	              testpath \
	              tqdm \
				  opencv \
                  nodejs \
                  seaborn \
	&& conda clean -p \
	&& conda clean -t \
	&& conda clean --yes --all 

# # add a jupyter kernel for the conda environment in case it's wanted
# first install jupyterlab toc (https://github.com/jupyterlab/jupyterlab-toc):
RUN jupyter labextension install @jupyterlab/toc
RUN source activate yoltv4 && python -m ipykernel.kernelspec
RUN python3 -m ipykernel.kernelspec

# # clone yoltv4
RUN mkdir -p /opt/
RUN git clone https://ghproxy.com//https://github.com/avanetten/yoltv4.git /opt/yoltv4/

###################
WORKDIR "/opt/"
# Set up our notebook config.
# open ports for jupyterlab
EXPOSE 8888
RUN ["/bin/bash"]