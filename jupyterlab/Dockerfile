FROM nvcr.io/nvidia/tensorflow:18.01-py3

# Install required packages
RUN apt-get update && \
     apt-get install -y python-setuptools python-dev build-essential python-pip python3-pip
RUN pip install --upgrade pip
RUN curl -sL https://deb.nodesource.com/setup_9.x | bash - &&  \
     apt-get install -y nodejs

# Install Jupyter
RUN pip install jupyter
RUN pip install ipywidgets
RUN jupyter nbextension enable --py widgetsnbextension

# Install JupyterLab
RUN pip install jupyterlab && jupyter serverextension enable --py jupyterlab

ENV LANG=C.UTF-8

# Install Python Packages & Requirements (Done near end to avoid invalidating cache)
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

RUN mkdir -p /usr/share/jupyter/kernels/python3 \
  printf '{\n "display_name": "Python 3",\n "language": "python",\n "argv": [\n "/usr/bin/python3",\n "-m",\n "ipykernel_launcher",\n "-f",\n "{connection_file}"\n ]\n }' > /usr/share/jupyter/kernels/python3/kernel.json

# Expose Jupyter port & cmd

EXPOSE 8888
RUN mkdir -p /opt/app/data/TL_training
VOLUME ["/opt/app/data/TL_training"]
CMD jupyter lab --ip=* --port=8888 --no-browser --notebook-dir=/opt/app/data --allow-root
