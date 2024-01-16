# safe-syn-fidelity
safety-aware fidelity calibration of synthetic data generators

### Limitations

The image generation process by ChatGPT4.0 + DALLE requires further fine-tuning; when comparing the image pairs in DALLE and in KITTI, one can still feel the strong difference. However, currently this is not done. 

### A. Hello world 

Run one of the jupyter notebooks, in order to replicate the experiment

* Run [KITTI_Carla_Style_Transfer.ipynb](KITTI_Carla_Style_Transfer.ipynb) for comparing the safety-aware fidelity between KITTI and KITTI being style-transfered into Carla.

* Run [KITTI_DALLE3.ipynb](KITTI_DALLE3.ipynb) for comparing the safety-aware fidelity between KITTI and DALLE3 images. 


### B. Folder structure

* [data_real/](data_real/) for the real data prepared following KITTI format, to be read by Pytorch
* [data_syn_Carla/](data_syn_Carla/) for the synthetic data style transferred into Carla, prepared following KITTI format, to be read by Pytorch
* [data_syn_Carla/](data_syn_Carla/) for the synthetic data generated by DALLE3, manually trunked and labelled, to be read by Pytorch

### C. Instructions to synthesize image using ChatGPT and DALLE3

For images manually generated from ChatGPT and DALLE3, we need to perform some manual work.

Step 1: Use ChatGPT 4.0 and follow the prompt [DALLE_Prompts.txt](DALLE_Prompts.txt) to feed a KITTI image, ask for scene interpretation, and finally, create a similar image.

Step 2: Download the image and store it in the folder [data_syn_DALLE3](data_syn_DALLE3). 


Step 3: Trim the image such that it has a dimension of 1242x375, and store them into [data_syn_DALLE3_Resize](data_syn_DALLE3_Resize). 

The PNG image generated by DALLE3 is with 4 channels (RGBA). To allow the object detector to read it, one shall convert it into a 3-channel image (RGB). 
* Images in [data_syn_DALLE3_Resize](data_syn_DALLE3_Resize) are with RGBA
* Images in [data/data_syn_DALLE3/Kitti/raw/training/image_2](data/data_syn_DALLE3/Kitti/raw/training/image_2) are already translated with RGB.
* To perform translation, one can trigger [util.ipynb](util.ipynb)

Step 4: Trigger [LabelImg](https://pypi.org/project/labelImg/1.4.0/) to perform manual labelling

Note: LabelImg has compatibility issues with python 3.10, follow this [link](https://github.com/HumanSignal/labelImg/issues/872#issuecomment-1309017766) to fix the problem.

```console
LabelImg data/KITTI_Syn_DALLE3_Resize predefined_classes.txt
```

In the "output folder" option, select [data/Syn_DALLE3_Label/PASCAL_VOC](data/Syn_DALLE3_Label/PASCAL_VOC)


Step 5: Generate the labels into KITTI form

```console
python xml2kitti.py data/KITTI_Syn_DALLE3_Label/PASCAL_VOC
```

The KITTI label (in .txt) will be stored in the same folder. Then you can copy the .txt labels to the folder [data/KITTI_Syn_DALLE3_Label/KITTI](data/KITTI_Syn_DALLE3_Label/KITTI), and subsequently, to the training dataset [data/data_syn_DALLE3/Kitti/raw/training/label_2](data/data_syn_DALLE3/Kitti/raw/training/label_2)
