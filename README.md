# Fearless Steps Challenge Phase-02 Scoring Toolkit (FS02-Sctk)
Scoring Toolkit for the Fearless Steps Challenge Phase-02

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

* [Overview](#overview)
  * [Credits](#credits)
	* [Requirements](#requirements)
	* [Features](#features)
	* [Folder Structure](#folder-structure)
	* [Usage](#usage)
		* [Config file format](#config-file-format)
		* [Using config files](#using-config-files)
		* [Resuming from checkpoints](#resuming-from-checkpoints)
    * [Using Multiple GPU](#using-multiple-gpu)
	* [Customization](#customization)
		* [Custom CLI options](#custom-cli-options)
		* [Data Loader](#data-loader)
		* [Trainer](#trainer)
		* [Model](#model)
		* [Loss](#loss)
		* [metrics](#metrics)
		* [Additional logging](#additional-logging)
		* [Validation data](#validation-data)
		* [Checkpoints](#checkpoints)
    * [Tensorboard Visualization](#tensorboard-visualization)
	* [Contribution](#contribution)
	* [TODOs](#todos)
	* [License](#license)
	* [Acknowledgements](#acknowledgements)

<!-- /code_chunk_output -->
## Overview
This software was developed at the University of Texas at Dallas, Center for Robust Speech Systems (UTD-CRSS). It serves as a wrapper around multiple third-party open-source code listed below. (See [Credits](#credits))


## Requirements
* Python >= 3.6 (3.7 recommended: Tested with Python 3.6.X, and 3.7.X.)
* Kaldi [Kaldi Installation](http://kaldi-asr.org/doc/install.html) (*Required for ASR evaluation)
* intervaltree>=3.0.0     (see requirements.txt)
* numpy>=1.16.2           (see requirements.txt)
* scipy>=0.17.0           (see requirements.txt)
* tabulate>=0.5.0         (see requirements.txt)
* sortedcontainers==2.1.0 (see requirements.txt)

## Credits
```

```

## Folder Structure
```
    ./FS02_Scoring_Toolkit
  ├── egs
  │   ├── ref_gt
  │   │   ├── ASR
  │   │   │   ├── ASR_track1
  │   │   │   └── ASR_track2
  │   │   │       └── FS01_ASR_track2_transcriptions_Dev
  │   │   ├── SAD
  │   │   ├── SD
  │   │   │   ├── RTTM
  │   │   │   └── UEM
  │   │   └── SID
  │   │       └── FS01_SID_uttID2spkID_Dev.txt
  │   └── sys_results
  │       ├── ASR
  │       │   ├── ASR_track1
  │       │   └── ASR_track2
  │       │       └── FS01_ASR_track2_transcriptions_Dev
  │       ├── SAD
  │       ├── SD
  │       └── SID
  │           └── FS01_SID_uttID2spkID_Dev.txt
  ├── LICENSE
  ├── README.md
  ├── requirements.txt
  ├── scripts
  │   ├── cfg_path.sh
  │   ├── scoreFS02_ASR.sh
  │   ├── scoreFS02_SAD.sh
  │   ├── scoreFS02_SD.sh
  │   └── scoreFS02_SID.sh
  ├── scutils
  │   ├── dscore
  │   │   ├── LICENSE
  │   │   ├── scorelib
  │   │   ├── score.py
  │   │   └── validate_rttm.py
  │   ├── fs02utils.py
  │   ├── scoreFile_SAD.pl
  │   ├── scoreFS02ASR.py
  │   ├── scoreFS02SAD.py
  │   ├── scoreFS02SD.py
  │   └── scoreFS02SID.py
  └── submission_packet
      ├── crss@utdallas.edu_ASR_track2_Submission_1
      │   ├── Dev
      │   │   └── Dev_Readme
      │   ├── Eval
      │   │   └── Eval_Readme
      │   └── FS02_System_Description_eg.pdf
      ├── crss@utdallas.edu_ASR_track2_Submission_1.tar.gz
      └── Evaluation_and_Submission_Rules.txt
```

## Usage
The code in this repo is an MNIST example of the template.
Try `python train.py -c config.json` to run code.

### Config file format
Config files are in `.json` format:
```javascript
{
  "name": "Mnist_LeNet",        // training session name
  "n_gpu": 1,                   // number of GPUs to use for training.
  
  "arch": {
    "type": "MnistModel",       // name of model architecture to train
    "args": {

    }                
  },
  "data_loader": {
    "type": "MnistDataLoader",         // selecting data loader
    "args":{
      "data_dir": "data/",             // dataset path
      "batch_size": 64,                // batch size
      "shuffle": true,                 // shuffle training data before splitting
      "validation_split": 0.1          // size of validation dataset. float(portion) or int(number of samples)
      "num_workers": 2,                // number of cpu processes to be used for data loading
    }
  },
  "optimizer": {
    "type": "Adam",
    "args":{
      "lr": 0.001,                     // learning rate
      "weight_decay": 0,               // (optional) weight decay
      "amsgrad": true
    }
  },
  "loss": "nll_loss",                  // loss
  "metrics": [
    "accuracy", "top_k_acc"            // list of metrics to evaluate
  ],                         
  "lr_scheduler": {
    "type": "StepLR",                  // learning rate scheduler
    "args":{
      "step_size": 50,          
      "gamma": 0.1
    }
  },
  "trainer": {
    "epochs": 100,                     // number of training epochs
    "save_dir": "saved/",              // checkpoints are saved in save_dir/models/name
    "save_freq": 1,                    // save checkpoints every save_freq epochs
    "verbosity": 2,                    // 0: quiet, 1: per epoch, 2: full
  
    "monitor": "min val_loss"          // mode and metric for model performance monitoring. set 'off' to disable.
    "early_stop": 10	                 // number of epochs to wait before early stop. set 0 to disable.
  
    "tensorboard": true,               // enable tensorboard visualization
  }
}
```

Add addional configurations if you need.

### Using config files
Modify the configurations in `.json` config files, then run:

  ```
  python train.py --config config.json
  ```

### Resuming from checkpoints
You can resume from a previously saved checkpoint by:

  ```
  python train.py --resume path/to/checkpoint
  ```

### Using Multiple GPU
You can enable multi-GPU training by setting `n_gpu` argument of the config file to larger number.
If configured to use smaller number of gpu than available, first n devices will be used by default.
Specify indices of available GPUs by cuda environmental variable.
  ```
  python train.py --device 2,3 -c config.json
  ```
  This is equivalent to
  ```
  CUDA_VISIBLE_DEVICES=2,3 python train.py -c config.py
  ```

## Customization

### Project initialization
Use the `new_project.py` script to make your new project directory with template files.
`python new_project.py ../NewProject` then a new project folder named 'NewProject' will be made.
This script will filter out unneccessary files like cache, git files or readme file. 

### Custom CLI options

Changing values of config file is a clean, safe and easy way of tuning hyperparameters. However, sometimes
it is better to have command line options if some values need to be changed too often or quickly.

This template uses the configurations stored in the json file by default, but by registering custom options as follows
you can change some of them using CLI flags.

  ```python
  # simple class-like object having 3 attributes, `flags`, `type`, `target`.
  CustomArgs = collections.namedtuple('CustomArgs', 'flags type target')
  options = [
      CustomArgs(['--lr', '--learning_rate'], type=float, target=('optimizer', 'args', 'lr')),
      CustomArgs(['--bs', '--batch_size'], type=int, target=('data_loader', 'args', 'batch_size'))
      # options added here can be modified by command line flags.
  ]
  ```
`target` argument should be sequence of keys, which are used to access that option in the config dict. In this example, `target` 
for the learning rate option is `('optimizer', 'args', 'lr')` because `config['optimizer']['args']['lr']` points to the learning rate.
`python train.py -c config.json --bs 256` runs training with options given in `config.json` except for the `batch size`
which is increased to 256 by command line options.


### Data Loader
* **Writing your own data loader**

1. **Inherit ```BaseDataLoader```**

    `BaseDataLoader` is a subclass of `torch.utils.data.DataLoader`, you can use either of them.

    `BaseDataLoader` handles:
    * Generating next batch
    * Data shuffling
    * Generating validation data loader by calling
    `BaseDataLoader.split_validation()`

* **DataLoader Usage**

  `BaseDataLoader` is an iterator, to iterate through batches:
  ```python
  for batch_idx, (x_batch, y_batch) in data_loader:
      pass
  ```
* **Example**

  Please refer to `data_loader/data_loaders.py` for an MNIST data loading example.

### Trainer
* **Writing your own trainer**

1. **Inherit ```BaseTrainer```**

    `BaseTrainer` handles:
    * Training process logging
    * Checkpoint saving
    * Checkpoint resuming
    * Reconfigurable performance monitoring for saving current best model, and early stop training.
      * If config `monitor` is set to `max val_accuracy`, which means then the trainer will save a checkpoint `model_best.pth` when `validation accuracy` of epoch replaces current `maximum`.
      * If config `early_stop` is set, training will be automatically terminated when model performance does not improve for given number of epochs. This feature can be turned off by passing 0 to the `early_stop` option, or just deleting the line of config.

2. **Implementing abstract methods**

    You need to implement `_train_epoch()` for your training process, if you need validation then you can implement `_valid_epoch()` as in `trainer/trainer.py`

* **Example**

  Please refer to `trainer/trainer.py` for MNIST training.

* **Iteration-based training**

  `Trainer.__init__` takes an optional argument, `len_epoch` which controls number of batches(steps) in each epoch.

### Model
* **Writing your own model**

1. **Inherit `BaseModel`**

    `BaseModel` handles:
    * Inherited from `torch.nn.Module`
    * `__str__`: Modify native `print` function to prints the number of trainable parameters.

2. **Implementing abstract methods**

    Implement the foward pass method `forward()`

* **Example**

  Please refer to `model/model.py` for a LeNet example.

### Loss
Custom loss functions can be implemented in 'model/loss.py'. Use them by changing the name given in "loss" in config file, to corresponding name.

### Metrics
Metric functions are located in 'model/metric.py'.

You can monitor multiple metrics by providing a list in the configuration file, e.g.:
  ```json
  "metrics": ["accuracy", "top_k_acc"],
  ```

### Additional logging
If you have additional information to be logged, in `_train_epoch()` of your trainer class, merge them with `log` as shown below before returning:

  ```python
  additional_log = {"gradient_norm": g, "sensitivity": s}
  log.update(additional_log)
  return log
  ```

### Testing
You can test trained model by running `test.py` passing path to the trained checkpoint by `--resume` argument.

### Validation data
To split validation data from a data loader, call `BaseDataLoader.split_validation()`, then it will return a data loader for validation of size specified in your config file.
The `validation_split` can be a ratio of validation set per total data(0.0 <= float < 1.0), or the number of samples (0 <= int < `n_total_samples`).

**Note**: the `split_validation()` method will modify the original data loader
**Note**: `split_validation()` will return `None` if `"validation_split"` is set to `0`

### Checkpoints
You can specify the name of the training session in config files:
  ```json
  "name": "MNIST_LeNet",
  ```

The checkpoints will be saved in `save_dir/name/timestamp/checkpoint_epoch_n`, with timestamp in mmdd_HHMMSS format.

A copy of config file will be saved in the same folder.

**Note**: checkpoints contain:
  ```python
  {
    'arch': arch,
    'epoch': epoch,
    'state_dict': self.model.state_dict(),
    'optimizer': self.optimizer.state_dict(),
    'monitor_best': self.mnt_best,
    'config': self.config
  }
  ```

### Tensorboard Visualization
This template supports Tensorboard visualization by using either  `torch.utils.tensorboard` or [TensorboardX](https://github.com/lanpa/tensorboardX).

1. **Install**

    If you are using pytorch 1.1 or higher, install tensorboard by 'pip install tensorboard>=1.14.0'.

    Otherwise, you should install tensorboardx. Follow installation guide in [TensorboardX](https://github.com/lanpa/tensorboardX).

2. **Run training** 

    Make sure that `tensorboard` option in the config file is turned on.

    ```
     "tensorboard" : true
    ```

3. **Open Tensorboard server** 

    Type `tensorboard --logdir saved/log/` at the project root, then server will open at `http://localhost:6006`

By default, values of loss and metrics specified in config file, input images, and histogram of model parameters will be logged.
If you need more visualizations, use `add_scalar('tag', data)`, `add_image('tag', image)`, etc in the `trainer._train_epoch` method.
`add_something()` methods in this template are basically wrappers for those of `tensorboardX.SummaryWriter` and `torch.utils.tensorboard.SummaryWriter` modules. 

**Note**: You don't have to specify current steps, since `WriterTensorboard` class defined at `logger/visualization.py` will track current steps.

## Contribution
Feel free to contribute any kind of function or enhancement, here the coding style follows PEP8

Code should pass the [Flake8](http://flake8.pycqa.org/en/latest/) check before committing.

## TODOs

- [ ] Multiple optimizers
- [ ] Support more tensorboard functions
- [x] Using fixed random seed
- [x] Support pytorch native tensorboard
- [x] `tensorboardX` logger support
- [x] Configurable logging layout, checkpoint naming
- [x] Iteration-based training (instead of epoch-based)
- [x] Adding command line option for fine-tuning

## License
This project is licensed under the MIT License. See  LICENSE for more details

## Acknowledgements
This project is inspired by the project [Tensorflow-Project-Template](https://github.com/MrGemy95/Tensorflow-Project-Template) by [Mahmoud Gemy](https://github.com/MrGemy95)
