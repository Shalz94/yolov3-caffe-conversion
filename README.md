# Readme

# Darknet-yolov3 to caffe coversion
This project includes caffe installation (ubuntu 18 and above version) and cnversion of yolov3 model to caffe. Yolov3 model training details are not included here. 

#### 1. Creating virtual environment
This is to keep the dependencies isolated from other projects as python2.7 would be required.

```sh
$ virtualenv <folder name>
$ cd <folder name>
$ source bin/activate
```
#### 2. Installing dependencies
```sh
$ sudo apt-get install libopencv-dev python-opencv
$ sudo apt-get install libopenblas-dev
	or 
$ sudo apt-get install libatlas-base-dev
$ sudo apt-get install libboost-all-dev
$ sudo pip install protobuf
$ sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler 
$ sudo apt-get install the python-dev
$ sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev 
```
#### 3. Caffe (forked from )
```sh
$ git clone https://github.com/BVLC/caffe
$ cd caffe
$ cp Makefile.config.example Makefile.config
$ vi Makefile.config 
```
Include the following changes in Makefile.config
    `uncomment CPU_ONLY := 1`
	`uncomment OPENCV_VERSION := 3`

To check the opencv version
```sh
$ python
>> import cv2
>> cv2.__version__
'3.0.0'
```
`uncomment CUSTOM_CXX := g++`
if OpenBLAS is installed
`set BLAS := open`
if Atlas is installed
`set BLAS := atlas`

line 94: change
`INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include` to `INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial`

line 95: change
`LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib` to `LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib  /usr/lib/x86_64-linux-gnu/hdf5/serial`

save the file

within caffe
	- make all
	- make test
	- make runtest

If there's an error encountered, then remove build and then run make again
```sh
$ rm -rf ./build*/
```
-make pycaffe

to export caffe within python
```sh
export PYTHONPATH=~/Home/_username_/caffe/python:$PYTHONPATH
```
test import caffe within python 2
```sh
$ Python
>>>import caffe
If you get an error while importing then do the following

>>> sys.path
If the exported path is not not available then, do the following

>>>sys.path.append('/Home/_username_/caffe/python')
>>>import caffe
```
#### 4. Including layers fro conversion
upsample, mish and pooling layers are to be added
Each layer folder consists of a cpp, cu and hpp file

    copy mish_layer/mish_layer.hpp, upsample_layer/upsample_layer.hpp into include/caffe/layers/
    copy mish_layer/mish_layer.cpp and mish_layer.cu, upsample_layer/upsample_layer.cpp and upsample_layer.cu into src/caffe/layers/
 
 #### 5. Add the following code to `src/caffe/proto/caffe.proto`
    // LayerParameter next available layer-specific ID: 147 (last added: recurrent_param) message LayerParameter {
        optional TileParameter tile_param = 138;
        optional VideoDataParameter video_data_param = 207;
        optional WindowDataParameter window_data_param = 129;
        #line 424
        ++optional UpsampleParameter upsample_param = 149; 
    
    #line 1453    
    ++message UpsampleParameter{
    ++  optional int32 scale = 1 [default = 1];
    ++}
    
    #line 1457
    // Message that stores parameters used by MishLayer
    ++message MishParameter {
    ++  enum Engine {
    ++    DEFAULT = 0;
    ++    CAFFE = 1;
    ++    CUDNN = 2;
    ++  }
    ++  optional Engine engine = 2 [default = DEFAULT];
    ++}

-remake caffe
#### 6. Conversion

```sh
$ python darknet2caffe.py cfg/yolov3-416.cfg cfg/yolov3-416.weights yolov3-416.prototxt yolov3-416.caffemodel
```
Here the yolov3-416.cfg and yolov3-416.weights are taken from darknet repo

#### 7. Links
[Caffe installation] (https://gist.github.com/nikitametha/c54e1abecff7ab53896270509da80215/forks)

[caffe layers modification] (https://github.com/ChenYingpeng/darknet2caffe)
