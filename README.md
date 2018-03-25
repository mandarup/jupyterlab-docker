# jupyterlab-docker
Jupyterlab Docker image specially modified to support nvidia optimized python for tensorflow.

## Build
Head to [NVidia GPU Cloud](https://ngc.nvidia.com/) and create an account. Head
to the configuration section and follow the steps to login. It should resemble

```
$ docker login nvcr.io

Username: $oauthtoken
Password: <Your Key>
```

next

```
# Change to the jupyterlab directory
cd jupyterlab
# Build the container
nvidia-docker build -t jupyterlab .
```

to run

```
nvidia-docker run --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 -p 8888:8888 -v $HOME/TL_training:/opt/app/data/TL_training --rm -it jupyterlab
```


