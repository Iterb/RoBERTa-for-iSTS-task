
# Table Of Contents
-  [Introduction](#introduction)
-  [Requirements](#requirements)
-  [Datasets](#datasets)
-  [Future Work](#future-work)
-  [Contributing](#contributing)
-  [Acknowledgments](#acknowledgments)

## Introduction
Scope of this project is to is to use the [RoBERTa](https://arxiv.org/abs/1907.11692) model to determine interpretable semantic textual similarity (iSTS) between two sentences. Given two sentences of text, s1 and s2, the STS systems compute how similar s1 and s2 are, returning a similarity score. Although the score is useful for many tasks, it does not allow to know which parts of the sentences are equivalent in meaning (or very close in meaning) and which not. The aim of interpretable STS is to explore whether systems are able to explain WHY they think the two sentences are related / unrelated, adding an explanatory layer to the similarity score. The explanatory layer consists of an alignment of chunks across the two sentences, where alignments are annotated with a similarity score and a relation label. Task is inspired by [SemEval](https://alt.qcri.org/semeval2020/) competition.


## Requirements
```bash
pip install -r requirements.txt
```
## Datasets
All needed datasets are in `data/datasets` folder. Data has been downloaded from 2015 and 2016 SemEval competition [site](http://ixa2.si.ehu.eus/stswiki/index.php/Main_Page#Interpretable_STS) and processed. If needed datasets can be reproduced with following commands:
```bash
./download_semeval_data.sh
python tools/create_csv.py
```
# Training model 
To train model with default parameters run following command:
```bash
python tools/train_net.py
```
To change any parametr command can be run with additional arguments, for example to set `max_epochs` to 100 and `learning rate` to 0.0002 run following command:
```bash
python tools/train_net.py SOLVER.MAX_EPOCHS 100 SOLVER.BASE_LR 0.0002
```
All the available parameters are defined in `net_config/defaults.py` file

Parameters can also be loaded from file with `--config_file`:
```bash
python tools/train_net.py --config_file configs/roberta_config.yml
```
- In `engine`  folder create a model trainer function and inference function. In trainer function, you need to write the logic of the training process, you can use some third-party library to decrease the repeated stuff.

```python
# trainer
def do_train(cfg, model, train_loader, val_loader, optimizer, scheduler, loss_fn):
 """
 implement the logic of epoch:
 -loop on the number of iterations in the config and call the train step
 -add any summaries you want using the summary
 """
pass

# inference
def inference(cfg, model, val_loader):
"""
implement the logic of the train step
- run the tensorflow session
- return any metrics you need to summarize
 """
pass
```

- In `tools`  folder, you create the `train.py` .  In this file, you need to get the instances of the following objects "Model",  "DataLoader”, “Optimizer”, and config
```python
# create instance of the model you want
model = build_model(cfg)

# create your data generator
train_loader = make_data_loader(cfg, is_train=True)
val_loader = make_data_loader(cfg, is_train=False)

# create your model optimizer
optimizer = make_optimizer(cfg, model)
```

- Pass the all these objects to the function `do_train` , and start your training
```python
# here you train your model
do_train(cfg, model, train_loader, val_loader, optimizer, None, F.cross_entropy)
```

**You will find a template file and a simple example in the model and trainer folder that shows you how to try your first model simply.**


# In Details
```
├──  config
│    └── defaults.py  - here's the default config file.
│
│
├──  configs  
│    └── train_mnist_softmax.yml  - here's the specific config file for specific model or dataset.
│ 
│
├──  data  
│    └── datasets  - here's the datasets folder that is responsible for all data handling.
│    └── transforms  - here's the data preprocess folder that is responsible for all data augmentation.
│    └── build.py  		   - here's the file to make dataloader.
│    └── collate_batch.py   - here's the file that is responsible for merges a list of samples to form a mini-batch.
│
│
├──  engine
│   ├── trainer.py     - this file contains the train loops.
│   └── inference.py   - this file contains the inference process.
│
│
├── layers              - this folder contains any customed layers of your project.
│   └── conv_layer.py
│
│
├── modeling            - this folder contains any model of your project.
│   └── example_model.py
│
│
├── solver             - this folder contains optimizer of your project.
│   └── build.py
│   └── lr_scheduler.py
│   
│ 
├──  tools                - here's the train/test model of your project.
│    └── train_net.py  - here's an example of train model that is responsible for the whole pipeline.
│ 
│ 
└── utils
│    ├── logger.py
│    └── any_other_utils_you_need
│ 
│ 
└── tests					- this foler contains unit test of your project.
     ├── test_data_sampler.py
```


# Future Work

# Contributing
Any kind of enhancement or contribution is welcomed.


# Credits
Repo template - L1aoXingyu/Deep-Learning-Project-Template



