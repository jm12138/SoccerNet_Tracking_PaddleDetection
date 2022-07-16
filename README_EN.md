# SoccerNet Multiple Object Tracking Baseline Based on PaddleDetection
* [中文](./README.md) | English

## 1. Introduction
* A baseline for Multiple Object Tracking (MOT) of soccer and soccer players based on the [PaddleDetection](https://github.com/PaddlePaddle/PaddleDetection) and the [SoccerNet Tracking](https://github.com/SoccerNet/sn-tracking) dataset

* Includes four classic Multiple Object Tracking models, DeepSort, ByteTrack, JDE and FairMOT, with full process support for model training, evaluation, inference and deployment

## 2. Demo
* The soccer and soccer player Multiple Object Tracking demonstration is as follows:

    ![demo](https://ai-studio-static-online.cdn.bcebos.com/c2e1a47da7c345c4b483367803b1c42c4bfba0984fa046c3ba19630687ac9398)

## 3. Directory Structure
* The directory structure of this repo is as follows：

    * configs -> Configuration files of models

    * dataset -> Dataset download and pre-processing scripts

    * tools -> Evaluation Scripts

## 4. Dataset
### 4.1 Dataset Introduction
* SoccerNet Tracking dataset consists of 12 complete soccer games from the main camera including:

    * 200 clips of 30 seconds with tracking data.

    * one complete halftime annotated with tracking data.

    * the complete videos for the 12 games.

### 4.2 Dataset Format
* The ground truth and detections are stored in comma-separate csv files with 10 columns：

    |Frame ID|Track ID|X|Y|W|H|Score|-1|-1|-1|
    |:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|

* The dataset meets MOT20 format requirements and is marked as -1 because some values are not used

## 5. Models
* The models and their configuration files are shown in the following table:

    |Model|Paper|Config|
    |:-:|:-:|:-:|
    |DeepSort|[Simple Online and Realtime Tracking with a Deep Association Metric](https://arxiv.org/abs/1703.07402)|[configs](./configs/snmot/deepsort)|
    |ByteTrack|[ByteTrack: Multi-Object Tracking by Associating Every Detection Box](https://arxiv.org/abs/2110.06864)|[configs](./configs/snmot/bytetrack)|
    |JDE|[Towards Real-Time Multi-Object Tracking](https://arxiv.org/abs/1909.12605)|[configs](./configs/snmot/jde)|
    |FairMOT|[FairMOT: On the Fairness of Detection and Re-Identification in Multiple Object Tracking](https://arxiv.org/abs/2004.01888v6)|[configs](./configs/snmot/fairmot)|


## 6. Usage
### 6.1 Installation
* Clone this repo:

    ```bash
    $ git clone https://github.com/jm12138/SoccerNet_Tracking_PaddleDetection

    $ cd SoccerNet_Tracking_PaddleDetection

    $ pip install -r requirements.txt
    ```

* Clone PaddleDetection:

    ```bash
    $ git clone https://github.com/PaddlePaddle/PaddleDetection
    
    $ cd PaddleDetection

    $ pip install -r requirements.txt
    ```

* Copy files:

    ```bash
    $ cp -r ../configs ./
    $ cp -r ../dataset ./
    $ cp -r ../tools ./
    ```

### 6.2 Dataset Download
* Downloading datasets using the download script:

    ```bash
    $ cd ./dataset/snmot
    
    $ python download_data.py
    ```

### 6.3 Dataset Decompression
* Use the following command to unpack the dataset:

    ```bash
    $ cd ../../
    $ mkdir ./dataset/snmot/SNMOT/images

    $ unzip -q ./dataset/snmot/SNMOT/tracking/train.zip -d ./dataset/snmot/SNMOT/images
    $ unzip -q ./dataset/snmot/SNMOT/tracking/test.zip -d ./dataset/snmot/SNMOT/images
    $ unzip -q ./dataset/snmot/SNMOT/tracking/challenge.zip -d ./dataset/snmot/SNMOT/images
    ```

### 6.4 Dataset Processing
* Convert data format to comply with PaddleDetection requirements:

    ```bash
    $ cd ./dataset/snmot
    
    $ python gen_labels.py
    $ python gen_image_list.py
    $ python gen_det_coco.py
    $ python gen_det_results.py
    $ python zip_gt.py
    ```

### 6.5 Model Training
* Specify a model configuration file and use the following command for model training (using FairMOT as an example):

    ```bash
    $ cd ../../

    $ python tools/train.py -c ./configs/snmot/fairmot/fairmot_dla34_30e_1088x608.yml 
    ```

### 6.6 Model Evaluation
* Use the following command for model evaluation:

    ```bash
    $ python tools/eval_mot.py \
        -c ./configs/snmot/fairmot/fairmot_dla34_30e_1088x608.yml \
        -o weights=./output/fairmot_dla34_30e_1088x608/model_final

    $ cd ./output/mot_results
    
    $ zip soccernet_mot_results.zip *.txt

    $ cd ../../
    
    $ python tools/evaluate_soccernet_v3_tracking.py \
        --BENCHMARK SNMOT \
        --DO_PREPROC False \
        --SEQMAP_FILE tools/SNMOT-test.txt \
        --TRACKERS_TO_EVAL test \
        --SPLIT_TO_EVAL test \
        --OUTPUT_SUB_FOLDER eval_results \
        --TRACKERS_FOLDER_ZIP ./output/mot_results/soccernet_mot_results.zip \
        --GT_FOLDER_ZIP ./dataset/snmot/gt.zip
    ```

### 6.7 Model Inference
* Use the following command for model inference:

    ```bash
    !python tools/infer_mot.py \
        -c ./configs/snmot/fairmot/fairmot_dla34_30e_1088x608.yml \
        -o weights=./output/fairmot_dla34_30e_1088x608/model_final \
        --image_dir ./dataset/snmot/SNMOT/images/challenge/SNMOT-021/img1 \
        --frame_rate 25 \
        --output_dir ./output \
        --save_videos
    ```

### 6.8 More Details
* Please refer to the official PaddleDetection documentation for model deployment and further usage guidelines

## 7. References
* Links:

    * [SoccerNet: Soccer Video Understanding Benchmark Suite](https://www.soccer-net.org/home)

    * [SoccerNet Tracking Official Website](https://www.soccer-net.org/tasks/tracking)

    * [SoccerNet Tracking Development Kit](https://github.com/SoccerNet/sn-tracking)

    * [PaddleDetection](https://github.com/PaddlePaddle/PaddleDetection)

* Citations:

    ```BibTeX
    @inproceedings{Wojke2017simple,
        title={Simple Online and Realtime Tracking with a Deep Association Metric},
        author={Wojke, Nicolai and Bewley, Alex and Paulus, Dietrich},
        booktitle={2017 IEEE International Conference on Image Processing (ICIP)},
        year={2017},
        pages={3645--3649},
        organization={IEEE},
        doi={10.1109/ICIP.2017.8296962}
    }

    @article{DBLP:journals/corr/abs-2110-06864,
        title = {ByteTrack: Multi-Object Tracking by Associating Every Detection Box},
        author = {Zhang, Yifu and Sun, Peize and Jiang, Yi and Yu, Dongdong and Weng, Fucheng and Yuan, Zehuan and Luo, Ping and Liu, Wenyu and Wang, Xinggang},
        doi = {10.48550/ARXIV.2110.06864},
        url = {https://arxiv.org/abs/2110.06864},
        publisher = {arXiv},
        year = {2021}
    }

    @article{wang2019towards,
        title={Towards Real-Time Multi-Object Tracking},
        author={Wang, Zhongdao and Zheng, Liang and Liu, Yixuan and Wang, Shengjin},
        journal={arXiv preprint arXiv:1909.12605},
        year={2019}
    }

    @article{zhang2020fair,
        title={FairMOT: On the Fairness of Detection and Re-Identification in Multiple Object Tracking},
        author={Zhang, Yifu and Wang, Chunyu and Wang, Xinggang and Zeng, Wenjun and Liu, Wenyu},
        journal={arXiv preprint arXiv:2004.01888},
        year={2020}
    }
    ```
