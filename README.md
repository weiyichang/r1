# R-FCN with PVANet
This repo contains an integrated framework of [R-FCN](https://arxiv.org/abs/1605.06409) and [PVANet](https://arxiv.org/abs/1611.08588) for object (clothing) detecion. For the details of these methods, please refer to the official github repo ([R-FCN](https://github.com/YuwenXiong/py-R-FCN) , [PVANet](https://github.com/sanghoon/pva-faster-rcnn)) and paper.

### Main feature
1. [OHEM](https://arxiv.org/abs/1604.03540) (CVPR2016) implementation from R-FCN (for data augmentation)
2. Plateau (for learning rate) and bounding box voting implementation from PVANet 
3. Merge BN/Scale layer code from PVANet (for acceleration) 

### Installation
Follow the step0  to step3 that were shown in [PVANet](https://github.com/sanghoon/pva-faster-rcnn). 

### How to run the demo
1. Download PVANet_comp or PVANet_lite detection model (scp or ?!)
2. Run the demo script **(TBM)**
3. Models and deploy OOXXXXXXXXXXXXXXXX


### How to train PVANet_comp model
This model is trained and tested by using the following features: **Plateau, OHEM, bbox voting**, and I also apply SVD decomposition on FCs to compress model size (two small FCs to approximate the original FC).  

The simple training log is in OOOXXXXXXXXXXXXXXXXXXXXXXXXXX

Run the script for training PVANet_comp model
```
./tools/train_net.py \
--gpu 0 \
--solver models/pascal_voc/PVANet/solver_plateau_comp_ohem.prototxt \ 
--weights models/pascal_voc/PVANet/pva_comp_original.model \ 
--imdb FlickrClothingTrain_0926 \ 
--iters 500000 \
--cfg experiments/cfgs/rfcn_pvanet_ohem.yml
```

Note:
1. This model will fine-tune from VOC model (pva_comp_original.model), and the snapshot function in ***train_net.py*** has been modified (snapshot **bbox_pred_new** layer, not **bbox_pred** layer)
2. My *comp* version is training a new model (no conversion). Actually, the suitable way for generating *comp* version is converting from PVANet model by :
```
tools/compress_net.py
```

Training data
1. The format of annotation is similar to VOC (XML files)
2. An example of imdb is in ***lib/dataset/largeflickr.py***
3. There would be a cache file generated in *data/cache*, if you want to add more data with same imdb name, please delete the previous imdb file (.pkl)


### How to train PVANet_lite model
This model is trained and tested by using the following features: **Plateau, OHEM, BN layer (RC), Conv_rpn/Convf, bbox voting** .  

The simple training log is in OOOXXXXXXXXXXXXXXXXXXXXXXXXXX

Run the script for training PVANet_lite model:
```
./tools/train_net.py \
--gpu 0 \
--solver models/pascal_voc/PVANet_lite/solver_plateau_rc.prototxt \ 
--weights models/pascal_voc/PVANet_lite/original.model \
--imdb FlickrClothingTrain_0926 \
--iters 500000 \
--cfg experiments/cfgs/rfcn_pvanet_ohem.yml
```

Training data: same (FlickrClothingTrain_0926)

### Results

|  Model | Inference Time |  Model Size | MAP |
| ------ | ---- |  ---- | ---- |
| PVANet_org | 48 / X  ms| 313 MB| 0.835/0.816 |
| PVANet_comp_480000.caffemodel | 36 / 28 ms | 79 MB | **0.855/0.822** |
| PVANet_lite_390000.caffemodel | **16 / 12** ms | 36 MB | 0.835/0.797 |
| R-FCN with PVANet             | 35 / 28 ms | **22** MB | 0.837/0.797 |

Note
1. The inference time was run on Titan X (Maxwell and Pascal).
2. These models have done BN/Scale conversion.
3. The MAP is testing on two datasets with scenario of street and shop (VIPs). 

