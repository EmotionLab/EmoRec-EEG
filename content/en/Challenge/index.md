---
title: Challenge
weight: 5
omit_header_text: true
description: We'd love to hear from you
featured_image: '/images/Dalle4.webp'
type: page
menu: main

---

## Challenge rules and instructions 
The aim of the challenge is to foster generalizability of EEG-based emotion-recognition approaches. To this end, the challenge uses the four most common datasets in the field of EEG-based emotion recognition (see table below). **The framework supports dataset uploading in one line of code, but you need to have downloaded the datasets first.** All of these sets are freely available from the respective authors. We are in contact with the authors of all datasets. If you have trouble to gain access to a specific dataset, please let us know.

---
| Dataset | Electrodes | No of Channels | Sampling Rate | N Subjects | N Trials | Trial Duration |   Labels   | Label Scale |
|---------|:----------:|:--------------:|:-------------:|:----------:|:--------:|:--------------:|:----------:|:-----------:|
| MAHNOB  |     Wet    |       32       |      256      |     27     |    20    |     1-2 min    | 1-9+verbal |   discrete  |
| SEED    |     Wet    |       62       |      1000     |     15     |    15    |    ca. 4 min   |   verbal   |   discrete  |
| SEED IV |     Wet    |       62       |      200      |     15     |    24    |    ca. 2 min   |   verbal   |   discrete  |
| DREAMER |     Dry    |       14       |      128      |     23     |    18    |    ca. 3 min   |     1-5    |   discrete  |
---
The challenge contains two tasks. Submissions to the Challenge Track of the workshop have to address at least one of those two challenges:

1. Person-dependent task: In this task, data from the same participant is used to train and test the models. The specific rules for this task are:
   - Custom EEG pre-processing steps (e.g., filtering, downsampling, windowing) may be used, but these should be consistent accross participants and meticulously documented and thus reproducible.
   - The leave-one-trial out (LOTO) cross-validation needs to be used for training/testing.
   - Importantly, the same procedure must be used for all of the participants contained in the four datasets.
   - For each participant in the four datasets, accuracy must be calculated, separately for valence and arousal. Subsequently accuracy must be averaged across participants (separately for valence and arousal).
   - The harmonic mean of accuracy for valence and arousal will be the critical measure in order to rank the submissions.
   - The code used to train the models needs to be part of the submssion so the program committee can be sure that the training procedure was not adjusted to individual participants/datasets.

2. Person-independent task: The task is to create an EEG-based emotion recognition approach that generalizes across subjects from different datasets. The specific rules for this challenge are:
   - Custom EEG pre-processing steps (e.g., filtering, downsampling, windowing) may be used, but these should be consistent accross datasets and meticulously documented and thus reproducible.
   - A fixed train-test split has to be used. The training set contains 75% of all participants across datasets, the test set 25%.
   - This means that data from a given participant are either part of the training or the test set - never of both sets.
   - During training, all datasets may be used jointly to improve performance.
   - As evaluation metric, please use accuracy, separately for valence and arousal. The harmonic mean of accuracy for valence and arousal will be the critical measure in order to rank the submissions.
   - The code used to train the models needs to be part of the submssion so the program committee can be sure that the models were not optimized on the test set.

## EEGAIN framework

We provide the EEGAIN framework [here](https://github.com/EmotionLab/EEGain) for participants to use in the challenge. The EEGAIN framework substantially facilitates the programming effort it takes to run your own experiments. It includes standardized methods for data pre-processing, data splitting, evaluation metrics, and the ability to
load the four datasets used in the challenge (see below) with only a single line of code. Here is a short introduction on how to use it:

1. Clone the github repository and install all libraries contained in requirements.txt.
2. Run the ssh file: 
```bash
run_cli.sh
```

3. Within the ssh-file, arguments are given to 
```bash
run_cli.py <arguments>
```
You can adapt these arguments within the sh file according to your specific intentions:

* `--model_name` Selects a custom or pre-trained model. The pretrained implemented pre-trained models are: 
    - `TSception`
    - `EEGNet`
    - `DeepconvNet`
    - `Shallow`
* `--data_name` Chooses a custom name or a name of predefined datasets. The predefined datasets are:
    - `DEAP`
    - `MAHNOB`
    - `AMIGOS`
    - `DREAMER`
	- `SEED`
	- `SEEDIV`
* `--data_path` Specifies the directory where the data files are saved.
* `--log_dir` Specifies the directory where the log files will be saved.
* `--overal_log_file` Specifies the name of the log file that will be created.
* `--label_type` Specifies whether data is separated into classes based on valence or arousal. This argument has no effect on the Seed and SEED IV dataset because these datasets have fixed splits based on categorical labels. You can choose the following options:
    - `V`: Valence
    - `A`: Arousal
* `--num_epochs` Sets the number of epochs for training.
* `--batch_size` Specifies the batch size for training.
* `--lr` Specifies the learning rate for training.
* `--sampling_r` Specifies the sampling rate of the EEG data.
* `--window` Specifies the length of the EEG segments (in seconds).
* `--weight_decay` Specifies the weight decay ratio for regularization.
* `--label_smoothing` Smoothing factor applied to the labels to make them less extreme.
* `--dropout_rate` Probability at which outputs of the layer are dropped out. 
* `--num_classes` Specifies the number of classes of the classification problem. Please leave this argument at a value of 2 because we conceptualized the task as a binary classification problem.
* `--channels` Specifies the number of channels for the dataset. Set this argument to 14 for AMIGOS and DREAMER, 32 for MAHNOB and DEAP, and to 62 for SEED and SEED IV.
* `--split_type` Specifies the type of train-test splitting. Please use only the following two splitting types as any other splits will disqualify your submission:
    - `LOTO`: Leave one trial out. Use this split for the person-dependent task.
    - `LOSO_Fixed`: Creates a fixed 75/25 train-test split that is mandatory for the person-independent task (see below).

## Baseline 
Coming soon