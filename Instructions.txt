Waseen youtube instructions:
https://www.youtube.com/watch?v=_Tbu0yLPbVA

step 1:
cd ~/Desktop/IDS\ Camera/Code/python/
mkdir images
python3 captureVideoUEye.py --fps=30 --parameterSetFile="parameter_sets/ultimate_1280x1024.ini" --calib=0 //got normal video and vignette calibration
# create the video intrinsic calibration

step 2:
cp ~/Desktop/IDS Camera/Code/python/images ~/Desktop/calibration_bag
cd ~/Desktop/DSP/catkin_ws/
roscore
rosrun BagFromImages BagFromImages /home/yoni4/Desktop/calibration_bag/images/ .jpg 30 /home/yoni4/Desktop/calibration_bag/calibration.bag
cd ~/Desktop/Kalibr_Calibration/kalibr_workspace
source devel/setup.bash
cp ~/Desktop/calibration_bag/best_calibration/checkerboard.yaml ~/Desktop/calibration_bag
cd ~/Desktop/Kalibr_Calibration/kalibr_workspace/src/kalibr/aslam_offline_calibration/kalibr/python
python kalibr_calibrate_cameras --target /home/yoni4/Desktop/calibration_bag/checkerboard.yaml --bag /home/yoni4/Desktop/calibration_bag/calibration.bag --models pinhole-equi --topics /camera/image_raw

step 3: create camera.txt:
cd ~/Desktop/calibration_bag
touch ~/Desktop/calibration_bag/camera.txt
code ~/Desktop/calibration_bag
# copy the intrinsic params as following:

to home_sweep folder insert the following camera.txt file, where the first 4 numbers are fx/1280 fy/1024 cx/1280 cy/1024

EquiDistant   0.655561689  0.819980781  0.556134596  0.622731208 -0.0019668261344074397 -0.02539404563772661 0.032937928652400555 -0.013733559603575744
1280 1024
crop
640 480

to home_sweep_response and home_sweep_vignette folders insert the following camera.txt file

0.655561689  0.819980781  0.556134596  0.622731208 0
1280 1024
crop
640 480

step 4: record video for responce calibration
cd /home/yoni4/Desktop/IDS Camera/Code/python
mkdir images
python3 captureVideoUEye.py --fps=30 --parameterSetFile="parameter_sets/ultimate_1280x1024.ini" --calib=1

step 5:respose calibration (pcalib.txt file)
copy the images lib and times.txt just created to /home/user/Datasets/home_sweep_response 
cd  DSO/mono_dataset_code/build/bin
./responseCalib "/home/user/Datasets/home_sweep_response/"
copy /home/user/DSO/mono_dataset_code/build/bin/photoCalibResult/pcalib.txt to /home/user/Datasets/home_sweep_vignette

Step 6 - use ar code to perform Vignette calibration 
cd ~/IDS_cam/DSO/IDS
create the library images or erase its content if already created
python3  captureVideoUEye.py --fps=30 --parameterSetFile="your_name.ini" --calib=0
use the backside of the apriltagboard and watch minute in the youtube video
capture about 550 frames (erase the rest from images library, also in times.txt, and erase the 0th frame and timestamp)
copy the images lib and times.txt to to /home/user/Datasets/home_sweep_vignette
cd ~/DSO/mono_dataset_code/build/bin
./vignetteCalib "/home/user/Datasets/home_sweep_vignette/"
from DSO/mono_dataset_code/build/bin/vignette_calib_result take vignette.png and put in "/home/user/Datasets/home_sweep"
from home_sweep_vignette take pcalib.txt and put in "/home/user/Datasets/home_sweep"


Run DSO on a prerecorded video (images folder + times.txt)
cd ~/IDS_cam/DSO/IDS
create the library images or erase its content if already created
prerecord a video using:  python3  captureVideoUEye.py --fps=30 --parameterSetFile="your_name.ini" --calib=0 , and copy the images lib and times.txt to /home/user/Datasets/home_sweep
in a new terminal type - cd ~/DSO/dso/build/bin
./dso_dataset files="/home/user/Datasets/home_sweep/images"  vignette="/home/user/Datasets/home_sweep/vignette.png" calib="/home/user/Datasets/home_sweep/camera.txt"  gamma="/home/user/Datasets/home_sweep/pcalib.txt"

