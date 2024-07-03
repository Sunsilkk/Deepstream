# DeepStream-Yolo Jetson Nano


## Prerequisites
 - Jetson Nano with JetPack installed (version compatibility details can be found on the NVIDIA website)
 - Python 3 and pip package manager

## Install jtop
  ```sh
  pip install jetson-stats
  jtop
  ```

## Docker image build

### 1. Build the image
  ```sh
  cd docker/
  sudo docker build . -t  deepstream:jetson
  cd ..
  ```
### 2. Start a container
#### 2.1 Prerequisite Setup
 -  **X11  Forwarding**: Enable X11 forwarding to allow graphical applications to run inside the container: 

```sh
xhost +
```

 -  **Display Environment Variable:** Set the DISPLAY environment variable:

 ```sh
export DISPLAY=:0
```
 - Run the following command to start a Docker container named deepstream based on the deepstream:jetson image:

```sh
sudo docker run -it --rm --net=host --runtime nvidia -e DISPLAY=$DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix -v $PWD:/app deepstream:jetson
```
## Running the Application

### 1. Build the application

```sh
CUDA_VER=10.2 make -C nvdsinfer_custom_impl_Yolo
```

### 2. Start the application

Edit the `config.txt` file according to your model
You need to convert your pretrained model to onnx or you con found pretrained model in [NVIDIA DeepStream SDK on Jetson](https://developer.nvidia.com/embedded/deepstream-on-jetson-downloads-archived)

```
[property]
...
onnx-file=model/yolov8n.onnx
...
```

convert model into fp16
```
/usr/src/tensorrt/bin/trtexec --onnx=model/yolov8n.onnx --fp16 --saveEngine=model/yolov8n_fp16.engine
```

`config_infer_primary_yoloV8_onnx.txt`
```
[property]
...
model-engine-file=model/yolov8n_fp16.engine
...
```
Add video source  `deepstream_app_config.txt`  

```sh
[source0]
....
uri=file:///app/video/3.mp4
....
```

Now, run the application by running the following command:

```sh
deepstream-app

 -c deepstream_app_config.txt
```
Debuging 
```sh
GST_DEBUG=3 deepstream-app -c deepstream_app_config.txt
```


In raspi4 install the [Mediamtx](https://github.com/bluenviron/mediamtx) to create a RTSP server and add the source camera to file yml like this.

```sh
paths:
   camera1:
     source: rtsp://admin:Pqshop2k3.@192.168.1.64:554/Streaming/Channels/1/
```

### Usefull links
[NVIDIA DeepStream SDK on Jetson](https://developer.nvidia.com/embedded/deepstream-on-jetson-downloads-archived)

[YOLOv8-DeepStream-TRT-Jetson](https://wiki.seeedstudio.com/YOLOv8-DeepStream-TRT-Jetson/)



