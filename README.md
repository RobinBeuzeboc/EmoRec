# EmoRec

This project uses Python, DLib, OpenCV and Facial Landmarks to recognize emotions from images/videos/webcam feed

# Motivation

This project is part of something bigger: InMoov. The goal of the project was to create an algorithm that would allow the robot to recognize emotions and react to them. This repo contains only a insight of the whole project. Here, it is possible to create a model and apply it to videos

All the project was made on linux, so it is much easier if you want to try it.

:

If you want to try it with python 2.7, then you'll need those libraries:
- Python 2.7
- Dlib (19.7)
- Boost (1.65.1)
- SKLearn (`pip install sklearn`)
- Numpy (`pip install numpy`)
- OpenCV (2.4.13)

OR
- Python 3.6
- Dlib (19.6 but 19.7 should be fine)
- Opencv (3.2 or 3.4)
- Boost (1.65.1)
- SKLearn (`pip install sklearn`)
- Numpy (`pip install numpy`)

You can run it on windows, but this readme will only cover the "linux" install version

Building Dlib is fairly straightforward. It basically requires the C++ Boost library to be build, and then you can build Dlib and install it for Python. If everything installed correctly, but you still get an `ImportError` when importing dlib, running `sudo ldconfig` might solve this.

SKlearn will already be installed if you're using [Anaconda](https://www.anaconda.com/). If not, `pip install sklearn` will do the trick. `pip install numpy` is sufficient for numpy as well.

OpenCV gave a lot of trouble when building from source. So here are a couple of notes / tips that helped make the build succesful:
- If you want to build OpenCV version 2.4.9, you might run into an error during building pertaining to the `face_detector.cpp`, you should try building a newer version of OpenCV (2.4.13 for example).
- The build might fail on `ffmpeg`. In this case, you should build `ffmpeg` from source before building OpenCV. For example:
```bash
cd ffmpeg-<version>
./configure --enable-nonfree --enable-pic --enable-shared
make
sudo make install
```
**Warning** This will override any version of `ffmpeg` you might already have installed. Any software that relied on `ffmpeg` might break as a cause of this (ex. `mpv`).
- You might need to run `cmake` with `-D ENABLED_PRECOMPILED_HEADER=OFF` (see also [this github issue](https://github.com/opencv/opencv/issues/8878))

What worked for us eventually:

- Build `ffmpeg` from source.
- Next use `CMake`:
```bash
cd opencv-<version>
mkdir build
cd build
cmake -D WITH_TBB=ON -D BUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D INSTALL_C_EXAMPLE=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D WITH_VTK=ON -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D ENABLE_PRECOMPILED_HEADERS=OFF -fPIC ..
```
- Check the output of `CMake` to ensure there is `ffmpeg` support.
- Make and make install: `make && make install`

Once the build is successful, check in a Python interpreter session that you can do `import cv2`. If this is not the case, you might need to manually copy or symlink `cv2.so` to your `python2.7/lib` folder:
```bash
cp /path/to/opencv/build/lib/cv2.so /path/to/virtualenv/lib/python2.7/cv2.so
OR to symlink:
ln -s /path/to/opencv/build/lib/cv2.so /path/to/virtualenv/lib/python2.7/cv2.so
```
Open a new Python interpreter session, and try `import cv2` again.



## Usage

Once all the above requirements are met, you can try it out. Some functionality, such as sorting and preparing of the dataset, requires there to be a dataset. We used the Cohn-Kanade extended (CK+) dataset. http://www.consortium.ri.cmu.edu/ckagree/
 http://www.paulvangent.com/2016/04/01/emotion-recognition-with-python-opencv-and-a-face-dataset/ for more infos on how to create a trained model (great explanations)

Example usage:
```bash
$ python -m emotionreader --help
usage: emotionreader [-h] [-V] {sort,prepare-dataset,train,webcam} ...

 arguments:
  {sort,prepare-dataset,train,webcam}     the action to perform
    sort                sort the CK+ dataset
    prepare-dataset     prepare the dataset by detecting faces and cutting
                        them to size
    train               train the model
    webcam              start real timedetection from the command line
optional arguments:
    -h, --help          show this help message and exit
```

Each action also has a help function. For example:
```bash
$ python -m emotionreader webcam --help
usage: emotionreader webcam [-h] [-d DIMENSIONS] [-l]

optional arguments:
    -h, --help          show this help message and exit
    -d DIMENSIONS, --dimensions DIMENSIONS
                        the width and height to start the webcam with

```

# Citations

- Lucey, P., Cohn, J. F., Kanade, T., Saragih, J., Ambadar, Z., & Matthews, I. (2010). The Extended Cohn-Kanade Dataset (CK+): A complete expression dataset for action unit and emotion-specified expression. Proceedings of the Third International Workshop on CVPR for Human Communicative Behavior Analysis (CVPR4HB 2010), San Francisco, USA, 94-101.
- van Gent, P. (2016). Emotion Recognition Using Facial Landmarks, Python, DLib and OpenCV. A tech blog about fun things with Python and embedded electronics. Retrieved from: http://www.paulvangent.com/2016/08/05/emotion-recognition-using-facial-landmarks/
- Kanade, T., Cohn, J. F., & Tian, Y. (2000). Comprehensive database for facial expression analysis. Proceedings of the Fourth IEEE International Conference on Automatic Face and Gesture Recognition (FG'00), Grenoble, France, 46-53.

if you're interested to install it on raspberry:
- https://www.pyimagesearch.com/2016/04/18/install-guide-raspberry-pi-3-raspbian-jessie-opencv-3/
- https://www.pyimagesearch.com/2017/05/01/install-dlib-raspberry-pi/
