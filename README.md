# CASENet: PyTorch Implementation
This is an implementation for paper [CASENet: Deep Category-Aware Semantic Edge Detection](https://arxiv.org/abs/1705.09759).

This repo has been adapted for the Cityscapes Dataset.

## Input pre-processing
The author's preprocessing repo, cityscapes-preprocess was used. Instructions for use can be found in the corresponding directory. This is used to generate binary files(.bin) for each label in the dataset. 
For data loading into hdf5 file, an hdf5 file containing these binary files needs to be generated. For this conversion, run:
`python utils/convert_bin_to_hdf5.py`
after updating the directory paths (use absolute paths).

## Model
The model used is the ResNet-101 variant of CASENet. 

The model configuration (.prototxt) can be found: [here.](https://github.com/Chrisding/seal/blob/master/exper/sbd/config/deploy.prototxt)
The download links for pretrained weights for CASENet can be found: [here.](https://github.com/Chrisding/seal#usage)

### Converting Caffe weights to PyTorch
To use the pretrained caffemodel in PyTorch, [extract-caffe-params](https://github.com/nilboy/extract-caffe-params) is used to save each layer's weights as in numpy format. The code along with instructions for usage can be found in the utils folder. 

To load these npy weights for each layer into a PyTorch model, run:
`python modules\CASENet.py`
after updating the directory path [this line](https://github.com/anirudh-chakravarthy/CASENet/blob/master/modules/CASENet.py#L386) to the parent directory containing the numpy files (use absolute paths).

## Training
For training, run:
```python main.py

Optional arguments:
    -h, --help              show help message and exit
    --checkpoint-folder     path to checkpoint dir (default: ./checkpoint)
    --multigpu              use multiple GPUs (default: False)
    -j, --workers           number of data loading workers (default: 16)
    --epochs                number of total epochs to run (default: 150)
    --start-epoch           manual epoch number (useful on restarts)
    --cls-num               The number of classes (default: 19 for Cityscapes)
    --lr-steps              iterations to decay learning rate by 10
    -b, --batch-size        mini-batch size (default: 1)
    --lr                    lr (default: 1e-7)
    --momentum              momentum (default: 0.9)
    --weight-decay          weight decay (default: 1e-4)
    -p, --print-freq        print frequency (default: 10)
    --resume-model          path to latest checkpoint (default: None)
    --pretrained-model      path to pretrained checkpoint (default: None)
```

## Visualization
For visualizing feature maps, ground truths and predictions, run: 
`python visualize_multilabel.py [--model MODEL] [--image_file IMAGE] [--image_dir IMAGE_DIR] [--output_dir OUTPUT_DIR]`

For example, to visualize on the validation set of Cityscapes dataset:
`python visualize_multilabel.py -m pretrained_models/model_casenet.pth.tar -f leftImg8bit/val/lindau/lindau_000045_000019_leftImg8bit.png -d cityscapes-preprocess/data_proc/ -o output/ `

## Testing
For testing a pretrained model on new images, run:
`python get_results_for_benchmark.py [--model MODEL] [--image_file IMAGE] [--image_dir IMAGE_DIR] [--output_dir OUTPUT_DIR]`

For example, 
`python get_results_for_benchmark.py -m pretrained_models/model_casenet.pth.tar -f img1.png -d images/ -o output/`

A class wise prediction map will be generated in the output directory specified.

## Acknowledgements
Data processing was done from the following repository: <https://github.com/Chrisding/cityscapes-preprocess>

Conversion from caffemodel to numpy format was done using the following repository: <https://github.com/nilboy/extract-caffe-params>

The model was heavily referenced from the following repository for the Semantic Boundaries Dataset: <https://github.com/lijiaman/CASENet>

The original Caffe implementation of the paper, can be downloaded from the following link: <http://www.merl.com/research/license#CASENet>