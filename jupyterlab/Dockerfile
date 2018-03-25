FROM nvcr.io/nvidia/tensorflow:18.01-py3

# Install required packages
RUN apt-get update && \
     apt-get install -y python-setuptools python-dev build-essential python-pip \
                        protobuf-compiler 

RUN python2 -m pip install --upgrade pip
RUN curl -sL https://deb.nodesource.com/setup_9.x | bash - &&  \
     apt-get install -y nodejs

# Install Jupyter
RUN pip install jupyter
RUN pip install ipywidgets
RUN jupyter nbextension enable --py widgetsnbextension

# Install JupyterLab
RUN pip install jupyterlab && jupyter serverextension enable --py jupyterlab

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

ENV PATH /opt/conda/bin:$PATH

RUN apt-get update --fix-missing && apt-get install -y wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion

RUN wget --quiet https://repo.continuum.io/miniconda/Miniconda2-4.4.10-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc

RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

# Install Python Packages & Requirements (Done near end to avoid invalidating cache) for Jupyterlab
COPY requirements.txt requirements.txt
RUN python -m pip install -r requirements.txt

COPY environment.yml environment.yml
RUN conda env create -f environment.yml

SHELL ["/bin/bash", "-c"]

# Configure python3 as kernel
RUN source activate tl-detect && python3 -m ipykernel install --user

# Cleanup old tensorflow
RUN mv /opt/tensorflow /opt/tensorflow_1.4
# Get new tensorflow and set version equal to binary version 
RUN cd /opt && \
     git clone https://github.com/tensorflow/tensorflow tensorflow && \
     cd tensorflow && \
     git checkout tags/v1.6.0 

# Download object detection models
RUN cd /opt && \
     git clone https://github.com/tensorflow/models tensorflow_models

# Install coco 
RUN source activate tl-detect && \
   git clone https://github.com/cocodataset/cocoapi.git && \
   cd cocoapi/PythonAPI && \
   sed -i 's/python/python3/g' Makefile && \
   make && \
   mkdir -p /usr/local/lib/python3.5/dist-packages/tensorflow/models/research && \
   cp -r pycocotools /opt/tensorflow_models/research && \
   cd /opt/tensorflow_models/research && \
   protoc object_detection/protos/*.proto --python_out=. && \
   echo "export PYTHONPATH=$PYTHONPATH:/opt/tensorflow_models/research:/opt/tensorflow_models/research/slim" >> ~/.bashrc

# Expose Jupyter port & cmd
ENTRYPOINT [ "/usr/bin/tini", "--" ]
EXPOSE 8888
RUN mkdir -p /opt/app/data/TL_training
VOLUME ["/opt/app/data/TL_training"]
CMD jupyter lab --ip=* --port=8888 --no-browser --notebook-dir=/opt/app/data --allow-root
