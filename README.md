# Deeply Supervised Part Feature (DSPF) network
This project is for introducing how to use the code of the thesis of Deep Learning based Open-World Person Re-Identification.

# Requirements
* Tensorflow 1.10
* Python 3.6

# Usage

## Dataset Preparation
We use Market-1501 and DukeMTMC-reID datasets for performance evaluation. For open-world ReID settings, we follow the training/testing protocol proposed in paper
```
@INPROCEEDINGS{zheng2012,
author={W. {Zheng} and S. {Gong} and T. {Xiang}},
booktitle={2012 IEEE Conference on Computer Vision and Pattern Recognition},
title={Transfer re-identification: From person to set-based verification},
year={2012},
pages={2650-2657},
}
```
to partition each dataset into triaining/gallery/query set and store them in different folders.

Next, the format of all images in each folder is transformed to .tfrecord file through the function "create_record" in data/prepare_tfrecords.py

## Training
When finshing data preparation, you can start training network by running "train.py":
```bash
python train.py --record_dir "tfrecord files path" \ 
--dataset "market or duke" \ 
--device 0 \ 
--pre_model pretrain/se_resnext50/se_resnext50.ckpt \ 
--total_epochs 100 \ 
--learning_rate 0.001 \ 
--lamda 0.8 \ 
--data_aug \ 
--attention --deeplysupervised --part
```

You can download the pretrained model from [here](), and then put them under this path: ./pretrain/se_resnext50/.

The training needs to run twice. In second time, you have to modify the setting of --pre_model into "the path of last model training in first time" and --learning_rate into 0.0001.

## Testing 
After training twice, the last model training in second time is used to extract features. You can use "FeatureExtraction.py" to convert all the gallery and query images into feature representataions:
```bash
python FeatureExtraction.py --record_dir "tfrecord files path" \ 
--dataset "market or duke" \ 
--device 0 \ 
--feature_dir "the path of storing the output feature representataions"
--restore_model "the path of the last model training in second time" \
--iteration 10 \ 
--extract_gallery --extract_query \ 
--attention --deeplysupervised --part
```

Next, you can calculate TTR/FTR score with those feature representataions through evaluation.py:

```bash
python evaluation.py --record_dir "tfrecord files path" \ 
--dataset "market or duke" \ 
--feature_dir "the path of stored feature representataions"
--result_dir "the path of the calculated TTR/FTR score" \
--iteration 10 \ 
--normalize_feat
```

# References & Credits
- [Se-ResNet-50 pretrained model]()
- [Market1501](http://www.liangzheng.org/Project/project_reid.html)
- [DukeMTMC-reID](https://github.com/layumi/DukeMTMC-reID_evaluation)