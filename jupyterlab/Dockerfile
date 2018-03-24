FROM nvcr.io/nvidia/tensorflow:18.01-py3

# Install required packages
RUN apt-get update && \
     apt-get install -y python-setuptools python-dev build-essential python-pip python3-pip python-opencv
RUN python2 -m pip install --upgrade pip
RUN python3 -m pip install --upgrade pip
RUN curl -sL https://deb.nodesource.com/setup_9.x | bash - &&  \
     apt-get install -y nodejs

# Install Jupyter
RUN pip install jupyter
RUN pip install ipywidgets
RUN jupyter nbextension enable --py widgetsnbextension

# Install JupyterLab
RUN pip install jupyterlab && jupyter serverextension enable --py jupyterlab

ENV LANG=C.UTF-8

# Configure python3 as kernel
RUN python3 -m pip install ipykernel && \
     python3 -m ipykernel install --sys-prefix

# Install Python Packages & Requirements (Done near end to avoid invalidating cache)
COPY requirements.txt requirements.txt
RUN python3 -m pip install -r requirements.txt

# Expose Jupyter port & cmd
EXPOSE 8888
RUN mkdir -p /opt/app/data/TL_training
VOLUME ["/opt/app/data/TL_training"]
CMD jupyter lab --ip=* --port=8888 --no-browser --notebook-dir=/opt/app/data --allow-root
