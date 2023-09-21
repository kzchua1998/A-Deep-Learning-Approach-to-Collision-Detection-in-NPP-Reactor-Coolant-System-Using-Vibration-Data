# A-Deep-Learning-Approach-to-Collision-Detection-in-NPP-Reactor-Coolant-System-Using-Vibration-Data

# Installation
**Requirements**:
- PyTorch >= 1.0. Installation instructions can be found at https://pytorch.org/get-started/locally/
- opencv-python
- [pycocotools](https://github.com/cocodataset/cocoapi) (for Windows users, please refer to [this repo](https://github.com/philferriere/cocoapi))
- tqdm
- tensorboard (optional, only for training)

# Datasets
The `Collider Detection using Vibration dataset` provided by Korea Atomic Energy Research Institute (KAERI) consists of simulation time-domain vibration data of `2800 collision` events generated by `4 accelerometers`. Each collision event is associated with collision parameters such as `collision point`, (X, Y) in mm, the `mass`, M (g), and `velocity`, V (m/s) of the body upon collision to be trained and predicted. The locations of the accelerometers in the simulator are enclosed to avoid any intended selection bias.

<p align="center">
<img src="https://github.com/kzchua1998/A-Deep-Learning-Approach-to-Collision-Detection-in-NPP-Reactor-Coolant-System-Using-Vibration-Data/assets/64066100/59126d0c-c31c-4a71-b6b2-04563593f6ee" width="300" height="300">
</p>


It should be noted that MW-R only provides [raw videos](https://www.youtube.com/playlist?list=PLKjRNrBNA-nzzv4KqqdeMHMtq26kue5ZR) and their corresponding annotations in COCO json format. Therefore, further processing is necessary to convert the videos into frames and name them appropriately.


**Instructions**:
- Convert the MW Train Set videos into frames. 

- `MW-18Mar-2` video should contains 297 frames, 788 frames for `MW-18Mar-3` video, and 451 frames each for other videos. 

- In total, there should be 297 + 788 + 17*451 = 8752 frames.

- Rename the MW frames using the following file names: `Mar#_******.jpg`, where # is the video number as in the original MW dataset, and ****** is the frame number in that video but zero-padded to 6 digits. 

For example, the first frame of the `MW-18Mar-3` video will be `Mar3_000001.jpg`, and the 10th frame of the `MW-18Mar-12` video will be `Mar12_000010.jpg`.

## Demo
The video was taken approximately 17 feet from the ground near Kolej 10, Universiti Teknologi Malaysia (UTM).

https://user-images.githubusercontent.com/64066100/180419839-38764a0a-ff4e-4acc-83ec-60359f2c1bfe.mp4

## Evaluation and Visualization
Here is an example of evaluating trained RAPiD on a single image in terms of the AP metric. Skip to step 2 for visualization only.

0. Modify line 41-42 to evaluate your trained weights. Default weight used is `rapid_pL1_dark53_COCO608_Jun18_4000.ckpt`.
```
rapid = Detector(model_name='rapid',
                     weights_path='./weights/rapid_pL1_dark53_COCO608_Jun18_4000.ckpt')
```
1. Run `python evaluate.py --metric AP`
2. Visualize the result by running `python demo.py` or using `demo.ipynb` provided.

<p align="center">
<img src="https://github.com/kzchua1998/Rotation-Aware-Overhead-Human-Detection-in-Fisheye-Images/blob/master/images/visualization.png?raw=true" width="500" height="500">
</p>

## Training on COCO json data format
0. Download [the Darknet-53 weights](https://github.com/duanzhiihao/RAPiD/releases/download/v0.1/dark53_imgnet.pth) by RAPiD authors which is pre-trained on ImageNet. This is identical to the one provided by the official YOLOv3 authors but in PyTorch format.
1. Place the weights file under the /weights folder;
2. Download the COCO dataset and put it at `path/to/COCO`
3. Modify line 57-60 in train.py according to the following code snippet.
```
if args.dataset == 'COCO':
    train_img_dir = 'path/to/img/train'
    train_json = 'path/to/json/train.json'
    val_img_dir = 'path/to/img/val'
    val_json = 'path/to/json/val.json'
```
4. Run `python train.py --model rapid_pL1 --dataset COCO --batch_size 2`. Set the largest possible batch size that can fit in the GPU memory.

## Citation
```
Z. Duan, M.O. Tezcan, H. Nakamura, P. Ishwar and J. Konrad, 
“RAPiD: Rotation-Aware People Detection in Overhead Fisheye Images”, 
in IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), 
Omnidirectional Computer Vision in Research and Industry (OmniCV) Workshop, June 2020.
```
